---
title: Reference – důvěryhodnosti architektury v Azure Active Directory B2C | Dokumentace Microsoftu
description: Téma o vlastní zásady pro Azure Active Directory B2C a architekturu rozhraní identit.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 694d463c158f8c8beb0396edf22ac749c03e9e97
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54845686"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definice architektury důvěryhodnosti s architekturu rozhraní identit Azure AD B2C

Azure Active Directory B2C (Azure AD B2C) vlastní zásady, které používají architekturu rozhraní identit poskytnout vaší organizaci s centralizované služby. Tato služba pro snížení složitosti federaci identit velké komunity zájmu. Představuje jeden vztah důvěryhodnosti a jeden metadata exchange se snižuje složitost.

Azure AD B2C vlastní zásady, které používají architekturu rozhraní identit umožňuje odpovědět na následující otázky:

- Co je právní, zabezpečení, ochrany osobních údajů a zásady ochrany dat, které musí dodržovat?
- Kdo jsou kontakty a co jsou procesy pro stát účastníkovi akreditované?
- Informace o zprostředkovatelé identity akreditované (označované také jako "zprostředkovatelů deklarací identity"), kteří jsou a k čemu nabízejí?
- Kdo jsou akreditované předávající strany (a volitelně, co bude potřebovat)?
- Co jsou technické "na lince" požadavky vzájemná funkční spolupráce pro účastníky?
- Co jsou pravidla provozní "runtime", které musí vynucovat k výměně informací o digitální identity?

Odpovědi na tyto otázky, vytvořit vlastní zásady Azure AD B2C, které používají architekturu rozhraní identit použijte důvěřovat Framework (TF). Pojďme se podívat tento konstruktor a poskytuje.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Vysvětlení právech důvěřovat rozhraní Framework a federace

Rozhraní vztahu důvěryhodnosti je písemné specifikace identit, zabezpečení, ochrany osobních údajů a zásady ochrany dat, ke kterým musí odpovídat účastníků v komunitě zajímavé.

Federované identity poskytuje základ pro dosažení assurance identitu koncového uživatele v internetovém měřítku. Delegováním identity management třetím stranám, lze opětovně použít jednu digitální identitu koncového uživatele s víc předávajících stran.  

Identita assurance vyžaduje zprostředkovatelů identity (IDP) a poskytovatelé atribut (AtPs), které se dodržovat konkrétní zabezpečení, ochrany osobních údajů a zkontrolovala zásady a postupy.  Pokud nemohou provádět kontroly s přímým přístupem, předávající strany (předávajících stran) musí vyvinout vztahy důvěryhodnosti pomocí zprostředkovatelů identity a AtPs se rozhodnete pracovat.  

Roste počet příjemci a zprostředkovatelé identity digitálních informací, je obtížné pokračovat pairwise správu tyto vztahy důvěryhodnosti nebo dokonce pairwise exchange technické metadata potřebná pro připojení k síti.  Federace hubs dosáhli pouze omezené úspěch při řešení těchto problémů.

### <a name="what-a-trust-framework-specification-defines"></a>Definuje vztah důvěryhodnosti Framework specifikace
TFs jsou linchpins modelu rozhraní Framework důvěřovat otevřít Identity Exchange (OIX), kde je každý komunitě zajímavé řídí konkrétní specifikace TF. Definuje TF specifikace:

- **Zabezpečení a ochrana osobních údajů metrik pro komunitu zajímají s definicí:**
    - Úrovně, které jsou požadované účastníci; jejich nabízí záruky (Symbolů) například uspořádané sady hodnocení spolehlivosti za pravosti digitální identity informace.
    - Úrovně ochrany (LOP), které jsou nabízí/vyžadované účastníků například uspořádané sady hodnocení spolehlivosti pro ochranu informací digitální identity, který zařizuje služba účastníků v komunitě zajímavé.

- **Popis digitálních informací o identitě, který má nabízí/vyžadováno účastníci**.

- **Technické zásady pro produkci a využití digitálních identit a proto pro měření Symbolů a LOP. Tyto zásady písemné obvykle zahrnují následující kategorie zásad:**
    - Identita kontroly pravopisu zásady, třeba: *Jak důrazně se informace o identitě osoby prověřeny?*
    - Zásady zabezpečení, například: *Jak důrazně jsou chráněné informace integrity a důvěrnosti?*
    - Zásady ochrany osobních údajů, například: *Jaké ovládacího prvku uživatel nemá přes osobní identifikovatelné údaje (PII)*?
    - Přežití zásady, například: *Pokud zprostředkovatele přestane operace, jak funguje kontinuity podnikových procesů a ochranu funkce identifikovatelné osobní údaje?*

