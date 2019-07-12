---
title: Azure Migrate – nejčastější dotazy (FAQ) | Dokumentace Microsoftu
description: Nejčastější dotazy k Azure Migrate adresy
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: snehaa
ms.openlocfilehash: 08a0312f12b3daab8b7f5e88da118b5bcbeb2f4c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807332"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate – nejčastější dotazy (FAQ)

Tento článek obsahuje nejčastější dotazy týkající se Azure Migrate. Pokud máte další dotazy, po přečtení tohoto článku, publikujte je na [fórum pro Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Obecné

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Které geografické oblasti Azure jsou podporovány službou Azure Migrate?
Azure Migrate aktuálně podporuje řadu geografické oblasti, ve kterých je vytvořit projekt Azure Migrate. I v případě, že projekty lze vytvořit pouze v těchto geografické oblasti, můžete přesto posoudit vašich počítačů pro jiné cílové umístění. Zeměpisné oblasti projektu slouží pouze k uložení metadat zjištěných.

**Zeměpisné oblasti** | **umístění úložiště metadat** Azure Government | Asie Virginie US Gov | Jihovýchodní Asie nebo Evropy východní Asie | Jižní Evropa a západní Evropa, Spojené království | Velká Británie – jih nebo Velká Británie – západ USA | USA (střed) nebo USA – západ 2

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Jak se Azure Migrate liší od Azure Site Recovery?

Azure Migrate nabízí nástroje, které vám umožní zjišťovat, posuzovat a migrovat počítače a úlohy do Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) je řešení pro zotavení po havárii. Obě služby sdílí některé komponenty.

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Zařízení Azure Migrate (VMware/fyzické servery)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Jak Azure Migrate zařízení připojit k Azure?

Může být připojení přes internet, nebo můžete použít u veřejného partnerského vztahu ExpressRoute.

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Jaké požadavky síťového připojení jsou potřeba pro Azure Migrate serveru vyhodnocení a migrace

Adresy URL a porty potřebné ke službě Azure Migrate ke komunikaci s Azure, najdete v tématu [VMWare](migrate-support-matrix-vmware.md) a [Hyper-V](migrate-support-matrix-hyper-v.md) podporují matice.

### <a name="can-i-harden-the-appliance-vmware-vm-i-set-up-with-the-ova-template"></a>Můžete posílení zabezpečení zařízení nastavit pomocí OVA šablony virtuálního počítače VMware?

Další součásti (například antivirový program) je možné přidat do šablony OVA komunikace a brány firewall pravidel potřebných pro zařízení Azure Migrate re doleva, jako je.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Posilte zabezpečení zařízení Azure Migrate, jaké jsou doporučené výjimky antivirové ochrany v programu (AV)?

Je třeba vyloučit z kontroly na zařízení následující složky:

- Složky obsahující binární soubory pro služby Azure Migrate. Vylučte všechny podsložky.
- %ProgramFiles%\ProfilerService  
- Azure Migrate webové aplikace. Vylučte všechny podsložky.
- %SystemDrive%\inetpub\wwwroot
- V místní mezipaměti databáze a soubory protokolů. Služba Azure Migrate potřebuje přístup pro čtení/zápis do této složky.
  - %SystemDrive%\Profiler

### <a name="what-data-is-collected-by-azure-migrate"></a>Jaká data se shromažďují službou Azure Migrate?

Zařízení Azure Migrate shromáždí metadata pro místní virtuální počítače, včetně:

**Data konfigurace virtuálního počítače**
- Zobrazovaný název virtuálního počítače (na serveru vCenter)
- Cesta inventáře virtuálního počítače (hostitele nebo clusteru/složku v systému vCenter)
- IP adresa
- Adresa MAC
- Operační systém
- Počet jader, disků, síťových adaptérů
- Velikost paměti, velikosti disků

**Údaje o výkonu virtuálního počítače**
- Využití procesoru
- Využití paměti
- U každého disku připojeného k virtuálnímu počítači:
  - Propustnost čtení z disku
  - Propustnost zápisů disku
  - Čtení z disku operací za sekundu
  - Disk se zapisuje operací za sekundu
