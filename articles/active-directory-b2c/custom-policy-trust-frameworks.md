---
title: Rozhraní pro vztahy důvěryhodnosti referencí v Azure Active Directory B2C | Microsoft Docs
description: Téma o Azure Active Directory B2C vlastních zásadách a prostředí identit.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d3d29bd05f67d00047499dc256e5e1a82f98693a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95990979"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definování vztahů důvěryhodnosti pomocí Azure AD B2C Framework Experience identity

Azure Active Directory B2C (Azure AD B2C) vlastní zásady, které používají architekturu prostředí identity, poskytují vaší organizaci centralizovanou službu. Tato služba snižuje složitost federace identit ve velké komunitě zájmu. Složitost se zkracuje na jeden vztah důvěryhodnosti a na jednu výměnu metadat.

Azure AD B2C vlastní zásady používají rozhraní identity Experience Framework, které vám umožní zodpovědět následující otázky:

- Jaké jsou právní a bezpečnostní zásady zabezpečení, ochrany osobních údajů a ochrany dat, které musí dodržovat?
- Kdo jsou kontakty a jaké procesy se stanou neschváleným účastníkem?
- Kdo jsou poskytovatelé informací o identitě (označované také jako poskytovatelé deklarací identity) a co nabízejí?
- Kdo jsou akreditační předávající strany (a volitelně co vyžadují)?
- Jaké jsou technické požadavky na interoperabilitu pro účastníky?
- Jaké jsou provozní pravidla "runtime", která se musí vymáhat pro výměnu informací o digitální identitě?

Pokud chcete odpovědět na všechny tyto otázky, Azure AD B2C vlastní zásady, které používají architekturu identity Experience Framework, použijte konstrukci Trust Framework (TF). Pojďme vzít v úvahu tento konstruktor a to, co poskytuje.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Pochopení architektury vztahu důvěryhodnosti a základu správy federace

Vztah důvěryhodnosti je písemná specifikace pro zásady týkající se identity, zabezpečení, ochrany osobních údajů a ochrany dat, které musí splňovat účastníci ve komunitě zájmu.

Federované identity poskytuje základ pro zajištění identity identity koncových uživatelů v internetovém měřítku. Delegováním správy identit na třetí strany se dá použít jedna Digitální identita pro koncového uživatele s více předávajícími stranami.

Identity Assurance vyžaduje, aby zprostředkovatelé identity (zprostředkovatelů identity) a zprostředkovatelé atributů (AtPs) dodržovali konkrétní zabezpečení, ochranu osobních údajů a provozní zásady a postupy.  Pokud nemohou provádět přímé kontroly, musí předávající strany (RPs) vyvíjet vztahy důvěryhodnosti s zprostředkovatelů identity a AtPs, se kterými se uživatelé rozhodnou pracovat.

Vzhledem k tomu, že se počet spotřebitelů a poskytovatelé informací o digitální identitě roste, je obtížné pokračovat ve správě těchto vztahů důvěryhodnosti, nebo dokonce na základě výměny technických metadat, která jsou potřebná pro připojení k síti.  Federační centra se dosáhlo pouze omezené úspěšnosti při řešení těchto problémů.

### <a name="what-a-trust-framework-specification-defines"></a>Definice specifikace architektury pro vztahy důvěryhodnosti
TFs jsou linchpins modelu rozhraní OIX (Open identity Exchange) pro vztah důvěryhodnosti, kde se každá komunita zájmů řídí konkrétní specifikací TF. Taková specifikace TF definuje:

- **Metrika zabezpečení a ochrany osobních údajů v komunitě zájmu s definicí:**
    - Úrovně Assurance (LOA), které jsou nabídnuty nebo požadovány účastníky; například uspořádaná sada hodnocení spolehlivosti pro pravost informací o digitální identitě.
    - Úrovně ochrany (LOP), které jsou nabídnuty nebo požadovány účastníky; například uspořádaná sada hodnocení spolehlivosti pro ochranu informací o digitální identitě, která je pořízena účastníky v komunitě zájmu.

- **Popis informací o digitální identitě, které jsou nabídnuty nebo požadovány účastníky**.

