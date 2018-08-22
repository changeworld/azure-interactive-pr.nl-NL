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
De toepassing die u bouwt, is een fotogalerie. De galerie maakt aan de clientzijde gebruik van JavaScript voor het aanroepen van API’s om afbeeldingen te uploaden en weer te geven. In deze module maakt u een API met behulp van een serverloze functie waarmee een tijdelijke URL wordt gegenereerd om een afbeelding te uploaden. De webtoepassing maakt gebruik van de gegenereerde URL om een afbeelding naar Blob Storage te uploaden met behulp van de [Blob Storage REST API](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api).

## <a name="create-a-blob-storage-container-for-images"></a>Een Blob Storage-container maken voor afbeeldingen

Voor de toepassing is een afzonderlijke opslagcontainer vereist voor het uploaden en hosten van afbeeldingen.

1. Controleer of u nog steeds bent aangemeld bij Cloud Shell (bash). Als dit niet het geval is, selecteert u **Focusmodus openen** om een Cloud Shell-venster te openen.

1.  Maak een nieuwe container met de naam **images** in het opslagaccount met openbare toegang tot alle blobs.

    ```azurecli
    az storage container create -n images --account-name <storage account name> --public-access blob
    ```

## <a name="create-an-azure-function-app"></a>Een Azure-functie-app maken

Azure Functions is een service voor het uitvoeren van serverloze functies. Een serverloze functie kan worden geactiveerd (aangeroepen) voor gebeurtenissen zoals een HTTP-aanvraag of wanneer er een blob is gemaakt in de opslagcontainer.

Een Azure-functie-app is een container voor een of meer serverloze functies.

1. Maak een nieuwe Azure-functie-app met een unieke naam in de resourcegroep **first-serverless-app** die u eerder hebt gemaakt. Voor functie-apps is een opslagaccount vereist. In deze zelfstudie gebruikt u het bestaande opslagaccount.

    ```azurecli
    az functionapp create -n <function app name> -g first-serverless-app -s <storage account name> -c westcentralus
    ```


## <a name="create-an-http-triggered-serverless-function"></a>Een serverloze functie maken die wordt geactiveerd via HTTP

Met de fotogaleriewebtoepassing wordt een HTTP-aanvraag verzonden naar een serverloze functie om een tijdelijke URL te genereren voor het veilig uploaden van een afbeelding naar Blob Storage. De functie wordt geactiveerd via een HTTP-aanvraag en maakt gebruik van de Azure Storage-SDK voor het genereren en retourneren van de beveiligde URL.

1. Nadat de functie-app is gemaakt, zoekt u in de Azure-portal met behulp van het zoekvak naar de functie-app en klikt u erop om deze te openen.

    ![De functie-app openen](media/functions-first-serverless-web-app/2-search-function-app.png)

1. Wijs in de linkernavigatie van het venster van de functie-app naar **Functies** en klik op **+** om te beginnen met het maken van een nieuwe serverloze functie.

    ![Een nieuwe functie maken](media/functions-first-serverless-web-app/2-new-function.png)

1. Klik op **Aangepaste functie** voor een lijst met functiesjablonen.

