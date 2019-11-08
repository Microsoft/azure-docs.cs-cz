---
title: Požadavky na rozhraní API | Azure Marketplace
description: Předpoklady s použitím rozhraní portál partnerů cloudu API.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: d3c2d89d3c3561e86047529e5b284e4481fc1652
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819706"
---
<a name="api-prerequisites"></a>Požadavky na rozhraní API
================

Existují dva požadované programové prostředky, které potřebujete použít portál partnerů cloudu rozhraní API: instanční objekt a přístupový token Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Vytvoření instančního objektu ve vašem tenantovi Azure Active Directory
----------------------------------------------------------------

Nejdřív je potřeba vytvořit instanční objekt v tenantovi Azure AD. Tomuto tenantovi se přiřadí vlastní sada oprávnění v portál partnerů cloudu. Váš kód bude volat rozhraní API pomocí tohoto tenanta namísto použití vašich osobních přihlašovacích údajů.  Úplné vysvětlení Vytvoření instančního objektu najdete v tématu [použití portálu k vytvoření Azure Active Directory aplikace a instančního objektu, který má přístup k prostředkům](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Přidání instančního objektu k vašemu účtu
-----------------------------------------

Teď, když jste ve svém tenantovi vytvořili instanční objekt, můžete ho přidat jako uživatele do svého účtu portál partnerů cloudu. Stejně jako uživatel může instanční objekt být vlastníkem nebo přispěvatelem portálu.

K přidání instančního objektu použijte následující postup:

1. Přihlaste se na portál partnerů cloudu. 
2. V levém řádku nabídek klikněte na **Uživatelé** a vyberte **Přidat uživatele**.

   ![Přidání uživatele na portál](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. V rozevíracím seznamu **typ** vyberte **instanční objekt** a přidejte následující podrobnosti:

-   **Popisný název** instančního objektu, například `spAccount`.
-   **ID aplikace** Pokud chcete tento identifikátor najít, přejděte na [portál Azure](https://portal.azure.com), klikněte na **Azure Active Directory**, vyberte **Registrace aplikací**a klikněte na svou aplikaci.
-   **ID tenanta**, označované také jako **ID adresáře**, pro vašeho tenanta Azure AD. Tento identifikátor najdete na stránce Azure Active Directory v [Azure Portal](https://portal.azure.com)v části **vlastnosti**.
-   **ID objektu** instančního objektu služby. Tento identifikátor můžete získat z Azure Portal. Přejděte na **Azure Active Directory**, vyberte **Registrace aplikací**, klikněte na svou aplikaci a klikněte na název aplikace v části **spravovaná aplikace v místním adresáři**. Potom pro vyhledání ID objektu přejít na stránku **vlastností** . Ujistěte se, že nevytváříte počáteční ID objektu, který je ve vaší aplikaci, ale místo ID objektu ve spravované aplikaci.
-   **Role** přidružená k účtu, který se použije pro RBAC.

     ![Přidání spravované aplikace na portál](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Kliknutím na **Přidat** přidejte instanční objekt k vašemu účtu.

   ![Přidání instančního objektu](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Získání přístupového tokenu Azure AD
----------------------------

Rozhraní portál partnerů cloudu API při ověřování používají následující prostředky a protokoly:

- Nosný token JSON Web Token (JWT) pro vyžádání přístupu k prostředkům
- Protokol [OpenID Connect](https://openid.net/connect/) (OIDC) pro ověření identity
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jako autorita identity

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
