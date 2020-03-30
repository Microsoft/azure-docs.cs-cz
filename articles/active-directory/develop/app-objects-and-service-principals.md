---
title: Aplikace & instanční objekty ve službě Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Informace o vztahu mezi objekty hlavního nastavení aplikace a služby ve službě Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/13/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: 19085346fb5797245c9f71911f8178df0a1b742a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263007"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Instanční objekty aplikací a služeb v Azure Active Directory

V některých případě význam termínu "aplikace" může být nepochopena při použití v kontextu Služby Azure Active Directory (Azure AD). Tento článek objasňuje koncepční a konkrétní aspekty integrace aplikací Azure AD s ilustrací registrace a souhlasu pro [víceklientské aplikace](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Přehled

Aplikace, která byla integrována s Azure AD má důsledky, které jdou nad rámec aspekt u softwaru. "Aplikace" se často používá jako koncepční termín, odkazující nejen na aplikační software, ale také jeho registrace Azure AD a role v ověřování/autorizace "konverzace" za běhu.

Podle definice aplikace může fungovat v těchto rolích:

- [Role klienta](developer-glossary.md#client-application) (využití prostředku)
- Role [serveru prostředků](developer-glossary.md#resource-server) (vystavení api klientům)
- Role klienta i role serveru prostředků

[Tok udělení autorizace OAuth 2.0](developer-glossary.md#authorization-grant) definuje protokol konverzace, který umožňuje klientovi/prostředku přístup/ochranu dat prostředku.

V následujících částech uvidíte, jak model aplikace Azure AD představuje aplikaci v době návrhu a za běhu.

## <a name="application-registration"></a>Registrace aplikací

Když zaregistrujete aplikaci Azure AD na [webu Azure Portal][AZURE-Portal], vytvoří se ve vašem tenantovi Azure AD dva objekty:

- Aplikační objekt a
- instanční objekt.

### <a name="application-object"></a>Aplikační objekt

Aplikace Azure AD je definována jeho jediný aplikační objekt, který se nachází v tenantovi Azure AD, kde byla aplikace registrována, označované jako "domácí" tenantaplikace. [Entita aplikace Microsoft][MS-Graph-App-Entity] Graph definuje schéma vlastností aplikačního objektu.

### <a name="service-principal-object"></a>Objekt instančního objektu

Chcete-li získat přístup k prostředkům, které jsou zabezpečeny klientem Azure AD, musí být entita, která vyžaduje přístup, reprezentována objektem zabezpečení. To platí jak pro uživatele (zaregistrovaný objekt uživatele), tak pro aplikace (instanční objekt).

Zaregistrovaný objekt zabezpečení definuje zásady přístupu a oprávnění pro uživatele nebo aplikaci v tenantovi Azure AD. To umožňuje základní funkce, jako je ověřování uživatele nebo aplikace během přihlášení a autorizace během přístupu k prostředkům.

Když je aplikaci uděleno oprávnění k přístupu k prostředkům v tenantovi (po registraci nebo [souhlasu](developer-glossary.md#consent)), vytvoří se objekt instančního objektu. [Entita][MS-Graph-Sp-Entity] Microsoft Graph ServicePrincipal definuje schéma pro vlastnosti objektu instančního objektu.

### <a name="application-and-service-principal-relationship"></a>Vztah aplikace a instančního objektu

Zvažte objekt aplikace jako *globální* reprezentaci vaší aplikace pro použití ve všech klientech a instanční objekt jako *místní* reprezentace pro použití v konkrétním klientovi.

Aplikační objekt slouží jako šablona, ze které se *odvozují* běžné nebo výchozí vlastnosti, které se používají při vytváření odpovídajících instančních objektů. Aplikační objekt má proto vztah 1:1 se softwarovou aplikací a vztahy 1:n s odpovídajícím objektem instančního objektu.

Instanční objekt musí být vytvořen v každém tenantovi, kde se používá aplikace, což mu umožňuje vytvořit identitu pro přihlášení a/nebo přístup k prostředkům zabezpečeným klientem. Aplikace s jedním tenantem má jenom jeden instanční objekt (ve svém domovském tenantovi), který je vytvořený a odsouhlasený k použití při registraci aplikace. Víceklientská webová aplikace nebo rozhraní API má také instanční objekt vytvořený v každém klientovi, kde uživatel z tohoto klienta souhlasil s jeho použitím.

> [!NOTE]
> Všechny změny provedené v objektu aplikace se také projeví v objektu instančního objektu v domovském tenantovi aplikace (klient, kde byl zaregistrován). U víceklientských aplikací se změny aplikačního objektu neprojeví v objektech instančního objektu žádného klienta příjemce, dokud nebude přístup odebrán prostřednictvím [přístupového panelu aplikace](https://myapps.microsoft.com) a znovu udělen.
>
> Všimněte si také, že nativní aplikace jsou registrovány jako víceklientské ve výchozím nastavení.

## <a name="example"></a>Příklad

Následující diagram znázorňuje vztah mezi aplikačním objektem aplikace a odpovídajícími objekty instančního objektu v kontextu ukázkové víceklientské aplikace nazývané **aplikace HR**. V tomto příkladovém scénáři existují tři klienti Azure AD:

- **Adatum** - Nájemce používaný společností, která vyvinula **hr aplikaci**
- **Contoso** – tenant používaný organizací Contoso, která je příjemcem **aplikace HR**
- **Fabrikam** – tenant používaný organizací Fabrikam, která také spotřebovává **aplikaci HR**

![Vztah mezi objektem aplikace a objektem instančního objektu](./media/app-objects-and-service-principals/application-objects-relationship.svg)

V tomto příkladu scénáře:

| Krok | Popis |
|------|-------------|
| 1    | Je proces vytváření objektů hlavního nastavení aplikace a služby v domovském tenantovi aplikace. |
| 2    | Když správci contoso a Fabrikam dokončit souhlas, objekt instančního objektu se vytvoří v tenantovi jejich společnosti Azure AD a přiřazena oprávnění, která udělil správce. Všimněte si také, že hr aplikace by mohla být nakonfigurována / navržena tak, aby umožňovala souhlas uživatelů pro individuální použití. |
| 3    | Spotřebitelské klienty aplikace HR (Contoso a Fabrikam) mají každý svůj vlastní objekt instančního objektu. Každý představuje jejich použití instance aplikace za běhu, řídí oprávnění mi se souhlasem příslušného správce. |

## <a name="next-steps"></a>Další kroky

- Pomocí aplikace [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) můžete dotazovat objekty instančního objektu i objekty instančního objektu.
- K aplikačnímu objektu aplikace můžete přistupovat pomocí rozhraní Microsoft Graph API, editoru manifestu aplikace [portálu Azure][AZURE-Portal] nebo [rutin prostředí Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), které reprezentují [její entita Aplikace][MS-Graph-App-Entity]OData .
- K objektu instančního objektu aplikace můžete přistupovat prostřednictvím rozhraní Microsoft Graph API nebo [rutin prostředí Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), které reprezentují její [entita][MS-Graph-Sp-Entity]OData ServicePrincipal .

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
