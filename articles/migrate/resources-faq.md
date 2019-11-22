---
title: Běžné otázky týkající se Azure Migrate
description: Získejte odpovědi na běžné otázky týkající se služby Azure Migrate.
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: dc7dff0119ec849b447754ae54a45911038f6c48
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284456"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: běžné otázky

Tento článek obsahuje odpovědi na běžné otázky týkající se Azure Migrate. Pokud máte další dotazy po přečtení tohoto článku, publikujte je na [Azure Migrate Fórum](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Obecné

### <a name="which-azure-geographies-are-supported"></a>Které geografické oblasti Azure jsou podporované?

Projděte si Azure Migrate podporovaných geografických oblastí pro [virtuální počítače VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects) a pro [virtuální počítače Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects).

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Jaký je rozdíl mezi Azure Migrate a Site Recovery?

Azure Migrate poskytuje centralizované centrum pro správu a sledování zjišťování, posouzení a migrace místních počítačů a úloh do Azure. [Azure Site Recovery](../site-recovery/site-recovery-overview.md) je řešení zotavení po havárii. Azure Migrate: Nástroj pro migraci serveru používá některé funkce back-endu Site Recovery pro migraci posunovacích místních počítačů.

### <a name="how-do-i-delete-an-azure-migrate-project"></a>Návody odstranit Azure Migrate projektu?

Chcete-li odstranit projekt, postupujte podle [těchto pokynů](how-to-delete-project.md) . [Projděte si materiály](how-to-delete-project.md#created-resources) , které jsou vytvořené při zjišťování, hodnocení a migraci počítačů a úloh v Azure Migrate projektu.


## <a name="azure-migrate-appliance"></a>Zařízení Azure Migrate

### <a name="how-does-the-appliance-connect-to-azure"></a>Jak se zařízení připojuje k Azure?

Připojení může být přes Internet nebo Azure ExpressRoute s veřejným partnerským vztahem/Microsoftu.

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Jaké síťové připojení je potřeba pro Azure Migrate posouzení a migraci serveru?

Podívejte se na matrice podpory [VMware](migrate-support-matrix-vmware.md) a [Hyper-V](migrate-support-matrix-hyper-v.md) , kde najdete informace o adresách URL a portech potřebných pro komunikaci se službou Azure Azure Migrate.

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>Můžu posílit virtuální počítač zařízení vytvořený pomocí šablony?

Do šablony můžete přidat součásti (například antivirovou ochranu), pokud ponecháte komunikaci a pravidla brány firewall požadovaná pro Azure Migrate zařízení.

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Jaká data shromažďuje zařízení Azure Migrate?

Zkontrolujte data shromážděná zařízením následujícím způsobem:
- [Data výkonu](migrate-appliance.md#collected-performance-data-vmware) a [metadata](migrate-appliance.md#collected-metadata-vmware) pro virtuální počítače VMware.
- [Data výkonu](migrate-appliance.md#collected-performance-data-hyper-v) a [metadata](migrate-appliance.md#collected-metadata-hyper-v) pro virtuální počítače Hyper-V.


### <a name="does-appliance-analysis-impact-performance"></a>Má dopad na analýzu vliv na výkon zařízení?

Zařízení Azure Migrate profily v místních počítačích průběžně měří data o výkonu virtuálních počítačů. Tato profilace má téměř žádný dopad na výkon hostitele Hyper-V/ESXi nebo na vCenter Server.

### <a name="where-and-how-long-is-collected-data-stored"></a>Kde a jak dlouho se shromažďují data?

Data shromážděná zařízením Azure Migrate se ukládají do umístění Azure, které zvolíte při vytváření projektu migrace. Data jsou bezpečně uložená v rámci předplatného Microsoft a odstraňují se při odstranění Azure Migrate projektu.

V případě Vizualizace závislostí jsou shromážděná data uložená v USA v pracovním prostoru Log Analytics vytvořeném v rámci předplatného Azure. Tato data se odstraní při odstranění pracovního prostoru Log Analytics v rámci vašeho předplatného. [Přečtěte si další informace](concepts-dependency-visualization.md) o vizualizaci závislostí.

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>Jaký objem dat se nahrává během nepřetržitého profilace?

Objem dat odesílaných do Azure Migrate se liší v závislosti na několika parametrech. Abyste vám poskytli smysl tohoto svazku, Azure Migrate projekt s 10 počítači (každý s jedním diskem a jednou síťovou kartou) posílá přibližně 50 MB za den. Tato hodnota je přibližná a mění se podle počtu datových bodů pro síťové karty a disky. Zvýšení odeslaných dat je nelineární, pokud se zvyšuje počet počítačů, síťových karet nebo disků.

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Jsou data zašifrovaná v klidovém stavu a v přenosu?

Ano, obojí.
- Metadata se bezpečně odesílají do služby Azure Migrate přes Internet prostřednictvím protokolu HTTPS.
- Metadata se ukládají do databáze [databáze Azure Cosmos](../cosmos-db/database-encryption-at-rest.md) a do služby [Azure Blob Storage](../storage/common/storage-service-encryption.md) v předplatném Microsoftu. Metadata jsou šifrovaná v klidovém stavu.
- Data pro analýzu závislostí se také šifrují při přenosu (zabezpečené HTTPS). Je uložený v pracovním prostoru Log Analytics v rámci vašeho předplatného. Také se zašifruje v klidovém stavu.

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>Jak zařízení komunikuje s vCenter Server a Azure Migrate?

1. Zařízení se připojí k vCenter Server (port 443) s použitím přihlašovacích údajů, které jste zadali při nastavení zařízení.
2. Zařízení používá VMware PowerCLI k dotazování vCenter Server pro shromažďování metadat o virtuálních počítačích spravovaných pomocí vCenter Server. Shromažďuje údaje o konfiguraci virtuálních počítačů (jader, paměti, disků, síťových karet) a historii výkonu každého virtuálního počítače za minulý měsíc.
3. Shromážděná metadata se pak odesílají do Azure Migrate: vyhodnocení serveru (přes Internet prostřednictvím protokolu HTTPS) pro posouzení.

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>Můžu připojit stejné zařízení k více instancím vCenter Server?

Ne. Mezi zařízením a vCenter Server existuje mapování 1:1. Chcete-li zjistit virtuální počítače ve více instancích vCenter Server, je nutné nasadit více zařízení.


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>Velikost počítače se změnila. Můžu znovu spustit posouzení?

Zařízení Azure Migrate průběžně shromažďuje informace o místním prostředí. Posouzení je ale snímkem v čase místních virtuálních počítačů. Pokud změníte nastavení na virtuálním počítači, který chcete vyhodnotit, použijte k aktualizaci posouzení nejnovější změny pomocí možnosti Přepočítat.

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>Jak můžu provést zjišťování ve víceklientském prostředí?

- Pokud se v případě VMware sdílí vaše prostředí mezi klienty a nechcete zjišťovat virtuální počítače jednoho tenanta v rámci předplatného jiného tenanta, vytvořte vCenter Server přihlašovacích údajů, které budou mít přístup jenom k virtuálním počítačům, které chcete zjistit. Pak tyto přihlašovací údaje použijte při zahájení zjišťování v zařízení Azure Migrate.
- U technologie Hyper-V používá zjišťování přihlašovací údaje hostitele Hyper-V. Pokud virtuální počítače sdílejí stejného hostitele Hyper-V, není v současnosti žádný způsob, jak zjišťování oddělit.  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>Kolik virtuálních počítačů je možné zjistit pomocí jediného zařízení?

Můžete zjistit až 10 000 virtuálních počítačů VMware a až 5 000 virtuálních počítačů Hyper-V s jedním zařízením migrace. Pokud máte ve svém místním prostředí více počítačů, Naučte se škálovat [technologie Hyper-V](scale-hyper-v-assessment.md) a odhad [VMware](scale-vmware-assessment.md) .

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>Můžu z projektu odstranit zařízení Azure Migrate?

V současné době odstranění zařízení z projektu se nepodporuje.

- Jediným způsobem, jak zařízení odstranit, je odstranit skupinu prostředků, která obsahuje Azure Migrate projekt přidružený k danému zařízení.
- Odstraněním skupiny prostředků se ale odstraní taky další registrovaná zařízení, zjištěný inventář, posouzení a všechny ostatní komponenty Azure přidružené k projektu ve skupině prostředků.

## <a name="azure-migrate-server-assessment"></a>Vyhodnocování serveru Azure Migrate


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>Vyžaduje Azure Migrate vCenter Server pro zjišťování virtuálních počítačů VMWare?

Ano, Azure Migrate potřebuje vCenter Server k provedení zjišťování v prostředí VMware. Nepodporuje zjišťování hostitelů ESXi, které nejsou spravované pomocí vCenter Server.

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaký je rozdíl mezi Azure Migrate posuzování serveru a mapou sady nástrojů?

Posouzení serveru poskytuje hodnocení, které vám pomůžou s připraveností na migraci, a vyhodnocení úloh pro migraci do Azure. [Sada nástrojů Microsoft Assessment and Planning (map) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) pomáhá s dalšími úkoly, včetně plánování migrace pro novější verze klientských a serverových operačních systémů Windows a sledování využívání softwaru. V těchto scénářích pokračujte v používání sady MAP Toolkit.

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Jaký je rozdíl mezi posuzováním serveru a Site Recovery Plánovač nasazení?

Posuzování serveru je nástroj pro plánování migrace. Plánovač nasazení Site Recovery je nástroj pro plánování zotavení po havárii.

- **Plánování místní migrace do Azure**: Pokud plánujete migrovat místní servery do Azure, použijte k plánování migrace server hodnocení. Vyhodnocuje místní úlohy a poskytuje pokyny a nástroje, které vám pomůžou s migrací. Po dokončení plánu migrace můžete k migraci počítačů do Azure použít nástroje, včetně migrace Azure Migrate serveru.
- **Plánování zotavení po havárii do Azure**: Pokud plánujete nastavovat zotavení po havárii z místního prostředí do azure pomocí Site Recovery, použijte Plánovač nasazení Site Recovery. Plánovač nasazení poskytuje hloubkové a Site Recovery vyhodnocení místního prostředí pro účely zotavení po havárii. Poskytuje doporučení týkající se zotavení po havárii, jako je například replikace a převzetí služeb při selhání.

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>Jaký je rozdíl mezi určením velikosti mezi místními a na základě výkonu?

Při změně velikosti v místním prostředí Azure Migrate nepovažují údaje o výkonu virtuálních počítačů za účelem posouzení. Vyhodnocuje velikosti virtuálních počítačů na základě místních konfigurací. Při změně velikosti na základě výkonu je velikost založena na datech využití.

- Pokud má například místní virtuální počítač 4 jádra a 8 GB paměti při 50% využití CPU a 50% využití paměti, dojde k následujícímu:
    - Při změně velikosti v místním prostředí se doporučuje použít SKU virtuálního počítače Azure se čtyřmi jádry a 8 GB paměti.
    - Velikost na základě výkonu doporučí SKU virtuálních počítačů, které mají dvě jádra a 4 GB paměti, protože je zváženo procento využití.

- Podobně závisí velikost disku na dvou vlastnostech posouzení: kritéria změny velikosti a typ úložiště.
    - Pokud jsou kritéria změny velikosti založená na výkonu a typ úložiště je automatický, Azure Migrate při určení typu cílového disku (Standard nebo Premium) převezme hodnoty IOPS a propustnosti disku do účtu.
    - Pokud jsou kritéria změny velikosti založená na výkonu a typ úložiště je Premium, Azure Migrate doporučuje SKU diskových jednotek úrovně Premium, a to na základě velikosti místního disku. Stejná logika se aplikuje na velikost disku, pokud je velikost v místním prostředí a typ úložiště je Standard nebo Premium.

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>Má historie výkonu a percentily velikost dopadu na velikost dopad?

Tyto vlastnosti se zohledňují pouze při určování velikosti na základě výkonu.

- Azure Migrate shromažďuje historii výkonu místních počítačů a používá je k tomu, aby v Azure doporučila velikost virtuálního počítače a typ disku.
- Zařízení nepřetržitě profiluje místní prostředí, které umožňuje shromažďovat data o využití v reálném čase každých 20 sekund.
- Zařízení shrnuje ukázky 20 sekund a vytvoří jeden datový bod každých 15 minut.
- Pokud chcete vytvořit datový bod, zařízení vybere nejvyšší hodnotu ze všech ukázek s 20 sekundami.
- Zařízení odešle tento datový bod do Azure.

Když vytvoříte posouzení v Azure na základě hodnoty doby trvání a percentilu historie výkonu, Azure Migrate vypočítá efektivní hodnotu využití a použije ji k určení velikosti.

- Pokud například nastavíte dobu trvání na jeden den a hodnotu percentilu na 95. percentil, Azure Migrate seřadí ukázkové body za 15 minut odeslané kolektorem za poslední den ve vzestupném pořadí a jako efektivní se vybere hodnota 95. percentilu. vytížen.
- Použití hodnoty 95. percentilu zajišťuje, že se podhodnoty ignorují. Pokud použijete 99 percentil, je možné zahrnout mimo jiné odlehlé hodnoty. Pokud chcete vybrat špičku v období, bez chybějících hodnot, vyberte 99 percentil.

## <a name="server-assessment---dependency-visualization"></a>Vyhodnocení serveru – Vizualizace závislostí


### <a name="what-is-dependency-visualization"></a>Co je Vizualizace závislostí?

Pomocí Vizualizace závislostí můžete posuzovat skupiny virtuálních počítačů pro migraci s větší jistotou. Vizualizace závislostí křížově kontroluje závislosti počítačů před spuštěním posouzení. Pomáhá zajistit, aby nic nezůstalo, a proto pomáhá vyhnout se neočekávanému výpadkům při migraci do Azure. Azure Migrate používá řešení Service Map v Azure Monitor k povolení Vizualizace závislostí. [Další informace](concepts-dependency-visualization.md)

> [!NOTE]
> Vizualizace závislostí není v Azure Government k dispozici.

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>Musím pro používání Vizualizace závislostí platit?
Ne. [Další informace](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenách služby Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Musím pro vizualizaci závislostí nainstalovat cokoli?

Pokud chcete používat vizualizaci závislostí, je potřeba stáhnout a nainstalovat agenty na každý místní počítač, který chcete vyhodnotit.

Na každý počítač je potřeba nainstalovat následující agenty:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows).
- [Agent závislostí](../azure-monitor/platform/agents-overview.md#dependency-agent).
- Pokud máte počítače bez připojení k Internetu, musíte na ně stáhnout a nainstalovat bránu Log Analytics.

Pokud nepoužíváte vizualizaci závislostí, nepotřebujete tyto agenty.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Můžu použít existující pracovní prostor pro vizualizaci závislostí?

Ano, existující pracovní prostor můžete připojit k projektu migrace a použít ho pro vizualizaci závislostí. [Další informace](concepts-dependency-visualization.md#how-does-it-work)

### <a name="can-i-export-the-dependency-visualization-report"></a>Můžu sestavu Vizualizace závislostí exportovat?

Ne, vizualizace závislosti se nedá exportovat. Azure Migrate však používá Service Map a můžete použít [REST API Service map](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) k načtení závislostí ve formátu JSON.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>Jak mohu automatizovat instalaci Microsoft Monitoring Agent (MMA) a agenta závislostí?

Pomocí tohoto [skriptu nainstalujte agenta závislostí](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples). Postupujte podle těchto [pokynů a nainstalujte MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) pomocí příkazového řádku nebo automatizace. Pro MMA použijte [Tento skript](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Kromě skriptů můžete k nasazení agentů použít taky nástroje pro nasazení, jako je System Center Configuration Manager a [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) .


### <a name="what-operating-systems-are-supported-by-mma"></a>Které operační systémy podporuje MMA?

- Prohlédněte si seznam [operačních systémů Windows podporovaných nástrojem MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
- Prohlédněte si seznam [operačních systémů Linux podporovaných nástrojem MMA](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>Je možné vizualizovat závislosti po dobu delší než hodinu?
Ne. Závislosti můžete vizualizovat až o hodinu. Můžete se vrátit k určitému datu v historii, pokud je to v měsíci zpátky, ale maximální doba trvání vizualizace je hodina. Například můžete použít dobu trvání na mapě závislostí k zobrazení závislostí včera, ale můžete zobrazit pouze závislosti pouze do jednoho hodiny okna. Můžete ale použít protokoly Azure Monitor k [dotazování na data závislostí](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) v delší době trvání.

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>Můžu použít vizualizaci závislostí pro skupiny více než 10 virtuálních počítačů?
Můžete [vizualizovat závislosti](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) pro skupiny, které obsahují až 10 virtuálních počítačů. Pokud máte skupinu s více než 10 virtuálními počítači, doporučujeme rozdělit skupinu do menších skupin a potom tyto závislosti vizualizovat.

## <a name="azure-migrate-server-migration"></a>Migrace serveru Azure Migrate

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Jaký je rozdíl mezi migrací Azure Migrate serverem a Site Recovery?

- Migrace místních virtuálních počítačů VMware bez agenta je nativní v rámci migrace Azure Migrate serveru.
- Pro migraci virtuálních počítačů Hyper-V, fyzických serverů a virtuálních počítačů VMware založených na agentech Azure Migrate migrace serveru používá Azure Site Recovery replikační modul.


## <a name="next-steps"></a>Další kroky
Přečtěte si [přehled Azure Migrate](migrate-services-overview.md).
