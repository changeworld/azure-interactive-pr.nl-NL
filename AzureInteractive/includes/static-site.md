<span data-ttu-id="71599-101">Azure Blob Storage is een goedkope en zeer schaalbare service die kan worden gebruikt voor het hosten van statische bestanden.</span><span class="sxs-lookup"><span data-stu-id="71599-101">Azure Blob storage is a low-cost and massively scalable service that can be used to host static files.</span></span> <span data-ttu-id="71599-102">In deze zelfstudie gebruikt u de service om statische inhoud (bijvoorbeeld HTML, JavaScript, CSS) te leveren voor de web-app die u hebt gebouwd.</span><span class="sxs-lookup"><span data-stu-id="71599-102">For this tutorial, you use it to serve static content (for example, HTML, JavaScript, CSS) for the web app that you build.</span></span>

## <a name="create-a-storage-account"></a><span data-ttu-id="71599-103">Een Storage-account maken</span><span class="sxs-lookup"><span data-stu-id="71599-103">Create a Storage account</span></span>

<span data-ttu-id="71599-104">Een Storage-account is een Azure-resource waarmee u tabellen, wachtrijen, bestanden, blobs (objecten) en schijven van een virtuele machineschijven kunt opslaan.</span><span class="sxs-lookup"><span data-stu-id="71599-104">A Storage account is an Azure resource that allows you to store tables, queues, files, blobs (objects), and virtual machine disks.</span></span>

1. <span data-ttu-id="71599-105">Meld u aan bij Cloud Shell (Bash) door de knop **Focusmodus openen** te selecteren.</span><span class="sxs-lookup"><span data-stu-id="71599-105">Log in to the Cloud Shell (Bash), by selecting the **Enter focus mode** button.</span></span> <span data-ttu-id="71599-106">Deze knop ziet u rechtsboven of onder aan de pagina, afhankelijk van de breedte van het browservenster.</span><span class="sxs-lookup"><span data-stu-id="71599-106">This button is at the top right or the bottom of the page, depending on how wide your browser window is.</span></span> <span data-ttu-id="71599-107">In de focusmodus wordt een Cloud Shell-venster vastgemaakt aan de rechterkant van het browservenster, zodat u gemakkelijk de opdrachten kunt uitvoeren die worden gebruikt in de zelfstudie.</span><span class="sxs-lookup"><span data-stu-id="71599-107">Focus mode docks a Cloud Shell window on the right side of your browser window, so you can easily execute commands that are shown in the tutorial.</span></span>

1. <span data-ttu-id="71599-108">In Azure is een resourcegroep een container waarin aan Azure gerelateerde resources worden bewaard voor gemakkelijk beheer.</span><span class="sxs-lookup"><span data-stu-id="71599-108">In Azure, a Resource Group is a container that holds related Azure resources for ease of management.</span></span> <span data-ttu-id="71599-109">Maak een nieuwe resourcegroep met de naam **first-serverless-app**.</span><span class="sxs-lookup"><span data-stu-id="71599-109">Create a new resource group named **first-serverless-app**.</span></span>

    ```azurecli
    az group create -n first-serverless-app -l westcentralus
    ```

1. <span data-ttu-id="71599-110">De statische inhoud (HTML-, CSS- en JavaScript-bestanden) voor deze zelfstudie wordt gehost in Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="71599-110">The static content (HTML, CSS, and JavaScript files) for this tutorial is hosted in Blob Storage.</span></span> <span data-ttu-id="71599-111">Voor Blob Storage is een Storage-account vereist.</span><span class="sxs-lookup"><span data-stu-id="71599-111">Blob Storage requires a Storage account.</span></span> <span data-ttu-id="71599-112">Maak een Storage-account (algemeen gebruik V2) in de resourcegroep.</span><span class="sxs-lookup"><span data-stu-id="71599-112">Create a Storage account (general purpose V2) in the resource group.</span></span> <span data-ttu-id="71599-113">Vervang `<storage account name>` door een unieke naam.</span><span class="sxs-lookup"><span data-stu-id="71599-113">Replace `<storage account name>` with a unique name.</span></span>

    ```azurecli
    az storage account create -n <storage account name> -g first-serverless-app --kind StorageV2 -l westcentralus --https-only true --sku Standard_LRS
    ```

