---
title: Znovu sestavte aplikaci Contoso on-premises do Azure | Dokumentace Microsoftu
description: Zjistěte, jak společnosti Contoso znovu sestaví aplikaci do Azure pomocí služby Azure App Services, Kubernetes service, služby cosmos DB, Azure Functions a Cognitive services.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/20/2018
ms.author: raynew
ms.openlocfilehash: f0dc199f8a91ac06993f4ccbc9dff7dfad9f8a19
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47042478"
---
# <a name="contoso-migration-rebuild-an-on-premises-app-to-azure"></a>Migrace Contoso: znovu sestavte místní aplikaci do Azure

Tento článek ukazuje, jak společnosti Contoso migruje a znovu sestaví SmartHotel360 aplikace v Azure. Contoso migraci do Azure App Services – webové aplikace front-endu aplikace virtuálního počítače. Back-endu aplikace je sestavena pomocí mikroslužeb, které jsou nasazené do kontejnerů, které jsou spravované službou Azure Kubernetes Service (AKS). Web komunikuje s využitím Azure Functions nakonfigurovánu domácí mazlíčky fotografii. 

Tento dokument je jednou z řady článků, které ukazují, jak fiktivní společnosti Contoso migraci místních prostředků do cloudu Microsoft Azure. Obsahuje základní informace a scénáře, které znázorňují nastavování infrastruktury migrace, posuzování migrace místních prostředků a spouštění různé druhy migrace. Scénáře jejich složitost v. Přidáme další články v čase.


