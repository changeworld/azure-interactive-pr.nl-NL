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
In dit stadium is de toepassing een functionele galerie waarmee u afbeeldingen kunt uploaden en weergeven. In deze module leert u hoe u de Computer Vision-API van Microsoft Cognitive Services kunt gebruiken om bijschriften te genereren voor geüploade afbeeldingen en de bijschriften met de metagegevens van afbeeldingen op te slaan in Cosmos DB.

## <a name="create-a-computer-vision-account"></a>Een Computer Vision-account maken

Microsoft Cognitive Services is een verzameling services waarmee ontwikkelaars hun toepassingen intelligenter kunnen maken. De Computer Vision-API is een serverloze service waarmee afbeeldingen worden verwerkt met behulp van geavanceerde algoritmen en waarmee informatie over elke afbeelding wordt geretourneerd. Het beschikt over een gratis laag die maximaal 5.000 API-aanroepen per maand biedt.

1. Controleer of u nog steeds bent aangemeld bij Cloud Shell. Als dit niet het geval is, selecteert u **Focusmodus openen** om een Cloud Shell-venster te openen. 

1. Maak een nieuw Cognitive Services-account van het soort **ComputerVision** met een unieke naam in de resourcegroep. Gebruik voor de gratis laag **F0** als de SKU. Als u al beschikt over een bestaand Computer Vision-account, moet u mogelijk een Standard-account (S1) maken. Dit kan echter kosten met zich meebrengen.

    ```azurecli
    az cognitiveservices account create -g first-serverless-app -n <computer vision account name> --kind ComputerVision --sku F0 -l westcentralus
    ```


## <a name="create-function-app-settings-for-computer-vision-url-and-key"></a>Functie-app-instellingen maken voor een Computer Vision-URL en -sleutel

Een URL en sleutel zijn vereist om de Computer Vision-API aan te roepen.

1. Haal de URL en sleutel voor de Computer Vision-API op en sla ze op in bash-variabelen.

    ```azurecli
    export COMP_VISION_KEY=$(az cognitiveservices account keys list -g first-serverless-app -n <computer vision account name> --query key1 --output tsv)
    ```
    ```azurecli
    export COMP_VISION_URL=$(az cognitiveservices account show -g first-serverless-app -n <computer vision account name> --query endpoint --output tsv)
    ```

1. Maak app-instellingen met respectievelijk de namen **COMP_VISION_KEY** en **COMP_VISION_URL** in de functie-app.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings COMP_VISION_KEY=$COMP_VISION_KEY COMP_VISION_URL=$COMP_VISION_URL -o table
    ```


## <a name="call-computer-vision-api-from-resizeimage-function"></a>Computer Vision-API aanroepen vanuit de functie ResizeImage

In de volgende stappen wijzigt u de functie **ResizeImage** om de Computer Vision-API aan te roepen om elke geüploade afbeelding te beschrijven en de beschrijving op te slaan in Cosmos DB.

1. Open de functie-app in de Azure-portal.

1. **C#**

    1. (C#) Ga via de linkernavigatie naar de functie **ResizeImage** en open het bijbehorende codevenster.

    1. (C#) Vervang de code door de inhoud van [**/csharp/ResizeImage/run-module5.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run-module5.csx). Voor deze code wordt gebruikgemaakt van `HttpClient` om de Computer Vision-API aan te roepen en de resultaten op te slaan in Cosmos DB.

1. **JavaScript**

    1. (JavaScript) Voor deze functie is het `axios`-pakket van npm vereist om een HTTP-aanroep te maken naar de Computer Vision-API. Klik in de linkernavigatie op de naam van de functie-app, en klik op **Platformfuncties** om het npm-pakket te installeren.

    1. (JavaScript) Klik op **Console** om een consolevenster weer te geven.

    1. (JavaScript) Voer de opdracht `npm install axios` uit in de console. Het kan een paar minuten duren om de bewerking te voltooien.

    1. (JavaScript) Klik in de linkernavigatie op de functienaam (**ResizeImage**) om de functie weer te geven. Vervang vervolgens alle **index.js** door de inhoud van [**/javascript/ResizeImage/module5.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index-module5.js).

1. Klik onder het codevenster op **Logboeken** om het paneel met logboeken uit te vouwen.

1. Klik op **Opslaan**. Ga naar het paneel met logboeken om te controleren of de functie is opgeslagen en er geen fouten zijn opgetreden.


## <a name="test-the-application"></a>De toepassing testen

1. Open de toepassing in een browser. Selecteer een afbeeldingsbestand en upload het.

1. Na enkele seconden wordt de miniatuur van de nieuwe afbeelding weergegeven op de pagina. Wijs de afbeelding aan om de beschrijving te zien die is gegenereerd met Computer Vision.


## <a name="summary"></a>Samenvatting

In deze module hebt u geleerd hoe u met behulp van de Computer Vision-API van Microsoft Cognitive Services automatisch een bijschrift kunt genereren voor elke geüploade afbeelding. Hierna leert u hoe u verificatie aan de toepassing toevoegt met behulp van Azure App Service-verificatie.
