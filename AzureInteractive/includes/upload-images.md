---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 10/12/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 3779c2e130afa7ee8d5879f30a924e258b7a41e9
ms.sourcegitcommit: fdb43556b8dcf67cb39c18e532b5fab7ac53eaee
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2018
ms.locfileid: "49315973"
---
<span data-ttu-id="05b4d-103">De toepassing die u bouwt, is een fotogalerie.</span><span class="sxs-lookup"><span data-stu-id="05b4d-103">The application that you're building is a photo gallery.</span></span> <span data-ttu-id="05b4d-104">De galerie maakt aan de clientzijde gebruik van JavaScript voor het aanroepen van API’s om afbeeldingen te uploaden en weer te geven.</span><span class="sxs-lookup"><span data-stu-id="05b4d-104">It uses client-side JavaScript to call APIs to upload and display images.</span></span> <span data-ttu-id="05b4d-105">In deze module maakt u een API met behulp van een serverloze functie waarmee een tijdelijke URL wordt gegenereerd om een afbeelding te uploaden.</span><span class="sxs-lookup"><span data-stu-id="05b4d-105">In this module, you create an API using a serverless function that generates a time-limited URL to upload an image.</span></span> <span data-ttu-id="05b4d-106">De webtoepassing maakt gebruik van de gegenereerde URL om een afbeelding naar Blob Storage te uploaden met behulp van de [Blob Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span><span class="sxs-lookup"><span data-stu-id="05b4d-106">The web application uses the generated URL to upload an image to Blob storage using the [Blob storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).</span></span>

## <a name="create-a-blob-storage-container-for-images"></a><span data-ttu-id="05b4d-107">Een Blob Storage-container maken voor afbeeldingen</span><span class="sxs-lookup"><span data-stu-id="05b4d-107">Create a blob storage container for images</span></span>

<span data-ttu-id="05b4d-108">Voor de toepassing is een afzonderlijke opslagcontainer vereist voor het uploaden en hosten van afbeeldingen.</span><span class="sxs-lookup"><span data-stu-id="05b4d-108">The application requires a separate storage container to upload and host images.</span></span>

1. <span data-ttu-id="05b4d-109">Controleer of u nog steeds bent aangemeld bij Cloud Shell (bash).</span><span class="sxs-lookup"><span data-stu-id="05b4d-109">Ensure you are still signed in to the Cloud Shell (bash).</span></span> <span data-ttu-id="05b4d-110">Als dit niet het geval is, selecteert u **Focusmodus openen** om een Cloud Shell-venster te openen.</span><span class="sxs-lookup"><span data-stu-id="05b4d-110">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span>

1.  <span data-ttu-id="05b4d-111">Maak een nieuwe container met de naam **images** in het opslagaccount met openbare toegang tot alle blobs.</span><span class="sxs-lookup"><span data-stu-id="05b4d-111">Create a new container named **images** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a><span data-ttu-id="05b4d-112">Een Azure-functie-app maken</span><span class="sxs-lookup"><span data-stu-id="05b4d-112">Create an Azure Function app</span></span>

<span data-ttu-id="05b4d-113">Azure Functions is een service voor het uitvoeren van serverloze functies.</span><span class="sxs-lookup"><span data-stu-id="05b4d-113">Azure Functions is a service for running serverless functions.</span></span> <span data-ttu-id="05b4d-114">Een serverloze functie kan worden geactiveerd (aangeroepen) voor gebeurtenissen zoals een HTTP-aanvraag of wanneer er een blob is gemaakt in de opslagcontainer.</span><span class="sxs-lookup"><span data-stu-id="05b4d-114">A serverless function can be triggered (called) by events such as an HTTP request or when a blob is created in a storage container.</span></span>

<span data-ttu-id="05b4d-115">Een Azure-functie-app is een container voor een of meer serverloze functies.</span><span class="sxs-lookup"><span data-stu-id="05b4d-115">An Azure Function app is a container for one or more serverless functions.</span></span>

<span data-ttu-id="05b4d-116">Maak een nieuwe Azure-functie-app met een unieke naam in de resourcegroep **first-serverless-app** die u eerder hebt gemaakt.</span><span class="sxs-lookup"><span data-stu-id="05b4d-116">Create a new Azure Function app with a unique name in the resource group you created earlier named **first-serverless-app**.</span></span> <span data-ttu-id="05b4d-117">Voor functie-apps is een opslagaccount vereist. In deze zelfstudie gebruikt u het bestaande opslagaccount.</span><span class="sxs-lookup"><span data-stu-id="05b4d-117">Function apps require a Storage account; in this tutorial, you use the existing storage account.</span></span>

