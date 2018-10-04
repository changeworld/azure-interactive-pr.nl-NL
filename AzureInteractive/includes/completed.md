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
U hebt een serverloze toepassing met volledig functionaliteit gemaakt met behulp van Azure.

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met werken met deze toepassing, kunt u de volgende opdracht gebruiken om alle resources te verwijderen die u tijdens deze zelfstudie hebt gemaakt:

```azurecli
az group delete --name first-serverless-app
```

Typ `y` wanneer u daarom wordt gevraagd.  

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:
> [!div class="checklist"]
> * Azure Blob Storage configureren voor het hosten van een statische website en geüploade afbeeldingen.
> * Afbeeldingen uploaden naar Azure Blob Storage met behulp van Azure Functions.
> * Het formaat van afbeeldingen wijzigen met behulp van Azure Functions.
> * Metagegevens van afbeeldingen opslaan in Azure Cosmos DB.
> * Cognitive Services Vision-API gebruiken om automatisch bijschriften voor afbeeldingen te genereren.
> * Azure Active Directory gebruiken om de web-app te beveiligen door gebruikers te verifiëren.

Ga verder met de zelfstudie voor Logic Apps om te leren hoe u nog meer services kunt verbinden met Functions. 

> [!div class="nextstepaction"]
> [Functions met Logic Apps](https://docs.microsoft.com/azure/azure-functions/functions-twitter-email)
