---
title: Azure platebních zpracování plán, podle kterého – požadavky na zabezpečení systému
description: PCI DSS požadavek 6
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 79889fdb-52d2-42db-9117-6e2f33d501e0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 2f6f1bfc853f261eecf5357cef5d3e3d972781b1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="secure-system-requirements-for-pci-dss-compliant-environments"></a>Požadavky na systém pro kompatibilní se standardem PCI DSS prostředí zabezpečení 
## <a name="pci-dss-requirement-6"></a>PCI DSS požadavek 6

**Vývoj a udržovat zabezpečených systémů a aplikací**

> [!NOTE]
> Tyto požadavky jsou definovány [Council standardy zabezpečení odvětví platebních karet (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako součást [PCI Data zabezpečení DSS (Standard) verze 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Naleznete PCI DSS informace o testování postupy a pokyny pro každý požadavek.

Skrupulí jednotlivce použít k získání privilegovaného přístupu k systémům ohrožení zabezpečení. Mnoho z těchto chyb zabezpečení jsou opraveny opravy zabezpečení poskytované dodavatele, které musí být nainstalované entitami, které spravují v systémech. Všechny systémy musí mít všechny opravy odpovídající softwaru pro ochranu proti zneužití a ohrožení zabezpečení dat držitele karty jednotlivcům se zlými úmysly a škodlivým softwarem.

> [!NOTE]
> Oprav odpovídající softwaru jsou tyto opravy, které byly vyhodnoceny a otestovat dostatečně k určení, že opravy nejsou v konfliktu s existující konfigurace zabezpečení. Pro interní aplikace vyvinuté může být řada chyb zabezpečení, kterým se lze vyhnout pomocí procesy vývoje standardní systém a zabezpečení, kódování techniky.

## <a name="pci-dss-requirement-61"></a>PCI DSS požadavek 6.1

**6.1** proces pro určení chyb zabezpečení, pomocí důvěryhodných mimo zdroje pro zabezpečení informací ohrožení zabezpečení, vytvořte a přiřaďte riziko řazení (pro například jako "vysoká", "střední" nebo "nízká") na nově zjištěných zabezpečení chyby zabezpečení.

> [!NOTE]
> Hodnocení rizik by měla být založena na osvědčené postupy v odvětví, jakož i zvážení potenciální dopad. Například kritéria pro určení rozsahu ohrožení zabezpečení mohou zahrnovat zvážení CVSS základní skóre a klasifikace dodavatele nebo typ systémů vliv. 
> 
> Metody pro hodnocení chyb zabezpečení a přiřazení hodnocení rizik se budou lišit v závislosti na prostředí a vyhodnocení rizik strategie organizace. Hodnocení rizik měli, minimálně, identifikovat všechna ohrožení zabezpečení považuje za "vysoce rizikové" v prostředí. Kromě hodnocení rizik, ohrožení zabezpečení lze považovat za "kritické" Pokud se představovat bezprostředního nebezpečí výskytu do prostředí, dopad rozhodujících systémů, nebo by mělo za následek potenciální ohrožení není-li řešit. Příklady kritických systémů může zahrnovat systémů zabezpečení, veřejné zařízení a systémy, databáze a jiné systémy, které ukládají, proces, nebo přenášíte data.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Postupy byly vytvořit a implementovat kontrolovala chyby zabezpečení v hostitelé hypervisoru v hranice oboru. Ohrožení zabezpečení kontrola se provádí na serverové operační systémy, databáze a síťová zařízení s příslušnou ohrožení zabezpečení, nástroje pro kontrolu. Čtvrtletně minimálně jsou prováděny prověřování ohrožení zabezpečení. Kontrakty Microsoft Azure s nezávislí posuzovatelé provést průnikům testování hranice Microsoft Azure. Cvičení Red týmu se také pravidelně provádí a výsledky používat k vylepšení zabezpečení. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore snižuje riziko ohrožení zabezpečení pomocí služby Application Gateway s firewall webových aplikací a ruleset OWASP povolena. Další informace najdete v tématu [pokyny PCI - zmírnění rizik chyb zabezpečení](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-62"></a>PCI DSS požadavek 6.2

**6.2** zajištění všech součástí systému a softwaru ochrany z známých slabých míst instalací opravy zabezpečení použít dodavatele. Nainstalujte do jednoho měsíce od vydání opravy zabezpečení.

> [!NOTE]
> Mělo by být určené opravy kritické zabezpečení podle riziko řazení procesu definovaného v 6.1 požadavek.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure je odpovědný za dodržování všech síťových zařízení a softwaru operačního systému hypervisoru chráněn před známých slabých míst instalací opravy zabezpečení použít dodavatele. Pokud nechcete použít službu na žádost zákazníka, aby se zajistilo zabránila a opraven včas úroveň ohrožení zabezpečení operačního systému existuje proces správy opravy. Produkční servery jsou prohledávány ověření dodržování předpisů oprava měsíčně. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore je řešení služby PaaS. Azure poskytuje údržby všechny opravy služby.|



## <a name="pci-dss-requirement-63"></a>PCI DSS požadavek 6.3

**6.3** vývoji softwaru interních a externích aplikací (včetně založené na webu pro správu přístup k aplikacím) bezpečně, a to takto:
- V souladu s PCI DSS (například zabezpečené ověřování a protokolování)
- Podle oborových standardů a osvědčených postupů
- Zařadit zabezpečení informací v průběhu životního cyklu softwaru vývoj 

> [!NOTE]
> To platí pro všechny interně vyvinuté a také sled prací nebo vlastní software vyvinuté třetích stran.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Aplikace Microsoft Azure a koncové body jsou vyvinuté v souladu s metodika Microsoft Security Development Lifecycle (SDL), který je v souladu s požadavky DSS. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore byla navržená tak, aby použijte osvědčené postupy ochrana CHD. Pokyny pro nasazení poskytuje podrobnosti o meassures zabezpečení a je povoleno protokolování.|



### <a name="pci-dss-requirement-631"></a>PCI DSS požadavek 6.3.1

**6.3.1** odebrat vývoj, testovací nebo účty vlastní aplikaci, ID uživatele a hesla předtím, než aplikace aktivní, nebo byly vydané zákazníků.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Zkontrolujte pro konečné zabezpečení (FSR) se provádí pro hlavní verze před produkční nasazení pomocí určených Poradce zabezpečení mimo Azure vývojový tým a ujistěte se, že jsou vydávány pouze aplikace, které jsou připravené pro produkci. Jako součást této poslední kontrole je zajistit, že všechny testovací účty a testovací data byly odebrány. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje pracovní službu, která je zaznamenána do protokolu a izolované. Každý z úrovně sítě má skupinu zabezpečení vyhrazenou síť [NSG]. Další informace najdete v tématu [pokyny PCI - skupiny zabezpečení sítě](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-632"></a>PCI DSS požadavek 6.3.2

**6.3.2** zkontrolujte před vlastní kód k uvolnění výrobní nebo zákazníků za účelem zjištění všechny potenciální ohrožení zabezpečení (pomocí manuální nebo automatizované procesy) kódování obsahovat alespoň následující:
- Změny kódu jsou kontrolovány jednotlivci než původní kód autora a jednotlivci znalostmi o technikách revize kódu a zabezpečení, kódování postupy.
- Kód recenze Ujistěte se, že kód vyvinutý podle pokynů pro zabezpečené kódování
- Náležité opravy jsou implementované před k uvolnění
- Výsledky revize kódu jsou zkontrolovány a schváleny správou předchozí verze 

> [!NOTE]
> Tento požadavek pro kód recenze platí pro všechny vlastní kód (interní a veřejné), jako součást systému vývojový životní cyklus. 
>
> Kód recenze mohou být prováděna zkušení interní pracovníky nebo třetí strany. Veřejné webové aplikace platí také další ovládací prvky pro adresu trvalé hrozby a ohrožení zabezpečení po implementaci, jak jsou definovány v PCI DSS požadavek 6.6.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Aplikace Microsoft Azure a koncové body jsou vyvinuté v souladu s metody Microsoft životního cyklu SDL (Security Development). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje pracovní službu, která je zaznamenána do protokolu a izolované. Každý z úrovně sítě má skupinu zabezpečení vyhrazenou síť [NSG]. Další informace najdete v tématu [pokyny PCI - skupiny zabezpečení sítě](payment-processing-blueprint.md#network-security-groups).|



## <a name="pci-dss-requirement-64"></a>PCI DSS požadavek 6.4

**6.4** postupujte podle změnit řízení procesy a postupy pro všechny změny do komponent systému. Procesy musí zahrnovat následující (viz požadavky na 6.4.1 k 6.4.6).

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft dodržuje NIST pokyny týkající aspekty zabezpečení při vývoji softwaru v této informace zabezpečení musí být integrován do SDLC od zahájení systému. Nepřetržité integrace postupů zabezpečení v procesu Microsoft SDL umožňuje:<ul><li>Časná identifikace a zmírnění chyby zabezpečení a nesprávné konfigurace</li><li>Informace o potenciální softwaru kódování problémy způsobené požadované bezpečnostní ovládací prvky</li><li>Identifikace sdílené zabezpečení služeb a opakované použití nástroje osvědčené postupy zabezpečení, což zvyšuje postavení zabezpečení prostřednictvím principy metody a techniky</li><li>Vynucení programu správy společnosti Microsoft již komplexní rizika</li></ul>Microsoft Azure navázal změn a verzí procesů správy k řízení provádění hlavních změn, včetně:<ul><li>Identifikace a dokumentaci plánované změny</li><li>Identifikace obchodních cílů, priority a scénáře při plánování produktu</li><li>Specifikace návrhu funkce nebo součást</li><li>Zkontrolujte provozní připravenosti podle předem definovaná kritéria/kontrolní seznam pro vyhodnocení celkové riziko/dopad</li><li>Testování, autorizace a správu změn na základě přechodu kritérií pro vývoj (vývoj), INT (integrace testování), fáze (předprodukční) a PRODUKČNÍMU (produkční) prostředí podle potřeby. Je zodpovědností pro své vlastní aplikace hostované ve službě Microsoft Azure zákazníků.</li></ul> |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Ukázkové společnosti Contoso Webstore poskytuje pracovní službu, která je zaznamenána do protokolu a izolované. <br /><br />Každý z úrovně sítě má skupinu zabezpečení vyhrazenou síť [NSG]. Další informace najdete v tématu [pokyny PCI - skupiny zabezpečení sítě](payment-processing-blueprint.md#network-security-groups).<br /><br />Změny se zaznamenávají pomocí služby Operations Management Suite a sady Runbook se používá ke shromažďování protokolů. Log Analytics poskytuje rozsáhlé protokolování změn. Změny můžete zkontrolovat a ověřit přesnost. Podrobnější pokyny najdete v tématu [pokyny PCI - Operations Management Suite](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-641"></a>PCI DSS požadavek 6.4.1

**6.4.1** oddělené vývoj a testovací prostředí z provozní prostředí a vynucení oddělení pomocí řízení přístupu.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 6.4](#pci-dss-requirement-6-4). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje pracovní službu, která je zaznamenána do protokolu a izolované. Každý z úrovně sítě má skupinu zabezpečení vyhrazenou síť [NSG]. Další informace najdete v tématu [pokyny PCI - skupiny zabezpečení sítě](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-642"></a>PCI DSS požadavek 6.4.2

**6.4.2** oddělení povinností mezi vývoj/testování a provozním prostředím

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 6.4](#pci-dss-requirement-6-4). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje pracovní službu, která je zaznamenána do protokolu a izolované. Každý z úrovně sítě má skupinu zabezpečení vyhrazenou síť [NSG]. Další informace najdete v tématu [pokyny PCI - skupiny zabezpečení sítě](payment-processing-blueprint.md#network-security-groups).|



### <a name="pci-dss-requirement-643"></a>PCI DSS požadavek 6.4.3

**6.4.3** provozních dat (zvětšení za provozu) se nepoužívají pro testování nebo pro vývoj.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 6.4](#pci-dss-requirement-6-4). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Dynamická data číslo (PANORAMOVÁNÍ) primární účet nemá Contoso Webstore.|



### <a name="pci-dss-requirement-644"></a>PCI DSS požadavek 6.4.4

**6.4.4** odebrání testovací data a účty z komponent systému předtím, než je systém stane aktivní nebo přejde do produkčního prostředí.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 6.4](#pci-dss-requirement-6-4). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore nemá všechny testovací účty.|



### <a name="pci-dss-requirement-645"></a>PCI DSS požadavek 6.4.5

**6.4.5** procedury řízení změn k provedení opravy zabezpečení a změny softwaru musí zahrnovat následující:
- **6.5.4.1** dokumentaci dopad.
- **6.5.4.2** dokumentované změnit schválení oprávnění zúčastněným stranám.
- **6.5.4.3** funkce test a ověřte, že tato změna nemá negativní vliv na nepříznivě zabezpečení systému.
- **6.5.4.4** zálohující postupy.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 6.4](#pci-dss-requirement-6-4). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore je řešení služby PaaS. A údržby všechny opravy service poskytuje Azure.|



### <a name="pci-dss-requirement-646"></a>PCI DSS požadavek 6.4.6

**6.4.6** po dokončení významné změny, musí být implementována všechny relevantní PCI DSS požadavky na všechny nové nebo změněné systémy, sítě a dokumentaci aktualizovat, protože použít.

> [!NOTE]
> Tento požadavek je osvědčeným postupem až 31. ledna 2018, po které bude požadavek.


**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 6.4](#pci-dss-requirement-6-4). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore je řešení služby PaaS. A údržby všechny opravy service poskytuje Azure.|



## <a name="pci-dss-requirement-65"></a>PCI DSS požadavek 6.5

**6.5** adresu běžné chyby zabezpečení v procesy vývoje softwaru kódování následujícím způsobem:
- Cvičení vývojáři nejméně jednou ročně v aktuální zabezpečený kódování techniky, včetně toho, jak předejdete známých chyb zabezpečení kódování.
- Vývoj aplikací založených na zabezpečené pokyny.

> [!NOTE]
> Chyby uvedené v bodu 6.5.1 prostřednictvím 6.5.10 byla aktuální s osvědčenými postupy v odvětví, pokud byla publikována tato verze PCI DSS. Však odvětví osvědčené postupy pro správu ohrožení zabezpečení jsou aktualizovány (například průvodci OWASP sítě SAN KWE horního 25, CERT zabezpečené kódování, atd.), musí být aktuální osvědčené postupy použit pro tyto požadavky. 
> 
> [!NOTE]
> Požadavky bodu 6.5.1 prostřednictvím 6.5.6, níže, platí pro všechny aplikace (interní nebo externí). Požadavky na 6.5.7 prostřednictvím 6.5.10, níže, použít pro webové aplikace a rozhraní aplikace (interní nebo externí). 

- **Bodu 6.5.1** vkládání nedostatky, zejména Injektáž SQL. Zvažte také vkládání příkaz operačního systému, LDAP a XPath nedostatky vkládání, jakož i další chyby vkládání.
- **6.5.2** přetečení vyrovnávací paměti
- **6.5.3** nezabezpečené kryptografického úložiště
- **6.5.4** nezabezpečené komunikace
- **6.5.5** zpracování nesprávné chyb
- **6.5.6** všechna ohrožení zabezpečení "vysoce rizikové" identifikovat procesu identifikace ohrožení zabezpečení (podle definice v PCI DSS požadavek 6.1)
- **6.5.7** skriptování (XSS)
- **6.5.8** řízení nevhodný přístup (například odkazy na objekty nezabezpečené přímo, selhání omezit přístup k adresu URL, traversal adresáře a selhání k omezení přístupu uživatelů k funkcím)
- **6.5.9** padělání požadavku posílaného mezi weby (proti útokům CSRF)
- **6.5.10** přerušený ověřování a relace správy

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 6.4](#pci-dss-requirement-6-4). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Ukázkové společnosti Contoso Webstore obsahuje pokyny, které zabezpečený vývoj, diagramu toku dat a hrozeb modelu pro ilustraci zabezpečený vývoj postupy.|



## <a name="pci-dss-requirement-66"></a>PCI DSS požadavek 6.6

**6.6** pro veřejné webové aplikace, adres nové hrozby a ohrožení zabezpečení průběžně a zkontrolujte tyto aplikace jsou chráněny před útoky pomocí známých některý z následujících metod:

- Kontrola veřejné webové aplikace prostřednictvím aplikace ruční nebo automatické ohrožení zabezpečení zabezpečení posuzovací nástroje nebo metod, nejméně jednou ročně a po provedení všech změn 

   > [!NOTE]
   > Tato assessment není stejný jako prověřování ohrožení zabezpečení pro požadavek 11.2 provést. 

- Instalace automatizované technické řešení, který zjišťuje a brání útoky založenými na web (například Brána firewall webové aplikace) před veřejné webové aplikace průběžně zkontrolujte veškerý provoz.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure testy veřejné webové aplikace v rámci procesu SDL, než se aplikace nasadí do produkčního prostředí. Kromě toho Microsoft kontroluje všechny veřejné webové aplikace v provozním prostředí v pravidelných intervalech ke zjištění všech možných ohrožení zabezpečení. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Odkaz na řešení snižuje riziko ohrožení zabezpečení pomocí služby Application Gateway s firewall webových aplikací a ruleset OWASP povolena. Další informace najdete v tématu [pokyny PCI - zmírnění rizik chyb zabezpečení](payment-processing-blueprint.md#application-gateway).|



## <a name="pci-dss-requirement-67"></a>PCI DSS požadavek 6.7

**6.7** zajistěte, aby zásady zabezpečení a provozní postupy pro vývoj a správa zabezpečené systémy a aplikace se zdokumentovat, používá a ví, že všechny dotčené strany.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Odkaz na řešení snižuje riziko ohrožení zabezpečení pomocí služby Application Gateway s firewall webových aplikací a ruleset OWASP povolena. Další informace najdete v tématu [pokyny PCI - zmírnění rizik chyb zabezpečení](payment-processing-blueprint.md#application-gateway).|




