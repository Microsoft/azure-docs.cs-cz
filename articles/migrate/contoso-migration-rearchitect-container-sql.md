---
title: Změna architektury aplikace Contoso v kontejneru Azure a Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso úprava architektury aplikace v kontejnerech Windows Azure a Azure SQL Database.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: raynew
ms.openlocfilehash: 733a93d0fc80d86d28f13a9e1d32108b58893bf0
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "42055435"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Migrace Contoso: Úprava architektury místní aplikace do kontejneru Azure a Azure SQL Database

Tento článek ukazuje, jak společnosti Contoso migruje a změna architektury aplikace jejich SmartHotel v Azure. Jsou aplikace front-endový virtuální počítač migrovat do kontejneru Windows Azure a databáze aplikace ke službě Azure SQL database.

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
[Článek 9: Refaktorujte aplikace do webové aplikace Azure a Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Ukazuje, jak společnosti Contoso migruje SmartHotel aplikace do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure | K dispozici.
[Článek 10: Refaktorujte Linuxovou aplikaci do Azure Web Apps a Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Ukazuje, jak společnosti Contoso migruje aplikace osTicket Linux do Azure Web Apps ve více lokalitách, integrovaná se službou GitHub pro průběžné doručování. Jejich migrovat databázi aplikace na instanci Azure MySQL. | K dispozici.
[Článek 11: Refaktorovat TFS na VSTS](contoso-migration-tfs-vsts.md) | Ukazuje, jak společnosti Contoso migruje svoje místní nasazení Team Foundation Server (TFS) a migrujte jej na Visual Studio Team Services (VSTS) v Azure.
Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database | Ukazuje, jak společnosti Contoso migruje a rearchitects jeho SmartHotel aplikace do Azure. Jejich úprava architektury webové vrstvy aplikace jako kontejner Windows a databáze aplikace ve službě Azure SQL Database. | V tomto článku
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Ukazuje, jak společnosti Contoso znovu sestavte své aplikace SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služeb App Services, Kubernetes v Azure, Azure Functions, Cognitive services a Cosmos DB. | K dispozici.

V tomto článku se migruje Contoso dvouvrstvé Windows. NET SmartHotel aplikace běžící na virtuálních počítačích VMware do Azure. Pokud chcete používat tuto aplikaci, se poskytuje jako open source a můžete ji stáhnout [Githubu](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co chtějí dosáhnout s tato migrace:

- **Adresa obchodní růst**: Contoso roste a kvůli tomu je tlak na svých místních systémů a infrastruktury.
- **Zvýšení efektivity**: Contoso musí odebrat nepotřebné postupy a zjednodušte procesy pro vývojáře a uživatele.  Obchodní potřeby IT bude rychlé a ne produkovaného odpadu čas nebo peníze, tím rychlejší doručování na požadavky zákazníků.
- **Zvýšení flexibility**: Contoso IT je potřeba se více přizpůsobovat podle potřeb firmy. Musí být schopné reagovat rychleji než změn na webu Marketplace, aby úspěch v globální ekonomiku.  Nesmí získat způsobem nebo stát obchodní blokování.
- **Škálování**: růstem podniku zvládla úspěšně, musíte zadat Contoso IT systémy, které mohou rozšířit stejným tempem.
- **Náklady na**: Contoso chce minimalizovat náklady na licencování.

## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou dolů cíle pro tuto migraci. Tyto cíle se používá k určení nejlepší metody migrace.

**Cíle** | **Podrobnosti**
--- | --- 
**Aplikace na systém** | Aplikace ve službě Azure zůstane tak kritický, protože se ještě dnes.<br/><br/> Měl by mít stejné možnosti výkonu, stejně jako aktuálně ve službě VMWare.<br/><br/> Chtějí se zastavit podporu systému Windows Server 2008 R2, na kterém aplikace aktuálně běží a jste ochotni investovat do aplikace.<br/><br/> Chtějí se přesunout mimo systém SQL Server 2008 R2 na moderní platformě databáze PaaS, což minimalizuje potřebu správy.<br/><br/> Contoso má využívat své investice do licencí SQL serveru se Software Assurance, kde je to možné.<br/><br/> Chtějí mít možnost vertikálně navýšit kapacitu vrstvy webové aplikace.
**Omezení** | Aplikace se skládá z aplikace ASP.NET a WCF služby spuštěné na stejném virtuálním počítači. Chtějí se to rozdělit mezi dva webové aplikace s využitím služby Azure App Service. 
**Azure na systém** | Chtějí se přesuňte aplikace do Azure a spuštění v kontejneru do prodloužit životnost aplikace. Nechtějí začněte úplně od začátku implementace aplikace v Azure. 

## <a name="solution-design"></a>Návrh řešení

Po Připnutí dolů své cíle a požadavky, Contoso navrhuje, zkontrolujte nasazení řešení a identifikuje proces migrace, včetně služeb Azure, které budete používat pro migraci.

### <a name="current-app"></a>Aktuální aplikaci

- SmartHotel místní aplikace je rozvrstvená na dva virtuální počítače (WEBVM a SQLVM).
- Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5)
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Contoso má místní datacentrum (contoso-datacenter), s řadičem domény v místním (**contosodc1**).
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.


### <a name="proposed-architecture"></a>Navrhované architektury

- Pro databázovou vrstvu aplikace Contoso porovnání Azure SQL Database s použitím SQL serveru [v tomto článku](https://docs.microsoft.com/azure/sql-database/sql-database-features). Se rozhodli pro Azure SQL Database z několika důvodů:
    - Azure SQL Database je spravovaná služba relační databáze. Nabízí předvídatelný výkon na několika úrovních služeb, téměř bez nutnosti jakékoli správy. K výhodám patří dynamickou škálovatelnost bez výpadků, integrovanou inteligentní optimalizaci a globální škálovatelnost a dostupnost.
    - Zjednodušené Data Migration Assistant (DMA) k vyhodnocení a migrovat místní databázi Azure SQL, můžete využít.
    - S programem Software Assurance si vyměňují svoje stávající licence pro zlevněné sazby pro SQL Database, pomocí zvýhodněné hybridní využití Azure pro SQL Server. To může poskytovat úspory až 30 %.
    - SQL Database poskytuje mnoho funkcí zabezpečení, včetně maskování neustálé šifrování, dynamických dat a detekce ohrožení/zabezpečení na úrovni řádků.
- Na úrovni webové aplikace, že jste se rozhodli ji převést do kontejneru Windows pomocí sady Visual Studio.
    - Budete nasazovat aplikace pomocí Azure Service Fabric a vyžádejte si image kontejneru Windows z Azure Container Registry (ACR).
    - Prototyp pro rozšíření aplikace tak, aby zahrnovala analýzu subjektivního hodnocení bude možné implementovat jako jiné služby v Service Fabric, připojení ke službě Cosmos DB.  To se načíst informace z Tweety a zobrazí v aplikaci.

    ![Architektura scénáře](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Kontrola řešení
Contoso vyhodnotí své navrhované návrhu společně vložením seznam výhody a nevýhody.

**Posouzení** | **Podrobnosti**
--- | ---
**V oblasti IT** | Kód aplikace SmartHotel bude nutné změnit pro migraci do Azure Service Fabric. Úsilí je však minimální, pomocí nástroje Service Fabric SDK pro změny.<br/><br/> S přechodem do Service Fabric můžete začít vyvíjet mikroslužby přidat do aplikace rychle v čase bez ohrožení původní základu kódu.<br/><br/> Kontejnery Windows v obecných nabízí stejné výhody jako kontejnery. Pomáhají zvýšit flexibilitu, přenositelnost a ovládací prvek.<br/><br/> Můžete využít své investice do Software Assurance využitím zvýhodněné hybridní využití Azure pro SQL Server a Windows Server.<br/><br/> Po dokončení migrace už musí podporovat Windows Server 2008 R2. [Další informace](https://support.microsoft.com/lifecycle).<br/><br/> Konfigurace na úrovni webové aplikace s více instancemi, tak, aby se už jediný bod selhání.<br/><br/> Už budou závislé na stárnutí SQL Server 2008 R2.<br/><br/> SQL Database podporuje technické požadavky společnosti Contoso. Tyto posouzení místní databáze pomocí Pomocníka s migrací databáze a zjistí, že je kompatibilní.<br/><br/> SQL Database nemá předdefinovanou odolnost proti chybám, Contoso nepotřebujete nastavit. Tím se zajistí, že datová vrstva již není jediným bodem převzetí služeb při selhání.
**Nevýhody** | Kontejnery jsou složitější než jiné možnosti migrace. Osvojování na kontejnery můžou být problémy pro doménu Contoso.  Ale zavádí novou úroveň složitosti, která poskytuje spoustu vylepšení, přestože křivky.<br/><br/> Provozní tým ve společnosti Contoso potřeba rychle pochopit práci a podporu Azure, kontejnery a mikroslužby pro aplikaci.<br/><br/> Pokud používají nástroj Data Migration Assistant namísto služby migrace dat k migraci jejich databáze, nebude mít Contoso infrastruktury připravené pro migraci databází ve velkém měřítku.



### <a name="migration-process"></a>Proces migrace

1. Contoso zřídit cluster Azure service fabric pro Windows.
2. Zřídit instanci Azure SQL a migraci databáze SmartHotel k němu.
3. Převodu virtuálního počítače webové vrstvy do kontejneru Docker pomocí nástroje Service Fabric SDK.
4. Připojit service fabric cluster a ACR a nasazení aplikace pomocí Azure service fabric.

    ![Proces migrace](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Přímý přístup do paměti, budete používat k vyhodnocení a zjištění problémů s kompatibilitou, které může mít vliv na jejich fungování databáze v Azure. DMA vyhodnocuje paritu funkcí mezi SQL zdroje a cíle a doporučuje vylepšení výkonu a spolehlivosti. | Tento nástroj je zdarma ke stažení.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Inteligentní, plně spravovaná relační Cloudová databázová služba. | Náklady na základě funkcí, propustnosti a velikosti. [Další informace](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Store imagí pro všechny typy kontejnerových nasazení. | Náklady podle funkce, úložiště a doby trvání využití. [Další informace](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Vytvářejte a provozujte vždy na, škálovatelné a distribuované aplikace | Náklady podle velikosti, umístění a doby trvání výpočetních uzlů. [Další informace](https://azure.microsoft.com/pricing/details/service-fabric/).

## <a name="prerequisites"></a>Požadavky

Zde je můžete vy (a Contoso) potřebovat ke spuštění tohoto scénáře:

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Měli jste už vytvořili odběr při provádění posouzení v první článku v této sérii. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.
**Infrastruktura Azure** | [Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastavení infrastruktury Azure.
**Požadavky pro vývojáře** | Contoso potřebuje následující nástroje na pracovní stanici pro vývojáře:<br/><br/> - [Visual Studio 2017 Community Edition: Verze 15.5](https://www.visualstudio.com/)<br/><br/> – .NET povolenou sadu funkcí.<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [V Service Fabric SDK 3.0 nebo novější](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Docker CE (Windows 10) nebo Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) nastavit používání kontejnerů Windows.



## <a name="scenario-steps"></a>Kroky scénáře

Tady je způsob spuštění migrace Contoso:

> [!div class="checklist"]
> * **Krok 1: Zřízení instance SQL Database v Azure**: Contoso zřídí instanci SQL v Azure. Po migraci virtuálních počítačů do Azure container webová front-endu bude odkazovat instanci kontejneru s front-endové webové aplikace k této databázi.
> * **Krok 2: Zřízení Azure Service Fabric**: zřizování clusteru Service Fabric.
> * **Krok 4: Migrace databáze pomocí DMA**: se migrovat databázi aplikace pomocí Pomocníka s migrací databáze.
> * **Krok 5: Převod aplikace do kontejneru**: převodu aplikace na kontejner pomocí sady Visual Studio a SDK Tools.
> * **Krok 6: Publikování aplikace**: publikování aplikace do služby ACR a Service Fabric Cluster.
> * **Krok 7: Rozšíření aplikace**: po aplikace je veřejný, že ji rozšířit, aby využít možnosti Azure a znovu ji publikovat do Azure.



## <a name="step-1-provision-an-azure-sql-database"></a>Krok 1: Zřízení služby Azure SQL Database

1. Výběrem k vytvoření databáze SQL v Azure. 

    ![Zřízení SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Zadejte název databáze tak, aby odpovídaly databáze na místním virtuálním počítači běží (**SmartHotel.Registration**). Ve skupině prostředků ContosoRG, umístěte databázi. Toto je skupina prostředků, které používají pro produkční prostředky v Azure.

    ![Zřízení SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Nastavené nové instanci SQL serveru (**sql. smarthotel eus2**) v primární oblasti.

    ![Zřízení SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Nastavují cenové úrovně tak, aby odpovídaly svůj server a databázové požadavky. A vyberou s programem Azure Hybrid Benefit ušetříte peníze, protože už mají licence SQL serveru.
5. K určení velikosti používají podle jader v nákupu a nastavte limity pro jejich očekávaný požadavky.

    ![Zřízení SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. Potom vytvoří instanci databáze.

    ![Zřízení SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. Po vytvoření instance otevřít databázi a poznamenejte si podrobnosti, které potřebují při použití asistované migraci databáze pro migraci.

    ![Zřízení SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**Potřebujete další pomoc?**

- [Získejte pomoc](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) Zřizuje se databáze SQL.
- [Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) omezení prostředků virtuálních jader.



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>Krok 2: Vytvoření ACR a zřízení kontejneru Azure

Azure container se vytvoří pomocí exportované soubory z virtuálního počítače Web. Kontejner je umístěno v Azure Container Registry (ACR).


1. Contoso vytvoří registr kontejnerů na webu Azure Portal.

     ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Poskytuje název registru (**contosoacreus2**) a umístěte ho do primární oblasti, ve skupině prostředků, které se používají pro své prostředky infrastruktury. Povolit přístup pro uživatele správce a nastavte ji jako SKU úrovně premium, takže mohou využít geografickou replikaci.

    ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>Krok 3: Zřízení Azure Service Fabric

Kontejner SmartHotel poběží v Azure Service Fabric Sluster. Contoso vytvoří Service Fabric Cluster následujícím způsobem:

1. Vytvoření prostředku Service Fabric v Azure Marketplace

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. V **základní**, poskytují jedinečné DS název clusteru a pověření pro přístup k místní virtuální počítač. Jejich umístit prostředku v provozní skupině prostředků (**ContosoRG**) v primární oblasti USA – východ 2.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. V **konfigurace typu uzlu**, jejich vstupní název typu uzlu, nastavení odolnosti, velikost virtuálního počítače a koncových bodů aplikace.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. V **vytvořit trezor klíčů**, vytvářejí nové služby key vault ve své skupině prostředků infrastruktury, k umístění certifikátu.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. V **zásady přístupu** jejich eanble přístup k virtuálním počítačům k nasazení trezoru klíčů.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric5.png) 

6. Zadejte název certifikátu.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric6.png) 

7. Na stránce souhrnu, zkopírujte odkaz, který se používá ke stažení certifikátu. Musí to pro připojení ke clusteru Service Fabric.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric7.png) 

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric8.png) 

8. Jakmile úspěšně proběhne ověření, zřizování clusteru.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric9.png) 

9. V Průvodci importem certifikátu, importujte stažený certifikát do vývojových počítačích. Certifikát se používá k ověření clusteru.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric10.png) 

10. Po zřízení clusteru připojí ke clusteru Service Fabric Exploreru.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric11.png) 

11. Je třeba vybrat správný certifikát.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric12.png) 

12. Service Fabric Explorer zatížení a správce společnosti Contoso mohou spravovat cluster.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric13.png) 


## <a name="step-3-migrate-the-database-with-dma"></a>Krok 3: Migrace databáze pomocí DMA

Contoso provede migraci SmartHotel database pomocí DMA.

### <a name="install-dma"></a>Instalace DMA

1. Stažení nástroje [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) k virtuálnímu počítači v místním SQL serveru (**SQLVM**).
2. Na virtuálním počítači, spusťte instalační program (DownloadMigrationAssistant.msi).
3. Na **Dokončit** stránky, vyberou **spusťte Microsoft Data Migration Assistant** před dokončením průvodce.

### <a name="configure-the-firewall"></a>Konfigurace brány firewall

Pro připojení ke službě Azure SQL Database, pravidlo brány firewall, je potřeba.

1. V **brány Firewall a virtuální sítě** vlastnosti pro databázi, povolit přístup ke službám Azure a přidejte pravidlo pro IP adresu klienta virtuálního počítače v místním SQL serverem.
2. Se vytvoří pravidlo brány firewall na úrovni serveru.

    ![Brána firewall](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

Potřebujete další pomoc?

[Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules) vytváření a Správa pravidel brány firewall pro službu Azure SQL Database.

### <a name="migrate"></a>Migrace

1. V DMA vytvořte nový projekt (**SmartHotelDB**) a vyberte **migrace** 
2. Typ zdrojového serveru jako vyberou **systému SQL Server**a cíl jako **Azure SQL Database**. 

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-1.png)

3. V podrobnostech migrace přidat **SQLVM** jako na zdrojovém serveru a **SmartHotel.Registration** databáze. 

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-2.png)

4. Obdrží chybu, která vypadá, že má být přidružena k ověřování. Po prozkoumání, ale tento problém je tečka (.) v názvu databáze. Alternativním řešením je se rozhodli zřídit novou databázi SQL pomocí názvu **SmartHotel registrace**, k vyřešení daného problému. Při spuštění DMA znovu, si mohou vybrat **SmartHotel registrace**a pokračujte v průvodci.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-3.png)

5. V **výběr objektů, které**, vyberte databázové tabulky a vygenerovat skript SQL.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-4.png)

6. Po DMS vytvoří skript, klepnutím na **nasadit schéma**.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-5.png)

7. DMA potvrdí, že nasazení proběhlo úspěšně.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-6.png)

8. Nyní spusťte migrace.

    ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-7.png)

9. Po dokončení migrace můžete ověřit, že databáze je spuštěna v instanci Azure SQL společnosti Contoso.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-8.png)

10. Jejich odstranění navíc databáze SQL **SmartHotel.Registration** na webu Azure Portal.

     ![DMA](./media/contoso-migration-rearchitect-container-sql/dma-9.png)



## <a name="step-4-convert-the-app-to-a-container"></a>Krok 4: Převod aplikace do kontejneru

Místní aplikace je tradiční aplikace tři vrstvy:

- Obsahuje webových formulářů a připojení k serveru SQL Server ve službě WCF.
- Používá Entity Framework pro integraci s daty ve službě SQL database bude vystavená prostřednictvím služby WCF.
- Aplikace webových formulářů komunikuje se službou WCF.

Contoso se převést aplikaci do kontejneru pomocí Visual Studio a SDK Tools následujícím způsobem:

1. Jejich naklonujte úložiště místně do počítače pro vývojáře:

    **klon gitu https://github.com/Microsoft/SmartHotel360-internal-booking-apps.git**

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container1.png)

