---
title: Referenční dokumentace - rozhraní vztah důvěryhodnosti v Azure Active Directory B2C | Microsoft Docs
description: Téma o vlastní zásady Azure Active Directory B2C a rozhraní prostředí Identity.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b75efe7464c32863781353549f73048b4e127ddf
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34710215"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definování rozhraní vztah důvěryhodnosti s Azure AD B2C Identity rozhraní Framework

Azure Active Directory B2C (Azure AD B2C) vlastní zásady, které používají rozhraní prostředí Identity zadejte vaše organizace centralizované službou. Tato služba snižuje složitost federaci identit ve velkých komunitě zájmu. Představuje jeden vztah důvěryhodnosti a exchange jednom metadata se snižuje složitost.

Azure AD B2C vlastní zásady, které používají rozhraní prostředí Identity umožnit odpovězte si na následující otázky:

- Jaké jsou právní, zabezpečení, ochrany osobních údajů a zásady ochrany dat, které musí být použito?
- Kdo jsou kontakty a jaké jsou procesy pro stal akreditované účastník?
- Kdo jsou informace o poskytovateli akreditované identity (také označované jako "zprostředkovatelů deklarací identity") a co dělat nabízejí?
- Kdo jsou akreditované předávající strany (a volitelně, co bude potřebovat)?
- Jaké jsou technická "přenášený" požadavky vzájemná funkční spolupráce pro účastníky?
- Co jsou pravidla provozní "runtime", které musí být vynucuje pro výměnu informací o identitě digitální?

K odpovědi na tyto otázky, vytvořit vlastní zásady Azure AD B2C, které používají identitu rozhraní Framework použití vztahu důvěryhodnosti Framework (TF). Pojďme se tato konstrukce a co nabízí.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Pochopení management foundation důvěřovat Framework a federation

Rozhraní vztahu důvěryhodnosti je zapsaný Specifikace identity, zabezpečení, ochrany osobních údajů a zásady ochrany dat, ke kterým musí odpovídat účastníky v komunitě zájmu.

Federovaných identit poskytuje základ pro dosažení záruku identity koncového uživatele v Internetu měřítku. Delegováním identity management třetím stranám, lze opětovně použít jedinou digitální identitu pro koncového uživatele s víc předávajících stran.  

Záruku identity vyžaduje, aby zprostředkovatelů identity (IdPs) a poskytovatelé atribut (AtPs) dodržovat konkrétní zabezpečení, ochrany osobních údajů a provozní zásady a postupy.  Pokud nemůžou provést přímý kontrol, předávající strany (RPs) musí vyvíjet vztahy důvěryhodnosti s IdPs a AtPs se rozhodnete pracovat.  

S růstem počet příjemci a zprostředkovatelé identity digitálních informací je obtížné pokračovat pairwise správu tyto vztahy důvěryhodnosti nebo i pairwise exchange technické metadata, která vyžaduje pro připojení k síti.  Federační centra jste dosáhli jenom omezené úspěch v řešení těchto problémů.

### <a name="what-a-trust-framework-specification-defines"></a>Definuje jaké specifikaci důvěřovat Framework
Sady TFs jsou linchpins modelu Framework důvěřovat otevřete Identity Exchange (OIX), kde každý community týkající se řídí konkrétní TF specifikací. Definuje TF specifikace:

- **Zabezpečení a ochrana osobních údajů metriky pro komunity týkající se definice:**
    - Úrovně záruky (LOA), které jsou nabízeny nebo vyžadují účastníky; například uspořádanou sadu hodnocení spolehlivosti pravdivosti digitální identitu informace.
    - Úrovně ochrany (LOP), které jsou nabízeny nebo vyžadují účastníky; například uspořádanou sadu hodnocení spolehlivosti pro ochranu informací digitální identitu, kterou provádí služba účastníky v komunitě zájmu.

- **Popis digitální identitu informace, které má nabízí požadované podle účastníky**.

