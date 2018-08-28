---
title: Refaktorujte Contoso Linuxovou aplikaci podpory služby Azure App Service a Azure MySQL | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso refactors Linuxové aplikace v místním migrací na Azure App Service pomocí Githubu pro webovou vrstvu a Azure SQL Database.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: raynew
ms.openlocfilehash: 9c43ce90603fd2bc812e5be00f438ee19054dc16
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43103626"
---
# <a name="contoso-migration-refactor-a-contoso-linux-service-desk-app-to-multiple-regions-with-azure-app-service-traffic-manager-and-azure-mysql"></a>Migrace Contoso: Refaktorovat Contoso Linuxovou aplikaci helpdesku service do několika oblastí pomocí služby Azure App Service, Traffic Manageru a Azure MySQL

Tento článek popisuje, jak společnosti Contoso refactors jejich místní dvouvrstvé Linux helpdesku app service (osTicket), díky migraci na Azure App Service díky integraci Githubu a MySQL v Azure.

Tento dokument je jednou z řady článků, které ukazují, jak fiktivní společnosti Contoso migraci svých místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a scénáře, které ukazují, jak nastavit infrastrukturu migrace a spustit různé druhy migrace. Scénáře jejich složitost v. Přidáme další články v čase.

**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Přehled řady článku, strategie migrace společnosti Contoso a ukázkové aplikace, které se používají v řadě. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Contoso připraví svoji místní infrastrukturu a jeho infrastruktury Azure pro migraci. Pro všechny články týkající se migrace z této série se používá stejnou infrastrukturu. | K dispozici.
[Článek 3: Posouzení místních prostředků pro migraci do Azure](contoso-migration-assessment.md)  | Contoso spuštění posouzení jeho místní SmartHotel aplikaci běžící ve VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí služby Azure Migrate a databáze aplikace SQL Server pomocí Data Migration Assistant. | K dispozici.
[Článek 4: Změna hostitele aplikace na virtuálním počítači Azure a SQL Database, spravované Instance](contoso-migration-rehost-vm-sql-managed-instance.md) | Contoso běží lift and shift migrace do Azure pro své místní SmartHotel aplikaci. Contoso migruje aplikace front-endového virtuálního počítače pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview). Contoso migraci databáze aplikace do Azure SQL Database Managed Instance pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.  
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Contoso migruje svou aplikaci SmartHotel virtuální počítače na virtuální počítače Azure pomocí služby Site Recovery. | K dispozici.
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a ve skupině dostupnosti AlwaysOn systému SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Contoso migruje SmartHotel aplikace. Společnost Contoso využívá Site Recovery k migraci virtuálních počítačů aplikace. Jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn využívá službu Database Migration Service. | K dispozici.    
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Contoso dokončení migrace lift and shift aplikace osTicket Linux do virtuálních počítačů Azure pomocí Azure Site Recovery | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Azure Site Recovery a migraci databáze aplikace na instanci serveru Azure MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorujte aplikace na Azure Web Apps a Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Contoso migruje SmartHotel aplikace do webové aplikace Azure a migraci databáze aplikace do instance serveru SQL Azure pomocí Pomocníka s migrací databáze | K dispozici.
Článek 10: Refaktorujte Linuxovou aplikaci na Azure Web Apps a Azure MySQL | Contoso migruje osTicket své Linuxové aplikace do webové aplikace Azure v několika oblastech Azure pomocí Azure Traffic Manageru, integrovaná se službou GitHub pro průběžné doručování. Contoso migraci databáze aplikace do Azure Database for MySQL – instance. | V tomto článku
[Článek 11: Refaktorovat TFS na VSTS](contoso-migration-tfs-vsts.md) | Contoso migruje své místní nasazení serveru Team Foundation Server pro Visual Studio Team Services v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a Azure SQL Database](contoso-migration-rearchitect-container-sql.md) | Contoso migruje jeho SmartHotel aplikace do Azure. Potom rearchitects webové vrstvy aplikace jako kontejner Windows se spuštěnou v Azure Service Fabric a databázi Azure SQL Database. | K dispozici.
[Článek 13: Znovu sestavte aplikaci v Azure](contoso-migration-rebuild.md) | Contoso replikujícím svou aplikaci SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služby Azure App Service, Azure Kubernetes Service (AKS), Azure Functions, Azure Cognitive Services a Azure Cosmos DB. | K dispozici.

