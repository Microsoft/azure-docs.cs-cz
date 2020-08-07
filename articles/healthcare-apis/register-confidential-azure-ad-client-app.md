---
title: Registrace důvěrné klientské aplikace v Azure AD – Azure API pro FHIR
description: Registrace důvěrné klientské aplikace v Azure Active Directory, která se ověřuje jménem uživatele a žádá o přístup k aplikacím prostředků
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: matjazl
ms.openlocfilehash: 756645d2df22f1222c3004a44e5a46c7a3bc1a2f
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2020
ms.locfileid: "87852544"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory"></a>Registrace důvěrné klientské aplikace v Azure Active Directory

V tomto kurzu se dozvíte, jak zaregistrovat důvěrnou klientskou aplikaci v Azure Active Directory. 

Registrace klientské aplikace je Azure Active Directory reprezentace aplikace, kterou lze použít k ověřování jménem uživatele a požadování přístupu k [aplikacím prostředků](register-resource-azure-ad-client-app.md). Důvěrná klientská aplikace je aplikace, která může být důvěryhodná pro uchování tajného klíče a při vyžádání přístupových tokenů prezentuje tajný klíč. Příklady důvěrných aplikací jsou aplikace na straně serveru.

Pokud chcete zaregistrovat novou důvěrnou aplikaci na portálu, postupujte podle následujících kroků.

## <a name="app-registrations-in-azure-portal"></a>Registrace aplikací v Azure Portal

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na levém navigačním panelu na **Azure Active Directory**.

2. V okně **Azure Active Directory** klikněte na **Registrace aplikací**:

    ![Azure Portal. Registrace nové aplikace](media/how-to-aad/portal-aad-new-app-registration.png)

3. Klikněte na **novou registraci**.

## <a name="register-a-new-application"></a>Registrace nové aplikace

1. Zadejte zobrazovaný název aplikace.

2. Zadejte adresu URL odpovědi. Tyto podrobnosti lze později změnit, ale pokud znáte adresu URL odpovědi vaší aplikace, zadejte ji nyní.

    ![Nová registrace aplikace důvěrného klienta](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT.png)

## <a name="api-permissions"></a>Oprávnění rozhraní API

Další přidat oprávnění rozhraní API:

1. Otevřete **oprávnění rozhraní API**:

    ![Důvěrný klient. Oprávnění API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-Permissions.png)

2. Klikněte na **Přidat oprávnění** .

3. Vyberte příslušné rozhraní API prostředku:

    Pro rozhraní API Azure pro FHIR (spravovaná služba) klikněte na **rozhraní API moje organizace používá** a vyhledejte "rozhraní API pro zdravotní péče Azure". Pro Open Source Server FHIR pro Azure vyberte [registraci aplikace prostředků rozhraní FHIR API](register-resource-azure-ad-client-app.md):

    ![Důvěrný klient. Moje rozhraní API](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-MyApis.png)

4. Vyberte obory (oprávnění), na které by tajná aplikace měla být schopná požádat jménem uživatele:

    ![Důvěrný klient. Delegovaná oprávnění](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-API-DelegatedPermissions.png)

## <a name="application-secret"></a>Tajný kód aplikace

1. Vytvoření tajného klíče aplikace (tajný klíč klienta):

    ![Důvěrný klient. Tajný klíč aplikace](media/how-to-aad/portal-aad-register-new-app-registration-CONF-CLIENT-SECRET.png)

2. Zadejte popis a dobu trvání tajného kódu.

3. Po vygenerování se na portálu zobrazí jenom jednou. Poznamenejte si ho a bezpečně ho uložte.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s postupem registrace důvěrné klientské aplikace v Azure Active Directory. Nyní jste připraveni nasadit [rozhraní API Azure pro FHIR](fhir-paas-powershell-quickstart.md).

Po nasazení rozhraní API Azure pro FHIR můžete zkontrolovat další dostupná nastavení.
 
>[!div class="nextstepaction"]
>[Nasazení Azure API for FHIR](fhir-paas-powershell-quickstart.md)