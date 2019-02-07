---
title: Registrace ve službě Azure Active Directory – rozhraní API služby Azure pro FHIR důvěrnému klientovi aplikace
description: Tento článek vysvětluje, jak zaregistrovat aplikaci důvěrnému klientovi v Azure Active Directory.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: mihansen
ms.openlocfilehash: 064cf4bb0b6e9454a4049cc6c32b51b09e5b2a53
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824002"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrace aplikace důvěrnému klientovi v Azure Active Directory

V tomto článku se dozvíte, jak zaregistrovat důvěrné klientskou aplikaci v Azure Active Directory. Registrace aplikace klienta je reprezentací aplikaci, která slouží k ověřování jménem uživatele a požádejte o přístup k Azure Active Directory [prostředků aplikace](register-resource-azure-ad-client-app.md). Důvěrnému klientovi aplikace je aplikace, která může považovat za důvěryhodné, která bude uchovávat tajného kódu a poskytnout tento tajný kód při žádosti o přístupové tokeny. Příkladem důvěrných aplikací jsou aplikace na straně serveru.

Registrace nové aplikace důvěrné informace na portálu, použijte následující postup.

## <a name="app-registrations-in-azure-portal"></a>Registrace aplikací na webu Azure portal

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na levém navigačním panelu na **Azure Active Directory**.

2. V **Azure Active Directory** okně klikněte na **registrace aplikací (Preview)**:

    ![Azure portal. Registrace nové aplikace.](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klikněte na tlačítko **registrace nové**.

## <a name="register-a-new-application"></a>Registrace nové aplikace

1. Zadejte zobrazovaný název aplikace.

2. Zadejte adresu URL odpovědi. Tyto informace se dá změnit později, ale pokud znáte příslušnou odpovědní adresu URL vaší aplikace, zadejte ho nyní.

    ![Registrace nové aplikace Důvěrnému klientovi.](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>Oprávnění rozhraní API

Dále přidejte oprávnění k rozhraní API:

1. Otevřít **oprávnění k rozhraní API** a vyberte váš [registrace aplikace prostředku rozhraní API FHIR](register-resource-azure-ad-client-app.md):

    ![Důvěrnému klientovi. Oprávnění k rozhraní API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Klikněte na tlačítko **přidat oprávnění**

3. Vyberte prostředek rozhraní API:

    ![Důvěrnému klientovi. Moje rozhraní API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Vyberte obory (oprávnění), které důvěrné aplikace by měla být schopna požádat jménem uživatele:

    ![Důvěrnému klientovi. Delegovaná oprávnění](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Tajný klíč aplikace

1. Vytvořte tajný klíč aplikace (tajný klíč klienta):

    ![Důvěrnému klientovi. Tajný klíč aplikace](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Zadejte popis a dobu trvání tajný kód.

3. Po vygenerování se bude zobrazovat na portálu jenom jednou. Poznamenejte si ji a bezpečně uložit.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak zaregistrovat aplikaci důvěrnému klientovi v Azure Active Directory. V dalším kroku nasaďte FHIR API v Azure.
 
>[!div class="nextstepaction"]
>[Nasazení Open FHIR zdrojového serveru](fhir-oss-powershell-quickstart.md)