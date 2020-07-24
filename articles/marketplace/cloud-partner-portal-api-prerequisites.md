---
title: Požadavky na rozhraní API – Azure Marketplace
description: Předpoklady pro používání rozhraní portál partnerů cloudu API
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: dsindona
ms.author: dsindona
ms.date: 07/14/2020
ms.openlocfilehash: b672afcd8f52d8293b1bb81e30097062e2ee49c3
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87053587"
---
<a name="api-prerequisites"></a>Požadavky na rozhraní API
================

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Existují dva požadované programové prostředky, které potřebujete použít portál partnerů cloudu rozhraní API: instanční objekt a přístupový token Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Vytvoření instančního objektu ve vašem tenantovi Azure Active Directory
----------------------------------------------------------------

Nejdřív je potřeba vytvořit instanční objekt v tenantovi Azure AD. Tomuto tenantovi se přiřadí vlastní sada oprávnění v portál partnerů cloudu. Váš kód bude volat rozhraní API pomocí tohoto tenanta namísto použití vašich osobních přihlašovacích údajů.  Úplné vysvětlení vytváření instančního objektu naleznete v tématu [How to: use the a Create a Application Azure AD a instanční objekt, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md).


<a name="add-the-service-principal-to-your-account"></a>Přidání instančního objektu k vašemu účtu
-----------------------------------------

Teď, když jste ve svém tenantovi vytvořili instanční objekt, můžete ho přidat jako uživatele do svého účtu portál partnerů cloudu. Stejně jako uživatel může instanční objekt být vlastníkem nebo přispěvatelem portálu.

K přidání instančního objektu použijte následující postup:

1. Přihlaste se na portál partnerů cloudu. 
2. V levém řádku nabídek klikněte na **Uživatelé** a vyberte **Přidat uživatele**.

   ![Přidání uživatele na portál](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. V rozevíracím seznamu **typ** vyberte **instanční objekt** a přidejte následující podrobnosti:

-   **Popisný název** instančního objektu, například `spAccount` .
-   **ID aplikace** Pokud chcete tento identifikátor najít, přejděte na [Azure Portal](https://portal.azure.com), klikněte na **Azure Active Directory**, vyberte **Registrace aplikací**a klikněte na svou aplikaci.
-   **ID tenanta**, označované také jako **ID adresáře**, pro vašeho tenanta Azure AD. Tento identifikátor najdete na stránce Azure Active Directory v [Azure Portal](https://portal.azure.com)v části **vlastnosti**.
-   **ID objektu** instančního objektu služby. Tento identifikátor můžete získat z Azure Portal. Přejděte na **Azure Active Directory**, vyberte **Registrace aplikací**, klikněte na svou aplikaci a klikněte na název aplikace v části **spravovaná aplikace v místním adresáři**. Potom pro vyhledání ID objektu přejít na stránku **vlastností** . Ujistěte se, že nevytváříte počáteční ID objektu, který je ve vaší aplikaci, ale místo ID objektu ve spravované aplikaci.
-   **Role** přidružená k účtu, který se použije pro RBAC.

     ![Přidání spravované aplikace na portál](./media/cloud-partner-portal-api-prerequisites/managed-app.png)

1. Kliknutím na **Přidat** přidejte instanční objekt k vašemu účtu.

   ![Přidání instančního objektu](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Získání přístupového tokenu Azure AD
----------------------------

Rozhraní portál partnerů cloudu API při ověřování používají následující prostředky a protokoly:

- Nosný token JSON Web Token (JWT) pro vyžádání přístupu k prostředkům
- Protokol [OpenID Connect](https://openid.net/connect/) (OIDC) pro ověření identity
- [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) jako autorita identity

Existují dva základní přístupy k programovému získání tokenu JWT:

- Použijte Microsoft Authentication Library pro .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Tento přístup vyšší úrovně se doporučuje pro vývojáře v rozhraní .NET. 
- Proveďte požadavek **http post** na koncový bod **tokenu** OAuth Azure AD, který má formu:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Nyní můžete tento token předat jako součást autorizační hlavičky pro požadavky rozhraní API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> V případě všech rozhraní API v této referenci se vždy předpokládá, že je předána záhlaví autorizace, takže není výslovně zmíněná.

Pokud v žádosti spustíte chyby ověřování, přečtěte si téma [Poradce při potížích s chybami ověřování](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
