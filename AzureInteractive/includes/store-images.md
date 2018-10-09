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
ms.openlocfilehash: 194a25dbf9abda80379aa5aab408ac4ffe9ab7f5
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460055"
---
Azure Cosmos DB is de wereldwijd gedistribueerde serverloze database van Microsoft met meerdere modellen. In deze module leert u hoe u Azure Functions gebruikt om metagegevens van afbeeldingen op te slaan en op te halen als JSON-documenten in Cosmos DB.

## <a name="create-a-cosmos-db-account-database-and-collection"></a>Een account, database en verzameling maken in Cosmos DB

Een Cosmos DB-account is een Azure-resource die Cosmos DB-databases bevat.

1. Controleer of u nog steeds bent aangemeld bij Cloud Shell.  Als dit niet het geval is, selecteert u **Focusmodus openen** om een Cloud Shell-venster te openen. 

1. Maak een Cosmos DB-account met een unieke naam in dezelfde resourcegroep als de andere resources in deze zelfstudie.

    ```azurecli
    az cosmosdb create -g first-serverless-app -n <cosmos db account name>
    ```

1. Nadat het Cosmos DB-account is gemaakt, maakt u in het account een nieuwe database met de naam **imagesdb**.

    ```azurecli
    az cosmosdb database create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb
    ```

1. Nadat de database is gemaakt, maakt u in de database een nieuwe verzameling met de naam **images** met een doorvoercapaciteit van 400 aanvraageenheden (RU’s).

    ```azurecli
    az cosmosdb collection create -g first-serverless-app -n <cosmos db account name> --db-name imagesdb --collection-name images --throughput 400
    ```


## <a name="save-a-document-to-cosmos-db-when-a-thumbnail-is-created"></a>Een document opslaan in Cosmos DB wanneer een miniatuur wordt gemaakt

Met de Cosmos DB-uitvoerbinding kunt u vanuit Azure Functions documenten maken in een Cosmos DB-verzameling. In de volgende stappen configureert u een Cosmos DB-uitvoerbinding in de functie **ResizeImage** en wijzigt u de functie om een document (object) te retourneren dat moet worden opgeslagen.

1. Open de functie-app in de Azure-portal.

1. Vouw in de linkernavigatie de functie **ResizeImage** uit en selecteer vervolgens **Integreren**.

1. Klik onder **Nieuwe uitvoer** op **Uitvoer**.

1. Ga naar het item **Azure Cosmos DB** en selecteer het. Klik vervolgens op **Selecteren**.

    ![Nieuwe uitvoer selecteren](media/functions-first-serverless-web-app/4-new-output.jpg)

1. Vul de velden onder **Azure Cosmos DB-uitvoer** in met de volgende waarden.

    | Instelling      |  Voorgestelde waarde   | Beschrijving                                        |
    | --- | --- | ---|
    | **Parameternaam van document** | Selecteer **Retourwaarde van functie gebruiken** | De waarde van het tekstvak wordt automatisch ingesteld op **$return**. |
    | **Databasenaam** | imagesdb | Gebruik de naam van de database die u hebt gemaakt. |
    | **Naam van verzameling** | images | Gebruik de naam van de verzameling die u hebt gemaakt. |

1. Klik naast **Verbinding met het Azure Cosmos DB-account** op **Nieuw**. Selecteer het Cosmos DB-account dat u eerder hebt gemaakt.

    ![Voer instellingen in voor de Azure Cosmos DB-uitvoerbinding](media/functions-first-serverless-web-app/4-cosmos-db-output.png)

1. Klik op **Opslaan** om de Cosmos DB-uitvoerbinding te maken.

1. Klik aan de linkerkant op de functienaam **ResizeImage** om de functie te openen.