- Pro každý síťový adaptér připojený k virtuálnímu počítači:
  - Sítě v
  - Síťové výstupy

TN toho, pokud provádíte nasazení mapování závislostí, agenti mapování závislostí shromažďovat informace, jako jsou počítače plně kvalifikovaný název domény, operačního systému, IP adresa, adresa MAC, spuštěné procesy v rámci virtuálního počítače a příchozí/odchozí připojení TCP pro virtuální počítač. Toto zjišťování je volitelný používá pouze v případě, že povolíte mapování závislostí pro zjišťování.

### <a name="is-there-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>Je v analyzované prostředí hostitele ESXi ovlivnit výkon?

S průběžné profilace údaje o výkonu, profily zařízení Azure Migrate místních počítačů k měření údaje o výkonu virtuálního počítače. Tato akce nemá téměř žádný vliv na výkon na hostitelích ESXi, stejně jako v systému vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Kde se shromážděná data uložená a jak dlouho?

Data shromažďovaná společností zařízení Azure Migrate je uložen v umístění Azure, který zadáváte při vytváření projektu migrace. Data je bezpečně uložen v rámci předplatného Microsoft a se odstraní při odstranění projektu Azure Migrate.

Pro vizualizaci závislostí Pokud jste instalovali agenty na virtuální počítače, data shromážděná agenty závislostí uložená v USA, v pracovním prostoru Log Analytics vytvořené v rámci předplatného Azure. Tato data jsou odstraněna při odstranění pracovního prostoru Log Analytics v rámci vašeho předplatného. [Další informace](concepts-dependency-visualization.md).

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-during-continuous-profiling"></a>Co je objem dat odeslaných službou Azure Migrate během profilace průběžné?

Objem dat odesílaných do Azure Migrate se liší v závislosti na několik parametrů. Projekt Azure Migrate s 10 počítačů (každá má jeden disk a jednu síťovou kartu), poskytnout orientační číslo, odešle přibližně 50 MB za den. To je přibližně hodnota, která změny podle počtu datových bodů pro síťová rozhraní a disků (data odeslaná je nelineárních, pokud se zvyšuje počet počítačů, síťové adaptéry nebo disky).

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>Je šifrování dat v klidovém stavu a během přenosu?

Ano pro obojí. Metadata se ke službě Azure Migrate odeslány bezpečně přes internet prostřednictvím protokolu https. Metadata uložená v [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)a v [úložiště objektů blob v Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) v předplatném Microsoft, a je šifrovaný v klidovém stavu.

Data shromážděná agenty závislostí je také šifrovaný v přenosu (secure HTTPS) a je uložen v pracovním prostoru Log Analytics v předplatném uživatele. Je také šifrovaný v klidovém stavu.

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Jak Azure Migrate zařízení komunikují s systému vCenter Server a služby Azure Migrate?

Zařízení se připojuje k systému vCenter Server (port 443) pomocí přihlašovacích údajů k dispozici při nastavování zařízení. Vyžádá si vCenter Server s použitím VMware PowerCLI se získat metadata o virtuálních počítačích spravovaných přes vCenter Server. Shromažďuje konfigurační data o virtuálních počítačích (jader, paměti, disky, NIC atd.), a také historie výkonu každého virtuálního počítače za poslední měsíc. Shromážděná metadata se pak posílají do Azure migrovat posouzení serveru (přes internet prostřednictvím protokolu HTTPS) pro posouzení. 

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>Můžete připojit na stejné zařízení na několik serverů vCenter?

Ano, o jediné zařízení Azure Migrate je možné zjistit více vCenter servery, ale ne současně. Budete muset spustit zjišťování jeden po druhém.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Šablona OVA používané pro Site Recovery integrovaná OVA použít se službou Azure Migrate?