- **Technické zásady pro produkční a spotřeba informací digitální identitu a proto pro měření LOA a LOP. Tyto zásady napsané obvykle obsahují následující kategorie zásady:**
    - Identity kontroly pravopisu systému zásady, například: *jak důrazně se informace o identitě osoby vetted?*
    - Zásady zabezpečení, například: *jak důrazně jsou informace integrity a důvěrnosti chráněné?*
    - Zásady ochrany osobních údajů, například: *co ovládací prvek uživatel mít přes osobní identifikovatelné údaje (PII)*?
    - Funkční schopnost zásady, například: *zprostředkovatele přestane operací, jak funguje kontinuitu a ochranu PII funkce?*

- **Technické profily pro produkční a spotřeba digitální identitu informací. Tyto profily zahrnují:**
    - Obor rozhraní, pro které je k dispozici na zadaný LOA digitální identitu informace.
    - Technické požadavky na interoperabilitu ve přenosu.

- **Popis různých rolích, které může provádět účastníky v komunitě a kvalifikace, které jsou nutné ke splnění těchto rolí.**

Proto specifikaci TF řídí výměna informací o identitě mezi jednotlivými účastníky komunity, které vás zajímají: předávající strany, identity a poskytovatelé atribut a atribut ověřovatele.

Specifikace TF je jeden nebo více dokumentů, které slouží jako odkaz pro zásady správného řízení community týkající se některá z kontrolní výraz a spotřeba digitální identitu informací v komunitě. Je sadu zdokumentovaných zásady a postupy určené k navázání vztahu důvěryhodnosti v digitální identity, které se používají pro online transakce mezi členy komunity zájmu.  

Jinými slovy specifikaci TF definuje pravidla pro vytváření ekosystém přijatelná federovaných identit pro komunitu.

Aktuálně je rozšířeným smlouvy využívat takový přístup. Je už nepochybně, specifikace framework usnadňují vývoj digitální identitu ekosystémů s ověřitelná charakteristikami zabezpečení, záruky a ochrany osobních údajů, což znamená, že budoucí napříč více komunit týkající se vztah důvěryhodnosti.

Pro tento důvod, Azure AD B2C vlastní zásady, které používají rozhraní Identity prostředí používá specifikace jako základ pro jeho znázornění dat pro TF usnadňuje interoperabilitu.  

Azure AD B2C vlastní zásady, které využívají rozhraní prostředí Identity představují specifikaci TF jako směs lidské a strojově čitelným data. Některé části tohoto modelu (obvykle oddíly, které jsou více orientované na zásad správného řízení) jsou reprezentovány jako odkazy na publikované zabezpečení a ochrana osobních údajů dokumentaci k zásadám společně s postupy související (pokud existuje). Ostatní části podrobně popisují konfigurace metadata a runtime pravidla, která usnadňují provozní automatizace.

## <a name="understand-trust-framework-policies"></a>Pochopit zásady důvěryhodnosti Framework

Z hlediska implementace specifikace TF se skládá ze sady zásad, které umožňují úplnou kontrolu nad chováním identity a prostředí.  Azure vlastní zásady AD B2C, které využívají rozhraní prostředí Identity umožňují vytvářet a vytvořit vlastní TF prostřednictvím takových deklarativní zásad, které můžete definovat a nakonfigurovat:

- Odkaz na dokument nebo odkazy, které definují ekosystému federovaných identit komunity, která má vztah k TF. Jsou odkazy na dokumentaci TF. Pravidla provozní (předdefinovaná) "runtime", nebo uživatel cesty, které automatizují nebo řízení exchange a použití deklarací identity. Tyto cesty uživatele jsou přidruženy LOA (a LOP). Zásady můžete mít proto cesty uživatele s různými LOAs (a LOPs).

- Zprostředkovatelé identity a atribut, nebo zprostředkovatele deklarací identity, v komunitě zájmu a technické profily, které podporují společně s schválení LOA/LOP (out-of-band), která má vztah k nim.

- Integrace s atribut ověřovatele nebo poskytovatelů deklarací identity.

- Předávající strany v komunitě (podle odvození).

- Metadata pro zřízení síťové komunikace mezi účastníky. Tato metadata, společně s technické profily se používají během transakce pro vložení "přenášený" vzájemná funkční spolupráce mezi předávající strany a ostatní účastníci komunity.

- Převod protokol případných (například SAML, OAuth2, WS-Federation a OpenID Connect).

- Požadavky na ověřování.

- Vícefaktorového orchestration pokud existuje.

