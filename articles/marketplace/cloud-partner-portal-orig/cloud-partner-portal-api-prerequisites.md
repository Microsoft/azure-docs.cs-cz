---
title: Požadavky rozhraní API | Azure Marketplace
description: Požadavky na základě řešení api portálu cloudových partnerů.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: df94cba1f77ae3ea8cf595e7c651af7a69108bb6
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255970"
---
<a name="api-prerequisites"></a>Požadavky rozhraní API
================

> [!NOTE]
> Api portálu pro partnery cloudu jsou integrovaná s Partnerským centrem a budou fungovat i po migraci nabídek do Centra partnerů. Integrace přináší malé změny. Zkontrolujte změny uvedené v [referenčním rozhraní API portálu cloudových partnerů a](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) ujistěte se, že váš kód bude fungovat i po migraci do Centra partnerů.

Existují dva požadované programové prostředky, které je potřeba použít rozhraní API portálu cloudových partnerů: instanční objekt a přístupový token Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Vytvoření objektu zabezpečení služeb v tenantovi služby Azure Active Directory
----------------------------------------------------------------

Nejprve je potřeba vytvořit instanční objekt ve vašem tenantovi Azure AD. Tomuto tenantovi bude přiřazena vlastní sada oprávnění na portálu partnerů cloudu. Váš kód bude volat API pomocí jako tento klient namísto použití osobních pověření.  Úplné vysvětlení vytvoření instančního objektu najdete [v tématu Použití portálu k vytvoření aplikace Azure Active Directory a instancí služby, které mají přístup k prostředkům](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).


<a name="add-the-service-principal-to-your-account"></a>Přidání instančního objektu k účtu
-----------------------------------------

Teď, když jste vytvořili instanční objekt ve vašem tenantovi, můžete ho přidat jako uživatele do účtu portálu cloudových partnerů. Stejně jako uživatel může být instanční objekt vlastníkem nebo přispěvatelem portálu.

Pomocí následujících kroků přidejte instanční objekt:

1. Přihlaste se na portál partnerů cloudu. 
2. Klikněte na **Uživatelé** na levém panelu menu a zvolte **Přidat uživatele**.

   ![Přidání uživatele na portál](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. V rozevíracím **příkladu Typ** vyberte **Instanční objekt** a přidejte následující podrobnosti:

-   Popisný **název** pro instanční `spAccount`objekt, například .
-   **ID aplikace**. Pokud chcete najít tento identifikátor, přejděte na [portál Azure](https://portal.azure.com), klikněte na Azure **Active Directory**, zvolte Registrace **aplikací**a klikněte na svou aplikaci.
-   **ID klienta**, označované také jako **ID adresáře**, pro vašeho klienta Azure AD. Tento identifikátor najdete na stránce Azure Active Directory na [webu Azure Portal](https://portal.azure.com)v části **Vlastnosti**.
-   **ID objektu** pro objekt instančního objektu. Tento identifikátor můžete získat z webu Azure Portal. Přejděte do **Služby Azure Active Directory**, zvolte **Registrace aplikací**, klikněte na aplikaci a klikněte na název aplikace v části **Spravovaná aplikace v místním adresáři**. Potom přejděte na stránku **Vlastnosti** a vyhledejte ID objektu. Ujistěte se, že nezabírápočáteční ID objektu, který je ve vaší aplikaci, ale místo toho ID objektu ve spravované aplikaci.
-   **Role** přidružené k účtu, který bude použit pro RBAC.

     ![Přidání spravované aplikace na portál](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Kliknutím na **Přidat** přidáte instanční objekt do svého účtu.

   ![Přidání instančního objektu](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Získání přístupového tokenu Azure AD
----------------------------

Api portálu pro cloudpartnery používají při ověřování následující prostředky a protokoly:

- JSON Web Token (JWT) nosič token pro vyžádání přístupu k prostředkům
- Protokol [OpenID Connect](https://openid.net/connect/) (OIDC) pro ověření identity
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) jako autorita identity

Existují dva principové přístupy k programovém získání tokenu JWT:

- Použijte knihovnu ověřování společnosti Microsoft pro rozhraní .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Tento přístup vyšší úrovně je doporučen pro vývojáře rozhraní .NET. 
- Vytvořte požadavek **HTTP POST** na koncový bod **tokenu** Azure AD oauth, který má formu:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Nyní můžete předat tento token jako součást hlavičky autorizace pro požadavky rozhraní API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Pro všechna úložiště API v tomto odkazu je vždy předpokládána hlavička autorizace předána, takže není explicitně uvedena.

Pokud v žádosti narazíte na chyby ověřování, [přečtěte si článek Poradce při potížích s chybami ověřování](./cloud-partner-portal-api-troubleshooting-authentication-errors.md).
