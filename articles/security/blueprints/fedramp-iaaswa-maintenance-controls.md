---
title: "Zabezpečení Azure a dodržování předpisů plán, podle kterého - FedRAMP webové aplikace automatizace - údržby"
description: "FedRAMP webové aplikace automatizace - údržby"
services: security
documentationcenter: na
author: jomolesk
manager: mbaldwin
editor: tomsh
ms.assetid: 53acae01-bea9-4570-a9bc-734ff65262ba
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/08/2018
ms.author: jomolesk
ms.openlocfilehash: de7dd5b4651f7f74d90d9d026af71cd676c720e6
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2018
---
# <a name="maintenance-ma"></a>Údržby (MA)

> [!NOTE]
> Tyto ovládací prvky jsou definovány NIST a USA Ministerstvo obchodu jako součást speciální publikace NIST 800-53 revize 4. Naleznete NIST 800-53 Rev. 4 informace o testování postupy a pokyny pro každý ovládací prvek.

## <a name="nist-800-53-control-ma-1"></a>NIST 800-53 řízení MA-1

#### <a name="system-maintenance-policy-and-procedures"></a>Zásady údržby systému a postupy

**MA-1** organizace sama vyvinula, dokumenty a šíří do [přiřazení: organizace definované pracovníky nebo rolí] zásadu údržby systému, která řeší účel, oboru, role, odpovědnosti, správu závazků, spolupráce mezi organizační entity a dodržování předpisů; a postupy pro usnadnění provádění údržby zásady systému a ovládací prvky přidružené systému údržby; kontroluje a aktualizuje aktuální zásady údržby systému [přiřazení: organizace definované frekvence]; a postupy údržby systému [přiřazení: organizace definované frekvence].

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zásady údržby systému úrovni celého podniku a postupy zákazníka může být dostatečná k vyřešení tohoto ovládacího prvku. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-2a"></a>NIST 800-53 řízení MA-2.a

#### <a name="controlled-maintenance"></a>Řízené údržby

**MA 2.a** organizace plánuje, provede, dokumenty a zkontroluje záznamy o údržbu a opravy na informace o součástech systému v souladu se specifikací výrobce nebo dodavatele nebo požadavků organizace.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za řízené údržby. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-2b"></a>NIST 800-53 řízení MA-2.b

#### <a name="controlled-maintenance"></a>Řízené údržby

**MA 2.b** organizace schvalovat a monitoruje všechny aktivity údržby, zda provést v lokalitě, nebo vzdáleně a jestli je zařízení servis v lokalitě nebo odebrat do jiného umístění.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za řízené údržby. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-2c"></a>NIST 800-53 řízení MA-2.c

#### <a name="controlled-maintenance"></a>Řízené údržby

**MA 2.c** organizace vyžaduje, aby [přiřazení: organizace definované pracovníky nebo rolí] explicitně schválit odebrání informace systému nebo součástí systému od organizace zařízení pro odlehlého údržby nebo oprav.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure vyžaduje, aby tuto vlastnost prostředky (například síťové zařízení nebo serveru) vyžadující přenos mimo pracoviště mít schválení vlastníka explicitní asset. |


 ## <a name="nist-800-53-control-ma-2d"></a>NIST 800-53 řízení MA-2.d

#### <a name="controlled-maintenance"></a>Řízené údržby

**MA 2.d** organizace upraví zařízení k odebrání všech informací z přidružených média před odebrání od organizace zařízení pro odlehlého údržby nebo oprav.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure Asset ochrany standardní definuje asset zpracování opatření, vyžaduje se pro přenosy mimo pracoviště prostředků. Standardní ochrany Asset vyžaduje, aby prostředky úložiště dat v souladu s SP NIST 800-88, pokyny pro čištění média vymazán nebo vymazat před opuštění datového centra. |


 ## <a name="nist-800-53-control-ma-2e"></a>NIST 800-53 řízení MA-2.e

#### <a name="controlled-maintenance"></a>Řízené údržby

**MA-2.e** Organizace kontroluje všechny kontrolní mechanismy zabezpečení potenciálně ovlivněné ověřit, zda ovládací prvky jsou stále funguje správně následující akce údržby nebo oprava.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za řízené údržby. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-2f"></a>NIST 800-53 řízení MA-2.f

#### <a name="controlled-maintenance"></a>Řízené údržby

