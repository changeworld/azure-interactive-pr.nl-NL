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
ms.openlocfilehash: 2202cdebe77668972372983a0e802d00edabf6dd
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079240"
---
<span data-ttu-id="cf2f3-103">Azure Cosmos DB is de wereldwijd gedistribueerde serverloze database van Microsoft met meerdere modellen.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-103">Azure Cosmos DB is Microsoft's serverless, globally distributed, multi-model database.</span></span> <span data-ttu-id="cf2f3-104">In deze module leert u hoe u Azure Functions gebruikt om metagegevens van afbeeldingen op te slaan en op te halen als JSON-documenten in Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-104">In this module, you learn how to use Azure Functions to store and retrieve image metadata as JSON documents in Cosmos DB.</span></span>

## <a name="create-a-cosmos-db-account-database-and-collection"></a><span data-ttu-id="cf2f3-105">Een account, database en verzameling maken in Cosmos DB</span><span class="sxs-lookup"><span data-stu-id="cf2f3-105">Create a Cosmos DB account, database, and collection</span></span>

<span data-ttu-id="cf2f3-106">Een Cosmos DB-account is een Azure-resource die Cosmos DB-databases bevat.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-106">A Cosmos DB account is an Azure resource that contains Cosmos DB databases.</span></span>

1. <span data-ttu-id="cf2f3-107">Controleer of u nog steeds bent aangemeld bij Cloud Shell.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-107">Ensure you are still signed into the Cloud Shell.</span></span>  <span data-ttu-id="cf2f3-108">Als dit niet het geval is, selecteert u **Focusmodus openen** om een Cloud Shell-venster te openen.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-108">If not, select **Enter focus mode** to open a Cloud Shell window.</span></span> 

1. <span data-ttu-id="cf2f3-109">Maak een Cosmos DB-account met een unieke naam in dezelfde resourcegroep als de andere resources in deze zelfstudie.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-109">Create a Cosmos DB account with a unique name in the same resource group as the other resources in this tutorial.</span></span>

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. <span data-ttu-id="cf2f3-110">Nadat het Cosmos DB-account is gemaakt, maakt u in het account een nieuwe database met de naam **imagesdb**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-110">After the Cosmos DB account is created, create a new database named **imagesdb** in the account.</span></span>

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. <span data-ttu-id="cf2f3-111">Nadat de database is gemaakt, maakt u in de database een nieuwe verzameling met de naam **images** met een doorvoercapaciteit van 400 aanvraageenheden (RU’s).</span><span class="sxs-lookup"><span data-stu-id="cf2f3-111">After the database is created, create a new collection named **images** in the database with a throughput of 400 request units (RUs).</span></span>

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a><span data-ttu-id="cf2f3-112">Een document opslaan in Cosmos DB wanneer een miniatuur wordt gemaakt</span><span class="sxs-lookup"><span data-stu-id="cf2f3-112">Save a document to Cosmos DB when a thumbnail is created</span></span>

<span data-ttu-id="cf2f3-113">Met de Cosmos DB-uitvoerbinding kunt u vanuit Azure Functions documenten maken in een Cosmos DB-verzameling.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-113">The Cosmos DB output binding lets you create documents in a Cosmos DB collection from Azure Functions.</span></span> <span data-ttu-id="cf2f3-114">In de volgende stappen configureert u een Cosmos DB-uitvoerbinding in de functie **ResizeImage** en wijzigt u de functie om een document (object) te retourneren dat moet worden opgeslagen.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-114">In the following steps, you configure a Cosmos DB output binding in the **ResizeImage** function and modify the function to return a document (object) to be saved.</span></span>

1. <span data-ttu-id="cf2f3-115">Open de functie-app in de Azure-portal.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-115">Open the function app in the Azure Portal.</span></span>

1. <span data-ttu-id="cf2f3-116">Vouw in de linkernavigatie de functie **ResizeImage** uit en selecteer vervolgens **Integreren**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-116">In the left hand navigation, expand the **ResizeImage** function, and then select **Integrate**.</span></span>

