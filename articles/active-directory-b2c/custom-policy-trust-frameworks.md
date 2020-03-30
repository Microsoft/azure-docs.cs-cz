---
title: Reference – architektury důvěryhodnosti ve službě Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Téma o vlastních zásadách služby Azure Active Directory B2C a rozhraní Identity Experience Framework.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ffa25eec9c4b668f428d8e8b5a780a5fe4625a2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188881"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definování architektury důvěryhodnosti pomocí architektury Azure AD B2C Identity Experience Framework

Vlastní zásady Azure Active Directory B2C (Azure AD B2C), které používají rozhraní Identity Experience Framework, poskytují vaší organizaci centralizovanou službu. Tato služba snižuje složitost federace identit ve velké komunitě zájmu. Složitost je snížena na jeden vztah důvěryhodnosti a jednu výměnu metadat.

Vlastní zásady Azure AD B2C používají rozhraní Identity Experience Framework, které vám umožní odpovědět na následující otázky:

- Jaké jsou právní zásady, zásady zabezpečení, ochrany osobních údajů a ochrany osobních údajů, které je třeba dodržovat?
- Kdo jsou kontakty a jaké jsou procesy pro to, aby se stal akreditovaným účastníkem?
- Kdo jsou akreditovaní poskytovatelé informací o identitě (také známí jako "poskytovatelé deklarací identity") a co nabízejí?
- Kdo jsou akreditované předávající strany (a volitelně, co vyžadují)?
- Jaké jsou technické požadavky na interoperabilitu účastníků "na drátě"?
- Jaká jsou provozní "runtime" pravidla, která musí být vynucena pro výměnu informací o digitální identitě?

Chcete-li odpovědět na všechny tyto otázky, vlastní zásady Azure AD B2C, které používají architekturu prostředí identity, používají konstrukci architektury důvěryhodnosti (TF). Podívejme se na tuto konstrukci a to, co poskytuje.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Principy architektury důvěryhodnosti a nadace pro správu federací

Rozhraní Trust Framework je písemná specifikace zásad ochrany identity, zabezpečení, ochrany osobních údajů a ochrany dat, které musí účastníci komunity, která se zajímají, dodržovat.

Federovaná identita poskytuje základ pro dosažení záruky identity koncového uživatele v internetovém měřítku. Delegováním správy identit na třetí strany lze jednu digitální identitu pro koncového uživatele znovu použít s více předávajícími stranami.

Zajištění identity vyžaduje, aby poskytovatelé identit (IP) a poskytovatelé atributů (AtPs) dodržovali konkrétní zásady a postupy zabezpečení, ochrany osobních údajů a provozních zásad a postupů.  Pokud nemohou provádět přímé kontroly, musí předávající strany rozvíjet vztahy důvěryhodnosti s ips a atp, se kterými se rozhodnou pracovat.

S růstem počtu spotřebitelů a poskytovatelů informací o digitální identitě je obtížné pokračovat ve správě těchto vztahů důvěryhodnosti nebo dokonce v případě výměny technických metadat, která jsou vyžadována pro připojení k síti.  Federační centra dosáhla při řešení těchto problémů jen omezeného úspěchu.

### <a name="what-a-trust-framework-specification-defines"></a>Co definuje specifikace architektury důvěryhodnosti
Zprostředkovatele spolehlivosti jsou základními pilíři modelu Open Identity Exchange (OIX) Trust Framework, kde se každá zájmová komunita řídí konkrétní specifikací TF. Tato specifikace TF definuje:

- **Metriky zabezpečení a ochrany osobních údajů pro zájmovou komunitu s definicí:**
    - Úrovně záruky (LOA), které účastníci nabízejí/požadují; například uspořádaná sada hodnocení spolehlivosti pro pravost informací o digitální identitě.
    - Úrovně ochrany (LOP), které jsou nabízeny / požadované účastníky; například uspořádaná sada hodnocení spolehlivosti pro ochranu informací o digitální identitě, kterou zpracovávají účastníci komunity zájmu.

- **Popis informací o digitální identitě, které účastníci nabízejí/požadují**.

