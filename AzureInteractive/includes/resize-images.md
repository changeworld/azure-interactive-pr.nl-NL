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
ms.openlocfilehash: 88b0ac838dfa8e097a30cc6cef591377e4a95ad8
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079186"
---
<span data-ttu-id="46e8f-103">In de vorige module hebt u gezien hoe een serverloze functie het veilig uploaden van afbeeldingen vanuit een webtoepassing naar Blob Storage mogelijk maakt.</span><span class="sxs-lookup"><span data-stu-id="46e8f-103">In the previous module, you saw how a serverless function can facilitate the secure uploading of images to Blob storage from a web application.</span></span> <span data-ttu-id="46e8f-104">In deze module maakt u een andere serverloze functie waarmee geüploade afbeeldingen worden gedetecteerd en hiervoor miniaturen worden gemaakt.</span><span class="sxs-lookup"><span data-stu-id="46e8f-104">In this module, you create another serverless function to watch for uploaded images and create thumbnails from them.</span></span>

## <a name="create-a-blob-storage-container-for-thumbnails"></a><span data-ttu-id="46e8f-105">Een Blob Storage-container maken voor miniaturen</span><span class="sxs-lookup"><span data-stu-id="46e8f-105">Create a blob storage container for thumbnails</span></span>

<span data-ttu-id="46e8f-106">De afbeeldingen zijn op ware grootte opgeslagen in een container met de naam **images**.</span><span class="sxs-lookup"><span data-stu-id="46e8f-106">The full size images are stored in a container named **images**.</span></span> <span data-ttu-id="46e8f-107">U hebt nog een container nodig om miniaturen van deze afbeeldingen op te slaan.</span><span class="sxs-lookup"><span data-stu-id="46e8f-107">You need another container to store thumbnails of those images.</span></span>

1. <span data-ttu-id="46e8f-108">Controleer of u nog steeds bent aangemeld bij Cloud Shell (bash).</span><span class="sxs-lookup"><span data-stu-id="46e8f-108">Ensure you are still signed in to the Cloud Shell (bash).</span></span>  <span data-ttu-id="46e8f-109">Als dit niet het geval is, selecteert u **Focusmodus openen** om een Cloud Shell-venster te openen.</span><span class="sxs-lookup"><span data-stu-id="46e8f-109">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="46e8f-110">Maak een nieuwe container met de naam **thumbnails** in het opslagaccount met openbare toegang tot alle blobs.</span><span class="sxs-lookup"><span data-stu-id="46e8f-110">Create a new container named **thumbnails** in your storage account with public access to all blobs.</span></span>

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a><span data-ttu-id="46e8f-111">Een serverloze functie maken die wordt geactiveerd via een blob</span><span class="sxs-lookup"><span data-stu-id="46e8f-111">Create a blob-triggered serverless function</span></span>

<span data-ttu-id="46e8f-112">Via een trigger wordt bepaald hoe een functie wordt aangeroepen.</span><span class="sxs-lookup"><span data-stu-id="46e8f-112">A trigger defines how a function is invoked.</span></span> <span data-ttu-id="46e8f-113">Voor de functie die u nu gaat maken, wordt een blobtrigger gebruikt: de functie wordt automatisch aangeroepen wanneer een blob (afbeeldingsbestand) wordt geüpload naar de container **images**.</span><span class="sxs-lookup"><span data-stu-id="46e8f-113">The function you create next uses a blob trigger: the function is automatically invoked when a blob (image file) is uploaded to the **images** container.</span></span> <span data-ttu-id="46e8f-114">Een functie moet één trigger hebben.</span><span class="sxs-lookup"><span data-stu-id="46e8f-114">A function must have one trigger.</span></span> <span data-ttu-id="46e8f-115">Aan triggers zijn gegevens gekoppeld. Meestal is dit de nettolading waarmee de functie is geactiveerd.</span><span class="sxs-lookup"><span data-stu-id="46e8f-115">Triggers have associated data, which is usually the payload that triggered the function.</span></span>