Aktuálně neexistuje žádná integrace. Na. Šablony pro soubory OVA ve službě Site Recovery se používá k nastavení konfiguračního serveru Site Recovery pro replikaci virtuálních počítačů VMware nebo fyzický server. Na. Soubory OVA použít se službou Azure Migrate se používá ke zjišťování virtuálních počítačů VMware, které jsou spravovány serverem vCenter, pro účely vyhodnocení a migraci.

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>Můžu změnit svoji velikost počítače. Můžete znovu spustit posouzení?
Zařízení Azure Migrate průběžně shromažďuje informace o místním prostředí. Posouzení ale snímku bodu v čase z místních virtuálních počítačů. Pokud změníte nastavení virtuálního počítače chcete posouzení, použijte možnost 'Přepočítat' k aktualizaci posouzení s nejnovějšími změnami.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Jak lze zjistit prostředí s více tenanty ve službě Azure Migrate?

Pro replikaci z VMware Pokud máte prostředí, které se sdílejí napříč tenanty a nechcete, aby ke zjištění virtuálních počítačů z jednoho tenanta v jiném tenantovi předplatné, vytvořte vCenter Server přihlašovací údaje s přístupem jen pro tyto virtuální počítače, které jste chtěli vyhledat. Pak použijte přihlašovací údaje při rutinního zjišťování v zařízení Azure Migrate.

Pro Hyper-V, zjišťování používá přihlašovacím údajům hostitele Hyper-V, pokud virtuální počítače sdílení stejného hostitele Hyper-V, neexistuje aktuálně žádný způsob, jak oddělit zjišťování.  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>Kolik virtuálních počítačů můžete zjistit pomocí zařízení jedna migrace?

Je možné vyhledat až 10 000 virtuálních počítačů VMware a až 5 000 virtuálních počítačů Hyper-V pomocí zařízení jedna migrace.  Pokud máte další počítače ve vašem místním prostředí, přečtěte si, jak škálovat [Hyper-V](scale-hyper-v-assessment.md) a [VMware](scale-vmware-assessment.md) posouzení.


## <a name="azure-migrate-server-assessment"></a>Azure Migrate: Server Assessment

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Migrace Azure: Server Assessment podporuje posouzení fyzických serverů?

Ne, Azure Migrate v současné době nepodporuje posouzení fyzických serverů. 

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Serveru ke zjištění prostředí VMware vCenter potřebuje Azure Migrate?

Ano, potřebuje Azure Migrate vCenter Server ke zjištění prostředí VMware. Nepodporuje zjišťování hostitelů ESXi, které nejsou spravované přes vCenter Server.

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>Jaký je rozdíl mezi použitím nástrojů Azure Migrate: Server Assessment and Map Toolkit?

Azure Migrate: Server Assessment poskytuje posouzení migrace, které vám pomůžou s přípravu migrace a hodnocení úloh pro migraci do Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826) má jiné funkce, jako je migrace, plánování novějších verzích Windows klientských a serverových operačních systémů a sledování využití softwaru. U scénářů dál používat MAP Toolkit.

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>Jak se Azure Migrate: Posouzení serveru liší od Azure Site Recovery Deployment Planner?

Azure Migrate: Server Assessment je nástroj pro plánování migrace. Azure Site Recovery Deployment Planner je nástroj pro plánování obnovení po havárii.

- **Migraci z VMware/Hyper-V do Azure**: Pokud máte v úmyslu migrace vašich místních serverů do Azure, použijte Azure Migrate: Nástroj pro vyhodnocení server pro plánování migrace. Nástroj posuzuje místní úlohy a poskytuje pokyny, přehledy a mechanismy, které vám pomohou při migraci do Azure. Až budete připravení plán migrace zrealizovat, můžete použít nástroje, jako je Azure Migrate: Migrace serveru k migraci počítače do Azure.
- **Zotavení po havárii z VMware/Hyper-V do Azure**: Pro zotavení po havárii do Azure pomocí Site Recovery použijte Plánovač nasazení služby Site Recovery pro plánování zotavení po havárii. Plánovač nasazení služby Site Recovery nemá podrobné, posouzení specifické pro Site Recovery v místním prostředí. Poskytuje doporučení, které jsou potřebné pro úspěšné po havárii operace, jako je replikace a převzetí služeb při selhání virtuálních počítačů pomocí Site Recovery. 

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Odhad nákladů na základě smlouvy Enterprise Agreement EA podporuje Azure Migrate?