- **Technické profily pro produkci a využití digitálních identit. Tyto profily zahrnují:**
    - Obor rozhraní, pro které je k dispozici na zadané Symbolů digitální identity informace.
    - Technické požadavky pro spolupráci ve přenosu.

- **Popis různých rolích, které mohou provádět členové komunity a podmínky, které jsou nezbytné pro splnění těchto rolí.**

Proto TF specifikace řídí, jak informace o identitě se vyměňují mezi jednotlivými účastníky komunity, které vás zajímají: předávající strany, identity a poskytovatelé atribut a atribut ověřovatele.

Specifikace TF je jeden nebo více dokumentů, které slouží jako odkaz pro zásady správného řízení komunitě zajímavé, který řídí kontrolního výrazu a využití digitální informace o identitě v rámci komunity. Je zdokumentovaná sadu zásad a postupů, které jsou navržené tak, aby navázání vztahu důvěryhodnosti v digitálních identit, které se používají pro online transakce mezi členy komunity, které vás zajímají.  

Jinými slovy TF specifikace definuje pravidla pro vytváření ekosystému přijatelné federovaných identit pro komunity.

Aktuálně je široce smlouvy využívat takový přístup. Je už nepochybně, který důvěryhodnosti specifikace rozhraní framework usnadňují vývoj ekosystémů digitální identity s ověřitelný vlastnosti zabezpečení, zabezpečení a ochrana osobních údajů, což znamená, že můžete opakovaně napříč více komunit, které vás zajímají.

Z důvodu vlastní zásady Azure AD B2C, které používají architekturu rozhraní identit pro, který používá specifikaci jako základ pro její znázornění dat TF pro usnadnění vzájemná funkční spolupráce.  

Azure AD B2C vlastní zásady, které využívají architekturu rozhraní identit představují TF specifikace jako kombinace modifikátorů dat, lidské a který je strojově čitelný. Některé části tohoto modelu (obvykle oddíly, které jsou více orientované na zásady správného řízení) jsou reprezentovány ve formě odkazů na publikované zabezpečení a ochrana osobních údajů dokumentace k zásadám spolu s postupy související (pokud existuje). Ostatní části podrobně popisují konfiguraci metadata a modulu runtime pravidla, která usnadňují automatizaci provozní.

## <a name="understand-trust-framework-policies"></a>Vysvětlení zásad důvěryhodnosti Framework

Z hlediska implementace TF specifikace sestává ze sady zásad, které umožní úplnou kontrolu nad chováním identity a prostředí.  AD B2C vlastní zásady Azure, které využívají architekturu rozhraní identit umožňují vytvářet a vytvořit vlastní TF prostřednictvím deklarativních zásad, které můžete definovat a konfigurace:

- Odkaz na dokument nebo odkazy, které definují federovanou identitu ekosystém komunity, který se vztahuje TF. Jsou odkazy na dokumentaci TF. Pravidla (Předdefinované) provozní "runtime", nebo jízdy uživatele, které automatizují a/nebo řídit exchange a použití deklarací identity. Tyto cesty uživatele jsou přidruženy Symbolů (a LOP). Zásady proto může mít cesty uživatele s použitím různých LOAs (a LOPs).

- Zprostředkovatelé identitu a atribut, nebo poskytovatele deklarací identity v komunitě zájmu a technické profily, které podporují spolu s akreditace Symbolů/LOP (out-of-band), která má vztah k nim.

- Integrace s atribut ověřovatele nebo zprostředkovatelů deklarací identity.

- Předávající strany v komunitě (podle odvození).

- Metadata pro navázání komunikace mezi účastníky v síti. Tato metadata, spolu s technické profily se používají během transakce pro vložení "na lince" vzájemná funkční spolupráce mezi předávající strany a ostatní účastníci komunity.

- Protokol převodu, pokud existuje (například SAML 2.0, OAuth2, WS-Federation a OpenID Connect).

- Požadavky na ověřování.

- Vícefaktorové Orchestrace případné.