1. <span data-ttu-id="cf2f3-117">Klik onder **Nieuwe uitvoer** op **Uitvoer**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-117">Under **Outputs**, click **New Output**.</span></span>

1. <span data-ttu-id="cf2f3-118">Ga naar het item **Azure Cosmos DB** en selecteer het.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-118">Find the **Azure Cosmos DB** item and select it.</span></span> <span data-ttu-id="cf2f3-119">Klik vervolgens op **Selecteren**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-119">Then click **Select**.</span></span>

    ![Nieuwe uitvoer selecteren](media/functions-first-serverless-web-app/4-new-output.jpg)

1. <span data-ttu-id="cf2f3-121">Vul de velden onder **Azure Cosmos DB-uitvoer** in met de volgende waarden.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-121">Fill out the fields under **Azure Cosmos DB output** with the following values.</span></span>

    | <span data-ttu-id="cf2f3-122">Instelling</span><span class="sxs-lookup"><span data-stu-id="cf2f3-122">Setting</span></span>      |  <span data-ttu-id="cf2f3-123">Voorgestelde waarde</span><span class="sxs-lookup"><span data-stu-id="cf2f3-123">Suggested value</span></span>   | <span data-ttu-id="cf2f3-124">Beschrijving</span><span class="sxs-lookup"><span data-stu-id="cf2f3-124">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="cf2f3-125">**Parameternaam van document**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-125">**Document parameter name**</span></span> | <span data-ttu-id="cf2f3-126">Selecteer **Retourwaarde van functie gebruiken**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-126">Select **Use function return value**</span></span> | <span data-ttu-id="cf2f3-127">De waarde van het tekstvak wordt automatisch ingesteld op **$return**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-127">The value of the textbox is automatically set to **$return**.</span></span> |
    | <span data-ttu-id="cf2f3-128">**Databasenaam**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-128">**Database name**</span></span> | <span data-ttu-id="cf2f3-129">imagesdb</span><span class="sxs-lookup"><span data-stu-id="cf2f3-129">imagesdb</span></span> | <span data-ttu-id="cf2f3-130">Gebruik de naam van de database die u hebt gemaakt.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-130">Use the name of the database that you created.</span></span> |
    | <span data-ttu-id="cf2f3-131">**Naam van verzameling**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-131">**Collection name**</span></span> | <span data-ttu-id="cf2f3-132">images</span><span class="sxs-lookup"><span data-stu-id="cf2f3-132">images</span></span> | <span data-ttu-id="cf2f3-133">Gebruik de naam van de verzameling die u hebt gemaakt.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-133">Use the name of the collection that you created.</span></span> |