- **Technické politiky pro výrobu a spotřebu informací o digitální identitě, a tedy pro měření LOA a LOP. Tyto písemné zásady obvykle zahrnují následující kategorie zásad:**
    - Zásady kontroly identity, například: *Jak silně jsou informace o totožnosti osoby prověřeny?*
    - Zásady zabezpečení, například: *Jak silně jsou chráněna integrita a důvěrnost informací?*
    - Zásady ochrany osobních údajů, například: *Jakou kontrolu má uživatel nad osobními identifikovatelnými údaji ?*
    - Zásady přežití, například: *Pokud poskytovatel ukončí provoz, jak funguje kontinuita a ochrana pii?*

- **Technické profily pro výrobu a spotřebu informací o digitální identitě. Mezi tyto profily patří:**
    - Rozhraní oboru, pro které jsou k dispozici informace o digitální identitě na určenélo LOA.
    - Technické požadavky na interoperabilitu na lince.

- **Popisy různých rolí, které mohou účastníci v komunitě vykonávat, a kvalifikace, které jsou nutné ke splnění těchto rolí.**

Specifikace TF tedy určuje, jak se vyměňují informace o identitě mezi účastníky komunity zájmu: předávajícími stranami, poskytovateli identit a atributů a ověřovatelé atributů.

Specifikace TF je jeden nebo více dokumentů, které slouží jako odkaz pro řízení komunity zájmu, která reguluje tvrzení a spotřebu informací o digitální identitě v rámci komunity. Jedná se o zdokumentovanou sadu zásad a postupů určených k navázání důvěry v digitální identity, které se používají pro online transakce mezi členy zájmové komunity.

Jinými slovy specifikace TF definuje pravidla pro vytvoření životaschopného ekosystému federované identity pro komunitu.

V současné době panuje všeobecná shoda o výhodách takového přístupu. Není pochyb o tom, že specifikace rámce důvěry usnadňují rozvoj ekosystémů digitální identity s ověřitelnými charakteristikami zabezpečení, záruky a ochrany osobních údajů, což znamená, že mohou být znovu použity v různých komunitách zájmu.

Z tohoto důvodu vlastní zásady Azure AD B2C, které používají rozhraní Identity Experience Framework používá specifikace jako základ jeho reprezentace dat pro TF k usnadnění interoperability.

Vlastní zásady Azure AD B2C, které využívají rozhraní Identity Experience Framework, představují specifikaci TF jako kombinaci lidských a strojově čitelných dat. Některé části tohoto modelu (obvykle oddíly, které jsou více orientované na zásady správného řízení) jsou reprezentovány jako odkazy na publikovanou dokumentaci zásad zabezpečení a ochrany osobních údajů spolu se souvisejícími postupy (pokud existují). Další části podrobně popisují metadata konfigurace a pravidla modulu runtime, která usnadňují provozní automatizaci.

## <a name="understand-trust-framework-policies"></a>Principy zásad architektury důvěryhodnosti

Pokud jde o implementaci, specifikace TF se skládá ze sady zásad, které umožňují úplnou kontrolu nad chování identity a prostředí.  Vlastní zásady Azure AD B2C, které využívají rozhraní Identity Experience Framework, umožňují vytvářet a vytvářet vlastní TF prostřednictvím takových deklarativních zásad, které můžou definovat a konfigurovat:

- Odkaz na dokument nebo odkazy, které definují federovaný ekosystém identity komunity, která se vztahuje k TF. Jedná se o odkazy na dokumentaci TF. (Předdefinovaná) provozní pravidla "runtime" nebo cesty uživatele, které automatizují nebo řídí výměnu a používání deklarací. Tyto cesty uživatele jsou spojeny s LOA (a LOP). Zásada proto může mít cesty uživatele s různými loa (a LOPs).

- Poskytovatelé identit a atributů nebo poskytovatelé deklarací identity v zájmové masivu a technické profily, které podporují, spolu s akreditací LOA/LOP (mimo pásmo), která se jich týká.

- Integrace s ověřovatelé atributů nebo zprostředkovatelé deklarací identity.

- Předávající strany v komunitě (odvozením).

- Metadata pro navázání síťové komunikace mezi účastníky. Tato metadata, spolu s technickými profily, se používají během transakce plumb "na drátě" interoperability mezi předávající strany a ostatní účastníci komunity.

- Převod protokolu, pokud existuje (například SAML 2.0, OAuth2, WS-Federation a OpenID Connect).

- Požadavky na ověření.

- Multifaktororchestrace pokud existuje.