V tomto článku se migruje Contoso aplikace služby podpory Linux Apache MySQL PHP (LAMP) dvouvrstvé (osTicket) do Azure. Pokud chcete používat tuto aplikaci open source, můžete ji stáhnout [Githubu](https://github.com/osTicket/osTicket).


## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzké spolupráci s obchodními partnery pochopit, co chtějí dosáhnout:

- **Adresa obchodní růst**: Contoso se rozrůstá a přechod na nové trhy. Rozpojuje agenty služby další odběratele. 
- **Škálování**: řešení by měly být sestaveny tak, aby Contoso můžete přidat další agenty služby zákazníka jako obchodní škálování.
- **Zvýšení odolnosti**: poslední problémy s systém vliv jenom interní uživatele. Pomocí nového obchodního modelu externí uživatele bude mít vliv a neustále Contoso potřeba aplikace rychle zprovoznit.

## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou dolů cíle pro tuto migraci, aby bylo možné určit nejlepší metody migrace:

- Aplikace by se měly škálovat nad rámec aktuální místní kapacitu a výkon.  Contoso je přesouvají aplikace, abyste mohli využívat Azure škálování na vyžádání.
- Contoso chcete přesunout základní kód aplikace do kanálu průběžného doručování.  Změny aplikace jsou vloženy do Githubu, Contoso chtít nasadit tyto změny bez úlohy pro provozní personál.
- Aplikace musí být odolné s možnostmi pro růst a převzetí služeb při selhání. Contoso chcete nasadit aplikaci ve dvou různých oblastech Azure a nastavit tak, aby automatické škálování.
- Contoso chce minimalizovat úlohy správy databáze po přesunu aplikace do cloudu.

## <a name="solution-design"></a>Návrh řešení
Po Připnutí dolů své cíle a požadavky, Contoso navrhuje a kontrol řešení nasazení a identifikuje proces migrace, včetně služeb Azure, které se použije pro migraci.


## <a name="current-architecture"></a>Aktuální architektura

- Aplikace je rozvrstvená na dva virtuální počítače (OSTICKETWEB a OSTICKETMYSQL).
- Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5).
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Contoso má místní datacentrum (contoso-datacenter), s řadičem domény v místním (**contosodc1**).

![Aktuální architektura](./media/contoso-migration-refactor-linux-app-service-mysql/current-architecture.png) 


## <a name="proposed-architecture"></a>Navrhované architektury

Tady je architektura navržená:

- Webová aplikace vrstvy v OSTICKETWEB budou migrovány pomocí služby Azure App Service ve dvou oblastech Azure. Azure App Service pro Linux se provádí pomocí kontejneru Dockeru s PHP 7.0.
- Kód aplikace se přesunou na Githubu a webové aplikace Azure se nakonfiguruje pro průběžné nasazování s Githubem.
- Servery služby Azure App se nasadí v primární (východní USA 2) a sekundární oblasti (centrální USA).
- Traffic Manager nastaví se před dvě webové aplikace Azure v obou oblastech.
- Traffic Manager se nakonfiguruje v režimu prioritu pro vynucení provozu prostřednictvím východní USA 2.
- Pokud Server aplikace Azure v oblasti východní USA 2, přejde do režimu offline, mohou uživatelé se přes aplikaci v oblasti střed USA.
- Databáze aplikace budou migrovat do služby Azure MySQL PaaS pomocí nástroje MySQL Workbench. V místní databázi bude zálohování místně a obnovit přímo do Azure MySQL.
- Databázi se bude nacházet v primární oblasti USA – východ 2, v databázi podsítě (PROD-DB-EUS2) v produkční sítě (VNET-PROD-EUS2):
- Vzhledem k tomu, že migrujete produkční úlohy, budou prostředky Azure pro aplikace umístěné v provozní skupině prostředků **ContosoRG**.
- Traffic Manager prostředků se nasadí ve skupině prostředků infrastruktury společnosti Contoso **ContosoInfraRG**.
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.


![Architektura scénáře](./media/contoso-migration-refactor-linux-app-service-mysql/proposed-architecture.png) 


