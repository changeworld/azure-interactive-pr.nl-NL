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
ms.openlocfilehash: d19a9d0e7e0347b38fc16f85fa440281be5347f2
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460076"
---
In de vorige module hebt u gezien hoe een serverloze functie het veilig uploaden van afbeeldingen vanuit een webtoepassing naar Blob Storage mogelijk maakt. In deze module maakt u een andere serverloze functie waarmee geüploade afbeeldingen worden gedetecteerd en hiervoor miniaturen worden gemaakt.

## <a name="create-a-blob-storage-container-for-thumbnails"></a>Een Blob Storage-container maken voor miniaturen

De afbeeldingen zijn op ware grootte opgeslagen in een container met de naam **images**. U hebt nog een container nodig om miniaturen van deze afbeeldingen op te slaan.

1. Controleer of u nog steeds bent aangemeld bij Cloud Shell (bash).  Als dit niet het geval is, selecteert u **Focusmodus openen** om een Cloud Shell-venster te openen. 

1. Maak een nieuwe container met de naam **thumbnails** in het opslagaccount met openbare toegang tot alle blobs.

    ```azurecli
    az storage container create -n thumbnails --account-name <storage account name> --public-access blob
    ```


## <a name="create-a-blob-triggered-serverless-function"></a>Een serverloze functie maken die wordt geactiveerd via een blob

Via een trigger wordt bepaald hoe een functie wordt aangeroepen. Voor de functie die u nu gaat maken, wordt een blobtrigger gebruikt: de functie wordt automatisch aangeroepen wanneer een blob (afbeeldingsbestand) wordt geüpload naar de container **images**. Een functie moet één trigger hebben. Aan triggers zijn gegevens gekoppeld. Meestal is dit de nettolading waarmee de functie is geactiveerd.

Met bindingen wordt gedefinieerd hoe gegevens in Azure of in services van derden worden gelezen of geschreven met de functie. Met deze functie wordt een miniatuurversie van de afbeelding gemaakt waarmee de functie wordt geactiveerd. De miniatuur wordt opgeslagen in de container *thumbnails*.

1. Open de functie-app in de Azure-portal.

1. Wijs in de linkernavigatie van het venster van de functie-app naar **Functies** en klik op **+** om te beginnen met het maken van een nieuwe serverloze functie. Als er een snelstartpagina wordt weergegeven, klikt u op **Aangepaste functie** voor een lijst met functiesjablonen.

1. Ga naar de sjabloon **BlobTrigger** en selecteer deze.

1. Gebruik deze waarden om een functie te maken waarmee miniaturen worden gemaakt bij het uploaden van afbeeldingen.

    | Instelling      |  Voorgestelde waarde   | Beschrijving                                        |
    | --- | --- | ---|
    | **Taal** | C# of JavaScript | Kies de voorkeurstaal. |
    | **Een naam voor de functie opgeven** | ResizeImage | Typ de naam precies zoals deze wordt weergegeven, zodat de functie kan worden gedetecteerd met de toepassing. |
    | **Pad** | images/{name} | Voer de functie uit wanneer een bestand wordt weergegeven in de container **images**. |
    | **Opslagaccountinformatie** | AZURE_STORAGE_CONNECTION_STRING | Gebruik de omgevingsvariabele die eerder is gemaakt met de verbindingsreeks. |

    ![Voer instellingen in voor de nieuwe functie](media/functions-first-serverless-web-app/3-new-function.png)

1. Klik op **Maken** om de functie te maken.

1. Wanneer de functie is gemaakt, klikt u op **Integreren** om de bijbehorende trigger-, invoer- en uitvoerbindingen te bekijken.

1. Klik op **Nieuwe uitvoer** om een nieuwe uitvoerbinding te maken.

    ![Selecteer op het tabblad Integreren de optie Nieuwe uitvoer](media/functions-first-serverless-web-app/3-new-output.jpg)

1. Selecteer **Azure Blob Storage** en klik op **Selecteren**. U moet misschien omlaag schuiven om de knop **Selecteren** te zien.

    ![Azure Blob Storage selecteren](media/functions-first-serverless-web-app/3-storage-output.jpg)

1. Voer de volgende waarden in.

    | Instelling      |  Voorgestelde waarde   | Beschrijving                                        |
    | --- | --- | ---|
    | **Naam van blobparameter** | thumbnail | De functie maakt gebruik van de parameter met deze naam om de miniatuur te schrijven. |
    | **Retourwaarde van functie gebruiken** | Nee |  |
    | **Pad** | thumbnails/{name} | De miniaturen worden naar een container geschreven met de naam **thumbnails**. |
    | **Opslagaccountverbinding** | AZURE_STORAGE_CONNECTION_STRING | Gebruik de omgevingsvariabele die eerder is gemaakt met de verbindingsreeks. |

    ![Instellingen invoeren voor de blob-uitvoerbinding](media/functions-first-serverless-web-app/3-blob-output.png)

