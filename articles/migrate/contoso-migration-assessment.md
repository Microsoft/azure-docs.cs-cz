---
title: Posouzení vhodnosti místních úloh pro Contoso migraci do Azure | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso vyhodnocuje jejich vhodnost místních počítačů pro migraci do Azure pomocí Azure migrace a migrace databáze
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: fa6fb4ffe1eea98392b2199f379431b0dffc6774
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39006562"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Migrace Contoso: posouzení vhodnosti místních úloh pro migraci do Azure

Tento článek popisuje, jak společnosti Contoso vyhodnocuje SmartHotel jeho místní aplikaci při přípravě na jeho migraci do Azure.

Tento dokument je třetí webinář z řady článků, které dokumentují, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a řadu scénářů nasazení, které ukazují, jak nastavit infrastrukturu migrace vyhodnotit vhodnost migrace místních prostředků a spouštět různé druhy migrace. Scénáře jejich složitost v a budeme přidávat další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, článek řady a ukázkové aplikace, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak společnosti Contoso připraví jeho místní a infrastrukturu Azure na migraci. Stejnou infrastrukturu se používá pro všechny články týkající se migrace. | K dispozici.
Článek 3: Posouzení místních prostředků pro migraci do Azure  | Ukazuje, jak společnosti Contoso spuštění posouzení místních dvouvrstvé SmartHotel aplikaci spuštěnou v prostředí VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí [Azure Migrate](migrate-overview.md) služby a databáze aplikace SQL serveru s [Pomocníka s migrací databáze](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Tento článek.
[Článek 4: Změna hostitele aplikací na virtuálních počítačích Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak společnosti Contoso běží lift and shift migrace do Azure pro místní SmartHotel aplikaci. Contoso migruje virtuální počítač front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databáze aplikace do spravované Instance SQL, pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Ukazuje, jak společnosti Contoso aplikaci SmartHotel virtuální počítače migrovat do virtuálních počítačů Azure pomocí služby Site Recovery. | K dispozici.
[Článek 6: Změna hostitele aplikací na virtuálních počítačích Azure a skupina dostupnosti AlwaysOn SQL serveru](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace. Contoso využívá Site Recovery k migraci aplikace, virtuální počítače a Database Migration service, jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn. | K dispozici.
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak Contoso funguje lift and shift migrace aplikace osTicket Linux na virtuální počítače Azure pomocí služby Site Recovery | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak společnosti Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Site Recovery a migraci databáze aplikace na instanci serveru Azure MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorujte aplikace na Azure Web Apps a Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure | K dispozici.
[Článek 10: Refaktorujte Linuxovou aplikaci na Azure Web Apps a Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Ukazuje, jak společnosti Contoso migruje aplikace osTicket Linux do Azure Web Apps ve více lokalitách, integrovaná se službou GitHub pro průběžné doručování. Jejich migrovat databázi aplikace na instanci Azure MySQL. | K dispozici.
[Článek 11: Refaktorovat TFS na VSTS](contoso-migration-tfs-vsts.md) | Ukazuje, jak společnosti Contoso migruje svoje místní nasazení Team Foundation Server (TFS) a migrujte jej na Visual Studio Team Services (VSTS) v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Ukazuje, jak společnosti Contoso migruje a rearchitects jeho SmartHotel aplikace do Azure. Jejich úprava architektury webové vrstvy aplikace jako kontejner Windows a databáze aplikace ve službě Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Ukazuje, jak společnosti Contoso znovu sestavte své aplikace SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služeb App Services, Kubernetes v Azure, Azure Functions, Cognitive services a Cosmos DB. | K dispozici.


## <a name="overview"></a>Přehled

V rámci zvažování migrace do Azure, chce se společnosti Contoso provést interní technické a finanční hodnocení, zjistěte, jestli jsou jejich místní úlohy vhodné k migraci do cloudu. Konkrétně se tým Contoso chcete posoudit kompatibilitu počítačů a databází, migrace a odhadnout kapacitu a náklady na provoz jejich prostředků v Azure.

Chcete-li získat jejich posuzují a až lépe porozumíte technologií, že jsou teď k vyhodnocení dvě svých aplikací v místním souhrnu v následující tabulce. Všimněte si, že se už posouzení pro scénáře migrace této metody opětovného hostování a Refaktorujte aplikací pro migraci. Další informace o opětovném hostování a refaktoring v [Přehled migrace Contoso](contoso-migration-overview.md).

**Název aplikace** | **Platforma** | **Úrovně aplikace** | **Podrobnosti**
--- | --- | --- | ---
SmartHotel<br/><br/> Spravuje Contoso cestovní požadavky | S databází serveru SQL Server a systémem Windows | Dvouvrstvém aplikaci s front-endu ASP.NET web běžící na jeden virtuální počítač (WEBVM) a SQL Server běžící na jiný virtuální počítač (SQLVM) | Virtuální počítače VMware, které běží na hostiteli ESXi, který spravuje vCenter server.<br/><br/> Ukázková aplikace si můžete stáhnout z [Githubu](https://github.com/Microsoft/SmartHotel360).
OSTicket<br/><br/> Contoso služby app Service Desk | Spuštění na systému Linux/Apache, MySQL PHP (LAMP). | Dvouvrstvém aplikaci s front-endu webu v PHP na jeden virtuální počítač (OSTICKETWEB) a databáze MySQL, který je spuštěn na jiném virtuálním počítači (OSTICKETMYSQL) | Aplikace používá ke sledování problémů pro interní zaměstnance a externí zákazníky zákaznických služeb aplikací.<br/><br/> Ukázková aplikace si můžete stáhnout z [Githubu](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Aktuální architektura


Zde je diagram znázorňující aktuální místní infrastrukturou společnosti Contoso.

![Architektura contoso](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso má jeden hlavní datové centrum, které jsou umístěné v města New York východní USA.
- Mají tři další místní větve ve Spojených státech amerických.
- Hlavní datové centrum je připojený k Internetu pomocí fiber metro ethernetová připojení (500 MB/s).
- Každou větev je místně připojen k Internetu pomocí obchodní třídy připojení s IPSec VPN tunely zpět do hlavního datového centra. To umožňuje jejich celá síť trvale připojený k Internetu a optimalizuje připojení k Internetu.
- Hlavní datové centrum je plně virtualizovaný s VMware. Mají dva virtualizace hostitele ESXi 6.5 spravované přes vCenter Server verze 6.5.
- Contoso používá službu Active Directory pro správu identit a servery DNS v interní síti.
- Běží na virtuálních počítačích VMware řadiče domény v datovém centru. Řadiče domény v místní větve spustit na fyzických serverech.





## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co obchodní chce, aby k dosažení této migraci:

- **Adresa obchodní růst**: Contoso roste a kvůli tomu je tlak na svých místních systémů a infrastruktury.
- **Zvýšení efektivity**: Contoso je potřeba odebrat nepotřebné postupy a zjednodušte procesy pro jejich vývojáře a uživatele.  Obchodní potřeby IT bude rychlé a ne produkovaného odpadu čas nebo peníze, tím rychlejší doručování na požadavky zákazníků.
- **Zvýšení flexibility**: Contoso IT je potřeba se více přizpůsobovat podle potřeb firmy. Musí být schopné reagovat rychleji než změn na webu Marketplace, aby úspěch v globální ekonomiku.  Nesmí získat způsobem nebo stát obchodní blokování.
- **Škálování**: růstem podniku zvládla úspěšně, musíte zadat Contoso IT systémy, které mohou rozšířit stejným tempem.

## <a name="assessment-goals"></a>Posouzení cíle

Tým cloudových Contoso má připnutou dolů cíle pro jejich posouzení migrace:

- Po dokončení migrace aplikací v Azure by měl mít stejné možnosti výkonu stejně jako dnes v jejich místní prostředí VMWare.  Přesun do cloudu, neznamená, že výkon aplikace je méně kritické.
- Contoso potřebuje pochopit jejich aplikace a databáze kompatibilitu s požadavky služby Azure, stejně jako jejich možnosti hostování v Azure.
- Správa databází společnosti Contoso byste měli minimalizovat, po přesunutí aplikací do cloudu.  
- Contoso chce znát pouze možnosti migrace, ale také náklady spojené s vaší stávající infrastrukturou po přesunu do cloudu.

## <a name="assessment-tools"></a>Nástroje pro vyhodnocení
Contoso je pomocí nástrojů společnosti Microsoft pro posouzení. Tyto nástroje bylo v souladu s své cíle a by měla poskytnout všechny informace, které potřebují.

**Technologie** | **Popis** | **Náklady**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Přímý přístup do paměti, budete používat k vyhodnocení a zjištění problémů s kompatibilitou, které může mít vliv na jejich fungování databáze v Azure. DMA vyhodnocuje paritu funkcí mezi SQL zdroje a cíle a doporučuje vylepšení výkonu a spolehlivosti. | Tento nástroj je zdarma ke stažení.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso použije tuto službu k vyhodnocení jejich virtuálních počítačů VMware. Posuzuje vhodnost počítačů k migraci a poskytuje odhady velikostí a nákladů, které bude vyžadovat jejich provoz v Azure.  | Není aktuálně (může 2018) žádné poplatky za používání této služby.
[Mapa služeb](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate využívá mapu služeb k zobrazení závislostí mezi počítači, které chcete migrovat. |  Mapa služeb je součástí Azure Log Analytics. V současné době je možné ji používat po dobu 180 dnů bez poplatků.

V tomto scénáři Contoso stáhne a spustí DMA za účelem posouzení místní databáze SQL serveru pro jejich cestovní aplikaci. Použití Azure migrate pomocí mapování závislostí posoudíme virtuální počítače, aplikace před migrací do Azure.



## <a name="assessment-architecture"></a>Architektura posouzení


![Architektura posouzení migrace](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso je fiktivní název představující typické podnikové organizace.
- Contoso má místní datacentrum (**contoso-datacenter**), s řadiči domény v místním (CONTOSODC1, CONTOSODC2).
- Jsou umístěné virtuální počítače VMware na VMware ESXI hostitele se systémem verze 6.5. Hostitelé: **contosohost1**, **contosohost2**
- Správu prostředí VMware zajišťuje vCenter server verze 6.5 (**venter**běžící na virtuálním počítači.
- SmartHotel cestovní aplikaci:
    - Aplikace je rozvrstvená na dva virtuální počítače VMware, **WEBVM** a **SQLVM**.
    - Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com**.
    - Virtuální počítače se systémem Windows Server 2008 R2 Datacenter s aktualizací SP1.
- Správu prostředí VMware zajišťuje vCenter Server (**vcenter.contoso.com**) spuštěný na virtuálním počítači.
- OSTicket služby podpory aplikace:
    - Aplikace je rozvrstvená na dva virtuální počítače, **OSTICKETWEB** a **OSTICKETMYSQL**.
    - Virtuální počítače jsou spuštěné na serveru Linux Ubuntu 16.04-LTS.
    - Virtuální počítač OSTICKETWEB běží Apache 2 a PHP 7.0.
    - Virtuální počítač OSTICKETMYSQL běží MySQL 5.7.22.

![Architektura](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>Požadavky

Tady je Contoso (a) potřebuje pro posouzení:

- Vlastník nebo Přispěvatel přístup pro předplatné Azure, nebo pro skupinu prostředků v rámci předplatného Azure.
- Místní server vCenter verze 5.5, 6.0 nebo 6.5.
- Účet jen pro čtení na serveru vCenter nebo oprávnění k jeho vytvoření.
- Oprávnění k vytvoření virtuálního počítače na serveru vCenter pomocí šablony .OVA.
- Alespoň jednoho hostitele ESXi verze 5.0 nebo novější.
- Alespoň dva místní virtuální počítače VMware, na jednom z nichž běží databáze SQL Serveru.
- Oprávnění k instalaci agentů Azure Migrate na každém virtuálním počítači.
- Virtuální počítače by měly mít přímé připojení k internetu.
        – Internetový přístup můžete omezit na [požadované adresy URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        -Pokud počítače bez připojení k Internetu [bránu OMS](../log-analytics/log-analytics-oms-gateway.md) musí být nainstalovaný na ně.
- Plně kvalifikovaný název domény virtuálního počítače, na kterém je spuštěná instance SQL Serveru, pro účely posouzení databáze.
- Brána Windows Firewall na virtuálním počítači s SQL Serverem by měla umožňovat externí připojení na portu TCP 1433 (výchozí nastavení), aby se nástroj DMA mohl připojit.


## <a name="assessment-overview"></a>Přehled posouzení

Tady je způsob Contoso se to udělat posouzení:


> [!div class="checklist"]
> * **Krok 1: Stažení a instalace DMA**: Příprava DMA na posouzení místní databáze SQL serveru.
> * **Krok 2: Posouzení databáze pomocí DMA**: spuštění a analýza posouzení databáze.
> * **Krok 3: Příprava na posouzení virtuálních počítačů pomocí služby Azure Migrate**: nastavení nastavení místních účtů a úprava VMware.
> * **Krok 4: Vyhledání místních virtuálních počítačů pomocí služby Azure Migrate**: vytvoření Azure Migrate collector virtuálního počítače. Potom spusťte kolektoru pro vyhledání virtuálních počítačů pro posouzení.
> * **Krok 5: Příprava na analýzu závislostí pomocí služby Azure Migrate**: Instalace agentů Azure Migrate na virtuální počítače, tak, aby bylo patrné, mapování závislostí mezi virtuálními počítači.
> * **Krok 6: Posouzení virtuálních počítačů pomocí služby Azure Migrate**: Kontrola závislostí, seskupení virtuálních počítačů a spuštění posouzení. Po posouzení připravený, jejich analýzu během přípravy na migraci.


## <a name="step-1-download-and-install-the-dma"></a>Krok 1: Stažení a instalace DMA

1. Stáhne DMA ze společnosti Contoso [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Pomocník lze nainstalovat na jakýkoli počítač, který se může připojit k instanci serveru SQL. Není potřeba ho spouštět na počítači s SQL Serverem.
    - Nespouštějte na hostitelském počítači SQL serveru.
2. Spouštějí se na stažený instalační soubor (DownloadMigrationAssistant.msi) a spusťte instalaci.
3. Na **Dokončit** stránky, vyberou **spusťte Microsoft Data Migration Assistant** před dokončením průvodce.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>Krok 2: Spuštění a analýza posouzení databáze pro SmartHotel

Contoso nyní můžete spustit posouzení, které analyzuje jejich místní SQL Server pro aplikaci SmartHotel.

1. V databázi Pomocníka s migrací, kliknou **nový**vyberte **posouzení**a zadejte název projektu – posouzení **SmartHotel**.
2. Vyberou **serveru typ zdroje** jako **systému SQL Server na virtuálních počítačích Azure**.

    ![Výběr zdroje](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      V současné době DMA nepodporuje posouzení pro migraci do spravované instance SQL. Jako alternativní řešení Contoso používá SQL Server na virtuálním počítači Azure jako předpokládaný cíl pro posouzení.

3. V **vyberte cílovou verzi**, vyberou jako cílovou verzi SQL serveru 2017. Je třeba vybrat, protože je verze použitá ve spravované instanci SQL.
4. Vyberou se zjistit informace o kompatibilitě a nové funkce:
    - **Problémy s kompatibilitou** Všimněte si změny, které můžou narušit migraci nebo které vyžadují menší úpravu před migrací. Udržuje informace o všech funkcích aktuálně používán, které jsou zastaralé. Problémy jsou uspořádané podle úrovně kompatibility.
    - **Nová doporučení funkcí** poznámky nové funkce v cílové platformě SQL serveru, který lze použít pro databázi po migraci. Tyto funkce jsou uspořádané podle výkonu, zabezpečení a úložiště.

    ![Výběr cíle](./media/contoso-migration-assessment/dma-assessment-2.png)

2. V **připojit k serveru**, že zadejte název virtuálního počítače s databází a přihlašovacích údajů pro přístup k ní. Je nutné povolit **důvěřovat certifikátu serveru** abyste měli jistotu, můžete získat na SQL Server. Potom kliknou **připojit**.

    ![Výběr cíle](./media/contoso-migration-assessment/dma-assessment-3.png)

3. V **přidat zdroj**, přidávají databáze, které chtějí posoudit a klikněte na tlačítko **Další** spusťte posouzení.
4. Posouzení se vytvoří.

    ![Vytvoření posouzení](./media/contoso-migration-assessment/dma-assessment-4.png)

5. V **výsledků kontroly**, zobrazíte výsledky posouzení.


### <a name="analyze-the-database-assessment"></a>Analýza posouzení databáze

Výsledky se zobrazí, jakmile jsou k dispozici. Pokud jsou řešení problémů potřebují klikněte na tlačítko **restartovat posouzení** znovu spusťte posouzení.

1. V **problémy s kompatibilitou** sestavy, zkontrolujte všechny problémy na jednotlivých úrovních kompatibility. Mapování úrovní kompatibility na verze SQL Serveru je následující:

    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Problémy s kompatibilitou](./media/contoso-migration-assessment/dma-assessment-5.png)

2. V **doporučení funkcí** sestavy Contoso můžete zobrazit funkce výkonu, zabezpečení a úložiště, které posouzení doporučuje po migraci. Doporučují se širokou škálu funkcí, včetně OLTP v paměti a Columnstore, Stretch Database, Always Encrypted, dynamického maskování dat a transparentní šifrování dat (TDE).

    ![Doporučení funkcí](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Doporučujeme, aby Contoso [umožňuje transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) pro všechny servery SQL databáze a to je ještě důležitější při databáze jsou v cloudu. Transparentní šifrování dat byste měli povolit po migraci. Pokud už je povoleno TDE, musíte přesunout certifikátem nebo asymentrickým klíčem k hlavní databázi z cílového serveru. [Další informace](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. Můžete exportovat posouzení ve formátu JSON nebo CSV.

Všimněte si, že pokud spouštíte posouzení většího rozsahu můžete:

- Spustit několik posouzení současně a zobrazit jejich stav posouzení, která tak, že otevřete **všechna posouzení** stránky.
- [Konsolidovat posouzení do databáze SQL serveru](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- [Konsolidovat posouzení do sestavy Power BI](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>Krok 3: Příprava na posouzení virtuálních počítačů pomocí služby Azure Migrate

Contoso potřebuje vytvořte účet VMware, který Azure Migrate použije k automatické zjišťování virtuálních počítačů k posouzení, ověřte oprávnění k vytvoření virtuálního počítače, poznamenejte si porty, které je potřeba otevřít a nastavte úroveň nastavení statistiky.

### <a name="set-up-a-vmware-account"></a>Nastavení účtu VMware

 Zjišťování virtuálních počítačů vyžaduje účet jen pro čtení ve vCenter s následujícími vlastnostmi:

- Typ uživatele: Alespoň uživatel jen pro čtení.
- Oprávnění: Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only.
- Podrobnosti: Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v tomto datacentru.
- Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **Žádný přístup** s objektem **Rozšířit na podřízený objekt**.

### <a name="verify-permissions-to-create-a-vm"></a>Ověření oprávnění k vytvoření virtuálního počítače

Contoso ověřuje, že má oprávnění k vytvoření virtuálního počítače pomocí importu souboru v. Soubory OVA formátu. [Další informace](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Ověření portů

Posouzení Contoso využívá mapování závislostí. Tato funkce vyžaduje agent nainstalovaný na virtuálních počítačích, které chcete vyhodnotit. Agent musí být schopni připojit k Azure z portu TCP 443 na každém virtuálním počítači. [Další informace](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) o požadavcích na připojení.


### <a name="set-statistics-settings"></a>Nastavení statistiky

Než se pustíte do nasazování, nastavte Contoso nastavení statistiky systému vCenter Server na úroveň 3. Poznámky:

- Po nastavení úrovně, budete muset počkat aspoň jeden den před spuštěním posouzení. Jinak nemusí fungovat podle očekávání.
- Pokud je úroveň vyšší než 3, posouzení bude fungovat, ale:
    - Nebudou se shromažďovat data o výkonu disků a sítí.
    - V případě úložiště služba Azure Migrate doporučí v Azure standardní disk stejné velikosti jako místní disk.
    - V případě sítí se pro každý místní síťový adaptér doporučí síťový adaptér v Azure.
    - V případě výpočetních prostředků služba Azure Migrate zjistí počet jader a velikost paměti virtuálního počítače a doporučí virtuální počítač Azure se stejnou konfigurací. Pokud existuje více vhodných velikostí virtuálních počítačů Azure, doporučí se virtuální počítač s nejnižšími náklady.
- [Další informace](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) o určování velikosti u úrovně 3.

Nastavte úroveň následujícím způsobem:

1. Ve webovém klientovi vSphere otevřou instanci serveru vCenter.
2. V **spravovat** > **nastavení** > **Obecné**, kliknou **upravit**.
3. V **statistiky**, že nastavené Statistika nastavení úrovně **Level 3**.

    ![Úroveň statistiky vCenter](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>Krok 4: Vyhledání virtuálních počítačů

Ke zjištění virtuálních počítačů společnosti Contoso vytvoří projekt Azure Migrate. Jejich stažení a nastavení virtuálního počítače kolektoru a spuštění kolektoru pro vyhledání svých místních virtuálních počítačů.

### <a name="create-a-project"></a>Vytvoření projektu

1. V [webu Azure portal](https://portal.azure.com), vyhledávají **Azure Migrate**a vytvořte projekt (ContosoMigration).
2. Zadejte název projektu a předplatné Azure a vytvořte novou skupinu prostředků Azure, **ContosoFailoverRG**. Poznámky:

    - Projekt Azure Migrate můžete vytvořit pouze v oblasti Střed USA – západ nebo USA – východ.
    - Migraci můžete naplánovat pro jakékoli cílové umístění.
    - Umístění projektu slouží pouze k uložení metadat shromážděných z místních virtuálních počítačů.

    ![Azure Migrate](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>Stažení zařízení kolektoru

Azure Migrate vytvoří místní virtuální počítač, kterému se říká zařízení kolektoru. Tento virtuální počítač vyhledá místní virtuální počítače VMware a odešle jejich metadata do služby Azure Migrate. Nastavení zařízení kolektoru, soubory ke stažení Contoso. Šablony pro soubory OVA a naimportuje ho na místní vCenter server k vytvoření virtuálního počítače.

1. V projektu Azure Migrate > **Začínáme** > **zjistit a posoudit** > **zjistit počítače**, stáhnou. Soubor OVA šablony.
2. Jejich kopírování ID a klíč projektu. Tyto jsou potřebné ke konfiguraci kolektoru.

    ![Stažení souboru .OVA](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Ověření zařízení kolektoru

Před nasazením virtuálního počítače, Contoso kontroluje, zda. Soubor OVA je bezpečné.

1. Na počítači, na kterém se soubor stáhne otevřou jako správce příkazový řádek.
2. Spusťte následující příkaz, kterým vygenerujete hodnotu hash pro soubor OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Vygenerovaná hodnota hash by měla odpovídat následujícímu nastavení (verze 1.0.9.12)

**Algoritmus** | **Hodnota hash**
--- | ---
MD5 | d0363e5d1b377a8eb08843cf034ac28a
SHA1 | df4a0ada64bfa59c37acf521d15dcabe7f3f716b
SHA256 | f677b6c255e3d4d529315a31b5947edfe46f45e4eb4dbc8019d68d1d1b337c2e

### <a name="create-the-collector-appliance"></a>Vytvoření zařízení kolektoru

Nyní můžete importujte stažený soubor do vCenter serveru Contoso a proveďte zřízení serveru konfigurace virtuálního počítače.

1. V konzoli vSphere Client klikněte **souboru** > **Deploy OVF Template**.

    ![Nasazení šablony OVF](./media/contoso-migration-assessment/vcenter-wizard.png)

2. V Průvodci nasazením šablony OVF > **zdroj**, určí umístění. Soubor OVA.
3. V **název a umístění**, určí popisný název virtuálního počítače kolektoru a umístění inventáře, ve kterém bude virtuální počítač hostovaný. Specifikují také hostitele nebo cluster, na kterém bude zařízení kolektoru spouštět.
5. V **úložiště**, určí umístění úložiště a v **formát disku**, jak chtějí zřídit úložiště.
7. V **mapování sítě**, určují sítě, ke kterým se připojí virtuální počítač kolektoru. Aby mohla síť odesílat metadata do Azure, potřebuje připojení k internetu.
8. Zkontrolujte nastavení a vyberte **Power on po nasazení**> **Dokončit**. Po vytvoření zařízení se zobrazí zpráva potvrzující úspěšné dokončení.

### <a name="run-the-collector-to-discover-vms"></a>Spuštění kolektoru pro vyhledání virtuálních počítačů

Nyní se spuštění kolektoru pro vyhledání virtuálních počítačů. Všimněte si, že kolekce aktuálně podporuje pouze angličtinu (Spojené státy)"jako jazyk operačního systému a jazyk rozhraní kolektoru.

1. V vSphere klientské konzole > **otevřete konzoly**, určí jazyk, časové pásmo a heslo předvolby pro virtuální počítač kolektoru.
2. Na ploše klikněte **spustit kolektor** zástupce.

    ![Zástupce kolektoru](./media/contoso-migration-assessment/collector-shortcut.png)

4. V Azure Migrate Collector > **nastavit požadavky**, přijměte licenční podmínky a přečtěte si informace třetích stran.
5. Kolektor zkontroluje, že virtuální počítač má přístup k Internetu, synchronizaci času a že je spuštěná služba kolektoru (je nainstalovaný ve výchozím nastavení na virtuálním počítači). Kromě toho nainstaluje VMWare PowerCLI.

    > [!NOTE]
    > Předpokládáme, že má virtuální počítač přímý přístup k internetu bez proxy.

    ![Ověření požadavků](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. V **zadejte podrobnosti vCenter serveru**, určí název (FQDN) nebo IP adresu vCenter serveru a přihlašovací údaje jen pro čtení používá pro zjišťování.
7. Výběrem rozsah zjišťování virtuálních počítačů. Kolektor může vyhledat jen virtuální počítače v rámci zadaného rozsahu. Jako rozsah můžete vybrat konkrétní složku, datové centrum nebo cluster. Neměl by obsahovat víc než 1500 virtuálních počítačů.

    ![Připojení k vCenter](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. V **zadejte projekt migrace**, určí ID projektu Azure Migrate a klíč, který jste zkopírovali z portálu. Můžete je získat znovu v projektu **přehled** stránky > **zjistit počítače**.  

    ![Připojení k Azure](./media/contoso-migration-assessment/collector-connect-azure.png)

7. V **zobrazit průběh shromažďování** Contoso můžete sledovat zjišťování a zkontrolujte, jestli metadata shromážděná z virtuálních počítačů je v oboru. Kolektor vás informuje o tom, jak dlouho bude zjišťování přibližně trvat.

    ![Probíhající shromažďování](./media/contoso-migration-assessment/collector-collection-process.png)



### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po dokončení shromažďování Contoso ověří, že se virtuální počítače na portálu.

1. V projektu Azure Migrate > **spravovat** > **počítače**, zkontrolujte, jestli virtuální počítače, které chcete zjišťovat zobrazí.

    ![Zjištěné počítače](./media/contoso-migration-assessment/discovery-complete.png)

3. Všimněte si, že na počítačích aktuálně nejsou nainstalovaní agenti Azure Migrate. Contoso je potřeba nainstalovat abyste mohli zobrazit závislosti.

    ![Zjištěné počítače](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>Krok 5: Příprava na analýzu závislostí

Chcete-li zobrazit závislosti mezi virtuálními počítači, které chtějí mít přístup k Contoso, se stáhnout a nainstalovat agenty na ní virtuální počítače. Contoso to provede za všechny virtuální počítače pro svoje aplikace Windows a Linux.

### <a name="take-a-snapshot"></a>Pořízení snímku

Před změnou, předtím, než jsou nainstalovaní agenti, a to provedením snímku udržují kopie virtuálního počítače.

![Snímek počítače](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>Stažení a instalace agentů virtuálního počítače

1. Na **počítače** stránce, vyberte počítač a potom **vyžaduje instalaci** v **závislosti** sloupce.
2. Na **zjistit počítače** stránky, postupujte takto:
    - Stáhnout agenta MMA a závislosti pro každý virtuální počítač Windows
    - Stáhnout agenta MMA a závislosti pro každý virtuální počítač s Linuxem
3. Nyní, zkopírujte ID a klíč. Jejich potřebovat při instalaci agenta MMA.

    ![Stažení agenta](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Nainstalovat agenty na virtuálních počítačích s Windows

Na každém virtuálním počítači, spusťte instalaci.

#### <a name="install-the-mma-on-windows-vms"></a>Instalace agenta MMA na virtuálních počítačích s Windows

1. Dvakrát klikněte na staženého agenta.
2. V **cílovou složku**, ponechte výchozí instalační složku > **Další**.
2. V **možnosti instalace agenta**, vyberou **připojit agenta k Azure Log Analytics** > **Další**.

    ![Instalace agenta MMA](./media/contoso-migration-assessment/mma-install.png)

5. V **Azure Log Analytics**, vložte ID pracovního prostoru a klíč, který jste zkopírovali z portálu.

    ![Instalace agenta MMA](./media/contoso-migration-assessment/mma-install2.png)

6. V **připraveno k instalaci**, teď můžou nainstalovat agenta MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Instalace agenta závislostí na virtuálních počítačích s Windows

1. Dvakrát klikněte na staženého agenta závislostí.
2. Se přijetí podmínek licence a počkejte na dokončení instalace.

    ![Agent závislostí](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>Nainstalovat agenty na virtuální počítače s Linuxem

Na každém virtuálním počítači, spusťte instalaci.

#### <a name="install-the-mma-on-linux-vms"></a>Instalace agenta MMA na virtuální počítače s Linuxem

1. Knihovna python ctypes nainstalují na každý virtuální počítač pomocí: **sudo apt-get, nainstalujte python ctypeslib**.
2. Jejich by měl spustit příkaz k instalaci agenta MMA jako uživatel root.  Jako kořenové spusťte následující příkaz a zadejte kořenové heslo: **sudo -i**.
3. Nyní instalaci agenta MMA.
    - Vložte správné ID a klíč do příkazu.
    - Příkazy jsou pro 64bitovou verzi.
    - **ID pracovního prostoru** a **primární klíč** lze nalézt v portálu OMS > **nastavení**v **připojené zdroje** kartu.
    - Spusťte následující příkazy a stáhněte agenta OMS, ověření kontrolního součtu a instalaci a připojení agenta.

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```



#### <a name="install-the-dependency-agent-on-linux-vms"></a>Instalace agenta závislostí na virtuální počítače s Linuxem

Po instalaci agenta MMA Contoso agenta můžete nainstalovat závislost na virtuální počítače s Linuxem.

1. Agent závislostí je nainstalován v počítačích Linux pomocí Linux64.bin InstallDependencyAgent, skript prostředí samorozbalovací binárním souborem. Můžete spustit soubor s použitím TV nebo přidat oprávnění pro samotný soubor.

2. Instalace agenta závislostí Linux jako uživatel root:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Krok 6: Spuštění a analýza posouzení virtuálních počítačů

Contoso teď můžete ověřte závislosti počítačů a vytvořit skupinu. Pak spusťte posouzení pro skupinu.

### <a name="verify-dependencies-and-create-a-group"></a>Ověřte závislosti a vytvořte skupinu


1. Pro počítače k analýze kliknou **zobrazit závislosti**.

    ![Zobrazení závislostí počítačů](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Pro virtuální počítač SQLVM se na mapě závislostí zobrazí následující podrobnosti:

    - Spuštěné skupiny procesů nebo procesy s aktivními síťovými připojeními na virtuálním počítači SQLVM v zadaném časovém období (ve výchozím nastavení je to hodina).
    - Příchozí (klient) připojení přes protokol TCP ke všem závislým počítačům a odchozí (server) připojení přes protokol TCP ze všech závislých počítačů.
    - Závislé počítače s nainstalovanými agenty Azure Migrate se zobrazí v samostatných polích.
    - U počítačů bez nainstalovaných agentů se zobrazí informace o portu a IP adrese.

3. Pro počítače s nainstalovaným agentem (WEBVM), klikněte na pole počítače zobrazit další informace, včetně plně kvalifikovaný název domény, operačního systému a adresy MAC.

    ![Zobrazení skupinových závislostí](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Teď vyberte virtuální počítače přidat do skupiny (SQLVM a WEBVM).  Můžete stisknutím klávesy CTRL a kliknutím můžete vybrat několik virtuálních počítačů.
5. Kliknutím na **vytvořit skupinu**a zadejte název (smarthotelapp).

> [!NOTE]
    > Pokud chcete zobrazit podrobnější závislosti, můžete rozšířit časový rozsah. Můžete vybrat konkrétní dobu nebo počáteční a koncové datum.


### <a name="run-an-assessment"></a>Spuštění posouzení


1. Na **skupiny** stránky, otevřete skupinu (smarthotelapp) a klikněte na tlačítko **vytvořit posouzení**.

    ![Vytvoření posouzení](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Posouzení se zobrazí na stránce **Správa** > **Posouzení**.

Contoso používá výchozí nastavení posouzení, ale nastavení můžete přizpůsobit. [Další informace](how-to-modify-assessment.md).



### <a name="analyze-the-vm-assessment"></a>Analýza posouzení virtuálních počítačů

Posouzení služby Azure Migrate obsahuje informace o kompatibilitě místních virtuálních počítačů pro Azure, navrhované správné velikosti pro virtuální počítač Azure a odhadovaných měsíčních nákladech na Azure.

![Sestava posouzení](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

![Zobrazení posouzení](./media/contoso-migration-assessment/assessment-display.png)

Posouzení obdrží hodnocení spolehlivosti od 1 do 5 hvězdiček (1 hvězdička znamená nejnižší a 5 hvězdiček nejvyšší spolehlivost).
- Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení.
- Toto hodnocení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.
- Hodnocení spolehlivosti je užitečné při provádění *určení velikosti na základě výkonu* s Azure Migrate nemusí mít dostatek datových bodů určení velikosti na základě využití. V případě *určování stejné velikosti jako v místním prostředí* je hodnocení spolehlivosti vždy 5 hvězdiček, protože Azure Migrate má k dispozici všechny datové body, které k určení velikosti virtuálního počítače potřebuje.
- Tady je poskytnuté hodnocení spolehlivosti posouzení v závislosti na procentu dostupných datových bodů:

   **Dostupnost datových bodů** | **Hodnocení spolehlivosti**
   --- | ---
   0 až 20 % | 1 hvězdička
   21 až 40 % | 2 hvězdičky
   41 až 60 % | 3 hvězdičky
   61 až 80 % | 4 hvězdičky
   81 až 100 % | 5 hvězdiček

#### <a name="verify-readiness"></a>Ověření připravenosti

![Posouzení připravenosti](./media/contoso-migration-assessment/azure-readiness.png)  

V sestavě posouzení se zobrazí tabulka se souhrnem informací. Mějte na paměti, že k zobrazení velikostí určených na základě výkonu potřebuje služba Azure Migrate následující informace. Pokud tyto informace není možné shromáždit, posouzení velikostí nemusí být přesné.

- Data o využití procesoru a paměti.
- Informace o vstupně-výstupních operacích čtení a zápisu za sekundu a propustnosti pro všechny disky připojené k virtuálnímu počítači.
- Informace o síťových vstupech a výstupech pro všechny síťové adaptéry připojené k virtuálnímu počítači.


**Nastavení** | **Indikace** | **Podrobnosti**
--- | --- | ---
**Připravenost virtuálního počítače pro Azure** | Určuje, jestli je virtuální počítač připravený k migraci. | Možné stavy:</br><br/>– Připraveno pro Azure<br/><br/>– Připraveno s podmínkami <br/><br/>– Nepřipraveno pro Azure<br/><br/>– Připravenost neznámá<br/><br/> Pokud virtuální počítač není připravený, zobrazí se několik kroků pro odstranění problému.
**Velikost virtuálního počítače Azure** | Pro připravené virtuální počítače se zobrazí doporučená velikost virtuálního počítače Azure. | Doporučení velikosti závisí na vlastnostech posouzení:<br/><br/>– Pokud jste použili určení velikosti na základě výkonu, při určování velikosti se zohlední historie výkonu virtuálních počítačů.<br/><br/>– Pokud jste použili určení stejné velikosti jako v místním prostředí, určení velikosti bude založené na velikosti místního virtuálního počítače a data o využití se nepoužijí.
**Navrhovaný nástroj** | Vzhledem k tomu, že jsou na našich počítačích spuštění agenti, služba Azure Migrate prozkoumá spuštěné procesy v rámci počítače a určí, jestli daný počítač hostuje databázi, nebo ne.
**Informace o virtuálním počítači** | Tato sestava ukazuje nastavení místního virtuálního počítače, včetně informací o operačním systému, typu spouštění, discích a úložišti.


#### <a name="review-monthly-cost-estimates"></a>Kontrola odhadů měsíčních nákladů

Toto zobrazení informuje o celkových nákladech na výpočetní kapacitu a úložiště, které s sebou nese provoz virtuálních počítačů v Azure. Také nabízí podrobné údaje o jednotlivých počítačích.

![Posouzení připravenosti](./media/contoso-migration-assessment/azure-costs.png)

- Při výpočtu odhadovaných nákladů se používají doporučené velikosti počítačů.
- Odhadované měsíční náklady na výpočetní kapacitu a úložiště jsou agregované pro všechny virtuální počítače dané skupiny.


## <a name="clean-up-after-assessment"></a>Vyčištění po posouzení

- Po dokončení hodnocení, zachová Contoso Azure Migration zařízení pro budoucí hodnocení.
- Vypněte virtuální počítač VMware. Budete znovu spustit při vyhodnocují další virtuální počítače.
- Projekt migrace Contoso, budeme uchovávat v Azure.  Aktuálně je nasazených ve skupině prostředků ContosoFailoverRG oblasti USA – východ Azure.
-  Virtuální počítač kolektoru máte 180denní zkušební licenci. Pokud tento limit vyprší, se budete muset stáhnout a znovu nastavit kolektoru.


## <a name="conclusion"></a>Závěr

V tomto scénáři posuzuje Contoso jeho SmartHotel databáze aplikace pomocí nástroje DMA a místních virtuálních počítačů pomocí služby Azure Migrate. Jsou pak kontrola posouzení za účelem Ujistěte se, že místních prostředků jsou připravené k migraci do Azure.

## <a name="next-steps"></a>Další postup

V dalším článku v této sérii Contoso ke kolizi jeho SmartHotel aplikace v Azure s migrací lift and shift. Contoso migruje front-endu WEBVM pro aplikaci pomocí Azure Site Recovery a database aplikace do Azure SQL Managed Instance, používat službu Database Migration Service. [Začínáme](contoso-migration-rehost-vm-sql-managed-instance.md) s tímto nasazením.