1. **C#**

    1. (C#) Wijzig het retourtype van de functie van **void** in **object**.

    1. (C#) Voeg aan het einde van de functie het volgende codeblok toe om het document te retourneren dat moet worden opgeslagen:
    
        ```csharp
        return new {
            id = name,
            imgPath = "/images/" + name,
            thumbnailPath = "/thumbnails/" + name
        };
        ```
    
        ![run.csx voor de functie ResizeImages na de wijzigingen](media/functions-first-serverless-web-app/4-update-function.png)

1. **JavaScript**

    1. (JavaScript) Wijzig de instructie `context.done()` in het component `else` om het document te retourneren dat moet worden opgeslagen in Cosmos DB.

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

1. Klik onder het codevenster op **Logboeken** om het paneel met logboeken uit te vouwen.

1. Klik op **Opslaan**. Ga naar het paneel met logboeken om te controleren of de functie is opgeslagen en er geen fouten zijn opgetreden.


## <a name="create-a-function-to-list-images-from-cosmos-db"></a>Een functie maken om afbeeldingen uit Cosmos DB weer te geven

Voor de webtoepassing is een API vereist voor het ophalen van de metagegevens van afbeeldingen uit Cosmos DB. In de volgende stappen: maakt u een functie die wordt geactiveerd via HTTP, die gebruikmaakt van Cosmos DB-invoerbinding om de databaseverzameling te doorzoeken.

1. Wijs in de functie-app aan de linkerkant naar **Functies** en klik op **+** om een nieuwe functie te maken.

1. Ga naar de sjabloon **HttpTrigger** en selecteer deze.

1. Gebruik deze waarden om een functie te maken waarmee een URL voor de afbeeldingen wordt gegenereerd.

    | Instelling      |  Voorgestelde waarde   | Beschrijving                                        |
    | --- | --- | ---|
    | **Een naam voor de functie opgeven** | GetImages | Typ de naam precies zoals deze wordt weergegeven, zodat de functie kan worden gedetecteerd met de toepassing. |
    | **Verificatieniveau** | Anoniem | Sta toe dat de functie openbaar toegankelijk is. |

1. Klik op **Create**.

1. Wanneer de nieuwe functie is gemaakt, klik u in de linkernavigatie onder de naam van de functie op **Integreren**.

1. Klik op **Nieuwe invoer** en selecteer **Azure Cosmos DB**. 

    ![Nieuwe invoer selecteren](media/functions-first-serverless-web-app/4-new-input.jpg)

1. Klik op **Selecteren**.

1. Vul de volgende waarden in:

    | Instelling      |  Voorgestelde waarde   | Beschrijving                                        |
    | --- | --- | ---|
    | **Parameternaam van document** | documents | Komt overeen met de parameternaam in de functie. |
    | **Databasenaam** | imagesdb |  |
    | **Naam van verzameling** | images |  |
    | **SQL-query** | select * from c order by c._ts desc | Documenten ophalen, laatste documenten eerst. |
    | **Verbinding met het Azure Cosmos DB-account** | Bestaande verbindingsreeks selecteren |  |

1. Klik op **Opslaan** om de invoerbinding te maken.

1. **C#**

    1. Klik op de naam van de functie om het codevenster te openen. Vervang vervolgens alle **run.csx** door de inhoud in [**/csharp/GetImages/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/GetImages/run.csx).

1. **JavaScript**

    1. Klik op de naam van de functie om het codevenster te openen. Vervang vervolgens alle **index.js** door de inhoud in [**/javascript/GetImages/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/GetImages/index.js).

1. Klik onder het codevenster op **Logboeken** om het paneel met logboeken uit te vouwen.

1. Klik op **Opslaan**. Ga naar het paneel met logboeken om te controleren of de functie is opgeslagen en er geen fouten zijn opgetreden.


## <a name="test-the-application"></a>De toepassing testen

1. Open de toepassing in een browser. Selecteer een afbeeldingsbestand en upload het.

1. Na enkele seconden wordt de miniatuur van de nieuwe afbeelding weergegeven op de pagina.

1. Gebruik het zoekvak in de Azure-portal om op naam te zoeken naar uw Cosmos DB-account. Klik op het account om het te openen.

1. Klik aan de linkerkant op **Data Explorer** om door verzamelingen en documenten te bladeren.

1. Selecteer onder de database **imagesdb** de verzameling **images**.

1. Controleer of er een document is gemaakt voor de geüploade afbeelding.

    ![Data Explorer waarin een document wordt weergegeven voor een geüploade afbeelding](media/functions-first-serverless-web-app/4-data-explorer.png)



## <a name="summary"></a>Samenvatting

In deze module hebt u geleerd hoe u een account, database en verzameling maakt in Cosmos DB. U hebt ook geleerd hoe u Cosmos DB-bindingen gebruikt om metagegevens van afbeeldingen op te slaan en op te halen in de Cosmos DB-verzameling. Hierna leert u hoe u met behulp van Microsoft Cognitive Services automatisch een bijschrift kunt genereren voor elke geüploade afbeelding.
