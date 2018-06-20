---
title: Vyhodnocení místní úlohy pro migraci Contoso do Azure | Microsoft Docs
description: Zjistěte, jak Contoso vyhodnocuje své místní počítače pro migraci do Azure s Azure migrace a migrace databáze
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: fb987c95afc0f77386f4f78c44f3c6825f86ee43
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232329"
---
# <a name="contoso-migration-assess-on-premises-workloads-for-migration-to-azure"></a>Migrace Contoso: vyhodnocení místní úlohy pro migraci na Azure

Tento článek ukazuje, jak Contoso vyhodnocuje SmartHotel jeho místní aplikace při přípravě na jeho migraci do Azure.

Tento dokument je třetí v řadě článků, které dokumentů, jak fiktivní společnosti Contoso migruje jeho místních prostředků do cloudu Microsoft Azure. Řada obsahuje základní informace a řadu scénářů nasazení, které ukazují, jak nastavit infrastruktury, a migrace vyhodnocení vhodnosti místních prostředků pro migraci a spouštět různé typy migrace. Scénáře růst v složitost a jsme přidali další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, řady článku a ukázkových aplikací, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak Contoso připraví jeho místní a infrastrukturu Azure pro migraci. Stejnou infrastrukturu se používá pro všechny scénáře migrace Contoso. | K dispozici.
Článek 3: Vyhodnocení místních prostředků (v tomto článku)  | Ukazuje, jak Contoso spouští posouzení své místní aplikace SmartHotel dvouvrstvá běžící ve VMware. Jejich vyhodnocení aplikace virtuálních počítačů s [Azure migrovat](migrate-overview.md) služby a aplikace databáze systému SQL Server s [Azure databáze migrace pomocníka](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Metody opětovného hostování virtuální počítače Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak Contoso migruje SmartHotel aplikace do Azure. Jejich migraci virtuálních počítačů front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databáze aplikace pomocí [migrace databáze Azure](https://docs.microsoft.com/azure/dms/dms-overview) služby migrovat do spravované Instance SQL. | K dispozici.
[Článek 5: Pro virtuální počítače Azure opětovným hostováním](contoso-migration-rehost-vm.md) | Ukazuje, jak Contoso migrovat své aplikace SmartHotel virtuální počítače pouze pomocí Site Recovery.
[Článek 6: Opětovným hostováním do Azure virtuálních počítačů a skupin dostupnosti SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak Contoso migruje SmartHotel aplikace. Site Recovery používají k migraci aplikace virtuální počítače a migraci databáze službu, kterou chcete migrovat databázi aplikace do skupiny dostupnosti SQL Server. | K dispozici.
[Článek 7: Opětovným hostováním Linux aplikace pro virtuální počítače Azure](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak Contoso migruje jejich osService Linux aplikace pomocí Azure Site Recovery.
[Článek 8: Opětovným hostováním Linux aplikace pro virtuální počítače Azure a Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak Contoso migruje aplikace osService Linux, pomocí Site Recovery pro migraci virtuálních počítačů a MySQL Workbench migrace (do instance serveru MySQL Azure. | K dispozici.


## <a name="overview"></a>Přehled

Stejně jako se zvažte migraci na Azure, společnost Contoso chce spustit technické a finanční hodnocení zjistěte, jestli jsou vhodné pro migraci do cloudu jeho místní úlohy. Konkrétně týmem Contoso chcete posoudit kompatibilitu počítače a databáze pro migraci a odhadnout kapacitu a náklady na spuštění svým prostředkům v Azure.

Pokud chcete získat jejich wet nohou a lépe pochopit související se situací, že přejdete k vyhodnocení dva své místní aplikace, technologie shrnuté v následující tabulce. Všimněte si, že jste hodnocením pro scénáře migrace této metody opětovného hostování a refactor aplikací pro migraci. Další informace o opětovném hostování a refaktoring v [Přehled migrace Contoso](contoso-migration-overview.md).

**Název aplikace** | **Platforma** | **Aplikace úrovně** | **Podrobnosti**
--- | --- | --- | ---
SmartHotel<br/><br/> Spravuje požadavky cesta Contoso | V systému Windows s databází systému SQL Server | Dvojúrovňová aplikace pomocí webu ASP.NET front-endu systémem jeden virtuální počítač (WEBVM) a SQL Server běžící na jiný virtuální počítač (SQLVM) | Virtuální počítače jsou VMware, spuštěná na hostiteli ESXi spravuje vCenter server.<br/><br/> Ukázková aplikace si můžete stáhnout z [Githubu](https://github.com/Microsoft/SmartHotel360).
OSTicket<br/><br/> Aplikace contoso služby podpory | Systémem Linux nebo Apache s MySQL PHP (svítilna). | Dvojúrovňová aplikaci s front-endu webu PHP na jeden virtuální počítač (OSTICKETWEB) a databáze MySQL, spuštěná na jiný virtuální počítač (OSTICKETMYSQL) | Aplikace se používá aplikace služby zákazníka ke sledování problémů pro interní zaměstnance a externí zákazníky.<br/><br/> Ukázková aplikace si můžete stáhnout z [Githubu](https://github.com/osTicket/osTicket).

## <a name="current-architecture"></a>Aktuální architektura


Zde je diagram zobrazující aktuální místní infrastrukturu společnosti Contoso.

![Architektura contoso](./media/contoso-migration-assessment/contoso-architecture.png)  

- Contoso má jeden hlavní datovém centru, které jsou umístěné v městě New York ve východní USA.
- Mají tři další místní pobočky v USA.
- Hlavní datového centra je připojený k Internetu s fiber metro připojení k síti ethernet (500 MB/s).
- U každé větve je místně připojen k Internetu pomocí připojení třída firmy, tunelových propojení IPSec pro síť VPN zpět do hlavní datového centra. To umožňuje jejich celá síť být trvale připojené a optimalizuje připojení k Internetu.
- Hlavní datového centra je plně virtualizovaného s VMware. Mají dva virtualizace hostitele ESXi 6.5 spravovanou vCenter Server verze 6.5.
- Společnost Contoso využívá služby Active Directory pro správu identit a servery DNS v interní síti.
- Běží na virtuálních počítačích VMware řadiče domény v datovém centru. Řadiče domény v místní větví spustit na fyzických serverech.





## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co chce firmy dosáhnout s této migrace:

- **Adresa růst podniku**: Contoso roste a výsledkem je tlak na svých místních systémů a infrastruktury.
- **Zvýšení efektivity**: Contoso musí odebrat nepotřebné postupy a zefektivnit procesy pro jejich vývojářům a uživatelům.  Obchodním potřebám IT rychlá a není nakládání s čas nebo peníze, tím rychleji doručováním na požadavky zákazníků.
- **Zvýšení flexibility**: Contoso IT musí být rychlejšího potřebám firmy. Musí být schopné reagovat rychleji, než změny v marketplace, chcete-li povolit úspěch v globálním hospodářství.  Nemůže získat způsobem, ani stát otevíraných oken firmy.
- **Škálování**: růstem firmy úspěšně Contoso IT musí poskytnout systémy, které se můžou růst stejným tempem.

## <a name="assessment-goals"></a>Vyhodnocení cíle

Tým cloudu Contoso má připnutý dolů cíle pro jejich hodnocení migrace:

- Po dokončení migrace by měla aplikace v Azure měly stejné funkce výkonu jako dnes ve svém prostředí VMWare místní.  Přechodu do cloudu nebude znamenat, že je aplikace výkonu méně kritický.
- Contoso je potřeba pochopit jejich aplikace a databáze kompatibilitu s požadavky na Azure, stejně jako možnosti jejich hostování v Azure.
- Správa databáze společnosti Contoso by měl být minimalizován po aplikací mít přesunout do cloudu.  
- Contoso chce seznamování nejen možnosti migrace, ale také nákladů spojených s infrastrukturou po přesunu do cloudu.

## <a name="assessment-tools"></a>Posuzovací nástroje
Contoso je pomocí nástrojů Microsoft pro hodnocení. Tyto nástroje jsou zarovnané s svých cílů a by měl poskytovat všechny informace, které potřebují.

**Technologie** | **Popis** | **Náklady**
--- | --- | ---
[Asistent migrace systému databáze (DMA).](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Přímý přístup do paměti se budete používat při vyhodnocování a zjistit problémy s kompatibilitou, které může mít vliv na jejich funkce databáze v Azure. DMA vyhodnocuje parity funkcí mezi SQL zdroje a cíle a doporučuje vylepšení výkonu a spolehlivosti. | Tento nástroj je zdarma ke stažení.
[Azure Migrate](https://docs.microsoft.com/azure/migrate/migrate-overview) | Contoso použije k vyhodnocení jejich virtuální počítače VMware této služby. Posuzuje vhodnost počítačů k migraci a poskytuje odhady velikostí a nákladů, které bude vyžadovat jejich provoz v Azure.  | Je aktuálně (může 2018) bez poplatků pro používání této služby.
[Mapa služeb](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) | Azure Migrate využívá mapu služeb k zobrazení závislostí mezi počítači, které chcete migrovat. |  Mapa služeb je součástí Azure Log Analytics. V současné době je možné ji používat po dobu 180 dnů bez poplatků.

V tomto scénáři Contoso stáhne a spustí DMA k vyhodnocení, místní databázi systému SQL Server pro svou aplikaci cesta. Používají Azure migrovat pomocí mapování závislostí pro vyhodnocení aplikace virtuální počítače, před migrací do Azure.



## <a name="assessment-architecture"></a>Architektura hodnocení


![Architektura posouzení migrace](./media/contoso-migration-assessment/migration-assessment-architecture.png)

- Contoso je fiktivní název představující typické podnikové organizace. 
- Contoso má místního datového centra (**contoso-datacenter**), s řadiči domény místní (CONTOSODC1, CONTOSODC2).
- Virtuální počítače VMware se nacházejí na VMware ESXI hostitelích používajících verze 6.5. Hostitelé: **contosohost1**, **contosohost2**
- Spravuje prostředí VMware vCenter server verze 6.5 (**venter**, běžící na virtuálním počítači.
- Cesta aplikace SmartHotel:
    - Aplikace je vrstvené mezi dva virtuální počítače VMware, **WEBVM** a **SQLVM**.
    - Virtuální počítače se nenachází v hostiteli VMware ESXi **contosohost1.contoso.com**.
    - Virtuální počítače se systémem Windows Server 2008 R2 Datacenter s aktualizací SP1.
- Správu prostředí VMware zajišťuje vCenter Server (**vcenter.contoso.com**) spuštěný na virtuálním počítači.
- OSTicket služby podpory aplikace:
    - Aplikace je vrstvené mezi dva virtuální počítače, **OSTICKETWEB** a **OSTICKETMYSQL**.
    - Virtuální počítače běží na Ubuntu Linux Server 16.04-LTS.
    - Virtuální počítač OSTICKETWEB běží Apache 2 a PHP 7.0.
    - Virtuální počítač OSTICKETMYSQL běží MySQL 5.7.22.

![Architektura](./media/contoso-migration-assessment/architecture.png)


## <a name="prerequisites"></a>Požadavky

Zde je seznam toho, co Contoso (a) je pro posouzení:

- Vlastníkem nebo přispěvatelem přístup pro předplatné Azure, nebo pro skupinu prostředků v předplatném Azure.
- Místní server vCenter verze 5.5, 6.0 nebo 6.5.
- Účet jen pro čtení na serveru vCenter nebo oprávnění k jeho vytvoření.
- Oprávnění k vytvoření virtuálního počítače na serveru vCenter pomocí šablony .OVA.
- Alespoň jednoho hostitele ESXi verze 5.0 nebo novější.
- Alespoň dva místní virtuální počítače VMware, na jednom z nichž běží databáze SQL Serveru.
- Oprávnění k instalaci agentů Azure migrovat na každém virtuálním počítači.
- Virtuální počítače by měly mít přímé připojení k internetu.
        – Internetový přístup můžete omezit na [požadované adresy URL](https://docs.microsoft.com/azure/migrate/concepts-collector#collector-pre-requisites).
        -Pokud počítače bez připojení k Internetu [OMS brány](../log-analytics/log-analytics-oms-gateway.md) musí být nainstalovaný na ně.
- Plně kvalifikovaný název domény virtuálního počítače, na kterém je spuštěná instance SQL Serveru, pro účely posouzení databáze.
- Brána Windows Firewall na virtuálním počítači s SQL Serverem by měla umožňovat externí připojení na portu TCP 1433 (výchozí nastavení), aby se nástroj DMA mohl připojit.


## <a name="assessment-overview"></a>Přehled hodnocení

Zde uvádíme jak Contoso se chystáte provést vyhodnocení:


> [!div class="checklist"]
> * **Krok 1: Stáhněte a nainstalujte DMA**: Příprava DMA pro vyhodnocení místní databázi systému SQL Server.
> * **Krok 2: Vyhodnocení databáze s DMA**: spouštět a analyzovat assessment databáze.
> * **Krok 3: Příprava pro vyhodnocení virtuálních počítačů s Azure migrovat**: nastavení místních účtů a TweakUI VMware nastavení.
> * **Krok 4: Zjistit místní virtuální počítače s Azure migrovat**: vytvoření kolekce Azure migraci virtuálních počítačů. Potom spusťte kolekce k vyhledání virtuálních počítačů pro vyhodnocení.
> * **Krok 5: Příprava pro analýzu závislostí s Azure migrovat**: migrovat na Azure nainstalovat agenty na virtuálních počítačích, aby uvidí závislostí mapování mezi virtuálními počítači.
> * **Krok 6: Vyhodnocení virtuálních počítačů s Azure migrovat**: Zkontrolujte závislosti, skupiny virtuálních počítačů a spustit vyhodnocení. Po vyhodnocení je připraven, jejich analýza během přípravy na migraci.


## <a name="step-1-download-and-install-the-dma"></a>Krok 1: Stáhněte a nainstalujte DMA

1. Contoso stáhne DMA z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595).
    - Pomocník lze nainstalovat na jakýkoli počítač, který může připojit k instanci serveru SQL. Není potřeba ho spouštět na počítači s SQL Serverem.
    - By neměla být běžet na hostitelském počítači systému SQL Server.
2. Jejich spusťte stažený instalační soubor (DownloadMigrationAssistant.msi), spusťte instalaci.
3. Na **Dokončit** stránky, vyberou **spusťte Microsoft Data migrace pomocníka** před dokončením průvodce.

## <a name="step-2-run-and-analyze-the-database-assessment-for-smarthotel"></a>Krok 2: Spuštění a analyzovat assessment databáze pro SmartHotel

Contoso teď můžete spustit posouzení analyzovat jejich místní SQL Server pro aplikaci SmartHotel.

1. V Pomocníka pro migraci databáze kliknou **nový**, vyberte **Assessment**a zadejte název projektu - hodnocení **SmartHotel**.
2. Vyberou **serveru typ zdroje** jako **systému SQL Server na virtuálních počítačích Azure**. 

    ![Výběr zdroje](./media/contoso-migration-assessment/dma-assessment-1.png)

    > [!NOTE]
      V současné době DMA nepodporuje posouzení pro migraci do spravované instance SQL. Jako alternativní řešení Contoso používá SQL Server na virtuálním počítači Azure jako předpokládané cíl pro hodnocení.

3. V **vyberte cílovou verzi**, vyberou SQL Server 2017 jako cílová verze. Je třeba tuto možnost vyberte, protože se jedná o verzi, kterou spravované Instance systému SQL.
4. Vyberou zjistit informace o kompatibilitě a nové funkce:
    - **Problémy s kompatibilitou** Poznámka: změny, které by mohlo dojít k migraci nebo menší úpravy před migrací, které vyžadují. Udržuje přehled o funkcích aktuálně používán, které jsou zastaralé. Problémy jsou uspořádané podle úrovně kompatibility.
    - **Nové funkce doporučení** poznámky k nové funkce v cílové platformy systému SQL Server, který lze použít pro databázi po migraci. Tyto funkce jsou uspořádané podle výkonu, zabezpečení a úložiště.

    ![Výběr cíle](./media/contoso-migration-assessment/dma-assessment-2.png)

2. V **připojit k serveru**, jejich zadejte název virtuálního počítače s databáze a přihlašovacích údajů k přístupu. Je nutné povolit **certifikátu serveru důvěryhodnosti** a ujistěte se, mohou získat k systému SQL Server. Pak kliknou **Connect**.

    ![Výběr cíle](./media/contoso-migration-assessment/dma-assessment-3.png)

3. V **přidat zdroj**, jejich databázi chtějí vyhodnotit a klikněte na tlačítko Přidat **Další** zahájíte hodnocení.
4. Hodnocení je vytvořen.
    
    ![Vytvoření posouzení](./media/contoso-migration-assessment/dma-assessment-4.png)

5. V **zobrazit výsledky**, uvidí výsledky hodnocení.


### <a name="analyze-the-database-assessment"></a>Analýza posouzení databáze

Výsledky se zobrazí, jakmile jsou k dispozici. Pokud se opravit problémy, stačí kliknout na tlačítko **restartujte hodnocení** znovu hodnocení.

1. V **problémy s kompatibilitou** sestavy, zkontrolujte všechny problémy na každé úrovni kompatibility. Mapování úrovní kompatibility na verze SQL Serveru je následující:

    - 100: SQL Server 2008/Azure SQL Database
    - 110: SQL Server 2012/Azure SQL Database
    - 120: SQL Server 2014/Azure SQL Database
    - 130: SQL Server 2016/Azure SQL Database
    - 140: SQL Server 2017/Azure SQL Database

    ![Problémy s kompatibilitou](./media/contoso-migration-assessment/dma-assessment-5.png)

2. V **funkci doporučení** sestavu Contoso můžete zobrazit výkon, zabezpečení a úložiště funkcí, které doporučuje hodnocení po migraci. Doporučuje se celou řadu funkcí, včetně OLTP v paměti a Columnstore, funkce Stretch Database, vždycky šifrovaná, dynamického maskování dat a transparentní šifrování šifrování dat (TDE).

    ![Doporučení funkcí](./media/contoso-migration-assessment/dma-assessment-6.png)

    > [!NOTE]
    > Doporučujeme, aby Contoso [umožňuje šifrování TDE](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption?view=sql-server-2017) pro všechny SQL Server databáze a to je ještě více důležité při databáze jsou v cloudu. Šifrování TDE by měl povolit pouze po migraci. Pokud už je povolené šifrování TDE, musíte přesunout certifikát nebo asymetrický klíč do hlavní databáze cílového serveru. [Další informace](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).

2. Jejich můžete exportovat assessment ve formátu JSON nebo CSV.

Všimněte si, že pokud používáte větší assessment škálování můžete:

- Současně spustit více vyhodnocení a zobrazit stav hodnocení otevřením **všechny vyhodnocování** stránky.
- [Konsolidovat vyhodnocování do databáze systému SQL Server](https://docs.microsoft.com/sql/dma/dma-consolidatereports?view=ssdt-18vs2017#import-assessment-results-into-a-sql-server-database).
- [Konsolidovat vyhodnocování do PowerBI sestavy](https://docs.microsoft.com/sql/dma/dma-powerbiassesreport?view=ssdt-18vs2017).


## <a name="step-3-prepare-for-vm-assessment-with-azure-migrate"></a>Krok 3: Příprava pro vyhodnocení virtuálních počítačů s Azure migrovat

Contoso musí vytvořit účet VMware, který migrovat Azure budou používat pro automaticky zjistit virtuální počítače pro vyhodnocení, ověřte oprávnění k vytvoření virtuálního počítače, Všimněte si, které je třeba otevřít porty a úroveň nastavení statistik.

### <a name="set-up-a-vmware-account"></a>Nastavení účtu VMware

 Zjišťování virtuálních počítačů vyžaduje účet jen pro čtení v vCenter, s následujícími vlastnostmi: 

- Typ uživatele: Alespoň uživatel jen pro čtení.
- Oprávnění: Objekt datového centra –> Rozšířit na podřízený objekt, role=Read-only.
- Podrobnosti: Uživatel přiřazený na úrovni datacentra s přístupem ke všem objektům v tomto datacentru.
- Pokud chcete omezit přístup, přiřaďte podřízeným objektům (hostitelé vSphere, úložiště dat, virtuální počítače a sítě) roli **Žádný přístup** s objektem **Rozšířit na podřízený objekt**.

### <a name="verify-permissions-to-create-a-vm"></a>Ověření oprávnění k vytvoření virtuálního počítače

Contoso ověřte, zda má oprávnění k vytvoření virtuálního počítače importováním souboru v. VAJÍČKA formátu. [Další informace](https://kb.vmware.com/s/article/1023189?other.KM_Utility.getArticleLanguage=1&r=2&other.KM_Utility.getArticleData=1&other.KM_Utility.getArticle=1&ui-comm-runtime-components-aura-components-siteforce-qb.Quarterback.validateRoute=1&other.KM_Utility.getGUser=1).

### <a name="verify-ports"></a>Ověření portů

Hodnocení Contoso používá mapování závislostí. Tato funkce vyžaduje nainstalovaných na virtuálních počítačích, které chcete posoudit agenta. Agent musí být schopný se připojit k Azure z port 443 protokolu TCP pro každý virtuální počítač. [Další informace](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid) o požadavcích na připojení.


### <a name="set-statistics-settings"></a>Nastavení statistiky

Než se pustíte do nasazování, Contoso musí nastavit statistiky nastavení pro vCenter Server na úroveň 3. Poznámky:

- Po nastavení úrovně, budete muset počkat aspoň jeden den před spuštěním hodnocení. Jinak nemusí fungovat podle očekávání.
- Pokud je úroveň vyšší než 3, posouzení bude fungovat, ale:
    - Nebudou se shromažďovat data o výkonu disků a sítí.
    - V případě úložiště služba Azure Migrate doporučí v Azure standardní disk stejné velikosti jako místní disk.
    - V případě sítí se pro každý místní síťový adaptér doporučí síťový adaptér v Azure.
    - V případě výpočetních prostředků služba Azure Migrate zjistí počet jader a velikost paměti virtuálního počítače a doporučí virtuální počítač Azure se stejnou konfigurací. Pokud existuje více vhodných velikostí virtuálních počítačů Azure, doporučí se virtuální počítač s nejnižšími náklady.
- [Další informace](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#sizing) o určování velikosti u úrovně 3.

Úroveň nastavují následujícím způsobem:

1. V vSphere webovému klientovi že otevřete instance serveru vCenter.
2. V **spravovat** > **nastavení** > **Obecné**, klepnutím na **upravit**.
3. V **statistiky**, se nastavení této statistiky úrovně **úroveň 3**.

    ![Úroveň statistiky vCenter](./media/contoso-migration-assessment/vcenter-statistics-level.png)



## <a name="step-4-discover-vms"></a>Krok 4: Vyhledání virtuálních počítačů

K vyhledání virtuálních počítačů, vytvoří Contoso projektu Azure migrovat. Se stáhnout a nastavit kolekce virtuální počítač a spusťte kolekce ke zjištění jejich místní virtuální počítače.

### <a name="create-a-project"></a>Vytvoření projektu

1. V [portál Azure](https://portal.azure.com), vyhledávají **Azure migrovat**a vytvořte projekt (ContosoMigration).
2. Jejich zadejte název projektu, předplatné Azure a vytvořte novou skupinu prostředků Azure, **ContosoFailoverRG**. Poznámky:

    - Projekt Azure Migrate můžete vytvořit pouze v oblasti Střed USA – západ nebo USA – východ.
    - Migraci můžete naplánovat pro jakékoli cílové umístění.
    - Umístění projektu slouží pouze k uložení metadat shromážděných z místních virtuálních počítačů.

    ![Azure Migrate](./media/contoso-migration-assessment/project-1.png)


### <a name="download-the-collector-appliance"></a>Stažení zařízení kolektoru

Azure Migrate vytvoří místní virtuální počítač, kterému se říká zařízení kolektoru. Tento virtuální počítač vyhledá místní virtuální počítače VMware a odešle jejich metadata do služby Azure Migrate. Pokud chcete nastavit zařízení kolekce, stáhne Contoso. VAJÍČKA šablony a naimportuje ho do místní server vCenter vytvořte virtuální počítač.

1. V projektu Azure migrovat > **Začínáme** > **Discover & hodnocení** > **zjišťovat počítače**, stáhnou. Soubor šablony vajíčka.
2. Zkopírujte jejich projektu ID a klíč. Tyto jsou potřeba ke konfiguraci kolekce.

    ![Stažení souboru .OVA](./media/contoso-migration-assessment/download-ova.png)

### <a name="verify-the-collector-appliance"></a>Ověření zařízení kolektoru

Před nasazením virtuálního počítače, Contoso kontroluje, zda. Soubor vajíčka je zabezpečený.

1. Na počítači, na kterém soubor stažen že otevřete okno příkaz správce.
2. Jejich spusťte následující příkaz pro generování hodnoty hash pro vajíčka:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Příklady použití: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. Vygenerovaná hodnota hash by měla odpovídat následujícímu nastavení (verze 1.0.9.7):

    **Algoritmus** | **Hodnota hash**
    --- | ---
    MD5 | d5b6a03701203ff556fa78694d6d7c35
    SHA1 | f039feaa10dccd811c3d22d9a59fb83d0b01151e
    SHA256 | e5e997c003e29036f62bf3fdce96acd4a271799211a84b34b35dfd290e9bea9c


### <a name="create-the-collector-appliance"></a>Vytvoření zařízení kolektoru

Nyní můžete importovat stažený soubor k systému vCenter Server Contoso a proveďte zřízení serveru konfigurace virtuálních počítačů.

1. V konzole klienta vSphere kliknou **soubor** > **nasazení šablony OVF**.

    ![Nasazení šablony OVF](./media/contoso-migration-assessment/vcenter-wizard.png)

2. V Průvodci nasazením šablony OVF > **zdroj**, určí umístění. VAJÍČKA soubor.
3. V **název a umístění**, jejich zadejte popisný název pro kolekci virtuálních počítačů a inventáře umístění, ve kterém se bude hostovat virtuální počítač. Také určit hostitele nebo cluster, na kterém se spustí kolekce zařízení.
5. V **úložiště**, jejich určete umístění úložiště a v **formát disku**, jak se chcete zřídit úložiště.
7. V **mapování sítě**, určí sítě, ke kterému se připojí kolekce virtuálních počítačů. Aby mohla síť odesílat metadata do Azure, potřebuje připojení k internetu.
8. Zkontrolujte nastavení a vyberte **zapnout po nasazení**> **Dokončit**. Po vytvoření zařízení se zobrazí zpráva potvrzující úspěšné dokončení.

### <a name="run-the-collector-to-discover-vms"></a>Spuštění kolektoru pro vyhledání virtuálních počítačů

Nyní spusťte kolekce k vyhledání virtuálních počítačů. Všimněte si, že kolekce aktuálně podporuje pouze "Angličtina (Spojené státy)" jako jazyk operačního systému a collector jazyk rozhraní.

1. V vSphere klienta konzoly > **otevřít konzolu**, určí předvolby jazyka, časové pásmo a heslo pro kolekci virtuálních počítačů.
2. Na ploše klikněte **spustit kolekce** zástupce.

    ![Zástupce kolektoru](./media/contoso-migration-assessment/collector-shortcut.png)

4. V kolekci migrovat Azure > **nastavit požadavky**, přijměte licenční podmínky a číst informace o jiných výrobců.
5. Kolekce kontroluje, zda virtuální počítač má přístup k Internetu, že je čas synchronizován a zda je spuštěna služba collector (je nainstalovaná ve výchozím nastavení ve virtuálním počítači). Nainstaluje taky VMWare PowerCLI.

    > [!NOTE]
    > Předpokládáme, že má virtuální počítač přímý přístup k internetu bez proxy.

    ![Ověření požadavků](./media/contoso-migration-assessment/collector-verify-prereqs.png)


5. V **zadejte podrobnosti o serveru vCenter**, určí název (FQDN) nebo IP adresa serveru vCenter a jen pro čtení pověření použít pro vyhledávání.
7. Výběrem obor pro zjišťování virtuálních počítačů. Kolektor může vyhledat jen virtuální počítače v rámci zadaného rozsahu. Jako rozsah můžete vybrat konkrétní složku, datové centrum nebo cluster. Neměl by obsahovat víc než 1500 virtuálních počítačů.

    ![Připojení k vCenter](./media/contoso-migration-assessment/collector-connect-vcenter.png)

6. V **zadejte migrace projektu**, určí migrovat Azure projektu ID a klíč, který jste zkopírovali z portálu. Získat je můžete znovu v projektu **přehled** stránky > **zjišťovat počítače**.  

    ![Připojení k Azure](./media/contoso-migration-assessment/collector-connect-azure.png)

7. V **sledovat průběh kolekce** Contoso můžete sledovat zjišťování a zkontrolujte, že metadata shromážděných z virtuálních počítačů jsou v oboru. Kolektor vás informuje o tom, jak dlouho bude zjišťování přibližně trvat.

    ![Probíhající shromažďování](./media/contoso-migration-assessment/collector-collection-process.png) 



### <a name="verify-vms-in-the-portal"></a>Kontrola virtuálních počítačů na portálu

Po dokončení shromažďování Contoso ověří, že virtuální počítače se objeví na portálu.

1. V projektu Azure migrovat > **spravovat** > **počítače**, jejich zkontrolujte, že se objeví virtuálních počítačů, které chcete zjistit.

    ![Zjištěné počítače](./media/contoso-migration-assessment/discovery-complete.png)

3. Všimněte si, že na počítačích aktuálně nejsou nainstalovaní agenti Azure Migrate. Contoso je potřeba nainstalovat v závislosti na zobrazení pořadí.

    ![Zjištěné počítače](./media/contoso-migration-assessment/machines-no-agent.png)



## <a name="step-5-prepare-for-dependency-analysis"></a>Krok 5: Příprava pro analýzu závislostí

Zobrazit závislosti mezi virtuálními počítači, které chtějí mít přístup k Contoso, se stáhnout a nainstalovat agenty na aplikaci virtuálních počítačů. Contoso tomu na všech virtuálních počítačích pro své aplikace, systém Windows a Linux.

### <a name="take-a-snapshot"></a>Pořízení snímku

Před změnou, pořízením snímku před instalací agentů se jejich ponechat si kopii virtuálního počítače.

![Snímek počítače](./media/contoso-migration-assessment/snapshot-vm.png)


### <a name="download-and-install-the-vm-agents"></a>Stažení a instalace agentů virtuálního počítače

1. Na **počítače** stránky, jejich vyberte počítač a potom **vyžaduje instalaci** v **závislosti** sloupce.
2. Na **zjišťovat počítače** stránky udělají toto:
    - Stáhnout agenta MMA a závislosti pro každý virtuální počítač s Windows
    - Stáhnout agenta MMA a závislosti pro každý virtuální počítač s Linuxem
3. Nyní se zkopírujte ID a klíč. Při instalaci MMA musí tyto.

    ![Stažení agenta](./media/contoso-migration-assessment/download-agents.png)

### <a name="install-the-agents-on-windows-vms"></a>Nainstalovat agenty na virtuálních počítačích Windows

Spuštění instalace v jednotlivých virtuálních počítačů.

#### <a name="install-the-mma-on-windows-vms"></a>Nainstalujte na virtuální počítače Windows

1. Jejich poklikejte na stažený agenta.
2. V **cílovou složku**, se zachovat výchozí instalační složku > **Další**.
2. V **možnosti instalace agenta**, vyberou **připojit agenta k analýze protokolů Azure** > **Další**.

    ![Instalace agenta MMA](./media/contoso-migration-assessment/mma-install.png)
    
5. V **Azure Log Analytics**, vložte ID a klíč, který jste zkopírovali z portálu. 

    ![Instalace agenta MMA](./media/contoso-migration-assessment/mma-install2.png)

6. V **připraveno k instalaci**, se nyní můžete nainstalovat MMA.

#### <a name="install-the-dependency-agent-on-windows-vms"></a>Nainstalujte agenta závislost na virtuální počítače Windows

1. Jejich poklikejte na stažený agenta závislostí.
2. Jejich přijmout licenční podmínky a počkejte na dokončení instalace.

    ![Agent závislostí](./media/contoso-migration-assessment/dependency-agent.png)


### <a name="install-the-agents-on-linux-vms"></a>Nainstalovat agenty na virtuální počítače s Linuxem

Spuštění instalace v jednotlivých virtuálních počítačů.

#### <a name="install-the-mma-on-linux-vms"></a>Nainstalujte na virtuální počítače s Linuxem

1. Instalaci ctypes knihovna python na každý virtuální počítač pomocí: **sudo výstižný get nainstalovat python ctypeslib**.
2. Příkaz pro instalaci agenta MMA jako kořenový by měly být spuštěny.  K kořenové spustit následující příkaz a zadejte heslo kořenové: **sudo -i**.
3. Nyní nainstalovat agenta MMA.
    - Vložte vaše správné ID a klíč do příkazu.
    - Příkazy jsou pro 64bitovou verzi.
    - **ID pracovního prostoru** a **primární klíč** lze nalézt v portálu OMS > **nastavení**v **připojené zdroje** kartě.
    - Spusťte následující příkazy a stáhnout agenta OMS, ověření kontrolního součtu a instalaci nebo zařadit agenta.

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w 6b7fcaff-7efb-4356-ae06-516cacf5e25d -s k7gAMAw5Bk8pFVUTZKmk2lG4eUciswzWfYLDTxGcD8pcyc4oT8c6ZRgsMy3MmsQSHuSOcmBUsCjoRiG2x9A8Mg==
    ```
 


#### <a name="install-the-dependency-agent-on-linux-vms"></a>Nainstalujte agenta závislost na virtuální počítače s Linuxem

Po instalaci agenta MMA Contoso můžete nainstalovat agenta závislost na virtuální počítače s Linuxem.

1. Je závislost Agent nainstalován počítačů se systémem Linux pomocí InstallDependencyAgent-Linux64.bin, skript prostředí s samorozbalující binární. Můžete spustit soubor pomocí dílet nebo přidat oprávnění ke samotném souboru.

2. Instalaci agenta Linux závislostí jako kořenová:

    ```
    wget --content-disposition https://aka.ms/dependencyagentlinux -O InstallDependencyAgent-Linux64.bin && sudo sh InstallDependencyAgent-Linux64.bin -s
    ```


## <a name="step-6-run-and-analyze-the-vm-assessment"></a>Krok 6: Spuštění a analyzovat hodnocení virtuálního počítače

Contoso teď můžete ověřit počítač závislosti a vytvořte skupinu. Potom spusťte assessment pro skupinu.

### <a name="verify-dependencies-and-create-a-group"></a>Ověřte, závislosti a vytvořit skupinu


1. Pro počítače k analýze kliknou **zobrazení závislostí**.

    ![Zobrazení závislostí počítačů](./media/contoso-migration-assessment/view-machine-dependencies.png)

2. Pro virtuální počítač SQLVM se na mapě závislostí zobrazí následující podrobnosti:

    - Spuštěné skupiny procesů nebo procesy s aktivními síťovými připojeními na virtuálním počítači SQLVM v zadaném časovém období (ve výchozím nastavení je to hodina).
    - Příchozí (klient) připojení přes protokol TCP ke všem závislým počítačům a odchozí (server) připojení přes protokol TCP ze všech závislých počítačů.
    - Závislé počítače s nainstalovanými agenty Azure Migrate se zobrazí v samostatných polích.
    - U počítačů bez nainstalovaných agentů se zobrazí informace o portu a IP adrese.

3. Pro počítače s nainstalovaným agentem (WEBVM) že klikněte na pole počítače zobrazíte další informace, včetně plně kvalifikovaný název domény, operační systém a adresy MAC.

    ![Zobrazení skupinových závislostí](./media/contoso-migration-assessment/sqlvm-dependencies.png)

4. Nyní vyberte virtuální počítače, který chcete přidat do skupiny (SQLVM a WEBVM).  Se můžete pomocí kombinace CTRL + kliknutím vyberte víc virtuálních počítačů.
5. Klepnutím na **vytvořit skupinu**a zadejte název (smarthotelapp).

> [!NOTE]
    > Pokud chcete zobrazit podrobnější závislosti, můžete rozšířit časový rozsah. Můžete vybrat konkrétní dobu nebo počáteční a koncové datum.


### <a name="run-an-assessment"></a>Spuštění posouzení


1. Na **skupiny** stránky, otevřete skupinu (smarthotelapp) a klikněte na **vytvořit assessment**.

    ![Vytvoření posouzení](./media/contoso-migration-assessment/run-vm-assessment.png)

2. Posouzení se zobrazí na stránce **Správa** > **Posouzení**.

Contoso používá výchozí nastavení hodnocení, ale nastavení můžete přizpůsobit. [Další informace](how-to-modify-assessment.md).



### <a name="analyze-the-vm-assessment"></a>Analýza posouzení virtuálních počítačů

Posouzení migrace Azure obsahuje informace o kompatibilitě místní virtuální počítače Azure, doporučuje optimalizaci velikosti virtuálního počítače Azure a odhadované měsíční náklady na Azure.

![Sestava posouzení](./media/contoso-migration-assessment/assessment-overview.png)

#### <a name="review-confidence-rating"></a>Kontrola hodnocení spolehlivosti

![Zobrazení posouzení](./media/contoso-migration-assessment/assessment-display.png)

Posouzení získá jistotou z 1 hvězdička na 5 hvězdu (1 hvězdička se nejnižší a nejvyšší 5 hvězdičkou).
- Hodnocení spolehlivosti se k posouzení přiřadí na základě dostupnosti datových bodů potřebných pro výpočet posouzení.
- Toto hodnocení pomáhá odhadnout spolehlivost doporučení velikostí poskytovaných službou Azure Migrate.
- Hodnocení spolehlivosti se hodí, pokud byste *výkonu na základě velikosti* jako migrovat Azure pravděpodobně nemá dostatečný počet datových bodů udělat nastavení velikosti na základě využití. V případě *určování stejné velikosti jako v místním prostředí* je hodnocení spolehlivosti vždy 5 hvězdiček, protože Azure Migrate má k dispozici všechny datové body, které k určení velikosti virtuálního počítače potřebuje.
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


## <a name="clean-up-after-assessment"></a>Vyčištění po vyhodnocení

- Po dokončení hodnocení Contoso zachová zařízení migrace Azure pro budoucí hodnocení.
- Vypněte virtuální počítač VMware. Jejich budete spusťte ji znovu při vyhodnocují dalších virtuálních počítačů.
- V Azure se budete pokračovat v projektu Contoso migrace.  Je aktuálně nasazená ve skupině prostředků ContosoFailoverRG nám Azure oblasti Východ.
-  Kolekce virtuálních počítačů má 180denní zkušební licence. Pokud tento limit vyprší, se budete muset stáhnout a nastavit kolekce znovu.


## <a name="conclusion"></a>Závěr

V tomto scénáři Contoso hodnoceno její SmartHotel databáze aplikace pomocí nástroje pro přímý přístup do paměti a místní virtuální počítače pomocí služby Azure migrovat. Pak se kontrolují vyhodnocování a ujistěte se, že místní prostředky jsou připravené pro migraci do Azure.

## <a name="next-steps"></a>Další postup

V další článek z této série Contoso opětovném hostování jeho SmartHotel aplikaci v Azure pomocí nástroje Migrace navýšení a shift. Contoso migruje front-endu WEBVM pro aplikace pomocí Azure Site Recovery a databáze aplikace do Azure SQL spravované Instance, pomocí služby migrace databáze. [Začínáme](contoso-migration-rehost-vm-sql-managed-instance.md) v tomto nasazení.
