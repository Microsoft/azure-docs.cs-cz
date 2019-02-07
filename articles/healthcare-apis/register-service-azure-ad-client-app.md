---
title: Registrace aplikace služby v Azure Active Directory – rozhraní API služby Azure pro FHIR
description: Tento článek vysvětluje, jak zaregistrovat aplikaci služby v Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 19f730c1d8a0fc0f809e8e16016795e725d80b61
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824022"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registrace aplikace klienta služby v Azure Active Directory

V tomto článku se dozvíte, jak zaregistrovat klientská aplikace služby v Azure Active Directory. Registrace aplikace klienta jsou služby Azure Active Directory reprezentace aplikace, které lze použít k ověření a získání tokenů. Klient služby je určena pro použití aplikace k získání přístupového tokenu bez interaktivní ověřování uživatele. Bude mít určitá oprávnění aplikace a při získávání přístupových tokenů pomocí tajný klíč aplikace (heslo).

Postupujte podle pokynů můžete vytvořit nového klienta služby.

## <a name="app-registrations-in-azure-portal"></a>Registrace aplikací na webu Azure portal

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na levém navigačním panelu na **Azure Active Directory**.

2. V **Azure Active Directory** okně klikněte na **registrace aplikací (Preview)**:

    ![Azure portal. Registrace nové aplikace.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klikněte na tlačítko **registrace nové**.

## <a name="service-client-application-details"></a>Služba klienta aplikace podrobnosti

* Klient služby musí zobrazovaný název a můžete také zadat adresu URL odpovědi, ale nebude obvykle používá.

    ![Azure portal. New Service Client App Registration.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>Oprávnění rozhraní API

Je potřeba udělit této klientské služby aplikační role. 

1. Otevřít **oprávnění k rozhraní API** a vyberte váš [registrace aplikace prostředku rozhraní API FHIR](register-resource-azure-ad-client-app.md):

    ![Azure portal. Oprávnění klientského rozhraní API služby](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Vyberte aplikační role z těch, které jsou definovány pro prostředek aplikace:

    ![Azure portal. Oprávnění aplikace klienta služby](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Udělení souhlasu pro aplikaci. Pokud nemáte požadované oprávnění, obraťte se na správce Azure Active Directory:

    ![Azure portal. Souhlas správce klienta služby](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Tajný klíč aplikace

Služba Klient potřebuje tajný klíč (heslo), který se používá při získávání tokenů.

1. Klikněte na tlačítko **certifikáty &amp; tajných kódů**

2. Klikněte na tlačítko **nový tajný kód klienta**

    ![Azure portal. Tajný klíč klienta služby](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Zadejte dobu tajného klíče.

4. Jakmile byl vytvořen, se bude jenom zobrazovat jednou na portálu. Poznamenejte si ho a uložte bezpečné.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak zaregistrovat klientská aplikace služby v Azure Active Directory. V dalším kroku nasaďte FHIR API v Azure.
 
>[!div class="nextstepaction"]
>[Nasazení Open FHIR zdrojového serveru](fhir-oss-powershell-quickstart.md)