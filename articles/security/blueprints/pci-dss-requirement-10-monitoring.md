---
title: Azure platebních zpracování plán, podle kterého – požadavky na monitorování
description: PCI DSS požadavek 10
services: security
documentationcenter: na
author: simorjay
manager: mbaldwin
editor: tomsh
ms.assetid: 293a1673-54bc-478c-9400-231074004eee
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: frasim
ms.openlocfilehash: 708c57c1d7b79d3fd3c129de9a7ce4099ab6ac36
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="monitoring-requirements-for-pci-dss-compliant-environments"></a>Monitorování požadavky pro kompatibilní se standardem PCI DSS prostředí 
## <a name="pci-dss-requirement-10"></a>PCI DSS požadavek 10

**Sledování a monitorování veškerý přístup k síťovým prostředkům a data držitele karty**

> [!NOTE]
> Tyto požadavky jsou definovány [Council standardy zabezpečení odvětví platebních karet (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako součást [PCI Data zabezpečení DSS (Standard) verze 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Naleznete PCI DSS informace o testování postupy a pokyny pro každý požadavek.

Protokolování mechanismy a umožňuje sledovat aktivity uživatele jsou kritické v prevence, zjišťování nebo minimalizovat dopad ohrožení data. Přítomnost protokoly ve všech prostředích umožňuje důkladné sledování, výstrah a analýzy, pokud něco dojít k chybě. O příčině tohoto ohrožení zabezpečení je velmi obtížné, pokud není možné bez protokoly aktivitu systému.

## <a name="pci-dss-requirement-101"></a>PCI DSS požadavek 10.1

**10.1** záznamy auditu implementace propojení veškerý přístup k součástem systému pro každý jednotlivý uživatel.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure omezuje přístup pro správu a diagnostických nástrojů oprávněný personál s odpovědnost příslušné úlohy. Microsoft Azure omezuje privilegovaný přístup k nástroje používané v produkčním prostředí založené na zásadách nejnižší oprávnění. Microsoft Azure zaznamenává a udržuje protokolu všechny jednotlivé uživatelům přístup k součásti systému Microsoft Azure v prostředí platformy.<br /><br />Komponenty platformy Microsoft Azure (včetně operačního systému, CloudNet, infrastruktury a tak dále) je nakonfigurován pro protokolu a shromáždění událostí zabezpečení. Aktivita správce v platformě Microsoft Azure se zaznamená. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore má rozsáhlé protokolování všech system a aktivitu uživatelů (včetně CHD protokolování). Další informace najdete v tématu [pokyny PCI - protokolování](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-102"></a>PCI DSS požadavek 10.2

**10.2** implementace automatizované záznamy auditu pro všechny součásti systému k rekonstrukci následující události:
- **10.2.1** všechny jednotlivé uživatele přistupuje k datům držitele karty
- **10.2.2** všechny akce prováděné osoba, s kořenovou nebo oprávnění správce
- **10.2.3** přístup k všechny záznamy auditu
- **10.2.4** pokusí neplatný logického přístupu
- **10.2.5** použití a změny mechanismy identifikace a ověřování – včetně, ale není omezen na vytvoření nové účty a zvýšení oprávnění a všechny změny, přidání nebo odstranění účtů s kořenem nebo správce oprávnění
- **10.2.6** inicializace, zastavení nebo pozastavení protokolů auditu
- **10.2.7** vytváření a odstraňování objektů úrovni systému

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure omezuje přístup pro správu a diagnostických nástrojů oprávněný personál s odpovědnost příslušné úlohy. Microsoft Azure omezuje privilegovaný přístup k nástroje používané v produkčním prostředí založené na zásadách nejnižší oprávnění. Microsoft Azure zaznamenává a udržuje protokolu všechny jednotlivé uživatelům přístup k součásti systému Microsoft Azure v prostředí platformy.<br /><br />Komponenty platformy Microsoft Azure (včetně operačního systému, CloudNet, infrastruktury a tak dále) je nakonfigurován pro protokolu a shromáždění událostí zabezpečení. Aktivita správce v platformě Microsoft Azure se zaznamená. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore má rozsáhlé protokolování veškerou aktivitu systému a uživatele, včetně CHD protokolování. Další informace najdete v tématu [pokyny PCI - protokolování](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-103"></a>PCI DSS požadavek 10.3

**10.3** záznam nejméně následující záznam auditu pro všechny součásti systému pro každou jednotlivou událost:
- **10.3.1 specifikace** identifikace uživatele
- **10.3.2** typu události
- **10.3.3** datum a čas
- **10.3.4** indikaci úspěchu nebo neúspěchu
- **10.3.5** vzniku události
- **10.3.6** identity nebo název ovlivněných dat, součást systému nebo prostředků

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure navázal postupy k synchronizaci serverů a síťových zařízení v prostředí Microsoft Azure se servery času NTP vrstvě 1 synchronizovány do globální umístění systému GPS satelity. Synchronizace se provádí automaticky každých pět minut. Microsoft Azure je odpovědný za dodržování hostitelé služeb z správně čas synchronizace. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore zaznamenává identifikace uživatele, typu události, datum časového razítka, události úspěšného se nezdařilo, zdroj události a název prostředku podle požadavků 10.3 ovládacího prvku.|



## <a name="pci-dss-requirement-104"></a>PCI DSS požadavek 10.4

**10.4** používá technologii synchronizaci času, synchronizovat všechny kritické systémové hodiny a časy a ujistěte se, že následující se implementuje pro získávání, distribuci a ukládání čas. 
> [!NOTE]
> Jedním z příkladů technologie synchronizace času je protokol NTP (Network Time).

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure navázal postupy k synchronizaci serverů a síťových zařízení v prostředí Microsoft Azure se servery času NTP vrstvě 1 synchronizovány do globální umístění systému GPS satelity. Synchronizace se provádí automaticky každých pět minut. Microsoft Azure je odpovědný za dodržování hostitelé služeb z správně čas synchronizace. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Synchronizaci času pro službu PaaS provádí Azure.|



### <a name="pci-dss-requirement-1041"></a>PCI DSS požadavek 10.4.1

**10.4.1** důležité systémy mají správnou a konzistentní čas.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 10.4](#pci-dss-requirement-10-4). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Synchronizaci času pro službu PaaS provádí Azure.|



### <a name="pci-dss-requirement-1042"></a>PCI DSS požadavek 10.4.2

**10.4.2** čas data jsou chráněna.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 10.4](#pci-dss-requirement-10-4). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Synchronizaci času pro službu PaaS provádí Azure.|



### <a name="pci-dss-requirement-1043"></a>PCI DSS požadavek 10.4.3

**10.4.3** nastavení času, které byly přijaty z přijata odvětví čas zdroje.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 10.4](#pci-dss-requirement-10-4). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Synchronizaci času pro službu PaaS provádí Azure.|



## <a name="pci-dss-requirement-105"></a>PCI DSS požadavek 10.5

**10.5** zabezpečené auditu stop ukazatele, takže nemůže být změněn.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | FIM a ID nástroje jsou implementované v rámci prostředí Microsoft Azure. Microsoft Azure používá EWS pro podporu analýzy v reálném čase událostí v jeho provozní prostředí. MAs a cílů generovat téměř v reálném čase Výstrahy o událostech, které může potenciálně ohrozit zabezpečení systému. <br /><br />Protokolování služby, uživatelů a zabezpečení událostí (protokoly webového serveru, protokoly serveru FTP a tak dále), které je povoleno a centrálně zachována. Azure omezuje přístup k protokolů auditů, aby oprávněný personál podle pracovní náplně. Protokoly událostí jsou archivovány na infrastrukturu Azure zabezpečené archivace a jsou uchovány na 180 dní. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje pro auditování všech elementů do Azure. Probíhá zálohování do externího zdroje lze provést pomocí [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1051"></a>PCI DSS požadavek 10.5.1

**10.5.1** limit zobrazení auditu stop ukazatele na ty, která potřebuje související úlohy.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 10.5](#pci-dss-requirement-10-5). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje pro auditování všech elementů do Azure. Probíhá zálohování do externího zdroje lze provést pomocí [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1052"></a>PCI DSS požadavek 10.5.2

**10.5.2** soubory záznamu pro audit chránit z neoprávněné změny.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 10.5](#pci-dss-requirement-10-5). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje pro auditování všech elementů do Azure. Probíhá zálohování do externího zdroje lze provést pomocí [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1053"></a>PCI DSS požadavek 10.5.3

**10.5.3** rychle zálohovat soubory záznamu pro audit centralizované protokolu serveru nebo médium, které je obtížné alter.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 10.5](#pci-dss-requirement-10-5). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje pro auditování všech elementů do Azure. Probíhá zálohování do externího zdroje lze provést pomocí [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1054"></a>PCI DSS požadavek 10.5.4

**10.5.4** zápisu protokolech externím technologie do protokolu zabezpečení, centralizované, interní server nebo mediálního zařízení.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 10.5](#pci-dss-requirement-10-5). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje pro auditování všech elementů do Azure. Probíhá zálohování do externího zdroje lze provést pomocí [Azure Backup](https://azure.microsoft.com/services/backup/).|



### <a name="pci-dss-requirement-1055"></a>PCI DSS požadavek 10.5.5

**10.5.5** pomocí monitorování nebo detekce změn softwaru integrity souborů na protokoly zajistit, že existující data protokolu nelze změnit bez generování výstrah (i když se přidávají nová data by nemělo způsobit výstrahu).

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 10.5](#pci-dss-requirement-10-5). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje pro auditování všech elementů do Azure. Probíhá zálohování do externího zdroje lze provést pomocí [Azure Backup](https://azure.microsoft.com/services/backup/).|



## <a name="pci-dss-requirement-106"></a>PCI DSS požadavek 10.6

**10.6** Zkontrolujte protokoly a události zabezpečení pro všechny součásti systému k identifikaci anomálií nebo podezřelé aktivity.
 
> [!NOTE]
> Protokol shromáždění, analýze a upozornění, že nástroje může použít ke splnění tohoto požadavku.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | FIM a ID nástroje jsou implementované v rámci prostředí Microsoft Azure. Microsoft Azure používá EWS pro podporu analýzy v reálném čase událostí v jeho provozní prostředí. MAs a cílů generovat téměř v reálném čase Výstrahy o událostech, které může potenciálně ohrozit zabezpečení systému. <br /><br />Protokolování služby, uživatelů a zabezpečení událostí (protokoly webového serveru, protokoly serveru FTP a tak dále), které je povoleno a centrálně zachována. Azure omezuje přístup k protokolů auditů, aby oprávněný personál podle pracovní náplně. Protokoly událostí jsou archivovány na infrastrukturu Azure zabezpečené archivace a jsou uchovány na 180 dní. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá [Azure Security Center](https://azure.microsoft.com/services/security-center/) monitorovat, sestavy a zabránit anomálií. [Azure Advisor](/azure/advisor/advisor-security-recommendations) nabízí konzistentní, konsolidované zobrazení doporučení pro všechny prostředky Azure.|



### <a name="pci-dss-requirement-1061"></a>PCI DSS požadavek 10.6.1

**10.6.1** alespoň denně zkontrolujte následující:
- Všechny události zabezpečení
- Protokoly všechny součásti systému, které ukládat, zpracovat nebo přenášet CHD nebo SAD
- Protokoly všech důležitých systémových součástí
- Protokoly všech serverů a součástí systému, které provádějí funkce zabezpečení (například brány firewall, zjišťování neoprávněných vniknutí systémy nebo-prevence vniknutí systémy (ID nebo IP adresy), ověřování serverů, elektronické obchodování přesměrování servery a podobně).

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 10.6](#pci-dss-requirement-10-6). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá [Azure Security Center](https://azure.microsoft.com/services/security-center/) monitorovat, sestavy a zabránit anomálií. [Azure Advisor](/azure/advisor/advisor-security-recommendations) nabízí konzistentní, konsolidované zobrazení doporučení pro všechny prostředky Azure.|



### <a name="pci-dss-requirement-1062"></a>PCI DSS požadavek 10.6.2

**10.6.2** Zkontrolujte protokoly všechny součásti systému pravidelně podle organizace zásady a riziko strategie správy, počítáno od organizace roční vyhodnocení rizik.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Naleznete v části "Microsoft Azure" [požadavek 10.6](#pci-dss-requirement-10-6). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá [Azure Security Center](https://azure.microsoft.com/services/security-center/) monitorovat, sestavy a zabránit anomálií. [Azure Advisor](/azure/advisor/advisor-security-recommendations) nabízí konzistentní, konsolidované zobrazení doporučení pro všechny prostředky Azure.|



### <a name="pci-dss-requirement-1063"></a>PCI DSS požadavek 10.6.3

**10.6.3** následnou akci výjimky a identifikovány během procesu kontroly anomálií.

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Viz část "Microsoft Azure" [požadavek 10.6](#pci-dss-requirement-10-6). |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore využívá [Azure Security Center](https://azure.microsoft.com/services/security-center/) monitorovat, sestavy a zabránit anomálií. [Azure Advisor](/azure/advisor/advisor-security-recommendations) nabízí konzistentní, konsolidované zobrazení doporučení pro všechny prostředky Azure.|



## <a name="pci-dss-requirement-107"></a>PCI DSS požadavek 10.7

**10.7** uchovat historii záznam pro audit alespoň jeden rok, s minimálně tři měsíce okamžitě k dispozici pro analýzu (například online, archivované nebo možností obnovení ze zálohy).

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure uchovává protokoly auditu jeden rok, s poslední 3 měsíce okamžitě dostupný prostřednictvím jejich interní portálu. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore má rozsáhlé protokolování všech system a aktivitu uživatelů (včetně CHD protokolování). Další informace najdete v tématu [pokyny PCI - protokolování](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-108"></a>PCI DSS požadavek 10.8

**10.8** **další požadavek pro poskytovatele služeb pouze:** implementujte proces pro včasné zjišťování a vytváření sestav chyb řízení systémů zabezpečení, včetně bez omezení pro selhání:
- Brány firewall
- ID NEBO IP ADRESY
- FIM
- Ochrana proti virům
- Fyzické prvky řízení přístupu
- Ovládací prvky logického přístupu
- Mechanismy protokolování auditu
- Ovládací prvky segmentace (Pokud se používá) 

> [!NOTE]
> Tento požadavek je osvědčeným postupem až 31. ledna 2018, po které bude požadavek.



**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure používá EWS pro podporu analýzy v reálném čase událostí v jeho provozní prostředí. MAs a cílů generovat téměř v reálném čase Výstrahy o událostech, které může potenciálně ohrozit zabezpečení systému. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore má rozsáhlé protokolování všech system a aktivitu uživatelů (včetně CHD protokolování). Další informace najdete v tématu [pokyny PCI - protokolování](payment-processing-blueprint.md#logging-and-auditing).|



### <a name="pci-dss-requirement-1081"></a>PCI DSS požadavek 10.8.1

**10.8.1** **další požadavek pro poskytovatele služeb pouze:** reagovat na selhání všech ovládacích prvků zabezpečení v časovém limitu. Procesy pro odpověď na selhání v ovládacích prvcích zabezpečení musí zahrnovat:
- Obnovení funkcí zabezpečení
- Identifikace a dokumentace doba trvání (datum a čas spuštění na konec) chyby zabezpečení
- Identifikace a dokumentace příčiny či příčin selhání, včetně hlavní příčinu a dokumentace nápravy potřeba adresu příčiny
- Určení a vyřešení potíží zabezpečení, které vznikly při selhání
- Provádění vyhodnocení rizik k určení, zda jsou požadovány v důsledku selhání zabezpečení další akce
- Implementace ovládací prvky bránící důvod selhání ze nadále – obnovení monitorování ovládacích prvků zabezpečení 

> [!NOTE]
> Tento požadavek je osvědčeným postupem až 31. ledna 2018, po které bude požadavek.


**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure používá EWS pro podporu analýzy v reálném čase událostí v jeho provozní prostředí. MAs a cílů generovat téměř v reálném čase Výstrahy o událostech, které může potenciálně ohrozit zabezpečení systému. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore má rozsáhlé protokolování všech system a aktivitu uživatelů (včetně CHD protokolování). Další informace najdete v tématu [pokyny PCI - protokolování](payment-processing-blueprint.md#logging-and-auditing).|



## <a name="pci-dss-requirement-109"></a>PCI DSS požadavek 10.9

**10.9** zajistěte, aby zásady zabezpečení a provozní postupy pro monitorování všech přístupu k síťovým prostředkům a datům držitele karty jsou popsané v použití a ví, že všechny dotčené strany.


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Contoso Webstore poskytuje případ použití a popis o tom, jak je CHD spravované a chráněné.|