**Článek** | **Podrobnosti** | **Stav**
--- | --- | ---
[Článek 1: Přehled](contoso-migration-overview.md) | Poskytuje přehled strategie migrace společnosti Contoso, článek řady a ukázkové aplikace, které používáme. | K dispozici.
[Článek 2: Nasazení infrastruktury Azure](contoso-migration-infrastructure.md) | Popisuje, jak společnosti Contoso připraví jeho místní a infrastrukturu Azure na migraci. Stejnou infrastrukturu se používá pro všechny články týkající se migrace. | K dispozici.
[Článek 3: Posouzení místních prostředků](contoso-migration-assessment.md)  | Ukazuje, jak společnosti Contoso spuštění posouzení místních dvouvrstvé SmartHotel360 aplikaci spuštěnou v prostředí VMware. Contoso vyhodnocuje aplikací virtuálních počítačů pomocí [Azure Migrate](migrate-overview.md) služby a databáze aplikace SQL serveru s [Pomocníka s migrací databáze](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | K dispozici.
[Článek 4: Změna hostitele aplikací na virtuálních počítačích Azure a spravované Instance SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Ukazuje, jak společnosti Contoso běží lift and shift migrace do Azure pro aplikace SmartHotel360. Contoso migruje virtuální počítač front-endu aplikace pomocí [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)a databáze aplikace do spravované Instance SQL, pomocí [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview). | K dispozici.
[Článek 5: Změna hostitele aplikace na virtuálních počítačích Azure](contoso-migration-rehost-vm.md) | Ukazuje, jak migrovat aplikace SmartHotel360 virtuálních počítačů pomocí Site Recovery pouze Contoso. | K dispozici.
[Článek 6: Změna hostitele aplikace na virtuálních počítačích Azure a SQL Server Always On Availability Group](contoso-migration-rehost-vm-sql-ag.md) | Ukazuje, jak společnosti Contoso migruje aplikace SmartHotel360. Contoso využívá Site Recovery k migraci aplikace, virtuální počítače a Database Migration service, jak migrovat databázi aplikace do clusteru SQL serveru, který je chráněn skupiny dostupnosti AlwaysOn. | K dispozici.
[Článek 7: Změna hostitele aplikace na virtuálních počítačích Azure s Linuxem](contoso-migration-rehost-linux-vm.md) | Ukazuje, jak Contoso funguje lift and shift migrace aplikace osTicket Linux na virtuální počítače Azure pomocí služby Site Recovery | K dispozici.
[Článek 8: Změna hostitele Linuxovou aplikaci na virtuálních počítačích Azure a Azure serveru MySQL](contoso-migration-rehost-linux-vm-mysql.md) | Ukazuje, jak společnosti Contoso migruje Linuxovou aplikaci osTicket k virtuálním počítačům Azure pomocí Site Recovery a migraci databáze aplikace na instanci serveru Azure MySQL pomocí aplikace MySQL Workbench. | K dispozici.
[Článek 9: Refaktorujte aplikace na Azure Web Apps a Azure SQL database](contoso-migration-refactor-web-app-sql.md) | Ukazuje, jak společnosti Contoso migruje aplikace SmartHotel360 do webové aplikace Azure a migraci databáze aplikace na instanci serveru SQL Azure | K dispozici.
[Článek 10: Refaktorujte Linuxovou aplikaci do Azure Web Apps a Azure MySQL](contoso-migration-refactor-linux-app-service-mysql.md) | Ukazuje, jak společnosti Contoso migruje aplikace osTicket Linux do Azure Web Apps ve více lokalitách, integrovaná se službou GitHub pro průběžné doručování. Jejich migrovat databázi aplikace na instanci Azure MySQL. | K dispozici.
[Článek 11: Refaktorovat TFS na službách Azure DevOps](contoso-migration-tfs-vsts.md) | Ukazuje, jak společnosti Contoso migruje místní nasazení Team Foundation Server (TFS) a migrujte ho ke službám Azure DevOps v Azure. | K dispozici.
[Článek 12: Úprava architektury aplikace na kontejnery služby Azure a SQL Database](contoso-migration-rearchitect-container-sql.md) | Ukazuje, jak společnosti Contoso migruje a rearchitects jeho SmartHotel aplikace do Azure. Jejich úprava architektury webové vrstvy aplikace jako kontejner Windows a databáze aplikace ve službě Azure SQL Database. | K dispozici.
Článek 13: Znovu sestavte aplikaci do Azure | Ukazuje, jak společnosti Contoso znovu sestavte své aplikace SmartHotel pomocí celé řady funkcí Azure a služeb, včetně služeb App Services, Kubernetes v Azure, Azure Functions, Cognitive services a Cosmos DB. | Tento článek.

V tomto článku se migruje Contoso dvouvrstvé Windows. NET SmartHotel360 aplikace běžící na virtuálních počítačích VMware do Azure. Pokud chcete používat tuto aplikaci, se poskytuje jako open source a můžete ji stáhnout [Githubu](https://github.com/Microsoft/SmartHotel360).

## <a name="business-drivers"></a>Obchodní faktory

Vedení IT úzké spolupráci s obchodními partnery pochopit, co chtějí dosáhnout s tato migrace:

- **Adresa obchodní růst**: Contoso se rozrůstá a chce poskytovat rozlišené prostředí pro zákazníky, kteří na webech společnosti Contoso.
- **Flexibilita**: Contoso musí být schopné reagovat rychleji než změn na webu Marketplace, aby úspěch v globální ekonomiku. 
- **Škálování**: růstem podniku zvládla úspěšně, Contoso IT tým musí poskytovat systémy, které mohou rozšířit stejným tempem.
- **Náklady na**: Contoso chce minimalizovat náklady na licencování.

## <a name="migration-goals"></a>Migrace cíle

Tým cloudových Contoso má připnutou požadavky aplikace pro tuto migraci. Tyto požadavky byly použity k určení nejlepší metody migrace:
 - Aplikace ve službě Azure zůstane tak kritický, protože se ještě dnes. Měl by dobře fungují a snadno škálovat.
 - Aplikace by neměly používat komponenty IaaS. Všechno, co by měly být sestaveny pro použití PaaS nebo služeb bez serveru.
 - Sestavení aplikace by měly být spuštěny cloudové služby a kontejnery by měl být uložený v registru kontejneru soukromého podnikové úrovni v cloudu.
 - Služba rozhraní API používaná pro domácí mazlíčky fotografie by měl být přesným a spolehlivým z okolního světa, protože rozhodnutí aplikace musí být zachované jejich hotels. Žádné domácí mazlíček udělil přístup může zůstat na hotely.
 - Abyste splnili požadavky pro kanál DevOps, Contoso použije Azure DevOps pro zdrojový kód Management (SCM), s úložišti Git.  Automatizované buildy a vydání se použije k sestavení kódu a nasazení do Azure Web Apps, Azure Functions a AKS.
 - Různé kanály CI/CD jsou potřeba pro mikroslužby na back-endu a webového serveru na front-endu.
 - Back-endové služby mají různé verze cyklu z front-endové webové aplikace.  Tento požadavek splnit, nasadí dvě různé kanály DevOps.
 - Contoso potřebuje schválení správy všech front-endu pro vývoj pro web a to musí poskytnout kanálu CI/CD.


## <a name="solution-design"></a>Návrh řešení

Po Připnutí dolů cíle a požadavky, Contoso navrhuje, zkontrolujte nasazení řešení a identifikuje proces migrace, včetně služeb Azure, které se použije pro migraci.

### <a name="current-app"></a>Aktuální aplikaci

- Místní aplikace SmartHotel360 je rozvrstvená na dva virtuální počítače (WEBVM a SQLVM).
- Virtuální počítače jsou umístěné na hostiteli VMware ESXi **contosohost1.contoso.com** (verze 6.5)
- Správu prostředí VMware zajišťuje vCenter Server verze 6.5 (**vcenter.contoso.com**), běžící na virtuálním počítači.
- Contoso má místní datacentrum (contoso-datacenter), s řadičem domény v místním (**contosodc1**).
- Po dokončení migrace místních virtuálních počítačů v datacentru společnosti Contoso kdy.


### <a name="proposed-architecture"></a>Navrhované architektury

- Front-endové aplikace se nasadí jako aplikace služby Azure App Services Web v primární oblasti Azure.
- Funkce Azure poskytuje nahrávání fotografií domácích a Web komunikuje pomocí této funkce.
- Funkci domácí mazlíčky fotografii využívá Vision API služeb Cognitive Services a služby cosmos DB.
- Back-end serveru je sestavena pomocí mikroslužeb. Ty se nasadí do kontejnerů využívá Azure Kubernetes service (AKS).
- Kontejnery se vytvořené pomocí Azure DevOps a vloženy do Azure Container Registry (ACR).
- Prozatím Contoso ručně nasadit webové aplikace a funkce kódu pomocí sady Visual Studio
- Mikroslužby se nasadí pomocí Powershellového skriptu, který volá nástroje příkazového řádku Kubernetes.

    ![Architektura scénáře](./media/contoso-migration-rebuild/architecture.png) 

  
### <a name="solution-review"></a>Kontrola řešení

Contoso vyhodnotí navrhovaných návrhu společně vložením seznam výhody a nevýhody.

**Posouzení** | **Podrobnosti**
--- | ---
**V oblasti IT** | Použití řešení bez serveru a PaaS nasazení začátku do konce výrazně snižuje čas správy, který musíte zadat Contoso.<br/><br/> Přesun na architekturu mikroslužeb umožňuje společnosti Contoso na snadno rozšiřte řešení v čase.<br/><br/> Nové funkce můžete do online režimu bez narušení běžného některý z existujících základů kódu řešení.<br/><br/> Webová aplikace bude mít nakonfigurovanou více instancí se žádný jediný bod selhání.<br/><br/> Tak, aby aplikace dokáže zpracovat provoz hromadí se povolí automatické škálování.<br/><br/> S přechodem na služby modelu PaaS Contoso ho vyřadit. zastaralé řešení používá operační systém Windows Server 2008 R2.<br/><br/> Cosmos DB má předdefinovanou odolnost proti chybám, které není nutné nic konfigurovat společností Contoso. To znamená, že datová vrstva již není jediným bodem převzetí služeb při selhání.
**Nevýhody** | Kontejnery jsou složitější než jiné možnosti migrace. Učení můžou být problémy pro doménu Contoso.  Ale zavádí novou úroveň složitosti, která poskytuje spoustu vylepšení, přestože křivky.<br/><br/> Provozní tým ve společnosti Contoso musí rychle pochopit práci a podporu Azure, kontejnery a mikroslužby pro aplikaci.<br/><br/> Contoso neimplementovala plně DevOps pro celé řešení. Contoso je potřeba zamyslet, které pro nasazení služby AKS, functions a App Services.



### <a name="migration-process"></a>Proces migrace

1. Contoso zřizování ACR a AKS a cosmos DB.
2. Zřizování infrastrukturu pro nasazení, včetně webové aplikace Azure, účet úložiště, funkce a rozhraní API. 
3. Po vytvoření infrastruktury na místě, že budete vytvářet jejich mikroslužeb imagí kontejnerů pomocí Azure DevOps, které odesílá je služby ACR.
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

Tady je Contoso potřebuje pro tento scénář:

**Požadavky** | **Podrobnosti**
--- | ---
**Předplatné Azure** | Společnosti Contoso vytvořit odběry během dřívější článek. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Pokud vytvoříte bezplatný účet, jste správcem vašeho předplatného a můžete provádět všechny akce.<br/><br/> Pokud použijete existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.
**Infrastruktura Azure** | [Zjistěte, jak](contoso-migration-infrastructure.md) Contoso nastavení infrastruktury Azure.
**Požadavky pro vývojáře** | Contoso potřebuje následující nástroje na pracovní stanici pro vývojáře:<br/><br/> - [Visual Studio 2017 Community Edition: Verze 15.5](https://www.visualstudio.com/)<br/><br/> .NET povolenou sadu funkcí.<br/><br/> [Git](https://git-scm.com/)<br/><br/> [Azure PowerShell](https://azure.microsoft.com/downloads/)<br/><br/> [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)<br/><br/> [Docker CE (Windows 10) nebo Docker EE (Windows Server)](https://docs.docker.com/docker-for-windows/install/) nastavit používání kontejnerů Windows.



## <a name="scenario-steps"></a>Kroky scénáře

Tady je způsob spuštění migrace Contoso:

> [!div class="checklist"]
> * **Krok 1: Zřízení AKS a ACR**: Contoso zřídí spravovaný cluster AKS a Azure container registry pomocí Powershellu
> * **Krok 2: Vytvoření kontejnerů Dockeru**: nastavte průběžnou Integraci pro kontejnery Dockeru pomocí Azure DevOps a vložit je služby ACR.
> * **Krok 3: Nasazení mikroslužeb back-end**: nasadí zbývající infrastrukturu, která bude využívat back-end mikroslužeb.
> * **Krok 4: Nasazení front-endové infrastruktury**: nasadí infrastrukturu front-endu, prozkoumány úložiště objektů blob pro domácí mazlíčky telefony, Cosmos DB a rozhraní API pro zpracování obrazu.
> * **Krok 5: Migrace back-endu**: nasazování mikroslužeb a spustit v AKS k migraci back-endu.
> * **Krok 6: Publikování aplikace front-endu**: publikování aplikace SmartHotel360 a službou Azure App service, aplikace Function App, která bude volána domácí mazlíčky službou.



## <a name="step-1-provision-back-end-resources"></a>Krok 1: Zřizování back endové prostředky

Spusťte skript nasazení k vytvoření spravovaného clusteru Kubernetes AKS a Azure Container Registry (ACR) Správce společnosti Contoso.

- Pokyny k tomuto oddílu používají **SmartHotel360-Azure-backend** úložiště.
- **SmartHotel360-Azure-backend** veškerého softwaru pro tuto část nasazení obsahuje úložiště GitHub.

### <a name="prerequisites"></a>Požadavky

1. Dříve, než začnou, správce společnosti Contoso Ujistěte se, že veškerý software prerequisitie v nainstalovaný ve vývojovém počítači, který se používá pro nasazení.
2. Jejich naklonujte úložiště místním vývojovém počítači s aplikací Git: **klonu gitu https://github.com/Microsoft/SmartHotel360-Azure-backend.git**


### <a name="provision-aks-and-acr"></a>Zřizování virtuálních počítačů AKS a ACR

Zřízení správce společnosti Contoso:

1.  Otevřete složku, pomocí nástroje Visual Studio Code a přesune **/nasazení/k8s** adresáře, který obsahuje skript **obecné aks-env.ps1**.
2. Spouštějí se skript k vytvoření spravovaného clusteru Kubernetes AKS a služby ACR.

    ![AKS](./media/contoso-migration-rebuild/aks1.png)
 
3.  S otevřeným souborem aktualizují parametr $location **eastus2**a soubor uložte.

    ![AKS](./media/contoso-migration-rebuild/aks2.png)

4. Kliknutím na **zobrazení** > **integrovaný terminál** otevřít integrovaný terminál v kódu.

    ![AKS](./media/contoso-migration-rebuild/aks3.png)

5. V prostředí PowerShell integrovaný terminál kterým se přihlašují do Azure pomocí příkazu Connect-AzureRmAccount. [Další informace](https://docs.microsoft.com/powershell/azure/get-started-azureps) o zahájení práce s prostředím PowerShell.

    ![AKS](./media/contoso-migration-rebuild/aks4.png)

6. Ověřování pomocí Azure CLI **az login** příkaz a postupujte podle pokynů k ověření pomocí svého webového prohlížeče. [Další informace](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest) o přihlášení pomocí Azure CLI.

    ![AKS](./media/contoso-migration-rebuild/aks5.png)

7. Spusťte následující příkaz, předejte název skupiny prostředků ContosoRG, název AKS clusteru smarthotel-aks-eus2 a název nového registru.

    ```
    .\gen-aks-env.ps1  -resourceGroupName ContosoRg -orchestratorName smarthotelakseus2 -registryName smarthotelacreus2
    ```
    ![AKS](./media/contoso-migration-rebuild/aks6.png)

8. Azure vytvoří jiné skupiny prostředků obsahující prostředky clusteru AKS.

    ![AKS](./media/contoso-migration-rebuild/aks7.png)

9. Po dokončení nasazení nainstalují **kubectl** nástroj příkazového řádku. Nástroj je již nainstalován na Azure Cloud Shell.

    **az aks install-cli**

10. Připojení ke clusteru, ověřte spuštěním **kubectl get uzly** příkazu. Uzel se stejným názvem jako virtuální počítač ve skupině automaticky vytvořený prostředek.

    ![AKS](./media/contoso-migration-rebuild/aks8.png)

11. Spusťte následující příkaz, který spouští řídicí panel Kubernetes: 

    **Procházet az aks--& gt; resource-group ContosoRG – název smarthotelakseus2**

12. Na řídicí panel se otevře na kartě prohlížeče. Toto je tunelového propojení pomocí Azure CLI. 

    ![AKS](./media/contoso-migration-rebuild/aks9.png)




## <a name="step-2-configure-the-back-end-pipeline"></a>Krok 2: Konfigurace back-end kanálu

### <a name="create-an-azure-devops-project-and-build"></a>Vytvořit projekt Azure DevOps a sestavení

Contoso vytvoří projekt Azure DevOps a nakonfiguruje sestavení CI k vytvoření kontejneru a předá ji do služby ACR. Pokyny v této části používají [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) repository.r

1. Z visualstudio.com, vytvoří novou (**contosodevops360.visualstudio.com**) a nakonfigurujte ho na používání Git.

2. Vytvořte nový projekt (**SmartHotelBackend**) pomocí Gitu pro správu verzí a agilní pracovního postupu.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts1.png) 


3. Importují [úložiště GitHub se vzorovými](https://github.com/Microsoft/SmartHotel360-Azure-backend.git).

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts2.png)
    
4. V **kanály**, kliknou **sestavení**a vytvořit nový kanál pomocí Azure úložiště Git jako zdroje z úložiště. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts3.png)

6. Výběru se spustí s prázdnou úlohu.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts4.png)  

7. Vyberou **hostované Linuxové verze Preview** profilace sestavení.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts5.png) 
 
8. V **fáze 1**, přidávají **Docker Compose** úloh. Tento úkol sestavení Dockeru compose.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts6.png) 