1. **JavaScript**

    1. (JavaScript) Klik in de rechterbovenhoek van het venster op **Geavanceerde editor** om de JSON weer te geven die de bindingen vertegenwoordigt.

    1. (JavaScript) Voeg in de `blobTrigger`-binding een eigenschap toe met de naam `dataType` met een waarde van `binary`. Hierdoor wordt de binding geconfigureerd om de blobinhoud als binaire gegevens door te geven aan de functie.

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

1. Klik op **Opslaan** om de nieuwe binding te maken.

1. **C#**

    1. (C#) Selecteer in de linkernavigatie de functienaam **ResizeImage** om de broncode van de functie te openen.

    1. (C#) Voor de functie is een NuGet-pakket met de naam **ImageResizer** vereist om de miniaturen te genereren. NuGet-pakketten wordt met behulp van een **project.json**-bestand toegevoegd aan C#-functies. Als u het bestand wilt maken, klikt u aan de rechterkant op **Bestanden weergeven** om de bestanden weer te geven waaruit de functie bestaat.
    
    1. (C#) Klik op **Toevoegen** om een nieuw bestand toe te voegen met de naam **project.json**.
    
    1. (C#) Kopieer de inhoud van [**/csharp/ResizeImage/project.json**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/project.json) naar het zojuist gemaakte bestand. Sla het bestand op. Pakketten worden automatisch hersteld als het bestand is bijgewerkt.
    
        ![project.json-bestand met ImageResizer](media/functions-first-serverless-web-app/3-project-json.png)
    
    1. (C#) Selecteer onder **Bestanden weergeven** de optie **run.csx** en vervang de bijbehorende inhoud door de inhoud in [**/csharp/ResizeImage/run.csx**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/csharp/ResizeImage/run.csx).

1. **JavaScript** 

    1. (JavaScript) Voor deze functie is het `jimp`-pakket van npm vereist om het formaat van de foto te wijzigen. Klik in de linkernavigatie op de naam van de functie-app, en klik op **Platformfuncties** om het npm-pakket te installeren.

    1. (JavaScript) Klik op **Console** om een consolevenster weer te geven.

    1. (JavaScript) Voer de opdracht `npm install jimp` uit in de console. Het kan een paar minuten duren om de bewerking te voltooien.

    1. (JavaScript) Klik in de linkernavigatie op de functienaam **ResizeImage** om de functie weer te geven. Vervang alle **index.js** door de inhoud van [**/javascript/ResizeImage/index.js**](https://raw.githubusercontent.com/Azure-Samples/functions-first-serverless-web-application/master/javascript/ResizeImage/index.js).

1. Klik onder het codevenster op **Logboeken** om het paneel met logboeken uit te vouwen.

1. Klik op **Opslaan**. Ga naar het paneel met logboeken om te controleren of de functie is opgeslagen en er geen fouten zijn opgetreden.


## <a name="test-the-serverless-function"></a>De serverloze functie testen

1. Open de toepassing in een browser. Selecteer een afbeeldingsbestand en upload het. De upload wordt voltooid, maar de geüploade foto wordt nog niet weergegeven in de app, omdat de mogelijkheid om afbeeldingen weer te geven nog niet is toegevoegd.

1. Controleer in Cloud Shell of de afbeelding is geüpload naar de container **images**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c images -o table
    ```

1. Controleer of de miniatuur is gemaakt in een container met de naam **thumbnails**.

    ```azurecli
    az storage blob list --account-name <storage account name> -c thumbnails -o table
    ```

1. Haal de URL van de miniatuur op.

    ```azurecli
    az storage blob url --account-name <storage account name> -c thumbnails -n <filename> --output tsv
    ```

    Open de URL in een browser en controleer of de miniatuur juist is gemaakt.

1. Verwijder, voor u verdergaat met de volgende zelfstudie, eerst alle bestanden in de containers **images** en **thumbnails**.

    ```azurecli
    az storage blob delete-batch -s images --account-name <storage account name>
    ```
    ```azurecli
    az storage blob delete-batch -s thumbnails --account-name <storage account name>
    ```


## <a name="summary"></a>Samenvatting

In deze module hebt u een serverloze functie gemaakt om een miniatuur te maken telkens wanneer een afbeelding wordt geüpload naar een Blob Storage-container. Hierna leert u hoe u Azure Cosmos DB gebruikt om metagegevens van afbeeldingen op te slaan en weer te geven.
