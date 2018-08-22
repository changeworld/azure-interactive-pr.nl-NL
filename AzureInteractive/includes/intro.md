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
ms.openlocfilehash: d651fd3d03f2678d625e60f9ab1e9f59e623964f
ms.sourcegitcommit: e721422a57e6deb95245135fd9f4f5677c344d93
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2018
ms.locfileid: "40079237"
---
<span data-ttu-id="b7bce-103">In deze zelfstudie implementeert u een eenvoudige webtoepassing die een gebruikersinterface op basis van HTML heeft.</span><span class="sxs-lookup"><span data-stu-id="b7bce-103">In this tutorial, you deploy a simple web application that presents an HTML-based user interface.</span></span> <span data-ttu-id="b7bce-104">Door de serverloze back-end kunnen via de toepassing afbeeldingen worden geüpload en automatisch bijschriften worden opgehaald die deze afbeeldingen beschrijven.</span><span class="sxs-lookup"><span data-stu-id="b7bce-104">A serverless backend enables the application to upload images and automatically get captions describing them.</span></span>

![Web-app uitvoeren](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

<span data-ttu-id="b7bce-106">In het volgende diagram ziet u de Azure-services die worden gebruikt voor de toepassing:</span><span class="sxs-lookup"><span data-stu-id="b7bce-106">The following diagram shows the Azure services used by the application:</span></span>

1. <span data-ttu-id="b7bce-107">In Blob Storage wordt statische website-inhoud (HTML, CSS, JS) gehost en worden afbeeldingen opgeslagen.</span><span class="sxs-lookup"><span data-stu-id="b7bce-107">Blob Storage serves static web content (HTML, CSS, JS) and stores images.</span></span>
2. <span data-ttu-id="b7bce-108">Met Azure Functions worden uploads van afbeeldingen, wijzigingen in formaat, en opslag van metagegevens beheerd.</span><span class="sxs-lookup"><span data-stu-id="b7bce-108">Azure Functions manages image uploads, resizing, and metadata storage.</span></span>
3. <span data-ttu-id="b7bce-109">In Cosmos DB worden metagegevens van afbeeldingen opgeslagen.</span><span class="sxs-lookup"><span data-stu-id="b7bce-109">Cosmos DB stores image metadata.</span></span>
4. <span data-ttu-id="b7bce-110">Met Logic Apps worden bijschriften voor de afbeeldingen opgehaald uit Computer Vision-API.</span><span class="sxs-lookup"><span data-stu-id="b7bce-110">Logic Apps gets image captions from Computer Vision API.</span></span>
5. <span data-ttu-id="b7bce-111">Met Azure Active Directory wordt gebruikersverificatie beheerd.</span><span class="sxs-lookup"><span data-stu-id="b7bce-111">Azure Active Directory manages user authentication.</span></span>

![Diagram voor oplossingsarchitectuur](media/functions-first-serverless-web-app/0-architecture.jpg)

<span data-ttu-id="b7bce-113">In deze zelfstudie leert u het volgende:</span><span class="sxs-lookup"><span data-stu-id="b7bce-113">In this tutorial, you learn how to:</span></span>
> [!div class="checklist"]
> * <span data-ttu-id="b7bce-114">Azure Blob Storage configureren voor het hosten van een statische website en geüploade afbeeldingen.</span><span class="sxs-lookup"><span data-stu-id="b7bce-114">Configure Azure Blob storage to host a static website and uploaded images.</span></span>
> * <span data-ttu-id="b7bce-115">Afbeeldingen uploaden naar Azure Blob Storage met behulp van Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="b7bce-115">Upload images to Azure Blob storage using Azure Functions.</span></span>
> * <span data-ttu-id="b7bce-116">Het formaat van afbeeldingen wijzigen met behulp van Azure Functions.</span><span class="sxs-lookup"><span data-stu-id="b7bce-116">Resize images using Azure Functions.</span></span>
> * <span data-ttu-id="b7bce-117">Metagegevens van afbeeldingen opslaan in Azure Cosmos DB.</span><span class="sxs-lookup"><span data-stu-id="b7bce-117">Store image metadata in Azure Cosmos DB.</span></span>
> * <span data-ttu-id="b7bce-118">Cognitive Services Vision-API gebruiken om automatisch bijschriften voor afbeeldingen te genereren.</span><span class="sxs-lookup"><span data-stu-id="b7bce-118">Use Cognitive Services Vision API to auto-generate image captions.</span></span>
> * <span data-ttu-id="b7bce-119">Azure Active Directory gebruiken om de web-app te beveiligen door gebruikers te verifiëren.</span><span class="sxs-lookup"><span data-stu-id="b7bce-119">Use Azure Active Directory to secure the web app by authenticating users.</span></span>