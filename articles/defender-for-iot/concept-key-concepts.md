---
title: Klíčové výhody
description: Přečtěte si o konceptech Basic Defenderu pro IoT.
ms.date: 12/13/2020
ms.topic: article
ms.openlocfilehash: ca1e5a4d8554b208f5275fd0e7519f2db3fafc08
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784743"
---
# <a name="basic-concepts"></a>Základní koncepty 

Tento článek popisuje klíčové výhody Azure Defenderu pro IoT.

## <a name="rapid-non-invasive-deployment-and-passive-monitoring"></a>Rychlé neinvazivní nasazení a pasivní monitorování

Defender pro senzory IoT se připojuje k přepínání portů SPAN (mirror) a klepnutí na síť a okamžitě zahájí shromažďování síťového provozu ICS prostřednictvím pasivního monitorování (bez agentů). Hloubková kontrola paketů (DPI) se používá k Dissect provozu ze sériového a síťového zařízení pro řízení sítě Ethernet. Defender pro IoT má nulový dopad na sítě, protože není umístěný v cestě k datům a aktivně nekontroluje zařízení. 

Aby bylo možné doručovat rychlé snímky podrobných informací o zařízeních s Windows, je možné, že se Defender for IoT monitoruje s volitelnou aktivní komponentou. Tato součást používá bezpečné příkazy schválené dodavatelem k dotazování zařízení Windows na podrobnosti o zařízení, a to často nebo často, jak potřebujete.

## <a name="embedded-knowledge-of-ics-protocols-devices-and-applications"></a>Informace o vloženém znalostech protokolů, zařízení a aplikací ICS

Samotná hodnota DPI není dostačující k identifikaci anomálií protokolu a identifikaci zařízení na podrobné úrovni. Program Defender pro IoT snímač adresuje některé z největších a nejsložitých prostředí. Více než 1 300 OT. sítí bylo analyzováno do data v rámci všech průmyslových odvětví.

## <a name="analytics-and-self-learning-engines"></a>Analýzy a moduly pro samoobslužné učení

Motory identifikují problémy zabezpečení prostřednictvím průběžného monitorování a pěti analytických modulů, které zahrnují samoobslužné učení, aby eliminoval nutnost aktualizace podpisů nebo definování pravidel. Moduly využívají analýzy chování specifické pro ICS a datové vědy k nepřetržité analýze síťového provozu pro anomálie. Pět modulů:

- **Detekce porušení protokolu**: identifikuje použití struktur paketů a hodnot polí, které porušují specifikace protokolu ICS.

- **Detekce porušení zásad**: identifikuje porušení zásad, jako je neoprávněné použití kódů funkcí, přístup ke konkrétním objektům nebo změny konfigurace zařízení.

- **Detekce průmyslových malwaru**: identifikuje chování, které indikuje přítomnost známého malwaru, jako je například Conficker, černá energie, Havex, WannaCry a NotPetya.

- **Detekce anomálií**: detekuje neobvyklá komunikace a chování z počítače na počítač (M2M). Pomocí modelování sítí ICS jako deterministické sekvence stavů a přechodů používá modul patentovou techniku s názvem průmyslové modelování konečných stavů (IFSM). Řešení vyžaduje kratší období učení než obecné matematické přístupy nebo analýzy, které byly původně vyvinuty pro IT místo z. Také detekuje anomálie rychleji, s minimálními falešně falešně pozitivními.

- **Detekce provozního incidentu**: identifikuje provozní problémy, jako je přerušované připojení, které může označovat předčasné znaménko selhání zařízení.

## <a name="network-traffic-analysis-for-risk-and-vulnerability-assessment"></a>Analýza provozu v síti pro posouzení rizik a ohrožení zabezpečení

V odvětví, který je v oboru, Defender pro IoT používá vlastní algoritmy NTA (Network Traffic Analysis) k identifikaci všech ohrožení zabezpečení sítě a koncových bodů, například:

- Neautorizovaná připojení vzdáleného přístupu
- Nepřesné nebo nedokumentované zařízení
- Slabé ověřování
- Zranitelná zařízení (na základě neopravených CVEs)
- Neoprávněné mosty mezi podsítěmi
- Slabá pravidla brány firewall

## <a name="data-mining-for-investigations-forensics-and-threat-hunting"></a>Dolování dat pro šetření, forenzní a lovecké hrozby

Platforma poskytuje intuitivní rozhraní pro dolování dat pro podrobné vyhledávání historických přenosů napříč všemi relevantními dimenzemi. Mezi příklady patří časové období, IP adresa, adresa MAC a porty. Dotazy týkající se protokolu můžete také vytvářet na základě kódů funkcí, služeb protokolů a modulů. K dispozici jsou PCAPs s plnou věrností pro další analýzu podrobností.

## <a name="sensor-cloud-management-mode"></a>Režim správy cloudu senzorů

Režim správy cloudu senzoru určuje, kde se zobrazí zařízení, upozornění a další informace, které senzor detekuje.

V případě **senzorů připojených ke cloudu** se v konzole senzorů zobrazí informace, které senzor detekuje. Informace o výstrahách se doručují prostřednictvím služby IoT Hub a můžou se sdílet s ostatními službami Azure, jako je Azure Sentinel.

Pro **místně připojené senzory** se v konzole senzorů zobrazí informace, které senzor detekuje. Informace o detekci se také sdílí s místní konzolou pro správu, pokud je senzor připojený k němu.

