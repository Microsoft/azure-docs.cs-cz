---
title: Aplikace & instančních objektech ve službě Azure AD | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si o vztahu mezi aplikací a objekty zabezpečení služby v Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/15/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: 358e066631304e727d18d092bd4b9a5b2a0bb89a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199613"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Instanční objekty aplikací a služeb v Azure Active Directory

Tento článek popisuje registraci aplikace, aplikační objekty a instanční objekty v Azure Active Directory: co jsou, jak se používají a jak se vzájemně souvisí. Ukázkový scénář pro více tenantů je také znázorněn k ilustraci vztahu mezi objektem aplikace aplikace a odpovídajícími instančními objekty služby.

## <a name="application-registration"></a>Registrace aplikací
Aby bylo možné delegovat funkce pro správu identit a přístupu do služby Azure AD, musí být aplikace zaregistrovaná v [Tenantovi](developer-glossary.md#tenant)Azure AD. Při registraci aplikace ve službě Azure AD vytváříte konfiguraci identity pro vaši aplikaci, která umožňuje integraci se službou Azure AD. Při registraci aplikace v [Azure Portal][AZURE-Portal]zvolíte, zda se jedná o jediného tenanta (k dispozici pouze ve vašem tenantovi) nebo k víceklientskému přístupu (přístup v jiných klientech), a volitelně můžete nastavit identifikátor URI přesměrování (kam se přístupový token posílá).

Podrobné pokyny k registraci aplikace najdete v tématu [rychlý Start k registraci aplikace](quickstart-register-app.md).

Po dokončení registrace aplikace budete mít globálně jedinečnou instanci aplikace ( [objekt aplikace](#application-object)), která je umístěná v rámci vašeho domovského tenanta nebo adresáře.  Máte také globálně jedinečné ID vaší aplikace (ID aplikace nebo klienta).  Na portálu můžete přidat tajné klíče, certifikáty a obory, aby vaše aplikace fungovala, přizpůsobení značky vaší aplikace v dialogovém okně přihlášení a další.

Pokud zaregistrujete aplikaci na portálu, objekt aplikace a objekt instančního objektu se automaticky vytvoří v domovském tenantovi.  Pokud zaregistrujete nebo vytvoříte aplikaci pomocí rozhraní Microsoft Graph API, vytvoří se objekt instančního objektu jako samostatný krok.

## <a name="application-object"></a>Objekt aplikace
Aplikace služby Azure AD je definována jejím jedním a jediným objektem aplikace, který se nachází v tenantovi Azure AD, kde byla aplikace zaregistrovaná (označovaná jako "domovský" tenant aplikace).  Objekt aplikace se používá jako šablona nebo podrobný plán k vytvoření jednoho nebo více objektů instančního objektu.  Instanční objekt se vytvoří v každém tenantovi, kde se aplikace používá. Podobně jako u třídy v objektově orientovaném programování má objekt aplikace některé statické vlastnosti, které jsou aplikovány na všechny vytvořené instanční objekty (nebo instance aplikace).

Aplikační objekt popisuje tři aspekty aplikace: Jak může služba vystavovat tokeny pro přístup k aplikaci, prostředků, ke kterým může aplikace potřebovat přístup, a akcí, které může aplikace provádět.

Okno **Registrace aplikací** v [Azure Portal][AZURE-Portal] slouží k vypsání a správě objektů aplikace v domovském tenantovi.

![Okno Registrace aplikací](./media/app-objects-and-service-principals/app-registrations-blade.png)

Entita Microsoft Graph [aplikace][MS-Graph-App-Entity] definuje schéma pro vlastnosti objektu aplikace.

## <a name="service-principal-object"></a>Instanční objekt služby
Aby bylo možné získat přístup k prostředkům, které jsou zabezpečeny klientem služby Azure AD, musí být entita, která vyžaduje přístup, reprezentovaná objektem zabezpečení. Tento požadavek platí pro uživatele (objekt zabezpečení uživatele) i aplikace (instanční objekt). Objekt zabezpečení definuje zásady přístupu a oprávnění pro uživatele nebo aplikaci v tenantovi Azure AD. To umožňuje základní funkce, jako je ověřování uživatelů nebo aplikací během přihlašování, a autorizaci během přístupu k prostředkům.

Instanční objekt je místní reprezentace nebo instance aplikace globálního objektu aplikace v jednom tenantovi nebo adresáři. Instanční objekt je konkrétní instance vytvořená z objektu aplikace a dědí určité vlastnosti z tohoto objektu aplikace. Instanční objekt se vytvoří v každém tenantovi, kde se aplikace používá, a odkazuje na globálně jedinečný objekt aplikace.  Objekt instančního objektu definuje, co může aplikace ve skutečnosti dělat v konkrétním tenantovi, kdo má přístup k aplikaci a k jakým prostředkům může aplikace přistupovat.

Když aplikace udělí oprávnění k přístupu k prostředkům v tenantovi (při registraci nebo [souhlasu](developer-glossary.md#consent)), vytvoří se objekt instančního objektu. Můžete také vytvořit instanční objekty služby v tenantovi pomocí [Azure PowerShell](howto-authenticate-service-principal-powershell.md), [Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli), [Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http), [Azure Portal][AZURE-Portal]a dalších nástrojů. Při použití portálu se instanční objekt vytvoří automaticky při registraci aplikace.

Okno **podnikové aplikace** na portálu slouží k vypsání a správě objektů služby v tenantovi. Můžete se podívat na oprávnění instančního objektu, uživatelská oprávnění, která uživatelé provedli, kteří si ho udělali, informace o přihlášení a další.

![Okno podnikových aplikací](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

Entita Microsoft Graph [ServicePrincipal][MS-Graph-Sp-Entity] definuje schéma pro vlastnosti objektu instančního objektu.

## <a name="relationship-between-application-objects-and-service-principals"></a>Vztah mezi objekty aplikace a instančními objekty

Objekt aplikace je *globální* reprezentace vaší aplikace pro použití ve všech klientech a instanční objekt je *místní* reprezentace pro použití v konkrétním tenantovi.

Aplikační objekt slouží jako šablona, ze které se *odvozují* běžné nebo výchozí vlastnosti, které se používají při vytváření odpovídajících instančních objektů. Objekt aplikace má proto vztah 1:1 se softwarovou aplikací a 1: n relace s odpovídajícími objekty instančních služeb.

V každém tenantovi, kde se aplikace používá, se musí vytvořit instanční objekt, který mu umožní vytvořit identitu pro přihlášení a/nebo přístup k prostředkům, které klient zabezpečuje. Aplikace s jedním tenantem má jenom jeden instanční objekt (ve svém domovském tenantovi), který je vytvořený a odsouhlasený k použití při registraci aplikace. Víceklientská aplikace obsahuje taky instanční objekt vytvořený v každém tenantovi, ve kterém se uživatel z tohoto tenanta poslal k použití.

### <a name="consequences-of-modifying-and-deleting-applications"></a>Důsledky úprav a odstranění aplikací
Všechny změny, které provedete u objektu aplikace, se projeví také v hlavním objektu služby v domovském tenantovi aplikace (tenant, ve kterém byl zaregistrován). To znamená, že odstraněním objektu aplikace dojde také k odstranění jeho hlavního objektu služby klienta.  Obnovením tohoto objektu aplikace však nebude obnoven odpovídající objekt služby. U aplikací s více klienty se změny v objektu aplikace neprojeví v žádných objektech instančních objektů služby klienta, dokud se přístup neodebere přes [přístupový panel aplikace](https://myapps.microsoft.com) a znovu udělen.

## <a name="example"></a>Příklad

Následující diagram znázorňuje vztah mezi objektem aplikace aplikace a odpovídajícími instančními objekty služby v kontextu ukázkové aplikace s více klienty s názvem **aplikace HR**. V tomto ukázkovém scénáři jsou tři klienti Azure AD:

- **Adatum** – tenant používaný společností, která vyvinula aplikaci pro **HR**
- **Contoso** – tenant používaný organizací contoso, který je příjemcem **aplikace pro personální** oddělení
- **Fabrikam** – tenant používaný organizací Fabrikam, který také využívá **aplikaci pro personální** oddělení

![Vztah mezi objektem aplikace a instančním objektem služby](./media/app-objects-and-service-principals/application-objects-relationship.svg)

V tomto ukázkovém scénáři:

| Krok | Description |
|------|-------------|
| 1    | Je proces vytváření aplikací a objektů zabezpečení služby v domovském tenantovi aplikace. |
| 2    | Když správci společnosti Contoso a Fabrikam dokončí souhlas, vytvoří se v tenantovi služby Azure AD ve své společnosti objekt instančního objektu a přiřadí se mu oprávnění udělená správcem. Všimněte si také, že aplikace pro personální oddělení může být nakonfigurovaná/navržená tak, aby umožňovala souhlas uživatelům při individuálním použití. |
| 3    | Klienti spotřebitelů aplikace pro personální oddělení (Contoso a Fabrikam) mají vlastní objekt instančního objektu. Každý představuje použití instance aplikace za běhu, řídí se oprávněním, která souhlasují příslušný správce. |

## <a name="next-steps"></a>Další kroky

- K dotazování aplikace i instančních objektů služby můžete použít [průzkumníka Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer) .
- K aplikačnímu objektu aplikace můžete přistupovat pomocí rozhraní Microsoft Graph API, editoru manifestu aplikace [Azure Portal][AZURE-Portal] nebo [rutin služby Azure AD PowerShell](/powershell/azure/), jak je znázorněno v [entitě aplikace][MS-Graph-App-Entity]OData.
- K instančnímu objektu aplikace můžete přistupovat pomocí rozhraní Microsoft Graph API nebo [rutin Azure AD PowerShellu](/powershell/azure/), které jsou reprezentované [entitou ServicePrincipal][MS-Graph-Sp-Entity]OData.

<!--Image references-->

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com