- **Technické zásady pro produkci a spotřebu informací o digitální identitě, a to pro měření LOA a LOP. Tyto napsané zásady obvykle obsahují následující kategorie zásad:**
    - Zásady pro kontrolu identity, například: *jak silně je prověřené informace o identitě uživatele?*
    - Zásady zabezpečení, například: *jak silné jsou integrita informací a ochrana důvěrnosti?*
    - Zásady ochrany osobních údajů, například: *jaký ovládací prvek má uživatel více identifikovatelné osobní údaje (PII)*?
    - Zásady přežití, například: *Pokud poskytovatel přestává operace, jak funguje kontinuita a ochrana funkce PII?*

- **Technické profily pro produkci a spotřebu informací o digitální identitě. Mezi tyto profily patří:**
    - Rozhraní oboru, pro které jsou k dispozici informace o digitální identitě v zadaném LOA.
    - Technické požadavky na interoperabilitu s přenosem.

- **Popisy různých rolí, které mohou účastníci komunity provádět, a kvalifikace, které jsou nutné ke splnění těchto rolí.**

Proto specifikace TF určuje, jak se vyměňují informace o identitě mezi účastníky komunity zájmu: předávající strany, poskytovatelé identit a atributů a ověřovatele atributů.

Specifikace TF je jeden nebo několik dokumentů, které slouží jako reference pro zásady správného řízení komunity zájmu, které řídí kontrolní výraz a spotřebu informací o digitální identitě v rámci komunity. Jedná se o popsanou sadu zásad a postupů, které jsou určené k navázání vztahu důvěryhodnosti v digitálních identitách, které se používají pro online transakce mezi členy komunity zájmu.

Jinými slovy specifikace TF definuje pravidla pro vytvoření životaschopného ekosystému federované identity pro komunitu.

V současné době existuje širší dohoda o výhodách takového přístupu. Neexistují žádné pochybnosti, že specifikace architektury pro vztah důvěryhodnosti usnadňují vývoj digitálních ekosystémů identity pomocí ověřitelných vlastností zabezpečení, záruky a ochrany osobních údajů, což znamená, že je možné je znovu použít napříč několika komunitami, které vás zajímají.

Z tohoto důvodu Azure AD B2C vlastní zásady, které používají architekturu identity Experience Framework, k usnadnění interoperability slouží jako základ své reprezentace dat pro TF.

Azure AD B2C vlastní zásady, které využívají architekturu prostředí identity, reprezentují specifikaci TF jako kombinaci lidského a strojově čitelného data. Některé části tohoto modelu (obvykle oddíly, které se orientují směrem k zásadám správného řízení) jsou reprezentovány jako odkazy na publikovanou dokumentaci o zabezpečení a zásadách ochrany osobních údajů spolu se souvisejícími postupy (pokud existují). Ostatní oddíly popisují podrobně metadata konfigurace a pravidla modulu runtime, která usnadňují provozní automatizaci.

## <a name="understand-trust-framework-policies"></a>Principy zásad pro rozhraní Trust Framework

V souvislosti s implementací TF se specifikace TF skládá ze sady zásad, které umožňují úplnou kontrolu nad chováním identity a prostředími.  Azure AD B2C vlastní zásady, které využívají architekturu prostředí identit, umožňují vytvářet a vytvářet vlastní TF prostřednictvím takových deklarativních zásad, které můžou definovat a konfigurovat:

- Odkaz na dokument nebo odkazy definující ekosystém federované identity komunity, který se vztahuje k TF. Jsou odkazy na dokumentaci TF. (Předdefinovaná) Provozní pravidla "runtime" nebo uživatelské cesty, které automatizují nebo řídí výměnu a používání deklarací identity. Tyto cesty uživatelů jsou přidruženy k LOA (a LOP). Zásada může mít proto cestu uživatele s různou LOAsou (a LOPs).

- Poskytovatelé identit a atributů nebo poskytovatelé deklarací identity v komunitě zájmu a technické profily, které podporují, spolu s LOP schválením na základě kategorie (mimo pásmo), která souvisí s nimi.

- Integrace s ověřovateli atributů nebo zprostředkovateli deklarací identity.

- Předávající strany ve Společenství (odvozením).

- Metadata pro vytvoření síťové komunikace mezi účastníky. Tato metadata spolu s technickými profily se používají během transakce na domovní spolupráci mezi předávající stranou a ostatními účastníky komunity.

- Převod protokolu, pokud existuje (například SAML 2,0, OAuth2, WS-Federation a OpenID Connect).

- Požadavky na ověřování.

- Orchestrace s více fakty, pokud nějaká existuje.

