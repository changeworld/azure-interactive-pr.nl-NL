---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: f51b864cab14273c1e88dd85d22400e0e76ef770
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47459992"
---
<span data-ttu-id="0e6c4-103">In dit stadium is de toepassing een functionele galerie waarmee u afbeeldingen kunt uploaden en weergeven.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-103">At this point, the application is a functional gallery that allows you to upload and view images.</span></span> <span data-ttu-id="0e6c4-104">In deze module leert u hoe u de Computer Vision-API van Microsoft Cognitive Services kunt gebruiken om bijschriften te genereren voor geüploade afbeeldingen en de bijschriften met de metagegevens van afbeeldingen op te slaan in Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-104">In this module, you learn how to use the Computer Vision API from Microsoft Cognitive Services to generate captions for uploaded images and save the captions with the image metadata in Cosmos DB.</span></span>

## <a name="create-a-computer-vision-account"></a><span data-ttu-id="0e6c4-105">Een Computer Vision-account maken</span><span class="sxs-lookup"><span data-stu-id="0e6c4-105">Create a Computer Vision account</span></span>

<span data-ttu-id="0e6c4-106">Microsoft Cognitive Services is een verzameling services waarmee ontwikkelaars hun toepassingen intelligenter kunnen maken.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-106">Microsoft Cognitive Services is a collection of services available to developers to make their applications more intelligent.</span></span> <span data-ttu-id="0e6c4-107">De Computer Vision-API is een serverloze service waarmee afbeeldingen worden verwerkt met behulp van geavanceerde algoritmen en waarmee informatie over elke afbeelding wordt geretourneerd.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-107">The Computer Vision API is a serverless service that processes images using advanced algorithms and returns information about each image.</span></span> <span data-ttu-id="0e6c4-108">Het beschikt over een gratis laag die maximaal 5.000 API-aanroepen per maand biedt.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-108">It has a free tier that provides up to 5000 API calls per month.</span></span>

1. <span data-ttu-id="0e6c4-109">Controleer of u nog steeds bent aangemeld bij Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-109">Ensure you are still signed into the Cloud Shell.</span></span> <span data-ttu-id="0e6c4-110">Als dit niet het geval is, selecteert u **Focusmodus openen** om een Cloud Shell-venster te openen.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="0e6c4-111">Maak een nieuw Cognitive Services-account van het soort **ComputerVision** met een unieke naam in de resourcegroep.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-111">Create a new Cognitive Services account of kind **ComputerVision** with a unique name in your resource group.</span></span> <span data-ttu-id="0e6c4-112">Gebruik voor de gratis laag **F0** als de SKU.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-112">For the free tier, use **F0** as the SKU.</span></span> <span data-ttu-id="0e6c4-113">Als u al beschikt over een bestaand Computer Vision-account, moet u mogelijk een Standard-account (S1) maken. Dit kan echter kosten met zich meebrengen.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-113">If you already have an existing Computer Vision account, you may need to create a Standard account (S1); however, this may incur some costs.</span></span>

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a><span data-ttu-id="0e6c4-114">Functie-app-instellingen maken voor een Computer Vision-URL en -sleutel</span><span class="sxs-lookup"><span data-stu-id="0e6c4-114">Create Function App settings for Computer Vision URL and key</span></span>

<span data-ttu-id="0e6c4-115">Een URL en sleutel zijn vereist om de Computer Vision-API aan te roepen.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-115">To call the Computer Vision API, a URL and key are required.</span></span>

1. <span data-ttu-id="0e6c4-116">Haal de URL en sleutel voor de Computer Vision-API op en sla ze op in bash-variabelen.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-116">Obtain the Computer Vision API key and URL and save them in bash variables.</span></span>

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. <span data-ttu-id="0e6c4-117">Maak app-instellingen met respectievelijk de namen **COMP_VISION_KEY** en **COMP_VISION_URL** in de functie-app.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-117">Create app settings named **COMP_VISION_KEY** and **COMP_VISION_URL**, respectively, in the function app.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a><span data-ttu-id="0e6c4-118">Computer Vision-API aanroepen vanuit de functie ResizeImage</span><span class="sxs-lookup"><span data-stu-id="0e6c4-118">Call Computer Vision API from ResizeImage function</span></span>

<span data-ttu-id="0e6c4-119">In de volgende stappen wijzigt u de functie **ResizeImage** om de Computer Vision-API aan te roepen om elke geüploade afbeelding te beschrijven en de beschrijving op te slaan in Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-119">In the following steps, you modify the **ResizeImage** function to call the Computer Vision API to describe each uploaded image and save the description in Cosmos DB.</span></span>