## <a name="migration-process"></a>Proces migrace

Contoso dokončí proces migrace následujícím způsobem:

1. Jako první krok nastavte správce společnosti Contoso infrastrukturu Azure, včetně zřizování služby Azure App Services, si Traffic Manager nastavení a zřízení instance Azure MySQL.
2. Jakmile připravíte Azure, se migrace databáze pomocí aplikace MySQL Workbench. 
3. Po databáze běží v Azure, jsou do privátního úložiště GitHub pro Azure App Service se průběžné doručování a načtěte ho aplikaci osTicket.
4. Na webu Azure Portal se načíst aplikaci z Githubu do kontejneru Dockeru spuštěném služby Azure App Service. 
5. Upravit nastavení DNS a nakonfigurovat automatické škálování pro aplikaci.

![Proces migrace](./media/contoso-migration-refactor-linux-app-service-mysql/migration-process.png) 


### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[Azure App Service](https://azure.microsoft.com/services/app-service/) | Služba běží a škálování aplikací pomocí služby Azure PaaS pro weby.  | Cena se odvíjí velikost instance a požadované funkce. [Další informace](https://azure.microsoft.com/pricing/details/app-service/windows/).
[Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) | Nástroj pro vyrovnávání zatížení, který používá DNS, aby uživatelé do Azure nebo externích webů a služeb. | Ceny vychází z počtu přijatých dotazů DNS a počet monitorovaných koncových bodů. | [Další informace](https://azure.microsoft.com/pricing/details/traffic-manager/).
[Azure Database for MySQL](https://docs.microsoft.com/azure/mysql/) | Databáze je založen na open source modulu MySQL Server. Poskytuje plně spravované a připravená pro podnikové Společenství databáze MySQL jako služba pro vývoj a nasazování aplikací. | Cena je založená na výpočetní prostředky, úložiště a zálohování požadavků. [Další informace](https://azure.microsoft.com/pricing/details/mysql/).

 
## <a name="prerequisites"></a>Požadavky

Zde je, co Contoso potřebuje ke spuštění tohoto scénáře.

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Společnosti Contoso vytvořit odběry dříve v této sérii článku. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele. 
**Infrastruktura Azure** | Nastavit infrastrukturu Azure, jak je popsáno v contoso [infrastrukturu Azure na migraci](contoso-migration-infrastructure.md).



## <a name="scenario-steps"></a>Kroky scénáře

Zde je, jak bude Contoso dokončení migrace:

> [!div class="checklist"]
> * **Krok 1: Zřizování Azure App Services**: Správce společnosti Contoso zřídí Web Apps v primárních a sekundárních oblastech.
> * **Krok 2: Nastavte Traffic Manager**: nastavte Manager provoz před Web Apps pro směrování a vyrovnávání zatížení provozu.
> * **Krok 3: Zřízení MySQL**: V Azure, zřizování instance databáze Azure MySQL.
> * **Krok 4: Migrace databáze**: migraci databáze pomocí aplikace MySQL Workbench. 
> * **Krok 5: Nastavte GitHub**: nastavené místní úložiště GitHub pro projekt webové stránky nebo kódu aplikace.
> * **Krok 6: Nasazení webové aplikace**: nasadí webové aplikace z Githubu.




## <a name="step-1-provision-azure-app-services"></a>Krok 1: Zřizování Azure App Service

Správce společnosti Contoso zřízení dvě webové aplikace (jeden v každé oblasti) pomocí služby Azure App Services.

1. Vytvoří prostředek webové aplikace v primární oblasti USA – východ 2 (**osticket eus2**) z Azure Marketplace.
2. Převedou prostředek ve skupině prostředků produkční **ContosoRG**.

    ![Aplikace Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app1.png) 

3. Uživatel vytvořit nový plán služby App Service v primární oblasti (**aplikace. SVP EUS2**), pomocí standardní velikosti.

     ![Aplikace Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app2.png) 
    
4. Výběrem operačního systému Linux s zásobník modulu runtime PHP 7.0, což je kontejner Dockeru.

    ![Aplikace Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app3.png) 

5. Vytvoří druhou webovou aplikaci (**osticket cus**) a plán služby App service pro oblast USA (střed).

    ![Aplikace Azure](./media/contoso-migration-refactor-linux-app-service-mysql/azure-app4.png) 


**Potřebujete další pomoc?**

- Další informace o [Azure App Service Web apps](https://docs.microsoft.com/azure/app-service/app-service-web-overview).
- Další informace o [Azure App Service v Linuxu](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-intro).


## <a name="step-2-set-up-traffic-manager"></a>Krok 2: Nastavte Traffic Manager

Contoso správci nastavit pro přesměrování příchozích webových požadavků do webové aplikace běžící na osTicket webové vrstvy do Traffic Manageru.

1. Vytvoří prostředek Traffic Manageru (**osticket.trafficmanager.net**) z Azure Marketplace. Používají priority směrování tak, aby východní USA 2 je primární lokalita. Prostředek se umístit ve své skupině prostředků infrastruktury (**ContosoInfraRG**). Všimněte si, že Traffic Manager je globální a není vázaná na určité místo

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager1.png) 

2. Nyní jejich konfigurace Traffic Manageru s koncovými body. Východ USA 2 aplikaci přidávají jako primární lokality (**osticket eus2**) a USA (střed) aplikace jako sekundární (**osticket cus**).

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager2.png) 

3. Po přidání koncových bodů, můžete je monitorovat.

    ![Traffic Manager](./media/contoso-migration-refactor-linux-app-service-mysql/traffic-manager3.png)

**Potřebujete další pomoc?**

- Další informace o [Traffic Manageru](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview).
- Další informace o [směrování provozu do prioritního koncového bodu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-configure-priority-routing-method).
 
## <a name="step-3-provision-azure-database-for-mysql"></a>Krok 3: Zřízení Azure Database for MySQL

Správce společnosti Contoso zřízení instance databáze MySQL v primární oblasti USA – východ 2.

1. Na webu Azure Portal vytváření serveru Azure Database for MySQL prostředků. 

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-1.png)

