---
title: Azure platebních zpracování plán, podle kterého – požadavky na fyzický přístup
description: PCI DSS požadavek 9
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 91595a69-e9ce-4f9c-8388-10224165d9c0
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 2dbf4d9e2f4ff7b6cdf8e48c24eeee4d036fe195
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="physical-access-requirements-for-pci-dss-compliant-environments"></a>Požadavky fyzického přístupu pro prostředí kompatibilní s rozhraním PCI DSS 
## <a name="pci-dss-requirement-9"></a>PCI DSS požadavek 9

**Omezte fyzický přístup k datům držitele karty**

> [!NOTE]
> Tyto požadavky jsou definovány [Council standardy zabezpečení odvětví platebních karet (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako součást [PCI Data zabezpečení DSS (Standard) verze 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Naleznete PCI DSS informace o testování postupy a pokyny pro každý požadavek.

Všechny fyzický přístup k data nebo systémy této úklidové držitele karty data poskytuje možnost pro jednotlivce pro přístup k zařízení nebo data a odebrat systémy nebo hardcopies a musí být správně s omezeným přístupem. Pro účely požadavek 9 "dohlížející na bezpečný pracovníky" odkazuje na zaměstnance na plný úvazek a částečný, dočasný zaměstnancům, dodavatelů a konzultanty, kteří se fyzicky nacházejí místní entity. "Návštěvníka" odkazuje na dodavatele, hosta žádné pracovníky dohlížející na bezpečný, pracovníky služby nebo každý, kdo je potřeba zadat zařízení po krátkou dobu, obvykle více než jeden den. "Média" odkazuje na všechny dokumentu a elektronické médium obsahující data držitele karty.

## <a name="pci-dss-requirement-91"></a>PCI DSS požadavek 9.1

**9.1** pomocí ovládacích prvků položka příslušné zařízení na sledování a omezení fyzický přístup k systémům v prostředí datového držitele karty.

**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure zodpovídá za implementace, vynucení a monitorování zabezpečení fyzický přístup pro datového centra. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



### <a name="pci-dss-requirement-911"></a>PCI DSS požadavek 9.1.1

**9.1.1** použít buď videokamer nebo přístup řízení mechanismy (nebo oba) monitorování jednotlivých fyzický přístup do citlivých oblastí. Zkontrolujte shromážděná data a korelovat s další položky. Ukládat alespoň tři měsíce, není-li jinak omezené na základě zákona.

> [!NOTE]
> "Citlivých prostor" odkazuje na všechny datové centrum, místnosti serverů nebo všechny oblasti, kde systémy, které ukládat, zpracovat nebo přenášíte data. Nevztahuje se na veřejně přístupných oblastí, kde jsou umístěny, jako je například pokladní oblasti v úložišti prodejní pouze terminály POS.

**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure zodpovídá za implementace, vynucení a monitorování průmyslové televize a mechanismy řízení přístupu biometrické pro datového centra. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



### <a name="pci-dss-requirement-912"></a>PCI DSS požadavek 9.1.2

**9.1.2** implementovat fyzické a logické kontroly omezit přístup k veřejně přístupné síti konektorů. 

Například sítě konektorů umístěné ve veřejných oblastí a oblastí, které jsou přístupné pro návštěvníky může být zakázána a povolit pouze při přístupu k síti je výslovně povoleno. Alternativně může zajistit, že návštěvníky se vyvedena na všechny časy v oblastech s aktivní síťové konektorů implementovat procesy.

**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Neexistují žádné konektorů veřejně přístupné síti v rámci platformy Microsoft Azure. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



### <a name="pci-dss-requirement-913"></a>PCI DSS požadavek 9.1.3

**9.1.3** Omezte fyzický přístup k bezdrátové přístupové body, brány, kapesních zařízeních, sítě a komunikace hardwaru a telekomunikace řádky.

**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Fyzický přístup k Microsoft Azure seznamy přístupu úzce řídí síťového hardwaru, více způsoby ověřování, fyzických překážek položku a požadavek pro firmy vyžadují schválení pro přístup k zařízení. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



## <a name="pci-dss-requirement-92"></a>PCI DSS požadavek 9.2

**9.2** vyvíjet postupy snadno rozlišit mezi pracovníky dohlížející na bezpečný a návštěvníky, patří:
- Identifikace dohlížející na bezpečný pracovníky a návštěvníky (například přiřazení odznaky)
- Změny požadavků pro přístup
- Odvolání nebo ukončení režimu pracovníky a identifikaci vypršela platnost návštěvníka (například odznaky s ID).

**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure zodpovídá za implementace, vynucení a monitorování zabezpečení a zaměstnanec nebo dodavatel identifikace fyzický přístup při návštěvě datových centrech. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



## <a name="pci-dss-requirement-93"></a>PCI DSS požadavek 9.3

**9.3** řízení fyzického přístupu pro pracovníky dohlížející na bezpečný do citlivých prostor následujícím způsobem:
- Přístup musí být oprávnění a na základě jednotlivých úloh funkce.
- Přístup je odebrán okamžitě po ukončení a všechny mechanismů fyzického přístupu, například klíče, přístupové karty atd., jsou vráceny nebo vypnutá.

**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Povolení přístupu k datových Center společnosti Microsoft se řídí použití seznamu autorizovaný přístup schválení tým datové centrum podle Princip nejnižších nutných oprávnění. Seznam řízení přístupu je zkontrolovat, ověřit a čtvrtletně aktualizovat.<br /><br />Microsoft Azure datových centrech využívat fyzický přístup zařízení, jako je hraniční brány, čteček oznámení "BADGE" elektronické přístup, biometrické čtečky, man depeše/portály pro a proti průchodu zpět zařízení. Přístup k oznámení "BADGE" zařízení jsou neustále monitorovány. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



## <a name="pci-dss-requirement-94"></a>PCI DSS požadavek 9.4

**9.4** implementace postupů k identifikaci a autorizaci návštěvníky. Postupy by měly obsahovat následující.

**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure zodpovídá pro vynucení doručení předem schválené jsou dostali bay zabezpečené načítání, který je fyzicky izolované od zařízení, informace o zpracování a jsou monitorovány pomocí oprávněný personál. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



### <a name="pci-dss-requirement-941"></a>PCI DSS požadavek 9.4.1

**9.4.1** návštěvníky mají oprávnění před vstupem a vyvedena na všechny časy v oblastech, kde je dat držitele karty zpracovat nebo zachována.


**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure zodpovídá pro vynucení doručení předem schválené jsou dostali bay zabezpečené načítání, který je fyzicky izolované od zařízení, informace o zpracování a jsou monitorovány pomocí oprávněný personál. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



### <a name="pci-dss-requirement-942"></a>PCI DSS požadavek 9.4.2

**9.4.2** návštěvníky jsou identifikovány a danou oznámení nebo jiné identifikaci, který vyprší a který viditelně odlišuje návštěvníky z dohlížející na bezpečný pracovníky.

**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Aplikace Microsoft access center dat musí být předem schválené a osoby oprávněné jsou vyžadovány k vrácení se změnami s fyzického zabezpečení v místě doručení a zadejte platný důkaz ID před vstupem. Odznaky jasně označuje zaměstnanci. Dodavatelů a návštěvníky zobrazit dočasné oznámení, které musí být předána po opuštění zařízení. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



### <a name="pci-dss-requirement-943"></a>PCI DSS požadavek 9.4.3

**9.4.3** návštěvníky se zobrazí výzva k odstoupení od Odznáček nebo identifikace před opuštěním zařízení nebo na datum vypršení platnosti.

**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Návštěvníky vyžadovaných předáním odznaky po opuštění jakékoli zařízení společnosti Microsoft. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



### <a name="pci-dss-requirement-944"></a>PCI DSS požadavek 9.4.4

**9.4.4** návštěvníka protokolu se používá k udržení fyzické kontrolní záznam aktivity návštěvníka k zařízením a také místnostmi počítače a datových center, kde ukládána nebo přenášených dat držitele karty.
Zdokumentujte název návštěvníka, firma reprezentované a pracovníky dohlížející na bezpečný autorizace fyzický přístup v protokolu.
Zachovat tento protokol pro minimálně tři měsíce, není-li jinak omezené na základě zákona.

**Odpovědnosti:&nbsp;&nbsp;`Microsoft Azure Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure zodpovídá za údržbu návštěvníka protokolu jako fyzické kontrolní záznam aktivity návštěvníka k zařízením a také místnostmi počítače a datových center, kde ukládána nebo přenášených dat držitele karty. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



## <a name="pci-dss-requirement-95"></a>PCI DSS požadavek 9.5

**9.5** fyzicky zabezpečené všechna média.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore ukládá všechna data v Azure SQL Database. Instance databáze SQL PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-951"></a>PCI DSS požadavek 9.5.1

**9.5.1** uložte média zálohy v zabezpečeném umístění, nejlépe odlehlého budovy, například alternativní nebo zálohování lokality nebo budovy komerční úložiště. Zkontrolujte umístění zabezpečení nejméně jednou ročně.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore ukládá všechna data v Azure SQL Database. Instance databáze SQL PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-96"></a>PCI DSS požadavek 9.6

**9.6** udržovat přísnou kontrolu distribuci interních nebo externích libovolného typu médií, včetně následujících.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore ukládá všechna data v Azure SQL Database. Instance databáze SQL PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-961"></a>PCI DSS požadavek 9.6.1

**9.6.1** klasifikovat média, takže citlivosti dat se dá určit.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore ukládá všechna data v Azure SQL Database. Instance databáze SQL PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-962"></a>PCI DSS požadavek 9.6.2

**9.6.2** odeslat média zabezpečené Kurýrní nebo jinou metodu doručení, které lze přesně sledovat.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore ukládá všechna data v Azure SQL Database. Instance databáze SQL PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-963"></a>PCI DSS požadavek 9.6.3

**9.6.3** zajistěte, aby správu schválí veškeré média, které se přesune ze zabezpečené oblasti (včetně pokud média je distribuován do jednotlivce).

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore ukládá všechna data v Azure SQL Database. Instance databáze SQL PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-97"></a>PCI DSS požadavek 9.7

**9.7** udržovat přísnou kontrolu úložiště a usnadnění média.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore ukládá všechna data v Azure SQL Database. Instance databáze SQL PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-971"></a>PCI DSS požadavek 9.7.1

**9.7.1** správně údržbu inventáře protokoly všechna média a provedení inventáře média nejméně jednou ročně.


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore ukládá všechna data v Azure SQL Database. Instance databáze SQL PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



## <a name="pci-dss-requirement-98"></a>PCI DSS požadavek 9.8

**9.8** destroy médium, když ho už nepotřebují pro firmy nebo právních důvodů následujícím způsobem.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore ukládá všechna data v Azure SQL Database. Instance databáze SQL PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-981"></a>PCI DSS požadavek 9.8.1

**9.8.1** skartovat, spalování nebo pulpou tištěné materiály tak, aby data držitele karty nelze znovu vytvořena. Zabezpečení použít pro materiály, které mají být zničený, kontejnerů úložiště.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore ukládá všechna data v Azure SQL Database. Instance databáze SQL PaaS se používá k prezentují databáze bezpečnostní opatření. Další informace najdete v tématu [pokyny PCI - Azure SQL Database](payment-processing-blueprint.md#azure-sql-database).|



### <a name="pci-dss-requirement-982"></a>PCI DSS požadavek 9.8.2

**9.8.2** vykreslení držitele karty data na médiu elektronické neopravitelné tak, aby data držitele karty nelze znovu vytvořena.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Techniky odstraňování dat se liší v závislosti na typu datového objektu, bude zrušeno, ať to odběry, úložiště, virtuální počítače nebo databáze. V víceklientské prostředí Microsoft Azure postupujte opatrně, že je přijata pozornost jednoho zákazníka dat není povoleno buď "úniku" do jiné zákaznická data, nebo když zákazník odstraňuje data, žádné jiné zákazníků (včetně, ve většině případů zákazníka Kdo vlastní jednou data) můžete získat přístup k této odstraněná data.<br /><br />Microsoft Azure postupuje podle NIST 800-88 pokyny na média čištění, které adres hlavní zájmem zajistit tato data se náhodně neuvolní. Tyto pokyny zahrnovat elektronické a fyzické čištění. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore lze odstranit zcela odstraněním použitá při nasazení skupiny prostředků.|



## <a name="pci-dss-requirement-99"></a>PCI DSS požadavek 9.9

**9.9** chránit zařízení, která zaznamenání dat o platební karty prostřednictvím přímé fyzické interakci s kartou před manipulací a nahrazení.

> [!NOTE]
> Tyto požadavky platí pro čtení karty zařízení používaných v pokladním karty prezentace transakce (to znamená, karta prstem, dip). Tento požadavek není určen ruční klíč položka součástí například klávesnice počítače a POS klávesnice. 

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá Azure do protokolu všechny změny systému.<br /><br />Log Analytics poskytuje rozsáhlé protokolování změn. Změny můžete zkontrolovat a ověřit přesnost. Podrobnější pokyny najdete v tématu [pokyny PCI - protokolování a auditování](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-991"></a>PCI DSS požadavek 9.9.1

**9.9.1** zachovat aktuální seznam zařízení. V seznamu by měla obsahovat následující:
- Ujistěte se, model zařízení
- Umístění zařízení (například adresa serveru nebo zařízení, kde se nachází zařízení)
- Sériové číslo zařízení nebo jinou metodu jedinečnou identifikaci

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore obsahuje referenční architektura a seznam všech služeb používaných v dokumentaci k jeho nasazení.|



### <a name="pci-dss-requirement-992"></a>PCI DSS požadavek 9.9.2

**9.9.2** pravidelně kontrolovat povrchy zařízení ke zjištění manipulaci (například přidání skimmers karet do zařízení), nebo nahrazení (například kontrolou sériové číslo nebo jiné zařízení charakteristiky k ověření nebyl byla si místo se podvodné zařízení).

> [!NOTE]
> Přihlásí, zařízení mohl být manipulováno nebo nahrazena příklady neočekávané přílohy nebo kabely zapojeny do zařízení, chybí nebo se změnily názvy zabezpečení, poškozený nebo jinak barevnou velká a malá písmena nebo změny sériové číslo nebo jiné externí označení.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



### <a name="pci-dss-requirement-993"></a>PCI DSS požadavek 9.9.3

**9.9.3** poskytněte školení pro pracovníky zajímat, pokus o manipulaci nebo výměny zařízení. Školení by měla obsahovat následující:
- Ověřte identitu jakékoli třetí strany osoby uplatňující jako opravy nebo údržby pracovníky před jejich k udělení přístupu k upravovat nebo odstraňovat zařízení.
- Nainstalujte, nahraďte nebo vrátí zařízení bez ověření.
- Mějte na paměti podezřelého chování kolem zařízení (například pokusy o neznámé osob odpojit nebo otevřete zařízení).
- Hlášení podezřelého chování a údaje o manipulaci zařízení nebo nahrazení odpovídajícím osobám (například k vedoucím správce nebo zabezpečení).

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|



## <a name="pci-dss-requirement-910"></a>PCI DSS požadavek 9.10

**9.10** zajistěte, aby zásady zabezpečení a provozních postupů pro omezení fyzický přístup k datům držitele karty jsou popsané v použití a ví, že všechny dotčené strany.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Není k dispozici.|




