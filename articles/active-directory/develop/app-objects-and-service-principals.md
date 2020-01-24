---
title: Aplikace & instančních objektech ve službě Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o vztahu mezi aplikací a objekty zabezpečení služby v Azure Active Directory.
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76698420"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Instanční objekty aplikací a služeb v Azure Active Directory

V případě, že se používá v kontextu Azure Active Directory (Azure AD), může se stát, že výraz "aplikace" je v některých případech nesrozumitelný. Tento článek vysvětluje koncepční a konkrétní aspekty integrace aplikací Azure AD s ilustrací registrace a souhlasu pro [víceklientské aplikace](developer-glossary.md#multi-tenant-application).

## <a name="overview"></a>Přehled

Aplikace, která je integrovaná se službou Azure AD, má vliv na rámec aspektu softwaru. Pojem "aplikace" se často používá jako koncepční termín, který odkazuje nejen na aplikační software, ale také na jeho registraci a roli Azure AD v části ověřování/autorizace "konverzace" za běhu.

Aplikace může podle definice fungovat v těchto rolích:

- Role [klienta](developer-glossary.md#client-application) (spotřebovávání prostředku)
- Role [serveru prostředků](developer-glossary.md#resource-server) (odhalení rozhraní API klientům)
- Role klienta i role serveru prostředků

[Tok udělení autorizace OAuth 2,0](developer-glossary.md#authorization-grant) definuje protokol konverzace, který umožňuje klientovi nebo prostředku přístup k datům prostředku nebo jejich ochraně.

V následujících částech se dozvíte, jak model aplikace Azure AD představuje aplikaci v době návrhu a době běhu.

## <a name="application-registration"></a>Registrace aplikací

Když zaregistrujete aplikaci Azure AD v [Azure Portal][AZURE-Portal], vytvoří se ve vašem TENANTOVI Azure AD dva objekty:

- Objekt aplikace a
- Instanční objekt služby

### <a name="application-object"></a>objekt aplikace

Aplikace služby Azure AD je definovaná jejím jediným objektem aplikace, který se nachází v tenantovi Azure AD, kde byla aplikace zaregistrovaná, označovaná jako "Domovská" tenant aplikace. Entita Microsoft Graph [aplikace][MS-Graph-App-Entity] definuje schéma pro vlastnosti objektu aplikace.

### <a name="service-principal-object"></a>instanční objekt služby

Aby bylo možné získat přístup k prostředkům, které jsou zabezpečeny klientem služby Azure AD, musí být entita, která vyžaduje přístup, reprezentovaná objektem zabezpečení. To platí pro uživatele (hlavní uživatelské objekty) i pro aplikace (instanční objekt).

Objekt zabezpečení definuje zásady přístupu a oprávnění pro uživatele nebo aplikaci v tenantovi Azure AD. To umožňuje základní funkce, jako je ověřování uživatelů nebo aplikací během přihlašování, a autorizaci během přístupu k prostředkům.

Když aplikace udělí oprávnění k přístupu k prostředkům v tenantovi (při registraci nebo [souhlasu](developer-glossary.md#consent)), vytvoří se objekt instančního objektu. Entita Microsoft Graph [ServicePrincipal][MS-Graph-Sp-Entity] definuje schéma pro vlastnosti objektu instančního objektu.

### <a name="application-and-service-principal-relationship"></a>Vztah aplikace a instančního objektu

Zvažte použití objektu aplikace jako *globální* reprezentace aplikace pro použití ve všech klientech a instančního objektu jako *místní* reprezentace pro použití v konkrétním tenantovi.

Aplikační objekt slouží jako šablona, ze které se *odvozují* běžné nebo výchozí vlastnosti, které se používají při vytváření odpovídajících instančních objektů. Objekt aplikace má proto vztah 1:1 se softwarovou aplikací a 1: n vztahů s odpovídajícími objekty instančních služeb.

V každém tenantovi, kde se aplikace používá, se musí vytvořit instanční objekt, který mu umožní vytvořit identitu pro přihlášení a/nebo přístup k prostředkům, které klient zabezpečuje. Aplikace s jedním tenantem má jenom jeden instanční objekt (ve svém domovském tenantovi), který je vytvořený a odsouhlasený k použití při registraci aplikace. Víceklientská webová aplikace/rozhraní API obsahuje taky instanční objekt vytvořený v každém tenantovi, kde uživatel z tohoto tenanta souhlasí s jeho použitím.

> [!NOTE]
> Všechny změny, které provedete v objektu aplikace, se projeví také v hlavním objektu služby v domovském tenantovi aplikace (tenant, ve kterém byl zaregistrován). U aplikací s více klienty se změny v objektu aplikace neprojeví v žádných objektech instančních objektů služby klienta, dokud se přístup neodebere přes [přístupový panel aplikace](https://myapps.microsoft.com) a znovu udělen.
>
> Všimněte si také, že nativní aplikace jsou ve výchozím nastavení registrovány jako víceklientské klienty.

## <a name="example"></a>Příklad:

Následující diagram znázorňuje vztah mezi objektem aplikace aplikace a odpovídajícími instančními objekty služby v kontextu ukázkové aplikace s více klienty s názvem **aplikace HR**. V tomto ukázkovém scénáři jsou tři klienti Azure AD:

- **Adatum** – tenant používaný společností, která vyvinula aplikaci pro **HR**
- **Contoso** – tenant používaný organizací contoso, který je příjemcem **aplikace pro personální** oddělení
- **Fabrikam** – tenant používaný organizací Fabrikam, který také využívá **aplikaci pro personální** oddělení

![Vztah mezi objektem aplikace a instančním objektem služby](./media/app-objects-and-service-principals/application-objects-relationship.svg)

V tomto ukázkovém scénáři:

| Krok | Popis |
|------|-------------|
| 1\. místo    | Je proces vytváření aplikací a objektů zabezpečení služby v domovském tenantovi aplikace. |
| 2    | Když správci společnosti Contoso a Fabrikam dokončí souhlas, vytvoří se v tenantovi služby Azure AD ve své společnosti objekt instančního objektu a přiřadí se mu oprávnění udělená správcem. Všimněte si také, že aplikace pro personální oddělení může být nakonfigurovaná/navržená tak, aby umožňovala souhlas uživatelům při individuálním použití. |
| 3    | Klienti spotřebitelů aplikace pro personální oddělení (Contoso a Fabrikam) mají vlastní objekt instančního objektu. Každý představuje použití instance aplikace za běhu, řídí se oprávněním, která souhlasují příslušný správce. |

## <a name="next-steps"></a>Další kroky

- K dotazování aplikace i instančních objektů služby můžete použít [průzkumníka Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) .
- K aplikačnímu objektu aplikace můžete přistupovat pomocí rozhraní Microsoft Graph API, editoru manifestu aplikace [Azure Portal][AZURE-Portal] nebo [rutin služby Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), jak je znázorněno v [entitě aplikace][MS-Graph-App-Entity]OData.
- K instančnímu objektu aplikace můžete přistupovat pomocí rozhraní Microsoft Graph API nebo [rutin Azure AD PowerShellu](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), které jsou reprezentované [entitou ServicePrincipal][MS-Graph-Sp-Entity]OData.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: https://docs.microsoft.com/graph/api/resources/application
[MS-Graph-Sp-Entity]: https://docs.microsoft.com/graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