- Sdílené schéma pro všechny deklarace identity, které jsou k dispozici, a mapování účastníků zájmu komunity.

- Všechny transformace deklarací identity společně s možnou minimalizováním dat v tomto kontextu začlení výměnu a používání deklarací identity.

- Vazba a šifrování.

- Úložiště deklarací identity.

### <a name="understand-claims"></a>Pochopení deklarací identity

> [!NOTE]
> Souhrnně odkazujeme na všechny možné typy informací o identitách, které se můžou vyměňovat jako deklarace identity: deklarace identity pro přihlašovací údaje koncového uživatele, dozvíte ČSFD identity, komunikační zařízení, fyzické umístění, identifikovatelné atributy atd.
>
> Používáme pojem "deklarace identity", a ne "atributy" – protože v online transakcích nejsou tyto artefakty dat fakty, které je možné přímo ověřit předávající stranou. Jsou to ale kontrolní výrazy nebo deklarace identity. informace o skutečnostech, pro které předávající strana musí vyvíjet dostatečnou důvěru pro udělení požadované transakce koncovému uživateli.
>
> Používáme také pojem "deklarace identity", protože Azure AD B2C vlastní zásady, které používají architekturu prostředí identity, jsou navržené tak, aby se zjednodušila Výměna všech typů informací o digitální identitě konzistentním způsobem bez ohledu na to, zda je příslušný protokol definován pro ověřování uživatelů nebo pro načtení atributů.  Podobně používáme pojem "zprostředkovatelé deklarací identity" ke shromáždění informací o zprostředkovatelích identity, poskytovatelích atributů a ověřovatelích atributů, když nechcete rozlišovat mezi jejich konkrétními funkcemi.

Proto určují, jak se vyměňují informace o identitě mezi předávající stranou, zprostředkovatelem identity a atributů a ověřovateli atributů. Řídí, která zprostředkovatelé identity a atributů se vyžadují pro ověřování předávající strany. Měly by se brát v úvahu jako jazyk specifického pro doménu (DSL), což je jazyk počítače, který je specializovaný na určitou doménu aplikace s děděním, *Pokud* se jedná o příkazy, polymorfismus.

Tyto zásady představují strojově čitelnou část konstruktoru TF v Azure AD B2C vlastní zásady využívající architekturu prostředí identity. Zahrnují všechny provozní údaje, včetně metadat zprostředkovatelů deklarací identity a technických profilů, definic schémat deklarací identity, transformačních funkcí deklarací identity a cest uživatelů, které jsou vyplněny k usnadnění provozní Orchestrace a automatizace.

Předpokládá se, že se jedná o *živý dokument* , protože je velmi pravděpodobné, že se jejich obsah v průběhu času týká aktivních účastníků deklarovaných v zásadách. Je také možné, že se podmínky a ujednání pro účastníka mohou změnit.

Nastavení a údržba federace se značně zjednodušují ochranou předávající strany od probíhajících konfigurací vztahů důvěryhodnosti a připojení, protože různí poskytovatelé deklarací identity a Ověřovač se připojují nebo nechávají (komunita reprezentovaná) sadou zásad.

Interoperabilita je další důležitou výzvou. Další zprostředkovatelé nebo ověřovatele deklarací identity musí být integrováni, protože předávající strany pravděpodobně nebudou podporovat všechny potřebné protokoly. Azure AD B2C vlastní zásady tento problém řeší podporou standardních protokolů a použitím konkrétních cest uživatelů k předávání požadavků, když předávající strany a zprostředkovatelé atributů nepodporují stejný protokol.

Mezi cesty uživatelů patří profily protokolu a metadata, která se používají na domovní spolupráci mezi předávající stranou a dalšími účastníky. K dispozici jsou také pravidla provozního běhu, která se používají pro zprávy žádosti a odpovědi výměny informací o identitě pro vynucování dodržování zásad publikovaných v rámci specifikace TF. Nápad uživatelských cest je klíč k přizpůsobení prostředí pro zákazníky. Také se tím uvolňuje, jak systém funguje na úrovni protokolu.

Na tomto základě můžou aplikace a portály předávající strany v závislosti na jejich kontextu vyvolat Azure AD B2C vlastní zásady, které využívají architekturu identity Experience Framework, předávají název konkrétní zásady a přesně vyměňují chování a informace, které chtějí, bez jakéhokoli muss, Fuss nebo rizika.
