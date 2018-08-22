---
title: bestand opnemen
description: bestand opnemen
services: functions
author: tdykstra
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/21/2018
ms.author: tdykstra
ms.custom: include file
ms.openlocfilehash: 56cfb4c2893977086309660f4f6941fd0d648913
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079243"
---
<span data-ttu-id="9eb12-103">De toepassing die u bouwt, is een fotogalerie.</span><span class="sxs-lookup"><span data-stu-id="9eb12-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="9eb12-104">De galerie maakt aan de clientzijde gebruik van JavaScript voor het aanroepen van API’s om afbeeldingen te uploaden en weer te geven.</span><span class="sxs-lookup"><span data-stu-id="9eb12-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="9eb12-105">In deze module maakt u een API met behulp van een serverloze functie waarmee een tijdelijke URL wordt gegenereerd om een afbeelding te uploaden.</span><span class="sxs-lookup"><span data-stu-id="9eb12-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="9eb12-106">De webtoepassing maakt gebruik van de gegenereerde URL om een afbeelding naar Blob Storage te uploaden met behulp van de [Blob Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span><span class="sxs-lookup"><span data-stu-id="9eb12-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="9eb12-107">Een Blob Storage-container maken voor afbeeldingen</span><span class="sxs-lookup"><span data-stu-id="9eb12-107">Create a blob storage container for images</span></span>

<span data-ttu-id="9eb12-108">Voor de toepassing is een afzonderlijke opslagcontainer vereist voor het uploaden en hosten van afbeeldingen.</span><span class="sxs-lookup"><span data-stu-id="9eb12-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="9eb12-109">Controleer of u nog steeds bent aangemeld bij Cloud Shell (bash).</span><span class="sxs-lookup"><span data-stu-id="9eb12-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="9eb12-110">Als dit niet het geval is, selecteert u **Focusmodus openen** om een Cloud Shell-venster te openen.</span><span class="sxs-lookup"><span data-stu-id="9eb12-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="9eb12-111">Maak een nieuwe container met de naam **images** in het opslagaccount met openbare toegang tot alle blobs.</span><span class="sxs-lookup"><span data-stu-id="9eb12-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="9eb12-112">Een Azure-functie-app maken</span><span class="sxs-lookup"><span data-stu-id="9eb12-112">Create an Azure Function app</span></span>

<span data-ttu-id="9eb12-113">Azure Functions is een service voor het uitvoeren van serverloze functies.</span><span class="sxs-lookup"><span data-stu-id="9eb12-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="9eb12-114">Een serverloze functie kan worden geactiveerd (aangeroepen) voor gebeurtenissen zoals een HTTP-aanvraag of wanneer er een blob is gemaakt in de opslagcontainer.</span><span class="sxs-lookup"><span data-stu-id="9eb12-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="9eb12-115">Een Azure-functie-app is een container voor een of meer serverloze functies.</span><span class="sxs-lookup"><span data-stu-id="9eb12-115">An Azure Function app is a container for one or more serverless functions.</span></span>

1. <span data-ttu-id="9eb12-116">Maak een nieuwe Azure-functie-app met een unieke naam in de resourcegroep **first-serverless-app** die u eerder hebt gemaakt.</span><span class="sxs-lookup"><span data-stu-id="9eb12-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="9eb12-117">Voor functie-apps is een opslagaccount vereist. In deze zelfstudie gebruikt u het bestaande opslagaccount.</span><span class="sxs-lookup"><span data-stu-id="9eb12-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

    ```azurecli
    az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
    ```


## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="9eb12-118">Een serverloze functie maken die wordt geactiveerd via HTTP</span><span class="sxs-lookup"><span data-stu-id="9eb12-118">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="9eb12-119">Met de fotogaleriewebtoepassing wordt een HTTP-aanvraag verzonden naar een serverloze functie om een tijdelijke URL te genereren voor het veilig uploaden van een afbeelding naar Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="9eb12-119">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="9eb12-120">De functie wordt geactiveerd via een HTTP-aanvraag en maakt gebruik van de Azure Storage-SDK voor het genereren en retourneren van de beveiligde URL.</span><span class="sxs-lookup"><span data-stu-id="9eb12-120">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="9eb12-121">Nadat de functie-app is gemaakt, zoekt u in de Azure-portal met behulp van het zoekvak naar de functie-app en klikt u erop om deze te openen.</span><span class="sxs-lookup"><span data-stu-id="9eb12-121">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![De functie-app openen](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="9eb12-123">Wijs in de linkernavigatie van het venster van de functie-app naar **Functies** en klik op **+** om te beginnen met het maken van een nieuwe serverloze functie.</span><span class="sxs-lookup"><span data-stu-id="9eb12-123">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![Een nieuwe functie maken](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="9eb12-125">Klik op **Aangepaste functie** voor een lijst met functiesjablonen.</span><span class="sxs-lookup"><span data-stu-id="9eb12-125">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="9eb12-126">Ga naar de sjabloon **HttpTrigger** en klik op de taal die moet worden gebruikt (C# of JavaScript).</span><span class="sxs-lookup"><span data-stu-id="9eb12-126">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="9eb12-127">Gebruik deze waarden om een functie te maken waarmee een upload-URL voor een blob wordt gegenereerd.</span><span class="sxs-lookup"><span data-stu-id="9eb12-127">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="9eb12-128">Instelling</span><span class="sxs-lookup"><span data-stu-id="9eb12-128">Setting</span></span>      |  <span data-ttu-id="9eb12-129">Voorgestelde waarde</span><span class="sxs-lookup"><span data-stu-id="9eb12-129">Suggested value</span></span>   | <span data-ttu-id="9eb12-130">Beschrijving</span><span class="sxs-lookup"><span data-stu-id="9eb12-130">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="9eb12-131">**Taal**</span><span class="sxs-lookup"><span data-stu-id="9eb12-131">**Language**</span></span> | <span data-ttu-id="9eb12-132">C# of JavaScript</span><span class="sxs-lookup"><span data-stu-id="9eb12-132">C# or JavaScript</span></span> | <span data-ttu-id="9eb12-133">Selecteer de gewenste taal.</span><span class="sxs-lookup"><span data-stu-id="9eb12-133">Select the language you want to use.</span></span> |
    | <span data-ttu-id="9eb12-134">**Een naam voor de functie opgeven**</span><span class="sxs-lookup"><span data-stu-id="9eb12-134">**Name your function**</span></span> | <span data-ttu-id="9eb12-135">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="9eb12-135">GetUploadUrl</span></span> | <span data-ttu-id="9eb12-136">Typ de naam precies zoals deze wordt weergegeven, zodat de functie kan worden gedetecteerd met de toepassing.</span><span class="sxs-lookup"><span data-stu-id="9eb12-136">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="9eb12-137">**Verificatieniveau**</span><span class="sxs-lookup"><span data-stu-id="9eb12-137">**Authorization level**</span></span> | <span data-ttu-id="9eb12-138">Anoniem</span><span class="sxs-lookup"><span data-stu-id="9eb12-138">Anonymous</span></span> | <span data-ttu-id="9eb12-139">Sta toe dat de functie openbaar toegankelijk is.</span><span class="sxs-lookup"><span data-stu-id="9eb12-139">Allow the function to be accessed publicly.</span></span> |

    ![Instellingen invoeren voor een nieuwe functie die wordt geactiveerd via HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="9eb12-141">Klik op **Maken** om de functie te maken.</span><span class="sxs-lookup"><span data-stu-id="9eb12-141">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="9eb12-142">**C#**</span><span class="sxs-lookup"><span data-stu-id="9eb12-142">**C#**</span></span> 

    1. <span data-ttu-id="9eb12-143">Wanneer de broncode van de functie wordt weergegeven, vervangt u alle **run.csx** door de inhoud van [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span><span class="sxs-lookup"><span data-stu-id="9eb12-143">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="9eb12-144">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="9eb12-144">**JavaScript**</span></span> 

    1. <span data-ttu-id="9eb12-145">(JavaScript) Voor deze functie is vereist dat met het `azure-storage`-pakket van npm het SAS-token (Shared Access Signature) wordt gegenereerd dat is vereist om de beveiligde URL samen te stellen.</span><span class="sxs-lookup"><span data-stu-id="9eb12-145">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="9eb12-146">Klik in de linkernavigatie op de naam van de functie-app, en klik op **Platformfuncties** om het npm-pakket te installeren.</span><span class="sxs-lookup"><span data-stu-id="9eb12-146">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="9eb12-147">(JavaScript) Klik op **Console** om een consolevenster weer te geven.</span><span class="sxs-lookup"><span data-stu-id="9eb12-147">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![Een consolevenster openen](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="9eb12-149">(JavaScript) Zorg ervoor dat de map is **d:\home\site\wwwroot** door de opdracht `cd d:\home\site\wwwroot` uit te voeren.</span><span class="sxs-lookup"><span data-stu-id="9eb12-149">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="9eb12-150">(JavaScript) Voer de opdracht `npm init -y` uit om een leeg **package.json**-bestand te maken.</span><span class="sxs-lookup"><span data-stu-id="9eb12-150">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="9eb12-151">(JavaScript) Voer de opdracht `npm install --save azure-storage` uit in de console om het pakket te installeren en het op te slaan in **package.json**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-151">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="9eb12-152">Het kan een paar minuten duren om de bewerking te voltooien.</span><span class="sxs-lookup"><span data-stu-id="9eb12-152">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="9eb12-153">(JavaScript) Klik in de linkernavigatie op de functienaam (**GetUploadUrl**) om de functie weer te geven, vervang alle **index.js** door de inhoud van [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span><span class="sxs-lookup"><span data-stu-id="9eb12-153">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![index.js na de update](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="9eb12-155">Klik onder het codevenster op **Logboeken** om het paneel met logboeken uit te vouwen.</span><span class="sxs-lookup"><span data-stu-id="9eb12-155">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="9eb12-156">Klik op **Opslaan**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-156">Click **Save**.</span></span> <span data-ttu-id="9eb12-157">Ga naar het paneel met logboeken om te controleren of de functie is gecompileerd.</span><span class="sxs-lookup"><span data-stu-id="9eb12-157">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="9eb12-158">Met de functie wordt een zogenaamde SAS-URL (Shared Access Signature) gegenereerd die wordt gebruikt om een bestand te uploaden naar Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="9eb12-158">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="9eb12-159">De SAS-URL is gedurende een korte periode geldig en is alleen geschikt om een enkel bestand te uploaden.</span><span class="sxs-lookup"><span data-stu-id="9eb12-159">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="9eb12-160">Raadpleeg de Blob Storage-documentatie voor meer informatie over [handtekeningen voor gedeelde toegang](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span><span class="sxs-lookup"><span data-stu-id="9eb12-160">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="9eb12-161">Een omgevingsvariabele toevoegen voor de opslagverbindingsreeks</span><span class="sxs-lookup"><span data-stu-id="9eb12-161">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="9eb12-162">Voor de functie die u zojuist hebt gemaakt, is een verbindingsreeks vereist voor het opslagaccount, zodat de SAS-URL kan worden gegenereerd.</span><span class="sxs-lookup"><span data-stu-id="9eb12-162">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="9eb12-163">In plaats van de verbindingsreeks in de hoofdtekst van de functie in code vast te leggen, kan deze worden opgeslagen als een toepassingsinstelling.</span><span class="sxs-lookup"><span data-stu-id="9eb12-163">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="9eb12-164">Toepassingsinstellingen zijn toegankelijk als omgevingsvariabelen voor alle functies in de functie-app.</span><span class="sxs-lookup"><span data-stu-id="9eb12-164">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="9eb12-165">Voer in Cloud Shell een query uit voor de verbindingsreeks van het opslagaccount en sla deze op in een bash-variabele met de naam **STORAGE_CONNECTION_STRING**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-165">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="9eb12-166">Bevestig dat de variabele juist is ingesteld.</span><span class="sxs-lookup"><span data-stu-id="9eb12-166">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="9eb12-167">Maak een nieuwe toepassingsinstelling met de naam **AZURE_STORAGE_CONNECTION_STRING** met behulp van de waarde die is opgeslagen in de vorige stap.</span><span class="sxs-lookup"><span data-stu-id="9eb12-167">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="9eb12-168">Controleer of de uitvoer van de opdracht de nieuwe toepassingsinstelling bevat met de juiste waarde.</span><span class="sxs-lookup"><span data-stu-id="9eb12-168">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="9eb12-169">De serverloze functie testen</span><span class="sxs-lookup"><span data-stu-id="9eb12-169">Test the serverless function</span></span>

<span data-ttu-id="9eb12-170">Naast het maken en bewerken van functies biedt de Azure-portal ook een ingebouwd hulpprogramma voor het testen van functies.</span><span class="sxs-lookup"><span data-stu-id="9eb12-170">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="9eb12-171">Als u de serverloze HTTP-functie wilt testen, klikt u aan de rechterkant van het codevenster op het tabblad **Testen** om het testpaneel uit te vouwen.</span><span class="sxs-lookup"><span data-stu-id="9eb12-171">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="9eb12-172">Wijzig de **HTTP-methode** in **GET**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-172">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="9eb12-173">Klik onder **Query** op *Parameter toevoegen*\* en voeg de volgende parameter toe:</span><span class="sxs-lookup"><span data-stu-id="9eb12-173">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="9eb12-174">naam</span><span class="sxs-lookup"><span data-stu-id="9eb12-174">name</span></span>      |  <span data-ttu-id="9eb12-175">waarde</span><span class="sxs-lookup"><span data-stu-id="9eb12-175">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="9eb12-176">bestandsnaam</span><span class="sxs-lookup"><span data-stu-id="9eb12-176">filename</span></span> | <span data-ttu-id="9eb12-177">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="9eb12-177">image1.jpg</span></span> |

1. <span data-ttu-id="9eb12-178">Klik in het testpaneel op **Uitvoeren** om een HTTP-aanvraag naar de functie te verzenden.</span><span class="sxs-lookup"><span data-stu-id="9eb12-178">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="9eb12-179">De functie retourneert een upload-URL in de uitvoer.</span><span class="sxs-lookup"><span data-stu-id="9eb12-179">The function returns an upload URL in the output.</span></span> <span data-ttu-id="9eb12-180">De uitvoering van de functie wordt weergegeven in het paneel met logboeken.</span><span class="sxs-lookup"><span data-stu-id="9eb12-180">The function execution appears in the logs panel.</span></span>

    ![Logboeken waarin wordt weergegeven dat de functie juist is uitgevoerd](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="9eb12-182">CORS configureren in de functie-app</span><span class="sxs-lookup"><span data-stu-id="9eb12-182">Configure CORS in the function app</span></span>

<span data-ttu-id="9eb12-183">Omdat de front-end van de app wordt gehost in Blob Storage, heeft deze een andere domeinnaam dan de Azure-functie-app.</span><span class="sxs-lookup"><span data-stu-id="9eb12-183">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="9eb12-184">De functie-app moet zijn geconfigureerd voor CORS (Cross-Origin Resource Sharing) om ervoor te zorgen dat de functie die u zojuist hebt gemaakt, kan worden aangeroepen met JavaScript aan de clientzijde.</span><span class="sxs-lookup"><span data-stu-id="9eb12-184">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="9eb12-185">Klik in de linkernavigatiebalk van het venster voor de functie-app op de naam van de functie-app.</span><span class="sxs-lookup"><span data-stu-id="9eb12-185">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="9eb12-186">Klik op **Platformfuncties** om een lijst met geavanceerde functies weer te geven.</span><span class="sxs-lookup"><span data-stu-id="9eb12-186">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="9eb12-187">Klik onder **API** op **CORS**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-187">Under **API**, click **CORS**.</span></span>

    ![CORS selecteren](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="9eb12-189">Voeg een toegestane oorsprong toe voor de toepassings-URL uit de vorige module, waarbij u de afsluitende **/** weglaat. (Bijvoorbeeld: `https://firstserverlessweb.z4.web.core.windows.net`)</span><span class="sxs-lookup"><span data-stu-id="9eb12-189">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![CORS-instellingen waarin wordt weergegeven dat een URL voor een serverloze web-app is toegevoegd](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="9eb12-191">Klik op **Opslaan**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-191">Click **Save**.</span></span>

1. <span data-ttu-id="9eb12-192">C#</span><span class="sxs-lookup"><span data-stu-id="9eb12-192">C#</span></span>

   1. <span data-ttu-id="9eb12-193">(C#) Ga terug naar de `GetUploadUrl`-functie en selecteer vervolgens het tabblad **Integreren**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-193">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="9eb12-194">(C#) Selecteer onder Geselecteerde HTTP-methoden de methode **OPTIONS**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-194">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="9eb12-195">**GET**, **POST** en **OPTIONS** moeten allemaal zijn geselecteerd.</span><span class="sxs-lookup"><span data-stu-id="9eb12-195">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="9eb12-196">CORS maakt gebruik van de OPTIONS-methode. Deze methode is niet standaard geselecteerd voor C#-functies.</span><span class="sxs-lookup"><span data-stu-id="9eb12-196">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="9eb12-197">(C#) Klik op **Opslaan**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-197">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="9eb12-198">Blijf in de portal, ga naar de functie-app en selecteer het tabblad **Overzicht**. Klik vervolgens op **Opnieuw starten** om ervoor te zorgen dat de wijzigingen voor CORS worden doorgevoerd.</span><span class="sxs-lookup"><span data-stu-id="9eb12-198">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="9eb12-199">CORS configureren in het opslagaccount</span><span class="sxs-lookup"><span data-stu-id="9eb12-199">Configure CORS in the Storage account</span></span>

<span data-ttu-id="9eb12-200">Omdat via de app ook JavaScript-aanroepen aan de clientzijde naar Blob Storage worden verzonden om bestanden te uploaden, moet u ook het opslagaccount voor CORS configureren.</span><span class="sxs-lookup"><span data-stu-id="9eb12-200">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="9eb12-201">Voer de volgende opdracht uit om alle oorsprongen toe te staan bestanden te uploaden naar het opslagaccount.</span><span class="sxs-lookup"><span data-stu-id="9eb12-201">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="9eb12-202">De web-app wijzigen om afbeeldingen te uploaden</span><span class="sxs-lookup"><span data-stu-id="9eb12-202">Modify the web app to upload images</span></span>

<span data-ttu-id="9eb12-203">Met de web-app worden instellingen opgehaald uit het bestand met de naam **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-203">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="9eb12-204">In de volgende stappen maakt u het bestand met behulp van Cloud Shell. Vervolgens stelt u `window.apiBaseUrl` in op de URL voor de functie-app en `window.blobBaseUrl` op de URL voor het Azure Blob Storage-eindpunt.</span><span class="sxs-lookup"><span data-stu-id="9eb12-204">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="9eb12-205">Zorg ervoor dat de huidige map in Cloud Shell deze map is: **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-205">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="9eb12-206">Voer een query uit voor de URL voor de functie-app, en sla deze op in een bash-variabele met de naam **FUNCTION_APP_URL**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-206">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="9eb12-207">Bevestig dat de variabele juist is ingesteld.</span><span class="sxs-lookup"><span data-stu-id="9eb12-207">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="9eb12-208">Als u de basis-URI voor API-aanroepen naar de functie-app wilt instellen, maakt u **settings.js** en voegt u de URL voor de functie-app als volgt toe.</span><span class="sxs-lookup"><span data-stu-id="9eb12-208">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="9eb12-209">U kunt de wijziging aanbrengen door de volgende opdracht uit te voeren of door een opdrachtregeleditor te gebruiken, zoals VIM.</span><span class="sxs-lookup"><span data-stu-id="9eb12-209">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="9eb12-210">Controleer of het bestand juist is geschreven.</span><span class="sxs-lookup"><span data-stu-id="9eb12-210">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="9eb12-211">Voer een query uit voor de basis-URL voor Blob Storage, en sla deze op in een bash- variabele met de naam **BLOB_BASE_URL**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-211">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="9eb12-212">Bevestig dat de variabele juist is ingesteld.</span><span class="sxs-lookup"><span data-stu-id="9eb12-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="9eb12-213">Als u de basis-URI voor API-aanroepen naar de functie-app wilt instellen, voegt u de opslag-URL zoals de volgende coderegel toe aan **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-213">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="9eb12-214">U kunt de wijziging aanbrengen door de volgende opdracht uit te voeren of door een opdrachtregeleditor te gebruiken, zoals VIM.</span><span class="sxs-lookup"><span data-stu-id="9eb12-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="9eb12-215">Controleer of het bestand juist is geschreven en nu 2 regels bevat.</span><span class="sxs-lookup"><span data-stu-id="9eb12-215">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="9eb12-216">Upload het bestand naar Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="9eb12-216">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="9eb12-217">De webtoepassing testen</span><span class="sxs-lookup"><span data-stu-id="9eb12-217">Test the web application</span></span>

<span data-ttu-id="9eb12-218">In dit stadium kunnen met de galerietoepassing afbeeldingen worden geüpload naar Blob Storage, maar kunnen afbeeldingen nog niet worden weergeven.</span><span class="sxs-lookup"><span data-stu-id="9eb12-218">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="9eb12-219">Er wordt een poging gedaan om een `GetImages`-functie aan te roepen die nog niet bestaat, omdat u deze in een latere module pas maakt.</span><span class="sxs-lookup"><span data-stu-id="9eb12-219">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="9eb12-220">Deze aanroep mislukt en de webpagina lijkt te zijn vastgelopen bij ‘Analyseren...’. De afbeelding die u selecteert, wordt echter wel degelijk geüpload.</span><span class="sxs-lookup"><span data-stu-id="9eb12-220">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="9eb12-221">U kunt controleren of een afbeelding juist is geüpload door de inhoud van de container **images** in de Azure-portal te bekijken.</span><span class="sxs-lookup"><span data-stu-id="9eb12-221">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="9eb12-222">Blader in een browservenster naar de toepassing.</span><span class="sxs-lookup"><span data-stu-id="9eb12-222">In a browser window, browse to the application.</span></span> <span data-ttu-id="9eb12-223">Selecteer een afbeeldingsbestand en upload het.</span><span class="sxs-lookup"><span data-stu-id="9eb12-223">Select an image file and upload it.</span></span> <span data-ttu-id="9eb12-224">De upload wordt voltooid, maar de geüploade foto wordt nog niet weergegeven in de app, omdat de mogelijkheid om afbeeldingen weer te geven nog niet is toegevoegd.</span><span class="sxs-lookup"><span data-stu-id="9eb12-224">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="9eb12-225">(De webpagina lijkt te zijn vastgelopen bij ‘Afbeelding analyseren...’. Dit lost u later op.)</span><span class="sxs-lookup"><span data-stu-id="9eb12-225">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="9eb12-226">Controleer in Cloud Shell of de afbeelding is geüpload naar de container **images**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-226">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="9eb12-227">Verwijder, voor u verdergaat met de volgende zelfstudie, eerst alle bestanden in de container **images**.</span><span class="sxs-lookup"><span data-stu-id="9eb12-227">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="9eb12-228">Samenvatting</span><span class="sxs-lookup"><span data-stu-id="9eb12-228">Summary</span></span>

<span data-ttu-id="9eb12-229">In deze module hebt u een Azure-functie-app gemaakt en geleerd hoe u een serverloze functie kunt gebruiken om een webtoepassing toestemming te geven afbeeldingen te uploaden naar Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="9eb12-229">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="9eb12-230">Hierna leert u hoe u miniaturen maakt voor de geüploade afbeeldingen met behulp van de serverloze functie die wordt geactiveerd via een blob.</span><span class="sxs-lookup"><span data-stu-id="9eb12-230">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>