2. Přidejte název **contosoosticket** pro databázi Azure. Přidávají do skupiny prostředků produkční databázi **ContosoRG**a zadejte přihlašovací údaje pro něj.
3. V místní databázi MySQL je verzi 5.7, takže vyberou tuto verzi z důvodu kompatibility. Používají výchozí velikosti, které odpovídají jejich požadavky na databázi.

     ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-2.png)

4. Pro **možnosti redundance zálohy**, vyberou používat **geograficky redundantní**. Tato možnost umožňuje jejich obnovení databáze v jejich sekundární oblasti střed USA, pokud dojde k výpadku. Tato možnost mohou nakonfigurovat pouze při zřizování databáze.

    ![Redundance](./media/contoso-migration-refactor-linux-app-service-mysql/db-redundancy.png)

4. Nastavení zabezpečení připojení. V databázi > **zabezpečení připojení**, že nastavení pravidel brány Firewall umožňující databáze přístupu ke službám Azure.
5. IP adresa klienta místní pracovní stanici přidávají do počáteční a koncovou IP adresu. To umožňuje webové aplikace pro přístup k databázi MySQL, spolu s klienta databáze, který provádí migraci.

    ![MySQL](./media/contoso-migration-refactor-linux-app-service-mysql/mysql-3.png)



## <a name="step-4-migrate-the-database"></a>Krok 4: Migrace databáze

Správce společnosti Contoso migrace databáze pomocí zálohování a obnovení pomocí nástrojů MySQL. Jejich aplikaci MySQL Workbench můžete nainstalovat, proveďte zálohu databáze z OSTICKETMYSQL a jeho následnému obnovení – Azure Database for MySQL Server.

### <a name="install-mysql-workbench"></a>Instalace aplikace MySQL Workbench

1. Zkontrolujte, že [požadavky a soubory ke stažení aplikace MySQL Workbench](https://dev.mysql.com/downloads/workbench/?utm_source=tuicool).
2. Instalace aplikace MySQL Workbench pro Windows v souladu s maticí [pokyny k instalaci](https://dev.mysql.com/doc/workbench/en/wb-installing.html). Na počítači, na kterém instalaci musí být přístupná pro virtuální počítač OSTICKETMYSQL a Azure prostřednictvím Internetu.
3. V aplikaci MySQL Workbench vytvoří připojení k OSTICKETMYSQL MySQL. 

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench1.png)

4. Jejich export databáze jako **osticket**, místní samostatný soubor.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench2.png)

