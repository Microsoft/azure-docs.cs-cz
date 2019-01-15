---
title: Změna architektury aplikace Contoso v kontejneru Azure a Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso úprava architektury aplikace v kontejnerech Windows Azure a Azure SQL Database.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: raynew
ms.openlocfilehash: 6f894310157432a6e03e6ec4753f5efc2d8ac66d
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54267415"
---
# <a name="contoso-migration-rearchitect-an-on-premises-app-to-an-azure-container-and-azure-sql-database"></a>Migrace Contoso: Úprava architektury místní aplikace do kontejneru Azure a Azure SQL Database

Tento článek ukazuje, jak společnosti Contoso migruje a změna architektury aplikace jeho SmartHotel360 v Azure. Contoso migraci aplikace front-endový virtuální počítač do kontejneru Windows Azure a databáze aplikace ke službě Azure SQL database.

Tento dokument je jednou z řady článků, které ukazují, jak fiktivní společnosti Contoso migraci místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a scénáře, které znázorňují nastavování infrastruktury migrace, posuzování migrace místních prostředků a spouštění různé druhy migrace. Scénáře jejich složitost v. Postupně přibudou další články.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Přehled řady článku, strategie migrace společnosti Contoso a ukázkové aplikace, které se používají v řadě. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Contoso připraví svoji místní infrastrukturu a jeho infrastruktury Azure pro migraci. Pro všechny články týkající se migrace z této série se používá stejnou infrastrukturu. | K dispozici.
[Článek 3: Posouzení místních prostředků pro migraci do Azure](contoso-migration-assessment.md)  | Contoso spuštění posouzení své místní aplikace SmartHotel360 běžící ve VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí služby Azure Migrate a databáze aplikace SQL Server pomocí Data Migration Assistant. | K dispozici.
[Článek 4: Změna hostitele aplikace na virtuálním počítači Azure a SQL Database, spravované Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso běží lift and shift migrace do Azure pro své místní aplikace SmartHotel360. Contoso migruje aplikace front-endového virtuálního počítače pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migraci databáze aplikace do Azure SQL Database Managed Instance pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.   
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Contoso migruje jeho aplikace SmartHotel360 virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici.
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a ve skupině dostupnosti AlwaysOn systému SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje aplikace SmartHotel360. Společnost Contoso využívá Site Recovery k migraci virtuálních počítačů aplikace. Jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn využívá službu Database Migration Service. | K dispozici. 
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Contoso dokončení migrace lift and shift aplikace osTicket Linux do virtuálních počítačů Azure pomocí Azure Site Recovery | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Azure Site Recovery a migraci databáze aplikace na instanci serveru Azure MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorovat aplikace na Azure Web Apps a Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Contoso migruje aplikace SmartHotel360 do webové aplikace Azure a migraci databáze aplikace do instance serveru SQL Azure pomocí Pomocníka s migrací databáze | K dispozici.
[Článek 10: Refaktorovat Linuxovou aplikaci na Azure Web Apps a Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Contoso migruje osTicket své Linuxové aplikace do webové aplikace Azure v několika oblastech Azure pomocí Azure Traffic Manageru, integrovaná se službou GitHub pro průběžné doručování. Contoso migraci databáze aplikace do Azure Database for MySQL – instance. | K dispozici. 
[Článek 11: Refaktorujte TFS na službách Azure DevOps](contoso-migration-tfs-vsts.md) | Contoso migrovat své místní nasazení serveru Team Foundation Server ke službám Azure DevOps v Azure. | K dispozici.
Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database | Contoso migruje jeho SmartHotel aplikace do Azure. Potom rearchitects webové vrstvy aplikace jako kontejner Windows se spuštěnou v Azure Service Fabric a databázi Azure SQL Database. | V tomto článku
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Contoso replikujícím svou aplikaci SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služby Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB. | K dispozici. 
[Článek 14: Škálování migrace do Azure](contoso-migration-scale.md) | Po vyzkoušení kombinace migrace, Contoso připraví škálovat na plné migraci do Azure. | K dispozici.

V tomto článku se migruje Contoso dvouvrstvé Windows WPF, aplikací SmartHotel360 forms XAML běžící na virtuálních počítačích VMware do Azure. Pokud chcete používat tuto aplikaci, se poskytuje jako open source a můžete ji stáhnout [Githubu](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Obchodní faktory

Vedení Contoso IT úzké spolupráci s obchodními partnery pochopit, co chtějí dosáhnout s tato migrace:

- **Adresa obchodní růst**: Contoso roste a kvůli tomu je tlak na svých místních systémů a infrastruktury.
- **Zvýšení efektivity**: Contoso je potřeba odebrat nepotřebné postupy a zjednodušte procesy pro vývojáře a uživatele.  Obchodní potřeby IT bude rychlé a ne produkovaného odpadu čas nebo peníze, tím rychlejší doručování na požadavky zákazníků.
- **Zvýšení flexibility**:  Contoso IT je potřeba se více přizpůsobovat podle potřeb firmy. Musí být schopné reagovat rychleji než změn na webu Marketplace, aby úspěch v globální ekonomiku.  Nesmí získat způsobem nebo stát obchodní blokování.
- **Škálování**: Růstem podniku zvládla úspěšně, musíte zadat Contoso IT systémy, které mohou rozšířit stejným tempem.
- **Náklady na**: Contoso chce minimalizovat náklady na licencování.

## <a name="migration-goals"></a>Cíle migrace

Tým cloudových Contoso má připnutou dolů cíle pro tuto migraci. Tyto cíle se používá k určení nejlepší metody migrace.

**Cíle** | **Podrobnosti**
--- | --- 
**Aplikace na systém** | Aplikace ve službě Azure zůstane tak kritický, protože se ještě dnes.<br/><br/> Měl by mít stejné možnosti výkonu, stejně jako aktuálně ve službě VMWare.<br/><br/> Contoso chce zastavit podporu systému Windows Server 2008 R2, na kterém aplikace aktuálně běží a jste ochotni investovat do aplikace.<br/><br/> Contoso chce přesunout mimo systém SQL Server 2008 R2 na moderní platformě databáze PaaS, což minimalizuje potřebu správy.<br/><br/> Contoso má využívat své investice do licencí SQL serveru se Software Assurance, kde je to možné.<br/><br/> Contoso chce mít možnost vertikálně navýšit kapacitu vrstvy webové aplikace.
**Omezení** | Aplikace se skládá z aplikace ASP.NET a WCF služby spuštěné na stejném virtuálním počítači. Contoso chce to rozdělit mezi dva webové aplikace s využitím služby Azure App Service. 
**Azure na systém** | Contoso chce přesuňte aplikace do Azure a spuštění v kontejneru do prodloužit životnost aplikace. Není vhodné začít úplně od začátku implementace aplikace v Azure. 
**DevOps** | Contoso chce přesunout do modelu DevOps pomocí služby Azure DevOps pro kód sestavení a kanál verze.

## <a name="solution-design"></a>Návrh řešení

Po Připnutí dolů cíle a požadavky, Contoso navrhuje, zkontrolujte nasazení řešení a identifikuje proces migrace, včetně služeb Azure, které Contoso použije pro migraci.

### <a name="current-app"></a>Aktuální aplikaci

- Místní aplikace SmartHotel360 je rozvrstvená na dva virtuální počítače (WEBVM a SQLVM).
- Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5)
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Contoso má místní datacentrum (contoso-datacenter), s řadičem domény v místním (**contosodc1**).
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.


### <a name="proposed-architecture"></a>Navrhované architektury

- Pro databázovou vrstvu aplikace Contoso porovnání Azure SQL Database s použitím SQL serveru [v tomto článku](https://docs.microsoft.com/azure/sql-database/sql-database-features). To rozhodnutí přejít k Azure SQL Database z několika důvodů:
    - Azure SQL Database je spravovaná služba relační databáze. Nabízí předvídatelný výkon na několika úrovních služeb, téměř bez nutnosti jakékoli správy. K výhodám patří dynamickou škálovatelnost bez výpadků, integrovanou inteligentní optimalizaci a globální škálovatelnost a dostupnost.
    - Contoso využívá jednoduchý Data Migration Assistant (DMA) vyhodnocovat a migrovat místní databázi Azure SQL.
    - S programem Software Assurance společnosti Contoso si mohou vyměňovat své stávající licence pro zlevněné sazby pro SQL Database, pomocí zvýhodněné hybridní využití Azure pro SQL Server. To může poskytovat úspory až 30 %.
    - SQL Database poskytuje mnoho funkcí zabezpečení, včetně maskování neustálé šifrování, dynamických dat a detekce ohrožení/zabezpečení na úrovni řádků.
- Pro webovou vrstvu aplikace Contoso rozhodl převést do kontejneru Windows pomocí služby Azure DevOps.
    - Contoso se nasazení aplikace pomocí Azure Service Fabric a vyžádejte si image kontejneru Windows z Azure Container Registry (ACR).
    - Prototyp pro rozšíření aplikace tak, aby zahrnovala analýzu subjektivního hodnocení bude možné implementovat jako jiné služby v Service Fabric, připojení ke službě Cosmos DB.  To se načíst informace z Tweety a zobrazí v aplikaci.
- K implementaci kanálu DevOps, Contoso použije Azure DevOps pro Správa zdrojového kódu (SCM), s úložišti Git.  Automatizované buildy a vydání se použije k sestavení kódu a nasazení do Azure Container Registry a Azure Service Fabric.

    ![Architektura scénáře](./media/contoso-migration-rearchitect-container-sql/architecture.png) 

  
### <a name="solution-review"></a>Kontrola řešení
Contoso vyhodnotí navrhovaných návrhu společně vložením seznam výhody a nevýhody.

**Posouzení** | **Podrobnosti**
--- | ---
**V oblasti IT** | Kód aplikace SmartHotel360 bude muset změnit pro migraci do Azure Service Fabric. Úsilí je však minimální, pomocí nástroje Service Fabric SDK pro změny.<br/><br/> S přechodem do Service Fabric můžete začít vyvíjet mikroslužby přidat do aplikace rychle v čase bez ohrožení původní základu kódu Contoso.<br/><br/> Kontejnery Windows v obecných nabízí stejné výhody jako kontejnery. Pomáhají zvýšit flexibilitu, přenositelnost a ovládací prvek.<br/><br/> Contoso můžou využívat své investice do Software Assurance využitím zvýhodněné hybridní využití Azure pro SQL Server a Windows Server.<br/><br/> Po dokončení migrace se už nemusí podporovat Windows Server 2008 R2. [Další informace](https://support.microsoft.com/lifecycle).<br/><br/> Contoso můžete nakonfigurovat na úrovni webové aplikace s více instancemi, tak, aby se už jediný bod selhání.<br/><br/> Už je závislá na stárnutí SQL Server 2008 R2.<br/><br/> SQL Database podporuje technické požadavky společnosti Contoso. Správce společnosti Contoso posouzení místní databáze pomocí Pomocníka s migrací databáze a počet nalezených kompatibilních.<br/><br/> SQL Database nemá předdefinovanou odolnost proti chybám, která Contoso nepotřebuje k nastavení. Tím se zajistí, že datová vrstva již není jediným bodem převzetí služeb při selhání.
**Nevýhody** | Kontejnery jsou složitější než jiné možnosti migrace. Osvojování na kontejnery můžou být problémy pro doménu Contoso.  Ale zavádí novou úroveň složitosti, která poskytuje spoustu vylepšení, přestože křivky.<br/><br/> Provozní tým ve společnosti Contoso potřeba rychle pochopit práci a podporu Azure, kontejnery a mikroslužby pro aplikaci.<br/><br/> Pokud společnost Contoso využívá nástroj Data Migration Assistant namísto služby migrace dat k migraci databáze, nebude mít infrastruktury připravené pro migraci databází ve velkém měřítku.



### <a name="migration-process"></a>Proces migrace

1. Contoso zřizuje cluster Azure service fabric pro Windows.
2. Zřídí instanci Azure SQL a migraci databáze SmartHotel360 k němu.
3. Contoso webové vrstvy virtuálního počítače převede do kontejneru Docker pomocí nástroje Service Fabric SDK.
4. Se připojí service fabric cluster a ACR a nasadí aplikaci pomocí Azure service fabric.

    ![Proces migrace](./media/contoso-migration-rearchitect-container-sql/migration-process.png) 

### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Database Migration Assistant (DMA)](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Vyhodnocuje a detekuje problémy s kompatibilitou, které můžou ovlivnit fungování databáze v Azure. DMA vyhodnocuje paritu funkcí mezi SQL zdroje a cíle a doporučuje vylepšení výkonu a spolehlivosti. | Tento nástroj je zdarma ke stažení.
[Azure SQL Database](https://azure.microsoft.com/services/sql-database/) | Inteligentní, plně spravovaná relační Cloudová databázová služba poskytuje. | Náklady na základě funkcí, propustnosti a velikosti. [Další informace](https://azure.microsoft.com/pricing/details/sql-database/managed/).
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Ukládá Image pro všechny typy kontejnerových nasazení. | Náklady podle funkce, úložiště a doby trvání využití. [Další informace](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure Service Fabric](https://azure.microsoft.com/services/service-fabric/) | Sestavení a provozovat neustále aktivní, škálovatelné a distribuované aplikace | Náklady podle velikosti, umístění a doby trvání výpočetních uzlů. [Další informace](https://azure.microsoft.com/pricing/details/service-fabric/).
[Azure DevOps](https://docs.microsoft.com/azure/azure-portal/tutorial-azureportal-devops) | Poskytuje průběžné integrace a průběžného nasazování (CI/CD) kanálů pro vývoj aplikací. Kanál začíná úložiště Git pro správu kód aplikace, systém sestavení pro vytváření balíčků a další artefakty sestavení a systém správy vydaných verzí nasazení změny do vývojových, testovacích a produkčních prostředích.

## <a name="prerequisites"></a>Požadavky

Zde je, co Contoso potřebuje ke spuštění tohoto scénáře:

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Společnosti Contoso vytvořit odběry dříve v této sérii článku. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.
**Infrastruktura Azure** | [Zjistěte, jak](contoso-migration-infrastructure.md) Contoso si dříve nastavil infrastruktury Azure.
**Požadavky pro vývojáře** | Contoso potřebuje následující nástroje na pracovní stanici pro vývojáře:<br/><br/> - [Visual Studio 2017 Community Edition: Verze 15.5](https://www.visualstudio.com/)<br/><br/> – .NET povolenou sadu funkcí.<br/><br/> - [Git](https://git-scm.com/)<br/><br/> - [V Service Fabric SDK 3.0 nebo novější](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started)<br/><br/> - [Docker CE (Windows 10) nebo Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) nastavit používání kontejnerů Windows.



## <a name="scenario-steps"></a>Kroky scénáře

Tady je vykonávání migrace Contoso:

> [!div class="checklist"]
> * **Krok 1: Zřízení instance SQL Database v Azure**: Contoso zřídí instanci SQL v Azure. Po migraci virtuálních počítačů do Azure container webová front-endu bude odkazovat instanci kontejneru s front-endové webové aplikace k této databázi.
> * **Krok 2: Vytvoření služby Azure Container Registry (ACR)**: Contoso zřídí služby container registry organizace pro Image kontejneru dockeru.
> * **Krok 3: Zřízení Azure Service Fabric**: Zřizuje Service Fabric Cluster.
> * **Krok 4: Spravovat certifikáty služby service fabric**: Contoso nastaví certifikátů pro přístup ke službě Azure DevOps Services do clusteru.
> * **Krok 5: Migrace databáze pomocí DMA**: Migrace databáze aplikace pomocí Pomocníka s migrací databáze.
> * **Krok 6: Nastavení služby Azure DevOps**: Contoso nastaví nový projekt ve službách Azure DevOps a importuje kód do úložiště Git.
> * **Krok 7: Převést aplikaci**: Contoso převede aplikace do kontejneru pomocí nástrojů Azure DevOps a sady SDK.
> * **Krok 8: Nastavení sestavení a vydaná verze**: Contoso nastaví kanály sestavení a vydaných verzí pro vytvoření a publikování aplikace do služby ACR a Service Fabric Cluster.
> * **Krok 9: Rozšíření aplikace**: Po veřejné aplikace Contoso rozšiřuje jej využít možnosti Azure a znovu publikuje uzamkl do Azure s využitím kanálu.



## <a name="step-1-provision-an-azure-sql-database"></a>Krok 1: Zřízení databáze služby Azure SQL Database

Správce společnosti Contoso zřídit službu Azure SQL database.

1. Výběrem k vytvoření **SQL Database** v Azure. 

    ![Provision SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql1.png)

2. Zadejte název databáze tak, aby odpovídaly databáze na místním virtuálním počítači běží (**SmartHotel.Registration**). Ve skupině prostředků ContosoRG, umístěte databázi. Toto je skupina prostředků, které používají pro produkční prostředky v Azure.

    ![Provision SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql2.png)

3. Nastavené nové instanci SQL serveru (**sql. smarthotel eus2**) v primární oblasti.

    ![Provision SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql3.png)

4. Nastavují cenovou úroveň na aktuálních požadavků na server a databáze. A vyberou s programem Azure Hybrid Benefit ušetříte peníze, protože už mají licence SQL serveru.
5. K určení velikosti používají podle jader v nákupu a nastavte limity pro očekávané požadavky.

    ![Provision SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql4.png)

6. Potom vytvoří instanci databáze.

    ![Provision SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql5.png)

7. Po vytvoření instance otevřít databázi a poznamenejte si podrobnosti, které potřebují při použití asistované migraci databáze pro migraci.

    ![Provision SQL](./media/contoso-migration-rearchitect-container-sql/provision-sql6.png)


**Potřebujete další pomoc?**

- [Získejte pomoc](https://docs.microsoft.com/azure/sql-database/sql-database-get-started-portal) Zřizuje se databáze SQL.
- [Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) omezení prostředků virtuálních jader.



## <a name="step-2-create-an-acr-and-provision-an-azure-container"></a>Krok 2: Vytvoření ACR a zřízení kontejneru Azure

Azure container se vytvoří pomocí exportované soubory z virtuálního počítače Web. Kontejner je umístěno v Azure Container Registry (ACR).


1. Správce společnosti Contoso vytvoření registru kontejnerů na webu Azure Portal.

     ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry1.png)

2. Poskytuje název registru (**contosoacreus2**) a umístěte ho do primární oblasti, ve skupině prostředků, které se používají pro své prostředky infrastruktury. Povolit přístup pro uživatele správce a nastavte ji jako SKU úrovně premium, takže mohou využít geografickou replikaci.

    ![Container Registry](./media/contoso-migration-rearchitect-container-sql/container-registry2.png)  


## <a name="step-3-provision-azure-service-fabric"></a>Krok 3: Zřízení Azure Service Fabric

Kontejner SmartHotel360 poběží v Azure Service Fabric Sluster. Správce společnosti Contoso vytvořit Service Fabric Cluster následujícím způsobem:

1. Vytvoření prostředku Service Fabric v Azure Marketplace

     ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric1.png)

2. V **Základy**, poskytují jedinečné DS název clusteru a pověření pro přístup k místní virtuální počítač. Jejich umístit prostředku v provozní skupině prostředků (**ContosoRG**) v primární oblasti USA – východ 2.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric2.png) 

3. V **konfigurace typu uzlu**, jejich vstupní název typu uzlu, nastavení odolnosti, velikost virtuálního počítače a koncových bodů aplikace.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric3.png) 


4. V **vytvořit trezor klíčů**, vytvářejí nové služby key vault ve své skupině prostředků infrastruktury, k umístění certifikátu.

    ![Service Fabric](./media/contoso-migration-rearchitect-container-sql/service-fabric4.png) 


5. V **zásady přístupu** umožňují přístup k virtuálním počítačům k nasazení trezoru klíčů.

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


## <a name="step-4-manage-service-fabric-certificates"></a>Krok 4: Správa certifikátů pro Service Fabric

Contoso potřebuje certifikáty clusteru umožňuje přístup ke službě Azure DevOps Services do clusteru. Správce společnosti Contoso toto nastavení.

1. Otevřete na webu Azure portal a přejděte do trezoru klíčů.
2. Otevřít certifikáty a zkopírujte kryptografický otisk certifikátu, který jste vytvořili během procesu zřizování.

    ![Zkopírujte kryptografický otisk](./media/contoso-migration-rearchitect-container-sql/cert1.png)
 
3. Zkopírujte ho do textového souboru pro pozdější použití.
4. Teď přidejte klientský certifikát, který se stane certifikát klienta pro správu, který je v clusteru. To umožňuje službám Azure DevOps pro připojení ke clusteru pro nasazení aplikace v kanál pro vydávání verzí. K jejich účelu otevřete trezor klíčů na portálu a vyberte **certifikáty** > **vygenerovat/importovat**.

    ![Vygenerujte klientský certifikát](./media/contoso-migration-rearchitect-container-sql/cert2.png)

5. Zadejte název certifikátu a zadejte rozlišující název X.509 v **subjektu**.

     ![Název certifikátu](./media/contoso-migration-rearchitect-container-sql/cert3.png)

6. Po vytvoření certifikátu, stáhnou se místně ve formátu PFX.

     ![Stáhnout certifikát](./media/contoso-migration-rearchitect-container-sql/cert4.png)

7. Teď se vraťte se do seznamu certifikátů trezor klíčů a zkopírujte kryptografický otisk klientského certifikátu, který je právě vytvořili. Ukládají se v textovém souboru.

     ![Kryptografický otisk certifikátu klienta](./media/contoso-migration-rearchitect-container-sql/cert5.png)

8. Pro nasazení služby Azure DevOps, které potřebují k určení hodnoty ve formátu Base64 z certifikátu. Je to na místní vývojářskou pracovní stanici, pomocí Powershellu. Výstup, vložte do textového souboru pro pozdější použití.

    ```
        [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("C:\path\to\certificate.pfx")) 
    ```

     ![Hodnota ve formátu Base64](./media/contoso-migration-rearchitect-container-sql/cert6.png)

9. Nakonec se přidat nový certifikát do clusteru Service Fabric. K tomu, na portálu otevřete cluster a klikněte na tlačítko **zabezpečení**.

     ![Přidat klientský certifikát](./media/contoso-migration-rearchitect-container-sql/cert7.png)

10. Kliknutím na **přidat** > **klient pro správu**a vložte kryptografický otisk nového certifikátu klienta. Potom kliknou **přidat**. To může trvat až 15 minut.

     ![Přidat klientský certifikát](./media/contoso-migration-rearchitect-container-sql/cert8.png)

## <a name="step-5-migrate-the-database-with-dma"></a>Krok 5: Migrace databáze pomocí DMA

Správce společnosti Contoso teď můžete migrovat SmartHotel360 database pomocí DMA.

### <a name="install-dma"></a>Instalace DMA

1. Stažení nástroje [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=53595) k virtuálnímu počítači v místním SQL serveru (**SQLVM**).
2. Na virtuálním počítači, spusťte instalační program (DownloadMigrationAssistant.msi).
3. Na **Dokončit** stránky, vyberou **spusťte Microsoft Data Migration Assistant** před dokončením průvodce.

### <a name="configure-the-firewall"></a>Konfigurace brány firewall

Pro připojení k Azure SQL Database, nastavit pravidlo brány firewall umožňující přístup správce společnosti Contoso.

1. V **brány Firewall a virtuální sítě** vlastnosti pro databázi, povolit přístup ke službám Azure a přidejte pravidlo pro IP adresu klienta virtuálního počítače v místním SQL serverem.
2. Se vytvoří pravidlo brány firewall na úrovni serveru.

    ![Brána firewall](./media/contoso-migration-rearchitect-container-sql/sql-firewall.png)

Potřebujete další pomoc?

[Další informace o](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#creating-and-managing-firewall-rules) vytváření a Správa pravidel brány firewall pro službu Azure SQL Database.

### <a name="migrate"></a>Migrace

Správce společnosti Contoso teď migrovat databázi.

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


## <a name="step-6-set-up-azure-devops-services"></a>Krok 6: Nastavení služby Azure DevOps

Contoso potřebuje pro sestavení infrastruktury DevOps a kanály pro aplikaci.  K tomuto účelu správce společnosti Contoso vytvořit nový projekt Azure DevOps, naimportujte svůj kód sestavení a vydávání kanálů.

1.   V účtu Contoso Azure DevOps, vytvořte nový projekt (**ContosoSmartHotelRearchitect**) a vyberte **Git** pro správu verzí.
![Nový projekt](./media/contoso-migration-rearchitect-container-sql/vsts1.png)

2. Jejich importovat úložiště Git, která nyní obsahuje jejich kód aplikace. Se [veřejného úložiště](https://github.com/Microsoft/SmartHotel360-internal-booking-apps) a můžete ji stáhnout.

    ![Stáhněte si kód aplikace](./media/contoso-migration-rearchitect-container-sql/vsts2.png)

3. Po importu kód, připojte se k úložišti aplikace Visual Studio a klonování kódu pomocí Průzkumníka týmových projektů.

4. Po naklonování úložiště do počítače pro vývojáře, otevřete soubor řešení pro aplikaci. Webové aplikace a wcf služby mají oddělení v rámci souboru projektu.

    ![Soubor řešení](./media/contoso-migration-rearchitect-container-sql/vsts4.png)

## <a name="step-7-convert-the-app-to-a-container"></a>Krok 7: Převod aplikace do kontejneru

Místní aplikace je tradiční aplikace tři vrstvy:

- Obsahuje webových formulářů a připojení k serveru SQL Server ve službě WCF.
- Používá Entity Framework pro integraci s daty ve službě SQL database bude vystavená prostřednictvím služby WCF.
- Aplikace webových formulářů komunikuje se službou WCF.

Správce společnosti Contoso se převést aplikaci do kontejneru pomocí Visual Studio a SDK Tools následujícím způsobem:


1. Pomocí sady Visual Studio, v kontrole otevřený soubor řešení (SmartHotel.Registration.sln) **SmartHotel360 – interní rezervace apps\src\Registration** adresáře z místního úložiště.  Jsou uvedeny dvě aplikace. Webový front-end SmartHotel.Registration.Web a aplikace služby WCF SmartHotel.Registration.WCF.

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container2.png)


2. Pravým tlačítkem na webové aplikace > **přidat** > **podporu Orchestrátoru kontejnerů**.
3. V **přidat podporu kontejnerů orchestr**, vyberou **Service Fabric**.

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container3.png)
    
4. Opakujte proces pro SmartHotel.Registration.WCF aplikace.
5. Teď, zkontrolujte, jak byl změněn řešení.

    - Nová aplikace **SmartHotel.RegistrationApplication/**
    - Obsahuje dvě služby: **SmartHotel.Registration.WCF** a **SmartHotel.Registration.Web**.

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container4.png)

6. Visual Studio vytvoří soubor Docker a dali dolů požadované Image místně do počítače pro vývojáře.

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container5.png)

7. Soubor manifestu (**ServiceManifest.xml**) se vytvoří a otevřít v sadě Visual Studio. Tento soubor říká konfigurace kontejneru při nasazení do Azure Service Fabric.

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container6.png)

8. Jiný soubor manifestu (** ApplicationManifest.xml) obsahuje konfigurace aplikace pro kontejnery.

    ![Kontejner](./media/contoso-migration-rearchitect-container-sql/container7.png)

9. Otevřou **ApplicationParameters/Cloud.xml** souborů a aktualizovat připojovací řetězec pro připojení aplikace k databázi Azure SQL. Připojovací řetězec může být umístěný v databázi na webu Azure Portal.

    ![Připojovací řetězec](./media/contoso-migration-rearchitect-container-sql/container8.png)

10. Aktualizovaný kód potvrdí a předají ke službám Azure DevOps.

    ![Potvrzení](./media/contoso-migration-rearchitect-container-sql/container9.png)

## <a name="step-8-build-and-release-pipelines-in-azure-devops-services"></a>Krok 8: Vytváření a vydávání kanálů služby Azure DevOps

Správce společnosti Contoso teď nakonfigurovat služby Azure DevOps provést sestavení a vydání postup akce postupy DevOps.

1. Ve službě Azure DevOps Services kliknou **sestavení a vydání** > **nový kanál**.

    ![Nový kanál](./media/contoso-migration-rearchitect-container-sql/pipeline1.png)

2. Vyberou **Azure DevOps služby Git** a příslušné úložiště.

    ![Git a úložiště](./media/contoso-migration-rearchitect-container-sql/pipeline2.png)

3. V **vyberte šablonu**, vyberou prostředků infrastruktury pomocí podpory Dockeru.

     ![Prostředky infrastruktury a Dockeru](./media/contoso-migration-rearchitect-container-sql/pipeline3.png)
    
4. Se mění obrázky značku akce na **sestavte image**a nakonfigurovat úlohu, aby použití zřízené služby ACR.

     ![Registr](./media/contoso-migration-rearchitect-container-sql/pipeline4.png)

5. V **nahrávání imagí** úloh, konfigurují obrázek, který se vloží do ACR a vyberte zahrnout nejnovější značky.
6. V **triggery**, povolte průběžnou integraci a přidejte hlavní větve.

    ![Aktivační události](./media/contoso-migration-rearchitect-container-sql/pipeline5.png)

7. Kliknutím na **uložit a fronty** ke spuštění sestavení.
8. Po úspěšném sestavení, přecházejí na kanál pro vydávání verzí. Ve službě Azure DevOps Services kliknou **verze** > **nový kanál**.

    ![Kanál pro vydávání verzí](./media/contoso-migration-rearchitect-container-sql/pipeline6.png)    

9. Vyberou **nasazení Azure Service Fabric** šablony a názvu fázi (**SmartHotelSF**).

    ![Prostředí](./media/contoso-migration-rearchitect-container-sql/pipeline7.png)

10. Poskytuje název kanálu (**ContosoSmartHotel360Rearchitect**). Pro fázi, kliknou **úlohy 1, 1 úloha** ke konfiguraci nasazení Service Fabric.

    ![Fáze a úloh](./media/contoso-migration-rearchitect-container-sql/pipeline8.png)

11. Nyní, kliknou **nový** přidat nové připojení clusteru.

    ![Nové připojení](./media/contoso-migration-rearchitect-container-sql/pipeline9.png)

12. V **připojení služby Service Fabric přidat**, konfiguraci připojení a nastavení ověřování, které se použijí k nasazení aplikace pomocí služby Azure DevOps. Koncový bod clusteru můžou být umístěné na webu Azure Portal a přidat **tcp: / /** jako předponu.
13. Po zadání informací o certifikátu se shromažďují v **kryptografický otisk certifikátu serveru** a **klientský certifikát**.

    ![Certifikát](./media/contoso-migration-rearchitect-container-sql/pipeline10.png)

13. Kliknutím na kanál > **přidání artefaktu**.

     ![Artefakt](./media/contoso-migration-rearchitect-container-sql/pipeline11.png)

14. Vyberte projekt a vytvoření kanálu pomocí nejnovější verze.

     ![Sestavení](./media/contoso-migration-rearchitect-container-sql/pipeline12.png)

15. Všimněte si, že je zaškrtnuté políčko bolt blesku v artefaktu.

     ![Stav artefaktu](./media/contoso-migration-rearchitect-container-sql/pipeline13.png)

16. Navíc si všimněte, že je povoleno trigger průběžného nasazování.

   ![Průběžné nasazování povoleno](./media/contoso-migration-rearchitect-container-sql/pipeline14.png) 

17. Kliknutím na **Uložit** > **vytvořit vydanou verzi**.

    ![Vydat](./media/contoso-migration-rearchitect-container-sql/pipeline15.png)

18. Po dokončení nasazení SmartHotel360 se teď měla být spuštěná Service Fabric.

    ![Publikování](./media/contoso-migration-rearchitect-container-sql/publish4.png)

19. Pro připojení k aplikaci, se směrovat provoz na veřejnou IP adresu nástroje pro vyrovnávání zatížení Azure před uzly Service Fabricu.

    ![Publikování](./media/contoso-migration-rearchitect-container-sql/publish5.png)

## <a name="step-9-extend-the-app-and-republish"></a>Krok 9: Rozšíření aplikace a opětovné publikování

Po spuštění aplikace SmartHotel360 a databáze v Azure Contoso chce rozšíření aplikace.

- Vývojáři společnosti Contoso se vytváření prototypů novou aplikaci .NET Core, která se spustí v clusteru Service Fabric.
- Aplikace se použije k vyžádání mínění dat z cosmos DB.
- Tato data budou v podobě Tweety, které jsou zpracovány pomocí funkci Azure functions a Cognitive Services API analýzy Text.

### <a name="provision-azure-cosmos-db"></a>Zřízení služby Azure Cosmos DB

Správce společnosti Contoso jako první krok, zřídit databáze Azure Cosmos.

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
            "text": "This is a tweet about #SmartHotel360",
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

Pomocí služby Cosmos DB zřízené Contoso správci mohou nakonfigurovat aplikaci připojovat se k němu.

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

Po rozšíření aplikace, správce společnosti Contoso ji znovu publikovat do Azure s využitím kanálu.

1. Potvrdí a předají svůj kód ke službám Azure DevOps. To zahajuje kanály sestavení a vydaných verzí.

2. Po dokončení sestavení a nasazení SmartHotel360 se teď měla být spuštěná Service Fabric. Konzole pro správu Service Fabric teď zobrazuje tři služby.

    ![Opětovné publikování](./media/contoso-migration-rearchitect-container-sql/republish3.png)

3. Nyní mohou kliknout prostřednictvím služeb, které chcete zobrazit, že je aplikace SentimentIntegration zprovoznění

    ![Opětovné publikování](./media/contoso-migration-rearchitect-container-sql/republish4.png)

## <a name="clean-up-after-migration"></a>Vyčištění po migraci

Po dokončení migrace Contoso potřebuje k dokončení těchto kroků vyčištění:  

- Odebrání místních virtuálních počítačů z inventáře vCenter.
- Odeberte virtuální počítače z místní úlohy zálohování.
- Aktualizujte interní dokumentaci zobrazíte nové umístění pro aplikace SmartHotel360. Zobrazit databáze jako spuštěná v Azure SQL database a front-endu jako spuštěná ve službě Service Fabric.
- Zkontrolujte všechny prostředky, které pracují s Vyřazená z provozu virtuálních počítačů a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.


## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Contoso s migrovaných prostředků v Azure, musí plně zprovoznění a zabezpečení jejich nové infrastruktury.

### <a name="security"></a>Zabezpečení

- Správce společnosti Contoso musí zajistit, aby jejich nové **SmartHotel registrace** databáze je zabezpečená. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Konkrétně, by měl aktualizovat kontejner, aby používal SSL s certifikáty.
- Zvažte použití trezoru klíčů k ochraně tajných kódů pro své aplikace Service Fabric. [Další informace](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups"></a>Zálohování

- Contoso je potřeba zkontrolovat požadavkům zálohování pro Azure SQL Database. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Správce společnosti Contoso měli uvažovat o implementaci skupiny převzetí služeb při selhání pro zajištění regionální převzetí služeb při selhání pro databázi. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Můžete využít geografickou replikaci pro SKU úrovně premium služby ACR. [Další informace](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Contoso je nutné vzít v úvahu, jakmile je k dispozici Web App for Containers nasazení webové aplikace v hlavním východní USA 2 a oblast USA (střed). Správce společnosti Contoso může konfigurace Traffic Manageru zajistit převzetí služeb při selhání v případě místních výpadků.
- Cosmos DB, Zálohuje automaticky. Contoso [přečtěte si informace o](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) tento proces na další informace.

### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

- Po nasazení jsou všechny prostředky, by měl Contoso přiřadit Azure značky na základě [plánování infrastruktury](contoso-migration-infrastructure.md#set-up-tagging).
- Všechny licence je integrovaná do náklady na služby PaaS, které Contoso využívá. To se odečte od smlouvy EA.
- Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení správy nákladů multicloudové, který vám umožní využívat a spravovat Azure a další cloudové prostředky.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management.

## <a name="conclusion"></a>Závěr

V tomto článku se Refaktorovat Contoso SmartHotel360 aplikace v Azure a migrujte aplikace front-endový virtuální počítač do Service Fabric. Databáze aplikace se migroval do Azure SQL database.





