---
title: Přehled spravovaných instancí SQL
description: Tento článek popisuje spravovanou instanci Azure SQL Database.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268779"
---
# <a name="what-is-azure-sql-database-managed-instance"></a>Co je spravovaná instance Azure SQL Database?

Spravovaná instance je nová možnost nasazení Azure SQL Database, která poskytuje téměř 100% kompatibilitu s nejnovějším místním databázovým strojem SQL Server (Enterprise Edition), poskytuje implementaci nativní [virtuální sítě (VNet),](../virtual-network/virtual-networks-overview.md) která řeší běžné problémy se zabezpečením, a [obchodní model](https://azure.microsoft.com/pricing/details/sql-database/) příznivý pro místní zákazníky SQL Serveru. Model nasazení spravované instance umožňuje stávajícím zákazníkům SERVERU SQL Server zvedat a přesouvat své místní aplikace do cloudu s minimálními změnami aplikací a databází. Možnost nasazení spravované instance současně zachová všechny funkce PaaS (automatické aktualizace oprav a verzí, [automatické zálohování](sql-database-automated-backups.md), [vysoká dostupnost),](sql-database-high-availability.md) což výrazně snižuje režii správy a celkové náklady na ochranu nákladů na vztahy s nimi.

> [!IMPORTANT]
> Seznam oblastí, ve kterých je aktuálně dostupná možnost nasazení spravované instance, naleznete v [tématu Podporované oblasti](sql-database-managed-instance-resource-limits.md#supported-regions).

Následující diagram popisuje klíčové funkce spravovaných instancí:

![klíčové funkce](./media/sql-database-managed-instance/key-features.png)

Model nasazení spravované instance je určen pro zákazníky, kteří chtějí migrovat velký počet aplikací z místního prostředí nebo iaaS, vlastním prostředím nebo isv poskytovaného do plně spravovaného cloudového prostředí PaaS s co nejmenším úsilím o migraci. Pomocí plně automatizované [služby migrace dat (DMS)](../dms/tutorial-sql-server-to-managed-instance.md#create-an-azure-database-migration-service-instance) v Azure můžou zákazníci zvedat a přesouvat svůj místní SQL Server na spravovanou instanci, která nabízí kompatibilitu s místním SQL Serverem a úplnou izolaci instancí zákazníků s nativní podporou virtuální sítě.  Pomocí programu Software Assurance můžete vyměnit stávající licence za slanou instanci za slanou instanci pomocí [hybridní výhody Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).  Spravovaná instance je nejlepším cílem migrace v cloudu pro instance serveru SQL Server, které vyžadují vysoké zabezpečení a bohatou programovatelnost povrchu.

Cílem možnosti nasazení spravované instance je téměř 100% kompatibilita plochy s nejnovější místní verzí serveru SQL Server prostřednictvím plánu fázovaných verzí.

Pokud se chcete rozhodnout mezi možnostmi nasazení Azure SQL Database: jedna databáze, sdružená databáze a spravovaná instance a SQL Server hostovaný ve virtuálním počítači, přečtěte si, [jak vybrat správnou verzi SQL Serveru v Azure](sql-database-paas-vs-sql-server-iaas.md).

## <a name="key-features-and-capabilities"></a>Klíčové funkce a možnosti

Spravovaná instance kombinuje nejlepší funkce, které jsou k dispozici v Azure SQL Database a SQL Server Database Engine.

> [!IMPORTANT]
> Spravovaná instance je spuštěna se všemi funkcemi nejnovější verze serveru SQL Server, včetně online operací, automatických oprav plánu a dalších vylepšení výkonu rozlehlé sítě. Porovnání dostupných funkcí je vysvětleno v [porovnání funkcí: Azure SQL Database versus SQL Server](sql-database-features.md).

| **PaaS výhody** | **Kontinuita podnikových procesů** |
| --- | --- |
|Bez nákupu a správy hardwaru <br>Žádné režijní náklady na správu pro správu základní infrastruktury <br>Rychlé zřizování a škálování služeb <br>Automatické opravy a upgrade verze <br>Integrace s dalšími datovými službami PaaS |99,99% doba sla  <br>Vytvořeno s [vysokou dostupností](sql-database-high-availability.md) <br>Data chráněná [automatickým zálohováním](sql-database-automated-backups.md) <br>Zákaznická konfigurovatelná doba uchování záloh <br>Zálohy iniciované [uživatelem](https://docs.microsoft.com/sql/t-sql/statements/backup-transact-sql?view=azuresqldb-mi-current) <br>[Možnost obnovení databáze bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore) |
|**Zabezpečení a dodržování předpisů** | **správy**|
|Izolované prostředí[(integrace virtuální sítě](sql-database-managed-instance-connectivity-architecture.md), služba jednoho klienta, vyhrazené výpočetní prostředky a úložiště) <br>[Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)<br>[Ověřování Azure AD](sql-database-aad-authentication.md), podpora jednotného přihlašování <br> <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">Objekty serveru Azure AD (přihlášení)</a>  <br>Dodržuje standardy dodržování předpisů stejné jako databáze Azure SQL <br>[Auditování SQL](sql-database-managed-instance-auditing.md) <br>[Advanced Threat Protection](sql-database-managed-instance-threat-detection.md) |Rozhraní API Azure Resource Manager pro automatizaci zřizování a škálování služeb <br>Funkce portálu Azure pro ruční zřizování a škálování služeb <br>Database Migration Service

> [!IMPORTANT]
> Azure SQL Database (všechny možnosti nasazení) byla certifikována podle řady standardů dodržování předpisů. Další informace najdete v [Centru zabezpečení Microsoft Azure,](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942) kde najdete nejnovější seznam certifikací dodržování předpisů databáze SQL Database.

Klíčové funkce spravovaných instancí jsou uvedeny v následující tabulce:

|Funkce | Popis|
|---|---|
| Verze / sestavení serveru SQL Server | SQL Server Database Engine (nejnovější stabilní) |
| Spravované automatické zálohování | Ano |
| Integrované monitorování instancí a databází a metriky | Ano |
| Automatické opravy softwaru | Ano |
| Nejnovější funkce databázového stroje | Ano |
| Počet datových souborů (ŘÁDKŮ) v databázi | Několik |
| Počet souborů protokolu (LOG) na databázi | 1 |
| Virtuální síť – nasazení Správce prostředků Azure | Ano |
| Virtuální síť – klasický model nasazení | Ne |
| Podpora portálu | Ano|
| Vestavěná integrační služba (SSIS) | Ne – SSIS je součástí [Azure Data Factory PaaS](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) |
| Integrovaná služba analýzy (SSAS) | Ne - SSAS je samostatný [PaaS](https://docs.microsoft.com/azure/analysis-services/analysis-services-overview) |
| Integrovaná služba zasílání zpráv (SSRS) | Ne – použijte Power BI nebo SSRS IaaS |
|||

## <a name="vcore-based-purchasing-model"></a>Nákupní model založený na virtuálních jádrech

[Nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md) pro spravované instance poskytuje flexibilitu, řízení, transparentnost a jednoduchý způsob, jak převést požadavky na místní úlohy do cloudu. Tento model umožňuje měnit výpočetní prostředky, paměť a úložiště na základě vašich potřeb úlohy. Model virtuálních jader je také způsobilý pro úspory až 55 procent s [hybridní výhodou Azure pro SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/).

V modelu virtuálních jader si můžete vybrat mezi generacemi hardwaru.

- **Gen4 (Gen4)** Logické procesory jsou založeny na procesorech Intel E5-2673 v3 (Haswell) 2,4 GHz, připojených SSD, fyzických jádrech, 7 GB paměti RAM na jádro a výpočetních velikostech mezi 8 a 24 virtuálními jádry.
- **Gen5 (Gen5)** Logické procesory jsou založeny na procesorech Intel E5-2673 v4 (Broadwell) 2,3 GHz a Intel SP-8160 (Skylake), rychlých NVMe SSD, hyper-threaded logické jádro a výpočetní velikosti mezi 4 a 80 jádry.

Další informace o rozdílu mezi generacemi hardwaru v [limitech prostředků spravované instance](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).

## <a name="managed-instance-service-tiers"></a>Úrovně služeb spravované instance

Spravovaná instance je dostupná ve dvou úrovních služeb:

- **Obecné použití**: Určeno pro aplikace s požadavky na typický výkon a latenci vi.
- **Důležité pro podnikání**: Navrženo pro aplikace s nízkými požadavky na latenci vi a minimálním dopadem základních operací údržby na úlohu.

Obě úrovně služeb zaručují 99,99% dostupnost a umožňují nezávisle vybrat velikost úložiště a výpočetní kapacitu. Další informace o architektuře vysoké dostupnosti azure sql database najdete v tématu [Vysoká dostupnost a Azure SQL Database](sql-database-high-availability.md).

### <a name="general-purpose-service-tier"></a>Úroveň služby pro obecné účely

Následující seznam popisuje klíčovou charakteristiku úrovně služby pro všeobecné účely:

- Návrh pro většinu podnikových aplikací s typickými požadavky na výkon
- Vysoce výkonné úložiště objektů blob Azure (8 TB)
- Integrovaná [vysoká dostupnost](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) založená na spolehlivém úložišti objektů blob Azure a [Azure Service Fabric](../service-fabric/service-fabric-overview.md)

Další informace naleznete [v tématu vrstva úložiště v obecné úrovni](https://medium.com/azure-sqldb-managed-instance/file-layout-in-general-purpose-azure-sql-managed-instance-cf21fff9c76c) a výkon úložiště doporučené postupy a důležité informace pro spravované instance [(obecný účel)](https://blogs.msdn.microsoft.com/sqlcat/2018/07/20/storage-performance-best-practices-and-considerations-for-azure-sql-db-managed-instance-general-purpose/).

Další informace o rozdílu mezi úrovněmi služeb v [limitech prostředků spravované instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

### <a name="business-critical-service-tier"></a>Úroveň služeb Kritický pro podnikání

Úroveň služeb Business Critical je vytvořena pro aplikace s vysokými požadavky na vstupně-věci. Nabízí nejvyšší odolnost proti selhání pomocí několika izolovaných replik.

Následující seznam popisuje klíčové charakteristiky úrovně služeb Business Critical:

- Navrženo pro podnikové aplikace s nejvyššími požadavky na výkon a HA
- Dodává se se super rychlým místním ssd úložištěm (až 1 TB na Gen4 a až 4 TB na Gen5)
- Integrovaná [vysoká dostupnost](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) založená na [skupinách dostupnosti vždy zapnuté](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) a [Azure Service Fabric](../service-fabric/service-fabric-overview.md).
- Integrovaná další [replika databáze jen pro čtení,](sql-database-read-scale-out.md) kterou lze použít pro vytváření sestav a další úlohy jen pro čtení
- [Oltp v paměti,](sql-database-in-memory.md) který lze použít pro úlohy s požadavky na vysoký výkon  

Další informace o rozdílu mezi úrovněmi služeb v [limitech prostředků spravované instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).


## <a name="managed-instance-management-operations"></a>Operace správy spravovaných instancí

Azure SQL Database nabízí operace správy, které můžete využít k automatickému nasazování nových spravovaných instancí, aktualizaci vlastností instancí a odstraňování instancí v případě, že už je nepotřebujete. Tato část obsahuje informace o operacích správy a jejich typické doby trvání.

Pro podporu [nasazení v rámci virtuálních sítí (Virtuální sítě)](../virtual-network/virtual-network-for-azure-services.md#deploy-azure-services-into-virtual-networks) a poskytování izolace a zabezpečení pro zákazníky, spravované instance spoléhá na [virtuální clustery](sql-database-managed-instance-connectivity-architecture.md#high-level-connectivity-architecture), které představují vyhrazenou sadu izolovaných virtuálních počítačů nasazených uvnitř podsítě virtuální sítě zákazníka. V podstatě každé nasazení spravované instance v prázdné podsíti má za následek nové sestavení virtuálního clusteru.

Následné operace na nasazených spravovaných instancích mohou mít také vliv na jeho základní virtuální cluster. To má vliv na dobu trvání operací správy, protože nasazení dalších virtuálních počítačů přichází s režií, kterou je třeba vzít v úvahu při plánování nových nasazení nebo aktualizací existujících spravovaných instancí.

Všechny operace správy je možné uspořádat do následujících kategorií:

- Nasazení instance (vytvoření nové instance). 
- Aktualizace instance (změna vlastností instance, jako jsou virtuální jádra nebo rezervované úložiště.
- Odstranění instance.

Operace ve virtuálních clusterech obvykle trvá nejdéle. Doba trvání operací ve virtuálních clusterech se liší – níže jsou hodnoty, které můžete obvykle očekávat, na základě existujících telemetrických dat služby:

- Vytvoření virtuálního clusteru. Jedná se o synchronní krok v operacích správy instancí. **90% operací končí za 4 hodiny**.
- Změna velikosti virtuálního clusteru (rozšíření nebo zmenšení). Rozšíření je synchronní krok, zatímco zmenšování se provádí asynchronně (bez dopadu na dobu trvání operací správy instancí). **90 % rozšíření clusteru skončí za méně než 2,5 hodiny**.
- Odstranění virtuálního clusteru. Odstranění je asynchronní krok, ale může být také [zahájeno ručně](sql-database-managed-instance-delete-virtual-cluster.md) ve prázdném virtuálním clusteru, v takovém případě se provádí synchronně. **90 % odstranění virtuálního clusteru skončí za 1,5 hodiny**.

Kromě toho správa instancí může také zahrnovat jednu z operací v hostovaných databázích, což má za následek delší dobu trvání:

- Připojení databázových souborů z Azure Storage. Jedná se o synchronní krok, jako je například výpočetní (vCore) nebo škálování úložiště nahoru nebo dolů ve vrstvě služby obecné účely. **90% těchto operací končí za 5 minut**.
- Vždy na dostupnost skupiny výsev. Jedná se o synchronní krok, jako je výpočetní (vCore) nebo škálování úložiště na úrovni služby Business Critical, stejně jako při změně úrovně služby z obecného účelu na důležité pro obchodní (nebo naopak). Doba trvání této operace je úměrná celkové velikosti databáze i aktuální databázové aktivitě (počet aktivních transakcí). Aktivita databáze při aktualizaci instance může způsobit významnou odchylku od celkové doby trvání. **90% těchto operací se provádí při 220 GB / hodinu nebo vyšší**.

Následující tabulka shrnuje operace a typické celkové doby trvání:

|Kategorie  |Operace  |Dlouhotrvající segment  |Odhadovaná doba trvání  |
|---------|---------|---------|---------|
|**Nasazení** |První instance v prázdné podsíti|Vytvoření virtuálního clusteru|90% operací končí za 4 hodiny|
|Nasazení |První instance jiné generace hardwaru v neprázdné podsíti (například první instance Gen 5 v podsíti s instancemi Gen 4)|Vytvoření virtuálního clusteru*|90% operací končí za 4 hodiny|
|Nasazení |Vytvoření 4 virtuálních jader v prázdné nebo neprázdné podsíti|Vytvoření virtuálního clusteru**|90% operací končí za 4 hodiny|
|Nasazení |Následné vytvoření instance v neprázdné podsíti (instance 2., 3., atd.)|Změna velikosti virtuálního clusteru|90% operací končí za 2,5 hodiny|
|**Aktualizace** |Změna vlastnosti instance (heslo správce, přihlášení AAD, příznak Hybridní výhoda Azure)|Není dostupné.|Až 1 minuta|
|Aktualizace |Škálování úložiště instancí nahoru/dolů (úroveň služby pro obecné účely)|Připojení databázových souborů|90% operací končí za 5 minut|
|Aktualizace |Škálování úložiště instancí nahoru/dolů (úroveň služeb Důležité pro podnikání)|- Změna velikosti virtuálního clusteru<br>- Vždy na dostupnost skupiny výsev|90% operací končí za 2,5 hodiny + čas na osivo všech databází (220 GB / hod)|
|Aktualizace |Výpočetní výpočetní kapacity instance (virtuální jádra) se zvětšují nahoru a dolů (obecné účely)|- Změna velikosti virtuálního clusteru<br>- Připojení databázových souborů|90% operací končí za 2,5 hodiny|
|Aktualizace |Výpočetní kapacity instancí (virtuální jádra) se zvětšují nahoru a dolů (Důležité pro podnikání)|- Změna velikosti virtuálního clusteru<br>- Vždy na dostupnost skupiny výsev|90% operací končí za 2,5 hodiny + čas na osivo všech databází (220 GB / hod)|
|Aktualizace |Instance se zmenšená na 4 virtuální jádra (pro obecné účely)|- Změna velikosti virtuálního clusteru (pokud se provádí poprvé, může vyžadovat vytvoření virtuálního clusteru**)<br>- Připojení databázových souborů|90% operací končí za 4 h 5 min**|
|Aktualizace |Instance se zmenše na 4 virtuální jádra (důležité pro podnikání)|- Změna velikosti virtuálního clusteru (pokud se provádí poprvé, může vyžadovat vytvoření virtuálního clusteru**)<br>- Vždy na dostupnost skupiny výsev|90% operací končí za 4 hodiny + čas na osivo všech databází (220 GB / hod)|
|Aktualizace |Změna úrovně služby instance (obecné účely pro kritické pro obchodní a naopak)|- Změna velikosti virtuálního clusteru<br>- Vždy na dostupnost skupiny výsev|90% operací končí za 2,5 hodiny + čas na osivo všech databází (220 GB / hod)|
|**Odstranění**|Odstranění instance|Protokolovat zálohování ocasu pro všechny databáze|90% provoz končí až za 1 minutu.<br>Poznámka: Pokud je odstraněna poslední instance v podsíti, tato operace naplánuje odstranění virtuálního clusteru po 12 hodinách***|
|Odstranění|Odstranění virtuálního clusteru (jako operace iniciovaná uživatelem)|Odstranění virtuálního clusteru|90 % operací končí do 1,5 hodiny|

\*Virtuální cluster je sestaven pro generování hardwaru.

\*\*Možnost nasazení 4 virtuálních jader byla vydána v červnu 2019 a vyžaduje novou verzi virtuálního clusteru. Pokud jste měli instance v cílové podsíti, které byly všechny vytvořeny před 12.

\*\*\*12 hodin je aktuální konfigurace, ale to by se mohlo v budoucnu změnit, takže na ní nebuďte pevně závislý. Pokud potřebujete odstranit virtuální cluster dříve (například pro uvolnění podsítě), přečtěte si další informace [o odstranění podsítě po odstranění instance spravované službou Azure SQL Database](sql-database-managed-instance-delete-virtual-cluster.md).

### <a name="instance-availability-during-management"></a>Dostupnost instance během správy

Spravované instance nejsou k dispozici pro klientské aplikace během operací nasazení a odstranění.

Spravované instance jsou k dispozici během operací aktualizace, ale je krátká prostoje způsobená převzetím služeb při selhání, ke kterému dochází na konci aktualizací, které obvykle trvá až 10 sekund. Výjimkou je aktualizace vyhrazeného úložného prostoru ve vrstvě služby pro obecné účely, která nevzniká převzetí služeb při selhání ani nemá vliv na dostupnost instance.

> [!IMPORTANT]
> Doba trvání převzetí služeb při selhání se může výrazně lišit v případě dlouhotrvajících transakcí, ke kterým dochází v databázích z důvodu [prodloužené doby obnovení](sql-database-accelerated-database-recovery.md#the-current-database-recovery-process). Proto se nedoporučuje škálovat výpočetní prostředky nebo úložiště spravované instance Azure SQL Database nebo měnit úroveň služeb současně s dlouhotrvajícími transakcemi (import dat, úlohy zpracování dat, opětovné sestavení indexu atd.). Převzetí služeb při selhání databáze, které bude provedeno na konci operace zruší probíhající transakce a za následek prodlouženou dobu obnovení.

> [!TIP]
> Aktualizace vyhrazeného úložného prostoru ve vrstvě služby pro obecné účely nevzniká převzetí služeb při selhání ani nemá vliv na dostupnost instance.

[Zrychlené obnovení databáze](sql-database-accelerated-database-recovery.md) není aktuálně k dispozici pro instance spravované službou Azure SQL Database. Jakmile je tato funkce povolena, výrazně sníží variabilitu času převzetí služeb při selhání, a to i v případě dlouhotrvajících transakcí.

### <a name="canceling-management-operations"></a>Zrušení operací správy

Následující tabulka shrnuje schopnost zrušit konkrétní operace správy a typické celkové doby trvání:

Kategorie  |Operace  |Cancelable  |Předpokládaná doba trvání zrušení  |
|---------|---------|---------|---------|
|Nasazení |Vytvoření instance |Ne |  |
|Aktualizace |Škálování úložiště instance nahoru/dolů (obecné účely) |Ne |  |
|Aktualizace |Škálování úložiště instancí nahoru/dolů (důležité pro podnikání) |Ano |90% operací končí za 5 minut |
|Aktualizace |Výpočetní výpočetní kapacity instance (virtuální jádra) se zvětšují nahoru a dolů (obecné účely) |Ano |90% operací končí za 5 minut |
|Aktualizace |Výpočetní kapacity instancí (virtuální jádra) se zvětšují nahoru a dolů (Důležité pro podnikání) |Ano |90% operací končí za 5 minut |
|Aktualizace |Změna úrovně služby instance (obecné účely pro kritické pro obchodní a naopak) |Ano |90% operací končí za 5 minut |
|Odstranění |Odstranění instance |Ne |  |
|Odstranění |Odstranění virtuálního clusteru (jako operace iniciovaná uživatelem) |Ne |  |

Chcete-li operaci správy zrušit, přejděte do okna přehledu a klikněte na oznamovací schránku probíhajícího provozu. Z pravé strany se zobrazí obrazovka s probíhajícím provozem a bude k dispozici tlačítko pro zrušení operace. Po prvním kliknutí budete vyzváni k dalšímu klepnutí a potvrzení, že chcete operaci zrušit.

[![](./media/sql-database-managed-instance/canceling-operation.png)](./media/sql-database-managed-instance/canceling-operation.png#lightbox)

Po odeslání a zpracování žádosti o zrušení obdržíte oznámení, pokud bylo odeslání zrušení úspěšné nebo ne. 

V případě zrušení úspěchu bude operace správy zrušena během několika minut, což bude výsledkem selhání.

![zrušení výsledku operace](./media/sql-database-managed-instance/canceling-operation-result.png)

Pokud zrušit požadavek selže nebo zrušit tlačítko není aktivní, to znamená, že operace správy vstoupila nezrušitelný stav a že bude dokončena za pár minut. Operace správy bude pokračovat v provádění, dokud nebude dokončena.

> [!IMPORTANT]
> Zrušení operace je aktuálně podporováno pouze na portálu.

## <a name="advanced-security-and-compliance"></a>Pokročilé zabezpečení a dodržování předpisů

Možnost nasazení spravované instance kombinuje pokročilé funkce zabezpečení poskytované cloudem Azure a databázovým strojem SQL Server.

### <a name="managed-instance-security-isolation"></a>Izolace zabezpečení spravované instance

Spravovaná instance poskytuje další izolaci zabezpečení od ostatních klientů v cloudu Azure. Izolace zabezpečení zahrnuje:

- [Nativní implementace virtuální sítě](sql-database-managed-instance-connectivity-architecture.md) a připojení k místnímu prostředí pomocí azure express route nebo brány VPN Gateway.
- Ve výchozím nasazení je koncový bod SQL vystaven pouze prostřednictvím privátní IP adresy, což umožňuje bezpečné připojení z privátních sítí Azure nebo hybridních sítí.
- Jeden tenant s vyhrazenou základní infrastrukturou (výpočetní, úložiště).

Následující diagram popisuje různé možnosti připojení pro vaše aplikace:

![vysoká dostupnost](./media/sql-database-managed-instance/application-deployment-topologies.png)  

Další podrobnosti o integraci virtuální sítě a vynucení zásad v síti na úrovni podsítě najdete v tématu [architektura virtuální sítě pro spravované instance](sql-database-managed-instance-connectivity-architecture.md) a připojení aplikace ke spravované [instanci](sql-database-managed-instance-connect-app.md).

> [!IMPORTANT]
> Umístěte více spravovaných instancí do stejné podsítě, kamkoli to povolují vaše požadavky na zabezpečení, protože vám to přinese další výhody. Střídání instancí ve stejné podsíti výrazně zjednoduší údržbu síťové infrastruktury a zkrátí dobu zřizování instancí, protože dlouhá doba zřizování je spojena s náklady na nasazení první spravované instance v podsíti.

### <a name="azure-sql-database-security-features"></a>Funkce zabezpečení databáze Azure SQL

Azure SQL Database poskytuje sadu pokročilých funkcí zabezpečení, které lze použít k ochraně vašich dat.

- [Auditování spravovaných instancí](sql-database-managed-instance-auditing.md) sleduje události databáze a zapisuje je do souboru protokolu auditu umístěného ve vašem účtu úložiště Azure. Auditování může pomoci udržovat dodržování předpisů, pochopit databázovou aktivitu a získat přehled o nesrovnalostech a anomáliích, které by mohly naznačovat obchodní problémy nebo podezření na porušení zabezpečení.
- Šifrování dat v pohybu – spravovaná instance zabezpečuje vaše data tím, že poskytuje šifrování dat v pohybu pomocí zabezpečení transportní vrstvy. Kromě zabezpečení transportní vrstvy nabízí možnost nasazení spravované instance ochranu citlivých dat v letu, v klidovém stavu a během zpracování dotazů pomocí [vždy šifrované](/sql/relational-databases/security/encryption/always-encrypted-database-engine). Funkce Always Encrypted je špičkou v odvětví, která nabízí bezkonkurenční zabezpečení dat před porušením zabezpečení, včetně krádeže důležitých dat. Například s vždy šifrované, čísla platebních karet jsou uloženy šifrované v databázi vždy, a to i během zpracování dotazů, což umožňuje dešifrování v místě použití autorizovaným personálem nebo aplikace, které potřebují ke zpracování těchto dat.
- [Pokročilá ochrana před hrozbami](sql-database-managed-instance-threat-detection.md) doplňuje [auditování](sql-database-managed-instance-auditing.md) tím, že poskytuje další vrstvu inteligentního zabezpečení integrovanou do služby, která detekuje neobvyklé a potenciálně škodlivé pokusy o přístup k databázím nebo jejich zneužití. Budete upozorněni na podezřelé aktivity, potenciální chyby zabezpečení a útoky injektáže SQL, stejně jako neobvyklé vzory přístupu k databázi. Pokročilé výstrahy ochrany před hrozbami si můžete prohlédnout z [Azure Security Center](https://azure.microsoft.com/services/security-center/) a poskytnout podrobnosti o podezřelé aktivity a doporučit akce, jak prozkoumat a zmírnit hrozbu.  
- [Dynamické maskování dat](/sql/relational-databases/security/dynamic-data-masking) omezuje vystavení citlivých dat maskováním neprivilegovaným uživatelům. Dynamické maskování dat pomáhá zabránit neoprávněnému přístupu k citlivým datům tím, že umožňuje určit, kolik citlivých dat se má odhalit s minimálním dopadem na aplikační vrstvu. Je to funkce zabezpečení založená na zásadách, která skrývá citlivá data v sadě výsledků dotazu nad určenými poli databáze, zatímco data v databázi se nemění.
- [Zabezpečení na úrovni řádků](/sql/relational-databases/security/row-level-security) umožňuje řídit přístup k řádkům v databázové tabulce na základě charakteristik uživatele provádějícího dotaz (například podle členství ve skupině nebo kontextu spuštění). Zabezpečení na úrovni řádku (RLS) zjednodušuje návrh a psaní kódu zabezpečení v aplikaci. RLS umožňuje implementovat omezení přístupu k datovým řádkům. Například zajištění, že pracovníci mají přístup pouze k datovým řádkům, které jsou relevantní pro jejich oddělení, nebo omezení přístupu k datům pouze na příslušná data.
- [Transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) šifruje datové soubory spravovaných instancí, známé jako šifrování dat v klidovém stavu. TDE provádí vstupně-nevstupně-to šifrování a dešifrování dat a souborů protokolu. Šifrování používá šifrovací klíč databáze (DEK), který je uložen v záznamu spuštění databáze pro dostupnost během obnovení. Všechny databáze můžete chránit ve spravované instanci pomocí transparentního šifrování dat. TDE je osvědčená technologie šifrování v klidovém stavu serveru SQL Server, která je vyžadována mnoha standardy dodržování předpisů k ochraně před krádeží paměťových médií.

Migrace šifrované databáze na spravovanou instanci je podporována prostřednictvím služby Azure Database Migration Service (DMS) nebo nativního obnovení. Pokud plánujete migrovat šifrovanou databázi pomocí nativního obnovení, je migrace existujícího certifikátu TDE z místního serveru SQL Server nebo serveru SQL Server ve virtuálním počítači na spravovanou instanci povinným krokem. Další informace o možnostech migrace naleznete v [tématu migrace instance serveru SQL Server do spravované instance](sql-database-managed-instance-migrate.md).

## <a name="azure-active-directory-integration"></a>Integrace služby Azure Active Directory

Možnost nasazení spravované instance podporuje tradiční přihlášení databázového modulu SQL serveru a přihlášení integrovaná s Azure Active Directory (AAD). Principy serveru Azure AD (přihlášení)**(public preview)** jsou cloudová verze azure místní chození databáze, které používáte v místním prostředí. Objekty serveru Azure AD (přihlášení) umožňují určit uživatele a skupiny z vašeho tenanta Služby Azure Active Directory jako skutečné objekty s rozsahem instance, které jsou schopné provádět jakoukoli operaci na úrovni instance, včetně dotazů mezi databázemi v rámci stejné spravované Instance.

Nová syntaxe se zavádí k vytvoření objektů zabezpečení serveru Azure AD (přihlášení), **z externího zprostředkovatele**. Další informace o syntaxi najdete v <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">tématu CREATE LOGIN</a>a projděte [si článek o spravované instanci v článku Zřízení služby Azure Active Directory.](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integrace s Azure Active Directory a vícefaktorové ověřování

Možnost nasazení spravované instance umožňuje centrálně spravovat identity uživatelů databáze a dalších služeb Microsoftu pomocí [integrace Azure Active Directory](sql-database-aad-authentication.md). Tato možnost zjednodušuje správu oprávnění a zvyšuje zabezpečení. Azure Active Directory podporuje [vícefaktorové ověřování (MFA)](sql-database-ssms-mfa-authentication-configure.md) pro zvýšení zabezpečení dat a aplikací při současné podpoře jednotného přihlašování.

### <a name="authentication"></a>Ověřování

Ověřování spravované instance odkazuje na způsob, jakým uživatelé prokazují svou identitu při připojování k databázi. SQL Database podporuje dva typy ověřování:  

- **Ověřování SQL**:

  Tato metoda ověřování používá uživatelské jméno a heslo.
- **Ověřování služby Azure Active Directory**:

  Tato metoda ověřování používá identity spravované službou Azure Active Directory a je podporovaná pro spravované a integrované domény. [Kdykoliv to půjde](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode), použijte ověřování pomocí Active Directory (integrované zabezpečení).

### <a name="authorization"></a>Autorizace

Autorizace odkazuje na to, co může uživatel dělat v rámci azure sql database a je řízen a členství v databázové roli vašeho uživatelského účtu a oprávnění na úrovni objektu. Spravovaná instance má stejné možnosti autorizace jako SQL Server 2017.

## <a name="database-migration"></a>Migrace databáze

Možnost nasazení spravované instance cílí na scénáře uživatelů s migrací hromadné databáze z místních implementací nebo implementace databáze IaaS. Spravovaná instance podporuje několik možností migrace databáze:

### <a name="back-up-and-restore"></a>Zálohování a obnovení  

Přístup migrace využívá zálohy SQL k úložišti objektů blob Azure. Zálohy uložené v objektu blob úložiště Azure lze přímo obnovit do spravované instance pomocí [příkazu T-SQL RESTORE](https://docs.microsoft.com/sql/t-sql/statements/restore-statements-transact-sql?view=azuresqldb-mi-current).

- Úvodní příručka o tom, jak obnovit záložní soubor databáze Wide World Importers – Standard, najdete v [tématu Obnovení záložního souboru do spravované instance](sql-database-managed-instance-get-started-restore.md). Tento rychlý start ukazuje, že musíte nahrát záložní soubor do úložiště objektů blob Azure a zabezpečit ho pomocí klíče podpisu sdíleného přístupu (SAS).
- Informace o obnovení z adresy URL naleznete v [tématu Native RESTORE from URL](sql-database-managed-instance-migrate.md#native-restore-from-url).

> [!IMPORTANT]
> Zálohy ze spravované instance lze obnovit pouze do jiné spravované instance. Nelze je obnovit na místní sql server nebo do jednoho databázového/elastického fondu.

### <a name="data-migration-service"></a>Database Migration Service

Služba Migrace databází Azure je plně spravovaná služba navržená tak, aby umožňovala bezproblémové migrace z více databázových zdrojů na datové platformy Azure s minimálními prostoji. Tato služba zjednodušuje úlohy potřebné k přesunutí existujících databází třetích stran a SQL Serveru do Azure SQL Database (jediné databáze, sdružené databáze v elastických fondech a databáze instancí ve spravované instanci) a SQL Server ve virtuálním počítači Azure. Podívejte se na informace o [migraci místní databáze do spravované instance pomocí služby DMS](https://aka.ms/migratetoMIusingDMS).

## <a name="sql-features-supported"></a>Podporované funkce SQL

Možnost nasazení spravované instance má za cíl zajistit téměř 100% kompatibilitu plochy s místním SQL Serverem přicházejícím postupně, dokud služba obecná dostupnost. Funkce a seznam porovnání naleznete v [tématu porovnání funkcí databáze SQL](sql-database-features.md)a seznam rozdílů T-SQL ve spravovaných instancích versus SQL Server, viz spravované instance [T-SQL rozdíly od SQL Server](sql-database-managed-instance-transact-sql-information.md).

Možnost nasazení spravované instance podporuje zpětnou kompatibilitu s databázemi SQL 2008. Je podporována přímá migrace z databázových serverů SQL 2005, úroveň kompatibility pro migrované databáze SQL 2005 je aktualizována na SQL 2008.
  
Následující diagram popisuje kompatibilitu plochy ve spravované instanci:  

![Migrace](./media/sql-database-managed-instance/migration.png)

### <a name="key-differences-between-sql-server-on-premises-and-in-a-managed-instance"></a>Klíčové rozdíly mezi sql serverem místně a ve spravované instanci

Možnost nasazení spravované instance těží z toho, že je vždy aktuální v cloudu, což znamená, že některé funkce v místním SQL Serveru mohou být zastaralé, vyřazené nebo mají alternativy. Existují specifické případy, kdy nástroje potřebují rozpoznat, že určitá funkce funguje mírně jiným způsobem nebo že služba je spuštěna v prostředí, které plně neřídíte:

- Vysoká dostupnost je zabudována a předkonfigurována pomocí technologie podobné [skupinám dostupnosti always on](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server).
- Automatické zálohování a obnovení bodu v čase. Zákazník může `copy-only` iniciovat zálohy, které nenarušují řetěz automatického zálohování.
- Spravovaná instance neumožňuje určení úplné fyzické cesty, takže všechny odpovídající scénáře musí být podporovány odlišně: OBNOVENÍ DB nepodporuje s MOVE, VYTVOŘIT DB neumožňuje fyzické cesty, BULK INSERT funguje pouze s objekty BLOB Azure atd.
- Spravovaná instance podporuje [ověřování Azure AD](sql-database-aad-authentication.md) jako cloudovou alternativu k ověřování windows.
- Spravovaná instance automaticky spravuje skupinu souborů XTP a soubory pro databáze obsahující objekty OLTP v paměti
- Spravovaná instance podporuje sql server integration services (SSIS) a může být hostitelem katalogu SSIS (SSISDB), který ukládá balíčky SSIS, ale spouštějí se na spravovaném runtime integrace Azure-SSIS (IR) v Azure Data Factory (ADF), viz [Vytvoření Azure-SSIS IR v ADF](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime). Chcete-li porovnat funkce SSIS v databázi SQL, [přečtěte si témat u článku Porovnání jedné databáze Azure SQL Database, elastického fondu a spravované instance](../data-factory/create-azure-ssis-integration-runtime.md#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance).

### <a name="managed-instance-administration-features"></a>Funkce správy spravované instance

Možnost nasazení spravované instance umožňuje správci systému trávit méně času na úkolech správy, protože služba SQL Database je buď provádí za vás, nebo tyto úkoly výrazně zjednodušuje. Například [instalace a oprava operačního systému / RDBMS](sql-database-high-availability.md), [dynamická změna velikosti a konfigurace instance](sql-database-single-database-scale.md), [zálohování](sql-database-automated-backups.md), [replikace databáze](replication-with-sql-database-managed-instance.md) (včetně systémových databází), konfigurace [vysoké dostupnosti](sql-database-high-availability.md)a konfigurace datových proudů sledování stavu a [výkonu.](../azure-monitor/insights/azure-sql.md)

> [!IMPORTANT]
> Seznam podporovaných, částečně podporovaných a nepodporovaných funkcí naleznete v [tématu funkce databáze SQL](sql-database-features.md). Seznam rozdílů T-SQL ve spravovaných instancích versus SQL Server naleznete v tématu [spravované instance T-SQL rozdíly od SQL Serveru](sql-database-managed-instance-transact-sql-information.md)

### <a name="how-to-programmatically-identify-a-managed-instance"></a>Jak programově identifikovat spravovanou instanci

V následující tabulce je uvedeno několik vlastností, které jsou přístupné prostřednictvím aplikace Transact SQL a které můžete použít ke zjištění, že vaše aplikace pracuje se spravovanou instancí a načíst důležité vlastnosti.

|Vlastnost|Hodnota|Poznámka|
|---|---|---|
|`@@VERSION`|Microsoft SQL Azure (RTM) – 12.0.2000.8 2018-03-07 Copyright (C) 2018 Microsoft Corporation.|Tato hodnota je stejná jako v databázi SQL. To **neznamená,** že sql engine verze 12 (SQL Server 2014). Spravovaná instance vždy spouští nejnovější stabilní verzi modulu SQL, která je stejná nebo vyšší než nejnovější dostupná verze RTM serveru SQL Server.  |
|`SERVERPROPERTY ('Edition')`|SQL Azure|Tato hodnota je stejná jako v databázi SQL.|
|`SERVERPROPERTY('EngineEdition')`|8|Tato hodnota jednoznačně identifikuje spravovanou instanci.|
|`@@SERVERNAME`, `SERVERPROPERTY ('ServerName')`|Název DNS celé instance v`<instanceName>`následujícím formátu: . `<dnsPrefix>`.database.windows.net, `<instanceName>` kde je název poskytnutý zákazníkem, zatímco `<dnsPrefix>` je automaticky generována část názvu zaručující globální jedinečnost názvu DNS ("wcus17662feb9ce98", například)|Příklad: my-managed-instance.wcus17662feb9ce98.database.windows.net|

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak vytvořit první spravovanou instanci, najdete [v tématu Úvodní příručka](sql-database-managed-instance-get-started.md).
- Funkce a seznam porovnání naleznete v tématu [běžné funkce SQL](sql-database-features.md).
- Další informace o konfiguraci virtuální sítě najdete v tématu [konfigurace virtuální sítě spravované instance](sql-database-managed-instance-connectivity-architecture.md).
- Rychlý start, který vytvoří spravovanou instanci a obnoví databázi ze záložního souboru, najdete [v tématu vytvoření spravované instance](sql-database-managed-instance-get-started.md).
- Kurz využívající službu Migrace databáze Azure (DMS) pro migraci najdete v tématu [migrace spravovaných instancí pomocí DMS](../dms/tutorial-sql-server-to-managed-instance.md).
- Pokročilé monitorování výkonu databáze spravovaných instancí s integrovanou inteligencí řešení potíží najdete v tématu [Monitorování Azure SQL Database pomocí Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).
- Informace o cenách naleznete v [tématu SQL Database managed instance pricing](https://azure.microsoft.com/pricing/details/sql-database/managed/).
