Azure Blob Storage is een goedkope en zeer schaalbare service die kan worden gebruikt voor het hosten van statische bestanden. In deze zelfstudie gebruikt u de service om statische inhoud (bijvoorbeeld HTML, JavaScript, CSS) te leveren voor de web-app die u hebt gebouwd.

## <a name="create-a-storage-account"></a>Een Storage-account maken

Een Storage-account is een Azure-resource waarmee u tabellen, wachtrijen, bestanden, blobs (objecten) en schijven van een virtuele machineschijven kunt opslaan.

1. Meld u aan bij Cloud Shell (Bash) door de knop **Focusmodus openen** te selecteren. Deze knop ziet u rechtsboven of onder aan de pagina, afhankelijk van de breedte van het browservenster. In de focusmodus wordt een Cloud Shell-venster vastgemaakt aan de rechterkant van het browservenster, zodat u gemakkelijk de opdrachten kunt uitvoeren die worden gebruikt in de zelfstudie.

1. In Azure is een resourcegroep een container waarin aan Azure gerelateerde resources worden bewaard voor gemakkelijk beheer. Maak een nieuwe resourcegroep met de naam **first-serverless-app**.

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. De statische inhoud (HTML-, CSS- en JavaScript-bestanden) voor deze zelfstudie wordt gehost in Blob Storage. Voor Blob Storage is een Storage-account vereist. Maak een Storage-account (algemeen gebruik V2) in de resourcegroep. Vervang `<storage account name>` door een unieke naam.

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. Gebruik de zoekbalk boven in de [Azure-portal](https://portal.azure.com) om het opslagaccount te vinden dat u zojuist hebt gemaakt, en open dit account.

1. Selecteer in de linkernavigatie de optie **Statische website (preview)** om een container te configureren voor het hosten van statische websites.
    - Selecteer **Ingeschakeld** om de statische website in te schakelen.
    - Voer *index.html* in als de naam van het indexdocument. U ziet *index.html* al in grijze letters in het veld staan, maar dit is slechts een voorbeeldtekst. U moet de waarde toch nog invoeren in dit veld.
    - Klik op **Opslaan**.
    
    ![Instellingen voor statische website openen](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. Sla het **Primaire eindpunt** ergens op waar u dit gemakkelijk kunt kopiëren tijdens het volgen van de zelfstudie. Dit is de URL voor de webtoepassing.

## <a name="upload-the-web-application"></a>De webtoepassing uploaden

1. De bronbestanden voor de toepassing die u in deze zelfstudie bouwt, bevinden zich in een [GitHub-opslagplaats](https://github.com/Azure-Samples/functions-first-serverless-web-application). Zorg ervoor dat u de basismap in Cloud Shell hebt geopend en kloon deze opslagplaats.

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    De opslagplaats wordt gekloond naar `/home/<username>/functions-first-serverless-web-application`.

1. De webtoepassing aan de clientzijde bevindt zich in de map **www** en wordt gebouwd met het JavaScript-framework Vue.js. Ga naar de map en voer npm-opdrachten uit om de afhankelijkheden van de toepassing te installeren en de toepassing te bouwen. Het kan enkele minuten duren voordat deze laatste opdracht is voltooid.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    De toepassing wordt gegenereerd in de map **dist**.

1. Wijzig de huidige map in **dist** en upload de toepassing naar de blobcontainer **$web**.

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. Open de Storage-URL voor het primaire eindpunt van de statische websites in een webbrowser om de toepassing weer te geven.

    ![Startpagina voor de eerste serverloze web-app](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a>Samenvatting

In deze module hebt u een resourcegroep gemaakt met de naam **first-serverless-app** die een opslagaccount bevat. In een blobcontainer met de naam **$web** in het opslagaccount wordt de statische inhoud van de webtoepassing opgeslagen. De inhoud is openbaar beschikbaar. Hierna leert u hoe u een serverloze functie gebruikt om afbeeldingen vanuit deze webtoepassing te uploaden naar Blob Storage.
