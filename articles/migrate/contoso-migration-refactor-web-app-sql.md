---
title: Aplikace Contoso Refaktorovat a migrujte jej pro webové aplikace Azure a Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso ke kolizi místní aplikace díky migraci na Azure Web App a Azure SQL Server database.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: eb046ceebf9c6233fa5178d18603add2f0c26a29
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53608013"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Migrace Contoso: Refaktorovat a místní aplikace do webové aplikace Azure a Azure SQL database

Tento článek ukazuje, jak společnosti Contoso refactors jejich aplikace SmartHotel360 v Azure. Jsou aplikace front-endový virtuální počítač migrovat do webové aplikace Azure a databáze aplikace ke službě Azure SQL database.

Tento dokument je jednou z řady článků, které ukazují, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a scénáře, které znázorňují nastavování infrastruktury migrace, posuzování migrace místních prostředků a spouštění různé druhy migrace. Scénáře jejich složitost v. Přidáme další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, článek řady a ukázkové aplikace, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak společnosti Contoso připraví jeho místní a infrastrukturu Azure na migraci. Stejnou infrastrukturu se používá pro všechny články týkající se migrace. | K dispozici.
[Článek 3: Posouzení místních prostředků](contoso-migration-assessment.md)  | Ukazuje, jak společnosti Contoso spuštění posouzení místních dvouvrstvé SmartHotel aplikaci spuštěnou v prostředí VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí [Azure Migrate](migrate-overview.md) služby a databáze aplikace SQL serveru s [Pomocníka s migrací databáze](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Změna hostitele aplikace na virtuálních počítačích Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak společnosti Contoso běží lift and shift migrace do Azure pro aplikaci SmartHotel. Contoso migruje virtuální počítač front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databáze aplikace do spravované Instance SQL, pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.
[Článek 5: Změna hostitele aplikace do virtuálních počítačů Azure](contoso-migration-rehost-vm.md) | Ukazuje, jak migrovat aplikace SmartHotel virtuálních počítačů pomocí Site Recovery pouze Contoso. | K dispozici.
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a SQL Server Always On Availability Group](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace. Contoso využívá Site Recovery k migraci aplikace, virtuální počítače a Database Migration service, jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn. | K dispozici.
[Článek 7: Změna hostitele aplikace Linux na virtuálních počítačích Azure](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak Contoso funguje lift and shift migrace aplikace osTicket Linux na virtuální počítače Azure pomocí služby Site Recovery | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure serveru MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak společnosti Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Site Recovery a migraci databáze aplikace na instanci serveru Azure MySQL pomocí aplikace MySQL Workbench. | K dispozici.
Článek 9: Refaktoring aplikace do webové aplikace Azure a databáze SQL Azure | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure | V tomto článku
[Článek 10: Refaktorovat Linuxovou aplikaci do Azure Web Apps a Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Ukazuje, jak společnosti Contoso migruje aplikace osTicket Linux do Azure Web Apps ve více lokalitách, integrovaná se službou GitHub pro průběžné doručování. Jejich migrovat databázi aplikace na instanci Azure MySQL. | K dispozici.
[Článek 11: Refaktorujte TFS na službách Azure DevOps](contoso-migration-tfs-vsts.md) | Ukazuje, jak společnosti Contoso migruje svoje místní nasazení Team Foundation Server (TFS) a migrujte ho ke službám Azure DevOps v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Ukazuje, jak společnosti Contoso migruje a rearchitects jeho SmartHotel aplikace do Azure. Jejich úprava architektury webové vrstvy aplikace jako kontejner Windows a databáze aplikace ve službě Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Ukazuje, jak společnosti Contoso znovu sestavte své aplikace SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služeb App Services, Kubernetes v Azure, Azure Functions, Cognitive services a Cosmos DB. | K dispozici.
[Článek 14: Škálování migrace do Azure](contoso-migration-scale.md) | Po vyzkoušení kombinace migrace, Contoso připraví škálovat na plné migraci do Azure. | K dispozici.

V tomto článku se migruje Contoso dvouvrstvé Windows. NET SmartHotel360 aplikace běžící na virtuálních počítačích VMware do Azure. Pokud chcete používat tuto aplikaci, se poskytuje jako open source a můžete ji stáhnout [Githubu](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzké spolupráci s obchodními partnery pochopit, co chtějí dosáhnout s tato migrace:

- **Adresa obchodní růst**: Contoso se rozrůstá a není tlak na místních systémů a infrastruktury.
- **Zvýšení efektivity**: Contoso je potřeba odebrat nepotřebné postupy a zjednodušte procesy pro vývojáře a uživatele.  Obchodní potřeby IT bude rychlé a ne produkovaného odpadu čas nebo peníze, tím rychlejší doručování na požadavky zákazníků.
- **Zvýšení flexibility**:  Contoso IT je potřeba se více přizpůsobovat podle potřeb firmy. Musí být schopné reagovat rychleji než změn na webu Marketplace, aby úspěch v globální ekonomiku.  Nesmí získat způsobem nebo stát obchodní blokování.
- **Škálování**: Růstem podniku zvládla úspěšně, musíte zadat Contoso IT systémy, které mohou rozšířit stejným tempem.
- **Náklady na**: Contoso chce minimalizovat náklady na licencování.

## <a name="migration-goals"></a>Cíle migrace

Tým cloudových Contoso má připnutou dolů cíle pro tuto migraci. Tyto cíle se používá k určení nejlepší metody migrace.

**Požadavky** | **Podrobnosti**
--- | --- 
**Aplikace** | Aplikace ve službě Azure zůstane tak kritický, protože se ještě dnes.<br/><br/> Měl by mít stejné možnosti výkonu, stejně jako aktuálně ve službě VMWare.<br/><br/> Tým nechce investovat do aplikace. Prozatím se správci jednoduše přesune aplikace bezpečně do cloudu.<br/><br/> Tým má zastavit podporu Windows Server 2008 R2, která je aktuálně spuštění aplikace.<br/><br/> Také chce tým přesunout mimo systém SQL Server 2008 R2 na moderní platformě databáze PaaS, což minimalizuje potřebu správy.<br/><br/> Contoso má využívat své investice do licencí SQL serveru se Software Assurance, kde je to možné.<br/><br/> Kromě toho Contoso chce zmírnit jediný bod selhání na webové vrstvy.
**Omezení** | Aplikace se skládá z aplikace ASP.NET a WCF služby spuštěné na stejném virtuálním počítači. Chtějí se to rozdělit mezi dva webové aplikace s využitím služby Azure App Service. 
**Azure** | Contoso chce přejít aplikace do Azure, ale nechce spustit na virtuálních počítačích. Contoso chce využívat služby Azure PaaS pro úrovně web a data. 
**DevOps** | Contoso chce přesunout do modelu DevOps pomocí Azure DevOps pro jejich sestavení a vydávání kanálů.

## <a name="solution-design"></a>Návrh řešení

Po Připnutí dolů cíle a požadavky, Contoso navrhuje, zkontrolujte nasazení řešení a identifikuje proces migrace, včetně služeb Azure, které se použije pro migraci.

### <a name="current-app"></a>Aktuální aplikaci

- Místní aplikace SmartHotel360 je rozvrstvená na dva virtuální počítače (WEBVM a SQLVM).
- Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5)
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Contoso má místní datacentrum (contoso-datacenter), s řadičem domény v místním (**contosodc1**).
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.


### <a name="proposed-solution"></a>Navržené řešení

- Pro databázovou vrstvu aplikace Contoso porovnání Azure SQL Database s použitím SQL serveru [v tomto článku](https://docs.microsoft.com/azure/sql-database/sql-database-features). Contoso rozhodnutí přejít k Azure SQL Database z několika důvodů:
    - Azure SQL Database je spravovaná služba relační databáze. Nabízí předvídatelný výkon na několika úrovních služeb, téměř bez nutnosti jakékoli správy. K výhodám patří dynamickou škálovatelnost bez výpadků, integrovanou inteligentní optimalizaci a globální škálovatelnost a dostupnost.
    - Contoso mohou využívat lightweight Data Migration Assistant (DMA) k vyhodnocení a migrovat místní databázi Azure SQL.
    - S programem Software Assurance si mohou vyměňovat Contoso stávající licence pro zlevněné sazby pro SQL Database, pomocí zvýhodněné hybridní využití Azure pro SQL Server. To může poskytovat úspory až 30 %.
    - SQL Database poskytuje mnoho funkcí zabezpečení, včetně maskování neustálé šifrování, dynamických dat a detekce ohrožení/zabezpečení na úrovni řádků.
- Pro webovou vrstvu aplikace Contoso se rozhodl používat Azure App Service. Tato služba PaaS umožňuje, aby nasazení aplikace s pár změn konfigurace. Contoso se pomocí sady Visual Studio proveďte požadovanou změnu a nasaďte dvě webové aplikace. Jeden pro web a jeden pro službu WCF.
- Abyste splnili požadavky pro kanál DevOps, Contoso vybral používat Azure DevOps s úložišti Git pro správu zdrojového kódu (SCM). Automatizované buildy a vydání se použije k sestavení kódu a nasazení do Azure Web Apps.
  
### <a name="solution-review"></a>Kontrola řešení
Contoso vyhodnotí své navrhované návrhu společně vložením seznam výhody a nevýhody.

**Posouzení** | **Podrobnosti**
--- | ---
**V oblasti IT** | Kód aplikace SmartHotel360 nebudete muset změnit pro migraci do Azure.<br/><br/> Contoso můžou využívat své investice do Software Assurance využitím zvýhodněné hybridní využití Azure pro SQL Server a Windows Server.<br/><br/> Po dokončení migrace nebude nutné podporovat Windows Server 2008 R2. [Další informace](https://support.microsoft.com/lifecycle).<br/><br/> Contoso můžete nakonfigurovat na úrovni webové aplikace s více instancemi, tak, aby se už jediný bod selhání.<br/><br/> Databáze nebude závisí na stárnutí SQL Server 2008 R2.<br/><br/> SQL Database podporuje technické požadavky. Contoso posouzení místní databáze pomocí Pomocníka s migrací databáze a zjistí, že je kompatibilní.<br/><br/> SQL Database nemá předdefinovanou odolnost proti chybám, Contoso nepotřebujete nastavit. Tím se zajistí, že datová vrstva již není jediným bodem převzetí služeb při selhání.
**Nevýhody** | Služba Azure App Services podporuje pouze jedno nasazení aplikace pro každou webovou aplikaci. To znamená, že dvě webové aplikace musí být zřízená (jeden pro web) a jeden pro službu WCF.<br/><br/> Pokud společnost Contoso využívá nástroj Data Migration Assistant namísto služby migrace dat k migraci jejich databáze, nebude mít infrastruktury připravené pro migraci databází ve velkém měřítku. Contoso bude muset sestavit jiné oblasti zajistit převzetí služeb při selhání, pokud není k dispozici primární oblasti.

## <a name="proposed-architecture"></a>Navrhované architektury

![Architektura scénáře](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Proces migrace

1. Contoso zřídí instanci Azure SQL a migraci databáze SmartHotel360 k němu.
2. Contoso zřizuje a konfiguruje webových aplikací a nasadí aplikaci SmartHotel360 k nim.

    ![Proces migrace](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Společnost Contoso použije DMA k vyhodnocení a zjištění problémů s kompatibilitou, které může mít vliv na jejich fungování databáze v Azure. DMA vyhodnocuje paritu funkcí mezi SQL zdroje a cíle a doporučuje vylepšení výkonu a spolehlivosti. | Tento nástroj je zdarma ke stažení.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Inteligentní, plně spravovaná relační Cloudová databázová služba. | Náklady na základě funkcí, propustnosti a velikosti. [Další informace](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Azure App Service – Web Apps](https://docs.microsoft.com/azure/app-service/overview) | Vytváření výkonných cloudových aplikací s využitím plně spravované platformy | Náklady podle doby trvání velikost, umístění a využití. [Další informace](https://azure.microsoft.com/pricing/details/app-service/windows/).
[Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | Poskytuje průběžné integrace a průběžného nasazování (CI/CD) kanálů pro vývoj aplikací. Kanál začíná úložiště Git pro správu kód aplikace, systém sestavení pro vytváření balíčků a další artefakty sestavení a systém správy vydaných verzí nasazení změny do vývojových, testovacích a produkčních prostředích. 

## <a name="prerequisites"></a>Požadavky

Tady je Contoso je potřeba spustit tento scénář:

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Společnosti Contoso vytvořit odběry během dřívější článek. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.
**Infrastruktura Azure** | [Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastavení infrastruktury Azure.


## <a name="scenario-steps"></a>Kroky scénáře

Tady je způsob spuštění migrace Contoso:

> [!div class="checklist"]
> * **Krok 1: Zřízení instance SQL Database v Azure**: Contoso zřídí instanci SQL v Azure. Poté, co je web aplikace migraci do Azure, webové aplikace služby WCF bude odkazovat na tuto instanci.
> * **Krok 2: Migrace databáze pomocí DMA**: Contoso migraci databáze aplikace pomocí Pomocníka s migrací databáze.
> * **Krok 3: Zřízení webové aplikace**: Contoso zřídí dvě webové aplikace.
> * **Krok 4: Nastavení Azure DevOps**: Contoso vytvoří nový projekt Azure DevOps a naimportuje úložiště Git.
> * **Krok 5: Konfigurace připojovacích řetězců**: Contoso nakonfiguruje připojovacích řetězců tak, aby webové vrstvy webové aplikace, webové aplikace služby WCF a instanci SQL lze komunikovat.
> * **Krok 6: Nastavení sestavení a vydávání kanálů**: V posledním kroku Contoso nastaví sestavení a vydání kanály vytvořte aplikaci a nasadí je do dvou samostatných Azure Web Apps.


## <a name="step-1-provision-an-azure-sql-database"></a>Krok 1: Zřízení databáze služby Azure SQL Database

1. Vyberte správce společnosti Contoso vytvořit databázi SQL v Azure. 

    ![Zřízení SQL](media/contoso-migration-refactor-web-app-sql/provision-sql1.png)

2. Zadejte název databáze tak, aby odpovídaly databáze na místním virtuálním počítači běží (**SmartHotel.Registration**). Ve skupině prostředků ContosoRG, umístěte databázi. Toto je skupina prostředků, které používají pro produkční prostředky v Azure.

    ![Zřízení SQL](media/contoso-migration-refactor-web-app-sql/provision-sql2.png)

3. Nastavené nové instanci SQL serveru (**sql. smarthotel eus2**) v primární oblasti.

    ![Zřízení SQL](media/contoso-migration-refactor-web-app-sql/provision-sql3.png)

4. Nastavují cenové úrovně tak, aby odpovídaly svůj server a databázové požadavky. A vyberou s programem Azure Hybrid Benefit ušetříte peníze, protože už mají licence SQL serveru.
5. K určení velikosti používají podle jader v nákupu a nastavte limity pro jejich očekávaný požadavky.

    ![Zřízení SQL](media/contoso-migration-refactor-web-app-sql/provision-sql4.png)

6. Potom vytvoří instanci databáze.

    ![Zřízení SQL](media/contoso-migration-refactor-web-app-sql/provision-sql5.png)

7. Po vytvoření instance otevřít databázi a poznamenejte si podrobnosti, které potřebují při použití asistované migraci databáze pro migraci.

    ![Zřízení SQL](media/contoso-migration-refactor-web-app-sql/provision-sql6.png)


**Potřebujete další pomoc?**

- [Získejte pomoc](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) Zřizuje se databáze SQL.
- [Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) omezení prostředků virtuálních jader.


## <a name="step-2-migrate-the-database-with-dma"></a>Krok 2: Migrace databáze pomocí DMA

Správce společnosti Contoso provede migraci SmartHotel360 database pomocí DMA.

### <a name="install-dma"></a>Instalace DMA

1. Stažení nástroje [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) k virtuálnímu počítači v místním SQL serveru (**SQLVM**).
2. Na virtuálním počítači, spusťte instalační program (DownloadMigrationAssistant.msi).
3. Na **Dokončit** stránky, vyberou **spusťte Microsoft Data Migration Assistant** před dokončením průvodce.

### <a name="migrate-the-database-with-dma"></a>Migrace databáze pomocí DMA

1. V DMA, jejich vytvoření nového projektu (**SmartHotelDB**) a vyberte **migrace** 
2. Typ zdrojového serveru jako vyberou **systému SQL Server**a cíl jako **Azure SQL Database**. 

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-1.png)

3. V podrobnostech migrace přidat **SQLVM** jako na zdrojovém serveru a **SmartHotel.Registration** databáze. 

     ![DMA](media/contoso-migration-refactor-web-app-sql/dma-2.png)

4. Obdrží chybu, která vypadá, že má být přidružena k ověřování. Po prozkoumání, ale tento problém je tečka (.) v názvu databáze. Alternativním řešením je se rozhodli zřídit novou databázi SQL pomocí názvu **SmartHotel registrace**, k vyřešení daného problému. Při spuštění DMA znovu, si mohou vybrat **SmartHotel registrace**a pokračujte v průvodci.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-3.png)

5. V **výběr objektů, které**, vyberte databázové tabulky a vygenerovat skript SQL.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-4.png)

6. Po DMS vytvoří skript, klepnutím na **nasadit schéma**.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-5.png)

7. DMA potvrdí, že nasazení proběhlo úspěšně.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-6.png)

8. Nyní spusťte migrace.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-7.png)

9. Po dokončení migrace správce společnosti Contoso, můžete ověřit, že databáze je spuštěna v instanci Azure SQL.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Jejich odstranění navíc databáze SQL **SmartHotel.Registration** na webu Azure Portal.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>Krok 3: Zřízení webové aplikace

S databází migrovat, správce společnosti Contoso můžete nyní zřídit dvě webové aplikace.

1. Vyberou **webovou aplikaci** na portálu.

    ![Webová aplikace](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. Poskytuje název aplikace (**SHWEB EUS2**), spusťte ho na Windows a umístěte ho zrušit provozní skupině pro prostředky **ContosoRG**. Vytvoří novou službu app service a plán.

    ![Webová aplikace](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Po zřízení webové aplikace, opakujte postup vytvoření webové aplikace služby WCF (**SHWCF EUS2**)

    ![Webová aplikace](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. Po jejich dokončení, přejděte na adresu aplikací ke kontrole, že jste byla úspěšně vytvořena.


## <a name="step-4-set-up-azure-devops"></a>Krok 4: Nastavení služby Azure DevOps


Contoso potřebuje pro sestavení infrastruktury DevOps a kanály pro aplikaci.  K tomuto účelu správce společnosti Contoso vytvořit nový projekt DevOps, naimportovat kód a pak nastavení sestavení a vydávání kanálů.

1.   V účtu Contoso Azure DevOps, vytvořte nový projekt (**ContosoSmartHotelRefactor**) a vyberte **Git** pro správu verzí.

    ![Nový projekt](./media/contoso-migration-refactor-web-app-sql/vsts1.png)
2. Jejich importovat úložiště Git, která nyní obsahuje jejich kód aplikace. Se [veřejného úložiště](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) a můžete ji stáhnout.

    ![Stáhněte si kód aplikace](./media/contoso-migration-refactor-web-app-sql/vsts2.png)
    
3. Po importu kód, připojte se k úložišti aplikace Visual Studio a klonování kódu pomocí Průzkumníka týmových projektů.

    ![Připojte se k projektu](./media/contoso-migration-refactor-web-app-sql/devops1.png)

4. Po naklonování úložiště do počítače pro vývojáře, otevřete soubor řešení pro aplikaci. Webové aplikace a wcf služby mají oddělení v rámci souboru projektu.

    ![Soubor řešení](./media/contoso-migration-refactor-web-app-sql/vsts4.png)
    

## <a name="step-5-configure-connection-strings"></a>Krok 5: Konfigurace připojovacích řetězců

Správce společnosti Contoso se muset ujistit, že webové aplikace a databáze můžete všechny komunikovat. Provedete to tak, že konfigurace připojovacích řetězců v kódu a ve službě web apps.

1. Ve webové aplikaci pro službu WCF (**SHWCF EUS2**) > **nastavení** > **nastavení aplikace**, se přidat nový připojovací řetězec s názvem  **Možnost DefaultConnection**.
2. Připojovací řetězec se použije **SmartHotel registrace** databáze a je třeba aktualizovat se správnými přihlašovacími údaji.

    ![Připojovací řetězec](media/contoso-migration-refactor-web-app-sql/string1.png)

3. Pomocí sady Visual Studio, otevřou **SmartHotel.Registration.wcf** projektu ze souboru řešení. **ConnectionStrings** část souboru web.config pro službu WCF SmartHotel.Registration.Wcf je třeba aktualizovat připojovací řetězec.

     ![Připojovací řetězec](media/contoso-migration-refactor-web-app-sql/string2.png)

4. **Klienta** část souboru web.config SmartHotel.Registration.Web by měl být změněn tak, aby odkazoval na nové umístění služby WCF. Toto je adresa URL webové aplikace WCF, který je hostitelem koncového bodu služby.

    ![Připojovací řetězec](media/contoso-migration-refactor-web-app-sql/strings3.png)

5. Jakmile jsou změny v kódu, musí se správci tím potvrdíte změny. Pomocí Průzkumníka týmových projektů v sadě Visual Studio, že commmit a synchronizace.


## <a name="step-6-set-up-build-and-release-pipelines-in-azure-devops"></a>Krok 6: Nastavení sestavení a vydávání kanálů v Azure DevOps

Správce společnosti Contoso teď nakonfigurovat Azure DevOps k provedení sestavení a proces vydávání verzí.

1. V Azure DevOps kliknou **sestavení a vydání** > **nový kanál**.

    ![Nový kanál](./media/contoso-migration-refactor-web-app-sql/pipeline1.png)

2. Vyberou **úložiště Git v Azure** a příslušné úložiště.

    ![Git a úložiště](./media/contoso-migration-refactor-web-app-sql/pipeline2.png)

3. V **vyberte šablonu**, výběrem šablony ASP.NET pro jejich sestavení.

     ![Šablony ASP.NET](./media/contoso-migration-refactor-web-app-sql/pipeline3.png)
    
4. Název **ContosoSmartHotelRefactor. technologie ASP.NET CI** se používá pro sestavení. Kliknutím na **Uložit & frontu**.

     ![Uložit a fronty](./media/contoso-migration-refactor-web-app-sql/pipeline4.png)

5. To zahajuje první sestavení. Kliknutí na číslo sestavení ke sledování procesu. Po dokončení se mohou zobrazit proces zpětné vazby a klikněte na tlačítko **artefakty** zobrazení výsledků sestavení.

    ![Revize](./media/contoso-migration-refactor-web-app-sql/pipeline5.png)

6. Složka **vyřadit** obsahuje výsledků sestavení.

    - Soubory zip dva jsou balíčky obsahující aplikace.
    - Tyto soubory se používají v kanál pro vydávání verzí pro nasazení do Azure Web Apps

     ![Artefakt](./media/contoso-migration-refactor-web-app-sql/pipeline6.png)

7. Kliknutím na **verze** > **+ nový kanál**.

    ![Nový kanál](./media/contoso-migration-refactor-web-app-sql/pipeline7.png)

8. Výběrem šablony nasazení Azure App Service.

    ![Šablony Azure App Service](./media/contoso-migration-refactor-web-app-sql/pipeline8.png)

9. Jejich název kanál pro vydávání verzí **ContosoSmartHotel360Refactor**a zadejte název webové aplikace WCF (SHWCF EUS2) **fáze** název.

    ![Prostředí](./media/contoso-migration-refactor-web-app-sql/pipeline9.png)

10. V části fáze, kliknou **úlohy 1, 1 úloha** ke konfiguraci nasazení služby WCF.

    ![Nasazení WCF](./media/contoso-migration-refactor-web-app-sql/pipeline10.png)

11. Ověřují, že je vybraný a oprávnění a vyberte předplatné **název služby App service**.

     ![Vyberte službu app service](./media/contoso-migration-refactor-web-app-sql/pipeline11.png)

12. V kanálu > **artefakty**, vyberou **+ přidat artefakt**a vyberte k sestavení **ContosoSmarthotel360Refactor** kanálu.

     ![Sestavení](./media/contoso-migration-refactor-web-app-sql/pipeline12.png)

15. Kliknutí blesk na artefakt je zaškrtnuté políčko. povolte trigger průběžného nasazování.

     ![Blesk](./media/contoso-migration-refactor-web-app-sql/pipeline13.png)

16. Trigger průběžného nasazování musí být nastavená na **povoleno**.

   ![Průběžné nasazování povoleno](./media/contoso-migration-refactor-web-app-sql/pipeline14.png) 

17. Teď, přecházejí zpět do fáze 1 úlohy můžu úkoly a klikněte na **nasazení služby Azure App Service**.

    ![Nasadit službu app service](./media/contoso-migration-refactor-web-app-sql/pipeline15.png)

18. V **vybrat soubor nebo složku**, vyhledejte **SmartHotel.Registration.Wcf.zip** soubor, který vytvářel během sestavení a clilck **Uložit**.-sql

    ![Uložit WCF](./media/contoso-migration-refactor-web-app-sql/pipeline16.png)

19. Kliknutím na **kanálu** > **fáze** **+ přidat**, chcete-li přidat prostředí pro **SHWEB EUS2**. Výběrem jiné nasazení služby Azure App Service.

    ![Přidání prostředí](./media/contoso-migration-refactor-web-app-sql/pipeline17.png)

20. Opakujte postup publikování webové aplikace (**SmartHotel.Registration.Web.zip**) souboru do správné webové aplikace.

    ![Publikování webové aplikace](./media/contoso-migration-refactor-web-app-sql/pipeline18.png)

21. Po uložení, kanál pro vydávání verzí se zobrazí takto.

     ![Kanál souhrnu vydané verze](./media/contoso-migration-refactor-web-app-sql/pipeline19.png)

22. Při přechodu zpět **sestavení**a klikněte na tlačítko **triggery** > **aktivovat nepřetržitou integraci**. To umožňuje kanálu tak, že když se změny potvrdí do kódu a úplné sestavení a vydaných verzí dojde k.

    ![Povolit průběžnou integraci](./media/contoso-migration-refactor-web-app-sql/pipeline20.png)

23. Kliknutím na **Uložit & frontu** úplný kanál spouštět. Nové sestavení se aktivuje, který potom vytvoří první verzi této aplikace do služby Azure App Service.

    ![Uložení kanálu](./media/contoso-migration-refactor-web-app-sql/pipeline21.png)

24. Správce společnosti Contoso postupovat podle sestavení a vydání proces kanálu z Azure DevOps. Po dokončení sestavení, vydání se spustí.

    ![Sestavení a vydání aplikace](./media/contoso-migration-refactor-web-app-sql/pipeline22.png)

25. Po dokončení kanálu nasadili oba weby a aplikace je aplikace bude pracovat online.

    ![Dokončení vydání verze](./media/contoso-migration-refactor-web-app-sql/pipeline23.png)

Aplikace je v tuto chvíli úspěšné migraci do Azure.



## <a name="clean-up-after-migration"></a>Vyčištění po migraci

Po dokončení migrace Contoso potřebuje k dokončení těchto kroků vyčištění:  

- Odebrání místních virtuálních počítačů z inventáře vCenter.
- Odeberte virtuální počítače z místní úlohy zálohování.
- Aktualizujte interní dokumentaci zobrazíte nové umístění pro aplikace SmartHotel360. Zobrazit databáze jako spuštěná v Azure SQL database a front-endu jako spuštěná dvě webové aplikace.
- Zkontrolujte všechny prostředky, které pracují s Vyřazená z provozu virtuálních počítačů a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.


## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Contoso s migrovaných prostředků v Azure, musí plně zprovoznění a zabezpečení jejich nové infrastruktury.

### <a name="security"></a>Zabezpečení

- Contoso je potřeba zajistit, aby jejich nové **SmartHotel registrace** databáze je zabezpečená. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Zejména Contoso by měl aktualizovat webové aplikace pro použití protokolu SSL s certifikáty.

### <a name="backups"></a>Zálohování

- Contoso je potřeba zkontrolovat požadavkům zálohování pro Azure SQL Database. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso také potřebuje další informace o správě záloh SQL Database a obnoví. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups) o automatických zálohách.
- Contoso měli uvažovat o implementaci skupiny převzetí služeb při selhání pro zajištění regionální převzetí služeb při selhání pro databázi. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso je potřeba zvážit nasazení webové aplikace v hlavní oblasti východní USA 2 a oblast USA (střed) pro odolnost. Společnost Contoso může konfigurace služby Traffic Manager zajistit převzetí služeb při selhání v případě místních výpadků.

### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

- Po nasazení jsou všechny prostředky, by měl Contoso přiřadit Azure značky na základě jejich [plánování infrastruktury](contoso-migration-infrastructure.md#set-up-tagging).
- Všechny licence je integrovaná do náklady na služby PaaS, které Contoso využívá. To se odečte od smlouvy EA.
- Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení správy nákladů multicloudové, který vám umožní využívat a spravovat Azure a další cloudové prostředky.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management.

## <a name="conclusion"></a>Závěr

V tomto článku se Refaktorovat Contoso aplikace SmartHotel360 ve službě Azure migrací aplikace front-endový virtuální počítač na dvě webové aplikace Azure. Databáze aplikace se migroval do Azure SQL database.






