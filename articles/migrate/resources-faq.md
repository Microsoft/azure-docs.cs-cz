---
title: Azure Migrate – nejčastější dotazy (FAQ) | Dokumentace Microsoftu
description: Nejčastější dotazy k Azure Migrate adresy
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: snehaa
ms.openlocfilehash: 2efa450b6b0cfa299370df3941224f4f64e91b4b
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54230760"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate – nejčastější dotazy (FAQ)

Tento článek obsahuje nejčastější dotazy týkající se Azure Migrate. Pokud máte další dotazy, po přečtení tohoto článku, publikujte je na [fórum pro Azure Migrate](https://aka.ms/AzureMigrateForum).

## <a name="general"></a>Obecné

### <a name="does-azure-migrate-support-assessment-of-only-vmware-workloads"></a>Podporuje Azure Migrate posouzení pouze úloh VMware?

Ano, Azure Migrate aktuálně podporuje pouze posouzení úloh VMware. V budoucnu bude povolena podpora pro Hyper-V a fyzické servery.

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Serveru ke zjištění prostředí VMware vCenter potřebuje Azure Migrate?

Ano, Azure Migrate vyžaduje systém vCenter Server ke zjištění prostředí VMware. Nepodporuje zjišťování hostitelů ESXi, které nejsou spravované přes vCenter Server.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Jak se Azure Migrate liší od Azure Site Recovery?

Azure Migrate je služba posouzení, která vám pomůže zjistit vaše místní úlohy a naplánovat vaši migraci do Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), spolu se řešení zotavení po havárii, pomůže vám migrace místních úloh do virtuálních počítačů IaaS v Azure.

### <a name="whats-the-difference-between-using-azure-migrate-for-assessments-and-the-map-toolkit"></a>Jaký je rozdíl mezi použitím nástrojů pro posouzení a Map Toolkit Azure Migrate?

