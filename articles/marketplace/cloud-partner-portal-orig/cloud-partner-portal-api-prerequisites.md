---
title: Požadavky na rozhraní API | Dokumentace Microsoftu
description: Požadavky z pomocí rozhraní API portál partnerů cloudu.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: e6e25424981118363070d42b2fb057b18fdb91a9
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809657"
---
<a name="api-prerequisites"></a>Požadavky na rozhraní API
================

Existují dvě požadované prostředky prostřednictvím kódu programu, které budete muset použít rozhraní API portál partnerů cloudu: hlavního názvu služby a služby Azure Active Directory (Azure AD) přístupový token.


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Vytvoření instančního objektu služby ve vašem tenantovi Azure Active Directory
----------------------------------------------------------------

Nejprve musíte vytvořit instanční objekt služby ve vašem tenantovi Azure AD. Tohoto tenanta se přiřadí vlastní sadu oprávnění v portál partnerů cloudu. Váš kód bude volat rozhraní API pro použití jako tohoto tenanta namísto použití osobních údajů.  Úplné vysvětlení vytváří se instanční objekt služby, najdete v článku [použití portálu k vytvoření aplikace Azure Active Directory a instančního objektu, který má přístup k prostředkům](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Přidejte instanční objekt služby ke svému účtu
-----------------------------------------

Teď, když jste vytvořili službu objektu zabezpečení ve vašem tenantovi, můžete ho přidat jako uživatel ke svému účtu portál partnerů cloudu. Stejně jako uživatel může být instančního objektu jako vlastníka nebo přispěvatele, jak na portálu.

Pomocí následujících kroků můžete přidat instanční objekt služby:

1. Přihlášení na portál partnerů cloudu. 
2. Klikněte na **uživatelé** na levém řádku nabídek a vyberte **přidat uživatele**.

   ![Přidat uživatele na portálu](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. Z **typ** rozevíracího seznamu vyberte **instanční objekt služby** a přidejte následující podrobnosti:

-   A **popisný název** pro instanční objekt, například `spAccount`.
-   **ID aplikace**. Tento identifikátor, najdete [webu Azure Portal](https://portal.azure.com), klikněte na tlačítko **Azure Active Directory**, zvolte **registrace aplikací**a klikněte na vaši aplikaci.
-   **ID Tenanta**, označované také jako **ID adresáře**, pro vašeho tenanta Azure AD. Tento identifikátor najdete na stránce Azure Active Directory v [webu Azure portal](https://portal.azure.com)v části **vlastnosti**.
-   **ID objektu** pro vaše instanční objekt. Tento identifikátor můžete získat z webu Azure portal. Přejděte na **Azure Active Directory**, zvolte **registrace aplikací**, klikněte na vaši aplikaci a klikněte na název aplikace v rámci **spravované aplikace v místním adresáři**. Potom pokračujte **vlastnosti** stránky, vyhledejte ID objektu. Ujistěte se, že nejsou uchopíte jeho počáteční ID objektu, který je ve vaší aplikaci, ale místo ID objektu ve spravované aplikaci.
-   **Role** přidružené k účtu, který se použije pro RBAC.

     ![Přidat spravované aplikace na portálu](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

4. Klikněte na tlačítko **přidat** přidat instanční objekt služby ke svému účtu.

   ![Přidání instančního objektu](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Získání tokenu přístupu Azure AD
----------------------------

Rozhraní API portál partnerů cloudu použijte následující prostředky a protokoly během ověřování:

- Nosný token JSON Web Token (JWT) požádat o přístup k prostředkům
- [OpenID Connect](http://openid.net/connect/) protocol (OIDC) k ověření identity
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jako autoritu identity

Existují dva hlavní přístupy k programově získání tokenu JWT:

- Použít Microsoft Authentication Library pro .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Tato vyšší úrovně přístupu se doporučuje pro vývojáře na platformě .NET. 
- Ujistěte se, **HTTP POST** žádost o Azure AD oauth **token** koncový bod, který má podobu:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Teď můžete předat tento token jako součást autorizační hlavičky pro žádosti rozhraní API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Pro všechna rozhraní API v tomto odkazu autorizační hlavičky je vždy předpokládá, že proběhlo, takže nejsou výslovně uvedena.

Pokud narazíte na chyby ověřování ve vaší žádosti, přečtěte si téma [řešení chyb ověřování](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