<span data-ttu-id="46e8f-116">Met bindingen wordt gedefinieerd hoe gegevens in Azure of in services van derden worden gelezen of geschreven met de functie.</span><span class="sxs-lookup"><span data-stu-id="46e8f-116">Bindings define how a function reads or writes data in Azure or third-party services.</span></span> <span data-ttu-id="46e8f-117">Met deze functie wordt een miniatuurversie van de afbeelding gemaakt waarmee de functie wordt geactiveerd. De miniatuur wordt opgeslagen in de container *thumbnails*.</span><span class="sxs-lookup"><span data-stu-id="46e8f-117">This function creates a thumbnail version of the image that triggers it and saves the thumbnail in a *thumbnails* container.</span></span>

1. <span data-ttu-id="46e8f-118">Open de functie-app in de Azure-portal.</span><span class="sxs-lookup"><span data-stu-id="46e8f-118">Open your function app in the Azure Portal.</span></span>

1. <span data-ttu-id="46e8f-119">Wijs in de linkernavigatie van het venster van de functie-app naar **Functies** en klik op **+** om te beginnen met het maken van een nieuwe serverloze functie.</span><span class="sxs-lookup"><span data-stu-id="46e8f-119">In the function app window's left hand navigation, hover over **Functions** and click **+** to start creating a new serverless function.</span></span> <span data-ttu-id="46e8f-120">Als er een snelstartpagina wordt weergegeven, klikt u op **Aangepaste functie** voor een lijst met functiesjablonen.</span><span class="sxs-lookup"><span data-stu-id="46e8f-120">If a quickstart page appears, click **Custom function** to see a list of function templates.</span></span>

1. <span data-ttu-id="46e8f-121">Ga naar de sjabloon **BlobTrigger** en selecteer deze.</span><span class="sxs-lookup"><span data-stu-id="46e8f-121">Find the **BlobTrigger** template and select it.</span></span>

1. <span data-ttu-id="46e8f-122">Gebruik deze waarden om een functie te maken waarmee miniaturen worden gemaakt bij het uploaden van afbeeldingen.</span><span class="sxs-lookup"><span data-stu-id="46e8f-122">Use these values to create a function that creates thumbnails as images are uploaded.</span></span>

    | <span data-ttu-id="46e8f-123">Instelling</span><span class="sxs-lookup"><span data-stu-id="46e8f-123">Setting</span></span>      |  <span data-ttu-id="46e8f-124">Voorgestelde waarde</span><span class="sxs-lookup"><span data-stu-id="46e8f-124">Suggested value</span></span>   | <span data-ttu-id="46e8f-125">Beschrijving</span><span class="sxs-lookup"><span data-stu-id="46e8f-125">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="46e8f-126">**Taal**</span><span class="sxs-lookup"><span data-stu-id="46e8f-126">**Language**</span></span> | <span data-ttu-id="46e8f-127">C# of JavaScript</span><span class="sxs-lookup"><span data-stu-id="46e8f-127">C# or JavaScript</span></span> | <span data-ttu-id="46e8f-128">Kies de voorkeurstaal.</span><span class="sxs-lookup"><span data-stu-id="46e8f-128">Choose your preferred language.</span></span> |
    | <span data-ttu-id="46e8f-129">**Een naam voor de functie opgeven**</span><span class="sxs-lookup"><span data-stu-id="46e8f-129">**Name your function**</span></span> | <span data-ttu-id="46e8f-130">ResizeImage</span><span class="sxs-lookup"><span data-stu-id="46e8f-130">ResizeImage</span></span> | <span data-ttu-id="46e8f-131">Typ de naam precies zoals deze wordt weergegeven, zodat de functie kan worden gedetecteerd met de toepassing.</span><span class="sxs-lookup"><span data-stu-id="46e8f-131">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="46e8f-132">**Pad**</span><span class="sxs-lookup"><span data-stu-id="46e8f-132">**Path**</span></span> | <span data-ttu-id="46e8f-133">images/{name}</span><span class="sxs-lookup"><span data-stu-id="46e8f-133">images/{name}</span></span> | <span data-ttu-id="46e8f-134">Voer de functie uit wanneer een bestand wordt weergegeven in de container **images**.</span><span class="sxs-lookup"><span data-stu-id="46e8f-134">Execute the function when a file appears in the **images** container.</span></span> |
    | <span data-ttu-id="46e8f-135">**Opslagaccountinformatie**</span><span class="sxs-lookup"><span data-stu-id="46e8f-135">**Storage account information**</span></span> | <span data-ttu-id="46e8f-136">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="46e8f-136">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="46e8f-137">Gebruik de omgevingsvariabele die eerder is gemaakt met de verbindingsreeks.</span><span class="sxs-lookup"><span data-stu-id="46e8f-137">Use the environment variable previously created with the connection string.</span></span> |

    ![Voer instellingen in voor de nieuwe functie](media/functions-first-serverless-web-app/3-new-function.png)

