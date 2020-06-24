---
title: Registrace aplikace služby ve službě Azure AD – Azure API pro FHIR
description: Naučte se, jak zaregistrovat aplikaci klienta služby v Azure Active Directory, kterou je možné použít k ověřování a získání tokenů.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 83514ea13ca51e6980a1b4b60d4555974db5a240
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2020
ms.locfileid: "84871845"
---
# <a name="register-a-service-client-application-in-azure-active-directory"></a>Registrace klientské aplikace služby v Azure Active Directory

V tomto článku se dozvíte, jak zaregistrovat aplikaci klienta služby v Azure Active Directory. Registrace klientských aplikací Azure Active Directory reprezentace aplikací, které lze použít k ověřování a získání tokenů. Klient služby má být používán aplikací k získání přístupového tokenu bez interaktivního ověřování uživatele. Bude mít určitá oprávnění aplikace a při získání přístupových tokenů používat tajný klíč aplikace (heslo).

Pokud chcete vytvořit nového klienta služby, postupujte podle následujících pokynů.

## <a name="app-registrations-in-azure-portal"></a>Registrace aplikací v Azure Portal

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na levém navigačním panelu na **Azure Active Directory**.

2. V okně **Azure Active Directory** klikněte na **Registrace aplikací**:

    ![Azure Portal. Registrace nové aplikace](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klikněte na **Nová registrace**.

## <a name="service-client-application-details"></a>Podrobnosti klientské aplikace služby

* Klient služby potřebuje zobrazovaný název a můžete taky zadat adresu URL odpovědi, ale obvykle se nepoužije.

    ![Azure Portal. Nová registrace klientské aplikace služby.](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-NAME.png)

## <a name="api-permissions"></a>Oprávnění rozhraní API

Bude nutné poskytnout aplikační role klienta služby. 

1. Otevřete **oprávnění rozhraní API** a vyberte svou [registraci aplikace prostředků rozhraní FHIR API](register-resource-azure-ad-client-app.md). Pokud používáte rozhraní API Azure pro FHIR, přidáte oprávnění k rozhraním API pro zdravotní péče Azure pomocí hledání rozhraní API pro zdravotní péče Azure v části **rozhraní API, které používá moje organizace**.

    ![Azure Portal. Oprávnění rozhraní API klienta služby](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-API-PERMISSIONS.png)

2. Vyberte aplikační role z těch, které jsou definovány v aplikaci prostředků:

    ![Azure Portal. Oprávnění klientské aplikace služby](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-APPLICATION-PERMISSIONS.png)

3. Udělte aplikaci souhlas. Pokud nemáte požadovaná oprávnění, obraťte se na správce Azure Active Directory:

    ![Azure Portal. Souhlas správce klienta služby](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-ADMIN-CONSENT.png)

## <a name="application-secret"></a>Tajný kód aplikace

Klient služby potřebuje tajný klíč (heslo), který použijete při získávání tokenů.

1. Klikněte na **certifikáty &amp; tajné klíče** .

2. Klikněte na **Nový tajný kód klienta**.

    ![Azure Portal. Tajný kód klienta služby](media/how-to-aad/portal-aad-register-new-app-registration-SERVICE-CLIENT-SECRET.png)

3. Zadejte dobu trvání tajného kódu.

4. Jakmile je vygenerována, zobrazí se pouze jednou na portálu. Poznamenejte si ho a bezpečně ho uložte.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s postupem registrace klientské aplikace služby v Azure Active Directory. V dalším kroku nasaďte rozhraní FHIR API v Azure.
 
>[!div class="nextstepaction"]
>[Nasazení Open Source serveru FHIR](fhir-oss-powershell-quickstart.md)