2. Pomocí sady Visual Studio, v otevření souboru řešení (SmartHotel.Registration.sln) **SmartHotel360 – interní rezervace apps\src\Registration** adresáře z místního úložiště.  Jsou uvedeny dvě aplikace. Webový front-end SmartHotel.Registration.Web nad aplikace služby WCF SmartHotel.Registration.WCF.

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container2.png)


3. Pravým tlačítkem na webové aplikace > **přidat** > **podporu Orchestrátoru kontejnerů**.
4. V **přidat podporu kontejnerů orchestr**, vyberou **Service Fabric**.

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container3.png)

5. Contoso proces zopakuje pro SmartHotel.Registration.WCF aplikace.
6. Nyní Contoso kontroluje, jak byl změněn řešení.

    - Nová aplikace **SmartHotel.RegistrationApplication/**
    - Obsahuje dvě služby: **SmartHotel.Registration.WCF** a **SmartHotel.Registration.Web**.

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container4.png)

7. Visual Studio vytvoří soubor Docker a dali dolů požadované Image místně do počítače pro vývojáře.

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container5.png)

8. Soubor manifestu (**ServiceManifest.xml**) se vytvoří a otevřít v sadě Visual Studio. Tento soubor říká konfigurace kontejneru při nasazení do Azure Service Fabric.

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container6.png)

9. Jiný soubor manifestu (** ApplicationManifest.xml) obsahuje konfigurace aplikace pro kontejnery.

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container7.png)

## <a name="step-5-publish-the-app"></a>Krok 5: Publikování aplikace


Nakonec Contoso můžete publikování aplikace do služby ACR a Service Fabric Cluster.

> [!NOTE]
> V aplikaci SmartHotel byly provedeny nějaké změny související s clusterem Service Fabric. Můžete stáhnout kód aplikace původní a modernizované z [Githubu](https://github.com/Microsoft/SmartHotel360-internal-booking-apps). Je změněný soubor **AppliationModern/ApplicationParameters/Cloud.xml**.


1. V sadě Visual Studio update připojovací řetězec pro připojení aplikace ke službě Azure SQL Database. Připojovací řetězec můžete najít v databázi na webu Azure Portal.

    ![Publikování](./media/contoso-migration-rearchitect-container-sql/publish1.png)

2. Contoso publikuje aplikaci Service Fabric pomocí sady Visual Studio. Klikněte pravým tlačítkem na aplikaci Service Fabric > **publikovat**.

    ![Publikování](./media/contoso-migration-rearchitect-container-sql/publish2.png)

3. Výběrem předplatného, koncový bod připojení a služby ACR. Pak klikněte na tlačítko **publikovat**.

    ![Publikování](./media/contoso-migration-rearchitect-container-sql/publish3.png)

4. Po dokončení nasazení SmartHotel se teď měla být spuštěná Service Fabric.

    ![Publikování](./media/contoso-migration-rearchitect-container-sql/publish4.png)

5. Pro připojení k aplikaci, bude Contoso směrovat provoz na veřejnou IP adresu nástroje pro vyrovnávání zatížení Azure v před jejich uzlů Service Fabric.

    ![Publikování](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-6-extend-the-app-and-republish"></a>Krok 6: Rozšíření aplikace a opětovné publikování

Po spuštění SmartHotel aplikace a databáze v Azure Contoso chce rozšíření aplikace.

- Vývojáři společnosti Contoso se vytváření prototypů novou aplikaci .NET Core, která se spustí v clusteru Service Fabric.
- Aplikace se použije k vyžádání mínění dat z cosmos DB.
- Tato data budou v podobě Tweety, které jsou zpracovány pomocí funkci Azure functions a Cognitive Services API analýzy Text.

### <a name="provision-azure-cosmos-db"></a>Zřízení služby Azure Cosmos DB

Jako první krok zřídit Contoso databázi Azure Cosmos.

1. Vytvoří prostředek služby Azure Cosmos DB z Azure Marketplace.

    ![Rozšíření](./media/contoso-migration-rearchitect-container-sql/extend1.png)

2. Zadejte název databáze (**contososmarthotel**), vyberte SQL API a umístit na prostředek ve skupině prostředků produkčního prostředí, v primární oblasti USA – východ 2.

    ![Rozšíření](./media/contoso-migration-rearchitect-container-sql/extend2.png)

3. V **Začínáme**, vyberou **Průzkumník dat**a přidejte novou kolekci.
4. V **přidat kolekci** zadejte ID a nastavit kapacitu úložiště a propustnost.

    ![Rozšíření](./media/contoso-migration-rearchitect-container-sql/extend3.png)

5. Na portálu, otevřou se nová databáze > **kolekce** > **dokumenty** a klikněte na tlačítko **nový dokument**.
6. Následující kód JSON, vložte do okna dokumentu. Toto je ukázková data ve formě jednoho tweet.

    ```
    {
            "id": "2ed5e734-8034-bf3a-ac85-705b7713d911",
            "tweetId": 927750234331580911,
            "tweetUrl": "https://twitter.com/status/927750237331580911",
            "userName": "CoreySandersWA",
            "userAlias": "@CoreySandersWA",
            "userPictureUrl": "",
            "text": "This is a tweet about #SmartHotel",
            "language": "en",
            "sentiment": 0.5,
            "retweet_count": 1,
            "followers": 500, 
            "hashtags": [
                ""
            ]
    }
    ```

    ![Rozšíření](./media/contoso-migration-rearchitect-container-sql/extend4.png)

7. Jejich vyhledání koncového bodu služby Cosmos DB a ověřovací klíč. Ty se používají v aplikaci pro připojení ke kolekci. V databázi, kliknou **klíče**a zkopírujte identifikátor URI a primární klíč do poznámkového bloku.

    ![Rozšíření](./media/contoso-migration-rearchitect-container-sql/extend5.png)

### <a name="update-the-sentiment-app"></a>Aktualizace aplikace mínění

Pomocí služby Cosmos DB zřídili můžete nakonfigurovat Contoso aplikaci připojovat se k němu.

1. V sadě Visual Studio, otevřete soubor ApplicationModern\ApplicationParameters\cloud.xml v Průzkumníku řešení.

    ![Aplikace mínění](./media/contoso-migration-rearchitect-container-sql/sentiment1.png)

2. Vyplňte následující dva parametry:

   ```
   <Parameter Name="SentimentIntegration.CosmosDBEndpoint" Value="[URI]" />
   ```
   
   ```
   <Parameter Name="SentimentIntegration.CosmosDBAuthKey" Value="[Key]" />
   ```

    ![Aplikace mínění](./media/contoso-migration-rearchitect-container-sql/sentiment2.png)

### <a name="republish-the-app"></a>Znovu publikovat aplikaci

Po rozšíření aplikace Contoso znovu publikuje uzamkl ji do Azure.

1. Na portálu, klikněte pravým tlačítkem na aplikaci Service Fabric > **publikovat**.

    ![Opětovné publikování](./media/contoso-migration-rearchitect-container-sql/republish1.png)

2. Výběrem předplatného, koncový bod připojení a služby ACR. Pak klikněte na tlačítko **publikovat**.

    ![Opětovné publikování](./media/contoso-migration-rearchitect-container-sql/republish2.png)

4. Po dokončení nasazení SmartHotel se teď měla být spuštěná Service Fabric. Konzole pro správu prostředků infrastruktury Provisioning nyní zobrazuje tři služby.

    ![Opětovné publikování](./media/contoso-migration-rearchitect-container-sql/republish3.png)

5. Contoso se můžete proklikat služby zobrazíte, že je aplikace SentimentIntegration zprovoznění

    ![Opětovné publikování](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Vyčištění po migraci

Po dokončení migrace Contoso potřebuje k dokončení těchto kroků vyčištění:  

- Odebrání místních virtuálních počítačů z inventáře vCenter.
- Odeberte virtuální počítače z místní úlohy zálohování.
- Aktualizujte interní dokumentaci zobrazíte nové umístění pro SmartHotel aplikace. Zobrazit databáze jako spuštěná v Azure SQL database a front-endu jako spuštěná ve službě Service Fabric.
- Zkontrolujte všechny prostředky, které pracují s Vyřazená z provozu virtuálních počítačů a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.


## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Contoso s migrovaných prostředků v Azure, musí plně zprovoznění a zabezpečení jejich nové infrastruktury.

### <a name="security"></a>Zabezpečení

- Contoso je nutné zajistit, aby jejich nové **SmartHotel registrace** databáze je zabezpečená. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Konkrétně, by měl aktualizovat kontejner, aby používal SSL s certifikáty.
- Zvažte použití trezoru klíčů k ochraně tajných kódů pro své aplikace Service Fabric. [Další informace](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Zálohování

- Contoso je potřeba zkontrolovat požadavkům zálohování pro Azure SQL Database. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Jejich měli uvažovat o implementaci skupiny převzetí služeb při selhání pro zajištění regionální převzetí služeb při selhání pro databázi. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Můžete využít geografickou replikaci pro SKU úrovně premium služby ACR. [Další informace](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Contoso je nutné vzít v úvahu, jakmile je k dispozici Web App for Containers nasazení webové aplikace v hlavním východní USA 2 a oblast USA (střed). Může se konfigurace služby Traffic Manager zajistit převzetí služeb při selhání v případě místních výpadků.

### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

- Po nasazení jsou všechny prostředky, by měl Contoso přiřadit Azure značky na základě jejich [plánování infrastruktury](contoso-migration-infrastructure.md#set-up-tagging).
- Všechny licence je integrovaná do náklady na služby PaaS, které Contoso využívá. To se odečte od smlouvy EA.
1. Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení správy nákladů multicloudové, který vám umožní využívat a spravovat Azure a další cloudové prostředky.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management. 

## <a name="conclusion"></a>Závěr

V tomto článku se Refaktorovat Contoso SmartHotel aplikace v Azure a migrujte aplikace front-endový virtuální počítač do Service Fabric. Proto se migrovat databázi aplikace ke službě Azure SQL database.