**MA 2.f** organizace zahrnuje [přiřazení: organizace definované informace týkající se údržby] v záznamech organizační údržby.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za řízené údržby. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ma-2-2a"></a>.A NIST 800-53 řízení MA-2 (2)

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Řídí údržby | Automatické údržby aktivity

**MA-2 (2) .a** organizace využívá automatizované mechanismy pro plán, chování a dokumentu údržbu a opravy.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za automatizaci údržby aktivity. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ma-2-2b"></a>.B NIST 800-53 řízení MA-2 (2)

#### <a name="controlled-maintenance--automated-maintenance-activities"></a>Řídí údržby | Automatické údržby aktivity

**MA-2 (2) .b** organizace vytváří aktuální, přesné, a dokončení zaznamenává všechny akce údržby a opravu požadovali, naplánované v procesu a byla dokončena.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za automatizaci údržby aktivity. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-3"></a>NIST 800-53 řízení MA – 3

#### <a name="maintenance-tools"></a>Nástroje údržby

**MA 3** organizace schválí, ovládací prvky a sleduje informace nástroje pro údržbu systému.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure využívá několik nástrojů k dokončení údržby. Údržba softwaru nástroje jsou schváleny, řídit a udržují prostřednictvím Microsoft Azure změnit a verzí procesu. <br /> Tým služby lokality udržuje inventář schválené údržby nástroje pro použití v rámci datového centra (viz MA-3). Při použití nástroje pro zadaný údržby jsou směrované pracovníky údržby. Schválení správy Datacenter je vyžadován při použití nástroje není poskytovaný datového centra. Fyzické ruční nástroje (šroubováky, šrouby atd.) se nevztahují tohoto ovládacího prvku. <br /> Každé zařízení obsahuje pole s omezeným přístupem fyzické uzamčení nebo řízený přístup prostor pro ukládání specializované údržby nástrojů, jako je například fluke ether obory, fluke fiber channel testery, Ethernet tonery atd. Tým služby lokality provádí kontroly rutiny inventáře ověření stavu všechny nástroje. Přístup k uzamčení pole nebo údržby prostor úložiště je sledovat přístup oznámení "BADGE" čtečky protokolů, které jsou k dispozici v případě šetření. |


 ### <a name="nist-800-53-control-ma-3-1"></a>NIST 800-53 řízení MA-3 (1)

#### <a name="maintenance-tools--inspect-tools"></a>Nástroje údržby | Kontrola nástroje

**MA-3 (1)** organizace zkontroluje nástroje údržby přenášejí do zařízení, Údržba pracovníky nesprávná nebo neoprávněné změny.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Tým služby webu Microsoft Azure udržuje inventář schválené údržby nástroje pro použití v rámci datového centra (Další podrobnosti najdete v části MA-3). Při použití nástroje pro zadaný údržby jsou směrované pracovníky údržby. Schválení DCM potřebné k použití nástroje není poskytovaný datového centra. |


 ### <a name="nist-800-53-control-ma-3-2"></a>NIST 800-53 řízení MA-3 (2)

#### <a name="maintenance-tools--inspect-media"></a>Nástroje údržby | Zkontrolujte média

**MA-3 (2)** organizace kontroluje médium obsahující diagnostické a testovací programy pro škodlivý kód před média se používají v systému informace.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure zakáže použití mobilních computing nebo úložná média v provozním prostředí datových center Microsoft Azure bez schválení správy datacenter. Použití soukromých média je zakázáno používá v provozním prostředí datových center Microsoft Azure. <br /> Microsoft Azure implementovala proces kontrola přenosné počítače před používá v provozním prostředí datových center Microsoft Azure. Zabezpečení osob probíhá trénink na ověřovací pracovníky ověřte, zda mají přenosné prošly a předán kontroly pomocí přenosné počítače v produkčním prostředí. |


 ### <a name="nist-800-53-control-ma-3-3"></a>NIST 800-53 řízení MA-3 (3)

#### <a name="maintenance-tools--prevent-unauthorized-removal"></a>Nástroje údržby | Zabránit neoprávněným odebrání