- Sdílené schéma pro všechny deklarace identity, které jsou k dispozici, a mapování pro účastníky komunity zájmu.

- Všechny transformace deklarací identity, spolu s možnou minimalizací dat v tomto kontextu, k udržení výměny a používání deklarací.

- Vazba a šifrování.

- Úložiště reklamací.

### <a name="understand-claims"></a>Principy nároků

> [!NOTE]
> Společně odkazujeme na všechny možné typy informací o identitě, které mohou být vyměněny jako "nároky": tvrzení o ověřovacích pověřeních koncového uživatele, ověření identity, komunikační zařízení, fyzické umístění, osobní identifikační atributy, a tak dále.
>
> Používáme termín "deklarace" - spíše než "atributy" - protože v online transakcích tyto artefakty dat nejsou fakta, která může být přímo ověřena předávající stranou. Spíše se jedná o tvrzení nebo tvrzení o skutečnostech, pro které musí předávající strana získat dostatečnou důvěru, aby udělila požadovanou transakci koncového uživatele.
>
> Používáme také termín "deklarace identity", protože vlastní zásady Azure AD B2C, které používají rozhraní Identity Experience Framework jsou navrženy tak, aby zjednodušily výměnu všech typů informací o digitální identitě konzistentním způsobem bez ohledu na to, zda je základní protokol pro ověřování uživatelů nebo načítání atributů.  Stejně tak používáme termín "poskytovatelé deklarací identity" ke společnému odkazu na poskytovatele identity, poskytovatele atributů a ověřovače atributů, pokud nechceme rozlišovat mezi jejich konkrétními funkcemi.

Řídí tedy způsob výměny informací o identitě mezi předávající stranou, zprostředkovateli identity a atributů a ověřovatelé atributů. Řídí, které zprostředkovatelé identita a atributjsou požadovány pro ověřování předávající strany. Měly by být považovány za jazyk specifický pro doménu (DSL), to znamená, že počítačový jazyk, který se specializuje na konkrétní aplikační domény s dědičností, *pokud* příkazy, polymorfismus.

Tyto zásady představují strojově čitelnou část konstrukce TF v zásadách Azure AD B2C Custom využívající rozhraní Identity Experience Framework. Zahrnují všechny provozní podrobnosti, včetně metadat a technických profilů zprostředkovatelů deklarací, definice schématu deklarací, funkce transformace deklarací identity a cesty uživatelů, které jsou vyplněny pro usnadnění provozní orchestrace a automatizace.

Předpokládá se, že se jedná o *živé dokumenty,* protože existuje velká šance, že se jejich obsah v průběhu času změní, pokud jde o aktivní účastníky deklarované v politikách. Existuje také možnost, že by se mohly změnit podmínky účastnického účasti.

Nastavení a údržba federace jsou výrazně zjednodušeny tím, že chrání předávající strany před probíhajícími změnami důvěryhodnosti a připojení, protože různí poskytovatelé deklarací identity/ověřovatelé se připojují nebo opouštějí (komunita reprezentovaná) sadou zásad.

Další významnou výzvou je interoperabilita. Další zprostředkovatelé deklarací identity/ověřovatelé musí být integrováni, protože předávající strany pravděpodobně nepodporují všechny potřebné protokoly. Vlastní zásady Azure AD B2C řeší tento problém podporou standardních protokolů a použitím konkrétních cest uživatelů k transponování požadavků při předávajících stranách a poskytovatelích atributů nepodporují stejný protokol.

Cesty uživatelů zahrnují profily protokolu a metadata, které se používají k olovnici interoperability "na drátě" mezi předávající stranou a ostatními účastníky. Existují také provozní pravidla runtime, která se používají pro zprávy požadavku a odpovědi pro výměnu informací o identitě pro vynucení dodržování publikovaných zásad jako součást specifikace TF. Myšlenka uživatelských cest je klíčem k přizpůsobení zákaznického prostředí. To také vrhá světlo na to, jak systém funguje na úrovni protokolu.

Na tomto základě mohou aplikace a portály předávající strany v závislosti na svém kontextu vyvolat vlastní zásady Azure AD B2C, které využívají rámec zkušeností s identitami a předávají název konkrétní zásady a přesně získají chování a výměnu informací. chtějí bez muss, povyk, nebo riziko.