```azurecli
az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
```

## <a name="configure-the-function-app"></a><span data-ttu-id="05b4d-118">De functie-app configureren</span><span class="sxs-lookup"><span data-stu-id="05b4d-118">Configure the function app</span></span>

<span data-ttu-id="05b4d-119">De functie-app in deze zelfstudie vereist versie 1.x van de Functions-runtime.</span><span class="sxs-lookup"><span data-stu-id="05b4d-119">The function app in this tutorial requires version 1.x of the Functions runtime.</span></span> <span data-ttu-id="05b4d-120">Door de toepassingsinstelling `FUNCTIONS_EXTENSION_VERSION` in te stellen op `~1`, dwingt u de functie-app de meest recente 1.x-versie te gebruiken.</span><span class="sxs-lookup"><span data-stu-id="05b4d-120">Setting the `FUNCTIONS_EXTENSION_VERSION` application setting to `~1` pins the function app to the latest 1.x version.</span></span> <span data-ttu-id="05b4d-121">Stel toepassingsinstellingen in met de opdracht [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).</span><span class="sxs-lookup"><span data-stu-id="05b4d-121">Set application settings with the [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) command.</span></span>

<span data-ttu-id="05b4d-122">In de volgende Azure CLI-opdracht is < app_name> de naam van uw functie-app.</span><span class="sxs-lookup"><span data-stu-id="05b4d-122">In the following Azure CLI command, \`<app_name> is the name of your function app.</span></span>

```azurecli
az functionapp config appsettings set --name <function app name> --g first-serverless-app --settings FUNCTIONS_EXTENSION_VERSION=~1
```

## <a name="create-an-http-triggered-serverless-function"></a><span data-ttu-id="05b4d-123">Een serverloze functie maken die wordt geactiveerd via HTTP</span><span class="sxs-lookup"><span data-stu-id="05b4d-123">Create an HTTP-triggered serverless function</span></span>

<span data-ttu-id="05b4d-124">Met de fotogaleriewebtoepassing wordt een HTTP-aanvraag verzonden naar een serverloze functie om een tijdelijke URL te genereren voor het veilig uploaden van een afbeelding naar Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="05b4d-124">The photo gallery web application makes an HTTP request to serverless function to generate a time-limited URL to securely upload an image to Blob storage.</span></span> <span data-ttu-id="05b4d-125">De functie wordt geactiveerd via een HTTP-aanvraag en maakt gebruik van de Azure Storage-SDK voor het genereren en retourneren van de beveiligde URL.</span><span class="sxs-lookup"><span data-stu-id="05b4d-125">The function is triggered by an HTTP request and uses the Azure Storage SDK to generate and return the secure URL.</span></span>

1. <span data-ttu-id="05b4d-126">Nadat de functie-app is gemaakt, zoekt u in de Azure-portal met behulp van het zoekvak naar de functie-app en klikt u erop om deze te openen.</span><span class="sxs-lookup"><span data-stu-id="05b4d-126">After the Function app is created, search for it in the Azure Portal using the Search box and click to open it.</span></span>

    ![De functie-app openen](media/functions-first-serverless-web-app/2-search-function-app.png)

1. <span data-ttu-id="05b4d-128">Wijs in de linkernavigatie van het venster van de functie-app naar **Functies** en klik op **+** om te beginnen met het maken van een nieuwe serverloze functie.</span><span class="sxs-lookup"><span data-stu-id="05b4d-128">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span>

    ![Een nieuwe functie maken](media/functions-first-serverless-web-app/2-new-function.png)

1. <span data-ttu-id="05b4d-130">Klik op **Aangepaste functie** voor een lijst met functiesjablonen.</span><span class="sxs-lookup"><span data-stu-id="05b4d-130">Click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="05b4d-131">Ga naar de sjabloon **HttpTrigger** en klik op de taal die moet worden gebruikt (C# of JavaScript).</span><span class="sxs-lookup"><span data-stu-id="05b4d-131">Find the **HttpTrigger** template and click the language to use (C# or JavaScript).</span></span>

1. <span data-ttu-id="05b4d-132">Gebruik deze waarden om een functie te maken waarmee een upload-URL voor een blob wordt gegenereerd.</span><span class="sxs-lookup"><span data-stu-id="05b4d-132">Use these values to create a function that generates a blob upload URL.</span></span>

    | <span data-ttu-id="05b4d-133">Instelling</span><span class="sxs-lookup"><span data-stu-id="05b4d-133">Setting</span></span>      |  <span data-ttu-id="05b4d-134">Voorgestelde waarde</span><span class="sxs-lookup"><span data-stu-id="05b4d-134">Suggested value</span></span>   | <span data-ttu-id="05b4d-135">Beschrijving</span><span class="sxs-lookup"><span data-stu-id="05b4d-135">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="05b4d-136">**Taal**</span><span class="sxs-lookup"><span data-stu-id="05b4d-136">**Language**</span></span> | <span data-ttu-id="05b4d-137">C# of JavaScript</span><span class="sxs-lookup"><span data-stu-id="05b4d-137">C# or JavaScript</span></span> | <span data-ttu-id="05b4d-138">Selecteer de gewenste taal.</span><span class="sxs-lookup"><span data-stu-id="05b4d-138">Select the language you want to use.</span></span> |
    | <span data-ttu-id="05b4d-139">**Een naam voor de functie opgeven**</span><span class="sxs-lookup"><span data-stu-id="05b4d-139">**Name your function**</span></span> | <span data-ttu-id="05b4d-140">GetUploadUrl</span><span class="sxs-lookup"><span data-stu-id="05b4d-140">GetUploadUrl</span></span> | <span data-ttu-id="05b4d-141">Typ de naam precies zoals deze wordt weergegeven, zodat de functie kan worden gedetecteerd met de toepassing.</span><span class="sxs-lookup"><span data-stu-id="05b4d-141">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="05b4d-142">**Verificatieniveau**</span><span class="sxs-lookup"><span data-stu-id="05b4d-142">**Authorization level**</span></span> | <span data-ttu-id="05b4d-143">Anoniem</span><span class="sxs-lookup"><span data-stu-id="05b4d-143">Anonymous</span></span> | <span data-ttu-id="05b4d-144">Sta toe dat de functie openbaar toegankelijk is.</span><span class="sxs-lookup"><span data-stu-id="05b4d-144">Allow the function to be accessed publicly.</span></span> |

    ![Instellingen invoeren voor een nieuwe functie die wordt geactiveerd via HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. <span data-ttu-id="05b4d-146">Klik op **Maken** om de functie te maken.</span><span class="sxs-lookup"><span data-stu-id="05b4d-146">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="05b4d-147">**C#**</span><span class="sxs-lookup"><span data-stu-id="05b4d-147">**C#**</span></span> 

    1. <span data-ttu-id="05b4d-148">Wanneer de broncode van de functie wordt weergegeven, vervangt u alle **run.csx** door de inhoud van [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span><span class="sxs-lookup"><span data-stu-id="05b4d-148">When the function's source code appears, replace all of **run.csx** with the content of [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).</span></span>

1. <span data-ttu-id="05b4d-149">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="05b4d-149">**JavaScript**</span></span> 

    1. <span data-ttu-id="05b4d-150">(JavaScript) Voor deze functie is vereist dat met het `azure-storage`-pakket van npm het SAS-token (Shared Access Signature) wordt gegenereerd dat is vereist om de beveiligde URL samen te stellen.</span><span class="sxs-lookup"><span data-stu-id="05b4d-150">(JavaScript) This function requires the `azure-storage` package from npm to generate the shared access signature (SAS) token required to build the secure URL.</span></span> <span data-ttu-id="05b4d-151">Klik in de linkernavigatie op de naam van de functie-app, en klik op **Platformfuncties** om het npm-pakket te installeren.</span><span class="sxs-lookup"><span data-stu-id="05b4d-151">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="05b4d-152">(JavaScript) Klik op **Console** om een consolevenster weer te geven.</span><span class="sxs-lookup"><span data-stu-id="05b4d-152">(JavaScript) Click **Console** to reveal a console window.</span></span>

        ![Een consolevenster openen](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. <span data-ttu-id="05b4d-154">(JavaScript) Zorg ervoor dat de map is **d:\home\site\wwwroot** door de opdracht `cd d:\home\site\wwwroot` uit te voeren.</span><span class="sxs-lookup"><span data-stu-id="05b4d-154">(JavaScript) Ensure the current directory is **d:\home\site\wwwroot** by running the command `cd d:\home\site\wwwroot`.</span></span>

    1. <span data-ttu-id="05b4d-155">(JavaScript) Voer de opdracht `npm init -y` uit om een leeg **package.json**-bestand te maken.</span><span class="sxs-lookup"><span data-stu-id="05b4d-155">(JavaScript) Run the command `npm init -y` to create an empty **package.json** file.</span></span>

    1. <span data-ttu-id="05b4d-156">(JavaScript) Voer de opdracht `npm install --save azure-storage` uit in de console om het pakket te installeren en het op te slaan in **package.json**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-156">(JavaScript) Run the command `npm install --save azure-storage` in the console to install the package and save it in **package.json**.</span></span> <span data-ttu-id="05b4d-157">Het kan een paar minuten duren om de bewerking te voltooien.</span><span class="sxs-lookup"><span data-stu-id="05b4d-157">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="05b4d-158">(JavaScript) Klik in de linkernavigatie op de functienaam (**GetUploadUrl**) om de functie weer te geven, vervang alle **index.js** door de inhoud van [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span><span class="sxs-lookup"><span data-stu-id="05b4d-158">(JavaScript) Click on the function name (**GetUploadUrl**) in the left navigation to reveal the function, replace all of **index.js** with the content of [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).</span></span>
    
        ![index.js na de update](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. <span data-ttu-id="05b4d-160">Klik onder het codevenster op **Logboeken** om het paneel met logboeken uit te vouwen.</span><span class="sxs-lookup"><span data-stu-id="05b4d-160">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="05b4d-161">Klik op **Opslaan**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-161">Click **Save**.</span></span> <span data-ttu-id="05b4d-162">Ga naar het paneel met logboeken om te controleren of de functie is gecompileerd.</span><span class="sxs-lookup"><span data-stu-id="05b4d-162">Check the logs panel to ensure the function is successfully compiled.</span></span>

<span data-ttu-id="05b4d-163">Met de functie wordt een zogenaamde SAS-URL (Shared Access Signature) gegenereerd die wordt gebruikt om een bestand te uploaden naar Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="05b4d-163">The function generates what is called a shared access signature (SAS) URL that is used to upload a file to Blob storage.</span></span> <span data-ttu-id="05b4d-164">De SAS-URL is gedurende een korte periode geldig en is alleen geschikt om een enkel bestand te uploaden.</span><span class="sxs-lookup"><span data-stu-id="05b4d-164">The SAS URL is valid for a short period of time and only allows a single file to be uploaded.</span></span> <span data-ttu-id="05b4d-165">Raadpleeg de Blob Storage-documentatie voor meer informatie over [handtekeningen voor gedeelde toegang](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span><span class="sxs-lookup"><span data-stu-id="05b4d-165">Consult the Blob storage documentation for more information on [using shared access signatures](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).</span></span>


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a><span data-ttu-id="05b4d-166">Een omgevingsvariabele toevoegen voor de opslagverbindingsreeks</span><span class="sxs-lookup"><span data-stu-id="05b4d-166">Add an environment variable for the storage connection string</span></span>

<span data-ttu-id="05b4d-167">Voor de functie die u zojuist hebt gemaakt, is een verbindingsreeks vereist voor het opslagaccount, zodat de SAS-URL kan worden gegenereerd.</span><span class="sxs-lookup"><span data-stu-id="05b4d-167">The function you just created requires a connection string for the Storage account so that it can generate the SAS URL.</span></span> <span data-ttu-id="05b4d-168">In plaats van de verbindingsreeks in de hoofdtekst van de functie in code vast te leggen, kan deze worden opgeslagen als een toepassingsinstelling.</span><span class="sxs-lookup"><span data-stu-id="05b4d-168">Instead of hardcoding the connection string in the function's body, it can be stored as an application setting.</span></span> <span data-ttu-id="05b4d-169">Toepassingsinstellingen zijn toegankelijk als omgevingsvariabelen voor alle functies in de functie-app.</span><span class="sxs-lookup"><span data-stu-id="05b4d-169">Application settings are accessible as environment variables by all functions in the Function app.</span></span>

1. <span data-ttu-id="05b4d-170">Voer in Cloud Shell een query uit voor de verbindingsreeks van het opslagaccount en sla deze op in een bash-variabele met de naam **STORAGE_CONNECTION_STRING**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-170">In the Cloud Shell, query the Storage account connection string and save it to a bash variable named **STORAGE_CONNECTION_STRING**.</span></span>

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    <span data-ttu-id="05b4d-171">Bevestig dat de variabele juist is ingesteld.</span><span class="sxs-lookup"><span data-stu-id="05b4d-171">Confirm the variable is set successfully.</span></span>

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. <span data-ttu-id="05b4d-172">Maak een nieuwe toepassingsinstelling met de naam **AZURE_STORAGE_CONNECTION_STRING** met behulp van de waarde die is opgeslagen in de vorige stap.</span><span class="sxs-lookup"><span data-stu-id="05b4d-172">Create a new application setting named **AZURE_STORAGE_CONNECTION_STRING** using the value saved from the previous step.</span></span>

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    <span data-ttu-id="05b4d-173">Controleer of de uitvoer van de opdracht de nieuwe toepassingsinstelling bevat met de juiste waarde.</span><span class="sxs-lookup"><span data-stu-id="05b4d-173">Confirm that the command's output contains the new application setting with the correct value.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="05b4d-174">De serverloze functie testen</span><span class="sxs-lookup"><span data-stu-id="05b4d-174">Test the serverless function</span></span>

<span data-ttu-id="05b4d-175">Naast het maken en bewerken van functies biedt de Azure-portal ook een ingebouwd hulpprogramma voor het testen van functies.</span><span class="sxs-lookup"><span data-stu-id="05b4d-175">In addition to creating and editing functions, the Azure portal also provides an built-in tool for testing functions.</span></span>

1. <span data-ttu-id="05b4d-176">Als u de serverloze HTTP-functie wilt testen, klikt u aan de rechterkant van het codevenster op het tabblad **Testen** om het testpaneel uit te vouwen.</span><span class="sxs-lookup"><span data-stu-id="05b4d-176">To test the HTTP serverless function, click on the **Test** tab on the right of the code window to expand the test panel.</span></span>

1. <span data-ttu-id="05b4d-177">Wijzig de **HTTP-methode** in **GET**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-177">Change the **Http method** to **GET**.</span></span>

1. <span data-ttu-id="05b4d-178">Klik onder **Query** op *Parameter toevoegen*\* en voeg de volgende parameter toe:</span><span class="sxs-lookup"><span data-stu-id="05b4d-178">Under **Query**, click *Add parameter*\* and add the following parameter:</span></span>

    | <span data-ttu-id="05b4d-179">naam</span><span class="sxs-lookup"><span data-stu-id="05b4d-179">name</span></span>      |  <span data-ttu-id="05b4d-180">waarde</span><span class="sxs-lookup"><span data-stu-id="05b4d-180">value</span></span>   | 
    | --- | --- |
    | <span data-ttu-id="05b4d-181">bestandsnaam</span><span class="sxs-lookup"><span data-stu-id="05b4d-181">filename</span></span> | <span data-ttu-id="05b4d-182">image1.jpg</span><span class="sxs-lookup"><span data-stu-id="05b4d-182">image1.jpg</span></span> |

1. <span data-ttu-id="05b4d-183">Klik in het testpaneel op **Uitvoeren** om een HTTP-aanvraag naar de functie te verzenden.</span><span class="sxs-lookup"><span data-stu-id="05b4d-183">Click **Run** in the test panel to send an HTTP request to the function.</span></span>

1. <span data-ttu-id="05b4d-184">De functie retourneert een upload-URL in de uitvoer.</span><span class="sxs-lookup"><span data-stu-id="05b4d-184">The function returns an upload URL in the output.</span></span> <span data-ttu-id="05b4d-185">De uitvoering van de functie wordt weergegeven in het paneel met logboeken.</span><span class="sxs-lookup"><span data-stu-id="05b4d-185">The function execution appears in the logs panel.</span></span>

    ![Logboeken waarin wordt weergegeven dat de functie juist is uitgevoerd](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a><span data-ttu-id="05b4d-187">CORS configureren in de functie-app</span><span class="sxs-lookup"><span data-stu-id="05b4d-187">Configure CORS in the function app</span></span>

<span data-ttu-id="05b4d-188">Omdat de front-end van de app wordt gehost in Blob Storage, heeft deze een andere domeinnaam dan de Azure-functie-app.</span><span class="sxs-lookup"><span data-stu-id="05b4d-188">Because the app's frontend is hosted in Blob storage, it has a different domain name than the Azure Function app.</span></span> <span data-ttu-id="05b4d-189">De functie-app moet zijn geconfigureerd voor CORS (Cross-Origin Resource Sharing) om ervoor te zorgen dat de functie die u zojuist hebt gemaakt, kan worden aangeroepen met JavaScript aan de clientzijde.</span><span class="sxs-lookup"><span data-stu-id="05b4d-189">For the client-side JavaScript to successfully call the function you just created, the function app needs to be configured for cross-origin resource sharing (CORS).</span></span>

1. <span data-ttu-id="05b4d-190">Klik in de linkernavigatiebalk van het venster voor de functie-app op de naam van de functie-app.</span><span class="sxs-lookup"><span data-stu-id="05b4d-190">In the left navigation bar of the function app window, click on the name of your function app.</span></span>

1. <span data-ttu-id="05b4d-191">Klik op **Platformfuncties** om een lijst met geavanceerde functies weer te geven.</span><span class="sxs-lookup"><span data-stu-id="05b4d-191">Click on **Platform features** to view a list of advanced features.</span></span>

1. <span data-ttu-id="05b4d-192">Klik onder **API** op **CORS**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-192">Under **API**, click **CORS**.</span></span>

    ![CORS selecteren](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. <span data-ttu-id="05b4d-194">Voeg een toegestane oorsprong toe voor de toepassings-URL uit de vorige module, waarbij u de afsluitende **/** weglaat. (Bijvoorbeeld: `https://firstserverlessweb.z4.web.core.windows.net`)</span><span class="sxs-lookup"><span data-stu-id="05b4d-194">Add an allow origin for the application URL from the previous module, omitting the trailing **/** (for example: `https://firstserverlessweb.z4.web.core.windows.net`).</span></span>

    ![CORS-instellingen waarin wordt weergegeven dat een URL voor een serverloze web-app is toegevoegd](media/functions-first-serverless-web-app/2-add-cors.png)

