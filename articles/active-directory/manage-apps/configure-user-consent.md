---
title: Konfigurace souhlasu uživatele s aplikací – Azure Active Directory
description: Naučte se spravovat způsob, jakým uživatelé souhlasí s oprávněními k aplikacím. Můžete zjednodušit uživatelské prostředí tím, že udělíte souhlas správce. Tyto metody se vztahují na všechny koncové uživatele v tenantovi Azure Active Directory (Azure AD).
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a187ea9d22efa40c482ea8930be0271d84a899a
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74273994"
---
# <a name="configure-the-way-end-users-consent-to-an-application-in-azure-active-directory"></a>Konfigurace způsobu, jakým koncoví uživatelé souhlasí s aplikací v Azure Active Directory
Naučte se konfigurovat způsob, jakým uživatelé souhlasí s oprávněními k aplikacím. Můžete zjednodušit uživatelské prostředí tím, že udělíte souhlas správce. Tento článek popisuje různé způsoby, jak můžete nakonfigurovat souhlas uživatele. Metody se vztahují na všechny koncové uživatele v tenantovi Azure Active Directory (Azure AD). 

Další informace o tom, jak se předávají aplikacím, najdete v tématu [Azure Active Directory souhlasu Framework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Požadavky

Udělení souhlasu správce vyžaduje, abyste se přihlásili jako globální správce, správce aplikace nebo správce cloudové aplikace.

Chcete-li omezit přístup k aplikacím, je nutné vyžadovat přiřazení uživatele a pak přiřadit uživatele nebo skupiny k aplikaci.  Další informace najdete v tématu [metody přiřazení uživatelů a skupin](methods-for-assigning-users-and-groups.md).

## <a name="grant-admin-consent-to-enterprise-apps-in-the-azure-portal"></a>Udělení souhlasu správce podnikovým aplikacím v Azure Portal

Udělení souhlasu správce podnikové aplikaci:

1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce, správce aplikace nebo správce cloudové aplikace.
2. V horní části levé navigační nabídky klikněte na **všechny služby** . Otevře se **rozšíření Azure Active Directory** .
3. Do vyhledávacího pole filtru zadejte **"Azure Active Directory"** a vyberte položku **Azure Active Directory** .
4. V navigační nabídce klikněte na **podnikové aplikace**.
5. Vyberte aplikaci pro vyjádření souhlasu.
6. Vyberte **oprávnění** a pak klikněte na **udělit souhlas správce**. Zobrazí se výzva, abyste se přihlásili, aby se aplikace mohla spravovat.
7. Přihlaste se pomocí účtu, který má oprávnění k udělení souhlasu správce pro aplikaci. 
8. Vyjádření souhlasu s oprávněními aplikace

Tato možnost funguje jenom v případě, že je aplikace: 

- Registrováno ve vašem tenantovi nebo
- Registrováno v jiném tenantovi služby Azure AD a bylo odsouhlaseno alespoň jedním koncovým uživatelem. Když koncový uživatel souhlasí s aplikací, Azure AD vypíše aplikaci v části **podnikové aplikace** v Azure Portal.

## <a name="grant-admin-consent-when-registering-an-app-in-the-azure-portal"></a>Udělení souhlasu správce při registraci aplikace v Azure Portal

Udělení souhlasu správce při registraci aplikace: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce.
2. Přejděte do okna **Registrace aplikací** .
3. Vyberte aplikaci pro souhlas.
4. Vyberte **oprávnění k rozhraní API**.
5. Klikněte na **udělit souhlas správce**.


## <a name="grant-admin-consent-through-a-url-request"></a>Udělení souhlasu správce prostřednictvím žádosti adresy URL

Udělení souhlasu správce prostřednictvím žádosti adresy URL:

1. Vytvořte požadavek na *Login.microsoftonline.com* s konfiguracemi aplikace a přihlaste se na `&prompt=admin_consent`. Tato adresa URL bude vypadat takto: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=admin_consent`
2. Po přihlášení s přihlašovacími údaji správce má aplikace udělena souhlas pro všechny uživatele.


## <a name="force-user-consent-through-a-url-request"></a>Vynucení souhlasu uživatele prostřednictvím žádosti adresy URL

Pokud chcete, aby koncoví uživatelé souhlasili s aplikací při každém ověření, přidejte `&prompt=consent` k adrese URL žádosti o ověření.
Tato adresa URL bude vypadat takto: `https://login.microsoftonline.com/<tenant-id>/oauth2/authorize?client_id=<client id>&response_type=code&redirect_uri=<Your-Redirect-URI-Https-Encoded>&nonce=1234&resource=<your-resource-Https-encoded>&prompt=consent`

## <a name="next-steps"></a>Další kroky

[Souhlas a integrace aplikací do AzureAD](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

[Souhlas a oprávnění pro sblížené aplikace AzureAD v 2.0](../develop/active-directory-v2-scopes.md)

[AzureAD StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)