1. Ga naar de sjabloon **HttpTrigger** en klik op de taal die moet worden gebruikt (C# of JavaScript).

1. Gebruik deze waarden om een functie te maken waarmee een upload-URL voor een blob wordt gegenereerd.

    | Instelling      |  Voorgestelde waarde   | Beschrijving                                        |
    | --- | --- | ---|
    | **Taal** | C# of JavaScript | Selecteer de gewenste taal. |
    | **Een naam voor de functie opgeven** | GetUploadUrl | Typ de naam precies zoals deze wordt weergegeven, zodat de functie kan worden gedetecteerd met de toepassing. |
    | **Verificatieniveau** | Anoniem | Sta toe dat de functie openbaar toegankelijk is. |

    ![Instellingen invoeren voor een nieuwe functie die wordt geactiveerd via HTTP](media/functions-first-serverless-web-app/2-new-function-httptrigger.png)

1. Klik op **Maken** om de functie te maken.

1. **C#** 

    1. Wanneer de broncode van de functie wordt weergegeven, vervangt u alle **run.csx** door de inhoud van [**csharp/GetUploadUrl/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetUploadUrl/run.csx).

1. **JavaScript** 

    1. (JavaScript) Voor deze functie is vereist dat met het `azure-storage`-pakket van npm het SAS-token (Shared Access Signature) wordt gegenereerd dat is vereist om de beveiligde URL samen te stellen. Klik in de linkernavigatie op de naam van de functie-app, en klik op **Platformfuncties** om het npm-pakket te installeren.

    1. (JavaScript) Klik op **Console** om een consolevenster weer te geven.

        ![Een consolevenster openen](media/functions-first-serverless-web-app/2-open-console.jpg)

    1. (JavaScript) Zorg ervoor dat de map is **d:\home\site\wwwroot** door de opdracht `cd d:\home\site\wwwroot` uit te voeren.

    1. (JavaScript) Voer de opdracht `npm init -y` uit om een leeg **package.json**-bestand te maken.

    1. (JavaScript) Voer de opdracht `npm install --save azure-storage` uit in de console om het pakket te installeren en het op te slaan in **package.json**. Het kan een paar minuten duren om de bewerking te voltooien.

    1. (JavaScript) Klik in de linkernavigatie op de functienaam (**GetUploadUrl**) om de functie weer te geven, vervang alle **index.js** door de inhoud van [**javascript/GetUploadUrl/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetUploadUrl/index.js).
    
        ![index.js na de update](media/functions-first-serverless-web-app/2-paste-js.jpg)

1. Klik onder het codevenster op **Logboeken** om het paneel met logboeken uit te vouwen.

1. Klik op **Opslaan**. Ga naar het paneel met logboeken om te controleren of de functie is gecompileerd.

Met de functie wordt een zogenaamde SAS-URL (Shared Access Signature) gegenereerd die wordt gebruikt om een bestand te uploaden naar Blob Storage. De SAS-URL is gedurende een korte periode geldig en is alleen geschikt om een enkel bestand te uploaden. Raadpleeg de Blob Storage-documentatie voor meer informatie over [handtekeningen voor gedeelde toegang](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).


## <a name="add-an-environment-variable-for-the-storage-connection-string"></a>Een omgevingsvariabele toevoegen voor de opslagverbindingsreeks

Voor de functie die u zojuist hebt gemaakt, is een verbindingsreeks vereist voor het opslagaccount, zodat de SAS-URL kan worden gegenereerd. In plaats van de verbindingsreeks in de hoofdtekst van de functie in code vast te leggen, kan deze worden opgeslagen als een toepassingsinstelling. Toepassingsinstellingen zijn toegankelijk als omgevingsvariabelen voor alle functies in de functie-app.

1. Voer in Cloud Shell een query uit voor de verbindingsreeks van het opslagaccount en sla deze op in een bash-variabele met de naam **STORAGE_CONNECTION_STRING**.

    ```azurecli
    export STORAGE_CONNECTION_STRING=$(az storage account show-connection-string -n <storage account name> -g first-serverless-app --query "connectionString" --output tsv)
    ```

    Bevestig dat de variabele juist is ingesteld.

    ```azurecli
    echo $STORAGE_CONNECTION_STRING
    ```

1. Maak een nieuwe toepassingsinstelling met de naam **AZURE_STORAGE_CONNECTION_STRING** met behulp van de waarde die is opgeslagen in de vorige stap.

    ```azurecli
    az functionapp config appsettings set -n <function app name> -g first-serverless-app --settings AZURE_STORAGE_CONNECTION_STRING=$STORAGE_CONNECTION_STRING -o table
    ```

    Controleer of de uitvoer van de opdracht de nieuwe toepassingsinstelling bevat met de juiste waarde.


## <a name="test-the-serverless-function"></a>De serverloze functie testen

Naast het maken en bewerken van functies biedt de Azure-portal ook een ingebouwd hulpprogramma voor het testen van functies.

1. Als u de serverloze HTTP-functie wilt testen, klikt u aan de rechterkant van het codevenster op het tabblad **Testen** om het testpaneel uit te vouwen.

1. Wijzig de **HTTP-methode** in **GET**.

1. Klik onder **Query** op *Parameter toevoegen** en voeg de volgende parameter toe:

    | naam      |  waarde   | 
    | --- | --- |
    | bestandsnaam | image1.jpg |

1. Klik in het testpaneel op **Uitvoeren** om een HTTP-aanvraag naar de functie te verzenden.

1. De functie retourneert een upload-URL in de uitvoer. De uitvoering van de functie wordt weergegeven in het paneel met logboeken.

    ![Logboeken waarin wordt weergegeven dat de functie juist is uitgevoerd](media/functions-first-serverless-web-app/2-test-function.png)


## <a name="configure-cors-in-the-function-app"></a>CORS configureren in de functie-app

Omdat de front-end van de app wordt gehost in Blob Storage, heeft deze een andere domeinnaam dan de Azure-functie-app. De functie-app moet zijn geconfigureerd voor CORS (Cross-Origin Resource Sharing) om ervoor te zorgen dat de functie die u zojuist hebt gemaakt, kan worden aangeroepen met JavaScript aan de clientzijde.

1. Klik in de linkernavigatiebalk van het venster voor de functie-app op de naam van de functie-app.

1. Klik op **Platformfuncties** om een lijst met geavanceerde functies weer te geven.

1. Klik onder **API** op **CORS**.

    ![CORS selecteren](media/functions-first-serverless-web-app/2-open-cors.jpg)

1. Voeg een toegestane oorsprong toe voor de toepassings-URL uit de vorige module, waarbij u de afsluitende **/** weglaat. (Bijvoorbeeld: `https://firstserverlessweb.z4.web.core.windows.net`)

    ![CORS-instellingen waarin wordt weergegeven dat een URL voor een serverloze web-app is toegevoegd](media/functions-first-serverless-web-app/2-add-cors.png)

1. Klik op **Opslaan**.

1. C#

   1. (C#) Ga terug naar de `GetUploadUrl`-functie en selecteer vervolgens het tabblad **Integreren**.

   1. (C#) Selecteer onder Geselecteerde HTTP-methoden de methode **OPTIONS**.

      **GET**, **POST** en **OPTIONS** moeten allemaal zijn geselecteerd. CORS maakt gebruik van de OPTIONS-methode. Deze methode is niet standaard geselecteerd voor C#-functies.  

   1. (C#) Klik op **Opslaan**.

1. Blijf in de portal, ga naar de functie-app en selecteer het tabblad **Overzicht**. Klik vervolgens op **Opnieuw starten** om ervoor te zorgen dat de wijzigingen voor CORS worden doorgevoerd.

## <a name="configure-cors-in-the-storage-account"></a>CORS configureren in het opslagaccount

Omdat via de app ook JavaScript-aanroepen aan de clientzijde naar Blob Storage worden verzonden om bestanden te uploaden, moet u ook het opslagaccount voor CORS configureren.

1. Voer de volgende opdracht uit om alle oorsprongen toe te staan bestanden te uploaden naar het opslagaccount.

    ```azurecli
    az storage cors add --methods OPTIONS PUT --origins '*' --exposed-headers '*' --allowed-headers '*' --services b --account-name <storage account name>
    ```


## <a name="modify-the-web-app-to-upload-images"></a>De web-app wijzigen om afbeeldingen te uploaden

Met de web-app worden instellingen opgehaald uit het bestand met de naam **settings.js**. In de volgende stappen maakt u het bestand met behulp van Cloud Shell. Vervolgens stelt u `window.apiBaseUrl` in op de URL voor de functie-app en `window.blobBaseUrl` op de URL voor het Azure Blob Storage-eindpunt.

1. Zorg ervoor dat de huidige map in Cloud Shell deze map is: **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Voer een query uit voor de URL voor de functie-app, en sla deze op in een bash-variabele met de naam **FUNCTION_APP_URL**.

    ```azurecli
    export FUNCTION_APP_URL="https://"$(az functionapp show -n <function app name> -g first-serverless-app --query "defaultHostName" --output tsv)
    ```

    Bevestig dat de variabele juist is ingesteld.

    ```azurecli
    echo $FUNCTION_APP_URL
    ```

1. Als u de basis-URI voor API-aanroepen naar de functie-app wilt instellen, maakt u **settings.js** en voegt u de URL voor de functie-app als volgt toe.

    `window.apiBaseUrl = 'https://fnapp@lab.GlobalLabInstanceId.azurewebsites.net'`

    U kunt de wijziging aanbrengen door de volgende opdracht uit te voeren of door een opdrachtregeleditor te gebruiken, zoals VIM.

    ```azurecli
    echo "window.apiBaseUrl = '$FUNCTION_APP_URL'" > settings.js
    ```

    Controleer of het bestand juist is geschreven.

    ```azurecli
    cat settings.js
    ```

1. Voer een query uit voor de basis-URL voor Blob Storage, en sla deze op in een bash- variabele met de naam **BLOB_BASE_URL**.

    ```azurecli
    export BLOB_BASE_URL=$(az storage account show -n <storage account name> -g first-serverless-app --query primaryEndpoints.blob -o tsv | sed 's/\/$//')
    ```

    Bevestig dat de variabele juist is ingesteld.

    ```azurecli
    echo $BLOB_BASE_URL
    ```

1. Als u de basis-URI voor API-aanroepen naar de functie-app wilt instellen, voegt u de opslag-URL zoals de volgende coderegel toe aan **settings.js**.

    `window.blobBaseUrl = 'https://mystorage.blob.core.windows.net'`

    U kunt de wijziging aanbrengen door de volgende opdracht uit te voeren of door een opdrachtregeleditor te gebruiken, zoals VIM.

    ```azurecli
    echo "window.blobBaseUrl = '$BLOB_BASE_URL'" >> settings.js
    ```

    Controleer of het bestand juist is geschreven en nu 2 regels bevat.

    ```azurecli
    cat settings.js
    ```

1. Upload het bestand naar Blob Storage.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-web-application"></a>De webtoepassing testen

In dit stadium kunnen met de galerietoepassing afbeeldingen worden geüpload naar Blob Storage, maar kunnen afbeeldingen nog niet worden weergeven. Er wordt een poging gedaan om een `GetImages`-functie aan te roepen die nog niet bestaat, omdat u deze in een latere module pas maakt. Deze aanroep mislukt en de webpagina lijkt te zijn vastgelopen bij ‘Analyseren...’. De afbeelding die u selecteert, wordt echter wel degelijk geüpload.

U kunt controleren of een afbeelding juist is geüpload door de inhoud van de container **images** in de Azure-portal te bekijken.

1. Blader in een browservenster naar de toepassing. Selecteer een afbeeldingsbestand en upload het. De upload wordt voltooid, maar de geüploade foto wordt nog niet weergegeven in de app, omdat de mogelijkheid om afbeeldingen weer te geven nog niet is toegevoegd. (De webpagina lijkt te zijn vastgelopen bij ‘Afbeelding analyseren...’. Dit lost u later op.)

1. Controleer in Cloud Shell of de afbeelding is geüpload naar de container **images**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Verwijder, voor u verdergaat met de volgende zelfstudie, eerst alle bestanden in de container **images**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```


## <a name="summary"></a>Samenvatting

In deze module hebt u een Azure-functie-app gemaakt en geleerd hoe u een serverloze functie kunt gebruiken om een webtoepassing toestemming te geven afbeeldingen te uploaden naar Blob Storage. Hierna leert u hoe u miniaturen maakt voor de geüploade afbeeldingen met behulp van de serverloze functie die wordt geactiveerd via een blob.
