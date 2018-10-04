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
ms.openlocfilehash: 0f86f2698a3a0c1e20272c335b63faf03b4b92d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460021"
---
<span data-ttu-id="2187e-103">In deze zelfstudie implementeert u een eenvoudige webtoepassing die een gebruikersinterface op basis van HTML heeft.</span><span class="sxs-lookup"><span data-stu-id="2187e-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="2187e-104">Door de serverloze back-end kunnen via de toepassing afbeeldingen worden geüpload en automatisch bijschriften worden opgehaald die deze afbeeldingen beschrijven.</span><span class="sxs-lookup"><span data-stu-id="2187e-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Web-app uitvoeren](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="2187e-106">In het volgende diagram ziet u de Azure-services die worden gebruikt voor de toepassing:</span><span class="sxs-lookup"><span data-stu-id="2187e-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="2187e-107">In Blob Storage wordt statische website-inhoud (HTML, CSS, JS) gehost en worden afbeeldingen opgeslagen.</span><span class="sxs-lookup"><span data-stu-id="2187e-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="2187e-108">Met Azure Functions worden uploads van afbeeldingen, wijzigingen in formaat, en opslag van metagegevens beheerd.</span><span class="sxs-lookup"><span data-stu-id="2187e-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="2187e-109">In Cosmos DB worden metagegevens van afbeeldingen opgeslagen.</span><span class="sxs-lookup"><span data-stu-id="2187e-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="2187e-110">Met Logic Apps worden bijschriften voor de afbeeldingen opgehaald uit Computer Vision-API.</span><span class="sxs-lookup"><span data-stu-id="2187e-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="2187e-111">Met Azure Active Directory wordt gebruikersverificatie beheerd.</span><span class="sxs-lookup"><span data-stu-id="2187e-111">Azure Active Directory manages user authentication.</span></span>

![Diagram voor oplossingsarchitectuur](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="2187e-113">In deze zelfstudie leert u het volgende:</span><span class="sxs-lookup"><span data-stu-id="2187e-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="2187e-114">Azure Blob Storage configureren voor het hosten van een statische website en geüploade afbeeldingen.</span><span class="sxs-lookup"><span data-stu-id="2187e-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="2187e-115">Afbeeldingen uploaden naar Azure Blob Storage met behulp van Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="2187e-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="2187e-116">Het formaat van afbeeldingen wijzigen met behulp van Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="2187e-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="2187e-117">Metagegevens van afbeeldingen opslaan in Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="2187e-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="2187e-118">Cognitive Services Vision-API gebruiken om automatisch bijschriften voor afbeeldingen te genereren.</span><span class="sxs-lookup"><span data-stu-id="2187e-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="2187e-119">Azure Active Directory gebruiken om de web-app te beveiligen door gebruikers te verifiëren.</span><span class="sxs-lookup"><span data-stu-id="2187e-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>
