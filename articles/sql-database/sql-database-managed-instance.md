---
title: Přehled spravované instance SQL
description: Tento článek popisuje Azure SQL Database spravovanou instanci.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: sstein, carlrab, vanto
ms.date: 01/21/2020
ms.openlocfilehash: b9fdd1b25e53e1cdc8aa76564304a61adaa8d804
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201590"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Co je Azure SQL Database spravovaná instance?

Managed instance je nová možnost nasazení Azure SQL Database, která poskytuje téměř 100% kompatibilitu s nejnovějším databázovým strojem SQL Server on-premises (Enterprise Edition), zajišťuje implementaci nativní [virtuální sítě (VNET)](../virtual-network/virtual-networks-overview.md) , která řeší běžné bezpečnostní obavy a [obchodní model](https://azure.microsoft.com/pricing/details/sql-database/) , který je pro místní SQL Server zákazníky příznivý. Model nasazení spravované instance umožňuje stávajícím zákazníkům SQL Server překládat a přesouvat místní aplikace do cloudu s minimálními změnami aplikace a databáze. Zároveň možnost nasazení spravované instance zachovává všechny možnosti PaaS (automatické opravy a aktualizace verzí, [automatizované zálohování](sql-database-automated-backups.md)a [vysokou dostupnost](sql-database-high-availability.md) ), což významně snižuje režijní náklady na správu a celkové náklady na vlastnictví.

> [!IMPORTANT]
> Seznam oblastí, ve kterých je možnost nasazení spravované instance aktuálně k dispozici, najdete v tématu [podporované oblasti](sql-database-managed-instance-resource-limits.md#supported-regions).

Následující diagram popisuje klíčové funkce spravovaných instancí:

![klíčové funkce](./media/sql-database-managed-instance/key-features.png)

Model nasazení spravované instance je určený pro zákazníky, kteří chtějí migrovat velký počet aplikací z místního prostředí nebo prostředí IaaS, samoobslužného nebo nezávislého výrobce softwaru (ISV) do plně spravovaného cloudového prostředí PaaS, s co nejmenším možným úsilím při migraci. Pomocí plně automatizované [služby pro migraci dat (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) v Azure mohou zákazníci přenášet a přesouvat místní SQL Server do spravované instance, která nabízí kompatibilitu s SQL serverou v místním prostředí a dokončí izolaci zákaznických instancí s nativní podporou virtuální sítě.  Se Software Assurance vám umožní vyměňovat si stávající licence pro zvýhodněné sazby spravované instance pomocí [zvýhodněné hybridní využití Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  Spravovaná instance je nejlepší cíl migrace v cloudu pro SQL Server instance, které vyžadují vysoké zabezpečení a bohatou programovatelné plochu.

Cílem možnosti nasazení Managed instance je dosahovat téměř 100% kompatibility Surface Surface s nejnovější místní SQL Server verzí prostřednictvím plánu vydaných verzí.

Pokud se chcete rozhodnout mezi možnostmi nasazení Azure SQL Database: izolovaná databáze, databáze ve fondu a spravovaná instance a SQL Server hostovaná na virtuálním počítači, přečtěte si téma [Jak zvolit správnou verzi SQL Server v Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Klíčové funkce a možnosti

Spravovaná instance kombinuje nejlepší funkce, které jsou k dispozici v Azure SQL Database i SQL Server databázovém stroji.

> [!IMPORTANT]
> Spravovaná instance se spouští se všemi funkcemi nejnovější verze SQL Server, včetně online operací, automatických oprav plánů a dalších vylepšení výkonu podniku. Porovnání funkcí, které jsou k dispozici, je vysvětleno v tématu [porovnání funkcí: Azure SQL Database oproti SQL Server](sql-database-features.md).

| **Výhody PaaS** | **Kontinuita podnikových procesů** |
| --- | --- |
|Bez nákupu a správy hardwaru <br>Žádná režie správy pro správu základní infrastruktury <br>Rychlé zřizování a škálování služby <br>Automatizované opravy a upgrade verze <br>Integrace s jinými datovými službami PaaS |Smlouva SLA o 99,99% provozu  <br>Integrovaná [Vysoká dostupnost](sql-database-high-availability.md) <br>Data chráněná pomocí [automatizovaných záloh](sql-database-automated-backups.md) <br>Uživatelsky konfigurovatelné období uchovávání záloh pro zákazníky <br>[Zálohy](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) iniciované uživatelem <br>Možnost [obnovení databáze bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Zabezpečení a dodržování předpisů** | **Správu**|
|Izolované prostředí ([Integrace virtuální](sql-database-managed-instance-connectivity-architecture.md)sítě, samostatná služba tenanta, vyhrazený výpočetní výkon a úložiště) <br>[Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Ověřování Azure AD](sql-database-aad-authentication.md), podpora jednotného přihlašování <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Objekty zabezpečení serveru Azure AD (přihlášení)</a>  <br>Dodržuje standardy dodržování předpisů stejné jako Azure SQL Database. <br>[Auditování SQL](sql-database-managed-instance-auditing.md) <br>[Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) |Rozhraní Azure Resource Manager API pro automatizaci zřizování a škálování služby <br>Azure Portal funkce ručního zřizování a škálování služeb <br>Služba migrace dat

> [!IMPORTANT]
> Azure SQL Database (všechny možnosti nasazení) byly certifikovány na základě řady standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) , kde můžete najít nejaktuálnější seznam SQL Database certifikace dodržování předpisů.

Klíčové funkce spravovaných instancí jsou uvedené v následující tabulce:

|Funkce | Popis|
|---|---|
| Verze SQL Server/Build | Databázový stroj SQL Server (nejnovější stabilní) |
| Spravované automatizované zálohy | Ano |
| Integrovaná instance a monitorování databáze a metriky | Ano |
| Automatické opravy softwaru | Ano |
| Nejnovější funkce databázového stroje | Ano |
| Počet datových souborů (řádků) na databázi | Více |
| Počet souborů protokolu (protokol) na databázi | 1 |
| Nasazení VNet-Azure Resource Manager | Ano |
| Model nasazení sítě VNet – klasický | Ne |
| Podpora portálu | Ano|
| Integrovaná integrační služba (SSIS) | No-SSIS je součástí [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Integrovaná služba Analysis Service (SSAS) | No-SSAS je samostatný [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Integrovaná služba vytváření sestav (SSRS) | Bez použití Power BI nebo SSRS IaaS |
|||

## <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

[Nákupní model založený na Vcore](sql-database-service-tiers-vcore.md) pro spravované instance poskytuje flexibilitu, kontrolu, transparentnost a snadný způsob, jak převést požadavky na místní úlohy do cloudu. Tento model umožňuje změnit výpočetní prostředky, paměť a úložiště na základě potřeb vašich úloh. VCore model má taky nárok na úspory až 55 procent a [Zvýhodněné hybridní využití Azure SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

V modelu vCore si můžete vybrat mezi generacemi hardwaru.

- **COMPUTE GEN4 –** Logické procesory jsou založené na procesorech Intel E5-2673 V3 (Haswell) 2,4, Attached SSD, fyzických jader, 7-GB RAM na jádro a výpočetní velikosti mezi 8 a 24 virtuální jádra.
- **Gen5** Logické procesory jsou založené na procesorech Intel E5-2673 v4 (Broadwell) 2,3 a Intel SP-8160 (Skylake), Fast NVMe SSD, logických jader vláken a výpočetních velikostí mezi 4 a 80 jádry.

Přečtěte si další informace o rozdílech mezi generacemi hardwaru v [omezeních prostředků spravované instance](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Úrovně služby spravované instance

Spravovaná instance je k dispozici ve dvou úrovních služeb:

- **Obecné účely**: slouží pro aplikace s typickými požadavky na výkon a latenci v/v.
- **Důležité pro podnikání**: určené pro aplikace s nízkými nároky na latenci v/v a minimálním dopadem na základní operace údržby na zatížení.

Obě úrovně služeb zaručují 99,99% dostupnost a umožňují nezávisle vybrat velikost úložiště a výpočetní kapacitu. Další informace o architektuře Azure SQL Database s vysokou dostupností najdete v tématu [Vysoká dostupnost a Azure SQL Database](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Úroveň služby pro obecné účely

Následující seznam popisuje klíčové charakteristiky Pro obecné účely úrovně služeb:

- Návrh většiny obchodních aplikací s typickými požadavky na výkon
- Vysoce výkonné úložiště Azure Blob Storage (8 TB)
- Integrovaná [Vysoká dostupnost](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) založená na spolehlivých úložištích Azure Blob a [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Další informace najdete v tématech [vrstva úložiště v části Obecné účely](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) a [osvědčené postupy pro výkon úložiště a požadavky na spravované instance (obecné účely)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Přečtěte si další informace o rozdílu mezi úrovněmi služeb v [omezeních prostředků spravované instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Úroveň služby Pro důležité obchodní informace

Úroveň služby Pro důležité obchodní informace je sestavená pro aplikace s vysokými požadavky na vstupně-výstupní operace. Nabízí nejvyšší odolnost proti chybám při použití několika izolovaných replik.

Následující seznam popisuje klíčové charakteristiky Pro důležité obchodní informace úrovně služeb:

- Navrženo pro obchodní aplikace s nejvyšším výkonem a požadavky na HA
- Obsahuje vysoce rychlé místní úložiště SSD (až do 1 TB v COMPUTE GEN4 – a až 4 TB v Gen5).
- Integrovaná [Vysoká dostupnost](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) na základě [skupin dostupnosti Always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) a [Azure Service Fabric](../service-fabric/service-fabric-overview.md).
- Integrovaná další [replika databáze jen pro čtení](sql-database-read-scale-out.md) , která se dá použít pro vytváření sestav a další úlohy jen pro čtení
- [OLTP v paměti](sql-database-in-memory.md) , které se dají použít pro úlohy s vysokými nároky na výkon  

Přečtěte si další informace o rozdílu mezi úrovněmi služeb v [omezeních prostředků spravované instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).


## <a name="managed-instance-management-operations"></a>Operace správy spravované instance

Azure SQL Database nabízí operace správy, které můžete využít k automatickému nasazování nových spravovaných instancí, aktualizaci vlastností instancí a odstraňování instancí v případě, že už je nepotřebujete. V této části najdete informace o operacích správy a jejich typických trváních.

Za účelem podpory [nasazení v rámci virtuálních sítí Azure (virtuální sítě)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) a poskytování izolace a zabezpečení pro zákazníky se spravovaná instance spoléhá na [virtuální clustery](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture), které představují vyhrazenou sadu izolovaných virtuálních počítačů nasazených v podsíti virtuální sítě zákazníka. Každé nasazení spravované instance v rámci prázdné podsítě má za následek nové sestavení virtuálních clusterů.

Následné operace na nasazených spravovaných instancích můžou mít vliv i na svůj základní virtuální cluster. To ovlivňuje dobu trvání operací správy, protože nasazení dalších virtuálních počítačů přináší režii, která je potřeba vzít v úvahu při plánování nových nasazení nebo aktualizací existujících spravovaných instancí.

Všechny operace správy je možné uspořádat do následujících kategorií:

- Nasazení instance (vytvoření nové instance). 
- Aktualizace instance (Změna vlastností instance, jako je virtuální jádra nebo vyhrazené úložiště
- Odstranění instance.

Operace s virtuálními clustery se obvykle vybírají nejdéle. Doba trvání operací na virtuálních clusterech se liší – níže jsou uvedené hodnoty, které obvykle můžete očekávat na základě stávajících dat telemetrie služby:

- Vytváření virtuálních clusterů. Toto je synchronní krok při operacích správy instancí. **90% dokončených operací za 4 hodiny**.
- Změna velikosti virtuálního clusteru (rozšiřování nebo zmenšování). Rozšíření je synchronní krok, zatímco při zmenšování dochází k asynchronnímu zpracování (bez vlivu na dobu trvání operací správy instancí). **90% rozšíření clusteru skončí za méně než 2,5 hodin**.
- Odstranění virtuálního clusteru Odstranění je asynchronní krok, ale dá se taky [iniciovat ručně](sql-database-managed-instance-delete-virtual-cluster.md) v prázdném virtuálním clusteru. v takovém případě se to provede synchronně. **90% odstranění virtuálních clusterů se dokončí za 1,5 hodin**.

Kromě toho může Správa instancí zahrnovat taky jednu z operací na hostovaných databázích, což vede k delší dobu trvání:

- Připojení souborů databáze z Azure Storage. Jedná se o synchronní krok, například výpočetní výkon (vCore) nebo škálování úložiště v úrovni služby Pro obecné účely. **90% těchto operací skončí za 5 minut**.
- Vysazení skupiny dostupnosti Always On. Jedná se o synchronní krok, například výpočetní výkon (vCore) nebo škálování úložiště v úrovni služby Pro důležité obchodní informace, a také v části Změna úrovně služby z Pro obecné účely na Pro důležité obchodní informace (nebo naopak). Doba trvání této operace je úměrná celkové velikosti databáze a aktuální aktivitě databáze (počet aktivních transakcí). Databázová aktivita při aktualizaci instance může způsobit značnou odchylku od celkové doby trvání. **90% těchto operací se spustí v 220 GB/hod nebo vyšších**.

V následující tabulce najdete souhrn operací a typických celkových dob trvání:

|Kategorie  |Operace  |Dlouho běžící segment  |Odhadovaná doba trvání  |
|---------|---------|---------|---------|
|**Nasazení** |První instance v prázdné podsíti|Vytvoření virtuálního clusteru|90% dokončených operací za 4 hodiny|
|Nasazení |První instance jiné generace hardwaru v neprázdné podsíti (například první instance Gen 5 v podsíti s instancemi Gen 4)|Vytváření virtuálních clusterů *|90% dokončených operací za 4 hodiny|
|Nasazení |Vytvoření první instance 4 virtuální jádra, v prázdné nebo neprázdné podsíti|Vytváření virtuálních clusterů * *|90% dokončených operací za 4 hodiny|
|Nasazení |Vytváření dalších instancí v neprázdné podsíti (druhá, třetí atd. instance)|Změna velikosti virtuálního clusteru|90% dokončených operací za 2,5 hodin|
|**Aktualizace** |Změna vlastnosti instance (heslo správce, přihlášení AAD, příznak Zvýhodněné hybridní využití Azure)|NEUŽÍVÁ SE.|Až 1 minuta|
|Aktualizovat |Horizontální navýšení kapacity úložiště instance (Pro obecné účely úroveň služeb)|Připojení souborů databáze|90% dokončených operací za 5 minut|
|Aktualizovat |Horizontální navýšení kapacity úložiště instance (Pro důležité obchodní informace úroveň služeb)|– Změna velikosti virtuálního clusteru<br>– Vysazení skupiny dostupnosti Always On|90% dokončených operací během 2,5 hodin + času pro osazení všech databází (220 GB za hodinu)|
|Aktualizovat |Instance COMPUTE (virtuální jádra) pro škálování směrem nahoru a dolů (Pro obecné účely)|– Změna velikosti virtuálního clusteru<br>-Připojení souborů databáze|90% dokončených operací za 2,5 hodin|
|Aktualizovat |Instance COMPUTE (virtuální jádra) pro škálování směrem nahoru a dolů (Pro důležité obchodní informace)|– Změna velikosti virtuálního clusteru<br>– Vysazení skupiny dostupnosti Always On|90% dokončených operací během 2,5 hodin + času pro osazení všech databází (220 GB za hodinu)|
|Aktualizovat |Škálování instance dolů na 4 virtuální jádra (Pro obecné účely)|– Změna velikosti virtuálního clusteru (při prvním provedení) může vyžadovat vytvoření virtuálního clusteru * *).<br>-Připojení souborů databáze|90% dokončených operací za 4 h 5 min. * *|
|Aktualizovat |Škálování instance dolů na 4 virtuální jádra (Pro důležité obchodní informace)|– Změna velikosti virtuálního clusteru (při prvním provedení) může vyžadovat vytvoření virtuálního clusteru * *).<br>– Vysazení skupiny dostupnosti Always On|90% operací se dokončí během 4 hodiny + čas na osazení všech databází (220 GB za hodinu).|
|Aktualizovat |Instance instance služby instance (Pro obecné účely až Pro důležité obchodní informace a naopak)|– Změna velikosti virtuálního clusteru<br>– Vysazení skupiny dostupnosti Always On|90% dokončených operací během 2,5 hodin + času pro osazení všech databází (220 GB za hodinu)|
|**Změny**|Odstranění instance|Zálohování koncového protokolu pro všechny databáze|90% operací skončilo během až 1 minuty.<br>Poznámka: Pokud se poslední instance v podsíti odstraní, tato operace provede odebrání virtuálního clusteru po 12 hodinách * * *.|
|Odstranění|Odstranění virtuálního clusteru (jako operace iniciované uživatelem)|Odstranění virtuálního clusteru|90% operací se dokončilo během až 1,5 hodin.|

\* virtuální cluster je postaven na generaci hardwaru.

\*\* možnost nasazení 4 virtuální jádra byla vydána v červnu 2019 a vyžaduje novou verzi virtuálního clusteru. Pokud jste v cílové podsíti vytvořili instance, které byly všechny vytvořené před 12. června, automaticky se nasadí nový virtuální cluster na hostitele 4 vCore instance.

\*\*\* 12 hodin je aktuální konfigurace, která se může v budoucnu změnit, takže na ni nemusíte nic udělat. Pokud potřebujete virtuální cluster odstranit dřív (například pro vydání podsítě), přečtěte si téma [odstranění podsítě po odstranění spravované instance Azure SQL Database](sql-database-managed-instance-delete-virtual-cluster.md).

### <a name="instance-availability-during-management"></a>Dostupnost instance během správy

Spravované instance nejsou k dispozici klientským aplikacím během operací nasazení a odstranění.

Spravované instance jsou během operací aktualizace k dispozici, ale při převzetí služeb při selhání dochází k krátkému výpadku, ke kterému dochází na konci aktualizací, které obvykle trvají až 10 sekund. Výjimkou je aktualizace rezervovaného prostoru úložiště v Pro obecné účely úrovni služby, která neumožňuje převzetí služeb při selhání, ani neovlivňuje dostupnost instance.

> [!IMPORTANT]
> Doba trvání převzetí služeb při selhání se může výrazně lišit v případě dlouhotrvajících transakcí, ke kterým dochází v databázích z důvodu [dlouhotrvající doby obnovení](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process). Proto se nedoporučuje škálovat výpočetní výkon nebo úložiště Azure SQL Database spravované instance nebo změnit úroveň služby současně s dlouhotrvajícími transakcemi (import dat, úlohy zpracování dat, opětovné sestavení indexu atd.). Převzetí služeb při selhání databáze, které bude provedeno na konci operace, zruší probíhající transakce a výsledkem bude prodloužený čas obnovení.

> [!TIP]
> Aktualizace vyhrazeného prostoru úložiště v Pro obecné účely úrovni služby nenese převzetí služeb při selhání ani neovlivní dostupnost instance.

[Urychlené obnovení databáze](sql-database-accelerated-database-recovery.md) není aktuálně k dispozici pro Azure SQL Database spravované instance. Po povolení bude tato funkce významně snižovat proměnlivost doby převzetí služeb při selhání, a to i v případě dlouhotrvajících transakcí.

### <a name="canceling-management-operations"></a>Rušení operací správy

Následující tabulka shrnuje možnosti zrušení konkrétních operací správy a typických celkových dob trvání:

Kategorie  |Operace  |Zrušitelný  |Odhadovaná doba trvání zrušení  |
|---------|---------|---------|---------|
|Nasazení |Vytvoření instance |Ne |  |
|Aktualizovat |Horizontální navýšení kapacity úložiště instance (Pro obecné účely) |Ne |  |
|Aktualizovat |Horizontální navýšení kapacity úložiště instance (Pro důležité obchodní informace) |Ano |90% dokončených operací za 5 minut |
|Aktualizovat |Instance COMPUTE (virtuální jádra) pro škálování směrem nahoru a dolů (Pro obecné účely) |Ano |90% dokončených operací za 5 minut |
|Aktualizovat |Instance COMPUTE (virtuální jádra) pro škálování směrem nahoru a dolů (Pro důležité obchodní informace) |Ano |90% dokončených operací za 5 minut |
|Aktualizovat |Instance instance služby instance (Pro obecné účely až Pro důležité obchodní informace a naopak) |Ano |90% dokončených operací za 5 minut |
|Odstranit |Odstranění instance |Ne |  |
|Odstranit |Odstranění virtuálního clusteru (jako operace iniciované uživatelem) |Ne |  |

Chcete-li zrušit operaci správy, přejděte do okna Přehled a klikněte na oznamovací políčko probíhající operace. Na pravé straně se zobrazí obrazovka s probíhající operací a zobrazí se tlačítko pro zrušení operace. Po prvním kliknutí se zobrazí výzva, abyste znovu klikněte na tlačítko a potvrďte, že chcete operaci zrušit.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

Po odeslání a zpracování žádosti o zrušení se zobrazí oznámení, pokud bylo zrušení odeslání úspěšné. 

V případě úspěchu se operace správy zruší během několika minut, což vede k selhání.

![výsledek operace zrušení](./media/sql-database-managed-instance/canceling-operation-result.png)

Pokud není požadavek zrušit nebo pokud není aktivní tlačítko Storno, znamená to, že operace správy nepřešla do stavu bez možnosti zrušení a že se dokončí během několika minut. Operace správy bude pokračovat v provádění, dokud nebude dokončena.

> [!IMPORTANT]
> Operace zrušení je v tuto chvíli podporovaná jenom na portálu.

## <a name="advanced-security-and-compliance"></a>Pokročilé zabezpečení a dodržování předpisů

Možnost nasazení Managed instance kombinuje pokročilé funkce zabezpečení, které poskytuje Azure Cloud a SQL Server databázový stroj.

### <a name="managed-instance-security-isolation"></a>Izolace zabezpečení spravované instance

Spravovaná instance poskytuje další izolaci zabezpečení od ostatních tenantů v cloudu Azure. Izolace zabezpečení zahrnuje:

- [Implementace nativní virtuální sítě](sql-database-managed-instance-connectivity-architecture.md) a připojení k místnímu prostředí pomocí Azure Express Route nebo VPN Gateway.
- Ve výchozím nasazení se koncový bod SQL zveřejňuje jenom přes soukromou IP adresu, což umožňuje bezpečné připojení z privátních a hybridních sítí Azure.
- Jeden tenant s vyhrazenou základní infrastrukturou (COMPUTE, úložiště).

Následující diagram popisuje různé možnosti připojení pro vaše aplikace:

![vysoká dostupnost](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Další informace o integraci virtuální sítě a vynucování zásad sítě na úrovni podsítě najdete v tématu [Architektura virtuální sítě pro spravované instance](sql-database-managed-instance-connectivity-architecture.md) a [připojení aplikace ke spravované instanci](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Nasadíte více spravovaných instancí ve stejné podsíti, ať už je to povoleno v rámci požadavků na zabezpečení, jako by vám to přineslo další výhody. Instance Collocating ve stejné podsíti významně zjednodušují údržbu síťové infrastruktury a sníží dobu jejího zřizování. vzhledem k tomu, že je dlouhodobá doba zřízení spojená s náklady na nasazení první spravované instance v podsíti.

### <a name="azure-sql-database-security-features"></a>Funkce zabezpečení Azure SQL Database

Azure SQL Database poskytuje sadu pokročilých funkcí zabezpečení, které se dají použít k ochraně vašich dat.

- [Auditování spravované instance](sql-database-managed-instance-auditing.md) sleduje události databáze a zapisuje je do souboru protokolu auditu umístěného v účtu úložiště Azure. Auditování může pomoci zajistit dodržování legislativních předpisů, pochopit databázovou činnost a získat přehled o nesrovnalostech a anomáliích, které by mohly poukazovat na obavy z podnikání nebo na podezřelé porušení zabezpečení.
- Šifrování dat v pohybu – spravovaná instance zabezpečuje vaše data tím, že zajišťuje šifrování dat při pohybu pomocí Transport Layer Security. Kromě Transport Layer Security možnost nasazení spravované instance nabízí ochranu citlivých dat v letu, v klidovém prostředí a při zpracování dotazů pomocí [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Funkce Always Encrypted je špičkou v odvětví, která nabízí bezkonkurenční zabezpečení dat před porušením zabezpečení, včetně krádeže důležitých dat. Například u Always Encrypted se čísla kreditních karet ukládají v databázi vždycky, i když se zpracovávají dotazy a umožňují dešifrování v bodě použití autorizovanými pracovníky nebo aplikacemi, které potřebují zpracovávat tato data.
- [Rozšířená ochrana před internetovými útoky](sql-database-managed-instance-threat-detection.md) doplňuje [auditování](sql-database-managed-instance-auditing.md) tím, že poskytuje další vrstvu zabezpečení, která je součástí služby, která detekuje neobvyklé a potenciálně nebezpečné pokusy o přístup k databázím nebo jejich zneužití. Jste upozorňováni na podezřelé aktivity, potenciální ohrožení zabezpečení a útoky prostřednictvím injektáže SQL a také na neobvyklé vzory přístupu k databázi. Výstrahy rozšířené ochrany před internetovými útoky si můžete prohlédnout z [Azure Security Center](https://azure.microsoft.com/services/security-center/) a poskytnout podrobnosti o podezřelé aktivitě a doporučit, jak tuto hrozbu prozkoumat a zmírnit.  
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking) omezuje vystavení citlivých dat jejich maskováním na uživatele bez oprávnění. Dynamické maskování dat pomáhá zabránit neoprávněnému přístupu k citlivým datům tím, že umožňuje určit, kolik citlivých dat se má zobrazit s minimálním dopadem na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.
- [Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) vám umožňuje řídit přístup k řádkům v databázové tabulce na základě charakteristik uživatele, který spouští dotaz (například členství ve skupině nebo kontext spuštění). Zabezpečení na úrovni řádku (RLS) zjednodušuje návrh a psaní kódu zabezpečení v aplikaci. RLS umožňuje implementovat omezení přístupu k datovým řádkům. Například zajistěte, aby měli zaměstnanci přístup pouze k datovým řádkům, které jsou relevantní pro jejich oddělení, nebo omezit přístup k datům pouze na relevantní data.
- [Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) šifruje datové soubory spravované instance, označované jako šifrování dat v klidovém formátu. TDE provádí šifrování v/v v reálném čase a dešifruje data a soubory protokolů. Šifrování používá šifrovací klíč databáze (klíč DEK), který je uložený ve spouštěcím záznamu databáze pro dostupnost během obnovování. Pomocí transparentního šifrování dat můžete chránit všechny své databáze ve spravované instanci. TDE SQL Server je vyzkoušená technologie šifrování v klidovém formátu, kterou vyžaduje mnoho standardů dodržování předpisů pro ochranu před krádeží úložného média.

Migrace šifrované databáze do spravované instance je podporovaná prostřednictvím Azure Database Migration Service (DMS) nebo nativního obnovení. Pokud plánujete migrovat šifrovanou databázi pomocí nativního obnovení, je nutný krok migrace stávajícího certifikátu TDE z SQL Server místně nebo SQL Server na virtuálním počítači do spravované instance. Další informace o možnostech migrace najdete v tématu [migrace instance SQL Server do spravované instance](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integrace Azure Active Directory

Možnost nasazení Managed instance podporuje tradiční přihlášení a přihlašovací údaje databázového stroje SQL serveru integrované s Azure Active Directory (AAD). Objekty zabezpečení serveru Azure AD (přihlášení) (**Public Preview**) jsou cloudová verze místních přihlášení Azure, která používáte v místním prostředí. Objekty zabezpečení serveru Azure AD (přihlášení) umožňují zadat uživatele a skupiny z vašeho tenanta Azure Active Directory jako pravdivé objekty v rozsahu instance, které mohou provádět operace na úrovni instance, včetně databázových dotazů v rámci stejné spravované služby. případě.

Zavádí se nová syntaxe pro vytváření objektů zabezpečení serveru Azure AD (přihlášení) **od externího poskytovatele**. Další informace o syntaxi najdete v tématech <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Vytvoření přihlášení</a>a přečtěte si článek [zřízení Azure Active Directory správce pro vaši spravovanou instanci](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance) .

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrace s Azure Active Directory a vícefaktorové ověřování

Možnost nasazení spravované instance umožňuje centrálně spravovat identity uživatele databáze a dalších služeb Microsoftu pomocí [Azure Active Directory Integration](sql-database-aad-authentication.md). Tato možnost zjednodušuje správu oprávnění a zvyšuje zabezpečení. Azure Active Directory podporuje [vícefaktorové ověřování (MFA)](sql-database-ssms-mfa-authentication-configure.md) pro zvýšení zabezpečení dat a aplikací při současné podpoře jednotného přihlašování.

### <a name="authentication"></a>Ověřování

Ověřování spravované instance odkazuje na to, jak uživatelé při připojování k databázi prokáže jejich identitu. SQL Database podporuje dva typy ověřování:  

- **Ověřování SQL**:

  Tato metoda ověřování používá uživatelské jméno a heslo.
- **Ověřování Azure Active Directory**:

  Tato metoda ověřování používá identity spravované pomocí Azure Active Directory a je podporovaná pro spravované a integrované domény. [Kdykoliv to půjde](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), použijte ověřování pomocí Active Directory (integrované zabezpečení).

### <a name="authorization"></a>Autorizace

Autorizace odkazuje na to, co může uživatel provádět v rámci Azure SQL Database a je řízen členstvím databázové role vašeho uživatelského účtu a oprávněními na úrovni objektu. Spravovaná instance má stejné možnosti autorizace jako SQL Server 2017.

## <a name="database-migration"></a>Migrace databáze

Možnost nasazení spravované instance cílí na scénáře uživatelů s migrací do databáze z místních nebo IaaSch databázových implementací. Spravovaná instance podporuje několik možností migrace databáze:

### <a name="back-up-and-restore"></a>Zálohování a obnovení  

Přístup k migraci využívá zálohy SQL do úložiště objektů BLOB v Azure. Zálohy uložené v objektu BLOB služby Azure Storage je možné přímo obnovit do spravované instance pomocí [příkazu T-SQL Restore](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Základní informace o tom, jak obnovit World World Importers – soubor zálohy Standard, najdete v tématu [obnovení záložního souboru do spravované instance](sql-database-managed-instance-get-started-restore.md). V tomto rychlém startu se dozvíte, že musíte nahrát záložní soubor do služby Azure Blob Storage a zabezpečit ho pomocí klíče sdíleného přístupového podpisu (SAS).
- Informace o obnovení z adresy URL najdete v tématu [NATIVNÍ obnovení z adresy URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Zálohy ze spravované instance lze obnovit pouze do jiné spravované instance. Nemohou být obnoveny do místní SQL Server nebo do samostatného fondu databáze či elastického fondu.

### <a name="data-migration-service"></a>Služba migrace dat

Azure Database Migration Service je plně spravovaná služba navržená tak, aby umožňovala bezproblémové migrace z více databázových zdrojů do datových platforem Azure s minimálními výpadky. Tato služba zjednodušuje úlohy potřebné k přesunu stávajících databází třetích stran a SQL Server do Azure SQL Database (v případě samostatných databází, fondů databází v elastických fondech a databází instancí ve spravované instanci) a SQL Server na virtuálním počítači Azure. Podívejte se, [jak migrovat místní databázi do spravované instance pomocí DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Podporované funkce SQL

Možnost nasazení spravované instance se zaměřuje na zajištění téměř 100% kompatibility Surface Surface v místních SQL Serverch přicházejících ve fázích až do všeobecné dostupnosti služby. Seznam funkcí a porovnání najdete v tématu [SQL Database porovnání funkcí](sql-database-features.md)a seznam rozdílů v jazyce t-SQL ve spravovaných instancích a v SQL Server naleznete v tématu [Managed instance t-sql rozdíly od SQL Server](sql-database-managed-instance-transact-sql-information.md).

Možnost nasazení Managed instance podporuje zpětnou kompatibilitu databází SQL 2008. Přímá migrace z databázových serverů SQL 2005 je podporovaná, úroveň kompatibility migrovaných databází SQL 2005 se aktualizuje na SQL 2008.
  
Následující diagram popisuje kompatibilitu oblasti Surface ve spravované instanci:  

![migrace](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Klíčové rozdíly mezi SQL Server místním prostředím a ve spravované instanci

Možnost nasazení spravované instance je v cloudu vždycky v aktuálním stavu, což znamená, že některé funkce v místních SQL Server můžou být buď zastaralé, vyřazené nebo mají alternativy. Existují určité případy, kdy nástroje potřebují rozpoznat, že konkrétní funkce funguje trochu jiným způsobem nebo že služba běží v prostředí, které neovládáte úplně:

- Vysoká dostupnost je integrovaná a předem nakonfigurovaná s využitím technologie podobně jako [skupiny dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Automatické zálohování a obnovení bodu v čase. Zákazník může zahájit zálohování `copy-only`, které nekoliduje s automatickým řetězcem zálohování.
- Spravovaná instance nepovoluje zadání úplných fyzických cest, aby se všechny odpovídající scénáře musely podporovat jinak: příkaz Restore DB nepodporuje přesun, vytváření databáze neumožňuje použití fyzických cest, BULK INSERT pracuje pouze s objekty blob Azure atd.
- Spravovaná instance podporuje [ověřování Azure AD](sql-database-aad-authentication.md) jako alternativní cloudovou alternativu k ověřování systému Windows.
- Spravovaná instance automaticky spravuje soubor XTP a soubory pro databáze obsahující objekty OLTP v paměti.
- Spravovaná instance podporuje služba SSIS (SQL Server Integration Services) (SSIS) a může hostovat SSIS Catalog (SSISDB), který ukládá balíčky SSIS, ale spouští se na spravovaném Azure-SSIS Integration Runtime (IR) v Azure Data Factory (ADF), viz téma [Create Azure-SSIS IR in ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Porovnání funkcí SSIS v SQL Database najdete v tématu [porovnání Azure SQL Database izolované databáze, elastického fondu a spravované instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).

### <a name="managed-instance-administration-features"></a>Funkce správy spravované instance

Možnost nasazení spravované instance umožňuje správcům systému strávit méně času na úlohy správy, protože služba SQL Database je buď vykonává za vás, nebo významně tyto úlohy zjednodušuje. Například [operační systém/RDBMS instalace a opravy](sql-database-high-availability.md), [Změna velikosti a konfigurace dynamické instance](sql-database-single-database-scale.md), [zálohování](sql-database-automated-backups.md), [replikace databáze](replication-with-sql-database-managed-instance.md) (včetně systémových databází), konfigurace s [vysokou dostupností](sql-database-high-availability.md)a konfigurace datových proudů monitorování stavu a [výkonu](../azure-monitor/insights/azure-sql.md) .

> [!IMPORTANT]
> Seznam podporovaných, částečně podporovaných a nepodporovaných funkcí najdete v tématu [SQL Database funkce](sql-database-features.md). Seznam rozdílů v jazyce T-SQL ve spravovaných instancích oproti SQL Server naleznete v tématu [Managed instance t-SQL rozdíly z SQL Server](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Jak programově identifikovat spravovanou instanci

V následující tabulce je uvedeno několik vlastností, které jsou přístupné prostřednictvím jazyka Transact SQL, které můžete použít k detekci, že aplikace pracuje se spravovanou instancí a načetla důležité vlastnosti.

|Vlastnost|Hodnota|Komentář|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) – 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Tato hodnota je stejná jako v SQL Database. To neznamená, **že** modul SQL verze 12 (SQL Server 2014). Spravovaná instance vždy spouští nejnovější stabilní verzi modulu SQL, která je stejná nebo vyšší než nejnovější dostupná verze RTM SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Tato hodnota je stejná jako v SQL Database.|
|`SERVERPROPERTY('EngineEdition')`|8|Tato hodnota jednoznačně identifikuje spravovanou instanci.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Úplný název DNS instance v následujícím formátu:`<instanceName>`.`<dnsPrefix>`. database.windows.net, kde `<instanceName>` je jméno poskytované zákazníkem, zatímco `<dnsPrefix>` automaticky vygenerovala část názvu, která zaručuje jedinečnost globálních názvů DNS ("wcus17662feb9ce98", například).|Příklad: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak vytvořit první spravovanou instanci, najdete v tématu [Průvodce rychlým startem](sql-database-managed-instance-get-started.md).
- Seznam funkcí a porovnání najdete v tématu věnovaném [běžným funkcím SQL](sql-database-features.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [Konfigurace virtuální sítě VNet spravované instance](sql-database-managed-instance-connectivity-architecture.md).
- Pro rychlý Start, který vytváří spravovanou instanci a obnovuje databázi ze záložního souboru, najdete v tématu [Vytvoření spravované instance](sql-database-managed-instance-get-started.md).
- Kurz pro migraci pomocí Azure Database Migration Service (DMS) najdete v tématu [migrace spravované instance pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Pro pokročilé monitorování výkonu databáze spravované instance s integrovanými funkcemi pro odstraňování potíží najdete informace v tématu [monitorování Azure SQL Database pomocí Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Informace o cenách najdete v tématu [SQL Database ceny za Managed instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
