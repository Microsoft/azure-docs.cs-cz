---
title: Znovu sestavte aplikaci Contoso on-premises do Azure | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso znovu sestaví aplikaci do Azure pomocí služby Azure App Services, Kubernetes service, služby cosmos DB, Azure Functions a Cognitive services.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: raynew
ms.openlocfilehash: b99e4c4062723dad2b5ef089160799623f9d906c
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37934693"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Migrace Contoso: znovu sestavte místní aplikaci do Azure

Tento článek ukazuje, jak společnosti Contoso migraci a znovu sestaví jejich SmartHotel aplikace v Azure. Front-endu aplikace virtuálního počítače, migrace do Azure App Services – webové aplikace. Back-endu aplikace je sestavena pomocí mikroslužeb, které jsou nasazené do kontejnerů, které jsou spravované službou Azure Kubernetes Service (AKS). Web komunikuje s využitím Azure Functions poskytuje funkci domácí mazlíčky fotografii. 

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
[Článek 11: Úprava architektury aplikace na kontejnery služby Azure a SQL Database](contoso-migration-rearchitect-container-sql.md) | Ukazuje, jak společnosti Contoso migruje a rearchitects jeho SmartHotel aplikace do Azure. Jejich úprava architektury webové vrstvy aplikace jako kontejner Windows a databáze aplikace ve službě Azure SQL Database. | K dispozici.
Článek 12: Opětovné sestavení aplikace do Azure | Ukazuje, jak společnosti Contoso znovu sestavte své aplikace SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služeb App Services, Kubernetes v Azure, Azure Functions, Cognitive services a Cosmos DB. | Tento článek.

V tomto článku se migruje Contoso dvouvrstvé Windows. NET SmartHotel aplikace běžící na virtuálních počítačích VMware do Azure. Pokud chcete používat tuto aplikaci, se poskytuje jako open source a můžete ji stáhnout [Githubu](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzce spolupracuje s jejich obchodním partnerům umožní pochopit, co chtějí dosáhnout s tato migrace:

- **Adresa obchodní růst**: Contoso roste. Chce pro své zákazníky, kteří na svých webech poskytovat rozlišené prostředí.
- **Flexibilita**: Contoso musí být schopné reagovat rychleji než změn na webu Marketplace, aby úspěch v globální ekonomiku. 
- **Škálování**: růstem podniku zvládla úspěšně, musíte zadat Contoso IT systémy, které mohou rozšířit stejným tempem.
- **Náklady na**: Contoso chce minimalizovat náklady na licencování.

## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou požadavky aplikace pro tuto migraci. Tyto požadavky byly použity k určení nejlepší metody migrace:
 - Aplikace ve službě Azure zůstane tak kritický, protože se ještě dnes. Měl by dobře fungují a snadno škálovat.
 - Aplikace by neměly používat komponenty IaaS. Všechno, co by měly být sestaveny pro použití PaaS nebo služeb bez serveru.
 - Sestavení aplikace by měly být spuštěny cloudové služby a kontejnery by měl být uložený v registru kontejneru soukromého podnikové úrovni v cloudu.
 - Služba rozhraní API používaná pro domácí mazlíčky fotografie by měl být přesným a spolehlivým z okolního světa, protože rozhodnutí aplikace musí být zachované jejich hotels. Žádné domácí mazlíček udělil přístup může zůstat na hotely.

## <a name="solution-design"></a>Návrh řešení

Po Připnutí dolů své cíle a požadavky, Contoso navrhuje, zkontrolujte nasazení řešení a identifikuje proces migrace, včetně služeb Azure, které budete používat pro migraci.

### <a name="current-app"></a>Aktuální aplikaci

- SmartHotel místní aplikace je rozvrstvená na dva virtuální počítače (WEBVM a SQLVM).
- Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5)
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Contoso má místní datacentrum (contoso-datacenter), s řadičem domény v místním (**contosodc1**).
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.


### <a name="proposed-architecture"></a>Navrhované architektury