5. Po databáze byly zálohovány místně, vytvoří připojení k Azure Database for MySQL – instance.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench3.png)

6. Teď se můžete importovat (obnovení) databáze v instanci Azure MySQL, z samostatný soubor. Nové schéma (osticket) je vytvořena instance.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench4.png)

7. Po obnovení dat, může být dotázán pomocí aplikace Workbench a zobrazí se na webu Azure Portal.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench5.png)

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench6.png)

8. Nakonec je třeba aktualizovat informace o databázi ve službě web apps. Na instanci MySQL otevřete **připojovací řetězce**. 

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench7.png)

9. V seznamu řetězců, vyhledejte nastavení webové aplikace a klikněte na tlačítko Kopírovat.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench8.png)

10. Otevřete okno Poznámkový blok a vložte řetězec do nového souboru a aktualizujte ji, aby odpovídala osticket databázi, instanci MySQL a nastavení přihlašovacích údajů.

     ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench9.png)

11. Tney můžete ověřit název serveru a přihlašovací jméno z **přehled** v instanci MySQL na webu Azure Portal.

    ![MySQL Workbench](./media/contoso-migration-refactor-linux-app-service-mysql/workbench10.png)


## <a name="step-5-set-up-github"></a>Krok 5: Nastavte GitHub

Správce společnosti Contoso vytvořit nový privátní úložiště GitHub a nastaví připojení k databázi osTicket v Azure MySQL. Pak načíst webové aplikace Azure s aplikací.  

1.  Přejděte do OsTicket softwaru veřejného úložiště GitHub se vzorovými a rozvětvili do účtu Githubu Contoso.

    ![GitHubu](./media/contoso-migration-refactor-linux-app-service-mysql/github1.png)

2. Po vytvoření forku uživatel přejde na **zahrnují** složky a najít **ost config.php** souboru.

    ![GitHubu](./media/contoso-migration-refactor-linux-app-service-mysql/github2.png)


3. Soubor se otevře v prohlížeči a upravit ho.

    ![GitHubu](./media/contoso-migration-refactor-linux-app-service-mysql/github3.png)

4. V editoru, konkrétně aktualizovat podrobnosti databáze **DBHOST** a **DBUSER**. 

    ![GitHubu](./media/contoso-migration-refactor-linux-app-service-mysql/github4.png)

5. Pak tito uživatelé potvrdí změny.

    ![GitHubu](./media/contoso-migration-refactor-linux-app-service-mysql/github5.png)

6. Pro každou webovou aplikaci (**osticket eus2** a **osticket cus**), mění **nastavení aplikace** na webu Azure Portal.

    ![GitHubu](./media/contoso-migration-refactor-linux-app-service-mysql/github6.png)

7. Zadání připojovacího řetězce s názvem **osticket**a zkopírujte řetězec z poznámkového bloku do **hodnota oblasti**. Vyberou **MySQL** v rozevíracím seznamu vedle řetězec a uložte nastavení.

    ![GitHubu](./media/contoso-migration-refactor-linux-app-service-mysql/github7.png)

## <a name="step-6-configure-the-web-apps"></a>Krok 6: Konfigurace webových aplikací

Jako poslední krok v procesu migrace konfigurace správce společnosti Contoso webové aplikace s weby osTicket.



1. V primární web app (**osticket eus2**) otevřou **možnost nasazení** a nastavte zdroj na **Githubu**.

    ![Konfigurace aplikace](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app1.png)

2. Výběrem možnosti nasazení.

    ![Konfigurace aplikace](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app2.png)

3. Po nastavení možností konfigurace zobrazí jako čekající na webu Azure Portal.

    ![Konfigurace aplikace](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app3.png)

4. Po konfiguraci se aktualizuje a osTicket webové aplikace je načtena z Githubu do č. J. kontejneru služby Azure App Service, lokality zobrazí jako aktivní.

    ![Konfigurace aplikace](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app4.png)