- Sdílené schéma pro všechny deklarace identity, které jsou k dispozici a mapování účastníkům komunitu zajímají.

- Všechny deklarace identity transformace, spolu s minimalizaci možných data v tomto kontextu, chcete-li udržet exchange a použití deklarací identity.

- Vazby a šifrování.

- Deklarace identity úložiště.

### <a name="understand-claims"></a>Vysvětlení deklarací identity

> [!NOTE]
> Pro všechny možné druhy informací o identitě, který možná bude vyměněn jako "deklarace" souhrnně označujeme: deklarace identity o pověření pro ověření koncového uživatele, prověřování identity, zařízení komunikace, fyzické umístění, osobně určující atributy, a tak dále.  
>
> Vzhledem k tomu, že v online transakce, nejsou tyto artefakty dat faktů, které se dá přímo ověřit předávající stranou používáme – místo "atributy"--"deklarace". Místo toho jsou kontrolní výrazy nebo deklarace identity o fakta, pro které musíte předávající straně vývoj dostatečnou důvěru udělit požadovaná transakce koncového uživatele.  
>
> Použijeme také termín "deklarace", protože Azure AD B2C vlastní zásady, které používají architekturu rozhraní identit jsou navržené pro zjednodušení exchange všechny typy informací digitální identity konzistentním způsobem bez ohledu na to, zda je na základním protokolu definováno pro načtení ověřování nebo atribut uživatele.  Podobně, používáme pojem "zprostředkovatelů deklarací" souhrnně odkazovat na zprostředkovatelů identity, poskytovatelů atribut a atribut ověřovatele, když jsme nechcete, aby k rozlišení mezi vlastní specifické funkce.   

Proto se řídí, jak informace o identitě se vyměňují mezi předávající stranu, identity a poskytovatelé atribut a atribut ověřovatele. Tím i určovat, které identity a atribut poskytovatelé jsou požadovány pro předávající stranu ověřování. By měly být považovány jazyka specifického pro doménu (DSL), to znamená, že počítač jazyk, který má specializované pro určité domény aplikace s dědičnosti, *Pokud* příkazy, polymorfismu.

Tyto zásady představují, který je strojově čitelný část TF konstrukce v Azure AD B2C vlastní zásady, které využívá architekturu rozhraní identit. Patří mezi ně všechny provozní údaje, včetně metadat zprostředkovatelů deklarací identity a technické profily, definice schémat deklarace identity, funkce transformace deklarací identity a uživatel cesty, které jsou vyplněna usnadnit provozní Orchestrace a automatizace.  

Se považuje za *živé dokumenty* vzhledem k tomu, že existuje šance, který mění jejich obsah v čase týkající se aktivní účastníky deklarované v zásadách. Je také potenciál, který může změnit podmínky a ujednání se účastníka.  

Nastavení federace a údržby výrazně zjednodušuje různé deklarace zprostředkovatelé/ověřovatele připojení nebo ponechte (komunity reprezentována) pro stínění předávající strany z probíhající rekonfigurací důvěryhodnosti a připojení k sadě zásad.

Zajištění lepší interoperability je další velkou výzvou. Další deklarace identity zprostředkovatelé/ověřovatele musí být integrovány, protože předávající strany je nepravděpodobné, že podporují všechny nezbytné protokoly. Vlastní zásady služby Azure AD B2C tento problém vyřešit tak, že podpora standardních oborových protokolů a použití konkrétního uživatele cesty do transponovat žádostí přijímající strany a atribut zprostředkovatelů nepodporují stejný protokol.  

Cesty uživatele patří protokol profily a metadata, která slouží k vložení "na lince" vzájemná funkční spolupráce mezi předávající strany a ostatní účastníky. Existují také provozní runtime pravidla, která se použijí u zprávy s informacemi o exchange žádost/odpověď identity pro vynucování dodržování předpisů pomocí zásad publikované jako součást specifikaci TF. Cílem cesty uživatele je klíčem k přizpůsobení prostředí pro zákazníky. Také přibližuje o tom, jak systém funguje na úrovni protokolu.

Na tomto základě předávající strany aplikací a portály můžete, v závislosti na kontextu, vyvolat Azure AD B2C vlastní zásady, které využívají architekturu rozhraní identit název určité zásady a získat přesné informace o chování a exchange chtějí bez jakýchkoli muss, fuss nebo rizika.