9. Opakujte a přidejte další **Docker Compose** úloh. Kontejnery tohohle nahraje do služby ACR.

     ![Azure DevOps](./media/contoso-migration-rebuild/vsts7.png) 

8. Vyberte první úlohu (sestavení) a konfigurace sestavení pomocí předplatného Azure, autorizace a služby ACR. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts8.png)

9. Zadejte cestu **docker compose.yaml** souboru **src** složce úložiště. Výběrem imagí služby sestavení a zahrnout nejnovější značky. Při změně akci k **sestavování imagí služby**, název úlohy Azure DevOps se změní na **automaticky sestavovacích služeb**

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts9.png)

10. Nyní nakonfigurujte druhý Docker úlohy (push). Výběrem předplatného a **smarthotelacreus2** služby ACR. 

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts10.png)

11. Znovu, zadejte ho do souboru docker-compose.yaml a vyberte **nahrávání imagí služby** a obsahují nejnovější značky. Když akce změny **nahrávání imagí služby**, název úlohy Azure DevOps se změní na **služby nabízených oznámení automaticky**

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts11.png)

12. S úlohami Azure DevOps nakonfigurované Contoso uloží kanálu sestavení a spustí proces sestavení.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts12.png)

13. Kliknutí na úlohu sestavení ke kontrole průběhu.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts13.png)

