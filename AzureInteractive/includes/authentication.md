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
ms.openlocfilehash: 426a7287458a48d1bda220ad1a5f067be2ce77d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460038"
---
<span data-ttu-id="c5a59-103">Azure App Service-verificatie maakt gebruiksklare ondersteuning voor verificatie mogelijk in een Azure-functie-app.</span><span class="sxs-lookup"><span data-stu-id="c5a59-103">Azure App Service Authentication enables turn-key authentication support in an Azure Function app.</span></span> <span data-ttu-id="c5a59-104">Dit integreert naadloos met Facebook, Twitter, Microsoft-accounts, Google en Azure Active Directory.</span><span class="sxs-lookup"><span data-stu-id="c5a59-104">It integrates seamlessly with Facebook, Twitter, Microsoft Account, Google, and Azure Active Directory.</span></span> <span data-ttu-id="c5a59-105">U voegt App Service-verificatie toe om de back-end-API’s van de web-app te beveiligen.</span><span class="sxs-lookup"><span data-stu-id="c5a59-105">You'll add App Service Authentication to protect the backend APIs of your web app.</span></span>

## <a name="enable-app-service-authentication"></a><span data-ttu-id="c5a59-106">App Service-verificatie inschakelen</span><span class="sxs-lookup"><span data-stu-id="c5a59-106">Enable App Service Authentication</span></span>

1. <span data-ttu-id="c5a59-107">Open de functie-app in de Azure-portal.</span><span class="sxs-lookup"><span data-stu-id="c5a59-107">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="c5a59-108">Selecteer onder **Platformfuncties** de optie **Verificatie/autorisatie**.</span><span class="sxs-lookup"><span data-stu-id="c5a59-108">Under **Platform features**, select **Authentication/Authorization**.</span></span>

    ![Verificatie/autorisatie selecteren](media/functions-first-serverless-web-app/6-authorization.jpg)

1. <span data-ttu-id="c5a59-110">Selecteer de volgende waarden:</span><span class="sxs-lookup"><span data-stu-id="c5a59-110">Select the following values:</span></span>
    
    | <span data-ttu-id="c5a59-111">Instelling</span><span class="sxs-lookup"><span data-stu-id="c5a59-111">Setting</span></span>      |  <span data-ttu-id="c5a59-112">Voorgestelde waarde</span><span class="sxs-lookup"><span data-stu-id="c5a59-112">Suggested value</span></span>   | <span data-ttu-id="c5a59-113">Beschrijving</span><span class="sxs-lookup"><span data-stu-id="c5a59-113">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="c5a59-114">**App Service-verificatie**</span><span class="sxs-lookup"><span data-stu-id="c5a59-114">**App Service Authentication**</span></span> | <span data-ttu-id="c5a59-115">Aan</span><span class="sxs-lookup"><span data-stu-id="c5a59-115">On</span></span> | <span data-ttu-id="c5a59-116">Verificatie inschakelen.</span><span class="sxs-lookup"><span data-stu-id="c5a59-116">Enable authentication.</span></span> |
    | <span data-ttu-id="c5a59-117">**Actie wanneer de aanvraag niet is geverifieerd**</span><span class="sxs-lookup"><span data-stu-id="c5a59-117">**Action when request is not authenticated**</span></span> | <span data-ttu-id="c5a59-118">Meld u aan met Azure Active Directory</span><span class="sxs-lookup"><span data-stu-id="c5a59-118">Log in with Azure Active Directory</span></span> | <span data-ttu-id="c5a59-119">Selecteer een geconfigureerde verificatiemethode (hieronder).</span><span class="sxs-lookup"><span data-stu-id="c5a59-119">Select a configured authentication method (below).</span></span> |
    | <span data-ttu-id="c5a59-120">**Verificatieproviders**</span><span class="sxs-lookup"><span data-stu-id="c5a59-120">**Authentication Providers**</span></span> | <span data-ttu-id="c5a59-121">Zie hieronder</span><span class="sxs-lookup"><span data-stu-id="c5a59-121">See below</span></span> | <span data-ttu-id="c5a59-122">Zie hieronder</span><span class="sxs-lookup"><span data-stu-id="c5a59-122">See below</span></span> |
    | <span data-ttu-id="c5a59-123">**Tokenarchief**</span><span class="sxs-lookup"><span data-stu-id="c5a59-123">**Token store**</span></span> | <span data-ttu-id="c5a59-124">Aan</span><span class="sxs-lookup"><span data-stu-id="c5a59-124">On</span></span> | <span data-ttu-id="c5a59-125">Toestaan dat tokens worden opgeslagen en beheerd in App Service.</span><span class="sxs-lookup"><span data-stu-id="c5a59-125">Allow App Service to store and manage tokens.</span></span> |
    | <span data-ttu-id="c5a59-126">**Toegestane externe omleidings-URL's**</span><span class="sxs-lookup"><span data-stu-id="c5a59-126">**Allowed external redirect URLs**</span></span> | <span data-ttu-id="c5a59-127">De URL voor de toepassing, bijvoorbeeld: https://firstserverlessweb.z4.web.core.windows.net/</span><span class="sxs-lookup"><span data-stu-id="c5a59-127">The URL of your application, for example: https://firstserverlessweb.z4.web.core.windows.net/</span></span> | <span data-ttu-id="c5a59-128">URL(‘s) waarnaar via App Service mag worden omgeleid nadat de gebruiker is geverifieerd.</span><span class="sxs-lookup"><span data-stu-id="c5a59-128">URL(s) that App Service is allowed to redirect to after a user is authenticated.</span></span> |

