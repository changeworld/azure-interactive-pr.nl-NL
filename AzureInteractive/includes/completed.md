---
title: bestand opnemen
description: bestand opnemen
services: functions
author: ggailey777
manager: jeconnoc
ms.service: multiple
ms.topic: include
ms.date: 06/25/2018
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: a66fcc3a406c79fcf9881ddaaaf8330f5b373043
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47459991"
---
<span data-ttu-id="25957-103">U hebt een serverloze toepassing met volledig functionaliteit gemaakt met behulp van Azure.</span><span class="sxs-lookup"><span data-stu-id="25957-103">You've successfully created a full-featured, serverless application using Azure services.</span></span>

## <a name="clean-up-resources"></a><span data-ttu-id="25957-104">Resources opschonen</span><span class="sxs-lookup"><span data-stu-id="25957-104">Clean up resources</span></span>

<span data-ttu-id="25957-105">Als u klaar bent met werken met deze toepassing, kunt u de volgende opdracht gebruiken om alle resources te verwijderen die u tijdens deze zelfstudie hebt gemaakt:</span><span class="sxs-lookup"><span data-stu-id="25957-105">When you are done working with this application, you can use the following command to delete all resources created during the tutorial:</span></span>

```azurecli
az group delete --name first-serverless-app
```

<span data-ttu-id="25957-106">Typ `y` wanneer u daarom wordt gevraagd.</span><span class="sxs-lookup"><span data-stu-id="25957-106">Type `y` when prompted.</span></span>  

## <a name="next-steps"></a><span data-ttu-id="25957-107">Volgende stappen</span><span class="sxs-lookup"><span data-stu-id="25957-107">Next steps</span></span>

<span data-ttu-id="25957-108">In deze zelfstudie heeft u het volgende geleerd:</span><span class="sxs-lookup"><span data-stu-id="25957-108">In this tutorial, you learned how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="25957-109">Azure Blob Storage configureren voor het hosten van een statische website en geüploade afbeeldingen.</span><span class="sxs-lookup"><span data-stu-id="25957-109">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="25957-110">Afbeeldingen uploaden naar Azure Blob Storage met behulp van Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="25957-110">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="25957-111">Het formaat van afbeeldingen wijzigen met behulp van Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="25957-111">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="25957-112">Metagegevens van afbeeldingen opslaan in Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="25957-112">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="25957-113">Cognitive Services Vision-API gebruiken om automatisch bijschriften voor afbeeldingen te genereren.</span><span class="sxs-lookup"><span data-stu-id="25957-113">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="25957-114">Azure Active Directory gebruiken om de web-app te beveiligen door gebruikers te verifiëren.</span><span class="sxs-lookup"><span data-stu-id="25957-114">Use Azure Active Directory to secure the web app by authenticating users.</span></span>

<span data-ttu-id="25957-115">Ga verder met de zelfstudie voor Logic Apps om te leren hoe u nog meer services kunt verbinden met Functions.</span><span class="sxs-lookup"><span data-stu-id="25957-115">To learn how to connect even more services to Functions, continue to the Logic Apps tutorial.</span></span> 

> [!div class="nextstepaction"]
> [<span data-ttu-id="25957-116">Functions met Logic Apps</span><span class="sxs-lookup"><span data-stu-id="25957-116">Functions with Logic Apps</span></span>](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)