14. Po dokončení sestavení ACR zobrazuje nové úložiště, které se vyplní s kontejnery, mikroslužby používá.

    ![Azure DevOps](./media/contoso-migration-rebuild/vsts14.png)


### <a name="deploy-the-back-end-infrastructure"></a>Nasazení back endová infrastruktura

Vytvoření clusteru AKS a imagí Dockeru, který je sestaven správce společnosti Contoso teď nasadit rest infrastrukturu, která bude využívat back-end mikroslužeb.

- Podle pokynů v části použití [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) úložiště.
- V **/deploy/k8s/arm** složce neexistuje jeden skript k vytvoření všech položek. 

Nasadí následujícím způsobem:

1. Otevřete příkazový řádek pro vývojáře a pomocí příkazu az přihlášení pro předplatné Azure.
2. Souboru deploy.cmd používají k nasazení prostředků Azure ve skupině prostředků ContosoRG a EUS2 oblasti tak, že zadáte následující příkaz:

    **.\deploy.cmd azuredeploy - c eastus2 ContosoRG**

    ![Nasazení back-endu](./media/contoso-migration-rebuild/backend1.png)

2. Na webu Azure Portal dojde k zachycení připojovací řetězec pro každou databázi, pro pozdější použití.

    ![Nasazení back-endu](./media/contoso-migration-rebuild/backend2.png)