**MA-3 (3)** organizace brání neoprávněným odebrání zařízení údržby ověření, že je žádné organizační informace obsažené na zařízení; úpravě nebo zničení obsahující organizační údaje zařízení; zachování zařízení v rámci služby. nebo získání výjimku z [přiřazení: organizace definované pracovníky nebo rolí] explicitně autorizace odebrání zařízení ze zařízení.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Microsoft Azure aktivuje datacenter konkrétní údržby nástroje, které jsou uchovány v zařízení a při tom odebrány nejsou. Každé zařízení obsahuje s omezeným přístupem fyzické zámku pole nebo úložiště místnosti uchovávající údržby nástroje, například obory ether fluke, fluke fiber channel testery, Ethernet tonery atd. Přístup je řízen do prostoru uzamčení pole nebo úložiště v DCAT zakázat neoprávněného přístupu k použití nástrojů pro správu. <br /> Organizace je chráněný během údržby ovládacích prvků v MA – 4. Pro přístup k informacím organizace, musí uživatel privilegovaný účty a ověřovací data. |


 ## <a name="nist-800-53-control-ma-4a"></a>NIST 800-53 řízení MA-4.a

#### <a name="nonlocal-maintenance"></a>Příchozí data údržby

**MA 4.a** organizace schvalovat a monitoruje nemístních údržby a diagnostiky aktivity.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za provádění údržby není místní zákazníka nasazení operačních systémů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-4b"></a>NIST 800-53 řízení MA-4.b

#### <a name="nonlocal-maintenance"></a>Příchozí data údržby

**MA 4.b** organizace dovoluje nemístních údržby a diagnostických nástrojů pouze jako konzistentní s organizační zásady a popsané v plánu zabezpečení pro systém informace.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za provádění údržby není místní zákazníka nasazení operačních systémů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-4c"></a>NIST 800-53 řízení MA-4.c

#### <a name="nonlocal-maintenance"></a>Příchozí data údržby

**MA 4.c** organizace využívá silné ověřovací data v zařízení, které nejsou místní údržby a diagnostiky relací.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za provádění údržby není místní zákazníka nasazení operačních systémů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-4d"></a>NIST 800-53 řízení MA-4.d

#### <a name="nonlocal-maintenance"></a>Příchozí data údržby

**MA 4.d** organizace udržuje záznamy pro příchozí data údržby a diagnostiky aktivity.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za provádění údržby není místní zákazníka nasazení operačních systémů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-4e"></a>NIST 800-53 řízení MA-4e

#### <a name="nonlocal-maintenance"></a>Příchozí data údržby

**MA-4.e** Organizace ukončí relace a připojení k síti po dokončení příchozí data údržby.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za provádění údržby není místní zákazníka nasazení operačních systémů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ma-4-2"></a>NIST 800-53 řízení MA-4 (2)

#### <a name="nonlocal-maintenance--document-nonlocal-maintenance"></a>Příchozí data údržby | Příchozí data údržby dokumentu

**MA-4 (2)** dokumenty organizace v plánu zabezpečení pro informace o systému, zásady a postupy pro vytvoření a používání diagnostiky připojení a které nejsou místní údržby.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědný za dokumentaci plánu zabezpečení pro zákazníka nasadit operační systémy jiné než místní údržby. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ma-4-3"></a>NIST 800-53 řízení MA-4 (3)

#### <a name="nonlocal-maintenance--comparable-security--sanitization"></a>Příchozí data údržby | Porovnatelný z hlediska zabezpečení nebo čištění

**MA-4 (3)** organizace vyžaduje, aby z informace o systému, který implementuje zabezpečení schopností, který je porovnatelný z hlediska schopnosti implementované v systému probíhá údržba; nebo odebere provést nemístních údržby a diagnostické služby součást na obsloužení ze systému informace před nemístních údržby nebo diagnostické služby, upraví komponentu (s ohledem na organizace) před odebrání od zařízení, organizace, a poté, co je služba provést, zkontroluje a upraví komponentu (s ohledem na potenciálně škodlivého softwaru) před novým připojením součást do systému informace.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za provádění údržby všechny jiné než místní zákazníka nasazení operačních systémů z informace o systému, který je porovnatelný z hlediska zabezpečení. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ma-4-6"></a>NIST 800-53 řízení MA-4 (6)

#### <a name="nonlocal-maintenance--cryptographic-protection"></a>Příchozí data údržby | Kryptografické ochrany

**MA-4 (6)** systém informace implementuje kryptografických mechanismů k ochraně integrity a důvěrnosti nemístních údržby a diagnostiky komunikace.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Zákazník je zodpovědná za implementaci kryptografických mechanismů při provádění údržby není místní a Diagnostika zákazníka nasazení operačních systémů. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-5a"></a>NIST 800-53 řízení MA-5.a

#### <a name="maintenance-personnel"></a>Pracovníky údržby

