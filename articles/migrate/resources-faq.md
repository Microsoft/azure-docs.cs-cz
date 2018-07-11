---
title: Azure Migrate – nejčastější dotazy (FAQ) | Dokumentace Microsoftu
description: Nejčastější dotazy k Azure Migrate adresy
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: snehaa
ms.openlocfilehash: 3f035f38b1ad68e9e39d151ffad3fc650a0a1d80
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952745"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate – nejčastější dotazy (FAQ)

Tento článek obsahuje nejčastější dotazy týkající se Azure Migrate. Pokud máte další dotazy, po přečtení tohoto článku, publikujte je na [fórum pro Azure Migrate](http://aka.ms/AzureMigrateForum).

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

**Migraci z VMware do Azure**: Pokud máte v úmyslu migrace místních úloh do Azure, použijte Azure Migrate týkající se plánování migrace. Azure Migrate posuzuje místní úlohy a poskytuje pokyny, přehledy a mechanismy, které vám pomohou při migraci do Azure. Až budete připravení plán migrace zrealizovat, můžete počítače migrovat do Azure služeb, jako je Azure Site Recovery a Azure Database Migration Service.

**Migrace z technologie Hyper-V do Azure**: Azure Migrate aktuálně podporuje pouze posouzení virtuálních počítačů VMware pro migraci do Azure. Podpora technologie Hyper-V je na plán služby pro Azure Migrate. Prozatím můžete použít Plánovač nasazení služby Site Recovery. Jakmile se ve službě Azure Migrate je povolena podpora technologie Hyper-V, můžete použít Azure Migrate týkající se plánování migrace úloh Hyper-V.

**Zotavení po havárii z VMware/Hyper-V do Azure**: Pokud máte v úmyslu provést zotavení po havárii (DR) v Azure pomocí Azure Site Recovery (služba Site Recovery), použijte Plánovač nasazení služby Site Recovery pro plánování zotavení po Havárii. Plánovač nasazení služby Site Recovery nemá podrobné, specifické pro Azure Site Recovery hodnocení v místním prostředí. Poskytuje doporučení, která jsou vyžadované Site Recovery pro úspěšné operace zotavení po Havárii, jako je replikace, převzetí služeb při selhání virtuálních počítačů.  

### <a name="which-azure-regions-are-supported-by-azure-migrate"></a>Které oblasti Azure jsou podporovány službou Azure Migrate?

Azure Migrate aktuálně podporuje východní USA a střed USA – Západ jako umístění projektu migrace. Všimněte si, že i v případě, že projekty migrace můžete vytvořit jenom v západní USA – střed a východní USA, můžete přesto posoudit, s u svých počítačů [více cílových míst](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). Umístění projektu slouží pouze k uložení zjištěná data.

### <a name="how-does-the-on-premises-site-connect-to-azure-migrate"></a>Jak v místní lokalitě připojit k Azure Migrate?

Připojení může být přes internet nebo pomocí veřejného partnerského vztahu ExpressRoute.

### <a name="can-i-harden-the-vm-set-up-with-the-ova-template"></a>Můžete posílení virtuálního počítače s. Šablony pro soubory OVA?

Další součásti (například antivirový program) mohou být přidány do. Šablony pro soubory OVA za předpokladu, jako jsou ponechána komunikaci a pravidel brány firewall vyžadované pro zařízení Azure Migrate pro práci se.   

## <a name="discovery-and-assessment"></a>Zjišťování a posouzení

### <a name="what-data-is-collected-by-azure-migrate"></a>Jaká data se shromažďují službou Azure Migrate?

Azure Migrate podporuje dva druhy zjišťování, zjišťování na základě zařízení a zjišťování na základě agenta.
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

Zjišťování na základě agenta možnost je k dispozici nad rámec zjišťování na základě zařízení a pomáhá zákazníkům [vizualizace závislostí](how-to-create-group-machine-dependencies.md) virtuálních počítačů v místním prostředí. Agenti závislosti shromažďování podrobností jako plně kvalifikovaný název domény, operačního systému, IP adresa, adresa MAC, spuštěné procesy v rámci virtuálního počítače a příchozí/odchozí připojení TCP z virtuálního počítače. Zjišťování na základě agenta je volitelné a můžete zvolit není nainstalovat agenty, pokud nechcete vizualizovat závislosti virtuálních počítačů.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Kde se shromážděná data uložená a jak dlouho?

Data shromažďovaná společností zařízení kolektoru je uložen v umístění Azure, který zadáte při vytváření projektu migrace. Data je bezpečně uložen v rámci předplatného Microsoft a se odstraní při odstranění projektu Azure Migrate.

Pro vizualizaci závislostí, při instalaci agentů na virtuálních počítačích, data shromážděná agenty závislostí se ukládají v USA v pracovním prostoru OMS vytvoří v předplatném uživatele. Tato data jsou odstraněna při odstranění pracovního prostoru OMS ve vašem předplatném. [Další informace](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Má tato data šifrovat v klidovém stavu a v průběhu přenosu?

Ano, shromážděná data zašifrovaná v klidu i při přenosu. Metadata shromážděné na zařízení bezpečně posílá do služby Azure Migrate přes internet prostřednictvím protokolu https. Shromážděná metadata uložená v [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) a [úložiště objektů blob v Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) v předplatném Microsoft a se šifrují při nečinnosti.

Data shromážděná agenty závislostí je také šifrovaný v přenosu (secure https channel) a je uložen v pracovním prostoru Log Analytics v předplatném uživatele. To se také šifrují při nečinnosti.

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Způsob, jakým kolektoru komunikují pomocí systému vCenter Server a služby Azure Migrate?

Zařízení kolektoru se připojuje k systému vCenter Server (port 443) pomocí přihlašovacích údajů, které zadal uživatel v zařízení. Vyžádá si vCenter Server s použitím VMware PowerCLI se získat metadata o virtuálních počítačích spravovaných přes vCenter Server. Shromažďuje obou konfiguračních dat o virtuálních počítačích (jader, paměti, disky, NIC atd.) a také historie výkonu každého virtuálního počítače za poslední měsíc z vCenter serveru. Shromážděná metadata se pak posílají do služby Azure Migrate (přes internet prostřednictvím protokolu https) pro posouzení. [Další informace](concepts-collector.md)

### <a name="can-i-connect-the-same-collector-appliance-to-multiple-vcenter-servers"></a>Můžete připojit stejné zařízení kolektoru na několik serverů vCenter?

Ano, jeden kolektor zařízení je možné zjistit více vCenter servery, ale ne současně. Budete muset spustit zjišťování jeden po druhém.

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Je. Soubory OVA šablony používané pro Site Recovery integrovaná. Soubory OVA použít se službou Azure Migrate?

Aktuálně neexistuje žádná integrace. Na. Šablony pro soubory OVA ve službě Site Recovery se používá k nastavení konfiguračního serveru Site Recovery pro replikaci virtuálních počítačů VMware nebo fyzický server. Na. Soubory OVA použít se službou Azure Migrate se používá ke zjišťování virtuálních počítačů VMware, které jsou spravovány serverem vCenter, pro účely posouzení migrace.

### <a name="i-changed-my-machine-size-can-i-rerun-the-assessment"></a>Můžu změnit svoji velikost počítače. Můžete znovu spustit posouzení?

Pokud změníte nastavení na virtuálním počítači, kterou chcete posoudit, aktivační události zjišťování znovu pomocí zařízení kolektoru. V zařízení, použijte **spustit shromažďování znovu** možnost to udělat. Po dokončení kolekce, vyberte **přepočítat** možnost pro posouzení na portálu, zobrazíte výsledky posouzení aktualizované.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Jak lze zjistit prostředí s více tenanty ve službě Azure Migrate?

Pokud máte prostředí, které se sdílejí napříč tenanty a nechcete ke zjištění virtuálních počítačů z jednoho tenanta v jiném tenantovi předplatné, můžete použít pole oboru v zařízení kolektoru k určení rozsahu zjišťování. Pokud klienti sdílejí hostitele, vytvoření přihlašovacích údajů, který má přístup jen pro čtení na pouze virtuální počítače patří do konkrétního tenanta a použít tyto přihlašovací údaje v zařízení kolektoru a zadejte rozsah jako hostitele, které chcete zjišťování. Alternativně můžete také vytvořit složky v systému vCenter Server (Řekněme složku1 pro tenant1 a slozka2 pro tenant2), v rámci sdílené hostitele, virtuální počítače pro tenant1 do složku1 a tenant2 přesunout do slozka2 a odpovídajícím způsobem určit obor zjišťování v kolektoru zadáním příslušné složky.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Kolik virtuálních počítačů se dají zjistit ve jedna migrace projektu?

Je možné vyhledat 1 500 virtuálních počítačů v jedné migrace projektu. Pokud máte další počítače ve vašem místním prostředí [Další](how-to-scale-assessment.md) o způsob, jak můžete zjišťovat velký prostředí ve službě Azure Migrate.

## <a name="dependency-visualization"></a>Vizualizace závislostí

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Musím zaplatit pomocí funkce vizualizace závislostí?

Služba Azure Migrate je dostupná bez dalších poplatků. Další informace o cenách služby Azure Migrate najdete [zde](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Můžete použít stávající pracovní prostor pro vizualizace závislostí?

Azure Migrate nepodporuje použití existujícího pracovního prostoru pro vizualizaci závislostí, ale Microsoft Monitoring Agent (MMA) podporuje vícenásobné navádění a umožňuje vám to poslat data do několika pracovních prostorů. Proto pokud už máte agenty, nasadit a nakonfigurovat tak, aby pracovní prostor, můžete využít vícenásobné navádění v agenta MMA a nakonfigurovat ji do pracovního prostoru Azure Migrate (navíc k existující pracovní prostor) a jí umožňují fungovat. [Tady](https://blogs.technet.microsoft.com/msoms/2016/05/26/oms-log-analytics-agent-multi-homing-support/) je blogu o tom, jak můžete povolit vícenásobné navádění v agenta MMA.

## <a name="next-steps"></a>Další postup

- Přečtěte si [přehled Azure Migrate](migrate-overview.md)
- Přečtěte si, jak [zkoumat a posuzovat](tutorial-assessment-vmware.md) prostředí VMware