[Azure Migrate](migrate-overview.md) poskytuje posouzení migrace speciálně pro účely pomoci s přípravu migrace a vyhodnocení místních úloh do Azure. [Microsoft Assessment and Planning (MAP) Toolkit](https://www.microsoft.com/en-us/download/details.aspx?id=7826) má další funkce. Plánování pro novější verze Windows klientských a serverových operačních systémů, například migrace software využití sledování atd. U scénářů dál používat MAP Toolkit.


### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Jak se Azure Migrate liší od Azure Site Recovery Deployment Planner?

Azure Migrate je nástroj pro plánování migrace a Azure Site Recovery Deployment Planner je zotavení po havárii (DR) nástroj pro plánování.

**Migraci z VMware do Azure**: Pokud máte v úmyslu migrace místních úloh do Azure, použijte pro plánování migrace Azure Migrate. Azure Migrate posuzuje místní úlohy a poskytuje pokyny, přehledy a mechanismy, které vám pomohou při migraci do Azure. Až budete připravení plán migrace zrealizovat, můžete počítače migrovat do Azure služeb, jako je Azure Site Recovery a Azure Database Migration Service.

**Migrace z technologie Hyper-V do Azure**: Azure Migrate aktuálně podporuje pouze posouzení virtuálních počítačů VMware pro migraci do Azure. Podpora technologie Hyper-V je na plán služby pro Azure Migrate. Prozatím můžete použít Plánovač nasazení služby Site Recovery. Jakmile se ve službě Azure Migrate je povolena podpora technologie Hyper-V, můžete použít Azure Migrate týkající se plánování migrace úloh Hyper-V.

**Zotavení po havárii z VMware/Hyper-V do Azure**: Pokud máte v úmyslu provést zotavení po havárii (DR) v Azure pomocí Azure Site Recovery (služba Site Recovery), použijte Plánovač nasazení služby Site Recovery pro plánování zotavení po Havárii. Plánovač nasazení služby Site Recovery nemá podrobné, specifické pro Azure Site Recovery hodnocení v místním prostředí. Poskytuje doporučení, která jsou vyžadované Site Recovery pro úspěšné operace zotavení po Havárii, jako je replikace, převzetí služeb při selhání virtuálních počítačů.  

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Které geografické oblasti Azure jsou podporovány službou Azure Migrate?

Azure Migrate aktuálně podporuje Evropa, Spojené státy a Azure Government jako zeměpisných oblastech projektu. I když migrace projektů můžete vytvořit pouze v těchto geografické oblasti, můžete přesto posoudit u svých počítačů [více cílových míst](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). Zeměpisné oblasti projektu slouží pouze k uložení metadat zjištěných.

**Zeměpisné oblasti** | **Umístění úložiště metadat**
--- | ---
Azure Government | USA (Gov) – Virginia
Asie | Jihovýchodní Asie
Evropa | Severní Evropa a západní Evropa
Sjednotí stavy | Východní USA střed USA – západ

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Jak v místní lokalitě připojit k Azure Migrate?

Připojení může být přes internet nebo pomocí veřejného partnerského vztahu ExpressRoute.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Můžete posílit ochranu virtuálních počítačů s OVA šablony?

Další součásti (například antivirový program) je možné přidat do šablony pro soubory OVA tak dlouho, dokud komunikaci a pravidel brány firewall vyžadované pro zařízení Azure Migrate pro práci se ponechán beze změny.   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>Posilte zabezpečení zařízení Azure Migrate, jaké jsou doporučené výjimky antivirové ochrany v programu (AV)?

Je třeba vyloučit následující složky v zařízení pro antivirové kontroly:

- Složka, která obsahuje binární soubory pro služby Azure Migrate. Vylučte všechny podsložky.
  %ProgramFiles%\ProfilerService  
- Azure Migrate webové aplikace. Vylučte všechny podsložky.
  %SystemDrive%\inetpub\wwwroot
- Místní mezipaměť pro databáze a soubory protokolů. Azure migrate služba potřebuje čtení a zápis do této složky.
  %SystemDrive%\Profiler

## <a name="discovery"></a>Zjišťování

### <a name="what-data-is-collected-by-azure-migrate"></a>Jaká data se shromažďují službou Azure Migrate?

Azure Migrate podporuje dva druhy zjišťování: zjišťování na základě zařízení a zjišťování na základě agenta.
Zjišťování na základě zařízení shromažďuje metadata o místních virtuálních počítačů, úplný seznam metadat shromážděných zařízení je uveden níže:

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

Zjišťování na základě agenta je druhá možnost dostupná vedle zjišťování na základě zařízení a pomáhá zákazníkům [vizualizovat závislosti](how-to-create-group-machine-dependencies.md) virtuálních počítačů v místním prostředí. Agenti závislostí shromažďují podrobnosti, jako je plně kvalifikovaný název domény, operační systém, IP adresa, adresa MAC, procesy spuštěné ve virtuálním počítači a příchozí/odchozí připojení TCP virtuálního počítače. Zjišťování na základě agenta je volitelné a můžete zvolit není nainstalovat agenty, pokud nechcete vizualizovat závislosti virtuálních počítačů.

### <a name="would-there-be-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>By existovat ovlivnit výkon prostředí analyzované hostitele ESXi?

V případě třídy [jedním z přístupů doba zjišťování](https://docs.microsoft.com/azure/migrate/concepts-collector#discovery-methods), aby se daly shromažďovat data o výkonu, úroveň statistiky na vCenter serveru byste museli být nastavená na 3. Nastavení na této úrovni může shromažďovat velké množství dat, která bude uložena v databázi serveru vCenter pro řešení potíží. Proto to může způsobit problémy s výkonem v systému vCenter Server. Na hostiteli ESXi by mělo pouze nepatrný dopad.

Zavedli jsme průběžné profilace údaje o výkonu (což je ve verzi preview). Průběžné profilací, již není potřeba měnit statistiky systému vCenter Server úroveň se spustit posouzení na základě výkonu. Zařízení kolektoru bude nyní profilu místních počítačů k měření data o výkonu virtuálních počítačů. Toto musí téměř žádný vliv na výkon na hostitelích ESXi, stejně jako v systému vCenter Server.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Kde se shromážděná data uložená a jak dlouho?

Data shromažďovaná společností zařízení kolektoru je uložen v umístění Azure, který zadáte při vytváření projektu migrace. Data je bezpečně uložen v rámci předplatného Microsoft a se odstraní při odstranění projektu Azure Migrate.

Pro vizualizaci závislostí Pokud nenainstalujete agenty na virtuální počítače, data shromážděná agenty závislostí uložená v USA v pracovním prostoru Log Analytics vytvořené v rámci předplatného uživatele. Tato data jsou odstraněna při odstranění pracovního prostoru Log Analytics v rámci vašeho předplatného. [Další informace](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Má tato data šifrovat v klidovém stavu a v průběhu přenosu?

Ano, shromážděná data zašifrovaná v klidu i při přenosu. Metadata shromážděné na zařízení bezpečně posílá do služby Azure Migrate přes internet prostřednictvím protokolu https. Shromážděná metadata uložená v [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) a [úložiště objektů blob v Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) v předplatném Microsoft a se šifrují při nečinnosti.

Data shromážděná agenty závislostí je také šifrovaný v přenosu (secure https channel) a je uložen v pracovním prostoru Log Analytics v předplatném uživatele. To se také šifrují při nečinnosti.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Způsob, jakým kolektoru komunikují pomocí systému vCenter Server a služby Azure Migrate?

Zařízení kolektoru se připojuje k systému vCenter Server (port 443) pomocí přihlašovacích údajů, které zadal uživatel v zařízení. Vyžádá si vCenter Server s použitím VMware PowerCLI se získat metadata o virtuálních počítačích spravovaných přes vCenter Server. Shromažďuje obou konfiguračních dat o virtuálních počítačích (jader, paměti, disky, NIC atd.) a také historie výkonu každého virtuálního počítače za poslední měsíc z vCenter serveru. Shromážděná metadata se pak posílají do služby Azure Migrate (přes internet prostřednictvím protokolu https) pro posouzení. [Další informace](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>Můžete připojit stejné zařízení kolektoru na několik serverů vCenter?

Ano, jeden kolektor zařízení je možné zjistit více vCenter servery, ale ne současně. Budete muset spustit zjišťování jeden po druhém.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Šablona OVA používané pro Site Recovery integrovaná OVA použít se službou Azure Migrate?

Aktuálně neexistuje žádná integrace. Na. Šablony pro soubory OVA ve službě Site Recovery se používá k nastavení konfiguračního serveru Site Recovery pro replikaci virtuálních počítačů VMware nebo fyzický server. Na. Soubory OVA použít se službou Azure Migrate se používá ke zjišťování virtuálních počítačů VMware, které jsou spravovány serverem vCenter, pro účely posouzení migrace.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>Můžu změnit svoji velikost počítače. Můžete znovu spustit posouzení?

Pokud změníte nastavení na virtuálním počítači, kterou chcete posoudit, aktivační události zjišťování znovu pomocí zařízení kolektoru. V zařízení, použijte **spustit shromažďování znovu** možnost to udělat. Jakmile se shromažďování dokončí, vyberte možnost **Přepočítat** pro posouzení na portálu. Zobrazíte tak aktualizované výsledky posouzení.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Jak lze zjistit prostředí s více tenanty ve službě Azure Migrate?

Pokud máte prostředí, které se sdílejí napříč tenanty a nechcete ke zjištění virtuálních počítačů z jednoho tenanta v jiném tenantovi předplatné, můžete použít pole oboru v zařízení kolektoru k určení rozsahu zjišťování. Pokud klienti sdílejí hostitele, vytvoření přihlašovacích údajů, který má přístup jen pro čtení na pouze virtuální počítače patří do konkrétního tenanta a použít tyto přihlašovací údaje v zařízení kolektoru a zadejte rozsah jako hostitele, které chcete zjišťování. Alternativně můžete také vytvořit složky v systému vCenter Server (Řekněme složku1 pro tenant1 a slozka2 pro tenant2), v rámci sdílené hostitele, virtuální počítače pro tenant1 do složku1 a tenant2 přesunout do slozka2 a odpovídajícím způsobem určit obor zjišťování v kolektoru zadáním příslušné složky.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Kolik virtuálních počítačů se dají zjistit ve jedna migrace projektu?

Je možné vyhledat 1 500 virtuálních počítačů v jedné migrace projektu. Pokud máte další počítače ve vašem místním prostředí [Další](how-to-scale-assessment.md) o způsob, jak můžete zjišťovat velký prostředí ve službě Azure Migrate.


## <a name="assessment"></a>Posouzení

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Odhad nákladů podpory Azure Migrate na základě smlouvy Enterprise (EA)?

Azure Migrate aktuálně nepodporuje odhad nákladů pro [nabídky Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/). Alternativním řešením je zadat jako nabídky a ručně určení procento slevy (platí pro předplatné) v poli "Slevy" vlastnosti posouzení s průběžnými platbami.

  ![Sleva](./media/resources-faq/discount.png)

### <a name="what-is-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>Jaký je rozdíl mezi jako on-premises velikost a velikost na základě výkonu?

Pokud určíte kritérium určení velikosti bude jako typu místní – místní změny velikosti, Azure Migrate nebere v úvahu data o výkonu virtuálních počítačů a velikosti virtuálních počítačů založených na místní konfiguraci. Pokud kritérium určení velikosti na základě výkonu, velikost se provádí na základě dat využití. Například, pokud existuje místní virtuální počítač se 4 jádry a 8 GB paměti s 50 % využití CPU a využití paměti 50 %. Pokud je kritérium určení velikosti jako místní změny velikosti skladovou Položku virtuálního počítače Azure se 4 jádry a 8GB paměti se doporučuje, ale pokud je kritérium určení velikosti na základě výkonu jako skladovou Položku virtuálního počítače 2 jádra a 4 GB by se nedoporučuje používat jako procentuální hodnota využití se považuje za během Doporučujeme velikost. Podobně disků, velikosti disku závisí na dvě vlastnosti posouzení – Změna velikosti kritéria a úložiště typu. Pokud je kritérium určení velikosti na základě výkonu a úložiště typu je automatické, hodnoty IOPS a propustnost disku jsou považovány za identifikaci cílový typ disku (Standard nebo Premium). Pokud je kritérium určení velikosti na základě výkonu a premium je úložiště typu, se doporučuje disk úrovně premium, disk úrovně premium, které vybrali SKU v Azure na základě velikosti na místním disku. Stejnou logiku slouží k určení velikosti disku, pokud je kritérium určení velikosti jako v místním nastavení velikosti a typu úložiště je standard nebo premium.

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>Doporučení velikosti jaký vliv má percentilu a historii využití výkonu?

Tyto vlastnosti platí jenom pro určení velikosti na základě výkonu. Azure Migrate shromažďuje historie výkonu místních počítačů a použije ho k doporučujeme typ velikosti a disku virtuálního počítače v Azure. Zařízení kolektoru průběžně profily v místním prostředí pro shromažďování dat o využití v reálném čase každých 20 sekund. Zařízení shrnuje ukázky 20 sekund a vytvoří jeden datový bod pro každých 15 minut. Pokud chcete vytvořit jeden datový bod, zařízení vybere nejvyšší hodnota ze všech ukázek 20 sekund a odesílá je do Azure. Při vytváření posouzení v Azure, na základě dobu trvání výkonu a hodnota percentilu historie výkonu, Azure Migrate vypočítá hodnotu efektivní využití a použije ho k nastavení velikosti. Například pokud nastavíte dobu trvání výkonu 1 den a percentil hodnoty 95. percentilu, Azure Migrate použije body odesílaných kolekcí pro poslední den, seřazený ve vzestupném pořadí a vybere 95. percentil hodnoty jako efektivní ut vzorku 15 minut ilization. 95. percentil hodnoty zajistí, že se ignoruje všechny odlehlé hodnoty, které můžou mít, pokud vyberete 99. percentilu. Pokud chcete vybrat dobu využití ve špičce a nechcete neproběhly žádné odlehlé hodnoty, měli byste vybrat 99. percentilu.

## <a name="dependency-visualization"></a>Vizualizace závislostí

> [!NOTE]
> Funkce vizualizace závislostí není k dispozici ve službě Azure Government.

### <a name="what-is-dependency-visualization"></a>Co je vizualizace závislostí?

Vizualizace závislostí můžete posouzení skupiny virtuálních počítačů pro migraci s větší jistotou napříč Kontrola závislostí počítačů před spuštěním posouzení. Vizualizace závislostí vám pomůže zajistit, že nic se zachovají, jak se vyhnout nečekaných výpadků při migraci do Azure. Azure Migrate využívá řešení Service Map ve službě Log Analytics umožňuje vizualizace závislostí.

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Musím zaplatit pomocí funkce vizualizace závislostí?

Ne. Další informace o cenách služby Azure Migrate najdete [zde](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>Je potřeba instalovat cokoli, co je pro vizualizace závislostí?

Pokud chcete použít vizualizaci závislostí, budete muset stáhnout a nainstalovat agenty na každém v místním počítači, který chcete vyhodnotit.

- [Microsoft Monitoring agent(MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows) musí být nainstalovaný na každém počítači.
- [Agenta závislostí](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure) musí být nainstalovaný na každém počítači.
- Kromě toho pokud máte počítače bez připojení k Internetu, musíte stáhnout a nainstalovat bránu Log Analytics na ně.

Není nutné tyto agenty na počítačích, které chcete posoudit, pokud používáte vizualizace závislostí.

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Můžete použít stávající pracovní prostor pro vizualizace závislostí?

Ano, Azure Migrate teď umožňuje připojení existujícího pracovního prostoru do projektu migrace a využít pro vizualizace závislostí. [Další informace](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization#how-does-it-work).

### <a name="can-i-export-the-dependency-visualization-report"></a>Můžete exportovat sestavu vizualizace závislostí?

Ne, vizualizace závislostí se nedá exportovat. Ale protože Azure Migrate využívá mapu služeb pro vizualizaci závislostí, můžete použít [rozhraní REST API služby mapy](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) získat závislosti ve formátu json.

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>Jak automatizovat instalaci Microsoft Monitoring Agent (MMA) a agenta závislostí

[Tady](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples) je skript, který můžete použít pro instalaci agenta závislostí. Pro agenta MMA [tady](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab) je k dispozici na webu TechNet, který můžete využít skript.

Kromě skriptů, mohou také využívat nástroje nasazení, jako je System Center Configuration Manageru (SCCM) [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) atd. a nasadit agenty.

### <a name="what-are-the-operating-systems-supported-by-mma"></a>Co jsou operačních systémech podporovaných produktem MMA?

Seznam operačních systémů Windows nepodporuje agenta MMA [tady](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems).
Seznam operačních systémů Linux podporuje MMA [tady](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems).

### <a name="what-are-the-operating-systems-supported-by-dependency-agent"></a>Co jsou operačních systémech podporovaných produktem agenta závislostí?

Seznam operačních systémů Windows nepodporuje agenta závislostí je [tady](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems).
Seznam operačních systémů Linux podporuje agenta závislostí je [tady](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems).

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-one-hour-duration"></a>Je možné vizualizovat závislosti ve službě Azure Migrate déle než hodinu?
Ne, Azure Migrate vám umožňuje vizualizovat závislosti pro dobu trvání až jednu hodinu. Azure Migrate vám umožní přejít zpět na konkrétní datum v historii pro až za poslední měsíc, ale až 1 hodinu, než je maximální doba trvání, pro kterou můžete vizualizovat závislosti. Například můžete použít funkci doba trvání na mapě závislostí, chcete-li zobrazit závislosti včerejška ale jenom ji mohou zobrazit okna jednu hodinu.

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>Platí pro skupiny s více než 10 virtuálních počítačů vizualizace závislostí?
Je možné [vizualizace závislostí u skupin](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies) , že mají až na 10 virtuálních počítačů, pokud máte skupinu s více než 10 virtuálních počítačů, doporučujeme rozdělit skupiny v menším skupinám a vizualizace závislostí.


## <a name="next-steps"></a>Další postup

- Přečtěte si [přehled Azure Migrate](migrate-overview.md)
- Přečtěte si, jak [zkoumat a posuzovat](tutorial-assessment-vmware.md) prostředí VMware