1. <span data-ttu-id="46e8f-139">Klik op **Maken** om de functie te maken.</span><span class="sxs-lookup"><span data-stu-id="46e8f-139">Click **Create** to create the function.</span></span>

1. <span data-ttu-id="46e8f-140">Wanneer de functie is gemaakt, klikt u op **Integreren** om de bijbehorende trigger-, invoer- en uitvoerbindingen te bekijken.</span><span class="sxs-lookup"><span data-stu-id="46e8f-140">When the function is created, click **Integrate** to view its trigger, input, and output bindings.</span></span>

1. <span data-ttu-id="46e8f-141">Klik op **Nieuwe uitvoer** om een nieuwe uitvoerbinding te maken.</span><span class="sxs-lookup"><span data-stu-id="46e8f-141">Click **New output** to create a new output binding.</span></span>

    ![Selecteer op het tabblad Integreren de optie Nieuwe uitvoer](media/functions-first-serverless-web-app/3-new-output.jpg)

1. <span data-ttu-id="46e8f-143">Selecteer **Azure Blob Storage** en klik op **Selecteren**.</span><span class="sxs-lookup"><span data-stu-id="46e8f-143">Select **Azure Blob Storage** and click **Select**.</span></span> <span data-ttu-id="46e8f-144">U moet misschien omlaag schuiven om de knop **Selecteren** te zien.</span><span class="sxs-lookup"><span data-stu-id="46e8f-144">You may have to scroll down to see the **Select** button.</span></span>

    ![Azure Blob Storage selecteren](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. <span data-ttu-id="46e8f-146">Voer de volgende waarden in.</span><span class="sxs-lookup"><span data-stu-id="46e8f-146">Enter the following values.</span></span>

    | <span data-ttu-id="46e8f-147">Instelling</span><span class="sxs-lookup"><span data-stu-id="46e8f-147">Setting</span></span>      |  <span data-ttu-id="46e8f-148">Voorgestelde waarde</span><span class="sxs-lookup"><span data-stu-id="46e8f-148">Suggested value</span></span>   | <span data-ttu-id="46e8f-149">Beschrijving</span><span class="sxs-lookup"><span data-stu-id="46e8f-149">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="46e8f-150">**Naam van blobparameter**</span><span class="sxs-lookup"><span data-stu-id="46e8f-150">**Blob parameter name**</span></span> | <span data-ttu-id="46e8f-151">thumbnail</span><span class="sxs-lookup"><span data-stu-id="46e8f-151">thumbnail</span></span> | <span data-ttu-id="46e8f-152">De functie maakt gebruik van de parameter met deze naam om de miniatuur te schrijven.</span><span class="sxs-lookup"><span data-stu-id="46e8f-152">The function uses the parameter with this name to write the thumbnail.</span></span> |
    | <span data-ttu-id="46e8f-153">**Retourwaarde van functie gebruiken**</span><span class="sxs-lookup"><span data-stu-id="46e8f-153">**Use function return value**</span></span> | <span data-ttu-id="46e8f-154">Nee</span><span class="sxs-lookup"><span data-stu-id="46e8f-154">No</span></span> |  |
    | <span data-ttu-id="46e8f-155">**Pad**</span><span class="sxs-lookup"><span data-stu-id="46e8f-155">**Path**</span></span> | <span data-ttu-id="46e8f-156">thumbnails/{name}</span><span class="sxs-lookup"><span data-stu-id="46e8f-156">thumbnails/{name}</span></span> | <span data-ttu-id="46e8f-157">De miniaturen worden naar een container geschreven met de naam **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="46e8f-157">The thumbnails are written to a container named **thumbnails**.</span></span> |
    | <span data-ttu-id="46e8f-158">**Opslagaccountverbinding**</span><span class="sxs-lookup"><span data-stu-id="46e8f-158">**Storage account connection**</span></span> | <span data-ttu-id="46e8f-159">AZURE_STORAGE_CONNECTION_STRING</span><span class="sxs-lookup"><span data-stu-id="46e8f-159">AZURE_STORAGE_CONNECTION_STRING</span></span> | <span data-ttu-id="46e8f-160">Gebruik de omgevingsvariabele die eerder is gemaakt met de verbindingsreeks.</span><span class="sxs-lookup"><span data-stu-id="46e8f-160">Use the environment variable previously created with the connection string.</span></span> |

    ![Instellingen invoeren voor de blob-uitvoerbinding](media/functions-first-serverless-web-app/3-blob-output.png)

1. <span data-ttu-id="46e8f-162">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="46e8f-162">**JavaScript**</span></span>

    1. <span data-ttu-id="46e8f-163">(JavaScript) Klik in de rechterbovenhoek van het venster op **Geavanceerde editor** om de JSON weer te geven die de bindingen vertegenwoordigt.</span><span class="sxs-lookup"><span data-stu-id="46e8f-163">(JavaScript) Click on **Advanced editor** in the top right corner of the window to reveal the JSON representing the bindings.</span></span>

    1. <span data-ttu-id="46e8f-164">(JavaScript) Voeg in de `blobTrigger`-binding een eigenschap toe met de naam `dataType` met een waarde van `binary`.</span><span class="sxs-lookup"><span data-stu-id="46e8f-164">(JavaScript) In the `blobTrigger` binding, add a property named `dataType` with a value of `binary`.</span></span> <span data-ttu-id="46e8f-165">Hierdoor wordt de binding geconfigureerd om de blobinhoud als binaire gegevens door te geven aan de functie.</span><span class="sxs-lookup"><span data-stu-id="46e8f-165">This configures the binding to pass the blob contents to the function as binary data.</span></span>

    ```json
    {
      "name": "myBlob",
      "type": "blobTrigger",
      "direction": "in",
      "path": "images/{name}",
      "connection": "AZURE_STORAGE_CONNECTION_STRING",
      "dataType": "binary"
    }
    ```

1. <span data-ttu-id="46e8f-166">Klik op **Opslaan** om de nieuwe binding te maken.</span><span class="sxs-lookup"><span data-stu-id="46e8f-166">Click **Save** to create the new binding.</span></span>

1. <span data-ttu-id="46e8f-167">**C#**</span><span class="sxs-lookup"><span data-stu-id="46e8f-167">**C#**</span></span>

    1. <span data-ttu-id="46e8f-168">(C#) Selecteer in de linkernavigatie de functienaam **ResizeImage** om de broncode van de functie te openen.</span><span class="sxs-lookup"><span data-stu-id="46e8f-168">(C#) Select the **ResizeImage** function name on the left navigation to open the function's source code.</span></span>

    1. <span data-ttu-id="46e8f-169">(C#) Voor de functie is een NuGet-pakket met de naam **ImageResizer** vereist om de miniaturen te genereren.</span><span class="sxs-lookup"><span data-stu-id="46e8f-169">(C#) The function requires a NuGet package called **ImageResizer** to generate the thumbnails.</span></span> <span data-ttu-id="46e8f-170">NuGet-pakketten wordt met behulp van een **project.json**-bestand toegevoegd aan C#-functies.</span><span class="sxs-lookup"><span data-stu-id="46e8f-170">NuGet packages are added to C# functions using a **project.json** file.</span></span> <span data-ttu-id="46e8f-171">Als u het bestand wilt maken, klikt u aan de rechterkant op **Bestanden weergeven** om de bestanden weer te geven waaruit de functie bestaat.</span><span class="sxs-lookup"><span data-stu-id="46e8f-171">To create the file, click **View Files** on the right to reveal the files that make up the function.</span></span>
    
    1. <span data-ttu-id="46e8f-172">(C#) Klik op **Toevoegen** om een nieuw bestand toe te voegen met de naam **project.json**.</span><span class="sxs-lookup"><span data-stu-id="46e8f-172">(C#) Click **Add** to add a new file named **project.json**.</span></span>
    
    1. <span data-ttu-id="46e8f-173">(C#) Kopieer de inhoud van [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) naar het zojuist gemaakte bestand.</span><span class="sxs-lookup"><span data-stu-id="46e8f-173">(C#) Copy the contents of [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) into the newly created file.</span></span> <span data-ttu-id="46e8f-174">Sla het bestand op.</span><span class="sxs-lookup"><span data-stu-id="46e8f-174">Save the file.</span></span> <span data-ttu-id="46e8f-175">Pakketten worden automatisch hersteld als het bestand is bijgewerkt.</span><span class="sxs-lookup"><span data-stu-id="46e8f-175">Packages are automatically restored when the file is updated.</span></span>
    
        ![project.json-bestand met ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. <span data-ttu-id="46e8f-177">(C#) Selecteer onder **Bestanden weergeven** de optie **run.csx** en vervang de bijbehorende inhoud door de inhoud in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span><span class="sxs-lookup"><span data-stu-id="46e8f-177">(C#) Select **run.csx** under **View Files** and replace its content with the content in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).</span></span>

1. <span data-ttu-id="46e8f-178">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="46e8f-178">**JavaScript**</span></span> 

    1. <span data-ttu-id="46e8f-179">(JavaScript) Voor deze functie is het `jimp`-pakket van npm vereist om het formaat van de foto te wijzigen.</span><span class="sxs-lookup"><span data-stu-id="46e8f-179">(JavaScript) This function requires the `jimp` package from npm to resize the photo.</span></span> <span data-ttu-id="46e8f-180">Klik in de linkernavigatie op de naam van de functie-app, en klik op **Platformfuncties** om het npm-pakket te installeren.</span><span class="sxs-lookup"><span data-stu-id="46e8f-180">To install the npm package, click on the Function App's name on the left navigation and click **Platform features**.</span></span>

    1. <span data-ttu-id="46e8f-181">(JavaScript) Klik op **Console** om een consolevenster weer te geven.</span><span class="sxs-lookup"><span data-stu-id="46e8f-181">(JavaScript) Click **Console** to reveal a console window.</span></span>

    1. <span data-ttu-id="46e8f-182">(JavaScript) Voer de opdracht `npm install jimp` uit in de console.</span><span class="sxs-lookup"><span data-stu-id="46e8f-182">(JavaScript) Run the command `npm install jimp` in the console.</span></span> <span data-ttu-id="46e8f-183">Het kan een paar minuten duren om de bewerking te voltooien.</span><span class="sxs-lookup"><span data-stu-id="46e8f-183">It may take a minute or two to complete the operation.</span></span>

    1. <span data-ttu-id="46e8f-184">(JavaScript) Klik in de linkernavigatie op de functienaam **ResizeImage** om de functie weer te geven. Vervang alle **index.js** door de inhoud van [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span><span class="sxs-lookup"><span data-stu-id="46e8f-184">(JavaScript) Click on the **ResizeImage** function name in the left navigation to reveal the function, replace all of **index.js** with the content of [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).</span></span>

1. <span data-ttu-id="46e8f-185">Klik onder het codevenster op **Logboeken** om het paneel met logboeken uit te vouwen.</span><span class="sxs-lookup"><span data-stu-id="46e8f-185">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="46e8f-186">Klik op **Opslaan**.</span><span class="sxs-lookup"><span data-stu-id="46e8f-186">Click **Save**.</span></span> <span data-ttu-id="46e8f-187">Ga naar het paneel met logboeken om te controleren of de functie is opgeslagen en er geen fouten zijn opgetreden.</span><span class="sxs-lookup"><span data-stu-id="46e8f-187">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-serverless-function"></a><span data-ttu-id="46e8f-188">De serverloze functie testen</span><span class="sxs-lookup"><span data-stu-id="46e8f-188">Test the serverless function</span></span>

1. <span data-ttu-id="46e8f-189">Open de toepassing in een browser.</span><span class="sxs-lookup"><span data-stu-id="46e8f-189">Open the application in a browser.</span></span> <span data-ttu-id="46e8f-190">Selecteer een afbeeldingsbestand en upload het.</span><span class="sxs-lookup"><span data-stu-id="46e8f-190">Select an image file and upload it.</span></span> <span data-ttu-id="46e8f-191">De upload wordt voltooid, maar de geüploade foto wordt nog niet weergegeven in de app, omdat de mogelijkheid om afbeeldingen weer te geven nog niet is toegevoegd.</span><span class="sxs-lookup"><span data-stu-id="46e8f-191">The upload completes, but because we haven't added the ability to display images yet, the app doesn't show the uploaded photo.</span></span>

1. <span data-ttu-id="46e8f-192">Controleer in Cloud Shell of de afbeelding is geüpload naar de container **images**.</span><span class="sxs-lookup"><span data-stu-id="46e8f-192">In the Cloud Shell, confirm the image was uploaded to the **images** container.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. <span data-ttu-id="46e8f-193">Controleer of de miniatuur is gemaakt in een container met de naam **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="46e8f-193">Confirm the thumbnail was created in a container named **thumbnails**.</span></span>

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. <span data-ttu-id="46e8f-194">Haal de URL van de miniatuur op.</span><span class="sxs-lookup"><span data-stu-id="46e8f-194">Obtain the thumbnail's URL.</span></span>

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    <span data-ttu-id="46e8f-195">Open de URL in een browser en controleer of de miniatuur juist is gemaakt.</span><span class="sxs-lookup"><span data-stu-id="46e8f-195">Open the URL in a browser and confirm the thumbnail was properly created.</span></span>

1. <span data-ttu-id="46e8f-196">Verwijder, voor u verdergaat met de volgende zelfstudie, eerst alle bestanden in de containers **images** en **thumbnails**.</span><span class="sxs-lookup"><span data-stu-id="46e8f-196">Before moving on to the next tutorial, delete all files in the **images** and **thumbnails** containers.</span></span>

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a><span data-ttu-id="46e8f-197">Samenvatting</span><span class="sxs-lookup"><span data-stu-id="46e8f-197">Summary</span></span>

<span data-ttu-id="46e8f-198">In deze module hebt u een serverloze functie gemaakt om een miniatuur te maken telkens wanneer een afbeelding wordt geüpload naar een Blob Storage-container.</span><span class="sxs-lookup"><span data-stu-id="46e8f-198">In this module, you created a serverless function to create a thumbnail whenever an image is uploaded to a Blob storage container.</span></span> <span data-ttu-id="46e8f-199">Hierna leert u hoe u Azure Cosmos DB gebruikt om metagegevens van afbeeldingen op te slaan en weer te geven.</span><span class="sxs-lookup"><span data-stu-id="46e8f-199">Next, you learn how to use Azure Cosmos DB to store and list image metadata.</span></span>