Azure Migrate v současné době nepodporuje odhad nákladů pro [nabídky Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). Alternativním řešením je zadat jako nabídku s průběžnými platbami a ručně zadejte procento slevy (platí pro předplatné) do pole "Slevy" vlastnosti posouzení.

  ![Sleva](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Jaký je rozdíl mezi jako on-premises velikost a velikost na základě výkonu?

- V podle velikosti, v místním Azure Migrate nebere v úvahu údaje o výkonu virtuálního počítače. Jeho velikosti virtuálních počítačů založených na místní konfiguraci. – V určení velikosti na základě výkonu velikosti podle data o využití.
- Například pokud místní virtuální počítač má 4 jádra a 8 GB paměti s 50 % využití CPU a 50 % využití paměti v aplikaci, jako v místním nastavení velikosti doporučuje skladovou Položku virtuálního počítače Azure s 4 jádry a 8GB paměti. Změna velikosti na základě výkonu, ale doporučuje skladovou Položku virtuálního počítače 2 jádra a 4 GB, protože procentuální hodnota využití se považuje za.
- Podobně určení velikosti disku závisí na dvě vlastnosti posouzení – Změna velikosti kritéria a úložiště typu.
= Když je kritérium určení velikosti na základě výkonu a úložiště typu je automatické, hodnoty IOPS a propustnost disku jsou považovány za při identifikaci cílový typ disku (Standard nebo Premium).
- Pokud je kritérium určení velikosti na základě výkonu a úložiště typu premium, se doporučuje disk úrovně premium. Disk úrovně premium, který je vybraná skladová položka podle velikosti na místním disku. Stejnou logiku slouží k určení velikosti disku, pokud je kritérium určení velikosti jako v místním nastavení velikosti, a je typ úložiště úrovně standard nebo premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Doporučení velikosti jaký vliv má percentilu a historii využití výkonu?

Tyto vlastnosti se zohledňují pouze při určování velikosti na základě výkonu.

- Azure Migrate shromažďuje historie výkonu místních počítačů a použije ho k doporučujeme typ velikosti a disku virtuálního počítače v Azure.
- Zařízení průběžně profily v místním prostředí pro shromažďování dat o využití v reálném čase každých 20 sekund. Zařízení seskupuje 20sekundové vzorky a pro každých 15 minut vytvoří jeden datový bod. Jeden datový bod se vytvoří tak, že zařízení ze všech 20sekundových vzorků vybere maximální hodnotu a odešle ji do Azure.
- Když vytváříte posouzení v Azure (podle dobu trvání výkonu a hodnoty percentilu historie výkonu), Azure Migrate vypočítá hodnotu efektivní využití a použije ho k nastavení velikosti.
- Například pokud nastavíte dobu trvání výkonu bude jeden den a hodnotu percentilu 95. percentilu, Azure Migrate používá 15 minut vzorových bodů zaslaná z kolekce za poslední den, seřazený ve vzestupném pořadí a vybere 95. percentil hodnoty jako efektivní využití.
- 95. percentil hodnoty zajistí, že se ignoruje všechny odlehlé hodnoty, které může dojít, pokud 99. percentilu. Pokud chcete vybrat maximální využití v určitém období a nechcete přijít o žádné odlehlé hodnoty, měli byste vybrat 99. percentil.

### <a name="what-is-dependency-visualization"></a>Co je vizualizace závislostí?

Vizualizace závislostí lze posoudit skupiny virtuálních počítačů pro migraci s větší jistotou. Je mezi zjednodušená kontrola hodnot závislostí počítačů před spuštěním posouzení. Vizualizace závislostí pomáhá zajistit, že nic se zachovají a vyhnout se nečekaných výpadků při migraci do Azure. Azure Migrate využívá řešení Service Map v protokolech Azure Monitor umožňuje vizualizace závislostí.

> [!NOTE]
> Funkce vizualizace závislostí není k dispozici ve službě Azure Government.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Musím zaplatit pomocí funkce vizualizace závislostí?

Ne. [Další informace](https://azure.microsoft.com/pricing/details/azure-migrate/) o cenách služby Azure Migrate.

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Je potřeba instalovat cokoli, co je pro vizualizace závislostí?

Pokud chcete použít vizualizaci závislostí, budete muset stáhnout a nainstalovat agenty na každém v místním počítači, který chcete vyhodnotit.

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) musí být nainstalovaný na každém počítači.
- [Agenta závislostí](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) musí být nainstalovaný na každém počítači.
- Kromě toho pokud máte počítače bez připojení k Internetu, musíte stáhnout a nainstalovat bránu Log Analytics na ně.

Pokud používáte vizualizace závislostí, nemusíte tyto agenty.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Můžete použít stávající pracovní prostor pro vizualizace závislostí?

Ano, můžete připojit existující pracovní prostor do projektu migrace a využít pro vizualizace závislostí. [Další informace](concepts-dependency-visualization.md#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Můžete exportovat sestavu vizualizace závislostí?

Ne, není možné exportovat vizualizace závislostí. Ale protože Azure Migrate využívá mapu služeb pro vizualizaci závislostí, můžete použít [rozhraní REST API služby mapy](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) získat závislosti ve formátu json.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Jak automatizovat instalaci Microsoft Monitoring Agent (MMA) a agenta závislostí

[Pomocí tohoto skriptu](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) pro instalaci agentů. [Postupujte podle těchto pokynů](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) k instalaci agenta MMA pomocí příkazového řádku nebo automatizace. Pro agenta MMA, využijte [tento skript](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab).

Kromě skriptů, můžete pomocí nástroje nasazení, jako je System Center Configuration Manager [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) atd. a nasadit agenty.

### <a name="what-operating-systems-are-supported-by-mma"></a>Jaké operační systémy podporují MMA?

- [Kontrola](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems) seznamu operačních systémů Windows nepodporuje agenta MMA.
- [Revize] https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) seznam operačních systémů Linux podporuje agenta MMA.

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>Co jsou operačních systémech podporovaných produktem agenta závislostí?

[Kontrola](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems) operační systémy Windows nepodporuje agenta závislostí.
[Kontrola](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems) seznam operačních systémů Linux podporuje agenta závislostí.

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>Je možné vizualizovat závislosti ve službě Azure Migrate pro více než jedna hodina?
Ne, můžete vizualizovat závislosti pro až na jednu hodinu. Můžete přejít zpět na konkrétní datum v historii, až po poslední měsíc, ale maximální doba trvání za účelem vizualizace je jedna hodina. Například můžete dobu trvání na mapě závislostí zobrazit závislosti pro včerejška ale jenom ji mohou zobrazit okna hodinová. Můžete však použít protokoly Azure monitoru a [dotazovat data závislostí](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies) za delší dobu.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-ten-vms"></a>Platí pro skupiny s více než deset virtuálních počítačů vizualizace závislostí?
Je možné [vizualizace závislostí u skupin](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) s až deset virtuálních počítačů. Jestli máte skupinu s více než deset virtuálních počítačů, doporučujeme rozdělit skupiny v menším skupinám a pak vizualizovat závislosti.

## <a name="azure-migrate-server-migration"></a>Azure Migrate: Migrace serveru

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>Jak se Azure Migrate: Migrace serveru liší od Azure Site Recovery?

Azure Migrate: Migrace serveru využívá modul replikace Site Recovery pro migraci založené na agentovi serverů do Azure.
## <a name="next-steps"></a>Další postup
Přečtěte si [přehled Azure Migrate](migrate-services-overview.md)
