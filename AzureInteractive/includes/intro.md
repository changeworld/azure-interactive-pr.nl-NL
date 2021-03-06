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
In deze zelfstudie implementeert u een eenvoudige webtoepassing die een gebruikersinterface op basis van HTML heeft. Door de serverloze back-end kunnen via de toepassing afbeeldingen worden geüpload en automatisch bijschriften worden opgehaald die deze afbeeldingen beschrijven.

![Web-app uitvoeren](media/functions-first-serverless-web-app/0-app-screenshot-finished.png)

In het volgende diagram ziet u de Azure-services die worden gebruikt voor de toepassing:

1. In Blob Storage wordt statische website-inhoud (HTML, CSS, JS) gehost en worden afbeeldingen opgeslagen.
2. Met Azure Functions worden uploads van afbeeldingen, wijzigingen in formaat, en opslag van metagegevens beheerd.
3. In Cosmos DB worden metagegevens van afbeeldingen opgeslagen.
4. Met Logic Apps worden bijschriften voor de afbeeldingen opgehaald uit Computer Vision-API.
5. Met Azure Active Directory wordt gebruikersverificatie beheerd.

![Diagram voor oplossingsarchitectuur](media/functions-first-serverless-web-app/0-architecture.jpg)

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> * Azure Blob Storage configureren voor het hosten van een statische website en geüploade afbeeldingen.
> * Afbeeldingen uploaden naar Azure Blob Storage met behulp van Azure Functions.
> * Het formaat van afbeeldingen wijzigen met behulp van Azure Functions.
> * Metagegevens van afbeeldingen opslaan in Azure Cosmos DB.
> * Cognitive Services Vision-API gebruiken om automatisch bijschriften voor afbeeldingen te genereren.
> * Azure Active Directory gebruiken om de web-app te beveiligen door gebruikers te verifiëren.