1. <span data-ttu-id="cf2f3-134">Klik naast **Verbinding met het Azure Cosmos DB-account** op **Nieuw**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-134">Next to **Azure Cosmos DB account connection**, click **new**.</span></span> <span data-ttu-id="cf2f3-135">Selecteer het Cosmos DB-account dat u eerder hebt gemaakt.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-135">Select the Cosmos DB account you previously created.</span></span>

    ![Voer instellingen in voor de Azure Cosmos DB-uitvoerbinding](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. <span data-ttu-id="cf2f3-137">Klik op **Opslaan** om de Cosmos DB-uitvoerbinding te maken.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-137">Click **Save** to create the Cosmos DB output binding.</span></span>

1. <span data-ttu-id="cf2f3-138">Klik aan de linkerkant op de functienaam **ResizeImage** om de functie te openen.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-138">Click on the **ResizeImage** function name on the left to open the function.</span></span>

1. <span data-ttu-id="cf2f3-139">**C#**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-139">**C#**</span></span>

    1. <span data-ttu-id="cf2f3-140">(C#) Wijzig het retourtype van de functie van **void** in **object**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-140">(C#) Change the return type of the function from **void** to **object**.</span></span>

    1. <span data-ttu-id="cf2f3-141">(C#) Voeg aan het einde van de functie het volgende codeblok toe om het document te retourneren dat moet worden opgeslagen:</span><span class="sxs-lookup"><span data-stu-id="cf2f3-141">(C#) At the end of the function, add the following code block to return the document to be saved:</span></span>
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![run.csx voor de functie ResizeImages na de wijzigingen](media/functions-first-serverless-web-app/4-update-function.png)

1. <span data-ttu-id="cf2f3-143">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-143">**JavaScript**</span></span>

    1. <span data-ttu-id="cf2f3-144">(JavaScript) Wijzig de instructie `context.done()` in het component `else` om het document te retourneren dat moet worden opgeslagen in Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-144">(JavaScript) Change the `context.done()` statement in the `else` clause to return the document to be saved to Cosmos DB.</span></span>

    ```javascript
    if (error) {
        context.done(error);
    } else {
        context.bindings.thumbnail = stream;
        context.done(null, {
            id: context.bindingData.name,
            imgPath: "/images/" + context.bindingData.name,
            thumbnailPath: "/thumbnails/" + context.bindingData.name
        });
    }
    ```

1. <span data-ttu-id="cf2f3-145">Klik onder het codevenster op **Logboeken** om het paneel met logboeken uit te vouwen.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-145">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="cf2f3-146">Klik op **Opslaan**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-146">Click **Save**.</span></span> <span data-ttu-id="cf2f3-147">Ga naar het paneel met logboeken om te controleren of de functie is opgeslagen en er geen fouten zijn opgetreden.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-147">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="create-a-function-to-list-images-from-cosmos-db"></a><span data-ttu-id="cf2f3-148">Een functie maken om afbeeldingen uit Cosmos DB weer te geven</span><span class="sxs-lookup"><span data-stu-id="cf2f3-148">Create a function to list images from Cosmos DB</span></span>

<span data-ttu-id="cf2f3-149">Voor de webtoepassing is een API vereist voor het ophalen van de metagegevens van afbeeldingen uit Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-149">The web application requires an API to retrieve image metadata from Cosmos DB.</span></span> <span data-ttu-id="cf2f3-150">In de volgende stappen:</span><span class="sxs-lookup"><span data-stu-id="cf2f3-150">In the following steps.</span></span> <span data-ttu-id="cf2f3-151">Maakt u een functie die wordt geactiveerd via HTTP, die gebruikmaakt van Cosmos DB-invoerbinding om de databaseverzameling te doorzoeken.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-151">uou create an HTTP triggered function that uses a Cosmos DB input binding to query the database collection.</span></span>

1. <span data-ttu-id="cf2f3-152">Wijs in de functie-app aan de linkerkant naar **Functies** en klik op **+** om een nieuwe functie te maken.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-152">In your function app, hover over **Functions** on the left and click **+** to create a new function.</span></span>

1. <span data-ttu-id="cf2f3-153">Ga naar de sjabloon **HttpTrigger** en selecteer deze.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-153">Find the **HttpTrigger** template and select it.</span></span>

1. <span data-ttu-id="cf2f3-154">Gebruik deze waarden om een functie te maken waarmee een URL voor de afbeeldingen wordt gegenereerd.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-154">Use these values to create a function that generates a get images URL.</span></span>

    | <span data-ttu-id="cf2f3-155">Instelling</span><span class="sxs-lookup"><span data-stu-id="cf2f3-155">Setting</span></span>      |  <span data-ttu-id="cf2f3-156">Voorgestelde waarde</span><span class="sxs-lookup"><span data-stu-id="cf2f3-156">Suggested value</span></span>   | <span data-ttu-id="cf2f3-157">Beschrijving</span><span class="sxs-lookup"><span data-stu-id="cf2f3-157">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="cf2f3-158">**Een naam voor de functie opgeven**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-158">**Name your function**</span></span> | <span data-ttu-id="cf2f3-159">GetImages</span><span class="sxs-lookup"><span data-stu-id="cf2f3-159">GetImages</span></span> | <span data-ttu-id="cf2f3-160">Typ de naam precies zoals deze wordt weergegeven, zodat de functie kan worden gedetecteerd met de toepassing.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-160">Type this name exactly as shown so the application can discover the function.</span></span> |
    | <span data-ttu-id="cf2f3-161">**Verificatieniveau**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-161">**Authorization level**</span></span> | <span data-ttu-id="cf2f3-162">Anoniem</span><span class="sxs-lookup"><span data-stu-id="cf2f3-162">Anonymous</span></span> | <span data-ttu-id="cf2f3-163">Sta toe dat de functie openbaar toegankelijk is.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-163">Allow the function to be accessed publicly.</span></span> |

1. <span data-ttu-id="cf2f3-164">Klik op **Create**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-164">Click **Create**.</span></span>

1. <span data-ttu-id="cf2f3-165">Wanneer de nieuwe functie is gemaakt, klik u in de linkernavigatie onder de naam van de functie op **Integreren**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-165">When the new function is created, click **Integrate** under the function's name on the left navigation.</span></span>

1. <span data-ttu-id="cf2f3-166">Klik op **Nieuwe invoer** en selecteer **Azure Cosmos DB**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-166">Click **New Input** and select **Azure Cosmos DB**.</span></span> 

    ![Nieuwe invoer selecteren](media/functions-first-serverless-web-app/4-new-input.jpg)

1. <span data-ttu-id="cf2f3-168">Klik op **Selecteren**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-168">Click **Select**.</span></span>

1. <span data-ttu-id="cf2f3-169">Vul de volgende waarden in:</span><span class="sxs-lookup"><span data-stu-id="cf2f3-169">Fill out the following values:</span></span>

    | <span data-ttu-id="cf2f3-170">Instelling</span><span class="sxs-lookup"><span data-stu-id="cf2f3-170">Setting</span></span>      |  <span data-ttu-id="cf2f3-171">Voorgestelde waarde</span><span class="sxs-lookup"><span data-stu-id="cf2f3-171">Suggested value</span></span>   | <span data-ttu-id="cf2f3-172">Beschrijving</span><span class="sxs-lookup"><span data-stu-id="cf2f3-172">Description</span></span>                                        |
    | --- | --- | ---|
    | <span data-ttu-id="cf2f3-173">**Parameternaam van document**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-173">**Document parameter name**</span></span> | <span data-ttu-id="cf2f3-174">documents</span><span class="sxs-lookup"><span data-stu-id="cf2f3-174">documents</span></span> | <span data-ttu-id="cf2f3-175">Komt overeen met de parameternaam in de functie.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-175">Matches parameter name in the function.</span></span> |
    | <span data-ttu-id="cf2f3-176">**Databasenaam**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-176">**Database name**</span></span> | <span data-ttu-id="cf2f3-177">imagesdb</span><span class="sxs-lookup"><span data-stu-id="cf2f3-177">imagesdb</span></span> |  |
    | <span data-ttu-id="cf2f3-178">**Naam van verzameling**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-178">**Collection name**</span></span> | <span data-ttu-id="cf2f3-179">images</span><span class="sxs-lookup"><span data-stu-id="cf2f3-179">images</span></span> |  |
    | <span data-ttu-id="cf2f3-180">**SQL-query**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-180">**SQL query**</span></span> | <span data-ttu-id="cf2f3-181">select \* from c order by c._ts desc</span><span class="sxs-lookup"><span data-stu-id="cf2f3-181">select \* from c order by c._ts desc</span></span> | <span data-ttu-id="cf2f3-182">Documenten ophalen, laatste documenten eerst.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-182">Get documents, latest documents first.</span></span> |
    | <span data-ttu-id="cf2f3-183">**Verbinding met het Azure Cosmos DB-account**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-183">**Azure Cosmos DB account connection**</span></span> | <span data-ttu-id="cf2f3-184">Bestaande verbindingsreeks selecteren</span><span class="sxs-lookup"><span data-stu-id="cf2f3-184">Select existing connection string</span></span> |  |

1. <span data-ttu-id="cf2f3-185">Klik op **Opslaan** om de invoerbinding te maken.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-185">Click **Save** to create the input binding.</span></span>

1. <span data-ttu-id="cf2f3-186">**C#**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-186">**C#**</span></span>

    1. <span data-ttu-id="cf2f3-187">Klik op de naam van de functie om het codevenster te openen. Vervang vervolgens alle **run.csx** door de inhoud in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span><span class="sxs-lookup"><span data-stu-id="cf2f3-187">Click the function's name to open the code window, and then replace all of **run.csx** with the content in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).</span></span>

1. <span data-ttu-id="cf2f3-188">**JavaScript**</span><span class="sxs-lookup"><span data-stu-id="cf2f3-188">**JavaScript**</span></span>

    1. <span data-ttu-id="cf2f3-189">Klik op de naam van de functie om het codevenster te openen. Vervang vervolgens alle **index.js** door de inhoud in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span><span class="sxs-lookup"><span data-stu-id="cf2f3-189">Click the function's name to open the code window, and then replace all of **index.js** with the content in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).</span></span>

1. <span data-ttu-id="cf2f3-190">Klik onder het codevenster op **Logboeken** om het paneel met logboeken uit te vouwen.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-190">Click **Logs** below the code window to expand the logs panel.</span></span>

1. <span data-ttu-id="cf2f3-191">Klik op **Opslaan**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-191">Click **Save**.</span></span> <span data-ttu-id="cf2f3-192">Ga naar het paneel met logboeken om te controleren of de functie is opgeslagen en er geen fouten zijn opgetreden.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-192">Check the logs panel to ensure the function is successfully saved and there are no errors.</span></span>


## <a name="test-the-application"></a><span data-ttu-id="cf2f3-193">De toepassing testen</span><span class="sxs-lookup"><span data-stu-id="cf2f3-193">Test the application</span></span>

1. <span data-ttu-id="cf2f3-194">Open de toepassing in een browser.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-194">Open the application in a browser.</span></span> <span data-ttu-id="cf2f3-195">Selecteer een afbeeldingsbestand en upload het.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-195">Select an image file and upload it.</span></span>

1. <span data-ttu-id="cf2f3-196">Na enkele seconden wordt de miniatuur van de nieuwe afbeelding weergegeven op de pagina.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-196">After a few seconds, the thumbnail of the new image appears on the page.</span></span>

1. <span data-ttu-id="cf2f3-197">Gebruik het zoekvak in de Azure-portal om op naam te zoeken naar uw Cosmos DB-account.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-197">In the Azure portal, use the Search box to search for your Cosmos DB account by name.</span></span> <span data-ttu-id="cf2f3-198">Klik op het account om het te openen.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-198">Click it to open it.</span></span>

1. <span data-ttu-id="cf2f3-199">Klik aan de linkerkant op **Data Explorer** om door verzamelingen en documenten te bladeren.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-199">Click **Data Explorer** on the left to browse collections and documents.</span></span>

1. <span data-ttu-id="cf2f3-200">Selecteer onder de database **imagesdb** de verzameling **images**.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-200">Under the **imagesdb** database, select the **images** collection.</span></span>

1. <span data-ttu-id="cf2f3-201">Controleer of er een document is gemaakt voor de geüploade afbeelding.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-201">Confirm that a document was created for the uploaded image.</span></span>

    ![Data Explorer waarin een document wordt weergegeven voor een geüploade afbeelding](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a><span data-ttu-id="cf2f3-203">Samenvatting</span><span class="sxs-lookup"><span data-stu-id="cf2f3-203">Summary</span></span>

<span data-ttu-id="cf2f3-204">In deze module hebt u geleerd hoe u een account, database en verzameling maakt in Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-204">In this module, you learned how to create a Cosmos DB account, database, and collection.</span></span> <span data-ttu-id="cf2f3-205">U hebt ook geleerd hoe u Cosmos DB-bindingen gebruikt om metagegevens van afbeeldingen op te slaan en op te halen in de Cosmos DB-verzameling.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-205">You also learned how to use the Cosmos DB bindings to save and retrieve image metadata in the Cosmos DB collection.</span></span> <span data-ttu-id="cf2f3-206">Hierna leert u hoe u met behulp van Microsoft Cognitive Services automatisch een bijschrift kunt genereren voor elke geüploade afbeelding.</span><span class="sxs-lookup"><span data-stu-id="cf2f3-206">Next, you learn how to automatically generate a caption for each uploaded image using Microsoft Cognitive Services.</span></span>