1. <span data-ttu-id="0e6c4-120">Open de functie-app in de Azure-portal.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-120">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="0e6c4-121">**C#**</span><span class="sxs-lookup"><span data-stu-id="0e6c4-121">**C#**</span></span>

    1. <span data-ttu-id="0e6c4-122">(C#) Ga via de linkernavigatie naar de functie **ResizeImage** en open het bijbehorende codevenster.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-122">(C#) Using the left navigation, locate the **ResizeImage** function and open its code window.</span></span>

    1. <span data-ttu-id="0e6c4-123">(C#) Vervang de code door de inhoud van [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span><span class="sxs-lookup"><span data-stu-id="0e6c4-123">(C#) Replace the code with the contents of [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx).</span></span> <span data-ttu-id="0e6c4-124">Voor deze code wordt gebruikgemaakt van `HttpClient` om de Computer Vision-API aan te roepen en de resultaten op te slaan in Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-124">This code uses `HttpClient` to call Computer Vision API and save its result in Cosmos DB.</span></span>

1. <span data-ttu-id="0e6c4-125">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="0e6c4-125">**JavaScript**</span></span>

    1. <span data-ttu-id="0e6c4-126">(JavaScript) Voor deze functie is het `axios`-pakket van npm vereist om een HTTP-aanroep te maken naar de Computer Vision-API.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-126">(JavaScript) This function requires the `axios` package from npm to make an HTTP call to the Computer Vision API.</span></span> <span data-ttu-id="0e6c4-127">Klik in de linkernavigatie op de naam van de functie-app, en klik op **Platformfuncties** om het npm-pakket te installeren.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-127">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="0e6c4-128">(JavaScript) Klik op **Console** om een consolevenster weer te geven.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-128">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="0e6c4-129">(JavaScript) Voer de opdracht `npm install axios` uit in de console.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-129">(JavaScript) Run the command `npm install axios` in the console.</span></span> <span data-ttu-id="0e6c4-130">Het kan een paar minuten duren om de bewerking te voltooien.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-130">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="0e6c4-131">(JavaScript) Klik in de linkernavigatie op de functienaam (**ResizeImage**) om de functie weer te geven. Vervang vervolgens alle **index.js** door de inhoud van [**/javascript/ResizeImage/module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span><span class="sxs-lookup"><span data-stu-id="0e6c4-131">(JavaScript) Click on the function name (**ResizeImage**) in the left navigation to reveal the function, and then replace all of **index.js** with the contents of [**/javascript/ResizeImage/index-module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).</span></span>

1. <span data-ttu-id="0e6c4-132">Klik onder het codevenster op **Logboeken** om het paneel met logboeken uit te vouwen.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-132">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="0e6c4-133">Klik op **Opslaan**.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-133">Click **Save**.</span></span> <span data-ttu-id="0e6c4-134">Ga naar het paneel met logboeken om te controleren of de functie is opgeslagen en er geen fouten zijn opgetreden.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-134">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="0e6c4-135">De toepassing testen</span><span class="sxs-lookup"><span data-stu-id="0e6c4-135">Test the application</span></span>

1. <span data-ttu-id="0e6c4-136">Open de toepassing in een browser.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-136">Open the application in a browser.</span></span> <span data-ttu-id="0e6c4-137">Selecteer een afbeeldingsbestand en upload het.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-137">Select an image file, and upload it.</span></span>

1. <span data-ttu-id="0e6c4-138">Na enkele seconden wordt de miniatuur van de nieuwe afbeelding weergegeven op de pagina.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-138">After a few seconds, the thumbnail of the new image should appear on the page.</span></span> <span data-ttu-id="0e6c4-139">Wijs de afbeelding aan om de beschrijving te zien die is gegenereerd met Computer Vision.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-139">Hover over the image to see the description generated by Computer Vision.</span></span>


## <a name="summary"></a><span data-ttu-id="0e6c4-140">Samenvatting</span><span class="sxs-lookup"><span data-stu-id="0e6c4-140">Summary</span></span>

<span data-ttu-id="0e6c4-141">In deze module hebt u geleerd hoe u met behulp van de Computer Vision-API van Microsoft Cognitive Services automatisch een bijschrift kunt genereren voor elke geüploade afbeelding.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-141">In this module, you learned how to automatically generate captions for each uploaded image using Microsoft Cognitive Services Computer Vision API.</span></span> <span data-ttu-id="0e6c4-142">Hierna leert u hoe u verificatie aan de toepassing toevoegt met behulp van Azure App Service-verificatie.</span><span class="sxs-lookup"><span data-stu-id="0e6c4-142">Next, you learn how to add authentication to the application using Azure App Service Authentication.</span></span>