- Front-endové aplikace se nasadí jako aplikace služby Azure App Services Web v jejich primární oblasti.
- Funkce Azure bude poskytovat nahrávání fotografií domácích a web bude komunikovat pomocí této funkce.
- Funkci domácí mazlíčky fotografii využívá Vision API služeb Cognitive Services a služby cosmos DB.
- Back-end serveru je sestavena pomocí mikroslužeb. Ty se nasadí do kontejnerů využívá Azure Kubernetes service (AKS).
- Kontejnery se vytvořené pomocí VSTS a vloženy do Azure Container Registry (ACR).
- Prozatím Contoso ručně nasadit webové aplikace a funkce kódu pomocí sady Visual Studio.
- Mikroslužby se nasadí pomocí Powershellového skriptu, který volá nástroje příkazového řádku Kubernetes.

    ![Architektura scénáře](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Kontrola řešení
Contoso vyhodnotí své navrhované návrhu společně vložením seznam výhody a nevýhody.

**Posouzení** | **Podrobnosti**
--- | ---
**V oblasti IT** | Použití řešení bez serveru a PaaS nasazení začátku do konce výrazně snižuje čas správy, který musíte zadat Contoso.<br/><br/> Přesun na architekturu mikroslužeb umožňuje společnosti Contoso snadno rozšířit své řešení v čase.<br/><br/> Nové funkce můžete do online režimu bez narušení běžného některý z existujících základů kódu řešení.<br/><br/> Webová aplikace bude mít nakonfigurovanou více instancí se žádný jediný bod selhání.<br/><br/> Tak, aby aplikace dokáže zpracovat provoz hromadí se povolí automatické škálování.<br/><br/> S přechodem na služby modelu PaaS Contoso ho vyřadit. zastaralé řešení používá operační systém Windows Server 2008 R2.<br/><br/> Cosmos DB má předdefinovanou odolnost proti chybám, které není nutné nic konfigurovat společností Contoso. To znamená, že datová vrstva již není jediným bodem převzetí služeb při selhání.
**Nevýhody** | Kontejnery jsou složitější než jiné možnosti migrace. Učení můžou být problémy pro doménu Contoso.  Ale zavádí novou úroveň složitosti, která poskytuje spoustu vylepšení, přestože křivky.<br/><br/> Provozní tým ve společnosti Contoso potřeba rychle pochopit práci a podporu Azure, kontejnery a mikroslužby pro aplikaci.<br/><br/> Contoso neimplementovala plně DevOps pro celé řešení. Jsou potřeba se zamyslet, které pro nasazení služby AKS, functions a App Services.



### <a name="migration-process"></a>Proces migrace

1. Contoso zřizování ACR a AKS a cosmos DB.
2. Zřizování infrastrukturu pro nasazení, včetně webové aplikace Azure, účet úložiště, funkce a rozhraní API. 
3. Po vytvoření infrastruktury na místě, že budete vytvářet jejich mikroslužeb kontejneru obrázků s využitím VSTS, která odesílá je služby ACR.
4. Contoso nasadí těchto mikroslužeb na dotaz pomocí Powershellového skriptu.
5. Nakonec budete nasazovat funkce Azure functions a webové aplikace.

    ![Proces migrace](./media/contoso-migration-rebuild/migration-process.png) 

### <a name="azure-services"></a>Služby Azure

**Služba** | **Popis** | **Náklady**
--- | --- | ---
[AKS](https://docs.microsoft.com/sql/dma/dma-overview?view=ssdt-18vs2017) | Zjednodušuje správu, nasazení a provoz Kubernetes. Poskytuje plně spravovanou službu Orchestrace kontejnerů Kubernetes.  | AKS je bezplatná služba.  Plaťte jenom virtuální počítače a přidružené úložiště a síťové prostředky spotřebované. [Další informace](https://azure.microsoft.com/pricing/details/kubernetes-service/).
[Azure Functions](https://azure.microsoft.com/services/functions/) | Zrychluje vývoj aplikací pomocí prostředí řízené událostmi výpočetní prostředí bez serveru. Škálování na vyžádání.  | Platíte jenom za využité prostředky. Plán se fakturuje v závislosti na spotřebě prostředků za sekundu a spuštění. [Další informace](https://azure.microsoft.com/pricing/details/functions/).
[Azure Container Registry](https://azure.microsoft.com/services/container-registry/) | Ukládá Image pro všechny typy kontejnerových nasazení. | Náklady podle funkce, úložiště a doby trvání využití. [Další informace](https://azure.microsoft.com/pricing/details/container-registry/).
[Azure App Service](https://azure.microsoft.com/services/app-service/containers/) | Využijte možnost rychlého sestavení, nasazení a škálování webových, mobilních a API aplikací na podnikové úrovni, které běží na libovolné platformě. | Plány služby App Service se účtují po sekundách. [Další informace](https://azure.microsoft.com/pricing/details/app-service/windows/).

## <a name="prerequisites"></a>Požadavky

Zde je můžete vy (a Contoso) potřebovat ke spuštění tohoto scénáře:

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Měli jste už vytvořili odběr při provádění posouzení v první článku v této sérii. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.
**Infrastruktura Azure** | [Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastavení infrastruktury Azure.
**Požadavky pro vývojáře** | V publikovat, Contoso má přidané nastavení aplikace pro rozhraní API pro počítačové zpracování obrazu, úložiště a databáze Cosmos.<br/><br/> - [Pro místní spuštění funkce poprvé, se aktualizace nastavení v - PetCheckerFunction/local.settings.json[.](https://www.visualstudio.com/)<br/><br/> Po nasazení funkce zobrazuje na webu Azure Portal, se systémem stav.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [Procházení k aplikaci otestovat, kontrola AI domácí mazlíček funguje dle očekávání v [ ](https://docs.docker.com/docker-for-windows/install/) .



## <a name="scenario-steps"></a>Kroky scénáře

Tady je způsob spuštění migrace Contoso:

> [!div class="checklist"]
> * **Kliknutí na miniaturu pro nahrání obrázku.
> * **První fotografií, které chtějí zkontrolovat je malý pes.
> * **Aplikace vrátí zprávu přijetí.
> * **Contoso se muset ujistit, že jejich nové databáze jsou zabezpečené.
> * **Aplikace je potřeba aktualizovat pomocí certifikátů SSL.
> * **Odpovědět na 443 by měl znovu nasadit instanci kontejneru.



## <a name="step-1-provision-an-aks-cluster-and-acr"></a>Zvažte použití trezoru klíčů k ochraně tajných kódů pro své aplikace Service Fabric.

Zálohování a zotavení po havárii

- Jejich měli uvažovat o implementaci SQL skupiny převzetí služeb při selhání pro zajištění regionální převzetí služeb při selhání pro databázi.
- Můžete využít geografickou replikaci pro SKU úrovně premium služby ACR.

V tomto článku Contoso znovu sestavit SmartHotel aplikace v Azure.

1. Se znovu vytvořit místní aplikaci front-endového virtuálního počítače do Azure App Services Web apps. 
2. Vytvořili aplikace back-endu pomocí mikroslužeb, které jsou nasazené do kontejnerů, které jsou spravované službou Azure Kubernetes Service (AKS).

    **Tyto rozšířené funkce pomocí aplikace pro domácí mazlíčky fotografii. https://github.com/Microsoft/SmartHotel360-Azure-backend.git**

3.  Contoso otevře složku pomocí nástroje Visual Studio Code a přejde **/nasazení/k8s** adresáře, který obsahuje skript **obecné aks-env.ps1**.
4. Spouštějí se skript k vytvoření spravovaného clusteru Kubernetes AKS a Container Registry.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
5.  S otevřeným souborem aktualizují parametr $location **eastus2**a soubor uložte.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

6. Kliknutím na **zobrazení** > **integrovaný terminál** otevřít integrovaný terminál v kódu.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

7. V prostředí PowerShell integrovaný terminál kterým se přihlašují do Azure pomocí příkazu Connect-AzureRmAccount. [Další informace](https://docs.microsoft.com/powershell/azure/get-started-azureps) o zahájení práce s prostředím PowerShell.

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

8. Ověřování pomocí Azure CLI **az login** příkaz a postupujte podle pokynů k ověření pomocí svého webového prohlížeče. [Další informace](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) o přihlášení pomocí Azure CLI.

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

9. Spusťte následující příkaz, předejte název skupiny prostředků ContosoRG, název AKS clusteru smarthotel-aks-eus2 a název nového registru.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```

    ![AKS](./media/contoso-migration-rebuild/aks6.png)

10. Azure vytvoří jiné skupiny prostředků obsahující prostředky clusteru AKS.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

11. Po dokončení nasazení Contoso nainstalovat **kubectl** nástroj příkazového řádku. Nástroj je již nainstalován na Azure Cloud Shell.

    **AZ aks install-cli**

12. Připojení ke clusteru, ověřte spuštěním **kubectl get uzly** příkazu. Uzel se stejným názvem jako virtuální počítač ve skupině automaticky vytvořený prostředek.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

13. Spusťte následující příkaz, který spouští řídicí panel Kubernetes: 

    **Procházet az aks--& gt; resource-group ContosoRG – název smarthotelakseus2**

14. Na řídicí panel se otevře na kartě prohlížeče. Toto je tunelového propojení pomocí Azure CLI. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-build-a-docker-container"></a>Krok 2: Vytvoření kontejneru Dockeru

### <a name="create-a-vsts-and-build"></a>Vytvoření VSTS a sestavení

Contoso se vytvoří projekt VSTS a nakonfiguruje sestavení CI k vytvoření kontejneru a předá ji do služby ACR. Pokyny v této části používají [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) úložiště.

1. Z visualstudio.com, jejich vytvoření nového účtu (**contosodevops360.visualstudio.com**) a nakonfigurujte ho na používání Git.

2. Vytvořte nový projekt (**smarthotelrefactor**) pomocí Gitu pro správu verzí a agilní pracovního postupu.

    ![VSTS](./media/contoso-migration-rebuild/vsts1.png) 


3. Naimportují úložiště GitHub.

    ![VSTS](./media/contoso-migration-rebuild/vsts2.png)
    
4. V **sestavení a vydání**, vytvářejí nové definice pomocí VSTS Git jako zdroj, od importované **smarthotel** úložiště. 

    ![VSTS](./media/contoso-migration-rebuild/vsts3.png)

6. Výběru se spustí s prázdném kanálu.

    ![VSTS](./media/contoso-migration-rebuild/vsts4.png)  

7. Vyberou **hostované Linuxové verze Preview** pro definici sestavení.

    ![VSTS](./media/contoso-migration-rebuild/vsts5.png) 
 
8. V **fáze 1**, přidávají **Docker Compose** úloh. Tento úkol sestavení Dockeru compose.

    ![VSTS](./media/contoso-migration-rebuild/vsts6.png) 

9. Opakujte a přidejte další **Docker Compose** úloh. Kontejnery tohohle nahraje do služby ACR.

     ![VSTS](./media/contoso-migration-rebuild/vsts7.png) 

8. Vyberte první úlohu (sestavení) a konfigurace sestavení pomocí předplatného Azure, autorizace a služby ACR. 

    ![VSTS](./media/contoso-migration-rebuild/vsts8.png)

9. Určí cestu **č. j. compose.yaml** souboru **src** složce úložiště. Výběrem imagí služby sestavení a zahrnout nejnovější značky. Při změně akci k **sestavování imagí služby**, název úloh VSTS se změní na **automaticky vytvářet služby**

    ![VSTS](./media/contoso-migration-rebuild/vsts9.png)

10. Nyní Contoso nakonfiguruje druhý Docker úlohy (push). Výběrem předplatného a **smarthotelacreus2** služby ACR. 

    ![VSTS](./media/contoso-migration-rebuild/vsts10.png)

11. Znovu, zadejte ho do souboru docker-compose.yaml a vyberte **nahrávání imagí služby** a obsahují nejnovější značky. Když akce změny **nahrávání imagí služby**, název úloh VSTS se změní na **služby nabízených oznámení automaticky**

    ![VSTS](./media/contoso-migration-rebuild/vsts11.png)

12. Pomocí úloh VSTS nakonfigurované Contoso uloží definice sestavení a spustí proces sestavení.

    ![VSTS](./media/contoso-migration-rebuild/vsts12.png)

13. Kliknutí na úlohu sestavení ke kontrole průběhu.

    ![VSTS](./media/contoso-migration-rebuild/vsts13.png)

14. Po dokončení sestavení ACR zobrazuje nové úložiště, které se vyplní s kontejnery, mikroslužby používá.

    ![VSTS](./media/contoso-migration-rebuild/vsts14.png)


## <a name="step-3-deploy-back-end-microservices"></a>Krok 3: Nasazení mikroslužeb back-end

Vytvoření clusteru AKS a sestavení Image Dockeru Contoso nyní nasadí zbývající infrastrukturu, která bude využívat back-end mikroslužeb.

- Podle pokynů v části použití [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) úložiště.
- V **/deploy/k8s/arm** složce neexistuje jeden skript k vytvoření všech položek. 

Nasadí následujícím způsobem:

1. Společnost Contoso využívá souboru deploy.cmd k nasazení prostředků Azure ve skupině prostředků ContosoRG a EUS2 oblasti tak, že zadáte následující příkaz:

    **. \deploy azuredeploy - c eastus2 ContosoRG**

    ![Nasazení back-endu](./media/contoso-migration-rebuild/backend1.png)

2. Dojde k zachycení připojovací řetězec pro každou databázi, pro pozdější použití.

    ![Nasazení back-endu](./media/contoso-migration-rebuild/backend2.png)

## <a name="step-4-deploy-front-end-infrastructure"></a>Krok 4: Nasazení infrastruktury front-endu

Contoso je potřeba nasadit infrastrukturu, která bude používat front-endové aplikace. Vytvoří kontejner objektů blob úložiště pro ukládání imagí domácí mazlíčky; databázi Cosmos k ukládání dokumentů s informací o domácí mazlíčky; a rozhraní API pro zpracování obrazu pro web. 

Pokyny k tomuto oddílu používají [SmartHotel-public-web](https://github.com/Microsoft/SmartHotel360-public-web) úložiště.

### <a name="create-storage-containers"></a>Vytvořit kontejnery úložiště

1.  Na webu Azure portal Contoso, otevře se účet úložiště, který byl vytvořen a klikne na tlačítko **objekty BLOB**.
2.  Vytvoří nový kontejner (**Mazlíčci**) s úroveň veřejného přístupu nastavena na kontejner. Uživatelé odešlete své domácí mazlíčky fotky do tohoto kontejneru.

    ![Úložiště objektů blob](./media/contoso-migration-rebuild/blob1.png)

3. Vytvoří druhý nový kontejner s názvem **nastavení**. Soubor se všechna nastavení front-endu aplikací budou umístěny v tomto kontejneru.

    ![Úložiště objektů blob](./media/contoso-migration-rebuild/blob2.png)

4. Dojde k zachycení podrobností pro přístup k účtu úložiště v textovém souboru, pro pozdější použití.

    ![Úložiště objektů blob](./media/contoso-migration-rebuild/blob2.png)

## <a name="provision-a-cosmos-database"></a>Zřízení databáze Cosmos

Contoso zřizuje databázi Cosmos má být použit pro domácí mazlíčky informace.

1. Vytvoří **služby Azure Cosmos DB** na webu Azure Marketplace.

    ![Databáze Cosmos](./media/contoso-migration-rebuild/cosmos1.png)

2. Zadejte název (**contosomarthotel**), vyberte rozhraní SQL API a jeho následné uložení do skupiny prostředků produkční ContosoRG v hlavní oblasti USA – východ 2.

    ![Databáze Cosmos](./media/contoso-migration-rebuild/cosmos2.png)

3. Nová kolekce si přidají do databáze, výchozí kapacita a propustnost.

    ![Databáze Cosmos](./media/contoso-migration-rebuild/cosmos3.png)


4. Poznamenejte si informace o připojení pro databázi pro budoucí použití. 

    ![Databáze Cosmos](./media/contoso-migration-rebuild/cosmos4.png)


## <a name="provision-computer-vision"></a>Zřizování pro počítačové zpracování obrazu

Contoso zřídí rozhraní API pro počítačové zpracování obrazu. Rozhraní API bude volat funkci, k vyhodnocení obrázky odeslaný uživateli.

1. Vytvoří **pro počítačové zpracování obrazu** instance na webu Azure Marketplace.

     ![Computer Vision](./media/contoso-migration-rebuild/vision1.png)

2. Zřizování rozhraní API (**smarthotelpets**) v prostředku produkčních skupině ContosoRG v hlavní oblasti USA – východ 2.

    ![Computer Vision](./media/contoso-migration-rebuild/vision2.png)

3. Nastavení připojení pro rozhraní API ukládají do textového souboru pro pozdější použití.

     ![Computer Vision](./media/contoso-migration-rebuild/vision3.png)

## <a name="step-5-deploy-the-back-end-services-in-azure"></a>Krok 5: Nasazení back-end služby v Azure

Nyní Contoso je nutné nasadit kontroler příchozího přenosu dat NGINX povolí příchozí provoz do služeb a pak nasadit mikroslužby do clusteru AKS.

Pokyny v této části používají [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) úložiště.



1. K aktualizaci používají Visual Studio Code **/deploy/k8s/config_loal.yml** souboru. Tyto aktualizace různých připojení databáze z informací, že jste si předtím poznamenali.

     ![Nasazování mikroslužeb](./media/contoso-migration-rebuild/deploy-micro.png)

    > [!NOTE]
    > Některá nastavení konfigurace (třeba Active Directory B2C) nejsou pokryté v tomto článku. Není k dispozici další informace o těchto nastaveních v úložišti.

2. Soubor deploy.ps1 odstraní veškerý obsah clusteru (s výjimkou příchozího přenosu dat a kontroler příchozího přenosu dat) a nasazuje mikroslužby. Jejich spuštění následujícím způsobem:

  ```
  .\deploy.ps1 -configFile .\conf_local.yml -dockerUser smarthotelacreus2  -dockerPassword [passwordhere] -registry smarthotelacreus2.azurecr.io -imageTag latest -deployInfrastructure $false -buildImages $false -pushImages $false
  ```

3. Spusťte následující příkaz a zkontrolujte stav služeb:

    ```
    kubectl get services
    ```
4. Otevření řídicího panelu Kubernetes ke kontrole nasazení:

    ```
    az aks browse --resource-group ContosoRG --name smarthotelakseus2
    ```


## <a name="step-6-publish-the-frontend"></a>Krok 6: Publikování aplikace front-endu

V posledním kroku publikuje Contoso SmartHotel aplikace do služby Azure App Service a do aplikace Function App, která je volána domácí mazlíček služby.

Pokyny v této části používají [úložiště SmartHotel. veřejný web.](https://github.com/Microsoft/SmartHotel360-public-web) úložiště.

### <a name="set-up-web-app-to-use-contoso-resources"></a>Nastavení webové aplikace, využívat prostředky společnosti Contoso

1. Contoso duplicity úložiště lokálně na vývojovém počítači s aplikací Git:

    **Tyto rozšířené funkce pomocí aplikace pro domácí mazlíčky fotografii. https://github.com/Microsoft/SmartHotel360-public-web.git**

2. V sadě Visual Studio, otevřete složku, kterou chcete zobrazit všechny soubory v úložišti.

    ![Publikování front-endu](./media/contoso-migration-rebuild/front-publish1.png)

3. Změny konfigurace, které jsou potřebné k nastavení výchozí využívají.

    - Při spuštění webové aplikace, hledá **SettingsUrl** nastavení aplikace.
    - Tato proměnná musí obsahovat adresu URL do konfiguračního souboru.
    - Ve výchozím nastavení používá je veřejný koncový bod.

4. Aktualizují **/config-sample.json/sample.json** souboru. To je konfigurační soubor pro web, při použití veřejného koncového bodu.

    - Upraví i **adresy URL** a **pets_config** oddíly s hodnotami pro koncové body rozhraní API AKS, účty úložiště a databáze Cosmos. 
    - Adresy URL by měla odpovídat názvu DNS novou webovou aplikaci, která se vytvoří Contoso.
    - Pro společnost Contoso, toto je **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![Publikování front-endu](./media/contoso-migration-rebuild/front-publish2.png)

5. Po aktualizaci souboru, přejmenujte ho na **smarthotelsettingsurl**a nahrajte ho do objektu blob storage vytvořili dříve.

     ![Publikování front-endu](./media/contoso-migration-rebuild/front-publish3.png)

6. Klikněte na soubor, který má získat adresu URL. Při spuštění stáhnout konfigurační soubor, je tato adresa URL používá aplikace.

    ![Publikování front-endu](./media/contoso-migration-rebuild/front-publish4.png)

7. Aktualizují **SettingsUrl** v **appsettings. Production.JSON** soubory na adresu URL nového souboru.

    ![Publikování front-endu](./media/contoso-migration-rebuild/front-publish5.png)


### <a name="deploy-the-website-to-the-azure-app-service"></a>Nasadit do Azure App Service

Nyní Contoso Web můžete publikovat v jejich.


1. Umožňují monitorování pomocí Application Insights v sadě Visual Studio 2017. Chcete-li to provést, vyberte **PublicWeb** projektu v Průzkumníku řešení a vyhledejte **přidat službu Application Insights**. Aby se zaregistrovali aplikaci k instanci nástroje Application Insights, který byl vytvořen při jejich nasazení infrastruktury.

    ![Publikování webu](./media/contoso-migration-rebuild/deploy-website1.png)

2. V sadě Visual Studio, připojte PublicWeb projektu do App Insights a aktualizujte lze zobrazit, že je nakonfigurovaný.
 
    ![Publikování webu](./media/contoso-migration-rebuild/deploy-website2.png)

3. V sadě Visual Studio, vytvářet a publikovat své webové aplikace.

    ![Publikování webu](./media/contoso-migration-rebuild/deploy-website3.png)

5. Zadejte název aplikace a umístěte ji v produkčních skupině prostředků **ContosoRG**, v hlavní oblasti USA – východ 2.

    ![Publikování webu](./media/contoso-migration-rebuild/deploy-website4.png)

### <a name="deploy-the-function-to-azure"></a>Nasazení funkce do Azure

1. Vytvoření a publikování funkce používají Visual Studio. Uděláte to tak, že kliknete pravým tlačítkem na **PetCheckerFunction** > **publikovat**. Pak vytvořte nové funkce služby App Service.

     ![Nasazení funkce](./media/contoso-migration-rebuild/function1.png)

2. Určí název **smarthotelpetchecker**a umístěte ho do skupiny prostředků ContosoRG a nový plán služby app service.

     ![Nasazení funkce](./media/contoso-migration-rebuild/function2.png)

3. Vyberte úložiště na účet a vytvořte funkci.

    ![Nasazení funkce](./media/contoso-migration-rebuild/function3.png)

4. Nasazení se spustí při zřizování aplikace function app do Azure. V **publikovat**, Contoso má přidané nastavení aplikace pro rozhraní API pro počítačové zpracování obrazu, úložiště a databáze Cosmos.

    ![Nasazení funkce](./media/contoso-migration-rebuild/function4.png)

5. Pro místní spuštění funkce poprvé, se aktualizace nastavení v **PetCheckerFunction/local.settings.json**.

    ![Nasazení funkce](./media/contoso-migration-rebuild/function5.png)

6. Po nasazení funkce zobrazuje na webu Azure Portal, se **systémem** stav.

    ![Nasazení funkce](./media/contoso-migration-rebuild/function6.png)


7. Procházení k aplikaci otestovat, kontrola AI domácí mazlíček funguje dle očekávání v [ http://smarthotel360public.azurewebsites.net/Pets ](http://smarthotel360public.azurewebsites.net/Pets).
8. Kliknutí na miniaturu pro nahrání obrázku.

    ![Nasazení funkce](./media/contoso-migration-rebuild/function7.png)

9. První fotografií, které chtějí zkontrolovat je malý pes.

    ![Nasazení funkce](./media/contoso-migration-rebuild/function8.png)

10. Aplikace vrátí zprávu přijetí.

    ![Nasazení funkce](./media/contoso-migration-rebuild/function9.png)




## <a name="review-the-deployment"></a>Zkontrolujte nasazení

Contoso s migrovaných prostředků v Azure, musí plně zprovoznění a zabezpečení jejich nové infrastruktury.

### <a name="security"></a>Zabezpečení

- Contoso se muset ujistit, že jejich nové databáze jsou zabezpečené. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Aplikace je potřeba aktualizovat pomocí certifikátů SSL. Odpovědět na 443 by měl znovu nasadit instanci kontejneru.
- Zvažte použití trezoru klíčů k ochraně tajných kódů pro své aplikace Service Fabric. [Další informace](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups-and-disaster-recovery"></a>Zálohování a zotavení po havárii

- Contoso je potřeba zkontrolovat požadavkům zálohování pro Azure SQL Database. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Jejich měli uvažovat o implementaci SQL skupiny převzetí služeb při selhání pro zajištění regionální převzetí služeb při selhání pro databázi. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Můžete využít geografickou replikaci pro SKU úrovně premium služby ACR. [Další informace](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication)

### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

- Po nasazení jsou všechny prostředky, by měl Contoso přiřadit Azure značky na základě jejich [plánování infrastruktury](contoso-migration-infrastructure.md#set-up-tagging).
- Všechny licence je integrovaná do náklady na služby PaaS, které Contoso využívá. To se odečte od smlouvy EA.
- Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení správy nákladů multicloudové, který vám umožní využívat a spravovat Azure a další cloudové prostředky.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management.

## <a name="conclusion"></a>Závěr

V tomto článku Contoso znovu sestavit SmartHotel aplikace v Azure. Se znovu vytvořit místní aplikaci front-endového virtuálního počítače do Azure App Services Web apps. Vytvořili aplikace back-endu pomocí mikroslužeb, které jsou nasazené do kontejnerů, které jsou spravované službou Azure Kubernetes Service (AKS). Tyto rozšířené funkce pomocí aplikace pro domácí mazlíčky fotografii.