1. <span data-ttu-id="c5a59-129">Selecteer **Azure Active Directory** om de **Azure Active Directory-instellingen** weer te geven.</span><span class="sxs-lookup"><span data-stu-id="c5a59-129">Select **Azure Active Directory** to reveal **Azure Active Directory Settings**.</span></span>

    1. <span data-ttu-id="c5a59-130">Selecteer **Express** als de **Beheermodus**, en vul de volgende gegevens in.</span><span class="sxs-lookup"><span data-stu-id="c5a59-130">Select **Express** as the **Management Mode** and fill in the following information.</span></span>
    
        | <span data-ttu-id="c5a59-131">Instelling</span><span class="sxs-lookup"><span data-stu-id="c5a59-131">Setting</span></span>      |  <span data-ttu-id="c5a59-132">Voorgestelde waarde</span><span class="sxs-lookup"><span data-stu-id="c5a59-132">Suggested value</span></span>   | <span data-ttu-id="c5a59-133">Beschrijving</span><span class="sxs-lookup"><span data-stu-id="c5a59-133">Description</span></span>                                        |
        | --- | --- | ---|
        | <span data-ttu-id="c5a59-134">**Beheermodus**</span><span class="sxs-lookup"><span data-stu-id="c5a59-134">**Management mode**</span></span> | <span data-ttu-id="c5a59-135">Express, nieuwe AD-app maken</span><span class="sxs-lookup"><span data-stu-id="c5a59-135">Express, Create new AD app</span></span> | <span data-ttu-id="c5a59-136">Automatisch een service-principal en Azure Active Directory-verificatie instellen.</span><span class="sxs-lookup"><span data-stu-id="c5a59-136">Automatically set up a service principal and Azure Active Directory authentication.</span></span> |
        | <span data-ttu-id="c5a59-137">**App maken**</span><span class="sxs-lookup"><span data-stu-id="c5a59-137">**Create app**</span></span> | <span data-ttu-id="c5a59-138">my-serverless-webapp</span><span class="sxs-lookup"><span data-stu-id="c5a59-138">my-serverless-webapp</span></span> | <span data-ttu-id="c5a59-139">Voer een unieke naam in voor de toepassing.</span><span class="sxs-lookup"><span data-stu-id="c5a59-139">Enter a unique application name.</span></span> |
    
    1. <span data-ttu-id="c5a59-140">Klik op **OK** om de Azure Active Directory-instellingen op te slaan.</span><span class="sxs-lookup"><span data-stu-id="c5a59-140">Click **OK** to save the Azure Active Directory settings.</span></span>

    ![Verificatie/autorisatie en Azure Active Directory-instellingen](media/functions-first-serverless-web-app/6-create-aad.png)

1. <span data-ttu-id="c5a59-142">Klik op **Opslaan**.</span><span class="sxs-lookup"><span data-stu-id="c5a59-142">Click **Save**.</span></span>


## <a name="modify-the-web-app-to-enable-authentication"></a><span data-ttu-id="c5a59-143">De web-app wijzigen om verificatie in te schakelen</span><span class="sxs-lookup"><span data-stu-id="c5a59-143">Modify the web app to enable authentication</span></span>

1. <span data-ttu-id="c5a59-144">Zorg ervoor dat de huidige map in Cloud Shell deze map is: **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="c5a59-144">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="c5a59-145">Als u verificatie wilt inschakelen in uw functie-app, voegt u de volgende coderegel toe aan **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="c5a59-145">To enable authentication in your function app, append the following line of code to **settings.js**.</span></span>

    `window.authEnabled = true`

    <span data-ttu-id="c5a59-146">Breng de wijziging aan en bekijk het resultaat door de volgende opdrachten te gebruiken of met behulp van een opdrachtregeleditor, zoals VIM.</span><span class="sxs-lookup"><span data-stu-id="c5a59-146">Make the change and view the result by using the following commands or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    <span data-ttu-id="c5a59-147">Controleer of de wijziging is aangebracht in het bestand.</span><span class="sxs-lookup"><span data-stu-id="c5a59-147">Confirm the change was made to the file.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="c5a59-148">Upload het bestand naar Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="c5a59-148">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a><span data-ttu-id="c5a59-149">De toepassing testen</span><span class="sxs-lookup"><span data-stu-id="c5a59-149">Test the application</span></span>

1. <span data-ttu-id="c5a59-150">Open de toepassing in een browser.</span><span class="sxs-lookup"><span data-stu-id="c5a59-150">Open the application in a browser.</span></span> <span data-ttu-id="c5a59-151">Klik op **Aanmelden** en meld u aan.</span><span class="sxs-lookup"><span data-stu-id="c5a59-151">Click **Log in** and log in.</span></span>

1. <span data-ttu-id="c5a59-152">Selecteer een afbeeldingsbestand en upload het.</span><span class="sxs-lookup"><span data-stu-id="c5a59-152">Select an image file and upload it.</span></span>

    ![Aanmeldingspagina](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a><span data-ttu-id="c5a59-154">Samenvatting</span><span class="sxs-lookup"><span data-stu-id="c5a59-154">Summary</span></span>

<span data-ttu-id="c5a59-155">In deze module hebt u geleerd hoe u verificatie toevoegt aan de toepassing met behulp van Azure App Service-verificatie.</span><span class="sxs-lookup"><span data-stu-id="c5a59-155">In this module, you learned how to add authentication to the applcation using Azure App Service Authentication.</span></span>