- Sdílené schématu pro všechny deklarace identity, které jsou k dispozici a mapování účastníkům komunity zájmu.

- Všechny deklarace identity transformace, společně s minimalizace možné data v tomto kontextu tolerovat exchange a použití deklarací identity.

- Vazba a šifrování.

- Deklarace identity úložiště.

### <a name="understand-claims"></a>Pochopení deklarace identity

> [!NOTE]
> Souhrnně označovány všechny možné typy informací o identitě, který může být vyměňují jako "deklarace identity": deklarace identity o koncový uživatel ověřování přihlašovacích údajů, identity prověřování komunikace zařízení, fyzického umístění, osobní identifikaci atributů a tak dále.  
>
> Protože online transakcí, nejsou tyto artefakty dat faktů, které jsou přímo ověřovány předávající strany používáme termín "deklarace" – místo "atributy". Místo jsou kontrolní výrazy nebo deklarace identity o faktů, pro které musí předávající strany vyvíjet dostatečnou důvěru k udělení požadovaný transakce koncového uživatele.  
>
> Také používáme termín "deklarace identity", protože Azure AD B2C vlastní zásady, které používají rozhraní prostředí Identity jsou navrženy pro zjednodušení exchange všech typů informací digitální identitu konzistentním způsobem bez ohledu na to, jestli je definována základní protokol pro ověřování uživatelů nebo načítání atributu.  Podobně používáme termín "zprostředkovatelů deklarací identity" souhrnně označují poskytovatelů identit, zprostředkovatelé atribut a atribut ověřovatele, když jsme nechcete, aby k rozlišení mezi vlastní specifické funkce.   

Proto se řídí výměna informací o identitě mezi předávající strany, identity a poskytovatelé atribut a atribut ověřovatele. Zprostředkovatelé atribut je požadován pro ověřování předávající strany a tím i určovat, které identity. Se mají považovat jako jazyk specifické pro doménu (DSL), který je jazyk počítače, který se specializuje na určité domény aplikace s použitím dědičnosti, *Pokud* příkazy, polymorfismus.

Tyto zásady určují strojově čitelným část TF konstrukce v Azure AD B2C vlastní zásady využití rozhraní prostředí Identity. Patří mezi ně všechny provozní údaje, včetně metadat zprostředkovatelů deklarací identit a technické profily, definice schémat deklarace identity, funkce transformace deklarací identity a cesty uživatele, které jsou vyplněny v usnadňuje provozní orchestration a automatizace.  

Se považuje za *životností dokumenty* vzhledem k tomu, že je dobré pravděpodobné, že jejich obsah změní přes čas týkající se aktivní účastníky deklarované v zásadách. Je také možností podmínky a ujednání pro účastník, který se může změnit.  

Federační nastavování a údržbu jsou významně zjednodušit tím, jak různé deklarace zprostředkovatelé/ověřovatele připojení nebo nechte (komunitou reprezentována) stínění předávající strany z probíhající vztah důvěryhodnosti a připojení že změny konfigurace sady zásad.

Vzájemná funkční spolupráce je jiné významné výzvu. Další deklarace identity zprostředkovatelé/ověřovatele musí být integrován, protože je nepravděpodobné, že pro podporu všechny nezbytné protokoly předávající strany. Tento problém vyřešit vlastní zásady služby Azure AD B2C podporuje standardní protokoly a použití konkrétního uživatele cesty přejímající požadavky, když předávající strany a poskytovatelé atribut nepodporují stejný protokol.  

Cesty uživatele patří protokol profily a metadata, která slouží k vložení "přenášený" vzájemná funkční spolupráce mezi ostatní účastníci a předávající strany. Existují také provozní runtime pravidla, která se použijí u zprávy s informacemi o exchange požadavek a odpověď identity pro vynucování souladu se zásadami publikované jako součást specifikace TF. Představu o cesty uživatele je klíčová pro přizpůsobení prostředí zákazníků. Je také osvětluje fungování systému na úrovni protokolu.

Na základě předávající strany aplikací a portály můžete, v závislosti na jejich kontextu vyvolat Azure AD B2C vlastní zásady, které využívají rozhraní prostředí Identity předávání název konkrétní zásady a získat přesněji chování a informace o exchange, které chtějí bez jakýchkoli muss, fuss nebo riziko.