5. Výše uvedené kroky zopakují v sekundární webové aplikace (**osticket cus**).
6. Po dokončení konfigurace webu je přístupný prostřednictvím profilu Traffic Manageru. Název DNS je nové umístění osTicket aplikace. [Další informace](https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain#map-a-cname-record).

    ![Konfigurace aplikace](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app5.png)
    
7. Contoso chce, aby se název DNS, který se snadno zapamatuje. Vytváření záznamu o aliasu (CNAME) **osticket.contoso.com** který odkazuje na název Traffic Manageru ve službě DNS na svých řadičích domény.

    ![Konfigurace aplikace](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app6.png)

8. Konfigurace služby i **osticket eus2** a **osticket cus** webové aplikace, které chcete povolit vlastní názvy hostitelů.

    ![Konfigurace aplikace](./media/contoso-migration-refactor-linux-app-service-mysql/configure-app7.png)

### <a name="set-up-autoscaling"></a>Nastavení automatického škálování

Nakonec se nastavení automatického škálování pro aplikaci. Tím se zajistí, že agentů k používání aplikace, instance aplikace zvýšit nebo snížit podle obchodních potřeb. 

1. Ve službě App Service **aplikace. SRV EUS2**, otevřou **jednotka škálování**.
2. Nové nastavení automatického škálování, konfigurace s jedním pravidlem, které zvýší počet instancí o jednu, pokud procento využití procesoru pro aktuální instanci je vyšší než 70 % po dobu 10 minut.

    ![Automatické škálování](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale1.png)

3. Konfigurují na stejné nastavení **kapacitní jednotky APP SRV** zajistit, že platí stejné chování v případě, že aplikace převezme služby při selhání do sekundární oblasti. Jediným rozdílem je, že se instance limit nastavena na hodnotu 1 protože jde o pouze převzetí služeb při selhání.

   ![Automatické škálování](./media/contoso-migration-refactor-linux-app-service-mysql/autoscale2.png)

##  <a name="clean-up-after-migration"></a>Vyčištění po migraci

Pomocí migrace byla dokončena je teď vyčleněný osTicket aplikace ke spuštěným v Azure webové aplikace pomocí nepřetržité doručování pomocí privátního úložiště GitHub. Aplikace spuštěná ve dvou oblastech pro zvýšení odolnosti. Databáze osTicket běží ve službě Azure database for MySQL po dokončení migrace na platformě PaaS.

Pro čištění, Contoso potřebuje provést následující kroky: 
- Odeberte virtuální počítače VMware z inventáře vCenter.
- Odebrání místních virtuálních počítačů z místní úlohy zálohování.
- Aktualizace interní dokumentaci ukazují novým umístěním a IP adresy. 
- Zkontrolujte všechny prostředky, které pracují s místní virtuální počítače a aktualizovat všechny relevantní nastavení nebo v dokumentaci tak, aby odrážely novou konfiguraci.
- Změna konfigurace monitorování tak, aby odkazoval na adrese URL osticket trafficmanager.net, můžete sledovat, že aplikace funguje a spouštění.

## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Nyní spuštěnou aplikaci Contoso nutné plně zprovoznění a zabezpečení jejich nové infrastruktury.

### <a name="security"></a>Zabezpečení

Bezpečnostní tým Contoso zkontrolovat, jestli aplikace k určení jakékoli problémy se zabezpečením. Je-li zjistit, že komunikace mezi osTicket aplikace a instance databáze MySQL není nakonfigurovaný pro protokol SSL. Se musí provést k zajištění, že nemůže být zneužity provozu databáze. [Další informace](https://docs.microsoft.com/azure/mysql/howto-configure-ssl).

### <a name="backups"></a>Zálohování

- Webové aplikace osTicket neobsahují data o stavu a proto není nutné zálohovat.
- Není potřeba konfigurace zálohování pro databázi. Azure Database for MySQL automaticky vytvoří serverových záloh a uloží. Vybrali použijte geografickou redundanci pro databáze, tak, aby byl odolný a připravené pro produkční prostředí. Zálohy lze použít k obnovení vašeho serveru v daném okamžiku. [Další informace](https://docs.microsoft.com/azure/mysql/concepts-backup).


### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

- Nejsou žádné problémy s licencí pro nasazení PaaS.
- Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení správy nákladů multicloudové, který vám umožní využívat a spravovat Azure a další cloudové prostředky.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management.



