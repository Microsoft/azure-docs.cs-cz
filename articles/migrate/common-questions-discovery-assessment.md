---
title: Dotazy týkající se zjišťování, hodnocení a analýzy závislostí v Azure Migrate
description: Získejte odpovědi na běžné dotazy týkající se zjišťování, hodnocení a analýzy závislostí v Azure Migrate.
ms.topic: conceptual
ms.date: 06/09/2020
ms.openlocfilehash: cb1696c521f436280177f0263abd66aa2bfed7dc
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92312923"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Analýzy zjišťování, hodnocení a závislostí – běžné otázky

Tento článek obsahuje odpovědi na běžné dotazy týkající se zjišťování, hodnocení a analýzy závislostí v Azure Migrate. Pokud máte další otázky, ověřte tyto prostředky:

- [Obecné otázky](resources-faq.md) týkající se Azure Migrate
- Dotazy týkající se [zařízení Azure Migrate](common-questions-appliance.md)
- Dotazy týkající se [migrace serveru](common-questions-server-migration.md)
- Získání otázek zodpovězených ve [fóru Azure Migrate](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Jaké geografické oblasti jsou podporovány pro zjišťování a hodnocení pomocí Azure Migrate?

Projděte si podporované oblasti pro [veřejný cloud](migrate-support-matrix.md#supported-geographies-public-cloud) a [cloud pro státní správu](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Kolik virtuálních počítačů je možné zjistit pomocí zařízení?

Můžete zjistit až 10 000 virtuálních počítačů VMware, až 5 000 virtuálních počítačů Hyper-V a až 1000 fyzických serverů pomocí jediného zařízení. Pokud máte více počítačů, přečtěte si o [škálování posouzení technologie Hyper-V](scale-hyper-v-assessment.md), [škálování vyhodnocení VMware](scale-vmware-assessment.md)nebo [škálování posouzení fyzického serveru](scale-physical-assessment.md).

## <a name="how-do-i-choose-the-assessment-type"></a>Jaký zvolit typ hodnocení?

- **Posouzení virtuálních počítačů Azure** použijte, když chcete vyhodnotit místní [virtuální počítače VMware](how-to-set-up-appliance-vmware.md), [virtuální počítače Hyper-V](how-to-set-up-appliance-hyper-v.md)a [fyzické servery](how-to-set-up-appliance-physical.md) pro migraci na virtuální počítače Azure. [Další informace](concepts-assessment-calculation.md)

- Posouzení **Řešení Azure VMware (AVS)** použijte, když chcete vyhodnotit místní [virtuální počítače VMware](how-to-set-up-appliance-vmware.md) pro migraci do [Řešení Azure VMware (AVS)](../azure-vmware/introduction.md) pomocí tohoto typu posouzení. [Další informace](concepts-azure-vmware-solution-assessment-calculation.md)

- Ke spuštění obou typů hodnocení můžete použít společnou skupinu pouze s počítači VMware. Poznámka: Pokud ve službě Azure Migrate spouštíte hodnocení služby AVS poprvé, doporučujeme vytvořit novou skupinu počítačů VMware.
 

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>Proč v sestavě posouzení chybí data o výkonu některých nebo všech virtuálních počítačů?

Pokud zařízení Azure Migrate nemůže shromáždit údaje o výkonu místních virtuálních počítačů, v sestavě posouzení na základě výkonu se zobrazí PercentageOfCoresUtilizedMissing nebo PercentageOfMemoryUtilizedMissing. Zkontrolujte následující:

- Byly virtuální počítače po dobu trvání, pro kterou vytváříte posouzení, zapnuté?
- Pokud chybí pouze čítače paměti a pokoušíte se posoudit virtuální počítače Hyper-V, zkontrolujte, jestli je na těchto virtuálních počítačích povolená dynamická paměť. V současné době existuje známý problém, kvůli kterému zařízení Azure Migrate nemůže shromáždit data o využití paměti takových virtuálních počítačů.
- Pokud chybí všechny čítače výkonu, ujistěte se, že jsou povolená odchozí připojení na portu 443 (HTTPS).

Poznámka: Pokud jakýkoli čítač výkonu chybí, nástroj Azure Migrate: Hodnocení serverů se vrátí zpět k přiděleným jádrům a paměti v místním prostředí a na jejich základě doporučí velikost virtuálního počítače.

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>Proč je míra spolehlivosti mého hodnocení nízká?

Míra spolehlivosti posouzení na základě výkonu se počítá na základě procentuální hodnoty [dostupných datových bodů](./concepts-assessment-calculation.md#ratings) potřebných k výpočtu posouzení. Níže jsou uvedené důvody, proč k hodnocení může být přidělená nízká míra spolehlivosti:

- Neprofilovali jste své prostředí po dobu trvání, pro kterou vytváříte interní hodnocení. Například pokud vytváříte posouzení s dobou výkonu nastavenou na jeden týden, budete muset počkat alespoň jeden týden po spuštění zjišťování, aby se shromáždily všechny datové body. Pokud tuto dobu nemůžete počkat, snižte dobu výkonu a přepočítejte posouzení.
 
- Nástroj Hodnocení serverů nemůže shromáždit údaje o výkonu některých nebo všech virtuálních počítačů v daném období posouzení. Zkontrolujte, jestli byly virtuální počítače po dobu trvání posouzení zapnuté a jestli jsou povolená odchozí připojení na portu 443. U virtuálních počítačů Hyper-V platí, že pokud je povolená dynamická paměť, čítače paměti budou chybět, což povede ke snížení míry spolehlivosti. Přepočítejte posouzení, aby se projevily poslední změny míry spolehlivosti. 

- Po zahájení zjišťování v nástroji Hodnocení serverů se vytvořilo několik virtuálních počítačů. Například pokud vytváříte posouzení historie výkonu za poslední měsíc, ale před týdnem se v prostředí vytvořilo několik virtuálních počítačů. V takovém případě nebudou k dispozici data o výkonu nových virtuálních počítačů za celou dobu trvání a míra spolehlivosti bude nízká.

[Další informace](./concepts-assessment-calculation.md#confidence-ratings-performance-based) o míře spolehlivosti

## <a name="i-cant-see-some-groups-when-i-am-creating-an-azure-vmware-solution-avs-assessment"></a>Při vytváření posouzení řešení Azure VMware (AVS) nejde zobrazit některé skupiny

- Hodnocení služby AVS je možné provádět pouze pro skupiny, které obsahují pouze počítače VMware. Pokud chcete provést hodnocení služby AVS, odeberte ze skupiny všechny jiné počítače než VMware.
- Pokud ve službě Azure Migrate spouštíte hodnocení služby AVS poprvé, doporučujeme vytvořit novou skupinu počítačů VMware.

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>V Azure Government nevidím některé typy virtuálních počítačů.

Typy virtuálních počítačů podporované pro posouzení a migraci závisí na dostupnosti v umístění Azure Government. Můžete [zkontrolovat a porovnat](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) typy virtuálních počítačů v Azure Government.

## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>Změnila se velikost virtuálního počítače. Můžu znovu spustit posouzení?

Zařízení Azure Migrate průběžně shromažďuje informace o místním prostředí.  Posouzení je snímkem v čase místních virtuálních počítačů. Pokud změníte nastavení na virtuálním počítači, který chcete vyhodnotit, použijte k aktualizaci posouzení nejnovější změny pomocí možnosti Přepočítat.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Návody zjistit virtuální počítače ve víceklientském prostředí?

- **VMware**: Pokud je prostředí sdílené mezi klienty a nechcete zjistit virtuální počítače tenanta v rámci předplatného jiného tenanta, vytvořte vCenter Server přihlašovací údaje VMware, které budou mít přístup jenom k virtuálním počítačům, které chcete zjišťovat. Pak tyto přihlašovací údaje použijte při zahájení zjišťování v zařízení Azure Migrate.
- **Hyper-v**: zjišťování používá přihlašovací údaje hostitele Hyper-v. Pokud virtuální počítače sdílejí stejného hostitele Hyper-V, není v současnosti žádný způsob, jak zjišťování oddělit.  

## <a name="do-i-need-vcenter-server"></a>Potřebuji vCenter Server?

Ano, Azure Migrate vyžaduje vCenter Server v prostředí VMware, aby bylo možné provést zjišťování. Azure Migrate nepodporuje zjišťování hostitelů ESXi, které nejsou spravované pomocí vCenter Server.

## <a name="what-are-the-sizing-options-in-an-azure-vm-assessment"></a>Jaké jsou možnosti změny velikosti ve vyhodnocování virtuálních počítačů Azure?

Při změně velikosti v místním prostředí Azure Migrate nepovažují údaje o výkonu virtuálních počítačů za účelem posouzení. Azure Migrate vyhodnocuje velikosti virtuálních počítačů na základě místních konfigurací. Při změně velikosti na základě výkonu je velikost založena na datech využití.

Například pokud má místní virtuální počítač čtyři jádra a 8 GB paměti v 50% využití CPU a 50% využití paměti:
- Při změně velikosti v místním prostředí se doporučuje použít SKU virtuálního počítače Azure se čtyřmi jádry a 8 GB paměti.
- Velikost na základě výkonu doporučí SKU virtuálních počítačů, které mají dvě jádra a 4 GB paměti, protože je zváženo procento využití.

Podobně platí, že velikost disků závisí na kritériích velikosti a typu úložiště:
- Pokud jsou kritéria změny velikosti založená na výkonu a typ úložiště je automatický, Azure Migrate při určení typu cílového disku (Standard nebo Premium) převezme hodnoty IOPS a propustnosti disku do účtu.
- Pokud jsou kritéria změny velikosti v závislosti na výkonu a typ úložiště je Premium, Azure Migrate doporučuje SKU na disku úrovně Premium na základě velikosti místního disku. Stejná logika se aplikuje na velikost disku, pokud je velikost v místním prostředí a typ úložiště je Standard nebo Premium.

## <a name="does-performance-history-and-utilization-affect-sizing-in-an-azure-vm-assessment"></a>Má historie a využití výkonu vliv na změnu velikosti v posouzení virtuálních počítačů Azure?

Ano, historie výkonu a využití ovlivňují změnu velikosti v posouzení virtuálního počítače Azure.

### <a name="performance-history"></a>Historie výkonu

V případě změny velikosti založené na výkonu Azure Migrate shromažďuje historii výkonu místních počítačů a pak ji používá k doporučení velikosti virtuálního počítače a typu disku v Azure:

1. Zařízení nepřetržitě profiluje místní prostředí za účelem shromažďování dat o využití v reálném čase každých 20 sekund.
2. Zařízení shrnuje shromážděné 20 vzorků a používá je k vytvoření jednoho datového bodu každých 15 minut.
3. Pokud chcete vytvořit datový bod, zařízení vybere nejvyšší hodnotu ze všech ukázek s 20 sekundami.
4. Zařízení odešle datový bod do Azure.

### <a name="utilization"></a>Využití

Když v Azure vytvoříte posouzení v závislosti na době trvání a hodnotě percentilu historie výkonu, která je nastavená, Azure Migrate vypočítá efektivní hodnotu využití a pak ji použije ke změně velikosti.

Například pokud nastavíte dobu trvání na jeden den a percentil hodnoty na 95. percentil, Azure Migrate seřadí vzorové body na 15 minut odesílané kolektorem za poslední den ve vzestupném pořadí. Jako efektivní využití se vybere hodnota 95. percentilu.

Použití hodnoty 95. percentil zajistí, že se podhodnoty ignorují. Pokud Azure Migrate používá percentil 99, mohou být mimo jiné zahrnuté odlehlé hodnoty. Pokud chcete vybrat špičku v období bez chybějících hodnot, nastavte Azure Migrate, aby se 99 percentil používal.


## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Jak jsou vyhodnocování na základě importu odlišná od posouzení se zdrojem zjišťování jako zařízením?

Posouzení virtuálních počítačů Azure založené na importech jsou vyhodnocení vytvořená pomocí počítačů, které jsou naimportovány do Azure Migrate pomocí souboru CSV. Pro import jsou povinná jenom čtyři pole: název serveru, jádra, paměť a operační systém. Všimněte si následujících věcí: 
 - Kritéria připravenosti jsou méně přísná v hodnoceních na základě importu u parametru typu spuštění. Pokud typ spuštění není zadaný, předpokládá se, že počítač má typ spouštění BIOS a počítač není označený jako **podmíněně připravený**. V části posouzení se zdrojem zjišťování jako zařízením je připravenost označena jako **podmíněně připravena** , pokud chybí typ spuštění. Tento rozdíl ve výpočtu připravenosti je, protože uživatelé nemusí mít po dokončení vyhodnocení na základě importu k dispozici všechny informace o počítačích v počátečních fázích plánování migrace. 
 - Posouzení importu na základě výkonu používá hodnotu využití poskytovanou uživatelem pro výpočty podle správné velikosti. Vzhledem k tomu, že je hodnota využití poskytovaná uživatelem, jsou možnosti využití **Historie výkonu** a **percentilu** ve vlastnostech posouzení zakázané. Ve vyhodnocování se zdrojem zjišťování jako zařízením je zvolená hodnota percentilu převzata z údajů o výkonu shromážděných zařízením.

## <a name="why-is-the-suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>Proč je navrhovaný Nástroj pro migraci v rámci vyhodnocení pro funkci AVS na základě importu označený jako neznámý?

V případě počítačů importovaných prostřednictvím souboru CSV není výchozí nástroj pro migraci v posouzení služby AVS známý. U počítačů VMware se ale doporučuje použít řešení VMware Hybrid Cloud Extension (HCX). [Další informace](../azure-vmware/tutorial-deploy-vmware-hcx.md)


## <a name="what-is-dependency-visualization"></a>Co je Vizualizace závislostí?

Vizualizace závislostí vám může posuzovat skupiny virtuálních počítačů, které se budou migrovat s větší jistotou. Vizualizace závislostí křížově kontroluje závislosti počítačů před spuštěním posouzení. Pomáhá zajistit, aby nic nezůstalo a při migraci do Azure pomáhá zabránit neočekávanému výpadku. Azure Migrate používá řešení Service Map v Azure Monitor k povolení Vizualizace závislostí. [Další informace](concepts-dependency-visualization.md).

> [!NOTE]
> Analýza závislostí na základě agenta není v Azure Government k dispozici. Můžete použít analýzu závislostí bez agentů.

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Jaký je rozdíl mezi agenty a bez agenta?

Rozdíly mezi vizualizacemi bez agentů a vizualizací na základě agentů jsou shrnuty v tabulce.

**Požadavek** | **Bez agenta** | **Založené na agentovi**
--- | --- | ---
Podpora | Tato možnost je momentálně ve verzi Preview a je dostupná jenom pro virtuální počítače VMware. [Zkontrolujte](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) podporované operační systémy. | Obecně dostupná (GA).
Agent | Není nutné instalovat agenty na počítačích, které chcete křížově kontrolovat. | Agenti, kteří se mají nainstalovat na každý místní počítač, který chcete analyzovat: [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md)a [Agent závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent). 
Předpoklady | [Projděte si](concepts-dependency-visualization.md#agentless-analysis) požadavky a požadavky na nasazení. | [Projděte si](concepts-dependency-visualization.md#agent-based-analysis) požadavky a požadavky na nasazení.
Log Analytics | Nepožadováno. | Azure Migrate používá řešení [Service map](../azure-monitor/insights/service-map.md) v [protokolech Azure monitor](../azure-monitor/log-query/log-query-overview.md) pro vizualizaci závislostí. [Další informace](concepts-dependency-visualization.md#agent-based-analysis).
Jak to funguje | Zachycuje data připojení TCP na počítačích, které jsou povoleny pro vizualizaci závislostí. Po zjištění se data shromáždí v intervalech po pěti minutách. | Agenti Service Map nainstalovaná na počítači shromažďují data o procesech TCP a příchozích a odchozích připojeních pro jednotlivé procesy.
Data | Název zdrojového počítačového serveru, proces, název aplikace<br/><br/> Název cílového počítačového serveru, proces, název aplikace a port. | Název zdrojového počítačového serveru, proces, název aplikace<br/><br/> Název cílového počítačového serveru, proces, název aplikace a port.<br/><br/> Pro Log Analytics dotazy se shromažďují a k dispozici informace o počtu připojení, latenci a přenosu dat. 
Vizualizace | Mapa závislostí jednoho serveru se dá zobrazit po dobu od 1 hodiny do 30 dnů. | Mapa závislostí pro jeden server.<br/><br/> Mapu lze zobrazit pouze za hodinu.<br/><br/> Mapa závislostí skupiny serverů.<br/><br/> Přidejte nebo odeberte servery ve skupině z zobrazení mapy.
Export dat | Posledních 30 dní data je možné stáhnout ve formátu CSV. | Data se dají dotazovat pomocí Log Analytics.


## <a name="do-i-need-to-deploy-the-appliance-for-agentless-dependency-analysis"></a>Potřebuji nasadit zařízení pro analýzu závislostí bez agenta?

Ano, [zařízení Azure Migrate](migrate-appliance.md) musí být nasazeno.

## <a name="do-i-pay-for-dependency-visualization"></a>Platíte za vizualizaci závislostí?

Ne. Přečtěte si další informace o [cenách Azure Migrate](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Co mám nainstalovat pro vizualizaci závislostí založenou na agentech?

Chcete-li použít vizualizaci závislostí založenou na agentech, Stáhněte a nainstalujte agenty na každý místní počítač, který chcete vyhodnotit:

- [Microsoft Monitoring Agent (MMA)](../azure-monitor/platform/agent-windows.md)
- [Agent závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Pokud máte počítače, které nemají připojení k Internetu, Stáhněte a nainstalujte na ně bránu Log Analytics.

Tyto agenty potřebujete jenom v případě, že používáte vizualizaci závislostí založenou na agentech.

## <a name="can-i-use-an-existing-workspace"></a>Můžu použít stávající pracovní prostor?

Ano, pro vizualizaci závislostí založenou na agentech můžete existující pracovní prostor připojit k projektu migrace a použít ho pro vizualizaci závislostí. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Můžu exportovat sestavu vizualizace závislostí?

Ne, sestavu vizualizace závislosti v rámci vizualizace založenou na agentech nelze exportovat. Azure Migrate však používá Service Map a můžete použít [REST API Service map](/rest/api/servicemap/machines/listconnections) k načtení závislostí ve formátu JSON.

## <a name="can-i-automate-agent-installation"></a>Můžu automatizovat instalaci agenta?

Pro vizualizaci závislostí založenou na agentech:

- Pomocí [skriptu nainstalujte agenta závislostí](../azure-monitor/insights/vminsights-enable-hybrid.md#dependency-agent).
- Pro MMA [použijte příkazový řádek nebo automatizaci](../azure-monitor/platform/log-analytics-agent.md#installation-options)nebo použijte [skript](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Kromě skriptů můžete k nasazení agentů použít nástroje pro nasazení, jako je Microsoft Endpoint Configuration Manager a [Intigua](https://www.intigua.com/intigua-for-azure-migration) .

## <a name="what-operating-systems-does-mma-support"></a>Jaké operační systémy MMA podporuje?

- Prohlédněte si seznam [operačních systémů Windows, které podporuje MMA](../azure-monitor/platform/log-analytics-agent.md#installation-options).
- Prohlédněte si seznam [operačních systémů Linux, které podporuje MMA](../azure-monitor/platform/log-analytics-agent.md#installation-options).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Je možné vizualizovat závislosti po dobu více než jedné hodiny?

Pro vizualizaci založenou na agentech můžete vizualizovat závislosti po dobu až jedné hodiny. Můžete se vrátit do konkrétního data v historii, a to až do jednoho měsíce, ale maximální doba trvání vizualizace je jedna hodina. Například můžete použít dobu trvání na mapě závislostí k zobrazení závislostí včera, ale můžete zobrazit závislosti pouze pro jedno hodinové okno. Můžete ale použít protokoly Azure Monitor k [dotazování na data závislostí](./how-to-create-group-machine-dependencies.md) po delší dobu trvání.

U vizualizace bez agentů můžete zobrazit mapu závislostí jednoho serveru po dobu od 1 hodiny do 30 dnů.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Můžu vizualizovat závislosti pro skupiny více než 10 virtuálních počítačů?

Můžete [vizualizovat závislosti](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) pro skupiny, které mají až 10 virtuálních počítačů. Pokud máte skupinu s více než 10 virtuálními počítači, doporučujeme rozdělit skupinu do menších skupin a potom tyto závislosti vizualizovat.

## <a name="next-steps"></a>Další kroky

Přečtěte si [přehled Azure Migrate](migrate-services-overview.md).