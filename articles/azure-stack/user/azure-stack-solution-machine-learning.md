---
title: Vytvoření edge řešení strojového učení s využitím Azure a Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit edge machine learning řešení pomocí Pythonu s využitím Azure a Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: mabrigg
ms.reviewer: Anjay.Ajodha
ms.openlocfilehash: 5a1f0c0ee8a9f6ef6871e19e7722e09f4e96ba7f
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53142066"
---
# <a name="tutorial-create-an-edge-machine-learning-solution-with-azure-and-azure-stack"></a>Kurz: Vytvoření edge řešení strojového učení s využitím Azure a Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Zjistěte, jak vytvořit edge řešení strojového učení s využitím Azure a Azure Stack.

V tomto kurzu vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Vytvoření účtu úložiště a kontejner pro vyčistit data uložená.
> - Ubuntu dat virtuálního počítače VĚDY vytvořte na webu Azure portal.
> - Nasazení služby Azure Machine Learning v Azure k vytvoření a trénování modelů.
> - Vytvoření účtů služby Azure Machine Learning.
> - Nasazení a použití služby Azure Container Registry.
> - Nasazení clusteru Kubernetes do služby Azure Stack.
> - Vytvoření účtu úložiště Azure Stack a fronty úložiště pro data.
> - Vytvoření nové služby Azure Stack funkce můžete do Azure přesunout vyčištění dat ze služby Azure Stack.

**Kdy použít toto řešení**

 -  Vaše organizace používá přístup DevOps, nebo je plánována pro v blízké budoucnosti.
 -  Chcete implementovat postupů CI/CD ve vaší implementace služby Azure Stack a veřejným cloudem.
 -  Chcete sloučit kanálu CI/CD v cloudu a místních prostředích.
 -  Chcete mít možnost k vývoji aplikací pomocí cloudu nebo místních služeb.
 -  Budete chtít využít konzistentní vývojářské dovednosti v cloudu a místních aplikací.

> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí a povolení ten jediný hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace kdekoli.  
> 
> Dokument White Paper [aspekty návrhu pro hybridní aplikace](https://aka.ms/hybrid-cloud-applications-pillars) kontroly pro navrhování, nasazování a provozování pilířů kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) hybridní aplikace. Aspekty návrhu při optimalizaci návrhu hybridní aplikace, minimalizovat problémy v produkčním prostředí.

## <a name="prerequisites"></a>Požadavky

Několik komponent jsou nutné k vytvoření tento případ použití a může trvat nějakou dobu připravit:

 -  Výrobce OEM nebo Hardware partnerem Azure můžou nasadit produkčního prostředí Azure Stack a všichni uživatelé můžou nasadit ASDK

 -  Operátor Azure stacku musí také nasadit službu App Service, vytvořte plány a nabídky, vytvořit tenanta předplatného a přidejte image Windows serveru 2016

 -  Hybridní síť s a služby App Service je požadované nastavení (Další informace o [integrace aplikací s virtuálními sítěmi.](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet))

 -  Privátní [sestavení a verze agenta](https://docs.microsoft.com/vsts/pipelines/agents/agents?view=vsts) pro integrace VSTS musí být nastavena před nasazením (ujistěte se, že všechny existující komponenty použít splňovat požadavky před zahájením.)

Předchozí znalosti Azure a Azure Stack je povinný. Další informace než budete pokračovat, začněte s těmito tématy:

 -  [Úvod do Azure](https://azure.microsoft.com/overview/what-is-azure/)

 -  [Klíčové koncepty služby Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-key-features)

 -  [Průvodci řešením hybridní CI/CD služby Azure Stack](/azure/azure-stack/user/azure-stack-solution-pipeline)

**Azure**

 -  Předplatné Azure (vytvořit [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))

 -  Nová adresa URL webové aplikace vytvořené [webovou aplikaci](https://docs.microsoft.com/azure/app-service/app-service-web-overview) v Azure

 -  Nasazení [Azure Container Services (ACS) Kubernetes v Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

 -  Nasazení služby Azure Machine Learning [kurzu část 2](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)


**Azure Stack**

 -  Integrovaný systém Azure Stack nebo nasazení Azure Stack Development Kit.

    - Najít pokyny k instalaci služby Azure Stack na [instalace sady Azure Stack Development Kit](../asdk/asdk-install.md).
     - [https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1](https://github.com/mattmcspirit/azurestack/blob/master/deployment/ConfigASDK.ps1) Tato instalace může vyžadovat několik hodin.

 -  Nasazení [služby App Service](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-deploy) služeb PaaS do služby Azure Stack

 -  A [plánu nebo nabídky](https://docs.microsoft.com/azure/azure-stack/azure-stack-plan-offer-quota-overview) v rámci prostředí Azure Stack

 -  A [Tenanta předplatného](https://docs.microsoft.com/azure/azure-stack/azure-stack-subscribe-plan-provision-vm) v rámci prostředí Azure Stack

 -  Image virtuálního počítače Ubuntu serveru (k dispozici v [Azure Stack Marketplace](https://buildazure.com/2016/05/04/azure-marketplace-ubuntu-server-16-04-lts/)) tento virtuální počítač bude vytvořen v rámci předplatného tenanta ve službě Azure Stack jako agent soukromé sestavení, jakož i virtuální počítače Kubernetes. Pokud tuto bitovou kopii není k dispozici, operátor Azure stacku vám můžou pomoct, aby že tato funkce je přidána do prostředí. (Nepoužívejte 18.04 sestavení ubuntu, protože není aktuálně podporováno.)

 -  Webové aplikace v rámci předplatného tenanta (Všimněte si nových URL webové aplikace pro pozdější použití.)

 -  Nasazení sady založené na Linuxu VSTS privátní sestavení agenta virtuálního počítače, v rámci předplatného tenanta

 -  Nasazení [Azure Container Services (ACS) Kubernetes v Azure stacku](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-template-kubernetes-deploy)

**Nástroje pro vývojáře**

 -  [Pracovní prostor VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/sign-up-for-visual-studio-team-services) (procesu registrace do služby vytvoří projekt s názvem "MyFirstProject")

 -  [Visual Studio 2017](https://docs.microsoft.com/visualstudio/install/install-visual-studio) instalace a [VSTS](https://www.visualstudio.com/docs/setup-admin/team-services/connect-to-visual-studio-team-services) přihlašování

 -  [Místní klon](https://www.visualstudio.com/docs/git/gitquickstart) projektu

 -  [Linux podsystému pro Windows 10](https://docs.microsoft.com/windows/wsl/install-win10) instalaci (pro prostředí BASH a SSH)

 -  [Docker pro Windows](https://download.docker.com/win/stable/Docker%20for%20Windows%20Installer.exe) instalace

 -  [Aplikace Azure Machine Learning Workbench (preview)](https://aka.ms/azureml-wb-msi) instalace

 -  [Python](https://www.python.org/ftp/python/3.7.0/python-3.7.0rc1-amd64.exe) prostředí instalace

**VSTS**

 -  **Účet VSTS.** Rychle nastavte průběžnou integraci pro sestavení, testování a nasazení. Další informace o účtech VSTS najdete v tématu [vytvořit účet VSTS](https://docs.microsoft.com/vsts/accounts/create-account-msa-or-work-student?view=vsts).

 -  **Úložiště kódu.** Použití stávajících úložišť kódu v Githubu, BitBucket, DropBox, Onedrive a TFS, platformu VSTS můžou využívat více úložišť kódu zjednodušit vývoj kanálu. Další informace o kódu úložišť najdete v části [Začínáme s Git a VSTS](https://docs.microsoft.com/vsts/git/gitquickstart?view=vsts&tabs=visual-studio) kurzu.

 -  **Připojení služby.** Připojte se k externí a vzdálené služby k provedení úloh pro testování, build nebo nasazení. Další informace o VSTS najdete v článku Služba připojení [koncové body služeb pro sestavování a vydávání](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints?view=vsts).

 -  **Definice sestavení.** Definujte procesy automatické sestavení a vytvořte sadu úloh prostřednictvím katalogu úloh. Další informace o sestavení naleznete v tématu definice [vytvořte definici sestavení](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) dokumentaci.

 -  **Definice verzí.** Definujte proces nasazení pro kolekci prostředí, ve které jsou nasazené aplikace artefakty. Další informace o verzi najdete v článku definice [vytvoření definice verze](https://docs.microsoft.com/vsts/build-release/actions/ci-cd-part-1?view=vsts) dokumentaci.

 -  **Fondu agentů sestavení Linux hostované VSTS.** Rychle sestavovat, testovat a nasazovat aplikace pomocí Microsoft, spravovat a udržovat hostovaného agenta. Další informace o prostředí sestavení VSTS najdete v článku agentů [hostovaných agentů](https://docs.microsoft.com/vsts/build-release/concepts/agents/hosted?view=vsts) dokumentaci.

## <a name="step-1-create-a-storage-account"></a>Krok 1: vytvořit účet úložiště

Vytvoření účtu úložiště a kontejner pro vyčistit data uložená.

1.  Přihlaste se k [ *webu Azure portal*](https://portal.azure.com/).

2.  Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **všechny služby**. Přejděte dolů k položce **úložiště** a zvolte **účty úložiště**. V **účty úložiště** okna zvolte **přidat**.

3.  Zadejte název účtu úložiště.

    > [!Note]  
    > Názvy účtů úložiště musí mít délku 3 až 24 znaků a můžou obsahovat jenom číslice a malá písmena. Název účtu úložiště musí být jedinečný v rámci Azure. Na webu Azure portal upozorní, pokud se vybraný název účtu úložiště se už používá.

4.  Zadejte model nasazení, který se má použít: **Resource Manager**.

5.  Vyberte typ účtu úložiště: **Obecné účely V1**, pak zadejte úroveň výkonu: **Standardní**.

6.  Vyberte možnost replikace pro účet úložiště: **GRS**.

7.  Vyberte předplatné, nový účet úložiště.

8.  Zadejte novou skupinu prostředků nebo vyberte existující skupinu prostředků.

9.  Vyberte zeměpisné umístění účtu úložiště.

10. Vyberte **Vytvořit** a vytvořte účet úložiště.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image1.png)

11.  Zvolte účet úložiště, nedávno vytvořili.

12.  Vyberte na **objekty BLOB**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image2.png)

13.  Vyberte na **+ kontejner** a vyberte na **kontejneru**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image3.png)

14.  Zadejte název kontejneru **uploadeddata** a zvolte typ přístupu **kontejneru**.

15.  Vyberte na **vytvořit**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image4.png)

## <a name="step-2-create-a-data-science-virtual-machine"></a>Krok 2: Vytvořit virtuální počítač pro datové vědy

Ubuntu dat virtuálního počítače VĚDY vytvořte na webu Azure Portal.

1.  Přihlaste se k webu Azure portal [https://portal.azure.com](https://portal.azure.com/)

2.  Vyberte na **+ nová** odkaz a vyhledejte "Data Science virtuálních počítačů pro Linux Ubuntu CSP

    ![Alternativní text](media/azure-stack-solution-machine-learning/image5.png)

1.  Zvolte **virtuální počítač pro datové vědy pro Linux (Ubuntu)** v seznamu a použijte na obrazovce pokyny k vytvoření datové VĚDY.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image6.png)

> [!Important]  
> Zvolte **heslo** jako **typ ověřování**.

Nové datové VĚDY umístěte do stejné skupiny prostředků jako na nově vytvořený účet úložiště. Všechny objekty Edge ML jsou nasazené v Azure v rámci této skupiny prostředků.

1.  V nastavení nakonfigurovat volitelné funkce

    a.  Vyberte **účtu úložiště** vytvořili dříve.

    b.  Vytvořte nový **virtuální sítě**, **podsítě**, a **veřejnou IP adresu** má ve výchozím nastavení vytvořit název založený na názvu skupiny prostředků.

    c.  Vytvořte nový **NSG** ho automaticky vytvořila to správné pravidlům už používá.

    d.  Pro **účet úložiště diagnostiky**, vyberte dříve vytvořeného účtu úložiště.

    > [!Note]  
    > Pomocí AAD povolené a nakonfigurované pro předplatné Azure je možné také povolit spravovaných identit pro prostředky Azure.

2.  Vyberte **OK**.

### <a name="connect-to-the-dsvm"></a>Připojte se k datové VĚDY

Po vytvoření datové VĚDY, připojte se k virtuálnímu počítači ze subsystému Windows pro Linux.

```Bash  
    ssh <user>@<DSVM Public IP>
```

1.  Zadejte se zobrazí výzva k potvrzení připojení virtuálního počítače.

2.  Zadejte heslo, které vytvořili pro datové VĚDY.

### <a name="update-the-dsvm"></a>Aktualizace datové VĚDY 

```Bash  
sudo su 
apt-get update 
apt-get -y upgrade 
apt-get -y dist-upgrade 
apt-get -y autoremove
```

## <a name="step-3-deploy-azure-machine-learning-services"></a>Krok 3: Nasazení aplikace Azure Machine Learning Services

Nasazení aplikace Azure Machine Learning Services v Azure.

Služby Azure Machine Learning (Preview) představují komplexní integrované řešení pro datové vědy a pokročilé analýzy. Pomáhá profesionálním datovým vědcům připravovat data, vyvíjet experimenty a nasazovat modely na úrovni cloudu.

Tato část vysvětluje:

> [!div class="checklist"]
> - Vytvoření účtů služeb pro služby Azure Machine Learning
> - Instalace a přihlášení do aplikace Azure Machine Learning Workbench
> - Vytvoření projektu v aplikaci Workbench
> - Spuštění skriptu v tomto projektu
> - Přístup k rozhraní příkazového řádku

Jako součást portfolia Microsoft Azure vyžadují služby Azure Machine Learning předplatné Azure. Chcete-li získat předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Odpovídající oprávnění je potřeba vytvořit prostředky, jako jsou skupiny prostředků, virtuální počítače a dalších zdrojů.

V následujících operačních systémech může nainstalovat aplikaci Azure Machine Learning Workbench:

 -  Windows 10 nebo Windows Server 2016
 -  macOS Sierra nebo High Sierra

## <a name="step-4-create-azure-machine-learning-services"></a>Krok 4: Vytvoření služby Azure Machine Learning

Vytvoření účtů služby Azure Machine Learning.

Pomocí webu Azure portal ke zřízení účtů Azure Machine Learning:

1.  Přihlaste se k [webu Azure portal](https://portal.azure.com/) pomocí přihlašovacích údajů pro předplatné Azure, které má být použit. Chcete-li získat předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

    ![Alternativní text](media/azure-stack-solution-machine-learning/image7.png)

1.  Vyberte tlačítko **Vytvořit prostředek** (+) v levém horním rohu portálu.

    ![Vytvořit prostředek na webu Azure Portal](media/azure-stack-solution-machine-learning/image8.png)

1.  Do panelu vyhledávání zadejte **Machine Learning**. Vyberte výsledek hledání s názvem **Experimentování ve službě Machine Learning (Preview)**.

    ![Vyhledání služby Azure Machine Learning](media/azure-stack-solution-machine-learning/image9.png)

1.  V **experimentování ve službě Machine Learning** podokno, posuňte se dolů a vyberte **vytvořit** a začněte definovat účet experimentování ve službě.

    ![Azure Machine Learning – vytvoření účtu Experimentování](media/azure-stack-solution-machine-learning/image10.png)

1.  V **experimentování v ML** podokně Konfigurace účtu experimentování ve službě Machine Learning.

    | Nastavení | Navrhovaná hodnota pro tento kurz | Popis |
    |---------------------------------------|----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Název účtu služby Experimentování | Jedinečný název | Zadejte jedinečný název, který identifikuje tento účet. Použijte název oddělení nebo projekt, který experiment nejlépe identifikuje. Název musí mít délku od 2 do 32 znaků. Může obsahovat jenom alfanumerické znaky a znak spojovníku (-). |
    | Předplatné | Odběr | Zvolte předplatné Azure, které pro experiment použít. Pokud existuje více předplatných, vyberte odpovídající předplatné, ve kterém se prostředek účtuje. |
    | Skupina prostředků | Skupina prostředků | Použít existující skupinu prostředků v předplatném nebo zadejte název pro vytvoření pro tento účet experimentování novou skupinu prostředků. |
    | Umístění | Oblast nejbližší uživatelům | Zvolte umístění co nejblíže uživatelům a datovým prostředkům. |
    | Počet licencí | 2 | Zadejte počet licencí. Informace o tom, jak [licence ovlivňují ceny](https://azure.microsoft.com/pricing/details/machine-learning/).<br><br>Pro účely tohoto rychlého startu je potřeba jenom dvě licence. Licence je možné podle potřeby přidávat nebo odebírat na webu Azure Portal. |
    | Účet úložiště | Jedinečný název | Vyberte **Vytvořit nový** a zadejte název pro vytvoření [účtu úložiště Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal). Název musí být dlouhý 3 až 24 znaků a může obsahovat pouze alfanumerické znaky. Můžete také vybrat **použít existující** a ze seznamu vyberte existující účet úložiště. Účet úložiště je povinný a slouží k ukládání artefaktů projektu a dat historie spuštění. |
    | Účet aplikace Workspace pro experimentování | IrisGarden<br>(název používaný v kurzech) | Zadejte název pracovního prostoru pro tento účet. Název musí mít délku od 2 do 32 znaků. Může obsahovat jenom alfanumerické znaky a znak spojovníku (-). Tento pracovní prostor obsahuje nástroje potřebné k vytvoření, správě a publikování experimentů. |
    | Přiřazení vlastníka pracovního prostoru | Účet | Vyberte vlastní účet jako vlastníka pracovního prostoru. |
    | Vytvoření účtu služby Správa modelů | **zaškrtnutí** | Vytvořte účet služby Správa modelů. Ten se použije k nasazení a správě modelů, jako v reálném čase webové služby. <br><br>Zatímco je volitelné, vytváří se účet správy modelů ve stejnou dobu jako účet služby experimentování ve službě se doporučuje. |
    | Název účtu | Jedinečný název | Vyberte jedinečný název, který identifikuje účet služby Správa modelů. Použít oddělení nebo projektu název, který experiment nejlépe identifikuje. Název musí mít délku od 2 do 32 znaků. Může obsahovat jenom alfanumerické znaky a znak spojovníku (-). |
    | Cenová úroveň služby Správa modelů | **DEVTEST** | Vyberte **vybraná žádná cenová úroveň** zadat cenovou úroveň pro nový účet služby Správa modelů. Pro úsporu nákladů vyberte cenovou úroveň DEVTEST, pokud je k dispozici v rámci předplatného (omezená dostupnost). Jinak vyberte cenovou úroveň S1. Zvolte Výběr uložte výběr cenové úrovně. |
    | Připnout na řídicí panel | Kontrola | Vyberte **připnout na řídicí panel** možnost, povolíte snadné sledování účtu experimentování ve službě Machine Learning na úvodní stránce řídicího panelu na webu Azure portal. |

    ![Konfigurace účtu Experimentování ve službě Machine Learning](media/azure-stack-solution-machine-learning/image11.png)

1.  Vyberte **Vytvořit** a zahajte proces vytváření účtu Experimentování společně s účtem služby Správa modelů.

    ![Konfigurace účtu Experimentování ve službě Machine Learning](media/azure-stack-solution-machine-learning/image12.png)

    To může chvíli trvat vytvoření účtu služby. Zkontrolujte stav procesu nasazení tak, že vyberete ikonu upozornění (zvonek) na panelu nástrojů webu Azure portal.

    ![Oznámení portálu Azure Portal](media/azure-stack-solution-machine-learning/image13.png)

### <a name="install-and-log-in-to-workbench"></a>Instalace a přihlášení do aplikace workbench 

Aplikace Azure Machine Learning Workbench je dostupná pro Windows nebo macOS. Viz seznam [podporovaných platforem](https://docs.microsoft.com/azure/machine-learning/service/quickstart-installation).

> [!Warning]  
> Instalace může trvat přibližně hodinu na dokončení.

1.  Stáhněte a spusťte nejnovější instalační program aplikace Workbench.

    > [!Important]  
    > Stáhněte celý instalační program na disk a spusťte jej. Nespouštějte jej přímo z widgetu stahování v prohlížeči.<br>**Na Windows:<br>**  . Stáhněte soubor [AmlWorkbenchSetup.msi](https://aka.ms/azureml-wb-msi).<br>b. Dvakrát klikněte na stažený instalační program v Průzkumníku souborů.

1.  Použijte na obrazovce pokynů k dokončení instalačního programu.

    **Instalace může trvat až 30 minut.**
    
    `Windows: C:\\Users\\<user>\\AppData\\Local\\AmlWorkbench`
    
    Instalační program stáhnete a nastavit všechny potřebné závislosti, jako je Python, Miniconda a další související knihovny. Tato instalace zahrnuje také nástroj příkazového řádku Azure napříč platformami nebo rozhraní příkazového řádku Azure.

1.  Spusťte aplikaci Workbench výběrem tlačítka **Spustit aplikaci Workbench** na poslední obrazovce instalačního programu.

    Pokud je instalační program zavřeli, spustit **Machine Learning Workbench** zástupce na ploše.

1.  Vyberte **přihlásit se účtem Microsoft** k ověření pomocí Azure Machine Learning Workbench. Použijte stejné přihlašovací údaje použité na webu Azure Portal k vytvoření účtů experimentování a správa modelů.

    Jakmile se přihlásíte, aplikace Workbench použije první účet experimentování najde v předplatných Azure a zobrazí všechny pracovní prostory a projekty přidružené k tomuto účtu.

    > [!Tip]  
    > Přepnout na jiný účet experimentování ve službě pomocí ikony v levém dolním rohu okna aplikace Workbench.

### <a name="create-a-new-project-in-workbench"></a>Vytvoření nového projektu v aplikaci workbench

1.  Otevřete aplikaci Azure Machine Learning Workbench a přihlaste se v případě potřeby.

    - Na Windows, spustit **Machine Learning Workbench** zástupce na ploše.

    - V systému macOS vyberte **Azure ML Workbench** na hlavním panelu.

1.  V podokně **PROJEKTY** vyberte symbol plus (+) a zvolte **Nový projekt**.

    ![Nový pracovní prostor](media/azure-stack-solution-machine-learning/image14.png)

1.  Vyplňte pole formuláře a výběrem tlačítka **Vytvořit** v aplikaci Workbench vytvořte nový projekt.

    | Pole | Navrhovaná hodnota pro tento kurz | Popis |
    |-------------------------------------|------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Název projektu | myIris | Zadejte jedinečný název, který identifikuje tento účet. Použít oddělení nebo projektu název, který experiment nejlépe identifikuje. Název musí mít délku od 2 do 32 znaků. Může obsahovat jenom alfanumerické znaky a znak spojovníku (-). |
    | Adresář projektu | c:\Temp\ | Zadejte adresář, ve kterém se projekt vytvoří. |
    | Popis projektu | Ponechte prázdné | Volitelné pole, které je užitečné pro popis projektů. |
    | Adresa URL úložiště GIT Visualstudio.com | Ponechte prázdné | Volitelné pole. Přidružte projekt úložiště Git na úložiště Azure pro správu zdrojového kódu a spolupráci. |
    | Vybraný pracovní prostor | IrisGarden (pokud existuje) | Vyberte pracovní prostor vytvořený pro účet experimentování na webu Azure Portal. <br>V rámci rychlého startu, je uvedena pracovního prostoru s názvem IrisGarden. V opačném případě pomocí pracovního prostoru s názvem účtu služby experimentování ve službě nebo názvem preferovaného účtu. |
    | Šablona projektu | Klasifikace Iris | Šablony obsahují skripty a data použitá k produkt zkoumat. Tato šablona obsahuje skripty a data potřebná pro tento rychlý start a další kurzy na tomto webu dokumentace. |

    ![Nový projekt](media/azure-stack-solution-machine-learning/image15.png)

1.  Vytvoří se nový projekt a otevře se řídicí panel projektu s tímto projektem. Prozkoumejte domovskou stránku projektu, zdroje dat, poznámkové bloky a soubory zdrojového kódu.

    ![Otevřený projekt](media/azure-stack-solution-machine-learning/image16.png)

### <a name="attach-a-dsvm-compute-target"></a>Připojit cílové výpočetní prostředí DSVM

Po vytvoření datové VĚDY, připojte ji do projektu Azure ML.

1.  Z v rámci aplikace Azure ML Workbench spusťte rozhraní příkazového řádku Azure ML Workbench tak, že vyberete **souboru**->**otevřete PowerShell**

    ![Alternativní text](media/azure-stack-solution-machine-learning/image17.png)

1.  Po otevření řádku Powershellu pomocí následujícího příkazu:

    ```PowerShell  
        az login
    ```

1.  Zobrazí se následující řádek:

     ![Alternativní text](media/azure-stack-solution-machine-learning/image18.png)

1.  Přejděte na web podle popisu v příkazovém řádku a zadejte kód, který je k dispozici.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image19.png)

1.  Pokračovat po zobrazení výzvy vyberte a pak vyberte účet Azure, ke které experimentální účet služby Azure ML je přidružená.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image20.png)

1.  Rozhraní příkazového řádku Azure ML Workbench vám potom pošle následující řádek:

    ![Alternativní text](media/azure-stack-solution-machine-learning/image21.png)

1.  Při přihlášení ML účtu a pracovního prostoru se zobrazí jako úspěšně dokončený, připojte datové VĚDY.

    ```PowerShell  
        az ml computetarget attach remotedocker --name <compute target name> --address <ip address or FQDN> --username <admin username> --password <admin password>
    ```

    Zobrazí se následující upozornění:

    ![Alternativní text](media/azure-stack-solution-machine-learning/image22.png)

    ```PowerShell  
        # prepare the Docker image on the DSVM 
        az ml experiment prepare -c <compute target name>
    ```

Tento proces bude nějakou dobu trvat a vygeneruje značné množství textu, stáhne různých imagí dockeru, registruje je a potom nainstaluje potřebný kód a aplikace na ně.

Na tomto DSVM teď dají spouštět experimenty.

### <a name="create-a-data-preparation-package"></a>Vytvoření balíčku pro přípravu dat

Pak můžete začněte připravovat data v aplikaci Azure Machine Learning Workbench. Každá transformace, provést v aplikaci Workbench je uložen ve formátu JSON v balíčku pro přípravu dat místní (\*.dprep souboru). Tento balíček pro přípravu dat je primárním kontejnerem pro práci na přípravě dat v aplikaci Workbench.

Tento balíček pro přípravu dat je možné předat později do modulu runtime, jako je místní C\#/CoreCLR, Scala/Spark nebo Scala/HDI.

1.  Výběrem ikony složky otevřete zobrazení Soubory a pak výběrem otevřete soubor **iris.csv**.

    Tento soubor obsahuje tabulku s 5 sloupci a 50 řádky. Čtyři sloupce obsahují čísla. Pátý sloupec je cílový sloupec řetězcového typu. Žádný sloupec neobsahuje název záhlaví.

    ![iris.csv](media/azure-stack-solution-machine-learning/image23.png)

1.  V **Zobrazení dat** vyberte symbol plus (**+**) a přidejte nový zdroj dat. Otevře se stránka **Přidat zdroj dat**.

    ![Zobrazení dat v aplikaci Azure Machine Learning Workbench](media/azure-stack-solution-machine-learning/image24.png)

1.  Vyberte **textové soubory (\*CSV, \*.json, \*.txt.,...)** .

    ![Zdroj dat v aplikaci Azure Machine Learning Workbench](media/azure-stack-solution-machine-learning/image25.png)

1.  Vyberte **Další**.

2.  Přejděte k souboru **iris.csv**a vyberte **Dokončit**. Tím se pro parametry, jako jsou oddělovač a datové typy, použijí výchozí hodnoty.

    > [!Important]  
    > Vyberte **iris.csv** soubor z aktuálního adresáře projektu pro účely tohoto cvičení. V opačném případě pozdější kroky nemusí podařit.

    ![Výběr iris](media/azure-stack-solution-machine-learning/image26.png)

1.  Nový soubor s názvem `*iris-1.dsource` se vytvoří. Soubor s jedinečným způsobem názvem `-1` protože ukázkový projekt už obsahuje neočíslovaný **iris.dsource** souboru.

    Soubor se otevře a zobrazí se data. Řada záhlaví sloupců od **Sloupec1** k **Column5**, se automaticky přidá do této datové sady. Přejděte do dolní části a Všimněte si, že poslední řádek datové sady je prázdný. Řádek je prázdný, z důvodu zalomení řádku navíc v souboru CSV.

    ![Zobrazení dat iris](media/azure-stack-solution-machine-learning/image27.png)

1.  Vyberte tlačítko **Metriky**. Vygenerují a zobrazí se histogramy.

    Přepněte zpět do zobrazení dat tak, že vyberete **Data** tlačítko.

    ![Zobrazení dat iris](media/azure-stack-solution-machine-learning/image28.png)

1.  Podívejte se na histogramy. Pro každý sloupec se vypočítala úplná sada statistik.

    ![Zobrazení dat iris](media/azure-stack-solution-machine-learning/image29.png)

1.  Začněte vytvářet balíček pro přípravu dat výběrem tlačítka **Připravit**. Otevře se dialogové okno **Připravit**.

    Ukázkový projekt obsahuje **iris.dprep** přípravy dat, který je vybraný ve výchozím nastavení.

    ![Zobrazení dat iris](media/azure-stack-solution-machine-learning/image30.png)

1.  Vytvořit nový balíček pro přípravu dat výběrem **+ nový balíček pro přípravu dat** z nabídky.

    ![Zobrazení dat iris](media/azure-stack-solution-machine-learning/image31.png)

1.  Zadejte novou hodnotu názvu balíčku (použijte **iris-1**) a pak vyberte **OK**.

    Nový balíček pro přípravu dat s názvem **iris-1.dprep** je vytvořen a otevřen v editoru přípravy dat.

    ![Zobrazení dat iris](media/azure-stack-solution-machine-learning/image32.png)

    V dalším kroku přípravy dat, je potřeba.

1.  Výběrem záhlaví jednotlivých sloupců umožníte úpravu jejich textu. Pak jednotlivé sloupce přejmenujte následujícím způsobem:

    V pořadí, zadejte **Sepal Length**, **Sepal Width**, **Petal Length**, **Petal Width**, a **druhů** pro pět sloupců v uvedeném pořadí.

    ![Přejmenování sloupců](media/azure-stack-solution-machine-learning/image33.png)

1.  Počet jedinečných hodnot:

    1.  Vyberte sloupec **Species**.

    2.  Kliknutím pravým tlačítkem ho vyberte.

    3.  Vyberte **četnost hodnot** z nabídky.

        Pod daty se otevře podokno **Kontroly**. Zobrazí se histogram se čtyři pruhy. Cílový sloupec obsahuje čtyři různé hodnoty: **Iris-virginica**, **Iris-versicolor**,**Iris-setosa**a **(null)** hodnotu.

    ![Výběr možnosti Četnost hodnot](media/azure-stack-solution-machine-learning/image34.png)

    ![Histogram počtu hodnot](media/azure-stack-solution-machine-learning/image35.png)

1.  Pokud chcete vyfiltrovat hodnoty null, vyberte pruh (null) a pak symbol minus (**-**).

    Záznam (null) pak zešedne, což značí, že je vyfiltrovaný.

    ![Vyfiltrování hodnot null](media/azure-stack-solution-machine-learning/image36.png)

1.  Všimněte si jednotlivých kroků přípravy dat, které jsou podrobně popsané v podokně **KROKY**. Jsou přejmenování sloupců a jsou vyfiltrování řádků s hodnotou null, je každá akce zaznamenána jako krok přípravy dat. Upravte jednotlivé kroky k jejich nastavení, změnit pořadí kroků a odebrat kroky.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image37.png)

1.  Zavřete editor přípravy dat. Výběrem ikony **x** na kartě **iris-1** s ikonou grafu kartu zavřete. Práce se automaticky uloží do **iris-1.dprep** uvedené v části souboru **Příprava dat** záhlaví.

    ![Zavřít](media/azure-stack-solution-machine-learning/image38.png)

### <a name="generate-python-code-to-invoke-a-data-preparation-package"></a>Vygenerování kódu Pythonu k vyvolání balíčku pro přípravu dat

Výstup balíčku pro přípravu dat můžete prozkoumat přímo v Pythonu nebo v poznámkovém bloku Jupyter. Balíčky je možné spouštět napříč různými moduly runtime, včetně místního Pythonu, Spark (i v Dockeru) a HDInsight.

1.  Na kartě Příprava dat vyhledejte soubor **iris-1.dprep**.

2.  Klikněte pravým tlačítkem na soubor **iris-1.dprep** a v místní nabídce vyberte **Vygenerovat soubor s kódem pro přístup k datům**.

    ![Generování kódu](media/azure-stack-solution-machine-learning/image39.png)

    Nový soubor s názvem **iris-1.py** otevře s následujícími řádky kódu vyvolávajícími logiku, vytvořili jako balíček pro přípravu dat:

    ```Python
    # Use the Azure Machine Learning data preparation package
    from azureml.dataprep import package

    # Use the Azure Machine Learning data collector to log various metrics
    from azureml.logging import get_azureml_logger
    logger = get_azureml_logger()

    # This call will load the referenced package and return a DataFrame.
    # If run in a PySpark environment, this call returns a
    # Spark DataFrame. If not, it will return a Pandas DataFrame.
    df = package.run('iris-1.dprep', dataflow_idx=0)
    # Remove this line and add code that uses the DataFrame
    df.head(10)
    ```

    V závislosti na kontextu, ve kterém je tento kód spuštěný představuje drep různý druh datového rámce:

    -  Při spuštění v modulu runtime Pythonu se použije [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).

    -  Při spuštění v kontextu Spark se použije [Spark DataFrame](https://spark.apache.org/docs/latest/sql-programming-guide.html).

### <a name="review-irissklearnpy-and-the-configuration-files"></a>Kontrola skriptu iris_sklearn.py a konfiguračních souborů

1.  V otevřeném projektu, vyberte **soubory** tlačítko (ikona složky) v podokně úplně vlevo otevřete seznam souborů ve složce projektu.

    ![Otevření projektu aplikace Azure Machine Learning Workbench](media/azure-stack-solution-machine-learning/image40.png)

1.  Vyberte soubor skriptu Pythonu **iris_sklearn.py**.

    ![Výběr skriptu](media/azure-stack-solution-machine-learning/image41.png)

    Kód se v aplikaci Workbench otevře na nové kartě textového editoru.

    > [!Note]  
    > Zobrazí kód nemusí být přesně stejný jako předchozí kód protože tento ukázkový projekt se často aktualizuje.

    ![Otevření souboru](media/azure-stack-solution-machine-learning/image42.png)

1.  Projděte si kód skriptu Pythonu, abyste se seznámili se stylem kódování.

    Skript **iris_sklearn.py** provádí následující úlohy:  

    -  Načte výchozí balíček pro přípravu dat **iris.dprep** pro vytvoření [pandas DataFrame](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html).

    -   Přidá náhodné funkce, aby bylo obtížnější problém vyřešit. Náhodnost je potřeba, protože Iris je malá datová sada, která se dá snadno klasifikovat s téměř 100% přesností.

    -  Používá knihovnu learning thescikit learnmachine sestaví model logistické regrese. Tato knihovna je standardně součástí aplikace Azure Machine Learning Workbench.

    -  Serializuje model vložením knihovny [pickle](https://docs.python.org/3/library/pickle.html) do souboru ve složce `outputs`.

    -  Načte serializovaný model a pak ho deserializuje zpět do paměti.

    -  Pomocí deserializovaného modelu vytvoří předpověď nového záznamu.

    -  Vykreslí dva grafy – chybovou matici a příjemce roc provozu křivky charakteristiku (roc s více TŘÍDAMI), pomocí [matplotlib](https://matplotlib.org/) knihovny a pak je uloží v theoutputsfolder. Pokud není zřejmé, nainstalujte tuto knihovnu v prostředí.

    -  Regularizace rychlost a přesnost modelu v historii spuštění vykreslí automaticky. V celém průběhu se pomocí objektu `run_logger` zaznamenává do protokolů míra regularizace a přesnost modelu.

### <a name="run-irissklearnpy-in-the-local-environment"></a>Spuštění skriptu iris_sklearn.py v místním prostředí

1.  Spusťte rozhraní příkazového řádku služby Azure Machine Learning:

    1.  Spusťte aplikaci Azure Machine Learning Workbench.

    2.  V nabídce aplikace Workbench vyberte **Soubor**> **Otevřít příkazový řádek**.

    Otevře se okno rozhraní příkazového řádku (CLI) Azure Machine Learning ve složce projektu `C:\Temp\\myIris\` na Windows. Tento projekt je stejný jako ten, který je vytvořen v části 1 tohoto kurzu.

    > [!Important]  
    > Použijte toto okno rozhraní příkazového řádku k provedení dalších kroků.

1.  V okně rozhraní příkazového řádku nainstalujte knihovnu Pythonu zobrazování **matplotlib**, pokud ještě není nainstalovaný.

    Skript **iris_sklearn.py** obsahuje závislosti na dvou balíčcích Pythonu: **scikit-learn** a **matplotlib**. **Scikit-informace** balíčku se nainstaluje aplikace Azure Machine Learning Workbench pro pohodlí. Stále, instalace **matplotlib** může být nutné.

    Pokud budete pokračovat bez instalace **matplotlib**, kód v tomto kurzu můžete úspěšně spustit. Kód však nebude moct vytvořit výstup v podobě grafů chybové matice a křivky ROC s více třídami, které jsou vidět ve vizualizacích historie.

    ```CLI
    pip install matplotlib
    python -m pip install --upgrade pip
    ```

    Tato instalace trvá přibližně minutu.

1.  Vraťte se do aplikace Workbench.

2.  Vyhledejte kartu **iris_sklearn.py**.

    ![Vyhledání karty se skriptem](media/azure-stack-solution-machine-learning/image43.png)

1.  Na panelu nástrojů této karty vyberte **místní** jako spouštěcí prostředí, andiris_sklearn.pyas skript ke spuštění. Tyto hodnoty již můžou být vybrané.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image44.png)

1.  Přesuňte na pravou stranu panelu nástrojů a enter0.01in **argumenty** pole.

    Tato hodnota odpovídá regularizace modelu logistické regrese.

    ![Výběr spouštěcího prostředí local a skriptu](media/azure-stack-solution-machine-learning/image45.png)

1.  Vyberte tlačítko **Spustit**. Okamžitě se naplánuje úloha. Tato úloha se objeví v podokně **Úlohy** na pravé straně okna aplikace Workbench.

    ![Výběr spouštěcího prostředí local a skriptu](media/azure-stack-solution-machine-learning/image46.png)

    Po chvíli se stav úlohy změní z **odesílání**do **systémem**a nakonec na **dokončeno**.

1.  V textu stavu úlohy na panelu **Úlohy** vyberte **Dokončeno**.

    ![Spuštění skriptu sklearn](media/azure-stack-solution-machine-learning/image47.png)

    Automaticky otevírané okno se otevře a zobrazí text standardního výstupu (stdout) spuštění. Pokud chcete text standardního výstupu zavřít, vyberte **zavřete** (**x**) tlačítko v pravém horním rohu v automaticky otevíraném okně.

    ![Standardní výstup](media/azure-stack-solution-machine-learning/image48.png)

1.  Ve stejném stavu úlohy na **úlohy** podokně, vyberte modrý text **iris_sklearn.py \[n\] **(* n * je číslo spuštění) hned nad  **Dokončení** stavu a čas spuštění. Otevře se okno **Vlastnosti spuštění**, ve kterém se zobrazí následující informace o tomto konkrétním spuštění:

    -  Informace o **vlastnostech spuštění**

    -  **Výstupy**

    -  **Metriky**

    -  **Vizualizace**, pokud nějaké existují

    -  **Protokoly**

    Po dokončení spuštění se v automaticky otevíraném okně zobrazí následující výsledky:

    > [!Note]  
    > Protože kurz zaveden některé náhodné do trénovací sady použili náhodnost, můžou přesné výsledky mírně lišit od zde uvedených výsledků.

    ```Python  
        Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  5 2016, 11:41:13) [MSC v.1900 64 bit (AMD64)]

        Iris dataset shape: (150, 5)
        Regularization rate is 0.01
        LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
        Accuracy is 0.6792452830188679

        ==========================================
        Serialize and deserialize using the outputs folder.

        Export the model to model.pkl
        Import the model from model.pkl
        New sample: [[3.0, 3.6, 1.3, 0.25]]
        Predicted class is ['Iris-setosa']
        Plotting confusion matrix...
        Confusion matrix in text:
        [[50  0  0]
        [ 1 37 12]
        [ 0  4 46]]
        Confusion matrix plotted.
        Plotting ROC curve....

        ROC curve plotted.
        Confusion matrix and ROC curve plotted. See them in Run History details pane.

    ```

1.  Zavřete kartu **Vlastnosti spuštění** a vraťte se na kartu **iris_sklearn.py**.

1.  Opakujte pro další spuštění.

Do pole **Argumenty** zadejte řadu hodnot od `0.001` do `10`. Výběrem možnosti **Spustit** ještě několikrát spusťte kód. Hodnota argumentu změní pokaždé, když se zobrazí na model logistické Regrese v kódu, takže pokaždé, když jiné závěry.

### <a name="review-the-run-history-in-detail"></a>Podrobná kontrola historie spuštění

V aplikaci Azure Machine Learning Workbench každé spuštění skriptu zaznamená v podobě záznamu historie spuštění. Zobrazit historii spuštění určitého skriptu tak, že otevřete **spuštění** zobrazení.

1.  Pokud chcete otevřít seznam **Spuštění**, vyberte tlačítko **Spuštění** (ikona hodin) na levém panelu nástrojů. Potom vyberte **iris_sklearn.py** zobrazíte **řídicí panel spuštění** ofiris_sklearn.py.

    ![Zobrazení spuštění](media/azure-stack-solution-machine-learning/image49.png)

1.  Otevře se karta **řídicího panelu spuštění**.

    Zkontrolujte statistiky zaznamenané během různých spuštění. V horní části karty se vykreslují grafy. Každé spuštění má po sobě jdoucí čísla a podrobnosti o spuštění jsou uvedeny v tabulce v dolní části obrazovky.

    ![Řídicí panel spuštění](media/azure-stack-solution-machine-learning/image50.png)

1.  Tabulku můžete filtrovat a pak vybírat libovolné grafy, abyste zobrazili stav, dobu trvání, přesnost a míru regularizace jednotlivých spuštění.

2.  Zaškrtněte políčka vedle dvou nebo více spuštění v tabulce **Spuštění**. Výběrem tlačítka **Porovnat** otevřete podokno s podrobným porovnáním. Projděte si porovnání údajů vedle sebe.

3.  Pokud se chcete vrátit na **Řídicí panel spuštění**, vyberte tlačítko Zpět pro **Seznam spuštění** v levém horním rohu podokna **Porovnání**.

    ![Návrat na Seznam spuštění](media/azure-stack-solution-machine-learning/image51.png)

1.  Výběrem určitého spuštění otevřete jeho podrobné zobrazení. Všimněte si, že v části **Vlastností spuštění** se zobrazí statistika vybraného spuštění. Soubory zapsané do složky výstupu jsou uvedeny v **výstupy** části a stáhněte soubory z něj.

    ![Podrobnosti o spuštění](media/azure-stack-solution-machine-learning/image52.png)

V části **Vizualizace** se vykreslí dva grafy, chybová matice a křivka ROC s více třídami. Všechny soubory protokolu najdete také v části **Protokoly**.

### <a name="run-scripts-in-the-azure-machine-learning-ml-workbench-cli-window"></a>Spouštění skriptů v okně rozhraní příkazového řádku Azure Machine Learning (ML) aplikace Workbench

1.  Spusťte rozhraní příkazového řádku služby Azure Machine Learning:

    1.  Spusťte aplikaci Azure Machine Learning Workbench.

    2.  V nabídce aplikace Workbench vyberte **Soubor** > **Otevřít příkazový řádek**.

    Spustí příkazový řádek rozhraní příkazového řádku ve složce projektu `C:\\Temp\\myIris` na Windows. Jedná se o projekt, vytvořili v části 1 tohoto kurzu.

    > [!Important]  
    > Použijte toto okno rozhraní příkazového řádku k provedení dalších kroků.

1.  V okně rozhraní příkazového řádku se přihlaste k Azure. [Další informace o příkazu az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

    Tento krok přeskočte, pokud už přihlášení.

1.  Na příkazovém řádku zadejte:

    ```CLI
        az login
    ```

    Tento příkaz vrátí kód, který použijete v prohlížeči na [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin).

1.  Přejděte na [ https://aka.ms/devicelogin ](https://aka.ms/devicelogin) v prohlížeči. Zadejte kód, který jste dostali od přijaté v rozhraní příkazového řádku.

    Aplikace Workbench a rozhraní příkazového řádku používají při ověřování prostředků Azure nezávislé mezipaměti přihlašovacích údajů. Ověřování není potřeba znovu do vypršení platnosti tokenu v mezipaměti.

1.  Pokud má organizace více předplatných Azure (podnikové prostředí), nastavte předplatné, které chcete použít. Najít předplatné, nastavte ho pomocí ID předplatného a pak ho otestujte.

1.  Seznam všech použitých předplatného Azure pomocí tohoto příkazu:

    ```CLI
        az account list -o table 
    ```

    **Seznam účtů az** příkaz vrátí seznam předplatných dostupných pro přihlášení. Pokud existuje více než jeden, určete hodnotu ID předplatného pro předplatné použité.

1.  Nastavte předplatné Azure použité jako výchozí účet:

    ```CLI
        az account set -s <the-subscription-id
    ```

    Kde < subscription-id > je hodnota ID předplatného použité. Závorky nezadávejte.

1.  Potvrďte nové nastavení předplatného vyžádáním podrobností o aktuálním předplatném.

    ```CLI
        az account show
    ```

1.  V okně rozhraní příkazového řádku odešlete skript **iris_sklearn.py** jako experiment.

    Provádění skriptu iris_sklearn.py se spustí v místním výpočetním kontextu.

1.  Ve Windows:

    ```CLI
        az ml experiment submit -c local .\\iris_sklearn.py
    ```

1.  V systému macOS:

    ```CLI
        az ml experiment submit -c local iris_sklearn.py
    ```

    Výstup by měl vypadat přibližně: 

    ```
        RunId: myIris_1521077190506

    Executing user inputs .....
    ===========================

    Python version: 3.5.2 |Continuum Analytics, Inc.| (default, Jul  2 2016, 17:52:12) 
    [GCC 4.2.1 Compatible Apple LLVM 4.2 (clang-425.0.28)]

    Iris dataset shape: (150, 5)
    Regularization rate is 0.01
    LogisticRegression(C=100.0, class_weight=None, dual=False, fit_intercept=True,
            intercept_scaling=1, max_iter=100, multi_class='ovr', n_jobs=1,
            penalty='l2', random_state=None, solver='liblinear', tol=0.0001,
            verbose=0, warm_start=False)
    Accuracy is 0.6792452830188679

    ==========================================
    Serialize and deserialize using the outputs folder.

    Export the model to model.pkl
    Import the model from model.pkl
    New sample: [[3.0, 3.6, 1.3, 0.25]]
    Predicted class is ['Iris-setosa']
    Plotting confusion matrix...
    Confusion matrix in text:
    [[50  0  0]
    [ 1 37 12]
    [ 0  4 46]]
    Confusion matrix plotted.
    Plotting ROC curve....
    ROC curve plotted.
    Confusion matrix and ROC curve plotted. See them in Run History details page.

    Execution Details
    =================
    RunId: myIris_1521077190506

    ```

6.  Zkontrolujte výstup. Všechny by měly mít stejný výstup a výsledky jako při spuštění skriptu aplikace Workbench.

7.  Vraťte se do aplikace Workbench a:

    Výběrem ikony složky v levém podokně vypište soubory projektu.  Otevřete skript Pythonu **run.py**. Tento skript slouží k vytvoření smyčky různými mírami regularizace. 

    ![Vraťte se na seznam spuštění](media/azure-stack-solution-machine-learning/image53.png)

1.  Spusťte několikrát experiment s těmito mírami.

    Tento skript spustí` aniris_sklearn.pyjob` s o míry regularizace `10.0` (absurdně vysoké číslo). Skript pak snižuje rychlost do poloviny v následujícím spuštění a tak dále, dokud nedosáhne `0.005`. Skript obsahuje následující kód:

    ![Návrat na Seznam spuštění](media/azure-stack-solution-machine-learning/image54.png)

1.  Z příkazového řádku spusťte skript **run.py** následujícím způsobem:

    ```CLI
        python run.py
    ```
Tento příkaz odešle `iris_sklearn.py` několikrát s různými mírami regularizace

Když `run.py` dokončí, grafy různých metrik, které jsou zobrazeny v zobrazení seznamu historie spuštění v aplikaci workbench.

### <a name="run-scripts-in-an-ubuntu-based-data-science-virtual-machine-dsvm-on-azure"></a>Spouštění skriptů v založeného na Ubuntu dat virtuálního počítače VĚDY se v Azure

Spustit skript v kontejneru Docker ve vzdáleném počítači s Linuxem, je potřeba přístup SSH (uživatelské jméno a heslo) ke spuštění na vzdáleném počítači. Kromě toho musí být na počítači nainstalovaný a spuštěný modul Docker.

1.  Upravte vygenerovaný<your DSVM Name>.runconfigfile underaml_configand změňte rozhraní z výchozí valuePySparktoPython:

    ```yaml  
    Framework: Python
    ```
1.  Stejný příkaz jako předtím v okně rozhraní příkazového řádku pomocí cílové*<DSVM>* tentokrát pro spuštění skriptu iris_sklearn.py ve vzdáleném kontejneru Dockeru: (Náhradní <DSVM> názvem Data Science virtuálního počítače, bez závorek).

    ```CLI
        az ml experiment submit -c <DSVM> iris_sklearn.py
    ```

Příkaz provede sázen jako ve adocker pythonenvironment, s výjimkou spuštěn na vzdáleném virtuálním počítači s Linuxem. V okně CLI se zobrazí stejné výstupní informace.

### <a name="download-the-model-pickle-file"></a>Stažení souboru pickle modelu

V předchozí části kurzu se skript **iris_sklearn.py** spouštěl místně v aplikaci Machine Learning Workbench. Tento krok zajistil serializaci modelu logistické regrese pomocí oblíbeného balíčku serializace objektů Python [pickle](https://docs.python.org/3/library/pickle.html).

1.  Otevřete aplikaci Machine Learning Workbench. Otevřete **myIris** projekt vytvořený v předchozí části této série kurzů.

2.  Po otevření projektu, vyberte **soubory** tlačítko (ikona složky) v levém podokně otevřete seznam souborů ve složce projektu.

3.  Vyberte soubor **iris_sklearn.py**. V aplikaci Workbench se na nové kartě textového editoru otevře kód Pythonu.

4.  Podívejte se do souboru **iris_sklearn.py**, abyste věděli, kde se soubor pickle vygeneroval. Pomocí kombinace kláves CTRL+F otevřete dialogové okno **Vyhledávání** a potom v kódu Pythonu vyhledejte slovo **pickle**.

    Tento fragment kódu ukazuje, jak se vygeneroval výstupní soubor pickle. Výstupní soubor pickle má na disku název **model.pkl**.

    ```Python
        print("Export the model to model.pkl")
        f = open('./outputs/model.pkl', 'wb')
        pickle.dump(clf1, f)
        f.close()

    ```

1.  Vyhledejte soubor pickle modelu ve výstupních souborech některého z předchozích spuštění.

    Když **iris_sklearn.py** spuštění skriptu se souborem modelu jsou zapsána do **výstupy** složka s názvem **model.pkl**. Tato složka se nachází ve spouštěcím prostředí, spusťte skript se rozhodli a ne v místní složce projektu. 

    1. Chcete-li najít soubor, vyberte **spuštění** tlačítko (ikona hodin) v levém podokně otevřete seznam **všechna spuštění**.  

    2. Otevře se karta **Všechna spuštění**. V tabulce spuštění vyberte některé z nedávných spuštění s cílem **místní** a názvem skriptu **iris_sklearn.py**.  

    3. Otevře se podokno **Vlastnosti spuštění**. V pravé horní části podokna, Všimněte si, **výstupy** oddílu. d\. Stáhněte soubor pickle tím, zaškrtněte políčko vedle položky **model.pkl** souboru a pak vyberte **Stáhnout**. Uložte soubor do kořenové složky projektu. Tento soubor je potřeba v následujících krocích.  

    ![Stažení souboru pickle](media/azure-stack-solution-machine-learning/image55.png)

### <a name="get-scoring-script-and-schema-files"></a>Získat vyhodnocování skriptu a souborů schématu

Vyhodnocení skriptu je potřeba k nasazení webové služby se souborem modelu. Volitelně je potřeba schéma pro vstupní data webové služby. Hodnoticí skript načte soubor **model.pkl** z aktuální složky a použije ho k vytvoření nových předpovědí.

1.  Otevřete aplikaci Machine Learning Workbench. Otevřete **myIris** projekt vytvořený v předchozí části této série kurzů.

2.  Po otevření projektu, vyberte **soubory** tlačítko (ikona složky) v levém podokně otevřete seznam souborů ve složce projektu.

3.  Vyberte soubor **score_iris.py**. Otevře se skript Pythonu. Tento soubor se používá jako soubor vyhodnocení.

    ![Soubor vyhodnocení](media/azure-stack-solution-machine-learning/image56.png)

1.  Pokud chcete získat soubor schématu, spusťte skript. Na příkazovém řádku vyberte prostředí **local** a skript **score_iris.py** a pak vyberte **Spustit**.

    Tento skript vytvoří soubor JSON do **výstupy** oddílu, ve kterém je zaznamenané schéma vstupních dat vyžadované modelem.

1.  Všimněte si podokna **Úlohy** na pravé straně podokna **Řídicí panel projektu**. Počkejte, až se u nejnovější úlohy **score_iris.py** zobrazí zelený stav **Dokončeno**. Potom vyberte hypertextový odkaz **score_iris.py** u nejnovějšího spuštění úlohy a prohlédněte si podrobnosti o spuštění.

2.  V podokně **Vlastnosti spuštění** v části **Výstupy** vyberte nově vytvořený soubor **service_schema.json**. Zaškrtněte políčko vedle názvu souboru a pak vyberte **Stáhnout**. Uložte soubor do kořenové složky projektu.

3.  Vrátit na předchozí kartu a **score_iris.py** skriptu. Díky použití shromažďování dat, můžete zaznamenat vstupy modelu a předpovědi z webové služby. Následující kroky jsou zajímavé pro shromažďování dat.

4.  Projděte si kód v horní části souboru, který importuje třídu **ModelDataCollector**. Obsahuje funkci shromažďování dat modelu:

    ```Python  
        from azureml.datacollector import ModelDataCollector
    ```

1.  Projděte si následující řádky kódu ve funkci **init()**, která vytváří instanci třídy **ModelDataCollector**:

    ```Python  
        global inputs_dc, prediction_dc
        inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
        prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
    ```

1.  Projděte si následující řádky kódu ve funkci **run(input_df)**, která shromažďuje data vstupu a předpovědí:

    ```Python  
        inputs_dc.collect(input_df)
        prediction_dc.collect(pred)
    ```

Teď si připravte prostředí na zprovoznění modelu.

## <a name="step-5-deploy-and-use-azure-container-registry"></a>Krok 5: Nasazení a použití služby Azure Container Registry

Nasazení a použití služby Azure Container Registry.

Pomocí příkazu **az acr create** vytvořte registr kontejneru Azure. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. Skupina prostředků je stejná.

```CLI
    az acr create --resource-group <ResourceGroup> --name  <acrName> --sku Basic
```

### <a name="container-registry-login"></a>Přihlášení k registru kontejneru

Pomocí příkazu **az acr login** se přihlaste k instanci služby ACR. Uveďte jedinečný název zadaný pro registr kontejneru při jeho vytvoření.

```CLI
    az acr login --name <acrName>
```

Příkaz vrátí ' bylo přihlášení úspěšné zprávu po dokončení.

### <a name="prepare-to-operationalize-locally-for-development-and-testing-the-service"></a>Příprava na místní zprovoznění pro vývoj a testování služby

Použití *místní režim* nasazení ke spouštění v kontejnerech Docker v místním počítači a pro vývoj a testování.

K provedení následujících kroků pro zprovoznění modelu je potřeba, aby byl modul Docker spuštěný místně. Použití `-h` příznak na konci každého příkazu můžete zobrazit odpovídající zprávu nápovědy.

> [!Note]  
> Pokud není modul Docker místně dostupné, pokračovat vytvořením clusteru v Azure pro nasazení a zachovat clusteru pro opakované použití nebo odstranit po tomto kurzu, aby se zabránilo nenabíhaly poplatky.

> [!Note]  
> Webové služby nasazené místně se nezobrazí v seznamu služeb na webu Azure Portal. Budou spuštěné v Dockeru na místním počítači.

1.  Otevřete rozhraní příkazového řádku (CLI). V aplikaci Machine Learning Workbench v nabídce **Soubor** vyberte **Otevřít příkazový řádek**.

    Příkazový řádek se otevře v aktuálním umístění složky projektu **c:\\temp\\myIris**.

1.  Ujistěte se, že poskytovatel prostředků Azure **Microsoft.ContainerRegistry** je zaregistrován v rámci předplatného. Tohoto poskytovatele prostředků zaregistrujte před vytvořením prostředí v kroku 3. Zkontrolujte, jestli je už zaregistrovaný s použitím následujícího příkazu:

    ```CLI
        az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
    ```

    Zobrazte tento výstup:

    ```CLI
        Provider                                    Status 
        --------                                    --------
        Microsoft.Authorization                     Registered 
        Microsoft.ContainerRegistry                 Registered 
        microsoft.insights                          Registered 
        Microsoft.MachineLearningExperimentation    Registered 
    ```

    Pokud **Microsoft.ContainerRegistry** nejsou registrovány pomocí následujícího příkazu:

    ```CLI
    az provider register --namespace Microsoft.ContainerRegistry
    ```

    Registrace může trvat několik minut. Zkontrolovat stav registrace pomocí předchozího **az provider list** příkaz nebo následující příkaz:

    ```CLI
    az provider show -n Microsoft.ContainerRegistry
    ```

    Na třetím řádku výstupu se zobrazí **"registrationState": "Registrace"**. Chvíli počkejte a opakujte **zobrazit** příkaz, dokud se ve výstupu nezobrazí **"registrationState": "Registrováno.**

1.  Vytvořte prostředí. Spuštění tohoto kroku jednou pro každé prostředí.

    Následující příkaz pro nastavení vyžaduje přístup přispěvatele k předplatnému. Je vyžadován přístup přispěvatele k nasazení do skupiny prostředků. Zadejte název skupiny prostředků jako součást příkazu pro nastavení pomocí the gflag.

    ```CLI
    az ml env setup -n <new deployment environment name> --location <e.g. eastus2> -g <existing resource group name>
    ```

    Podle pokynů na obrazovce zřiďte účet úložiště pro ukládání imagí Dockeru, registr kontejnerů Azure, který obsahuje seznam imagí Dockeru, a účet Azure Application Insights, který shromažďuje telemetrická data. **Pokud používáte cswitch, příkaz navíc vytvoří i cluster Container Service**.

    Název clusteru je způsob, jak identifikovat příslušné prostředí. Umístění by mělo být stejné jako umístění účtu správy modelů vytvořili na webu Azure Portal.

    Pokud chcete ověřit úspěšné nastavení prostředí, pomocí následujícího příkazu zkontrolujte jeho stav:

    ```CLI
    az ml env show -n <deployment environment name> -g <existing resource group name>
    ```

    Ujistěte se, že "Stav zřizování" má hodnotu "ÚSPĚCH", jak je vidět, před nastavení prostředí v kroku 5:

    ![Stav zřizování](media/azure-stack-solution-machine-learning/image57.png)

1.  Nastavte prostředí.

    Po dokončení nastavení nastavte pomocí následujícího příkazu proměnné prostředí nutné k zprovoznění prostředí. Použijte stejný název prostředí použili v kroku 3. Použijte stejný název skupiny prostředků, který byl výstupem v příkazovém okně při dokončení procesu nastavení.

    ```CLI
        az ml env set -n <deployment environment name> -g <existing resource group name>
    ```

1.  Pokud chcete ověřit správné konfiguraci zprovozněné prostředí pro nasazení místní webové služby, zadejte následující příkaz:

    ```CLI
    az ml env show
    ```

    Teď vytvořte v reálném čase webové služby.

    > [!Note]  
    > Znovu použít účet služby Správa modelů a prostředí pro dalším nasazením webových služeb. Není nutné vytvořit pro každou webovou službu. K účtu nebo prostředí může být přiřazeno několik webových služeb.

### <a name="create-a-real-time-web-service-by-using-separate-commands"></a>Vytvoření webové služby v reálném čase pomocí samostatných příkazů

Jako alternativu k **az ml service vytvořit v reálném čase** dříve uvedenému, tyto kroky může provést také samostatně.

Nejprve zaregistrujte model. Potom vygenerujte manifest, sestavte image Dockeru a vytvořte webovou službu. Tento přístup poskytuje větší flexibilitu v každém kroku. Kromě toho může být znovu použít entity vygenerované v předchozích krocích.

1. Zaregistrujte model zadáním názvu souboru pickle.

    ```CLI
    az ml model register --model model.pkl --name model.pkl
    ```

    Tento příkaz vygeneruje ID modelu.

2.  Vytvořte manifest.

    K vytvoření manifestu použijte následující příkaz a zadejte výstup ID modelu z předchozího kroku: 
    
    ```CLI
    az ml manifest create --manifest-name <new manifest name> -f score_iris.py -r python -i <model ID> -s service_schema.json
    ```

    Tento příkaz vygeneruje ID manifestu.

3.  Vytvořte image Dockeru.

    K vytvoření image Dockeru, použijte následující příkaz a zadejte hodnotu výstupu ID manifestu z předchozího kroku. Závislosti systému conda, které mohou být využity také prostřednictvím `-c` přepnout. 
    
    ```CLI
    az ml image create -n irisimage --manifest-id <manifest ID> -c aml_config\conda_dependencies.yml
    ```
    
    Tento příkaz vygeneruje ID image Dockeru.

2.  Vytvořte službu.

    Pokud chcete vytvořit službu, použijte následující příkaz a zadejte výstup ID image z předchozího kroku: 
    
    ```CLI
    az ml service create realtime --image-id <image ID> -n irisapp --collect-model-data true
    ```
    
    Tento příkaz vygeneruje ID webové služby.
    
    V dalším kroku spuštění webové služby.

## <a name="step-6-deploy-a-kubernetes-cluster-to-azure-stack"></a>Krok 6: Nasazení clusteru Kubernetes do služby Azure Stack

Nasazení clusteru Kubernetes do služby Azure Stack.

Pomocí šablon Azure Resource Manageru, které jsou generovány pomocí modulu Azure Container Services (ACS) ve službě Azure Stack může nainstalovat Kubernetes. [*Kubernetes* ](https://kubernetes.io/) je open source systém pro automatizaci nasazení, škálování a správu aplikací v kontejnerech. A [ *kontejneru* ](https://www.docker.com/what-container) je součástí bitové kopie, podobně jako virtuální počítač. Na rozdíl od virtuálního počítače image kontejneru obsahuje prostředky, které potřebuje ke spuštění aplikace, jako je například kód runtime umožňující spouštění kódu, konkrétní knihovny a nastavení.

Pomocí Kubernetes, aby:

 -  Vývoj široce škálovatelné upgradovatelných, aplikací, které je možné nasadit v řádu sekund.

 -  Zjednodušit návrh aplikace a zvýšit jeho spolehlivost různými aplikacemi Helm. [*Příkaz Helm* ](https://github.com/kubernetes/helm) je balení open source nástroj, který umožňuje nainstalovat a spravovat životní cyklus aplikací Kubernetes.

 -  Snadno monitorovat a diagnostikovat stav aplikací díky škálování a upgradu funkce.

> [!Note]  
> Instalace clusteru bude trvat přibližně hodinu, prosím plán odpovídajícím způsobem.

### <a name="prerequisites-for-kubernetes-cluster-deployment"></a>Předpoklady pro nasazení clusteru Kubernetes

Chcete-li začít, zkontrolujte oprávnění a připravenosti služby Azure Stack:

1.  Ověřit, zda **vytvořit Kubernetes Cluster (Preview)** položka je dostupné v Tržišti Azure Stack. Pokud tato položka je chybějící, práci s Azure Stack operátor pro tuto položku Přidat do prostředí Azure Stack.

2.  Ověřte možnost vytvářet aplikace v tenantovi Azure Active Directory (Azure AD). Oprávnění se vyžadují pro nasazení Kubernetes.

    Pokyny pro kontrolu oprávnění najdete v tématu [ *zkontrolujte Azure Active Directory oprávnění*](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal).

3.  Generování veřejného a privátního klíče pár SSH pro přihlášení do virtuálního počítače s Linuxem v Azure stacku. Veřejný klíč je potřeba při vytváření clusteru. Pokyny najdete v části [generovat ověřovací klíč pro SSH](#generate-an-authenticatio-key-for-ssh).

4.  Zkontrolujte, zda platnost předplatného tenanta portálu Azure Stack a nejsou dost veřejné IP adresy pro přidání nové aplikace k dispozici.

    Cluster se nedá nasadit do služby Azure Stack **správce** předplatného. Použití **uživatele** předplatného.

###  <a name="generate-an-authentication-key-for-ssh"></a>Generovat ověřovací klíč pro SSH

Z v rámci subsystém Windows pro Linux relace použijte následující příkazy ke generování ověřovací klíč: 

1. Zadejte:

    ```Bash  
    ssh-keygen -t rsa
    ```
    
2. Vyberte `id_rsa` po zobrazení výzvy. 
3. Vytvořte heslo po zobrazení výzvy. Je důležité si toto heslo pro pozdější použití. 
    Výstup bude vypadat podobně jako následující: 
    
    ```Bash  
    Your identification has been saved in `id_rsa`.
    Your public key has been saved in `id_rsa.pub`. 
    The key fingerprint is: SHA256:lUtUUjzaqWqGeolEPKeBmsnrhcNGM9Dn2OxYatt05SE  <user>@<machine-name>
    The key's randomart image is:  
    ```
    ![Alternativní text](media/azure-stack-solution-machine-learning/image58.png)

4. Po vygenerování klíče, vložte informace klíče pomocí následujících příkazů: 
    ```Bash
    cat id_rsa.pub
    ```
    Výstup bude vypadat podobně jako následující: 
    ```Bash
    ssh-rsa  AAAAB3NzaC1yc2EAAAADAQABAAABAQDHaWrAktR3BlQ356T8Qvv8O2Q/U/hsXQwS9xJbuduuc9lkJwddzgmNCyM9PooZWYtGVXyHU6SC3YH1XkwqGtKhtPb03d24hmhX1euAaqIqHHSp4WgUS5s1gNsp37L8QCSGNCnF31FWavI8bnjOjccUkMowKl8iyGN++5UyQgNuynEVUbFJjrntoDL66HUu88xDxTcVB7rqDr2QKFwYJkg4HSoHYpezJd7W8kcmv33eh0xs8nlDA7Dzu7zXpyVc54bH9XtPR6EUXaQa+UqKaNlQNiJqEs+1X/zNuK9V6NLVNiO0h3jCHI3K8o4VnZyRKHCQProasiiPLUUJ6SF/RTLN  <user>@<machine-name>
    ```
    
5. Zkopírujte vygenerovaný klíč do SSH veřejný klíč pole.

### <a name="create-a-service-principal-in-azure-ad"></a>Vytvoření instančního objektu ve službě Azure AD

1.  Přihlaste se na globální [ *webu Azure portal*](http://portal.azure.com/).

2.  Přihlaste se pomocí tenanta Azure AD přidružené k instanci služby Azure Stack.

3.  Vytvořte aplikaci Azure AD.

    1. Vyberte **Azure Active Directory** > **+ registrace aplikací**> **registrace nové aplikace**.
    2. Zadejte **název** aplikace. 
    3. Vyberte **webovou aplikaci nebo API**. 
    4. Zadejte `http://localhost` pro **přihlašovací adresa URL**. 
    5. Vyberte **Vytvořit**.

1.  Poznamenejte si **ID aplikace**. Toto ID je potřeba při vytváření clusteru a je odkazováno jako **ID klienta instančního objektu**.

2.  Vyberte **nastavení** > **klíče**.

    1. Zadejte **popis**. 
    2. Vyberte **nikdy nevyprší platnost** pro **Expires**. 
    3. Vyberte **Uložit**. Ujistěte se, poznamenejte si řetězec klíče. Řetězec klíče je potřeba při vytváření clusteru a je odkazováno jako **tajný kód klienta instančního objektu**.

### <a name="give-the-service-principal-access"></a>Poskytnout přístup instančního objektu služby

Dejte přístupu instančního objektu služby k předplatnému, aby ho může vytvářet prostředky.

1.  Přihlaste se k [portál pro správu](https://adminportal.local.azurestack.external/).

2.  Vyberte **další služby** > ** předplatná uživatelů ** > **+ přidat**.

3.  Vyberte předplatné vytvořili.

4.  Vyberte **řízení přístupu (IAM)** > vyberte **+ přidat**.

5.  Vyberte **vlastníka** role.

6.  Vyberte název aplikace vytvořené pro službu objektu zabezpečení. Zadejte název do pole Hledat v případě potřeby.

7.  Vyberte **Uložit**.

8.  Otevřít [portálu Azure Stack](https://portal.local.azurestack.external).

9.  Vyberte **+ nový** > **Compute** > **clusteru Kubernetes**. Vyberte **Vytvořit**.

    ![Nasadit šablonu řešení](media/azure-stack-solution-machine-learning/image59.png)

10. Vyberte **Základy** v vytvoření clusteru Kubernetes.

    ![Nasadit šablonu řešení](media/azure-stack-solution-machine-learning/image60.png)

11. Zadejte **uživatelské jméno správce virtuálního počítače Linux**. Uživatelské jméno pro virtuální počítače Linux, které jsou součástí clusteru Kubernetes a DVM.

12. Zadejte **veřejný klíč SSH** se používají pro autorizaci pro všechny počítače s Linuxem vytvořili jako součást clusteru Kubernetes a DVM.

13. Zadejte **předpony DNS profilu hlavní** , které je jedinečné pro danou oblast. Musí se jednat o oblasti – jedinečný název, například `ask8s-12345`. Zkuste zvolit je stejná jako skupina prostředků pojmenujte jako nejlepší praxe.

    > [!Note]  
    > Pro každý cluster použijte předponu DNS nových a jedinečných hlavní profilu.

14. Zadejte **počet profil fond agentů**. Počet obsahuje počet agentů v clusteru. Může být od 1 do 4.

15. Zadejte **ID klienta instančního objektu** slouží od poskytovatele cloudu Kubernetes Azure.

16. Zadejte **tajný kód klienta instančního objektu** vytvářejí při vytváření instančního objektu aplikace služby.

17. Zadejte **verze zprostředkovatele služby Kubernetes Azure cloudu**. Toto je verze zprostředkovatele služby Kubernetes Azure. Azure Stack uvolní vlastního sestavení Kubernetes pro každou verzi služby Azure Stack.

18. Vyberte **předplatné** ID.

19. Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerické znaky a malá písmena.

20. Vyberte **umístění** skupiny prostředků. To je oblast zvolené pro instalaci služby Azure Stack.

### <a name="specify-the-azure-stack-settings"></a>Zadejte nastavení služby Azure Stack

1.  Vyberte **nastavení služby Azure Stack razítko**.

    ![Nasadit šablonu řešení](media/azure-stack-solution-machine-learning/image61.png)

2.  Zadejte **koncový bod Azure Resource Manageru Tenanta**. Toto je koncový bod Azure Resource Manageru pro připojení k vytvoření skupiny prostředků pro Kubernetes cluster. Koncový bod z operátoru Azure Stack je třeba integrovaný systém. Pro Azure Stack Development Kit (ASDK), použijte `https://management.local.azurestack.external`.

3.  Zadejte **ID Tenanta** pro příslušného tenanta. Zobrazit [ *získání ID tenanta* ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) nalezena hodnota ID tenanta.

### <a name="install-kubectl-on-windows-powershell-environment"></a>Instalace kubectl v prostředí Windows PowerShell

Z prostředí WSL spuštěním následujících příkazů kubectl instalace ve WSL prostředí.

```PowerShell  
Install-script -name install-kubectl -scope CurrentUser -force
Install-kubectl.ps1 -downloadlocation "C:\Users\<Current User>\Documents\Kube"
```

### <a name="install-kubectl-on-the-windows-subsystem-for-linux-environment"></a>Instalace kubectl v subsystému Windows pro Linuxové prostředí

Z prostředí WSL spuštěním následujících příkazů kubectl instalace ve WSL prostředí.

```Bash  
    apt-get update && apt-get install -y apt-transport-https
    curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add
    sudo apt-add-repository "deb http://apt.kubernetes.io/ kubernetes-xenial main"
    apt-get update
    apt-get install -y kubectl
```

### <a name="configure-kubectl"></a>Konfigurace kubectl

Aby klienta kubectl pro vyhledání a přístupu ke clusteru Kubernetes*kubeconfig souboru* je potřeba. Tím se vytvoří automaticky při vytvoření clusteru pomocí kube up.sh nebo nasazení Minikube clusteru. Zobrazit [ *příručky Začínáme* ](https://kubernetes.io/docs/setup/) Další informace o vytváření clusterů. Přístup ke clusteru vytvořené jiným uživatelem, najdete v článku [ *clusteru přístup sdílení dokumentu*](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/). Ve výchozím nastavení, konfigurace kubectl nachází ve **~.kube/config**.

### <a name="check-the-kubectl-configuration"></a>Kontrola konfigurace kubectl

Zkontrolujte, že tento kubectl tím, že získáme stav clusteru správně nakonfigurovaný:

```Bash  
kubectl cluster-info
```

kubectl je správně nakonfigurovaný pro přístup ke clusteru, když se zobrazí odpověď url.

kubectl není správně nakonfigurován nebo není možné se připojit ke clusteru Kubernetes, pokud se zobrazí následující zpráva:

```Bash  
The connection to the server <server-name:port> was refused -  did you specify the right host or port?
```

Například při spuštění clusteru Kubernetes na místním přenosném počítači, nástroj bude možná potřeba (minikube nebo podobné) k opětovnému spuštění výše uvedených příkazů.

Pokud cluster kubectl-informace o vrátí adresu url odpovědi, ale clusteru není stále dostupný, zkontrolujte správnou konfiguraci pomocí:

```Bash  
    kubectl cluster-info dump
```

### <a name="enable-shell-autocompletion"></a>Povolení automatického doplňování prostředí

kubectl obsahuje podporu automatického doplňování povolování prostředí provádění rychlé a snadné.

Samotný skript dokončení je generován kubectl a je přístupný z profilu.

### <a name="connect-to-the-kubernetes-cluster"></a>Připojte se ke clusteru Kubernetes

Pro připojení ke clusteru pomocí klienta příkazového řádku Kubernetes (**kubectl**) je potřeba. Pokyny pro připojení a Správa clusteru najdete v [dokumentace služby Azure Container Service.](https://docs.microsoft.com/azure/container-service/dcos-swarm/)

Žádné klienta SSH lze použít pro připojení ke clusteru Kubernetes. Subsystém Windows pro Linux (WSL) se doporučuje. Počítač připojení ke clusteru Kubernetes se ve skupině prostředků vytvořili pro cluster a budou začínat předponou vmd hrany ml protokolů.

```Bash  
ssh <user>@vmd-edge-ml-stack.<FQDN of Kubernetes Machine in  Azure Stack>
```

Po připojení k počítači Kubernetes, spusťte následující počítač se Kubernetes konfigurační soubor.

```Bash  
sudo find / -name \*kubeconfig\* -type f
```

Výstup bude podobný následujícímu:

```Bash  
/var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

Zkopírujte tyto soubory cesta informace a pak spusťte následující příkaz a vkládání v cestě k souboru kubeconfig zkopírovány z výše:

```Bash  
sudo cat  /var/lib/waagent/custom-script/download/0/acs-engine/_output/edgemlstack/kubeconfig/kubeconfig.<regionname>.json
```

Výstup bude velkou část textu, což je nezpracovaný obsah JSON. Zkopírujte následující text výstupu a vložte tento kód do souboru sady Visual Studio, uložíte jako soubor JSON. Výsledkem kubeconfig.json místně uložených souborů. (Uložit/mnt/c/uživatele/ <current user> /dokumentů/directory Kube jako kubeconfig.json)

### <a name="configure-the-kubernetes-cluster"></a>Konfigurace clusteru Kubernetes

Po získání místního souboru JSON, v nové relaci WSL, použijte následující příkazy ke konfiguraci clusteru.

```Bash  
    cd /mnt/c/users/<current user>/documents/Kube
    kubectl proxy
    kubectl create -f https://raw.githubusercontent.com/kubernetes/dashboard/master/src/deploy/recommended/kubernetes-dashboard.yaml
    kubectl proxy
    set KUBECONFIG="/mnt/c/users/<current user>/documents/Kube/kubeconfig.json"
    kubectl.exe config view
```

Nastavení konfigurace Kubernetes bude definici (viz následující výstup).

![Alternativní text](media/azure-stack-solution-machine-learning/image62.png)

Spusťte službu místní proxy server:

```Bash  
kubectl proxy
```

Přejděte na uživatelské rozhraní clusteru kubernetes na následující adrese: `https://localhost:8001`.

![Alternativní text](media/azure-stack-solution-machine-learning/image63.png)

Teď máte místo, kde můžete nasadit kontejner a kontejner, který se nachází v cloudu, který se zobrazí v místním prostředí.

![Alternativní text](media/azure-stack-solution-machine-learning/image64.png)

Přizpůsobit **iris_deployment.yaml** souboru (umístěný ve /*mnt/c/uživatele/<current user>Kube directory/dokumentů/*) tak **webservicename** a kontejnery  **Obrázek** a **název** odpovídat nasazení pomocí editoru kódu podle výběru.

![Alternativní text](media/azure-stack-solution-machine-learning/image65.png)

Nastavte port kontejneru na **5001.**

![Alternativní text](media/azure-stack-solution-machine-learning/image66.png)

A pak vytvořte **imagePullSecret**:

### <a name="create-a-secret-in-the-cluster-that-holds-the-authorization-token"></a>Vytvoření tajného klíče v clusteru, který obsahuje autorizační token

Kubernetes cluster pomocí tajného klíče **docker-registry** typu k ověření pomocí registru kontejnerů na vyžádání privátní image.

Vytvoření tajného kódu, jeho pojmenování **azuremlcr**:

```Bash  
kubectl create secret docker-registry azuremlcr --docker-server=<your-registry-server> --docker-username=<your-name> --docker-password= "<your-pword>" --docker-email=<your-email>
```

Hledání:

- **< your-registry-server >** je Azure Container Registry **přihlašovací Server**.
- **< your-name >** je Azure Container Registry **uživatelské jméno**.
- **< your pword – >** je Azure Container Registry **heslo**. Ujistěte se prosím, že je heslo v uvozovkách.
- **< svůj e-mail >** je uživatel, který má přístup k R/W, ke kontejneru.
- Tyto informace najdete v **kontejneru Azure** **registru** pod **přístupové klíče**.
- Docker přihlašovací údaje jsou nyní nastavit v clusteru jako tajný kód říká **azuremlcr**.

Uložit **iris_deployment.yaml** souboru (umístěný ve /*mnt/c/uživatele/<current user>Kubedirectory/dokumentů/*).

### <a name="create-the-kubernetes-container"></a>Vytvoření kontejneru pro Kubernetes

```Bash  
kubectl.exe create -f /mnt/c/users/<current  user>/documents/Kube/iris_deployment.yaml
```

![Alternativní text](media/azure-stack-solution-machine-learning/image67.png)

Zkontrolujte stav nasazení:

```Bash  
Kubectl get deployments
```

![Alternativní text](media/azure-stack-solution-machine-learning/image68.png)

Nasazení může nějakou dobu trvat.

### <a name="configure-azure-devops-to-deploy-automatically"></a>Konfigurace Azure DevOps pro automatické nasazení

#### <a name="create-a-team-project"></a>Vytvoření týmového projektu

1.  Zajištění [členství skupiny správců kolekce projektu.](https://docs.microsoft.com/vsts/organizations/security/set-project-collection-level-permissions?view=vsts) Chcete-li vytvořit týmové projekty, **vytvořit nové projekty** oprávnění musí být nastaveno na **povolit**.

2.  Na stránce projektů vyberte **nový projekt**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image69.png)

1.  Pojmenujte projekt **HybridMLIris**.

2.  Vyberte počáteční zdrojové typu ovládacího prvku jako **Git**

3.  Vyberte proces a vyberte **vytvořit**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image70.png)

### <a name="import-some-code--create-repository"></a>Import kódu vytvoření úložiště

Je potřeba úložiště Git pro kód YAML.

#### <a name="add-user-to-the-git-repo"></a>Přidání uživatele do úložiště GIT

1.  Výchozí řídicí panel Projekt vyberte přihlašovací údaje Git generovat.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image71.png)

1.  Zadejte heslo, kde je povinné a uložení přihlašovacích údajů Git.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image72.png)

1.  Inicializace úložiště tak, že vyberete **inicializovat** tlačítko a vytváření **README** souboru.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image73.png)

#### <a name="clone-the-git-repository-locally-and-upload-the-code"></a>Naklonujte úložiště Git místně a odešlete kód. 

1.  Aby byl adresář v počítači v `c:\\users\\<User>\\source\\repos\\hybridMLIris`a naklonujte úložiště

    ```Bash  
    sudo mkdir /mnt/c/users/<User>/source sudo mkdir /mnt/c/users/<User>/source/repos sudo mkdir /mnt/c/users/<User>/source/repos/hybridMLIris cd /mnt/c/users/<User>/source/repos/hybridMLIris sudo git clone  https://<yourvstssite>.visualstudio.com/HybridMLIris/_git/HybridMLIris
    ```

    ![Alternativní text](media/azure-stack-solution-machine-learning/image74.png)

1.  Přejděte do nově naklonované úložiště:

    ```Bash  
    ls
    cd ./HybridMLIris
    ```
    
    ![Alternativní text](media/azure-stack-solution-machine-learning/image75.png)

1.  Kopírovat **iris_deployment.yaml** souboru do úložiště.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_deployment.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_deployment.yaml
    ``` 

1.  Potvrdit změny v GITU

    ```Bash  
    git add . git commit -m Added Deployment YAML git push
    ```

    ![Alternativní text](media/azure-stack-solution-machine-learning/image76.png)

### <a name="prepare-the-private-build-and-release-agent-for-vsts-integration"></a>Příprava integrace VSTS soukromé sestavení a verze agenta

#### <a name="prerequisites"></a>Požadavky

VSTS se ověřuje na Azure Resource Manageru pomocí instančního objektu. Pro VSTS, abyste mohli ke zřízení prostředků v předplatném služby Azure Stack vyžaduje stav přispěvatelů. \ **Toto jsou základní kroky, které je potřeba nakonfigurovat tak, aby takové ověřovat:**

1.  Instanční objekt služby by měly být vytvořeny nebo některý z existujících mohou být použity.

2.  Ověřovací klíče musí být vytvořen pro instanční objekt.

3.  Musí se ověřit prostřednictvím řízení přístupu na základě rolí umožňuje hlavního názvu služby se součástí role přispěvatele předplatného Azure Stack.

4.  Pomocí koncových bodů služby Azure Stack, stejně jako informace hlavního názvu služby se musí vytvořit novou definici služby ve VSTS.

#### <a name="service-principal-creation"></a>Vytvoření instančního objektu

Odkazovat [pokyny k vytvoření instančního objektu](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) k vytvoření instančního objektu a zvolte webovou aplikaci nebo rozhraní API pro typ aplikace.

**Přístup k vytvoření klíče**

Instanční objekt služby vyžaduje klíče pro ověřování, postupujte podle kroků v této části se vygenerovat klíč.

1.  Z **registrace aplikací** ve službě Azure Active Directory, vyberte aplikaci.

    ![Výběr aplikace](media/azure-stack-solution-machine-learning/image77.png)

1.  Poznamenejte si hodnotu **ID aplikace. Hodnota se používá při konfiguraci koncového bodu služby ve VSTS.**

    ![Alternativní text](media/azure-stack-solution-machine-learning/image78.png)

1.  Pokud chcete generovat ověřovací klíč, vyberte **Nastavení**.

    ![Volba Nastavení](media/azure-stack-solution-machine-learning/image79.png)

1.  Vyberte **Klíče**.

    ![Volba Klíče](media/azure-stack-solution-machine-learning/image80.png)

1.  Zadejte popis klíče a jeho dobu platnosti. Až budete hotovi, vyberte **Uložit**.

    ![Uložení klíče](media/azure-stack-solution-machine-learning/image81.png)

Jakmile klíč uložíte, zobrazí se jeho hodnota. Zkopírujte tuto hodnotu, protože je budete později potřebovat. **Hodnotu klíče** s aplikací ID je potřeba se přihlásit jako aplikace. Hodnota klíče, kde aplikace může načíst ji Store.

![Alternativní text](media/azure-stack-solution-machine-learning/image82.png)

#### <a name="get-tenant-id"></a>Získání ID Tenanta

Jako součást konfigurace koncového bodu služby VSTS vyžaduje **ID Tenanta** , který odpovídá razítko Azure Stack je nasazená do adresáře AAD. Postupujte podle kroků v této části k získání ID Tenanta.

1.  Vyberte **Azure Active Directory**.

    ![Výběr Azure Active Directory](media/azure-stack-solution-machine-learning/image83.png)

1.  Pokud chcete získat ID tenanta, vyberte **vlastnosti** pro tenanta Azure AD.

    ![Výběr položky Vlastnosti v Azure AD](media/azure-stack-solution-machine-learning/image84.png)

1.  Zkopírujte **ID adresáře**. Tato hodnota je ID tenanta.

    ![ID tenanta](media/azure-stack-solution-machine-learning/image85.png)

Udělení práv instanční objekt služby pro nasazení prostředků v rámci předplatného Azure Stack

Pro přístup k prostředkům v rámci předplatného, přiřazení aplikace k roli. Rozhodněte, jakou roli představuje správná oprávnění pro aplikaci. Další informace o dostupných rolí, najdete v článku [RBAC: Vestavěné role](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

Nastavte obor na úrovni předplatného, skupinu prostředků nebo prostředek. Oprávnění se dědí do oboru na nižších úrovních. Například přidáním aplikace k roli Čtenář pro skupinu prostředků mu umožní načíst skupinu prostředků a všechny prostředky, které obsahuje.

1.  Přejděte na požadované úrovni oboru přiřazení aplikace. Například vyberte přiřazení role v oboru předplatného, **předplatná**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image86.jpeg)

1.  Vyberte **předplatné** (skupinu prostředků nebo prostředek), přiřazení aplikace.

    ![Vyberte předplatné pro přiřazení](media/azure-stack-solution-machine-learning/image87.png)

1.  Vyberte **řízení přístupu (IAM)**.

    ![Vyberte přístup](media/azure-stack-solution-machine-learning/image88.png)

1.  Vyberte **Přidat**.

    ![Výběr možnosti Přidat](media/azure-stack-solution-machine-learning/image89.png)

1.  Vyberte roli, kterou chcete přiřadit aplikace. Na následujícím obrázku **vlastníka** role.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image90.png)

1.  Ve výchozím nastavení aplikace Azure Active Directory nejsou zobrazeny v dostupných možnostech. Najít aplikace, **zadejte název** do vyhledávacího pole a vyberte ji.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image91.png)

1.  Vyberte **Uložit** k dokončení přiřazení role. Aplikace se zobrazí v seznamu Uživatelé přiřazení k roli pro tento obor.

### <a name="role-based-access-control"></a>Řízení přístupu na základě rolí

Azure na základě rolí řízení přístupu (RBAC) umožňuje přesnou správu přístupu pro Azure a Azure Stack. Pomocí RBAC, mohou být udělena pouze takovou úroveň přístupu, který uživatelé potřebují k provádění svých úloh. Další informace o řízení přístupu na základě rolí naleznete v tématu [spravovat přístup k prostředkům předplatného Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal?toc=%252fazure%252factive-directory%252ftoc.json).

**Fondy agentů VSTS**

Namísto samostatně spravovat každý agent, agenti jsou uspořádány do **fondy agentů**. Fond agentů definuje hranice sdílení pro všechny agenty v tomto fondu. Ve VSTS fondech agentů oborem pro účet VSTS, takže je možné sdílet mezi týmovými projekty. Další informace a kurz o tom, jak vytvořit VSTS najdete v článku fondy agentů [vytvořit fondy agentů a fronty.](https://docs.microsoft.com/vsts/build-release/concepts/agents/pools-queues?view=vsts)

**Přidat aPersonal access token PAT pro službu Azure Stack**

 -  Přihlaste se k účtu VSTS a výběr u **profilu účtu** název.

 -  Vyberte **spravovat zabezpečení** na stránku vytvoření tokenu přístupu.

![Alternativní text](media/azure-stack-solution-machine-learning/image92.png)

![Alternativní text](media/azure-stack-solution-machine-learning/image93.jpeg)

![Alternativní text](media/azure-stack-solution-machine-learning/image94.jpeg)

> [!Note]  
> Získáte informace o tokenu. Nebude se zobrazovat znovu po opuštění této obrazovce.

1.  Kopírovat **token**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image95.png)

#### <a name="install-the-vsts-build-agent-on-the-azure-stack-hosted-build-server"></a>Nainstalujte agenta sestavení VSTS ve službě Azure Stack hostování serveru pro sestavení

1.  Připojte se k **serveru sestavení** nasazený na hostiteli služby Azure Stack.

    > [!Note]  
    > Ujistěte se prosím, že Azure Stack hostované Build Server je přístupný z veřejného Internetu. Pokud ne, pracovat s Azure Stack operátor získat přístup.

    ```Bash  
    ssh <user>@<buildserver.publicip>
    ```

2.  Upgrade serveru Ubuntu sestavení na nejnovější verzi (18.04):

    ```Bash  
    sudo su
    apt-get update
    apt-get upgrade
    apt-get dist-upgrade
    apt-get autoremove
    do-release-upgrade -d
    ```

    > [!Note]  
    > Tato operace bude chvíli trvat.

2.  Instalace aplikací předpokladů pro server sestavení. Z bashe prostředí Ubuntu na základě sestavení serveru spusťte následující příkazy:

    ```Bash  
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.asc.gpg
    sudo mv microsoft.asc.gpg /etc/apt/trusted.gpg.d/
    wget -q https://packages.microsoft.com/config/ubuntu/18.04/prod.list 
    sudo mv prod.list /etc/apt/sources.list.d/microsoft-prod.list
    sudo chown root:root /etc/apt/trusted.gpg.d/microsoft.asc.gpg
    sudo chown root:root /etc/apt/sources.list.d/microsoft-prod.list
    sudo apt-get install apt-transport-https
    sudo apt-get update
    sudo apt-get install liblttng-ust0 libcurl3 libssl1.0.0 curl lsb-release libkrb5-3 zlib1g libicu60 aspnetcore-runtime-2.1 dotnet-sdk-2.1
    wget https://github.com/PowerShell/PowerShell/releases/download/v6.1.0-preview.3/powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo dpkg -i powershell-preview_6.1.0-preview.3-1.ubuntu.18.04_amd64.deb
    sudo apt-get install -f
    AZ_REPO=$(lsb_release -cs)
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $AZ_REPO main" | \
        sudo tee /etc/apt/sources.list.d/azure-cli.list
    curl -L https://packages.microsoft.com/keys/microsoft.asc | sudo apt-key add –
    sudo apt-get update && sudo apt-get install azure-cli
    ```

2.  Stáhnout a nasadit jako službu pomocí agenta sestavení **osobní přístupový token PAT** a správce virtuálního počítače účet Spustit jako.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image96.png)

    ```Bash  
        cd \home\<user>
        sudo mkdir myagent && cd myagent
        wget https://vstsagentpackage.azureedge.net/agent/2.134.2/vsts-agent-linux-x64-2.134.2.tar.gz
        sudo tar zxvf vsts-agent-linux-x64-2.134.2.tar.gz
    ```

2.  Přejděte do složky agenta sestavení byl extrahován. Spusťte následující kód.

    ```Bash  
        cd ..
        sudo chmod -R 777 myagent
        cd myagent
        ./config.sh
    ```

    ![Alternativní text](media/azure-stack-solution-machine-learning/image97.png)

2.  Po **./config.sh**dokončené, spusťte následující kód k povolení služby při spuštění serveru a spustit službu:

    ```Bash  
    sudo ./svc.sh install
    sudo ./svc.sh start
    ```

Agent je nyní viditelné ve složce VSTS.

#### <a name="endpoint-creation-permissions"></a>Oprávnění pro vytváření koncového bodu

Uživatelé můžou vytvářet koncové body, takže VSTO sestavení aplikace Azure Service můžete nasadit na zásobníku. VSTS se připojí k agent sestavení, který poté se připojí pomocí služby Azure Stack.

![Alternativní text](media/azure-stack-solution-machine-learning/image98.png)

1.  Na **nastavení** nabídce vyberte možnost **zabezpečení**.

2.  V **skupiny VSTS** seznamu na levé straně vyberte **koncový bod Creators**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image99.png)

3.  Na **Karta Členové** vyberte **+ přidat**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image100.png)

1.  Typ **uživatelské jméno** a vyberte uživatelské jméno ze seznamu.

2.  Vyberte **uložit změny**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image101.png)

3.  V **skupiny VSTS** seznamu na levé straně vyberte **koncový bod správci**.

4.  Na **Karta Členové** vyberte **+ přidat**.

5.  Zadejte **uživatelské jméno** a vyberte uživatele ze seznamu.

6.  Vyberte **uložit změny.**

    ![buchatech](media/azure-stack-solution-machine-learning/image102.jpeg)

    Agent sestavení ve službě Azure Stack získá pokyny z VSTS, které pak přenese informace o koncovém bodu pro komunikaci pomocí služby Azure Stack.

    VSTS pro připojení služby Azure Stack je teď připravený.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image103.png)

### <a name="configure-build-and-release-definitions"></a>Konfigurace definice sestavení a vydaná verze

Teď, když se připojení naváže, bude ručně mapování vytvořený koncový bod Azure, AKS a Azure Container Registry k sestavení a definice verzí.

#### <a name="create-the-build-definition-for-the-yaml-code"></a>Vytvoření definice sestavení pro kód The YAML

1.  Vyberte část sestavení v rámci sestavení a vydaná verze centra a vytvořte novou definici.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image104.png)

1.  Výběr úložiště VSTS Git a vybrat úložiště vytvořili dříve.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image105.png)

1.  Vyberte prázdném kanálu jako šablonu

    ![Alternativní text](media/azure-stack-solution-machine-learning/image106.png)

1.  Název sestavení **kopírování artefaktů** a vyberte Server, Azure Stack sestavení pro frontu agenta.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image107.png)

1.  Vyberte fázi 1 procesy a přejmenujte ho na **kopírování artefaktů**, pak **přidejte úkol** do fáze:

    ![Alternativní text](media/azure-stack-solution-machine-learning/image108.png)

1.  Vyberte **publikujte artefakty sestavení** z **nástroj** seznam a vyberte **přidat**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image109.png)

1.  Vyberte **cesta k publikování** a vyberte **iris_deployment.yaml** souboru.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image110.png)

1.  Název artefaktu **iris_deployment** a vyberte umístění pro publikování na **kanály Azure**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image111.png)

1.  Vyberte **Uložit & frontu**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image112.png)

1.  Zkontrolujte stav sestavení tak, že vyberete ID sestavení.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image113.png)

Úspěch bude vypadat nějak takto:

![Alternativní text](media/azure-stack-solution-machine-learning/image114.png)

#### <a name="create-the-release-definition-for-the-yaml-code"></a>Vytvoření definice verze pro kód YAML

1.  Vyberte odpovídající část vydané verze v rámci sestavení a vydaná verze centra, nová definice

    ![Alternativní text](media/azure-stack-solution-machine-learning/image115.png)

1.  Vyberte prázdném kanálu jako šablonu.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image106.png)

1.  Název prostředí Azure Stack.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image116.png)

1.  Přidání nové artefaktu tak, že vyberete **artefakty** a **+ přidat**

2.  Vyberte sestavení jako typ zdroje a **HybridMLIris** jako projekt.

3.  Vyberte definici sestavení předtím vytvořili pro zdroj.

4.  Potom vyberte **přidat**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image117.png)

1.  Vyberte z prostředí Azure Stack a potom přidejte nový úkol do služby Azure Stack

    ![Alternativní text](media/azure-stack-solution-machine-learning/image118.png)


1.  Fáze agenta nastavte frontu agenta k Azure Stack hostovaná sestavení serveru.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image119.png)

1.  Přidat nový úkol do této fáze, vyberte nasazení Kubernetes úkolu v rámci nasazení a vyberte Přidat.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image120.png)


1.  Pojmenujte ji **použití Kubectl** (výchozí název) a vyberte příkaz použít.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image121.png)

    Teď vytvořte nové připojení služby Kubernetes.

#### <a name="create-kubernetes-service-endpoint"></a>Vytvoření koncového bodu služby Kubernetes

1.  V části připojení služby Kubernetes, vyberte **+ nová** tlačítko a vyberte**Kubernetes**ze seznamu. můžete použít tento koncový bod pro připojení**VSTS**a**Azure Container Service (AKS)**.

2.  **Název připojení**: Zadejte název připojení.

3.  **Adresa URL serveru**: Zadejte adresu služby kontejneru v formathttp: / / {adresa serveru rozhraní API}

4.  **Kubeconfig**: K získání hodnoty Kubeconfig, spusťte následující příkazy Azure v příkazovém řádku spustit s oprávněními správce.

    > [!Important]  
    > Použijte toto okno rozhraní příkazového řádku k provedení dalších kroků.

6.  V okně rozhraní příkazového řádku se přihlaste k Azure. [Další informace o příkazu az login](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)

7.  Na příkazovém řádku zadejte:

    ```CLI
        az login
    ```

10. Tento příkaz vrátí kód, který použijete v prohlížeči na <https://aka.ms/devicelogin>.

11. Přejděte na <https://aka.ms/devicelogin> v prohlížeči. Po zobrazení výzvy zadejte přijatý v rozhraní příkazového řádku do prohlížeče kód.

    ![Koncový bod služby Kubernetes](media/azure-stack-solution-machine-learning/image122.png)

1.  Zadejte následující příkaz na příkazovém řádku, chcete-li získat přihlašovací údaje pro přístup pro Kubernetes cluster.

### <a name="azure-ml-workbench-cli"></a>Rozhraní příkazového řádku Azure ML Workbench

AZ aks get-credentials skupina_prostředků <yourResourceGroup> název <yourazurecontainerservice>

![Koncový bod služby Kubernetes](media/azure-stack-solution-machine-learning/image123.png)

1.  Přejděte **.kube**složka v domovském adresáři (např: C:\\uživatelé\\<user>\\dokumenty\\Kube)

2.  Zkopírujte obsah**config**soubor a vložte ho v okně připojení Kubernetes. Vyberte**OK**tlačítko.

    ![Koncový bod služby Kubernetes](media/azure-stack-solution-machine-learning/image124.png)
    

3.  Koncový bod Kubernetes je vytvořen a vyberete, zaškrtnutím políčka použijte konfigurační soubory přidání konfiguračního souboru. Vyhledejte soubor iris_deployment.yaml v propojených artefaktů.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image125.png)

    ![Alternativní text](media/azure-stack-solution-machine-learning/image126.png)

4.  Uložte definici verze.

#### <a name="check-the-status-of-the-release-definition"></a>Zkontrolujte stav definice vydané verze. 

Po uložení, by měl definice verze VSTS automaticky aktivovat vydanou verzi.

Zkontrolujte stav nasazení tak, že rychlé příkazu v příkazovém řádku WSL pak kontroluje uživatelské rozhraní Kubernetes.

```Bash  
kubectl get deployments
```

Výstup by měl vypadat nějak takto, zatímco v procesu nasazení.

![Alternativní text](media/azure-stack-solution-machine-learning/image127.png)

```Bash  
kubectl proxy
```

Jakmile uživatelského rozhraní kubernetes je spuštěná, přejděte do nasazení v [ **https://localhost:8001/** ](https://localhost:8001/) přejděte na **úloh -> sady replik**.

![Alternativní text](media/azure-stack-solution-machine-learning/image128.png)

### <a name="deploy-the-yaml-service"></a>Nasazení služby YAML

#### <a name="upload-the-irisserviceyaml-to-the-repository-and-sync-changes"></a>Nahrát iris_service.yaml do úložiště a synchronizace změn

1.  Přejděte do nově naklonované úložiště:

    ```Bash  
    cd /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/
    ```

    ![Alternativní text](media/azure-stack-solution-machine-learning/image75.png)

1.  Kopírovat **iris_service.yaml** souboru do úložiště.

    ```Bash  
    cp /mnt/c/users/<User>/documents/Kube/iris_service.yaml  /mnt/c/users/<User>/source/repos/HybridMLIris/HybridMLIris/iris_service.yaml
    ```

1.  Potvrdit změny v GITU

    ```Bash  
    git add .
    git commit -m "Added Service YAML" 
    git push
    ```

![Alternativní text](media/azure-stack-solution-machine-learning/image129.png)

#### <a name="update-the-build-definition-for-the-yaml-code"></a>Aktualizujte definici sestavení pro kód The YAML

1.  Vyberte část sestavení v rámci sestavení a vydaná verze centra a vyberte dříve vytvořenou definici.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image130.png)

2.  Výběr tlačítka Upravit upravte definici.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image131.png)

3.  **Přidejte úkol** na fázi. Vyberte **publikujte artefakty sestavení** z **nástroj** seznam a vyberte **přidat**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image108.png)

4.  Pojmenujte ji **použití Kubectl** (výchozí název) a vyberte příkaz použít.



#### <a name="update-the-release-definition-for-the-yaml-code"></a>Aktualizovat definici verze pro kód YAML

1.  Vyberte část theReleases v centru sestavení a vydaných verzí a vyberte dříve vytvořenou definici verze. Vyberte odkaz upravit.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image132.png)

1.  Vyberte prostředí **Azure Stack** pak přidejte nový úkol do služby Azure Stack.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image133.png)

1.  Přidat **nový úkol** do této fáze, vyberte **nasadit do Kubernetes** úloh v rámci **nasadit** a vyberte **přidat**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image134.png)

1.  Pojmenujte ji **použití Kubectl** (výchozí název) a vyberte příkaz použít.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image109.png)

1.  Nastavit připojení k službě Kubernates připojení k Azure Stack vytvořili dříve a pak vyberte **pomocí konfiguračních souborů** zaškrtávací políčko a přidáním konfigurační soubor. Přejděte k souboru iris_service.yaml v propojených artefaktů.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image135.png)
    <!-- -->
    ![alternativní text](media/azure-stack-solution-machine-learning/image136.png)

1.  Uložte definici verze.

#### <a name="check-the-status-of-the-release-definition"></a>Zkontrolujte stav definice vydané verze

Po uložení, by měl definice verze VSTS automaticky aktivovat vydanou verzi.

Zkontrolujte stav nasazení tak, že rychlé příkazu v příkazovém řádku WSL pak kontroluje uživatelské rozhraní Kubernetes.

```Bash  
kubectl get deployments
```

Výstup by měl vypadat nějak takto, zatímco v procesu nasazení.

![Alternativní text](media/azure-stack-solution-machine-learning/image127.png)


```Bash  
kubectl proxy
```

Jakmile uživatelského rozhraní kubernetes je spuštěná, přejděte do nasazení v [ **https://localhost:8001/** ](https://localhost:8001/) přejděte na **úloh -> sady replik**.

![Alternativní text](media/azure-stack-solution-machine-learning/image137.png)


### <a name="kubernetes-scoring-and-validation"></a>Kubernetes, vyhodnocení a ověření

Spuštění uživatelského rozhraní Kubernetes

```Bash  
kubectl proxy
```

Přejděte do uživatelského rozhraní Kubernetes, potom přejděte na stránku **nasazení** -> **Iris – nasazení** -> **nové sady replik**  ->  **Iris-nasazení xxxxxxxxx** (kde xs jsou ID nasazení).

![Alternativní text](media/azure-stack-solution-machine-learning/image138.png)

Přejděte na **služby** a vyberte **externí koncový bod** služby ověření pracovat.

![Alternativní text](media/azure-stack-solution-machine-learning/image139.png)

By měl zobrazit ověřovací zpráva podobná následující:

![Alternativní text](media/azure-stack-solution-machine-learning/image140.png)

#### <a name="create-azure-stack-scoring-function-app-in-the-azure-stack-portal"></a>Vytvoření služby Azure Stack vyhodnocování aplikace Function App na portálu Azure Stack

Aplikace function app se vyžaduje k hostování provádění jednotlivých funkcí. Aplikace function app umožňuje funkci seskupování jako logickou jednotku pro snadnější správu, nasazování a sdílení prostředků.

1.  Na portálu Azure Stack user portal, vyberte **+ nová** nalezeno tlačítko v levém horním rohu, pak vyberte **Web + mobilní zařízení** >**aplikace Function App**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image141.png)

1.  Pojmenujte funkci **funkce dat** a umístěte ho do stejné skupiny prostředků s zbývající Machine Learning obsahu. Umožňuje nástroj automaticky vytvořit nový plán app service pro využití a použijte účet úložiště předtím vytvořili pro úložiště aplikací.

    ![Definovat nové nastavení aplikace function app](media/azure-stack-solution-machine-learning/image142.png)

1.  Vyberte **vytvořit**zřídíte a nasadíte aplikaci function app.

2.  Vyberte ikonu oznámení v pravém horním rohu portálu a sledujte, kdy se objeví zpráva, že **nasazení bylo úspěšné**.

    ![Určení nastavení nové aplikace Function App](media/azure-stack-solution-machine-learning/image143.png)

1.  Vyberte **přejít k prostředku** Chcete-li zobrazit novou aplikaci function app.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image144.png)

1.  Vytvořit novou funkci tak, že vyberete **funkce**, pak bude **+ nová funkce** tlačítko.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image145.png)

1.  Výběr triggeru HTTP

    ![Alternativní text](media/azure-stack-solution-machine-learning/image146.png)

1.  Vyberte **C\#**  jako jazyk a název funkce: **vyčistit data skóre**a nastavte úroveň autorizace **anonymní**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image147.png)

1.  Obsah v příkladu kód pro vyčištění dat skóre do funkce kopírování a vkládání.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image148.png)

#### <a name="use-postman-to-validate-functions"></a>Ověřit funkce pomocí nástroje Postman

K zajištění, že jste nastavili Kbernetes a funkce správně můžete tuto bezplatnou aplikaci Postman k testování a validaci schémata a funkce. Chcete-li spustit tento proces, musíte nejprve shromáždit nějaké informace z vaší instance Kubernetes.

1.  Přejděte do uživatelského rozhraní Kubernetes, potom přejděte na stránku **nasazení** -> **Iris – nasazení** -> **nové sady replik**  ->  **Iris-nasazení xxxxxxxxx** (kde xs jsou ID nasazení)

    ![Alternativní text](media/azure-stack-solution-machine-learning/image138.png)

1.  Přejděte na **služby** a zkopírujte **externí koncový bod**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image149.png)

1.  Stáhněte a nainstalujte aplikaci Postman [tady](https://www.getpostman.com/apps) v případě potřeby.

2.  Přihlaste se k aplikaci Postman a zavřete dialogové okno Nový soubor.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image150.png)

1.  V rámci aplikace postman vyberte příspěvek...

    ![Alternativní text](media/azure-stack-solution-machine-learning/image151.png)

1.  Vložit **externí koncový bod** adresu URL do aplikace postman pod **adresy URL požadavku** přidání  **\\skóre** na konec adresy URL, jak je znázorněno níže.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image152.png)

1.  Vyberte **tělo** kartu a potom data zadejte jako **nezpracovaná**, pak **JSON**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image153.png)

1.  Z webového prohlížeče, přejděte na **externí koncový bod**. Přidáním následujícího do adresy URL **/swagger.json** to vede k souboru Services Swagger používá k testování instalace.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image154.png)

1.  Zkopírujte uvedený v příkladu **Swagger.JSON** souboru.

2.  V aplikaci Postman, vložte do text příspěvku v příkladu a vyberte **odeslat**. Měla by vrátit za podobně jako následující hodnota.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image155.png)

## <a name="step-7-create-an-azure-stack-storage-account-and-storage-queue"></a>Krok 7: Vytvoření účtu úložiště Azure Stack a fronty úložiště

Vytvoření účtu úložiště Azure Stack a fronty úložiště pro data.

1.  Přihlaste se k portálu User Portal pro Azure Stack. (Každý Azure Stack má jedinečný portál adresy URL)

2.  Na portálu pro uživatele Azure stacku rozbalením nabídky na levé straně otevřete nabídku služeb a zvolte **všechny služby**. Přejděte dolů k položce **úložiště** a zvolte **účty úložiště**. V **účty úložiště** okna zvolte **přidat**.

3.  Zadejte název účtu úložiště.

4.  Vyberte možnost replikace pro účet úložiště: **LRS**.

5.  Zadejte novou skupinu prostředků nebo vyberte existující skupinu prostředků.

6.  Vyberte **místní** pro umístění pro účet úložiště.

7.  Vyberte **vytvořit**k vytvoření účtu úložiště.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image156.png)

1.  Zvolte účet úložiště, nedávno vytvořili.

2.  Vyberte na **fronty**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image157.png)

1.  Vyberte na **+ fronta** a název fronty a vyberte **OK.**

    ![Alternativní text](media/azure-stack-solution-machine-learning/image158.png)

1.  Získejte **připojovací řetězec** pro frontu úložiště a zkopírujte ho.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image159.png)

1.  Přejděte do aplikace Azure Function App a potom vyberte **nastavení aplikace**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image160.png)

1.  Z v rámci nastavení aplikace Function App, posuňte se dolů nastavení aplikace a vyberte **+ přidat nové nastavení**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image161.png)

1.  Zadejte název účtu úložiště **název** pole, přidat do konce a `_STORAGE`.

Díky tomu, aby aplikace věděla, že toto je koncový bod účtu úložiště.

1.  Vložte připojovací řetězec do **hodnotu** pole.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image162.png)

1.  Přejděte do horní části Nastavení aplikace a vyberte **Uložit**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image163.png)

### <a name="update-the-scoring-function-to-use-storage-queue"></a>Funkce bodování použití fronty úložiště

1.  Vyberte na **integrovat** na funkci a zrušit výběr **získat** možnost.

2.  Vyberte **Uložit**.

3.  Potom vyberte **+ nový výstup** z výstupů.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image164.png)

1.  Potom vyberte **Azure Queue Storage** a zvolte **vyberte**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image165.png)

1.  Aktualizace **název fronty** do fronty úložiště vytvořené dříve a pak nastavte **připojení k účtu úložiště** k připojení k účtu úložiště vytvořili dříve a vyberte **uložit.**

    ![Alternativní text](media/azure-stack-solution-machine-learning/image166.png)

## <a name="step-8-create-a-function-to-handle-clean-data"></a>Krok 8: Vytvoření funkce pro zpracování vyčištění dat

Vytvoření nové funkce služby Azure Stack můžete do Azure přesunout vyčištění dat ze služby Azure Stack.

1.  Vytvořit novou funkci tak, že vyberete **funkce**, pak bude **+ nová funkce** tlačítko.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image167.png)

1.  Vyberte **Trigger časovače**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image168.png)

1.  Vyberte **C\#**  jako jazyk a název funkce: **nahrát do azure** a nastavte plán **0 0 \*/1 \* \* \***  který CRON zápis je jednou za hodinu.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image169.png)

### <a name="get-the-connection-string-to-the-azure-hosted-storage-account"></a>Získání připojovacího řetězce k účtu úložiště Azure hostované

1.  Přejděte do <https://portal.azure.com> a pak vyberte **účet služby Azure Storage** vytvořili dříve.

2.  Vyberte **přístupové klíče**, zkopírujte **připojovací řetězec** pro účet úložiště.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image170.png)

### <a name="update-the-upload-to-azure-function-to-use-the-azure-hosted-storage"></a>Aktualizovat nahrání do azure funkce použít úložiště hostované v Azure

1.  Přejděte do aplikace Azure Function App a potom vyberte **nastavení aplikace**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image171.png)

1.  Z v rámci nastavení aplikace Function App, posuňte se dolů nastavení aplikace a vyberte **+ přidat nové nastavení**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image172.png)

1.  Zadejte název účtu úložiště **název** pole, přidat na konec; _úložiště

Díky tomu, aby aplikace věděla, že toto je koncový bod účtu úložiště.

1.  Vložte Azure hostované připojovací řetězec účtu úložiště do **hodnotu** pole.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image173.png)

1.  Přejděte do horní části Nastavení aplikace a vyberte **Uložit**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image174.png)

1.  Přejděte zpět **nahrát do azure** funkce.

2.  Vyberte na **integrovat** na funkci.

3.  Potom vyberte **+ nový výstup** z výstupů.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image175.png)

1.  Potom vyberte **Azure Blob Storage** a zvolte **vyberte**.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image176.png)

1.  Aktualizace **cesta** do kontejneru úložiště vytvořili dříve v tomto formátu: **uploadeddata / {rand identifikátor guid} .txt**a pak nastavte **připojení k účtu úložiště** k Připojení k účtu úložiště do Azure vytvořili dříve a vyberte **uložit.**

    ![Alternativní text](media/azure-stack-solution-machine-learning/image177.png)

1.  Kopírování a vkládání obsahu v příkladu kódu pro **nahrát do azure** do funkce.

2.  Upravte podle potřeby tak, aby odkazoval na připojovací řetězec pro účty úložiště.

3.  Uložte a spusťte kód.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image178.png)

1.  Zkontrolujte účtu úložiště hostované v Azure, abyste zjistili, že má být data do cloudu v Azure: Úspěch bude vypadat podobně jako níže.

    ![Alternativní text](media/azure-stack-solution-machine-learning/image179.png)

Data byl upravený citlivých dat Azure stacku hostované Kubernetes strojové učení a odeslat do veřejného cloudu Azure z místního Azure Stack, prostřednictvím Azure Stack hostované aplikace Function App a můžete připravit data pro nahrávání do edge/odpojeném režimu scénář.

## <a name="next-steps"></a>Další postup

 - Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