## <a name="air-gapped-networks"></a>Letecké sítě gapped

Pokud pracujete v prostředí AIR gapped, místní Konzola pro správu v programu Defender pro IoT přináší zobrazení klíčových ukazatelů IoT a rizikových indikátorů a výstrah v reálném čase napříč všemi vašimi zařízeními. Úzce integrovaná s pracovními postupy a sadami Runbook SOC umožňuje snadnou prioritu aktivit zmírnění a korelace mezi různými pracovišti hrozeb.  

Defender pro IoT nabízí ucelený přehled o všech vašich zařízeních. Poskytuje také důležité informace o zařízeních, jako je například typ (PLC, RTU, řadiče domény atd.), výrobce, model a úroveň revize firmwaru a také informace o výstrahách.  

Defender pro IoT umožňuje efektivní správu více nasazení a komplexní jednotný pohled na síť. Defender pro IoT optimalizuje zpracování výstrah a kontrolu nad zabezpečením provozní sítě.

Místní Konzola pro správu je webová platforma pro správu, která umožňuje monitorovat a řídit aktivity globálních instalací senzorů. Kromě správy dat přijatých od nasazených senzorů místní Konzola pro správu hladce integruje data z různých obchodních prostředků: CMDBs, DNS, brány firewall, webová rozhraní API a další.

:::image type="content" source="media/concept-air-gapped-networks/site-management-alert-screen.png" alt-text="Zobrazí se místní Konzola pro správu.":::

Než začnete pracovat s místní konzolou pro správu, doporučujeme vám seznámit se s koncepty, funkcemi a funkcemi dostupnými pro senzory.

## <a name="integrations"></a>Integrace

Můžete rozšířit možnosti programu Defender pro IoT sdílením informací o zařízení i výstrahách s partnerskými systémy. Integrace pomůžou podnikům přemostění dřív dodaných řešení zabezpečení a významně tak vylepšit viditelnost zařízení a analýzu hrozeb. Integrace také pomáhají podnikům zrychlit reakce na systém a urychlit rizika. 

Díky integraci do stávajících pracovních postupů SOC a sady zabezpečení se jejich integrace omezují na složitost a eliminují se siloy. Například:

- Systémů Siem jako IBM QRadar, Splunk, ArcSight, LogRhythm a RSA NetWitness

- Systémy Orchestrace a vytváření lístků zabezpečení, jako je ServiceNow a IBM odolný

- Zabezpečená řešení vzdáleného přístupu, jako je CyberArk Privileged Session Manager (PSM) a BeyondTrust

- Zabezpečení systémů pro řízení přístupu k síti (NAC), jako je Aruba ClearPass a Forescout

- Brány firewall jako Fortinet a Check Point

## <a name="complete-protocol-support"></a>Kompletní podpora protokolů

Kromě podpory vloženého protokolu můžete zabezpečit zařízení IoT a ICS s použitím vlastních a vlastních protokolů nebo protokolů, které se odchylují od jakéhokoli standardu. Pomocí sady SDK pro vývojové prostředí pro vývoj v horizontu můžou vývojáři vytvářet moduly plug-in, které dekódují síťový provoz na základě definovaných protokolů. Služba analyzuje provoz tak, aby poskytovala kompletní monitorování, upozorňování a vytváření sestav. Použít horizont:

- Rozbalení viditelnosti a řízení bez nutnosti upgradu na nové verze

- Zabezpečte proprietární informace vývojem na pracovišti jako externí modul plug-in.

- Lokalizace textu pro výstrahy, události a parametry protokolu.

Kromě toho můžete k sdělování informací použít speciální výstrahy protokolu:

- Zjišťování provozu založeného na protokolech a základních protokolech v modulu plug-in speciálního horizontu.

- O kombinaci polí protokolu ze všech vrstev protokolu. Například v prostředí se systémem MODBUS můžete vygenerovat výstrahu v případě, že senzor zjistí příkaz zápisu do paměti registru na konkrétní IP adrese a cíli sítě Ethernet. Nebo můžete chtít vygenerovat výstrahu, když se na konkrétní IP adresu provede nějaký přístup.

Výstrahy se aktivují, když jsou splněné podmínky pro pravidlo upozornění horizontu.

Kromě toho můžete při práci s vlastními výstrahami na horizontu napsat vlastní názvy a zprávy výstrah. Do textu zprávy výstrahy lze vložit přeložená pole a hodnoty protokolu.

Když použijete vlastní, aktivovaná upozornění založená na podmínkách a zasílání zpráv pomáhají určit konkrétní síťovou aktivitu a efektivně aktualizovat vaše zabezpečení, IT a provozní týmy.


## <a name="high-availability"></a>Vysoká dostupnost

Pomocí instalace zařízení s vysokou dostupností v místní konzole pro správu Zvyšte odolnost svého programu Defender pro nasazení IoT. Nasazení s vysokou dostupností zajišťuje, aby se spravované senzory průběžně hlásily k aktivní místní konzole pro správu.

Toto nasazení je implementováno s dvojicí místní konzoly pro správu, která zahrnuje primární a sekundární zařízení.

## <a name="localization"></a>Lokalizace

Mnoho funkcí konzoly podporuje širokou škálu jazyků.

## <a name="next-step"></a>Další krok

[Začínáme s Defenderem pro IoT](getting-started.md)
