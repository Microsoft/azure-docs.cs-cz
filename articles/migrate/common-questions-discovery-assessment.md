---
title: Otázky týkající se zjišťování, hodnocení a analýzy závislostí v Azure Migrate
description: Získejte odpovědi na běžné otázky týkající se zjišťování, hodnocení a analýzy závislostí v Azure Migrate.
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: b4b2a50bc88768d46c82f6bce73447dc901e5dfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681905"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>Analýza zjišťování, hodnocení a závislostí – běžné otázky

Tento článek odpovídá na běžné otázky týkající se zjišťování, hodnocení a analýzy závislostí v Azure Migrate. Máte-li další dotazy, zkontrolujte tyto zdroje:

- [Obecné otázky týkající](resources-faq.md) se migrace Azure
- Otázky týkající se [zařízení Azure Migrate](common-questions-appliance.md)
- Otázky týkající se [migrace serveru](common-questions-server-migration.md)
- Získejte odpovědi na otázky ve [fóru Migrace Azure](https://aka.ms/AzureMigrateForum)


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Jaké zeměpisné oblasti jsou podporované pro zjišťování a hodnocení pomocí Migrace Azure?

Projděte si podporované zeměpisné oblasti pro [veřejné](migrate-support-matrix.md#supported-geographies-public-cloud) a [vládní cloudy](migrate-support-matrix.md#supported-geographies-azure-government).


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Kolik virtuálních zařízení můžu zjistit pomocí zařízení?

Pomocí jednoho zařízení můžete objevit až 10 000 virtuálních zařízení VMware, až 5 000 virtuálních virtuálních zařízení Hyper-V a až 250 fyzických serverů. Pokud máte více počítačů, přečtěte si o [škálování hodnocení Technologie Hyper-V](scale-hyper-v-assessment.md), [škálování hodnocení VMware](scale-vmware-assessment.md)nebo [škálování hodnocení fyzického serveru](scale-physical-assessment.md).

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>Ve službě Azure Government nevidím některé typy virtuálních počítačů.

Typy virtuálních počítačů podporované pro hodnocení a migraci závisí na dostupnosti v umístění Azure Government. Typy virtuálních počítačů ve službě Azure Government můžete [zkontrolovat a porovnat.](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines)


## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>Velikost virtuálního počítače se změnila. Mohu znovu spustit hodnocení?

Zařízení Azure Migrate nepřetržitě shromažďuje informace o místním prostředí.  Hodnocení je snímek v okamžiku místních virtuálních počítačích. Pokud změníte nastavení na virtuálním počítači, který chcete posoudit, použijte možnost přepočtu aktualizovat hodnocení s nejnovějšími změnami.

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Jak zjistím virtuální chod ve víceklientském prostředí?

- **VMware**: Pokud je prostředí sdílené mezi tenanty a nechcete zjistit virtuální chod klienta v předplatném jiného klienta, vytvořte přihlašovací údaje vMware vCenter Server, které mají přístup pouze k virtuálním mům, které chcete zjistit. Potom použijte tato pověření při spuštění zjišťování v zařízení Azure Migrate.
- **Hyper-V**: Zjišťování používá pověření hostitele Hyper-V. Pokud virtuální chody sdílejí stejného hostitele Hyper-V, neexistuje v současné době žádný způsob, jak oddělit zjišťování.  

## <a name="do-i-need-vcenter-server"></a>Potřebuji vCenter Server?

Ano, Migrace Azure vyžaduje k zjišťování server vCenter server v prostředí VMware. Azure Migrate nepodporuje zjišťování hostitelů ESXi, které nejsou spravované vCenter Server.

## <a name="what-are-the-sizing-options"></a>Jaké jsou možnosti velikosti?

S místní mise azure migrate nebere v úvahu data o výkonu virtuálních počítačích pro posouzení. Migrace Azure vyhodnocuje velikosti virtuálních počítačů na základě místní konfigurace. S velikosti založené na výkonu, velikost i na základě dat využití.

Například pokud místní virtuální počítač má čtyři jádra a 8 GB paměti při 50 % využití procesoru a 50 % využití paměti:
- Místní velikost doporučuje skladovou jednotku virtuálního počítače Azure, která má čtyři jádra a 8 GB paměti.
- Velikost založená na výkonu doporučí skladovou položku virtuálního zařízení, která má dvě jádra a 4 GB paměti, protože procento využití je považováno za.

Podobně velikost disku závisí na kritériích pro velikost a typu úložiště:
- Pokud jsou kritéria pro velikost založená na výkonu a typ úložiště je automatický, Azure Migrate vezme hodnoty IOPS a propustnosti disku v úvahu při identifikuje typ cílového disku (Standard nebo Premium).
- Pokud jsou kritéria pro velikost založená na výkonu a typ úložiště je Premium, Azure Migrate doporučuje skladovou položku disku Premium na základě velikosti místního disku. Stejná logika se používá pro velikost disku, pokud je velikost místní a typ úložiště je Standard nebo Premium.

## <a name="does-performance-history-and-utilization-affect-sizing"></a>Má historie výkonu a využití vliv na velikost?

Ano, historie výkonu a využití ovlivňují velikost v Azure Migrate.

### <a name="performance-history"></a>Historie výkonu

Pro jenom pro velikost i na základě výkonu Azure Migrate shromažďuje historii výkonu místních počítačů a pak ji používá k doporučení velikosti virtuálního počítače a typu disku v Azure:

1. Zařízení nepřetržitě profiluje místní prostředí, aby každých 20 sekund shromažďovalo data o využití v reálném čase.
1. Zařízení shrnuje shromážděné 20sekundové vzorky a používá je k vytvoření jednoho datového bodu každých 15 minut.
1. Chcete-li vytvořit datový bod, zařízení vybere špičkovou hodnotu ze všech 20sekundových vzorků.
1. Zařízení odešle datový bod do Azure.

### <a name="utilization"></a>Využití

Když vytvoříte hodnocení v Azure, v závislosti na době trvání výkonu a hodnotě percentilu historie výkonu, která je nastavena, Azure Migrate vypočítá hodnotu efektivního využití a pak ji použije pro velikost.

Pokud například nastavíte dobu trvání výkonu na jeden den a hodnotu percentilu na 95 percentil, Azure Migrate seřadí 15minutové ukázkové body odeslané kolektorem za poslední den ve vzestupném pořadí. Jako efektivní využití vybere hodnotu 95 percentilu.

Při použití hodnoty 95. Odlehlé hodnoty mohou být zahrnuty, pokud vaše Migrace Azure používá 99 percentil. Chcete-li vybrat využití ve špičce pro období bez chybějících odlehlých hodnot, nastavte Azure Migrate použít 99 percentil.

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>Jak se liší posouzení založená na importu od hodnocení se zdrojem zjišťování jako zařízením?

Hodnocení založená na importu jsou hodnocení vytvořená pomocí počítačů, které se importují do Migrace Azure pomocí souboru CSV. Import jsou povinná pouze čtyři pole: název serveru, jádra, paměť a operační systém. Zde je několik věcí, které je třeba poznamenat: 
 - Kritéria připravenosti jsou méně přísná v hodnoceních na základě importu na parametru typu spuštění. Pokud typ spuštění není k dispozici, předpokládá se, že počítač má typ spuštění systému BIOS a počítač není označen jako **podmíněně připraven**. V hodnocení se zdrojem zjišťování jako zařízení je připravenost označena jako **podmíněně připravená,** pokud chybí typ spouštění. Tento rozdíl ve výpočtu připravenosti je, protože uživatelé nemusí mít všechny informace o počítačích v raných fázích plánování migrace při provádí hodnocení na základě importu. 
 - Hodnocení importu na základě výkonu používají hodnotu využití poskytovanou uživatelem pro výpočty správné velikosti. Vzhledem k tomu, že hodnota využití je poskytována uživatelem, **historie výkonu** a **možnosti využití percentilu** jsou ve vlastnostech hodnocení zakázány. V hodnocení chodu se zdrojem zjišťování jako zařízením je zvolená hodnota percentilu vybrána z údajů o výkonu shromážděných zařízením.

## <a name="what-is-dependency-visualization"></a>Co je vizualizace závislostí?

Vizualizace závislostí vám může pomoci posoudit skupiny virtuálních aplikací, které mají být migrovány s větší spolehlivostí. Vizualizace závislostí křížově kontroluje závislosti počítače před spuštěním hodnocení. Pomáhá zajistit, že nic nezůstane pozadu, a pomáhá vyhnout se neočekávaným výpadkům při migraci do Azure. Azure Migrate používá řešení mapy služeb v Azure Monitoru k povolení vizualizace závislostí. [Další informace](concepts-dependency-visualization.md).

> [!NOTE]
> Analýza závislostí založená na agentovi není ve službě Azure Government dostupná. Můžete použít analýzu závislostí bez agenta

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>Jaký je rozdíl mezi agentem a bez agentů?

Rozdíly mezi vizualizací bez agenta a vizualizací na základě agenta jsou shrnuty v tabulce.

**Požadavek** | **Bez agenta** | **Na základě agenta**
--- | --- | ---
Podpora | Tato možnost je momentálně ve verzi Preview a je dostupná jenom pro virtuální měna VMware. [Zkontrolujte](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements) podporované operační systémy. | V obecné dostupnosti (GA).
Agent | Není třeba instalovat agenty na počítačích, které chcete křížově kontrolovat. | Agenti, kteří mají být nainstalováni v každém místním počítači, který chcete analyzovat: [Agent monitorování společnosti Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)a Agent [závislostí](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent). 
Požadavky | [Zkontrolujte](concepts-dependency-visualization.md#agentless-analysis) požadavky a požadavky na nasazení. | [Zkontrolujte](concepts-dependency-visualization.md#agent-based-analysis) požadavky a požadavky na nasazení.
Log Analytics | Není vyžadováno. | Azure Migrate používá řešení [map služeb](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) v [protokolech Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) u vizualizace závislostí. [Další informace](concepts-dependency-visualization.md#agent-based-analysis).
Jak to funguje | Zachytí data připojení TCP na počítačích, které jsou povoleny pro vizualizaci závislostí. Po zjištění shromažďuje data v intervalech pěti minut. | Agenti mapy služeb nainstalovaní v počítači shromažďují data o procesech TCP a příchozích a odchozích připojeních pro každý proces.
Data | Název zdrojového počítače, proces, název aplikace.<br/><br/> Název serveru cílového počítače, proces, název aplikace a port. | Název zdrojového počítače, proces, název aplikace.<br/><br/> Název serveru cílového počítače, proces, název aplikace a port.<br/><br/> Počet připojení, latence a informace o přenosu dat jsou shromažďovány a k dispozici pro dotazy Log Analytics. 
Vizualizace | Mapu závislostí jednoho serveru lze zobrazit po dobu jedné hodiny až 30 dnů. | Mapa závislostí jednoho serveru.<br/><br/> Mapu lze zobrazit pouze za hodinu.<br/><br/> Mapa závislostí skupiny serverů.<br/><br/> Přidejte a odeberte servery ve skupině ze zobrazení mapy.
Export dat | Nelze aktuálně stáhnout v tabulkovém formátu. | Data mohou být dotazováni pomocí Analýzy protokolů.

## <a name="do-i-pay-for-dependency-visualization"></a>Platím za vizualizaci závislostí?

Ne. Přečtěte si další informace o [cenách Migrace Azure](https://azure.microsoft.com/pricing/details/azure-migrate/).

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>Co mám nainstalovat pro vizualizaci závislostí založených na agentovi?

Chcete-li použít vizualizaci závislostí založenou na agentovi, stáhněte a nainstalujte agenty v každém místním počítači, který chcete vyhodnotit:

- [Agent monitorování společnosti Microsoft (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [Agent závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent)
- Pokud máte počítače, které nemají připojení k internetu, stáhněte a nainstalujte na ně bránu Log Analytics.

Tyto agenty potřebujete pouze v případě, že používáte vizualizaci závislostí na základě agenta.

## <a name="can-i-use-an-existing-workspace"></a>Mohu použít existující pracovní prostor?

Ano, pro vizualizaci závislostí na základě agenta můžete připojit existující pracovní prostor k projektu migrace a použít jej pro vizualizaci závislostí. 

## <a name="can-i-export-the-dependency-visualization-report"></a>Můžu exportovat sestavu vizualizace závislostí?

Ne, sestavu vizualizace závislostí ve vizualizaci založené na agentovi nelze exportovat. Azure Migrate však používá mapu služeb a můžete použít [rozhraní REST ROZHRANÍ MAP služeb](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) k načtení závislostí ve formátu JSON.

## <a name="can-i-automate-agent-installation"></a>Mohu automatizovat instalaci agenta?

Pro vizualizaci závislostí založených na agentovi:

- K [instalaci agenta závislostí](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)použijte skript .
- Pro MMA [použijte příkazový řádek nebo automatizaci](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)nebo použijte [skript](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).
- Kromě skriptů můžete k nasazení agentů použít nástroje pro nasazení, jako je Microsoft Endpoint Configuration Manager a [Intigua.](https://www.intigua.com/getting-started-intigua-for-azure-migration)

## <a name="what-operating-systems-does-mma-support"></a>Jaké operační systémy MMA podporuje?

- Zobrazení seznamu [operačních systémů Windows, které podporuje mma](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Zobrazení seznamu [operačních systémů Linux, které podporuje MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>Lze vizualizovat závislosti na více než jednu hodinu?

Pro vizualizaci založenou na agentovi můžete vizualizovat závislosti až na jednu hodinu. Můžete se vrátit až o jeden měsíc na konkrétní datum v historii, ale maximální doba trvání vizualizace je jedna hodina. Můžete například použít dobu trvání v mapě závislostí k zobrazení závislostí za včerejšek, ale můžete zobrazit závislosti pouze pro jednohodinové okno. Protokoly Azure Monitor však můžete použít k [dotazování dat závislostí](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) po delší dobu.

Pro vizualizaci bez agenta můžete zobrazit mapu závislostí jednoho serveru z doby trvání od jedné hodiny do 30 dnů.

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Můžu vizualizovat závislosti pro skupiny s více než 10 virtuálními zařízeními?

Můžete [vizualizovat závislosti](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) pro skupiny, které mají až 10 virtuálních účtů. Pokud máte skupinu, která má více než 10 virtuálních uživatelů, doporučujeme rozdělit skupinu do menších skupin a pak vizualizovat závislosti.

## <a name="next-steps"></a>Další kroky

Přečtěte si [přehled Migrace Azure](migrate-services-overview.md).
