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
ms.openlocfilehash: 426a7287458a48d1bda220ad1a5f067be2ce77d6
ms.sourcegitcommit: 81587470a181e314242c7a97cd0f91c82d4fe232
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2018
ms.locfileid: "47460038"
---
Azure App Service-verificatie maakt gebruiksklare ondersteuning voor verificatie mogelijk in een Azure-functie-app. Dit integreert naadloos met Facebook, Twitter, Microsoft-accounts, Google en Azure Active Directory. U voegt App Service-verificatie toe om de back-end-API’s van de web-app te beveiligen.

## <a name="enable-app-service-authentication"></a>App Service-verificatie inschakelen

1. Open de functie-app in de Azure-portal.

1. Selecteer onder **Platformfuncties** de optie **Verificatie/autorisatie**.

    ![Verificatie/autorisatie selecteren](media/functions-first-serverless-web-app/6-authorization.jpg)

1. Selecteer de volgende waarden:
    
    | Instelling      |  Voorgestelde waarde   | Beschrijving                                        |
    | --- | --- | ---|
    | **App Service-verificatie** | Aan | Verificatie inschakelen. |
    | **Actie wanneer de aanvraag niet is geverifieerd** | Meld u aan met Azure Active Directory | Selecteer een geconfigureerde verificatiemethode (hieronder). |
    | **Verificatieproviders** | Zie hieronder | Zie hieronder |
    | **Tokenarchief** | Aan | Toestaan dat tokens worden opgeslagen en beheerd in App Service. |
    | **Toegestane externe omleidings-URL's** | De URL voor de toepassing, bijvoorbeeld: https://firstserverlessweb.z4.web.core.windows.net/ | URL(‘s) waarnaar via App Service mag worden omgeleid nadat de gebruiker is geverifieerd. |

1. Selecteer **Azure Active Directory** om de **Azure Active Directory-instellingen** weer te geven.

    1. Selecteer **Express** als de **Beheermodus**, en vul de volgende gegevens in.
    
        | Instelling      |  Voorgestelde waarde   | Beschrijving                                        |
        | --- | --- | ---|
        | **Beheermodus** | Express, nieuwe AD-app maken | Automatisch een service-principal en Azure Active Directory-verificatie instellen. |
        | **App maken** | my-serverless-webapp | Voer een unieke naam in voor de toepassing. |
    
    1. Klik op **OK** om de Azure Active Directory-instellingen op te slaan.

    ![Verificatie/autorisatie en Azure Active Directory-instellingen](media/functions-first-serverless-web-app/6-create-aad.png)

1. Klik op **Opslaan**.


## <a name="modify-the-web-app-to-enable-authentication"></a>De web-app wijzigen om verificatie in te schakelen

1. Zorg ervoor dat de huidige map in Cloud Shell deze map is: **www/dist**.

    ```azurecli
    cd ~/functions-first-serverless-web-application/www/dist
    ```

1. Als u verificatie wilt inschakelen in uw functie-app, voegt u de volgende coderegel toe aan **settings.js**.

    `window.authEnabled = true`

    Breng de wijziging aan en bekijk het resultaat door de volgende opdrachten te gebruiken of met behulp van een opdrachtregeleditor, zoals VIM.

    ```azurecli
    echo "window.authEnabled = true" >> settings.js
    ```

    Controleer of de wijziging is aangebracht in het bestand.

    ```azurecli
    cat settings.js
    ```

1. Upload het bestand naar Blob Storage.

    ```azurecli
    az storage blob upload -c \$web --account-name <storage account name> -f settings.js -n settings.js
    ```


## <a name="test-the-application"></a>De toepassing testen

1. Open de toepassing in een browser. Klik op **Aanmelden** en meld u aan.

1. Selecteer een afbeeldingsbestand en upload het.

    ![Aanmeldingspagina](media/functions-first-serverless-web-app/6-aad-auth.png)
    

## <a name="summary"></a>Samenvatting

In deze module hebt u geleerd hoe u verificatie toevoegt aan de toepassing met behulp van Azure App Service-verificatie.
