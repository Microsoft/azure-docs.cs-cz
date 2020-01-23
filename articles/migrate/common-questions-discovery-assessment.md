---
title: Běžné otázky – zjišťování, hodnocení a analýza závislostí v Azure Migrate
description: Získejte odpovědi na běžné dotazy týkající se zjišťování, hodnocení a analýzy závislostí v Azure Migrate.
ms.topic: conceptual
ms.date: 12/29/2019
ms.openlocfilehash: 0132563072ed04a52e4937da7a8df69196f1a91f
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513282"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>Běžné dotazy týkající se zjišťování, hodnocení a analýzy závislostí

Tento článek obsahuje odpovědi na běžné dotazy týkající se zjišťování, hodnocení a analýzy závislostí v Azure Migrate. Pokud máte další dotazy po přečtení tohoto článku, publikujte je na [Azure Migrate Fórum](https://aka.ms/AzureMigrateForum). Pokud máte další otázky, přečtěte si tyto články:

- [Obecné otázky](resources-faq.md) týkající se Azure Migrate
- [Otázky](common-questions-appliance.md) týkající se zařízení Azure Migrate.


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>Kolik virtuálních počítačů je možné zjistit pomocí zařízení?

Můžete zjistit až 10 000 virtuálních počítačů VMware, až 5 000 virtuálních počítačů Hyper-V a až 250 serverů s jedním zařízením. Pokud máte ve svém místním prostředí více počítačů, přečtěte si informace o škálování [technologie Hyper-V](scale-hyper-v-assessment.md), [VMware](scale-vmware-assessment.md) a [fyzické](scale-physical-assessment.md) posouzení.



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>Velikost virtuálního počítače se změnila. Můžu znovu spustit posouzení?

Zařízení Azure Migrate průběžně shromažďuje informace o místním prostředí. Posouzení je ale snímkem v čase místních virtuálních počítačů. Pokud změníte nastavení na virtuálním počítači, který chcete vyhodnotit, použijte k aktualizaci posouzení nejnovější změny pomocí možnosti Přepočítat.

### <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>Návody zjistit virtuální počítače ve víceklientském prostředí?

- Pokud se v případě VMware sdílí vaše prostředí mezi klienty a nechcete zjišťovat virtuální počítače jednoho tenanta v rámci předplatného jiného tenanta, vytvořte vCenter Server přihlašovacích údajů, které budou mít přístup jenom k virtuálním počítačům, které chcete zjistit. Pak tyto přihlašovací údaje použijte při zahájení zjišťování v zařízení Azure Migrate.
- U technologie Hyper-V používá zjišťování přihlašovací údaje hostitele Hyper-V. Pokud virtuální počítače sdílejí stejného hostitele Hyper-V, není v současnosti žádný způsob, jak zjišťování oddělit.  


### <a name="do-i-need-vcenter-server-for-vmware-vm-discovery"></a>Potřebuji vCenter Server pro zjišťování virtuálních počítačů VMWare?

Ano, Azure Migrate potřebuje vCenter Server k provedení zjišťování v prostředí VMware. Nepodporuje zjišťování hostitelů ESXi, které nejsou spravované pomocí vCenter Server.


## <a name="whats-the-difference-sizing-options"></a>Jaké jsou možnosti velikosti rozdílů?

Při změně velikosti v místním prostředí Azure Migrate nepovažují údaje o výkonu virtuálních počítačů za účelem posouzení. Vyhodnocuje velikosti virtuálních počítačů na základě místních konfigurací. Při změně velikosti na základě výkonu je velikost založena na datech využití.

- Pokud má například místní virtuální počítač 4 jádra a 8 GB paměti při 50% využití CPU a 50% využití paměti, dojde k následujícímu:
    - Při změně velikosti v místním prostředí se doporučuje skladová položka virtuálního počítače Azure, která má 4 jádra a 8 GB paměti.
    - Velikost na základě výkonu doporučí SKU virtuálních počítačů, které mají 2 jádra a 4 GB paměti, protože je zváženo procento využití.

- Podobně závisí velikost disku na dvou vlastnostech posouzení: kritéria změny velikosti a typ úložiště.
    - Pokud jsou kritéria změny velikosti založená na výkonu a typ úložiště je automatický, Azure Migrate při určení typu cílového disku (Standard nebo Premium) převezme hodnoty IOPS a propustnosti disku do účtu.
    - Pokud jsou kritéria změny velikosti založená na výkonu a typ úložiště je Premium, Azure Migrate doporučuje SKU diskových jednotek úrovně Premium, a to na základě velikosti místního disku. Stejná logika se aplikuje na velikost disku, pokud je velikost v místním prostředí a typ úložiště je Standard nebo Premium.

## <a name="does-performance-historyutilization-impact-sizing"></a>Má dopad na historii výkonu/využití vliv na změnu?

Tyto vlastnosti se zohledňují pouze při určování velikosti na základě výkonu.

- Azure Migrate shromažďuje historii výkonu místních počítačů a používá je k tomu, aby v Azure doporučila velikost virtuálního počítače a typ disku.
- Zařízení nepřetržitě profiluje místní prostředí, které umožňuje shromažďovat data o využití v reálném čase každých 20 sekund.
- Zařízení shrnuje ukázky 20 sekund a vytvoří jeden datový bod každých 15 minut.
- Pokud chcete vytvořit datový bod, zařízení vybere nejvyšší hodnotu ze všech ukázek s 20 sekundami.
- Zařízení odešle tento datový bod do Azure.

Když vytvoříte posouzení v Azure na základě hodnoty doby trvání a percentilu historie výkonu, Azure Migrate vypočítá efektivní hodnotu využití a použije ji k určení velikosti.

- Pokud například nastavíte dobu trvání na jeden den a hodnotu percentilu na 95. percentil, Azure Migrate seřadí ukázkové body za 15 minut odeslané kolektorem za poslední den ve vzestupném pořadí a jako efektivní se vybere hodnota 95. percentilu. vytížen.
- Použití hodnoty 95. percentilu zajišťuje, že se podhodnoty ignorují. Pokud použijete 99 percentil, je možné zahrnout mimo jiné odlehlé hodnoty. Pokud chcete vybrat špičku v období, bez chybějících hodnot, vyberte 99 percentil.

## <a name="what-is-dependency-visualization"></a>Co je Vizualizace závislostí?

Pomocí Vizualizace závislostí můžete posuzovat skupiny virtuálních počítačů pro migraci s větší jistotou. Vizualizace závislostí křížově kontroluje závislosti počítačů před spuštěním posouzení. Pomáhá zajistit, aby nic nezůstalo, a proto pomáhá vyhnout se neočekávanému výpadkům při migraci do Azure. Azure Migrate používá řešení Service Map v Azure Monitor k povolení Vizualizace závislostí. [Další informace](concepts-dependency-visualization.md)

> [!NOTE]
> Vizualizace závislostí není v Azure Government k dispozici.

## <a name="do-i-pay-for-dependency-visualization"></a>Platíte za vizualizaci závislostí?
Ne. [Další informace](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenách služby Azure Migrate.

## <a name="what-do-i-install-for-dependency-visualization"></a>Co mám nainstalovat pro vizualizaci závislostí?

Pokud chcete používat vizualizaci závislostí, je potřeba stáhnout a nainstalovat agenty na každý místní počítač, který chcete vyhodnotit.

Na každý počítač je potřeba nainstalovat následující agenty:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agent závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Pokud máte počítače bez připojení k Internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics.

Pokud nepoužíváte vizualizaci závislostí, nepotřebujete tyto agenty.

## <a name="can-i-use-an-existing-workspace"></a>Můžu použít stávající pracovní prostor?

Ano, existující pracovní prostor můžete připojit k projektu migrace a použít ho pro vizualizaci závislostí. [Další informace](concepts-dependency-visualization.md#how-does-it-work).

## <a name="can-i-export-the-dependency-visualization-report"></a>Můžu sestavu Vizualizace závislostí exportovat?

Ne, vizualizace závislosti se nedá exportovat. Azure Migrate však používá Service Map a můžete použít [REST API Service map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) k načtení závislostí ve formátu JSON.

## <a name="can-i-automate--mmadependency-agent-installation"></a>Můžu automatizovat instalaci agenta MMA/Dependency?

Pomocí tohoto [skriptu nainstalujte agenta závislostí](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Postupujte podle těchto [pokynů a nainstalujte MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) pomocí příkazového řádku nebo automatizace. Pro MMA použijte [Tento skript](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Kromě skriptů můžete k nasazení agentů použít taky nástroje pro nasazení, jako je Microsoft Endpoint Configuration Manager a [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) .


## <a name="what-operating-systems-does-mma-support"></a>Jaké operační systémy MMA podporuje?

- Prohlédněte si seznam [operačních systémů Windows podporovaných nástrojem MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Prohlédněte si seznam [operačních systémů Linux podporovaných nástrojem MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Je možné vizualizovat závislosti po dobu delší než hodinu?
Ne. Závislosti můžete vizualizovat až o hodinu. Můžete se vrátit k určitému datu v historii, pokud je to v měsíci zpátky, ale maximální doba trvání vizualizace je hodina. Například můžete použít dobu trvání na mapě závislostí k zobrazení závislostí včera, ale můžete zobrazit pouze závislosti pouze do jednoho hodiny okna. Můžete ale použít protokoly Azure Monitor k [dotazování na data závislostí](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) v delší době trvání.

## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>Může vizualizovat závislosti pro skupiny více než 10 virtuálních počítačů?
Můžete [vizualizovat závislosti](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) pro skupiny, které obsahují až 10 virtuálních počítačů. Pokud máte skupinu s více než 10 virtuálními počítači, doporučujeme rozdělit skupinu do menších skupin a potom tyto závislosti vizualizovat.




## <a name="next-steps"></a>Další kroky
Přečtěte si [přehled Azure Migrate](migrate-services-overview.md).
