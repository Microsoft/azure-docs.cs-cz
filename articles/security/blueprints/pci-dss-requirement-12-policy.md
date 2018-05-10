---
title: Azure platebních zpracování plán, podle kterého - požadavky zásad
description: PCI DSS požadavek 12
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: a79d59d8-20e3-4efe-8686-c8f4ed80e220
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: jomolesk
ms.openlocfilehash: 2fb238e9b95180d6156159c87ec008a71943e698
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="policy-requirements-for-pci-dss-compliant-environments"></a>Požadavky zásad pro PCI DSS kompatibilní prostředí  
## <a name="pci-dss-requirement-12"></a>PCI DSS požadavek 12

**Udržovat zásadu, která řeší informace o zabezpečení pro všechny pracovníky**

> [!NOTE]
> Tyto požadavky jsou definovány [Council standardy zabezpečení odvětví platebních karet (PCI)](https://www.pcisecuritystandards.org/pci_security/) jako součást [PCI Data zabezpečení DSS (Standard) verze 3.2](https://www.pcisecuritystandards.org/document_library?category=pcidss&document=pci_dss). Naleznete PCI DSS informace o testování postupy a pokyny pro každý požadavek.

Silné zabezpečení zásady předurčuje zabezpečení pro celou entitu a informuje pracovníky očekávané z nich. Všichni pracovníci by měl být vědomi citlivosti dat a jejich zodpovědnosti pro její ochranu. Pro účely požadavek 12 "pracovníky" odkazuje na zaměstnance na plný úvazek a částečný, dočasný zaměstnancům, dodavatelů a konzultanty, kteří jsou "trvalé" v lokalitě entity nebo jinak mají přístup k prostředí dat držitele karty.

## <a name="pci-dss-requirement-121"></a>PCI DSS požadavek 12.1

**12.1** navázání, publikování, udržovat a šíření zásady zabezpečení.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytvoření a udržování zásad zabezpečení informací zákazníků.|



### <a name="pci-dss-requirement-1211"></a>PCI DSS požadavek 12.1.1

**12.1.1** nejméně jednou ročně zkontrolujte zásady zabezpečení a aktualizaci zásad, když se změní prostředí.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností nejméně jednou ročně aktualizaci jejich zásady zabezpečení informací, nebo pokud existují změny v prostředí datového svá držitele karty (CDE) zákazníků.|



## <a name="pci-dss-requirement-122"></a>PCI DSS požadavek 12.2

**12.2** zpracovat implementace vyhodnocení rizik, které:
- Provádí nejméně jednou ročně a po významné změny v prostředí (třeba pořízení fúze, přemístění, atd.)
- Identifikuje důležité prostředky, hrozby a ohrožení zabezpečení
- Výsledkem formální, zdokumentovaných analýza rizik.
- > Příklady metod vyhodnocení rizik zahrnovat, ale nejsou omezeny na OKTÁVU, ISO 27005 a NIST SP 800-30.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností implementace proces pro posouzení rizik, které řeší všechny hrozby uvedené v požadavek 12.2 zákazníků.|



## <a name="pci-dss-requirement-123"></a>PCI DSS požadavek 12.3

**12.3** vyvíjet zásady používání pro kritické technologie a zadejte správné použití těchto technologií.

> [!NOTE]
> Příklady kritických technologií zahrnovat, ale nejsou omezeny na vzdálený přístup a bezdrátové technologie, přenosné počítače, tablety, elektronické vyměnitelné médium, využití e-mailu a Internetu využití.
Zajistěte, aby že tyto zásady používání potřebovat.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad diktování správné použití, implementaci a ověřování pro kritické technologie v rámci jejich CDE zákazníků.|



### <a name="pci-dss-requirement-1231"></a>PCI DSS požadavek 12.3.1

**12.3.1** explicitní schválení autorizovaný strany

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad diktování správné použití, implementaci a ověřování pro kritické technologie v rámci jejich CDE zákazníků.|



### <a name="pci-dss-requirement-1232"></a>PCI DSS požadavek 12.3.2

**12.3.2** ověřování pro použití technologie

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad diktování správné použití, implementaci a ověřování pro kritické technologie v rámci jejich CDE zákazníků.|



### <a name="pci-dss-requirement-1233"></a>PCI DSS požadavek 12.3.3

**12.3.3** seznam všech těchto zařízení a pracovníky s přístupem

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad diktování správné použití, implementaci a ověřování pro kritické technologie v rámci jejich CDE zákazníků.|



### <a name="pci-dss-requirement-1234"></a>PCI DSS požadavek 12.3.4

**12.3.4** metoda přesně a snadno určit vlastníka, kontaktní informace a účel (například označování, kódování nebo inventarizaci zařízení)

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad diktování správné použití, implementaci a ověřování pro kritické technologie v rámci jejich CDE zákazníků.|



### <a name="pci-dss-requirement-1235"></a>PCI DSS požadavek 12.3.5

**12.3.5** podmínky používá technologie

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad diktování správné použití, implementaci a ověřování pro kritické technologie v rámci jejich CDE zákazníků.|



### <a name="pci-dss-requirement-1236"></a>PCI DSS požadavek 12.3.6

**12.3.6** přijatelné síťová umístění pro technologie

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědná za určení umístění v síti přijatelné pro cloudové virtuální počítače, úložiště a podpůrné služby zákazníků.|



### <a name="pci-dss-requirement-1237"></a>PCI DSS požadavek 12.3.7

**12.3.7** seznam produktů společnosti schválení

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědná za určení umístění v síti přijatelné pro cloudové virtuální počítače, úložiště a podpůrné služby zákazníků.|



### <a name="pci-dss-requirement-1238"></a>PCI DSS požadavek 12.3.8

**12.3.8** automatické odpojení relace pro vzdálený přístup technologie po určité době nečinnosti

**Odpovědnosti:&nbsp;&nbsp;`Shared`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Microsoft Azure používá Microsoft podnikové AD relace zámku funkce, které vynucuje výpisů relace uzamčení po určité době nečinnosti. Připojení k síti je ukončen po 30 minutách nečinnosti. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad diktování správné použití, implementaci a ověřování pro kritické technologie v rámci jejich CDE zákazníků.|



### <a name="pci-dss-requirement-1239"></a>PCI DSS požadavek 12.3.9

**12.3.9** aktivace technologie vzdáleného přístupu pro dodavatele a obchodními partnery pouze v případě potřeby podle dodavatele a obchodními partnery, se okamžitě deaktivace po použití

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad diktování správné použití, implementaci a ověřování pro kritické technologie v rámci jejich CDE zákazníků.|



### <a name="pci-dss-requirement-12310"></a>PCI DSS požadavek 12.3.10

**12.3.10** pro pracovníky přístup k datům držitele karty prostřednictvím vzdáleného přístupu technologií, zakázat kopírování, přesunutí a úložiště dat držitele karty na místních pevných discích a vyměnitelná média elektronické, výslovně oprávněni pro definované obchodních potřeb.
Tam, kde existuje potřeba oprávnění business, zásady používání musí vyžadovat chránit data v souladu s všechny příslušné požadavky PCI DSS.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci jsou zodpovědní za zajištění, aby pracovníky přístup k datům držitele karty prostřednictvím vzdáleného přístupu technologie bylo zakázáno kopírování, přesunutí a ukládání dat držitele karty na místních pevných discích a vyměnitelná média elektronické, pokud není explicitně povoleno pro definované obchodní potřeby.|



## <a name="pci-dss-requirement-124"></a>PCI DSS požadavek 12.4

**12.4** Ujistěte se, že zásady zabezpečení a postupy přesně definovat odpovědnosti informace o zabezpečení pro všechny pracovníky.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad diktování správné použití, implementaci a ověřování pro kritické technologie v rámci jejich CDE zákazníků.|



### <a name="pci-dss-requirement-1241"></a>PCI DSS požadavek 12.4.1

**12.4.1** další požadavek pro poskytovatele služeb pouze: vedení správu zřídí odpovědnost za ochranu dat držitele karty a dodržování předpisů programu PCI DSS zahrnout:
- Celkové odpovědnosti za pro údržbu PCI DSS dodržování předpisů
- Definování titulů pro program PCI DSS dodržování předpisů a komunikace executive správy 

> [!NOTE]
> Tento požadavek je osvědčeným postupem až 31. ledna 2018, po které bude požadavek.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci, kteří jsou poskytovatelé jsou zodpovědný za dokumentaci jejich program PCI dodržování předpisů.|



## <a name="pci-dss-requirement-125"></a>PCI DSS požadavek 12,5

**12,5** přiřadit až k jednotlivcům nebo tým následující zodpovědnosti správu informace o zabezpečení.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností definice a přiřazení informací povinnosti v oblasti zabezpečení svým zaměstnancům zákazníků.|



### <a name="pci-dss-requirement-1251"></a>PCI DSS požadavek 12.5.1

**12.5.1** navázání, dokumentu a distribuci zásad a postupů zabezpečení.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností definice a přiřazení informací povinnosti v oblasti zabezpečení svým zaměstnancům zákazníků.|



### <a name="pci-dss-requirement-1252"></a>PCI DSS požadavek 12.5.2

**12.5.2** sledování a analýza výstrahy zabezpečení a informace a distribuovat odpovídajícím osobám.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností definice a přiřazení informací povinnosti v oblasti zabezpečení svým zaměstnancům zákazníků.|



### <a name="pci-dss-requirement-1253"></a>PCI DSS požadavek 12.5.3

**12.5.3** navázání, dokumentu a distribuovat zabezpečení incidentu odpovědi eskalace procedury a zajistit včasné a efektivní zpracování všech situacích.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad diktování správné použití, implementaci a ověřování pro kritické technologie v rámci jejich CDE zákazníků.|



### <a name="pci-dss-requirement-1254"></a>PCI DSS požadavek 12.5.4

**12.5.4** spravovat uživatelské účty, včetně přidání, odstranění a změn.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad diktování správné použití, implementaci a ověřování pro kritické technologie v rámci jejich CDE zákazníků.|



### <a name="pci-dss-requirement-1255"></a>PCI DSS požadavek 12.5.5

**12.5.5** monitorování a řízení veškerá oprávnění pro přístup k datům.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad diktování správné použití, implementaci a ověřování pro kritické technologie v rámci jejich CDE zákazníků.|



## <a name="pci-dss-requirement-126"></a>PCI DSS požadavek 12,6

**12,6** implementovat program Sledování formální zabezpečení vědět význam postupů držitele karty zásady zabezpečení dat a aby všichni pracovníci.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností pro vytváření a údržbu zásad, které obaluje sledování zabezpečení pracovníků, s přístupem k CDE zákazníků.|



### <a name="pci-dss-requirement-1261"></a>PCI DSS požadavek 12.6.1

**12.6.1** informujte pracovníky po přijetím a nejméně jednou ročně. 

> [!NOTE]
> Metody se může lišit v závislosti na roli osob a jejich úroveň přístupu k datům držitele karty.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci jsou zodpovědní za zajištění pracovníci přijímat a potvrdit zabezpečení informací a zvýšení povědomí PCI-DSS cvičení nejméně jednou ročně.|



### <a name="pci-dss-requirement-1262"></a>PCI DSS požadavek 12.6.2

**12.6.2** vyžadují pracovníky nejméně jednou ročně potvrdit, že čtení a rozumím jim zásady zabezpečení a postupy.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci jsou zodpovědní za zajištění pracovníci přijímat a potvrdit zabezpečení informací a zvýšení povědomí PCI-DSS cvičení nejméně jednou ročně.|



## <a name="pci-dss-requirement-127"></a>PCI DSS požadavek 12.7

**12.7** obrazovky potenciální pracovníky před najímáme minimalizovat riziko útoků z interní zdrojů. (Kontroly příklady předchozí historii zaměstnání, trestního záznam, platební historii a referenční dokumentace kontroluje.) 

> [!NOTE]
> Pro tyto potenciální pracovníky k určité pronajaty formou umisťuje například pokladníci úložiště, pouze s přístupem k jedné kartě číslo současně při současném usnadnění transakce, tento požadavek je pouze doporučení.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci jsou zodpovědní za zajištění pracovníci s přístupem k CDE podstoupit důkladné kontroly.|



## <a name="pci-dss-requirement-128"></a>PCI DSS požadavek 12.8

**12.8** Údržba a implementujte zásady a postupy pro správu poskytovatelé služeb, se kterými se sdílí data držitele karty nebo zabezpečení dat držitele karty, které mohou ovlivnit následujícím způsobem.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědná za monitorování soulad s normami PCI pro poskytovatele služeb, se kterými se sdílí data držitele karty, nebo může mít vliv na zabezpečení CDE zákazníků. Zákazníci musí zachovat poskytuje seznam všech služeb v rámci jejich CDE používají.|



### <a name="pci-dss-requirement-1281"></a>PCI DSS požadavek 12.8.1

**12.8.1** spravovat seznam poskytovatelů služeb, včetně popisu poskytované služby.


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědná za monitorování soulad s normami PCI pro poskytovatele služeb, se kterými se sdílí data držitele karty, nebo může mít vliv na zabezpečení CDE zákazníků. Zákazníci musí zachovat poskytuje seznam všech služeb v rámci jejich CDE používají.|



### <a name="pci-dss-requirement-1282"></a>PCI DSS požadavek 12.8.2

**12.8.2** udržovat napsané smlouvy, která zahrnuje potvrzení, poskytovatelů služeb zodpovídají za zabezpečení dat držitele karty poskytovatelé služeb mají nebo jinak ukládat, zpracovat nebo přenášet jménem zákazníka, nebo v rozsahu, který se může mít vliv na zabezpečení zákazníka držitele karty data prostředí. 

> [!NOTE]
> Přesné znění potvrzení bude záviset na Smlouvu mezi dvěma účastníky, podrobnosti o službě poskytované a odpovědnosti přiřazené jednotlivým stranám. Potvrzení nemusí obsahovat přesný součástí tento požadavek.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci jsou odpovědné za údržbu napsané smluv se to v úvahu odpovědnost za údržbu zabezpečení dat držitele karty služeb.|



### <a name="pci-dss-requirement-1283"></a>PCI DSS požadavek 12.8.3

**12.8.3** zajistěte vytvořených procesů pro zapojení poskytovatelé služeb včetně správné kvůli opatrností před engagement.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci jsou zodpovědní za zajištění, že je vytvořených procesů pro zapojení poskytovatelé služeb včetně správné kvůli opatrností před engagement.|



### <a name="pci-dss-requirement-1284"></a>PCI DSS požadavek 12.8.4

**12.8.4** udržovat program k monitorování stavu kompatibility poskytovatelé služeb PCI DSS nejméně jednou ročně.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci zodpovídá za údržbu program k monitorování stavu kompatibility poskytovatelé služeb PCI DSS nejméně jednou ročně.|



### <a name="pci-dss-requirement-1285"></a>PCI DSS požadavek 12.8.5

**12.8.5** Udržovat informace o jaké požadavky PCI DSS spravuje poskytovateli jednotlivých služeb a které jsou spravovány entity.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností ponechá kopii zákazníků [matice souhrn odpovědnost](https://aka.ms/pciblueprintcrm32), který popisuje PCI DSS požadavky, které zodpovídají zákazníka a ty, které zodpovídají Microsoft Azure.|



## <a name="pci-dss-requirement-129"></a>PCI DSS požadavek 12,9

**12,9** další požadavek pro poskytovatele služeb pouze: poskytovatelé služeb vědomí zápis zákazníkům, kteří jsou za zabezpečení dat držitele karty poskytovatele služeb má nebo jinak ukládá procesy, nebo přenáší jménem zákazníka nebo v rozsahu, že by mohlo mít vliv zabezpečení zákazníka držitele karty data prostředí. 

> [!NOTE]
> Přesné znění potvrzení bude záviset na Smlouvu mezi dvěma účastníky, podrobnosti o službě poskytované a odpovědnosti přiřazené jednotlivým stranám. Potvrzení nemusí obsahovat přesný součástí tento požadavek.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci, které jsou poskytovatelé jsou zodpovědní za to jejich zodpovědnosti pro údržbu soulad s normami PCI v úvahu. |



## <a name="pci-dss-requirement-1210"></a>PCI DSS požadavek 12.10

**12.10** implementovat plán reakcí na incidenty. Buďte připraveni reagovat okamžitě na porušení zabezpečení systému.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností vývoj IR plány zákazníků a testování, které považovat všechny ovládací prvky zákazníků týkající se sdílené touch body a všem aplikacím zákazníka využívání infrastruktury Azure a. Je zodpovědností zákazníka poskytnout přesná kontaktní informace do Azure, v případě incidentu je třeba ohlásit k nim, který může mít vliv na jejich aplikace nebo data.|



### <a name="pci-dss-requirement-12101"></a>PCI DSS požadavek 12.10.1

**12.10.1** vytvořte plán reakcí na incidenty prováděná v případě porušení zabezpečení systému. Zajistěte adresy plán následující, minimálně:
- Role, zodpovědnostech a komunikace a obraťte se na strategie v případě ohrožení včetně oznámení o platebních značek, minimálně
- Postupy konkrétní reakcí na incidenty
- Postupy obnovení a kontinuitu podnikových
- Procesy zálohování dat
- Analýza právní požadavky pro vytváření sestav porušením zabezpečení
- Pokrytí a odpovědi všech důležitých systémových součástí
- Odkaz nebo zahrnutí reakcí na incidenty postupy platebních značek

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností vývoj IR plány zákazníků a testování, které považovat všechny ovládací prvky zákazníků týkající se sdílené touch body a všem aplikacím zákazníka využívání infrastruktury Azure a. Je zodpovědností zákazníka poskytnout přesná kontaktní informace do Azure, v případě incidentu je třeba ohlásit k nim, který může mít vliv na jejich aplikace nebo data.|



### <a name="pci-dss-requirement-12102"></a>PCI DSS požadavek 12.10.2

**12.10.2** zkontrolovat a otestovat plánu, včetně všech elementů uvedené v požadavek 12.10.1, nejméně jednou ročně.


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností vývoj IR plány zákazníků a testování, které považovat všechny ovládací prvky zákazníků týkající se sdílené touch body a všem aplikacím zákazníka využívání infrastruktury Azure a. Je zodpovědností zákazníka poskytnout přesná kontaktní informace do Azure, v případě incidentu je třeba ohlásit k nim, který může mít vliv na jejich aplikace nebo data.|



### <a name="pci-dss-requirement-12103"></a>PCI DSS požadavek 12.10.3

**12.10.3** určit konkrétní pracovníky být k dispozici na základě 24 hodin denně 7 reagovat na výstrahy.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností vývoj IR plány zákazníků a testování, které považovat všechny ovládací prvky zákazníků týkající se sdílené touch body a všem aplikacím zákazníka využívání infrastruktury Azure a. Je zodpovědností zákazníka poskytnout přesná kontaktní informace do Azure, v případě incidentu je třeba ohlásit k nim, který může mít vliv na jejich aplikace nebo data.|



### <a name="pci-dss-requirement-12104"></a>PCI DSS požadavek 12.10.4

**12.10.4** poskytnout příslušné školení zaměstnancům porušení zabezpečení odpovědnosti odpovědi.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností vývoj IR plány zákazníků a testování, které považovat všechny ovládací prvky zákazníků týkající se sdílené touch body a všem aplikacím zákazníka využívání infrastruktury Azure a. Je zodpovědností zákazníka poskytnout přesná kontaktní informace do Azure, v případě incidentu je třeba ohlásit k nim, který může mít vliv na jejich aplikace nebo data.|



### <a name="pci-dss-requirement-12105"></a>PCI DSS požadavek 12.10.5

**12.10.5** zahrnují výstrahy z monitorování, mimo jiné k zjišťování neoprávněných vniknutí, prevence vniknutí, brány firewall, včetně a integrity souborů monitorování systémy zabezpečení.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností vývoj IR plány zákazníků a testování, které považovat všechny ovládací prvky zákazníků týkající se sdílené touch body a všem aplikacím zákazníka využívání infrastruktury Azure a. Je zodpovědností zákazníka poskytnout přesná kontaktní informace do Azure, v případě incidentu je třeba ohlásit k nim, který může mít vliv na jejich aplikace nebo data.|



### <a name="pci-dss-requirement-12106"></a>PCI DSS požadavek 12.10.6

**12.10.6** vyvíjet proces upravit a momentální plánu reakcí na incidenty podle poznatky získané a začlenit vývoji odvětví.

**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Je zodpovědností vývoj IR plány zákazníků a testování, které považovat všechny ovládací prvky zákazníků týkající se sdílené touch body a všem aplikacím zákazníka využívání infrastruktury Azure a. Je zodpovědností zákazníka poskytnout přesná kontaktní informace do Azure, v případě incidentu je třeba ohlásit k nim, který může mít vliv na jejich aplikace nebo data.|



## <a name="pci-dss-requirement-1211"></a>PCI DSS požadavek čísla 12.11

**Čísla 12.11** **další požadavek pro poskytovatele služeb pouze:** provést recenze alespoň čtvrtletně potvrďte pracovníky splňují zásady zabezpečení a provozních postupů.
Recenze musí zahrnovat následující procesy:
- Zkontroluje denní protokolu
- Recenze sady pravidel brány firewall
- Použití normami konfigurace novými systémy
- Zpracování výstrah zabezpečení
- Procesy správy změn 

> [!NOTE]
> Tento požadavek je osvědčeným postupem až 31. ledna 2018, po které bude požadavek.


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci, kteří jsou poskytovatelé jsou zodpovědný za dokumentaci jejich recenze procesy pro potvrzení PCI dodržování předpisů řízení výkonu.|



### <a name="pci-dss-requirement-12111"></a>PCI DSS požadavek 12.11.1

**12.11.1** další požadavek pro poskytovatele služeb pouze: udržovat dokumentaci čtvrtletně kontrolním procesu zahrnout:
- Dokumentace výsledky recenze
- Přečtěte si a podpisu výsledků pracovníky přiřazené odpovědnost za programu PCI DSS dodržování předpisů 

> [!NOTE]
> Tento požadavek je osvědčeným postupem až 31. ledna 2018, po které bude požadavek.


**Odpovědnosti:&nbsp;&nbsp;`Customer Only`**

|||
|---|---|
| **Provider<br />(Microsoft&nbsp;Azure)** | Není k dispozici. |
| **Zákazník<br />(PCI&#8209;DSS&nbsp;plán, podle kterého)** | Zákazníci, kteří jsou poskytovatelé jsou zodpovědný za dokumentaci jejich recenze procesy pro potvrzení PCI dodržování předpisů řízení výkonu.|