1. <span data-ttu-id="05b4d-196">Klik op **Opslaan**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-196">Click **Save**.</span></span>

1. <span data-ttu-id="05b4d-197">C#</span><span class="sxs-lookup"><span data-stu-id="05b4d-197">C#</span></span>

   1. <span data-ttu-id="05b4d-198">(C#) Ga terug naar de `GetUploadUrl`-functie en selecteer vervolgens het tabblad **Integreren**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-198">(C#) Navigate back to the `GetUploadUrl` function, and then select the **Integrate** tab.</span></span>

   1. <span data-ttu-id="05b4d-199">(C#) Selecteer onder Geselecteerde HTTP-methoden de methode **OPTIONS**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-199">(C#) Under Selected HTTP methods, select **OPTIONS**.</span></span>

      <span data-ttu-id="05b4d-200">**GET**, **POST** en **OPTIONS** moeten allemaal zijn geselecteerd.</span><span class="sxs-lookup"><span data-stu-id="05b4d-200">**GET**, **POST**, and **OPTIONS** should all be selected.</span></span> <span data-ttu-id="05b4d-201">CORS maakt gebruik van de OPTIONS-methode. Deze methode is niet standaard geselecteerd voor C#-functies.</span><span class="sxs-lookup"><span data-stu-id="05b4d-201">CORS uses the OPTIONS method, which is not selected by default for C# functions.</span></span>  

   1. <span data-ttu-id="05b4d-202">(C#) Klik op **Opslaan**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-202">(C#) Click **Save**.</span></span>

1. <span data-ttu-id="05b4d-203">Blijf in de portal, ga naar de functie-app en selecteer het tabblad **Overzicht**. Klik vervolgens op **Opnieuw starten** om ervoor te zorgen dat de wijzigingen voor CORS worden doorgevoerd.</span><span class="sxs-lookup"><span data-stu-id="05b4d-203">Still in the portal, navigate to the function app, select the **Overview** tab, and then click **Restart** to make sure that the changes for CORS take effect.</span></span>

## <a name="configure-cors-in-the-storage-account"></a><span data-ttu-id="05b4d-204">CORS configureren in het opslagaccount</span><span class="sxs-lookup"><span data-stu-id="05b4d-204">Configure CORS in the Storage account</span></span>

<span data-ttu-id="05b4d-205">Omdat via de app ook JavaScript-aanroepen aan de clientzijde naar Blob Storage worden verzonden om bestanden te uploaden, moet u ook het opslagaccount voor CORS configureren.</span><span class="sxs-lookup"><span data-stu-id="05b4d-205">Because the app also makes client-side JavaScript calls to Blob Storage to upload files, you also have to configure the Storage account for CORS.</span></span>

1. <span data-ttu-id="05b4d-206">Voer de volgende opdracht uit om alle oorsprongen toe te staan bestanden te uploaden naar het opslagaccount.</span><span class="sxs-lookup"><span data-stu-id="05b4d-206">Run the following command to allow all origins to upload files to the Storage account.</span></span>

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a><span data-ttu-id="05b4d-207">De web-app wijzigen om afbeeldingen te uploaden</span><span class="sxs-lookup"><span data-stu-id="05b4d-207">Modify the web app to upload images</span></span>

<span data-ttu-id="05b4d-208">Met de web-app worden instellingen opgehaald uit het bestand met de naam **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-208">The web app retrieves settings from a file named **settings.js**.</span></span> <span data-ttu-id="05b4d-209">In de volgende stappen maakt u het bestand met behulp van Cloud Shell. Vervolgens stelt u `window.apiBaseUrl` in op de URL voor de functie-app en `window.blobBaseUrl` op de URL voor het Azure Blob Storage-eindpunt.</span><span class="sxs-lookup"><span data-stu-id="05b4d-209">In the following steps, you create the file using Cloud Shell, then set `window.apiBaseUrl` to the URL of the Function app and `window.blobBaseUrl` to the URL of the Azure Blob Storage endpoint.</span></span>

1. <span data-ttu-id="05b4d-210">Zorg ervoor dat de huidige map in Cloud Shell deze map is: **www/dist**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-210">In the Cloud Shell, ensure that the current directory is the **www/dist** folder.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. <span data-ttu-id="05b4d-211">Voer een query uit voor de URL voor de functie-app, en sla deze op in een bash-variabele met de naam **FUNCTION_APP_URL**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-211">Query the function app's URL and store it in a bash variable named **FUNCTION_APP_URL**.</span></span>

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    <span data-ttu-id="05b4d-212">Bevestig dat de variabele juist is ingesteld.</span><span class="sxs-lookup"><span data-stu-id="05b4d-212">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. <span data-ttu-id="05b4d-213">Als u de basis-URI voor API-aanroepen naar de functie-app wilt instellen, maakt u **settings.js** en voegt u de URL voor de functie-app als volgt toe.</span><span class="sxs-lookup"><span data-stu-id="05b4d-213">To set the base URI of API calls to your function app, create **settings.js** and add the function app URL like the following.</span></span>

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    <span data-ttu-id="05b4d-214">U kunt de wijziging aanbrengen door de volgende opdracht uit te voeren of door een opdrachtregeleditor te gebruiken, zoals VIM.</span><span class="sxs-lookup"><span data-stu-id="05b4d-214">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    <span data-ttu-id="05b4d-215">Controleer of het bestand juist is geschreven.</span><span class="sxs-lookup"><span data-stu-id="05b4d-215">Confirm the file was successfully written.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="05b4d-216">Voer een query uit voor de basis-URL voor Blob Storage, en sla deze op in een bash- variabele met de naam **BLOB_BASE_URL**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-216">Query the Blob Storage base URL and store it in a bash variable named **BLOB_BASE_URL**.</span></span>

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    <span data-ttu-id="05b4d-217">Bevestig dat de variabele juist is ingesteld.</span><span class="sxs-lookup"><span data-stu-id="05b4d-217">Confirm the variable is correctly set.</span></span>

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. <span data-ttu-id="05b4d-218">Als u de basis-URI voor API-aanroepen naar de functie-app wilt instellen, voegt u de opslag-URL zoals de volgende coderegel toe aan **settings.js**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-218">To set the base URI of API calls to your function app, append the storage URL like the following line of code to **settings.js**.</span></span>

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    <span data-ttu-id="05b4d-219">U kunt de wijziging aanbrengen door de volgende opdracht uit te voeren of door een opdrachtregeleditor te gebruiken, zoals VIM.</span><span class="sxs-lookup"><span data-stu-id="05b4d-219">You can make the change by running the following command or by using a command-line editor like VIM.</span></span>

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    <span data-ttu-id="05b4d-220">Controleer of het bestand juist is geschreven en nu 2 regels bevat.</span><span class="sxs-lookup"><span data-stu-id="05b4d-220">Confirm the file was successfully written and it now contains 2 lines.</span></span>

    ```azurecli
    cat settings.js
    ```

1. <span data-ttu-id="05b4d-221">Upload het bestand naar Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="05b4d-221">Upload the file to Blob storage.</span></span>

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a><span data-ttu-id="05b4d-222">De webtoepassing testen</span><span class="sxs-lookup"><span data-stu-id="05b4d-222">Test the web application</span></span>

<span data-ttu-id="05b4d-223">In dit stadium kunnen met de galerietoepassing afbeeldingen worden geüpload naar Blob Storage, maar kunnen afbeeldingen nog niet worden weergeven.</span><span class="sxs-lookup"><span data-stu-id="05b4d-223">At this point, the gallery application is able to upload an image to Blob storage, but it can't display images yet.</span></span> <span data-ttu-id="05b4d-224">Er wordt een poging gedaan om een `GetImages`-functie aan te roepen die nog niet bestaat, omdat u deze in een latere module pas maakt.</span><span class="sxs-lookup"><span data-stu-id="05b4d-224">It will try to call a `GetImages` function that doesn't exist yet because you create it in a later module.</span></span> <span data-ttu-id="05b4d-225">Deze aanroep mislukt en de webpagina lijkt te zijn vastgelopen bij ‘Analyseren...’. De afbeelding die u selecteert, wordt echter wel degelijk geüpload.</span><span class="sxs-lookup"><span data-stu-id="05b4d-225">That call will fail, and the web page will appear to be stuck on "Analyzing...", but the image you select will be successfully uploaded.</span></span>

<span data-ttu-id="05b4d-226">U kunt controleren of een afbeelding juist is geüpload door de inhoud van de container **images** in de Azure-portal te bekijken.</span><span class="sxs-lookup"><span data-stu-id="05b4d-226">You can verify an image is successfully uploaded by checking the contents of the **images** container in the Azure portal.</span></span>

1. <span data-ttu-id="05b4d-227">Blader in een browservenster naar de toepassing.</span><span class="sxs-lookup"><span data-stu-id="05b4d-227">In a browser window, browse to the application.</span></span> <span data-ttu-id="05b4d-228">Selecteer een afbeeldingsbestand en upload het.</span><span class="sxs-lookup"><span data-stu-id="05b4d-228">Select an image file and upload it.</span></span> <span data-ttu-id="05b4d-229">De upload wordt voltooid, maar de geüploade foto wordt nog niet weergegeven in de app, omdat de mogelijkheid om afbeeldingen weer te geven nog niet is toegevoegd.</span><span class="sxs-lookup"><span data-stu-id="05b4d-229">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span> <span data-ttu-id="05b4d-230">(De webpagina lijkt te zijn vastgelopen bij ‘Afbeelding analyseren...’. Dit lost u later op.)</span><span class="sxs-lookup"><span data-stu-id="05b4d-230">(The web page appears to be stuck on "Analyzing image..."; you'll fix that later.)</span></span>

1. <span data-ttu-id="05b4d-231">Controleer in Cloud Shell of de afbeelding is geüpload naar de container **images**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-231">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="05b4d-232">Verwijder, voor u verdergaat met de volgende zelfstudie, eerst alle bestanden in de container **images**.</span><span class="sxs-lookup"><span data-stu-id="05b4d-232">Before moving on to the next tutorial, delete all files in the **images** container.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="05b4d-233">Samenvatting</span><span class="sxs-lookup"><span data-stu-id="05b4d-233">Summary</span></span>

<span data-ttu-id="05b4d-234">In deze module hebt u een Azure-functie-app gemaakt en geleerd hoe u een serverloze functie kunt gebruiken om een webtoepassing toestemming te geven afbeeldingen te uploaden naar Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="05b4d-234">In this module, you created an Azure Function app and learned how to use a serverless function to allow a web application to upload images to Blob storage.</span></span> <span data-ttu-id="05b4d-235">Hierna leert u hoe u miniaturen maakt voor de geüploade afbeeldingen met behulp van de serverloze functie die wordt geactiveerd via een blob.</span><span class="sxs-lookup"><span data-stu-id="05b4d-235">Next, you learn how to create thumbnails for the uploaded images using a Blob triggered serverless function.</span></span>