**MA 5.a** organizace proces autorizace pracovníky údržby vytváří a udržuje seznam autorizovaných údržby organizace nebo pracovníky.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Chcete-li vyřešit tento ovládací prvek může být zákazníka podnikové úrovni systému údržby pracovníky autorizace a doprovodu procesy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-5b"></a>NIST 800-53 řízení MA-5.b

#### <a name="maintenance-personnel"></a>Pracovníky údržby

**MA 5.b** organizace zajistí, že-vyvedena pracovníky provádění údržby systému informace mají požadovaná oprávnění přístupu.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Chcete-li vyřešit tento ovládací prvek může být zákazníka podnikové úrovni systému údržby pracovníky autorizace a doprovodu procesy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-5c"></a>NIST 800-53 řízení MA-5.c

#### <a name="maintenance-personnel"></a>Pracovníky údržby

**MA 5.c** organizace označí organizační pracovníky s autorizací požadovaný přístup a technické způsobilosti dohlížet aktivity údržby pracovníků, kteří nemají potřebná přístupová oprávnění.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Chcete-li vyřešit tento ovládací prvek může být zákazníka podnikové úrovni systému údržby pracovníky autorizace a doprovodu procesy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ma-5-1a"></a>.A NIST 800-53 řízení MA-5 (1)

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Pracovníky údržby | Jednotlivce bez odpovídající přístup

**MA-5 (1) .a** organizace implementuje postupy pro použití údržby pracovníky, kteří nemají chráněných příslušná bezpečnostní prostorů, nebo nejsou občanům USA, které zahrnují následující pracovníky údržby požadavky, kteří mají není potřeba povolení přístupu, chráněných prostorů nebo formální přístup schválení jsou vyvedena a pod dohledem během provádění údržby a diagnostiky aktivity systému informace schválené organizační pracovníky, kteří jsou plně vymazány, mají odpovídající povolení přístupu a jsou technicky kvalifikovaný; před inicializace údržby nebo diagnostiky aktivity pracovníky kdo není musel povolení přístupu, chráněných prostorů nebo schválení přístup formální, všechny volatile informace, které jsou součástí úložišť v rámci systému informace upravený a všechny stálé úložiště média se odeberou nebo fyzicky odpojen od serveru a zabezpečené.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Chcete-li vyřešit tento ovládací prvek může být zákazníka podnikové úrovni systému údržby pracovníky autorizace a doprovodu procesy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ### <a name="nist-800-53-control-ma-5-1b"></a>.B NIST 800-53 řízení MA-5 (1)

#### <a name="maintenance-personnel--individuals-without-appropriate-access"></a>Pracovníky údržby | Jednotlivce bez odpovídající přístup

**MA-5 (1) .b** organizace vyvíjí a implementuje alternativní bezpečnostní opatření zabezpečení v případě komponentu systémové informace nelze upravený, odebrán nebo odpojen od systému.

**Odpovědnosti:**`Customer Only`

|||
|---|---|
| **Zákazníka** | Chcete-li vyřešit tento ovládací prvek může být zákazníka podnikové úrovni systému údržby pracovníky autorizace a doprovodu procesy. |
| **Zprostředkovatel (Microsoft Azure)** | Netýká se |


 ## <a name="nist-800-53-control-ma-6"></a>NIST 800-53 řízení MA-6

#### <a name="timely-maintenance"></a>Včas údržby

**MA 6** organizace získává údržby podporu nebo k výměně za chodu částí pro [přiřazení: komponent systému informace definované organizace] v [přiřazení: organizace definované časové období] selhání.

**Odpovědnosti:**`Azure Only`

|||
|---|---|
| **Zákazníka** | Zákazníci nemají fyzický přístup k všechny systémové prostředky v datových centrech Azure. |
| **Zprostředkovatel (Microsoft Azure)** | Datová centra služby Microsoft Azure udržovat trvalé údržby pracovníky k podpoře systémů infrastruktury kritické datacenter, jakož i operací datového centra. Týmy našli důležité součásti systému zabezpečení a technologie, které udržují ušetří pro dohlížející na bezpečný. Důležité systémy jsou navrženy v N + 1 konfiguracích a služby jsou navržené jako odolný. To umožňuje tým správy datacenter ke splnění cílů obnovení v případě přerušení poskytování služeb nebo pohotovostní plán situaci. Důležité informace o systémových služeb z více než jednoho datového centra, aby se zabránilo přerušení ve službě kvůli incidentu v některém z v datacentrech připravené. Zákazník aplikace jsou zodpovědní za nasazení do několik datových center k zajištění pro redundanci a odolnost proti chybám. |
