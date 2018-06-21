---
title: Azure migrovat – nejčastější dotazy (FAQ) | Microsoft Docs
description: Nejčastější dotazy na migraci Azure adresy
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: snehaa
ms.openlocfilehash: db47f774dd4f73692d0b6ab2c4a511f687864e37
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285701"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure migrovat – nejčastější dotazy (FAQ)

Tento článek obsahuje nejčastější dotazy týkající se migrace Azure. Pokud máte další dotazy, po přečtení tohoto článku, odešlete je na [fórum pro migraci Azure](http://aka.ms/AzureMigrateForum).

## <a name="general"></a>Obecné

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Jak se migrovat Azure liší od Azure Site Recovery?

Azure migrace je assessment služba, která vám pomůže odhalovat vaše místní úlohy a plánování migrace do Azure. [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure), nejen pro řešení zotavení po havárii, umožňuje migrovat místní úlohy virtuálních počítačů IaaS v Azure.

### <a name="how-is-azure-migrate-different-from-azure-site-recovery-deployment-planner"></a>Jak se migrovat Azure liší od Azure Site Recovery nasazení Planner?

Nástroje pro plánování migrace je Azure migrací a Azure Site Recovery nasazení Planner zotavení po havárii (DR), nástroje pro plánování.

**Migrace z VMware do Azure**: Pokud máte v úmyslu migrovat místní úlohy do Azure, použijte migrovat Azure pro plánování migrace. Azure migrací vyhodnocuje místní úlohy a poskytuje pokyny, přehledy a mechanismy, které vám pomohou při migraci na Azure. Až budete připravení s váš plán migrace, můžete služby, například Azure Site Recovery a služba migrace databáze Azure migrace počítačů do Azure.

**Migrace z technologie Hyper-V do Azure**: migrace Azure aktuálně podporuje jenom assessment virtuálních počítačů VMware pro migraci do Azure. Podpora technologie Hyper-V je plánovaná pro migraci na Azure. Do té doby můžete použít Plánovač nasazení automatické obnovení systému. Jakmile v Azure migrace je zapnutá podpora technologie Hyper-V, můžete migrovat Azure pro plánování migrace úlohy Hyper-V.

**Zotavení po havárii z VMware nebo Hyper-V do Azure**: Pokud máte v úmyslu provést zotavení po havárii (DR) v Azure pomocí Azure Site Recovery (ASR), použijte Plánovač nasazení automatické obnovení systému pro zotavení po Havárii plánování. Automatické obnovení systému nasazení Planner nemá hloubkové, specifické pro automatické obnovení systému vyhodnocení vašeho místního prostředí. Poskytuje doporučení, které jsou vyžadované automatické obnovení systému pro úspěšné operace zotavení po Havárii, jako je například replikace, převzetí služeb při selhání virtuálních počítačů.  

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>VCenter Server za účelem zjištění prostředí VMware musí migrovat Azure?

Ano, Azure migrace vyžaduje vCenter Server za účelem zjištění prostředí VMware. Nepodporuje zjišťování hostitelích ESXi, které nejsou spravované přes vCenter Server.

### <a name="which-azure-regions-are-supported-by-azure-migrate"></a>Které oblasti Azure podporuje migraci Azure?

Azure migrací teď podporuje Východ USA a západní centrální USA jako umístění projektu. Všimněte si, že i když migrace projekty může vytvořit pouze ve – Západ střední USA a Východ USA, můžete stále vyhodnotit, s vašich počítačů pro [více cílových míst](https://docs.microsoft.com/azure/migrate/how-to-modify-assessment#edit-assessment-properties). Umístění projektu slouží pouze k uložení zjištěná data.

## <a name="discovery"></a>Zjišťování

### <a name="what-data-is-collected-by-azure-migrate"></a>Jaká data se shromažďují pomocí Azure migrovat?

Azure migrací podporuje dva druhy zjišťování založené na agentovi, na základě zařízení zjišťování a zjišťování.
Zjišťování na základě zařízení shromažďuje metadata o virtuálních počítačích na místě, úplný seznam metadata shromažďují zařízení je uveden níže:

**Data konfigurace virtuálního počítače**
- Zobrazovaný název virtuálního počítače (na vCenter)
- Cesta inventáře virtuálních počítačů (hostitele nebo clusteru/složku v systému vCenter)
- IP adresa
- Adresa MAC
- Operační systém
- Počet jader, disků, síťových adaptérů
- Velikost paměti, velikosti disků

**Údaje o výkonu virtuálního počítače**
- Využití procesoru
- Využití paměti
- Pro každý disk připojen k virtuálnímu počítači:
  - Propustnost čtení disku
  - Rychlost zapisování na disk
  - Čtení disku operací za sekundu
  - Operací za sekundu zápisu disku
- Pro každý síťový adaptér připojený k virtuálnímu počítači:
  - Sítě v
  - Sítě out

Zjišťování na základě agenta možnost je k dispozici nad zjišťování na základě zařízení a pomáhá zákazníkům [vizualizovat závislosti](how-to-create-group-machine-dependencies.md) místní virtuálních počítačů. Agenti závislostí shromažďovat podrobnosti, třeba plně kvalifikovaný název domény, operační systém, IP adresa, adresa MAC, procesy, které běží uvnitř virtuálního počítače a připojení TCP příchozí nebo odchozí z virtuálního počítače. Zjišťování na základě agenta je volitelný a můžete k není instalaci agentů, pokud nechcete vizualizovat závislosti virtuálních počítačů.

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>Kde je shromážděných datech uložených a jak dlouho?

Data shromažďovaná společností kolekce zařízení je uložený v umístění Azure, který zadáte během vytváření projektu migrace. Data je bezpečně uloženo na předplatné Microsoft a se odstraní při odstranění projektu Azure migrovat.

Vizualizace závislostí při instalaci agentů na virtuálních počítačích, data shromažďovaná společností agenty závislostí ukládají v USA v pracovním prostoru OMS vytvořené v odběru uživatele. Tato data jsou odstraněna, když uživatel odstraní pracovní prostor OMS v jeho nebo jeho předplatného. [Další informace](https://docs.microsoft.com/azure/migrate/concepts-dependency-visualization).

### <a name="how-does-the-collector-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Jak kolekce komunikují s vCenter Server a službu Azure migrovat?

Kolekce zařízení se připojuje k systému vCenter Server (port 443) pomocí přihlašovacích údajů zadaných uživatelem v zařízení. Vyžádá si vCenter serveru pomocí VMware PowerCLI ke shromažďování metadata o virtuálních počítačů spravovaných pomocí systému vCenter Server. Shromáždí obou konfigurační data o virtuálních počítačích (jádra, paměti, disky, seskupování atd.) a také historie výkonu každého virtuálního počítače pro poslední jeden měsíc ze serveru vCenter Server. Shromážděných metadat se pak posílají do služby Azure migrovat (přes internet prostřednictvím protokolu https) pro vyhodnocení. [Další informace](concepts-collector.md)

### <a name="is-the-data-encrypted-at-rest-and-while-in-transit"></a>Má tato data šifrovat v klidovém stavu a v průběhu přenosu?

Ano, shromážděná data se v klidu i při přenosu zašifrován. Metadata shromažďují zařízení je bezpečně odešle do služby Azure migrovat přes internet prostřednictvím protokolu https. Shromážděné metadata se ukládají do [Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest) a v [úložiště objektů blob Azure](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) v rámci předplatného Microsoft a zašifrovaná přinejmenším.

Data shromažďovaná společností agenty závislostí je také přenosu v šifrované (https zabezpečený kanál) a je uložen v prostoru analýzy protokolů v rámci předplatného uživatele. Se šifrují taky v klidovém stavu.

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>Jak může zjišťovat prostředí s více klienty v Azure migrovat?

Pokud máte prostředí, který je sdílen mezi klienty a nechcete zjistit virtuální počítače jednoho klienta v rámci jiného klienta předplatného, pole obor v kolekci zařízení můžete použít k určení rozsahu zjišťování. Pokud klienti sdílejí hostitele, vytvoření přihlašovacích údajů, který má přístup jen pro čtení k jenom virtuální počítače patřící do určité klienta a použít tento přihlašovací údaj v zařízení kolekce a zadejte rozsah jako hostitel udělat zjišťování. Alternativně můžete také vytvořit složky v systému vCenter Server (Řekněme složku1 pro tenant1 a složka2 pro tenant2), pod sdílené hostitele, přesunutí virtuálních počítačů pro tenant1 do složku1 a tenant2 do složka2 a odpovídajícím způsobem určit obor zjišťování v kolekci zadáním příslušné složky.

### <a name="how-many-virtual-machines-can-be-discovered-in-a-single-migration-project"></a>Kolik virtuálních počítačů můžete zjistit v jednom migrace projektu?

Může zjišťovat 1500 virtuálních počítačů v jedné migrace projektu. Pokud máte další počítače ve vašem prostředí místní [Další](how-to-scale-assessment.md) o tom, jak může zjišťovat velký prostředí v Azure migrovat.

## <a name="dependency-visualization"></a>Vizualizace závislostí

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>Je potřeba věnovat používat funkci vizualizace závislostí?

Služba Azure Migrate je dostupná bez dalších poplatků. Další informace o cenách služby Azure Migrate najdete [zde](https://azure.microsoft.com/pricing/details/azure-migrate/).

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>Můžete použít existujícímu pracovnímu prostoru pro vizualizaci závislostí?

Azure migrace nepodporuje použití existujícímu pracovnímu prostoru pro vizualizaci závislostí, ale Microsoft Monitoring Agent (MMA) podporuje více domovských stránek a umožňuje odesílat data do více pracovních prostorů. Takže pokud již máte agenty, nasadit a nakonfigurovat tak, aby pracovního prostoru, můžete využívat více domovských stránek v agenta MMA a nakonfigurovat ji do pracovního prostoru migrovat Azure (kromě existujícímu pracovnímu prostoru) a aby fungoval. [Zde](https://blogs.technet.microsoft.com/msoms/2016/05/26/oms-log-analytics-agent-multi-homing-support/) je blog na tom, jak můžete povolit více domovských stránek v agenta MMA.

## <a name="next-steps"></a>Další postup

- Pro čtení [migrovat Azure – přehled](migrate-overview.md)
- Přečtěte si, jak [zjišťovat a vyhodnocení](tutorial-assessment-vmware.md) prostředí VMware