### <a name="create-the-back-end-release-pipeline"></a>Kanály pro vydávání back-end

Správce společnosti Contoso, postupujte takto:

- Nasaďte NGINX kontroler příchozího přenosu dat a povolení příchozí komunikace služeb.
- Mikroslužby nasaďte do clusteru AKS.
- Jako první krok, aktualizujte připojovací řetězce pro mikroslužby pomocí Azure DevOps. Nakonfigurujte nový kanál pro Azure DevOps vydání k nasazení mikroslužeb.
- Pokyny v této části používají [SmartHotel360-Azure-Backend](https://github.com/Microsoft/SmartHotel360-Azure-backend) úložiště.
- Všimněte si, že některá nastavení konfigurace (třeba Active Directory B2C) nejsou pokryté v tomto článku. Přečtěte si další informace o těchto nastaveních v úložišti.

Vytváření kanálu:

1. Pomocí sady Visual Studio, aktualizují **/deploy/k8s/config_local.yml** soubor s informacemi o připojení databáze, že jste si předtím poznamenali.

    ![Připojení databáze](./media/contoso-migration-rebuild/back-pipe1.png)

2. Otevřete Azure DevOps a v SmartHotel360 projektu v **verze**, kliknou **+ nový kanál**.

    ![Nový kanál](./media/contoso-migration-rebuild/back-pipe2.png)

3. Kliknutím na **prázdný úlohy** spuštění kanálu bez šablony.
4. Poskytují názvy fáze a kanálu.

      ![Název fáze](./media/contoso-migration-rebuild/back-pipe4.png)

      ![Název kanálu](./media/contoso-migration-rebuild/back-pipe5.png)

5. Přidávají artefakt.

     ![Přidání artefaktu](./media/contoso-migration-rebuild/back-pipe6.png)

6. Vyberou **Git** jako zdroj zadejte a určete projektu, zdroje a hlavní větev pro aplikace SmartHotel360.

    ![Nastavení artefaktu](./media/contoso-migration-rebuild/back-pipe7.png)

7. Kliknou na odkaz úloh.

    ![Úloha odkazu](./media/contoso-migration-rebuild/back-pipe8.png)

8. Přidávají nové úlohy Azure PowerShell tak, aby skript prostředí PowerShell můžete spouštět v prostředí Azure.

    ![Prostředí PowerShell v Azure](./media/contoso-migration-rebuild/back-pipe9.png)

9. Vyberte předplatné Azure pro úlohy a vyberte **deploy.ps1** skript z úložiště Git.

    ![Spuštění skriptu](./media/contoso-migration-rebuild/back-pipe10.png)


10. Argumenty, přidejte do skriptu. skript se odstranit veškerý obsah clusteru (s výjimkou **příchozího přenosu dat** a **kontroler příchozího přenosu dat**) a nasazování mikroslužeb.

    ![Argumenty skriptu](./media/contoso-migration-rebuild/back-pipe11.png)

11. Nastavte preferovanou verzi prostředí Azure PowerShell na nejnovější verzi a uložte kanálu.
12. Při přechodu zpět **Release** stránce a ručně vytvořit nové vydání.

    ![Nová vydaná verze](./media/contoso-migration-rebuild/back-pipe12.png)

13. Klikněte na vydání po jeho vytvoření a v **akce**, kliknou **nasadit**.

      ![Nasazení verze](./media/contoso-migration-rebuild/back-pipe13.png)  

14. Po dokončení nasazení, spusťte následující příkaz a zkontrolujte stav služeb, pomocí Azure Cloud Shell: **kubectl získejte služby**.


## <a name="step-3-provision-front-end-services"></a>Krok 3: Zřízení front-endové služby

Správce společnosti Contoso budete muset nasadit infrastrukturu, která bude používat front-endové aplikace. Vytvoří kontejner objektů blob úložiště pro ukládání imagí domácí mazlíčky; databáze Cosmos k ukládání dokumentů s informací o domácí mazlíčky; a rozhraní API pro zpracování obrazu pro web. 

Pokyny k tomuto oddílu používají [SmartHotel360. veřejný web](https://github.com/Microsoft/SmartHotel360-public-web) úložiště.

### <a name="create-blob-storage-containers"></a>Vytvoření kontejnerů služby Blob Storage

1.  Na webu Azure portal, otevřete účet úložiště, který byl vytvořen a klikne na tlačítko **objekty BLOB**.
2.  Vytvoří nový kontejner (**Mazlíčci**) s úroveň veřejného přístupu nastavena na kontejner. Uživatelé odešlete své domácí mazlíčky fotky do tohoto kontejneru.

    ![Úložiště objektů blob](./media/contoso-migration-rebuild/blob1.png)

3. Vytvoří druhý nový kontejner s názvem **nastavení**. Soubor se všechna nastavení front-endu aplikací budou umístěny v tomto kontejneru.

    ![Úložiště objektů blob](./media/contoso-migration-rebuild/blob2.png)

4. Dojde k zachycení podrobností pro přístup k účtu úložiště v textovém souboru, pro pozdější použití.

    ![Úložiště objektů blob](./media/contoso-migration-rebuild/blob2.png)

### <a name="provision-a-cosmos-database"></a>Zřízení databáze Cosmos

Správce společnosti Contoso zřízení databáze Cosmos má být použit pro domácí mazlíčky informace.

1. Vytvoří **služby Azure Cosmos DB** na webu Azure Marketplace.

    ![Databáze Cosmos](./media/contoso-migration-rebuild/cosmos1.png)

2. Zadejte název (**contosomarthotel**), vyberte rozhraní SQL API a jeho následné uložení do skupiny prostředků produkční ContosoRG v hlavní oblasti USA – východ 2.

    ![Databáze Cosmos](./media/contoso-migration-rebuild/cosmos2.png)

3. Nová kolekce si přidají do databáze, výchozí kapacita a propustnost.

    ![Databáze Cosmos](./media/contoso-migration-rebuild/cosmos3.png)


4. Poznamenejte si informace o připojení pro databázi pro budoucí použití. 

    ![Databáze Cosmos](./media/contoso-migration-rebuild/cosmos4.png)


### <a name="provision-computer-vision"></a>Zřizování pro počítačové zpracování obrazu

Správce společnosti Contoso zřídit rozhraní API pro počítačové zpracování obrazu. Rozhraní API bude volat funkci, k vyhodnocení obrázky odeslaný uživateli.

1. Vytvoří **pro počítačové zpracování obrazu** instance na webu Azure Marketplace.

     ![Computer Vision](./media/contoso-migration-rebuild/vision1.png)

2. Zřizování rozhraní API (**smarthotelpets**) v prostředku produkčních skupině ContosoRG v hlavní oblasti USA – východ 2.

    ![Computer Vision](./media/contoso-migration-rebuild/vision2.png)

3. Nastavení připojení pro rozhraní API ukládají do textového souboru pro pozdější použití.

     ![Computer Vision](./media/contoso-migration-rebuild/vision3.png)


### <a name="provision-the-azure-web-app"></a>Zřízení webové aplikace Azure

Správce společnosti Contoso zřízení webové aplikace pomocí webu Azure portal.


1. Vyberou **webovou aplikaci** na portálu.

    ![Webová aplikace](media/contoso-migration-rebuild/web-app1.png)

2. Poskytuje název aplikace (**smarthotelcontoso**), spusťte ho na Windows a umístěte ho v provozní skupině pro prostředky **ContosoRG**. Uživatel vytvořit novou instanci Application Insights pro monitorování aplikací...

    ![Název webové aplikace](media/contoso-migration-rebuild/web-app2.png)

3. Po jejich dokončení, přejděte na adresu aplikace, které chcete zkontrolovat, že je úspěšně vytvořen.

4. Teď na webu Azure Portal vytvářejí přípravný slot pro kód. kanálu se nasadí do tohoto slotu. Tím se zajistí, že kód není uvést do provozu, dokud se správci provádět vydanou verzi.

    ![Přípravný slot webové aplikace](media/contoso-migration-rebuild/web-app3.png)



### <a name="provision-the-azure-function-app"></a>Zřídit aplikace Azure function app

Správce společnosti Contoso na webu Azure Portal zřídit aplikace Function App.

1. Vyberou **aplikace Function App**.

    ![Vytvoření aplikace function app](./media/contoso-migration-rebuild/function-app1.png)

2. Poskytuje název aplikace (**smarthotelpetchecker**). Tyto aplikace umístit v provozní skupině prostředků **ContosoRG**. Nastavují hostování místem, kde **plánu Consumption**a umístěte ji v oblasti východní USA 2. Je vytvořen nový účet úložiště spolu s instanci Application Insights pro monitorování.

    ![Nastavení aplikace funkcí](./media/contoso-migration-rebuild/function-app2.png)


3. Po nasazení aplikace, přejděte na adresu aplikace chcete zkontrolovat, že je úspěšně vytvořen.


## <a name="step-4-set-up-the-front-end-pipeline"></a>Krok 4: Nastavení front-endu kanálu

Správce společnosti Contoso vytvořit dva různé projekty pro front-end webový server. 

1. V Azure DevOps, vytvořte projekt **SmartHotelFrontend**.

    ![Front-endového projektu](./media/contoso-migration-rebuild/function-app1.png)

2. Importují [SmartHotel360 front-endu](https://github.com/Microsoft/SmartHotel360-public-web.git) úložiště Git do nového projektu.
3. Pro aplikace Function App, vytvořte nový projekt Azure DevOps (SmartHotelPetChecker) a importujte [PetChecker](https://github.com/Microsoft/SmartHotel360-PetCheckerFunction ) úložiště Git do tohoto projektu.

### <a name="configure-the-web-app"></a>Konfigurace webové aplikace

Teď nakonfigurovat správce společnosti Contoso ve webové aplikaci využívat prostředky společnosti Contoso.

1. Připojení k Azure DevOps project a naklonujte úložiště místně do vývojového počítače.
2. V sadě Visual Studio, otevřete složku, kterou chcete zobrazit všechny soubory v úložišti.

    ![Úložiště souborů](./media/contoso-migration-rebuild/configure-webapp1.png)

3. Změny konfigurace podle potřeby aktualizují.

    - Při spuštění webové aplikace, hledá **SettingsUrl** nastavení aplikace.
    - Tato proměnná musí obsahovat adresu URL odkazující na konfigurační soubor.
    - Ve výchozím nastavení používá je veřejný koncový bod.

4.  Tyto aktualizace /config-sample.json/sample.json souboru.

    - To je konfigurační soubor pro web, při použití veřejného koncového bodu.
    - Upraví **adresy URL** a **pets_config** oddíly s hodnotami pro koncové body rozhraní API AKS, účty úložiště a databáze Cosmos.
    - Adresy URL by měla odpovídat názvu DNS novou webovou aplikaci, která se vytvoří Contoso.
    - Pro společnost Contoso, toto je **smarthotelcontoso.eastus2.cloudapp.azure.com**.

    ![Nastavení JSON](./media/contoso-migration-rebuild/configure-webapp2.png)

5. Po aktualizaci souboru se přejmenovat **smarthotelsettingsurl**a nahrajte ho do blog storage vytvořili dříve.

    ![Přejmenování a nahrávání](./media/contoso-migration-rebuild/configure-webapp3.png)

6. Klikněte na soubor, který má získat adresu URL. Adresa URL slouží aplikace, když ji stáhne konfigurační soubory.

    ![Adresa URL aplikace](./media/contoso-migration-rebuild/configure-webapp4.png)

7. V **appsettings. Production.JSON** souboru, aktualizují **SettingsURL** na adresu URL nového souboru.

    ![Aktualizovat adresu URL](./media/contoso-migration-rebuild/configure-webapp5.png)

### <a name="deploy-the-website-to-the-azure-app-service"></a>Nasadit do Azure App Service

Správce společnosti Contoso teď můžete publikovat na webu.


1. Otevření Azure DevOps a **SmartHotelFrontend** projektu v **Buildy a vydání**, kliknou **+ nový kanál**.
2. Vyberou **Azure DevOps Git** jako zdroj.
3. Vyberou **ASP.NET Core** šablony.
4. Zkontrolujte kanálu a zkontrolujte, že **publikování webových projektů** a **Zip publikovat projekty** jsou vybrány.

    ![Nastavení kanálu](./media/contoso-migration-rebuild/vsts-publishfront2.png)

5. V **triggery**, povolte průběžnou integraci a přidejte hlavní větve. Tím se zajistí, že pokaždé, když řešení obsahuje nový kód potvrzení do hlavní větve kanálu sestavení spustí.

    ![Nepřetržitá integrace](./media/contoso-migration-rebuild/vsts-publishfront3.png)

6. Kliknutím na **Uložit & frontu** ke spuštění sestavení.
7. Po dokončení sestavení, konfiguraci kanálu pro vydávání pomocí **nasazení služby Azure App Service**.
8. Název fáze poskytují **pracovní**.

    ![Název prostředí](./media/contoso-migration-rebuild/vsts-publishfront4.png)

9. Přidejte artefakt a vyberte sestavení, které jsou právě nakonfigurovali.

     ![Přidání artefaktu](./media/contoso-migration-rebuild/vsts-publishfront5.png)

10. Klikněte na ikonu blesku v artefaktu a umožnit průběžné nasazování.

    ![Průběžné nasazování](./media/contoso-migration-rebuild/vsts-publishfront6.png)
11. V **prostředí**, kliknou **úlohy 1, 1 úloha** pod **pracovní**.
12. Po výběru předplatného a název aplikace se otevřou **nasazení služby Azure App Service** úloh. Nasazení je nakonfigurován pro použití **pracovní** slot pro nasazení. To se automaticky vytvoří kód ke kontrole a schválení na této pozici.

     ![Slot](./media/contoso-migration-rebuild/vsts-publishfront7.png)

13. V **kanálu**, přidávají nové fázi.

    ![Nové prostředí](./media/contoso-migration-rebuild/vsts-publishfront8.png)

14. Vyberou **nasazení služby Azure App Service se slotem**a pojmenujte prostředí **Prod**.
15. Kliknutí na **úlohy 1, 2 úlohy**a vyberte předplatné, název služby app service a **pracovní** slot.

    ![Název prostředí](./media/contoso-migration-rebuild/vsts-publishfront10.png)

16. Odstraňují **nasazení služby Azure App Service do slotu** z kanálu. Existuje byl umístěn v předchozích krocích.

    ![Odebrat z kanálu](./media/contoso-migration-rebuild/vsts-publishfront11.png)

13. Uložení kanálu. V kanálu, klikněte na **po nasazení podmínky**.

    ![Po nasazení](./media/contoso-migration-rebuild/vsts-publishfront12.png)

14. Umožňují **po schválení**, a přidejte nový zájemce dev jako schvalovatele.

    ![Po nasazení schválení](./media/contoso-migration-rebuild/vsts-publishfront13.png)

15. V kanálu sestavení jejich ručně aktivovat sestavení. Tím se aktivuje nový kanál pro vydávání verzí, které nasadí do přípravného slotu webu. Pro společnost Contoso, je adresa URL pro slot **https://smarthotelcontoso-staging.azurewebsites.net/**.
16. Po dokončení sestavení a vydání nasadí do přihrádky, Azure DevOps e-mailem vedoucí vývoje pro schválení.
17. Kliknutí vedoucí vývoje **zobrazení schválení**a můžete schválit nebo odmítnout žádost na portálu Azure DevOps.

    ![E-mail pro schválení](./media/contoso-migration-rebuild/vsts-publishfront14.png)

16. Zájemce díky komentář a schválí. Spustí se přepnutí z **pracovní** a **produkční** sloty a přesune sestavení do produkčního prostředí.

    ![Schválení a prohození](./media/contoso-migration-rebuild/vsts-publishfront15.png)

17. Příkazy neskončí prohození.

    ![Dokončit prohození](./media/contoso-migration-rebuild/vsts-publishfront16.png)

18. Kontroly týmu **produkční** slotu zkontrolujte, že je webová aplikace v produkčním prostředí na **https://smarthotelcontoso.azurewebsites.net/**.


### <a name="deploy-the-petchecker-function-app"></a>Nasazení aplikace PetChecker – funkce

Správce společnosti Contoso následujícím způsobem nasazení aplikace.

1. Pomocí připojení k Azure DevOps project, naklonujte úložiště lokálně na vývojovém počítači.
2. V sadě Visual Studio, otevřete složku, kterou chcete zobrazit všechny soubory v úložišti.
3. Otevřou **src/PetCheckerFunction/local.settings.json** soubor a přidat nastavení aplikace pro rozhraní API pro počítačové zpracování obrazu, úložiště a databáze Cosmos.

    ![Nasazení funkce](./media/contoso-migration-rebuild/function5.png)

4. Potvrdit kód a synchronizovat ji zpět do Azure DevOps doručením (push) změny.
5. Přidejte nový kanál sestavení a vyberte **Azure DevOps Git** zdroje.
6. Vyberou **ASP.NET Core (.NET Framework)** šablony.
7. Přijměte výchozí hodnoty pro šablonu.
8. V **triggery**a pak vyberte k **aktivovat nepřetržitou integraci**a klikněte na tlačítko **Uložit & frontu** ke spuštění sestavení.
9. Po úspěšném sestavení vytváří kanál pro vydávání verzí, přidání **nasazení služby Azure App Service se slotem**.
10. Jejich název prostředí **Prod**a vyberte předplatné. Nastavují **typ aplikace** k **aplikace Function App**a název služby app service jako **smarthotelpetchecker**.

    ![Function App](./media/contoso-migration-rebuild/petchecker2.png)

11. Přidání artefaktu **sestavení**.

    ![Artefakt](./media/contoso-migration-rebuild/petchecker3.png)

12. Umožňují **trigger průběžného nasazování**a klikněte na tlačítko **Uložit**.
13. Kliknutím na **nové sestavení do fronty** Spustit úplný kanál CI/CD.
14. Po nasazení funkce zobrazuje na webu Azure Portal, se **systémem** stav.

    ![Nasazení funkce](./media/contoso-migration-rebuild/function6.png)


7. Procházení k aplikaci otestovat, že aplikace prerequisite Checker domácí mazlíček funguje dle očekávání v [ http://smarthotel360public.azurewebsites.net/Pets ](http://smarthotel360public.azurewebsites.net/Pets).
8. Kliknutí na miniaturu pro nahrání obrázku.

    ![Nasazení funkce](./media/contoso-migration-rebuild/function7.png)

9. První fotografií, které chtějí zkontrolovat je malý pes.

    ![Nasazení funkce](./media/contoso-migration-rebuild/function8.png)

10. Aplikace vrátí zprávu přijetí.

    ![Nasazení funkce](./media/contoso-migration-rebuild/function9.png)






## <a name="review-the-deployment"></a>Zkontrolujte nasazení

S migrovaných prostředků v Azure Contoso teď je potřeba plně zprovoznění a zabezpečení nové infrastruktury.

### <a name="security"></a>Zabezpečení

- Contoso potřebuje k zajištění zabezpečeného nové databáze. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-security-overview).
- Aplikace je potřeba aktualizovat pomocí certifikátů SSL. Odpovědět na 443 by měl znovu nasadit instanci kontejneru.
- Contoso byste zvážit použití trezoru klíčů k ochraně tajných kódů pro své aplikace Service Fabric. [Další informace](https://docs.microsoft.com/azure/service-fabric/service-fabric-application-secret-management).

### <a name="backups-and-disaster-recovery"></a>Zálohování a zotavení po havárii

- Contoso je potřeba zkontrolovat požadavkům zálohování pro Azure SQL Database. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
- Contoso měli uvažovat o implementaci SQL skupiny převzetí služeb při selhání pro zajištění regionální převzetí služeb při selhání pro databázi. [Další informace](https://docs.microsoft.com/azure/sql-database/sql-database-geo-replication-overview).
- Contoso můžete využít geografickou replikaci pro SKU úrovně premium služby ACR. [Další informace](https://docs.microsoft.com/azure/container-registry/container-registry-geo-replication).
- Cosmos DB, Zálohuje automaticky. Společnost Contoso může [Další](https://docs.microsoft.com/azure/cosmos-db/online-backup-and-restore) o tomto procesu.

### <a name="licensing-and-cost-optimization"></a>Optimalizace nákladů a licencování

- Po nasazení jsou všechny prostředky, by měl Contoso přiřadit Azure značky na základě jejich [plánování infrastruktury](contoso-migration-infrastructure.md#set-up-tagging).
- Všechny licence je integrovaná do náklady na služby PaaS, které Contoso využívá. To se odečte od smlouvy EA.
- Azure Cost Management licencuje Cloudyn, pobočka Microsoftu vám umožní společnosti Contoso. Je řešení správy nákladů multicloudové, který vám umožní využívat a spravovat Azure a další cloudové prostředky.  [Další informace](https://docs.microsoft.com/azure/cost-management/overview) o službě Azure Cost Management.

## <a name="conclusion"></a>Závěr

V tomto článku Contoso znovu sestaví SmartHotel360 aplikace v Azure. Místní aplikace, které front-endový virtuální počítač znovu sestavit, aby službě Azure App Services Web Apps. Back-endu aplikace je sestavena pomocí mikroslužeb, které jsou nasazené do kontejnerů, které jsou spravované službou Azure Kubernetes Service (AKS). Contoso rozšířené funkce pomocí aplikace pro domácí mazlíčky fotografii.




