---
title: Refaktorovat aplikaci Contoso a migrujte je do skupiny dostupnosti virtuálních počítačů Azure a SQL Server AlwaysOn | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso opětovným hostováním místní aplikaci a migrujte ho do kontejneru Azure webové aplikace a databáze Azure SQL Server.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/12/2018
ms.author: raynew
ms.openlocfilehash: f4a348815ef058cb795ed12e8f118b494650a555
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39005186"
---
# <a name="contoso-migration-refactor-an-on-premises-app-to-an-azure-web-app-and-azure-sql-database"></a>Migrace Contoso: Refaktorovat místní aplikace do webové aplikace Azure a Azure SQL database

Tento článek ukazuje, jak společnosti Contoso refactors jejich SmartHotel aplikace v Azure. Jsou aplikace front-endový virtuální počítač migrovat do webové aplikace Azure a databáze aplikace ke službě Azure SQL database.

Tento dokument je jednou z řady článků, které ukazují, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a scénáře, které znázorňují nastavování infrastruktury migrace, posuzování migrace místních prostředků a spouštění různé druhy migrace. Scénáře jejich složitost v a přidáme další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, článek řady a ukázkové aplikace, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak společnosti Contoso připraví jeho místní a infrastrukturu Azure na migraci. Stejnou infrastrukturu se používá pro všechny články týkající se migrace. | K dispozici.
[Článek 3: Posouzení místních prostředků](contoso-migration-assessment.md)  | Ukazuje, jak společnosti Contoso spuštění posouzení místních dvouvrstvé SmartHotel aplikaci spuštěnou v prostředí VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí [Azure Migrate](migrate-overview.md) služby a databáze aplikace SQL serveru s [Pomocníka s migrací databáze](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Změna hostitele aplikace na virtuálních počítačích Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak společnosti Contoso běží lift and shift migrace do Azure pro aplikaci SmartHotel. Contoso migruje virtuální počítač front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databáze aplikace do spravované Instance SQL, pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.
[Článek 5: Změna hostitele aplikace do virtuálních počítačů Azure](contoso-migration-rehost-vm.md) | Ukazuje, jak migrovat aplikace SmartHotel virtuálních počítačů pomocí Site Recovery pouze Contoso. | K dispozici.
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a SQL Server Always On Availability Group](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace. Contoso využívá Site Recovery k migraci aplikace, virtuální počítače a Database Migration service, jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn. | K dispozici.
[Článek 7: Změna hostitele Linuxovou aplikaci do virtuálních počítačů Azure](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak Contoso funguje lift and shift migrace aplikace osTicket Linux na virtuální počítače Azure pomocí služby Site Recovery | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure serveru MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak společnosti Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Site Recovery a migraci databáze aplikace na instanci serveru Azure MySQL pomocí aplikace MySQL Workbench. | K dispozici.
Článek 9: Refaktorujte aplikace do webové aplikace Azure a Azure SQL database | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure | V tomto článku
[Článek 10: Refaktorujte Linuxovou aplikaci do Azure Web Apps a Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Ukazuje, jak společnosti Contoso migruje aplikace osTicket Linux do Azure Web Apps ve více lokalitách, integrovaná se službou GitHub pro průběžné doručování. Jejich migrovat databázi aplikace na instanci Azure MySQL. | K dispozici.
[Článek 11: Refaktorovat TFS na VSTS](contoso-migration-tfs-vsts.md) | Ukazuje, jak společnosti Contoso migruje svoje místní nasazení Team Foundation Server (TFS) a migrujte jej na Visual Studio Team Services (VSTS) v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Ukazuje, jak společnosti Contoso migruje a rearchitects jeho SmartHotel aplikace do Azure. Jejich úprava architektury webové vrstvy aplikace jako kontejner Windows a databáze aplikace ve službě Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Ukazuje, jak společnosti Contoso znovu sestavte své aplikace SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služeb App Services, Kubernetes v Azure, Azure Functions, Cognitive services a Cosmos DB. | K dispozici.


V tomto článku se migruje Contoso dvouvrstvé Windows. NET SmartHotel aplikace běžící na virtuálních počítačích VMware do Azure. Pokud chcete používat tuto aplikaci, se poskytuje jako open source a můžete ji stáhnout [Githubu](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co chtějí dosáhnout s tato migrace:

- **Adresa obchodní růst**: Contoso se rozrůstá a není tlak na svých místních systémů a infrastruktury.
- **Zvýšení efektivity**: Contoso musí odebrat nepotřebné postupy a zjednodušte procesy pro vývojáře a uživatele.  Obchodní potřeby IT bude rychlé a ne produkovaného odpadu čas nebo peníze, tím rychlejší doručování na požadavky zákazníků.
- **Zvýšení flexibility**: Contoso IT je potřeba se více přizpůsobovat podle potřeb firmy. Musí být schopné reagovat rychleji než změn na webu Marketplace, aby úspěch v globální ekonomiku.  Nesmí získat způsobem nebo stát obchodní blokování.
- **Škálování**: růstem podniku zvládla úspěšně, musíte zadat Contoso IT systémy, které mohou rozšířit stejným tempem.
- **Náklady na**: Contoso chce minimalizovat náklady na licencování.

## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou dolů cíle pro tuto migraci. Tyto cíle se používá k určení nejlepší metody migrace.

**Požadavky** | **Podrobnosti**
--- | --- 
**Aplikace** | Aplikace ve službě Azure zůstane tak kritický, protože se ještě dnes.<br/><br/> Měl by mít stejné možnosti výkonu, stejně jako aktuálně ve službě VMWare.<br/><br/> Jejich nechcete investovat do aplikace. Prozatím jednoduše chtějí se bezpečně přesunout do cloudu.<br/><br/> Chtějí se zastavit podporu Windows Server 2008 R2, která je aktuálně spuštění aplikace.<br/><br/> Chtějí se přesunout mimo systém SQL Server 2008 R2 na moderní platformě databáze PaaS, což minimalizuje potřebu správy.<br/><br/> Contoso má využívat své investice do licencí SQL serveru se Software Assurance, kde je to možné.<br/><br/> Kromě toho Contoso chcete zmírnit jediný bod selhání na webové vrstvy.
**Omezení** | Aplikace se skládá z aplikace ASP.NET a WCF služby spuštěné na stejném virtuálním počítači. Chtějí se to rozdělit mezi dva webové aplikace s využitím služby Azure App Service. 
**Azure** | Chtějí se přesuňte aplikace do Azure, ale nechtějí ho spouštět na virtuálních počítačích. Které chtějí využít služby Azure PaaS pro úrovně web a data. 

## <a name="solution-design"></a>Návrh řešení

Po Připnutí dolů své cíle a požadavky, Contoso navrhuje, zkontrolujte nasazení řešení a identifikuje proces migrace, včetně služeb Azure, které budete používat pro migraci.

### <a name="current-app"></a>Aktuální aplikaci

- SmartHotel místní aplikace je rozvrstvená na dva virtuální počítače (WEBVM a SQLVM).
- Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5)
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Contoso má místní datacentrum (contoso-datacenter), s řadičem domény v místním (**contosodc1**).
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.


### <a name="proposed-solution"></a>Navržené řešení

- Pro databázovou vrstvu aplikace Contoso porovnání Azure SQL Database s použitím SQL serveru [v tomto článku](https://docs.microsoft.com/azure/sql-database/sql-database-features). Se rozhodli pro Azure SQL Database z několika důvodů:
    - Azure SQL Database je spravovaná služba relační databáze. Nabízí předvídatelný výkon na několika úrovních služeb, téměř bez nutnosti jakékoli správy. K výhodám patří dynamickou škálovatelnost bez výpadků, integrovanou inteligentní optimalizaci a globální škálovatelnost a dostupnost.
    - Zjednodušené Data Migration Assistant (DMA) k vyhodnocení a migrovat místní databázi Azure SQL, můžete využít.
    - S programem Software Assurance si vyměňují svoje stávající licence pro zlevněné sazby pro SQL Database, pomocí zvýhodněné hybridní využití Azure pro SQL Server. To může poskytovat úspory až 30 %.
    - SQL Database poskytuje mnoho funkcí zabezpečení, včetně maskování neustálé šifrování, dynamických dat a detekce ohrožení/zabezpečení na úrovni řádků.
- Na úrovni webové aplikace že jste se rozhodli používat službu Azure App Service. Tato služba PaaS umožňuje, aby nasazení aplikace s pár změn konfigurace. Budete pomocí sady Visual Studio proveďte požadovanou změnu a nasaďte dvě webové aplikace. Jeden pro web a jeden pro službu WCF.
  
### <a name="solution-review"></a>Kontrola řešení
Contoso vyhodnotí své navrhované návrhu společně vložením seznam výhody a nevýhody.

**Posouzení** | **Podrobnosti**
--- | ---
**V oblasti IT** | Kód aplikace SmartHotel nebudete muset změnit pro migraci do Azure.<br/><br/> Můžete využít své investice do Software Assurance využitím zvýhodněné hybridní využití Azure pro SQL Server a Windows Server.<br/><br/> Po dokončení migrace už musí podporovat Windows Server 2008 R2. [Další informace](https://support.microsoft.com/lifecycle).<br/><br/> Konfigurace na úrovni webové aplikace s více instancemi, tak, aby se už jediný bod selhání.<br/><br/> Už budou závislé na stárnutí SQL Server 2008 R2.<br/><br/> SQL Database podporuje technické požadavky společnosti Contoso. Tyto posouzení místní databáze pomocí Pomocníka s migrací databáze a zjistí, že je kompatibilní.<br/><br/> SQL Database nemá předdefinovanou odolnost proti chybám, Contoso nepotřebujete nastavit. Tím se zajistí, že datová vrstva již není jediným bodem převzetí služeb při selhání.
**Nevýhody** | Služba Azure App Services podporuje pouze jedno nasazení aplikace pro každou webovou aplikaci. To znamená, že dvě webové aplikace musí být zřízená (jeden pro web) a jeden pro službu WCF.<br/><br/> Pokud používají nástroj Data Migration Assistant namísto služby migrace dat k migraci jejich databáze, nebude mít Contoso infrastruktury připravené pro migraci databází ve velkém měřítku. Budete muset sestavit jiné oblasti zajistit převzetí služeb při selhání, pokud není k dispozici primární oblasti.

## <a name="proposed-architecture"></a>Navrhované architektury

![Architektura scénáře](media/contoso-migration-refactor-web-app-sql/architecture.png) 


### <a name="migration-process"></a>Proces migrace

1. Contoso zřídit instanci Azure SQL a migraci databáze SmartHotel k němu.
2. Jejich zřízení a konfigurace webové aplikace a nasazení aplikace SmartHotel k nim.

    ![Proces migrace](media/contoso-migration-refactor-web-app-sql/migration-process.png) 

### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Přímý přístup do paměti, budete používat k vyhodnocení a zjištění problémů s kompatibilitou, které může mít vliv na jejich fungování databáze v Azure. DMA vyhodnocuje paritu funkcí mezi SQL zdroje a cíle a doporučuje vylepšení výkonu a spolehlivosti. | Tento nástroj je zdarma ke stažení.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Inteligentní, plně spravovaná relační Cloudová databázová služba. | Náklady na základě funkcí, propustnosti a velikosti. [Další informace](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Azure App Service – Web Apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview) | Vytváření výkonných cloudových aplikací s využitím plně spravované platformy | Náklady podle doby trvání velikost, umístění a využití. [Další informace](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Požadavky

Zde je můžete vy (a Contoso) potřebovat ke spuštění tohoto scénáře:

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Měli jste už vytvořili odběr při provádění posouzení v první článku v této sérii. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.
**Infrastruktura Azure** | [Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastavení infrastruktury Azure.


## <a name="scenario-steps"></a>Kroky scénáře

Tady je způsob spuštění migrace Contoso:

> [!div class="checklist"]
> * **Krok 1: Zřízení instance SQL Database v Azure**: Contoso zřídí instanci SQL v Azure. Poté, co je web aplikace migraci do Azure, webové aplikace služby WCF bude odkazovat na tuto instanci.
> * **Krok 2: Migrace databáze pomocí DMA**: se migrovat databázi aplikace pomocí Pomocníka s migrací databáze.
> * **Krok 3: Zřízení webové aplikace**: zřizování dvě webové aplikace.
> * **Krok 4: Konfigurace připojovacích řetězců**: nakonfigurování připojovacích řetězců, aby mohla komunikovat na úroveň webovou aplikaci, webové aplikace služby WCF a SQL instance.
> * **Krok 5: Publikování webových aplikací**: Contoso v posledním kroku, publikuje aplikace do Azure.


## <a name="step-1-provision-an-azure-sql-database"></a>Krok 1: Zřízení služby Azure SQL Database

1. Výběrem k vytvoření databáze SQL v Azure. 

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

Contoso provede migraci SmartHotel database pomocí DMA.

### <a name="install-dma"></a>Instalace DMA

1. Stažení nástroje [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) k virtuálnímu počítači v místním SQL serveru (**SQLVM**).
2. Na virtuálním počítači, spusťte instalační program (DownloadMigrationAssistant.msi).
3. Na **Dokončit** stránky, vyberou **spusťte Microsoft Data Migration Assistant** před dokončením průvodce.

### <a name="migrate-the-database-with-dma"></a>Migrace databáze pomocí DMA

1. V DMA vytvořte nový projekt (**SmartHotelDB**) a vyberte **migrace** 
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

9. Po dokončení migrace můžete ověřit, že databáze je spuštěna v instanci Azure SQL společnosti Contoso.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-8.png)

10. Jejich odstranění navíc databáze SQL **SmartHotel.Registration** na webu Azure Portal.

    ![DMA](media/contoso-migration-refactor-web-app-sql/dma-9.png)


## <a name="step-3-provision-web-apps"></a>Krok 3: Zřízení webové aplikace

S databází migrovat, společnost Contoso může nyní zřízení dvě webové aplikace.

1. Vyberou **webovou aplikaci** na portálu.

    ![Webová aplikace](media/contoso-migration-refactor-web-app-sql/web-app1.png)

2. Poskytuje název aplikace (**SHWEB EUS2**), spusťte ho na Windows a umístěte ho zrušit provozní skupině pro prostředky **ContosoRG**. Vytvoří novou službu app service a plán.

    ![Webová aplikace](media/contoso-migration-refactor-web-app-sql/web-app2.png)

3. Po zřízení webové aplikace, opakujte postup vytvoření webové aplikace služby WCF (**SHWCF EUS2**)

    ![Webová aplikace](media/contoso-migration-refactor-web-app-sql/web-app3.png)

4. Po jejich dokončení, přejděte na adresu aplikací ke kontrole, že jste byla úspěšně vytvořena.

## <a name="step-4-configure-connection-strings"></a>Krok 4: Konfigurace připojovacích řetězců

Contoso je potřeba Ujistěte se, že webové aplikace a databáze můžete všechny komunikovat. Provedete to tak, že konfigurace připojovacích řetězců v kódu a ve službě web apps.

1. Ve webové aplikaci pro službu WCF (**SHWCF EUS2**) > **nastavení** > **nastavení aplikace**, se přidat nový připojovací řetězec s názvem  **Možnost DefaultConnection**.
2. Připojovací řetězec se použije **SmartHotel registrace** databáze a je třeba aktualizovat se správnými přihlašovacími údaji.

    ![Připojovací řetězec](media/contoso-migration-refactor-web-app-sql/string1.png)

3. Pomocí sady Visual Studio, otevře soubor řešení od společnosti Contoso **SmartHotel360-interní rezervace apps** složky. **ConnectionStrings** část souboru web.config pro službu WCF SmartHotel.Registration.Wcf je třeba aktualizovat připojovací řetězec.

     ![Připojovací řetězec](media/contoso-migration-refactor-web-app-sql/string2.png)

4. **Klienta** část souboru web.config SmartHotel.Registration.Web by měl být změněn tak, aby odkazoval na nové umístění služby WCF. Toto je adresa URL webové aplikace WCF, který je hostitelem koncového bodu služby.

    ![Připojovací řetězec](media/contoso-migration-refactor-web-app-sql/strings3.png)


## <a name="step-5-publish-web-apps"></a>Krok 5: Publikování webových aplikací

V posledním kroku publikuje Contoso webové aplikace do Azure.

1. V sadě Visual Studio, klikněte pravým tlačítkem na projekt SmartHotel.REgistration.Wcf > **publikovat**.

    ![Publikování](media/contoso-migration-refactor-web-app-sql/publish-web1.png)

2. Spuštění publikování.

    ![Publikování](media/contoso-migration-refactor-web-app-sql/publish-web2.png)

3. Mohou vybrat stávající službu App Service, protože jste už vytvořili webovou aplikaci.

    ![Publikování](media/contoso-migration-refactor-web-app-sql/publish-web3.png)

4. Po výběru aplikace WCF, Visual Studio ho nasadí.

    ![Publikování](media/contoso-migration-refactor-web-app-sql/publish-web4.png)

5. Jsou následně zopakovat tento proces publikování webové aplikace – SmartHotel.Registration.Web.

    ![Publikování](media/contoso-migration-refactor-web-app-sql/publish-web5.png)


Aplikace je v tuto chvíli úspěšné migraci do Azure.

## <a name="clean-up-after-migration"></a>Vyčištění po migraci

Po dokončení migrace Contoso potřebuje k dokončení těchto kroků vyčištění:  

- Odebrání místních virtuálních počítačů z inventáře vCenter.
- Odeberte virtuální počítače z místní úlohy zálohování.
- Aktualizujte interní dokumentaci zobrazíte nové umístění pro SmartHotel aplikace. Zobrazit databáze jako spuštěná v Azure SQL database a front-endu jako spuštěná dvě webové aplikace.
- Zkontrolujte všechny prostředky, které pracují s Vyřazená z provozu virtuálních počítačů a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.


## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Contoso s migrovaných prostředků v Azure, musí plně zprovoznění a zabezpečení jejich nové infrastruktury.

### <a name="security"></a>Zabezpečení

- Contoso je nutné zajistit, aby jejich nové **SmartHotel registrace** databáze je zabezpečená. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Konkrétně, by měl aktualizovat webové aplikace pro použití protokolu SSL s certifikáty.

### <a name="backups"></a>Zálohování

- Contoso je potřeba zkontrolovat požadavkům zálohování pro Azure SQL Database. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Jejich měli uvažovat o implementaci skupiny převzetí služeb při selhání pro zajištění regionální převzetí služeb při selhání pro databázi. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso je potřeba zvážit nasazení webové aplikace v hlavní oblasti východní USA 2 a oblast USA (střed) pro odolnost. Může se konfigurace služby Traffic Manager zajistit převzetí služeb při selhání v případě místních výpadků.

### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

- Po nasazení jsou všechny prostředky, by měl Contoso přiřadit Azure značky na základě jejich [plánování infrastruktury](contoso-migration-infrastructure.md#set-up-tagging).
- Všechny licence je integrovaná do náklady na služby PaaS, které Contoso využívá. To se odečte od smlouvy EA.
1. Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení správy nákladů multicloudové, který vám umožní využívat a spravovat Azure a další cloudové prostředky.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management. 

## <a name="conclusion"></a>Závěr

V tomto článku se Refaktorovat Contoso SmartHotel aplikace ve službě Azure migrací aplikace front-endový virtuální počítač na dvě webové aplikace Azure. Proto se migrovat databázi aplikace ke službě Azure SQL database.