1. <span data-ttu-id="71599-114">Gebruik de zoekbalk boven in de [Azure-portal](https://portal.azure.com) om het opslagaccount te vinden dat u zojuist hebt gemaakt, en open dit account.</span><span class="sxs-lookup"><span data-stu-id="71599-114">Using the Search bar at the top of the [Azure portal](https://portal.azure.com), find the storage account you just created and open it.</span></span>

1. <span data-ttu-id="71599-115">Selecteer in de linkernavigatie de optie **Statische website (preview)** om een container te configureren voor het hosten van statische websites.</span><span class="sxs-lookup"><span data-stu-id="71599-115">On the left navigation, select **Static website (preview)** to configure a container for static website hosting.</span></span>
    - <span data-ttu-id="71599-116">Selecteer **Ingeschakeld** om de statische website in te schakelen.</span><span class="sxs-lookup"><span data-stu-id="71599-116">Select **Enabled** to enable static website.</span></span>
    - <span data-ttu-id="71599-117">Voer *index.html* in als de naam van het indexdocument.</span><span class="sxs-lookup"><span data-stu-id="71599-117">Enter *index.html* as the index document name.</span></span> <span data-ttu-id="71599-118">U ziet *index.html* al in grijze letters in het veld staan, maar dit is slechts een voorbeeldtekst. U moet de waarde toch nog invoeren in dit veld.</span><span class="sxs-lookup"><span data-stu-id="71599-118">The field already has *index.html* in a gray font but this is only example text; you still have to enter that value in the field.</span></span>
    - <span data-ttu-id="71599-119">Klik op **Opslaan**.</span><span class="sxs-lookup"><span data-stu-id="71599-119">Click **Save**.</span></span>
    
    ![Instellingen voor statische website openen](media/functions-first-serverless-web-app/1-storage-static-website.png)

1. <span data-ttu-id="71599-121">Sla het **Primaire eindpunt** ergens op waar u dit gemakkelijk kunt kopiëren tijdens het volgen van de zelfstudie.</span><span class="sxs-lookup"><span data-stu-id="71599-121">Save the **Primary Endpoint** somewhere you can conveniently copy it from while working through the tutorial.</span></span> <span data-ttu-id="71599-122">Dit is de URL voor de webtoepassing.</span><span class="sxs-lookup"><span data-stu-id="71599-122">This is the URL of your web application.</span></span>

## <a name="upload-the-web-application"></a><span data-ttu-id="71599-123">De webtoepassing uploaden</span><span class="sxs-lookup"><span data-stu-id="71599-123">Upload the web application</span></span>

1. <span data-ttu-id="71599-124">De bronbestanden voor de toepassing die u in deze zelfstudie bouwt, bevinden zich in een [GitHub-opslagplaats](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span><span class="sxs-lookup"><span data-stu-id="71599-124">The source files for the application that you build in this tutorial are located in a [GitHub repository](https://github.com/Azure-Samples/functions-first-serverless-web-application).</span></span> <span data-ttu-id="71599-125">Zorg ervoor dat u de basismap in Cloud Shell hebt geopend en kloon deze opslagplaats.</span><span class="sxs-lookup"><span data-stu-id="71599-125">Make sure that you are in your home directory in Cloud Shell and clone this repository.</span></span>

    ```azurecli
    cd ~
    git clone https://github.com/Azure-Samples/functions-first-serverless-web-application
    ```

    <span data-ttu-id="71599-126">De opslagplaats wordt gekloond naar `/home/<username>/functions-first-serverless-web-application`.</span><span class="sxs-lookup"><span data-stu-id="71599-126">The repository is cloned to `/home/<username>/functions-first-serverless-web-application`.</span></span>

1. <span data-ttu-id="71599-127">De webtoepassing aan de clientzijde bevindt zich in de map **www** en wordt gebouwd met het JavaScript-framework Vue.js.</span><span class="sxs-lookup"><span data-stu-id="71599-127">The client-side web application is located in the **www** folder and is built using the Vue.js JavaScript framework.</span></span> <span data-ttu-id="71599-128">Ga naar de map en voer npm-opdrachten uit om de afhankelijkheden van de toepassing te installeren en de toepassing te bouwen.</span><span class="sxs-lookup"><span data-stu-id="71599-128">Change into the folder and run npm commands to install the application's dependencies and build the application.</span></span> <span data-ttu-id="71599-129">Het kan enkele minuten duren voordat deze laatste opdracht is voltooid.</span><span class="sxs-lookup"><span data-stu-id="71599-129">The last of these commands might take several minutes to complete.</span></span>

    ```azurecli
    cd ~/functions-first-serverless-web-application/www
    npm install
    npm run generate
    ```

    <span data-ttu-id="71599-130">De toepassing wordt gegenereerd in de map **dist**.</span><span class="sxs-lookup"><span data-stu-id="71599-130">The application is generated in the **dist** folder.</span></span>

1. <span data-ttu-id="71599-131">Wijzig de huidige map in **dist** en upload de toepassing naar de blobcontainer **$web**.</span><span class="sxs-lookup"><span data-stu-id="71599-131">Change the current directory to **dist** and upload the application to the **$web** Blob container.</span></span>

    ```azurecli
    cd dist
    az storage blob upload-batch -s . -d \$web --account-name <storage account name>
    ```

1. <span data-ttu-id="71599-132">Open de Storage-URL voor het primaire eindpunt van de statische websites in een webbrowser om de toepassing weer te geven.</span><span class="sxs-lookup"><span data-stu-id="71599-132">Open the Storage static websites primary endpoint URL in a web browser to view the application.</span></span>

    ![Startpagina voor de eerste serverloze web-app](media/functions-first-serverless-web-app/1-app-screenshot-new.png)


## <a name="summary"></a><span data-ttu-id="71599-134">Samenvatting</span><span class="sxs-lookup"><span data-stu-id="71599-134">Summary</span></span>

<span data-ttu-id="71599-135">In deze module hebt u een resourcegroep gemaakt met de naam **first-serverless-app** die een opslagaccount bevat.</span><span class="sxs-lookup"><span data-stu-id="71599-135">In this module, you created a resource group named **first-serverless-app** containing a Storage account.</span></span> <span data-ttu-id="71599-136">In een blobcontainer met de naam **$web** in het opslagaccount wordt de statische inhoud van de webtoepassing opgeslagen. De inhoud is openbaar beschikbaar.</span><span class="sxs-lookup"><span data-stu-id="71599-136">A blob container named **$web** in the Storage account stores the static content for your web application and makes the content available publicly.</span></span> <span data-ttu-id="71599-137">Hierna leert u hoe u een serverloze functie gebruikt om afbeeldingen vanuit deze webtoepassing te uploaden naar Blob Storage.</span><span class="sxs-lookup"><span data-stu-id="71599-137">Next, you learn how to use a serverless function to upload images to Blob storage from this web application.</span></span>
