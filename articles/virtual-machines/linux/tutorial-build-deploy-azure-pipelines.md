---
title: Kurz – CI/CD na virtuální počítače Azure pomocí Azure Pipelines
description: V tomto kurzu se dozvíte, jak nastavit průběžnou integraci (CI) a průběžné nasazování (CD) aplikace Node.js do virtuálních aplikací Azure pomocí kanálu Azure založeného na YAML.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: bb7c773d02c5da5c115af79cd9e90c78e71eb6bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76988324"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Kurz: Nasazení aplikace do virtuálních počítačů S Linuxem v Azure pomocí služeb Azure DevOps Services a Azure Pipelines

Průběžná integrace (CI) a průběžné nasazování (CD) tvoří kanál, podle kterého můžete sestavit, uvolnit a nasadit kód po každém potvrzení kódu. Tento dokument obsahuje kroky spojené s nastavením kanálu CI/CD pro vícepočítačová nasazení pomocí Azure Pipelines.

Azure Pipelines poskytuje kompletní, plně vybavenou sadu nástrojů pro automatizaci CI/CD pro nasazení do virtuálních počítačů, a to jak v prem, tak v libovolném cloudu.

V tomto kurzu nastavíte kanál CI/CD založený na YAML pro nasazení aplikace do [prostředí](https://docs.microsoft.com/azure/devops/pipelines/process/environments?view=azure-devops) Azure Pipelines s virtuálními počítači Linuxu jako prostředky, z nichž každý slouží jako webové servery pro spuštění aplikace.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Získejte ukázkovou aplikaci.
> * Vytvořte kanál Azure Pipelines CI založený na YAML pro vytváření ukázkové aplikace.
> * Vytvoření prostředí Azure Pipelines pro virtuální počítače Azure
> * Vytvořte kanál CD Azure Pipelines.
> * Ruční nasazení nebo nasazení aktivované přes CI

## <a name="before-you-begin"></a>Než začnete

* Přihlaste se k organizaci služby**https://dev.azure.com/** Azure DevOps Services ( ). 
  Můžete si opatřit [bezplatnou organizaci služby Azure DevOps Services](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Další informace najdete v článku o [připojení ke službě Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Potřebujete linuxový virtuální počítač jako cíl nasazení.  Další informace najdete v článku o [vytvoření a správě linuxových virtuálních počítačů pomocí rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Otevřete příchozí port 80 pro virtuální počítač. Další informace najdete v článku o [vytvoření skupin zabezpečení sítě pomocí Azure Portalu](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-your-sample-app-code"></a>Získání ukázkového kódu aplikace

Pokud už máte aplikaci v GitHubu, kterou chcete nasadit, můžete zkusit vytvořit kanál pro tento kód.

Pokud jste však nový uživatel, můžete začít lépe pomocí našeho ukázkového kódu. V takovém případě rozvětevte tento repo v GitHubu:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic je [Java Spring Boot](https://spring.io/guides/gs/spring-boot) aplikace postavená pomocí [Maven](https://spring.io/guides/gs/maven/).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Tato aplikace Node.js byla vytvořena prostřednictvím [yeoman .](https://yeoman.io/learning/index.html) Používá Express, Bower a Grunt. Jako závislosti také obsahuje několik balíčků npm.
> Součástí této ukázky je také skript, který nainstaluje Nginx a nasadí tuto aplikaci. Spouští se na virtuálních počítačích. Tento skript konkrétně:
> 1. Nainstaluje Node, Nginx a PM2
> 2. Nakonfiguruje Nginx a PM2
> 3. Spustí aplikaci Node

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Předpoklady pro virtuální počítač s Linuxem

Ukázkové aplikace uvedené výše byly testovány na Ubuntu 16.04 a doporučujeme použít stejnou verzi virtuálního počítače Linux pro tento rychlý start.
Postupujte podle dalších kroků popsaných níže na základě zásobníku runtime používaného pro aplikaci.

#### <a name="java"></a>[Java](#tab/java)

- Pro nasazení java jarního bootu a spring cloudových aplikací vytvořte virtuální počítač s Linuxem v Azure pomocí [této](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) šablony, která poskytuje plně podporovaný modul runtime založený na OpenJDK.
- Pro nasazení servlet Java na serveru Tomcat vytvořte virtuální počítač s Linuxem s Javou 8 pomocí [této](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) šablony Azure a [nakonfigurujte Tomcat 9.x jako službu](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- Pro nasazení aplikace založené na jazyce Java EE použijte šablonu Azure k vytvoření [Linux UVM + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) nebo [Linux VM + Java + WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) nebo [Linux VM + Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) + WildFly / JBoss 14 

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Chcete-li nainstalovat aplikaci javascript nebo aplikaci Node.js, budete k nasazení aplikace potřebovat virtuální počítač s Linuxem s webovým serverem Nginx.
Pokud ještě nemáte virtuální počítač s Linuxem s Nginx, vytvořte ho teď v Azure pomocí kroků v [tomto příkladu](/azure/virtual-machines/linux/quick-create-cli).

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Vytvoření prostředí Azure Pipelines s virtuálními počítači Azure

Virtuální počítače lze přidat jako prostředky v rámci [prostředí](https://docs.microsoft.com/azure/devops/pipelines/process/environments) a lze je zaměřit na nasazení ve více počítačích. Zobrazení historie nasazení v rámci prostředí poskytují sledovatelnost z virtuálního počítače do kanálu a potom do potvrzení.

Prostředí můžete vytvořit v rozbočovači "**Prostředí**" v části **"Kanály".**
1.  Přihlaste se k organizaci Azure DevOps a přejděte k projektu.
2.  V projektu přejděte na stránku **Kanály.** Pak zvolte **Prostředí** a klepněte na **vytvořit prostředí**. Zadejte **název** (povinný) pro prostředí a **popis**.
3.  Zvolte **Virtuální počítače** jako **prostředek,** který chcete přidat do prostředí a klepněte na tlačítko **Další**.
4.  Zvolte operační systém (Windows/Linux) a **zkopírujte registrační skript PS**. 
5.  Nyní spusťte zkopírovaný skript z příkazového řádku správce prostředí PowerShell na každém z cílových virtuálních počítačů, které mají být registrovány v tomto prostředí.
    > [!NOTE]
    > - Osobní přístupový token přihlášeného uživatele je předem vložen do skriptu, jehož platnost vyprší ve stejný den, takže zkopírovaný skript je na něm nepoužitelný.
    > - Pokud váš virtuální počítač už má nějakého agenta spuštěného na něm, zadejte jedinečný název pro "agent" pro registraci s prostředím.
6.  Jakmile je virtuální virtuální ms zaregistrován, začne se zobrazovat jako prostředek prostředí pod záložkou "zdroje" prostředí.

    ![Vytvoření virtuálních měnových společností](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Chcete-li přidat další virtuální počítače, můžete skript znovu zobrazit a zkopírovat kliknutím na "Přidat prostředek" a výběrem možnosti Virtuální počítače jako prostředku. Tento skript by zůstal stejný pro všechny virtuální chody, které mají být přidány do tohoto prostředí. 
8.  Každý počítač spolupracuje s Azure Pipelines a koordinuje nasazení vaší aplikace.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. Značky můžete přidat do virtuálního počítače jako součást interaktivního skriptu pro registraci PS (nebo) můžete také přidat nebo odebrat stejné ze zobrazení prostředků kliknutím na trojité tečky na konci každého prostředku virtuálního počítače v zobrazení prostředků.

   Značky, které přiřadíte, umožňují omezit nasazení na konkrétní virtuální počítače při použití prostředí v úloze nasazení. Značky jsou omezeny na 256 znaků, ale neexistuje žádné omezení počtu značek, které můžete použít.

   ![Značky virtuálních měn](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Definování kanálu sestavení CI

Budete potřebovat kanál sestavení průběžné integrace (CI), který publikuje vaši webovou aplikaci, stejně jako skript nasazení, který lze spustit místně na serveru Ubuntu. Nastavte kanál sestavení CI na základě běhu, který chcete použít. 

1. Přihlaste se k organizaci Azure DevOps a přejděte k projektu.

1. V projektu přejděte na stránku **Kanály.** Pak zvolte akci a vytvořte nový kanál.

1. Projděte si kroky průvodce tak, že nejprve vyberete **GitHub** jako umístění zdrojového kódu.

1. Můžete být přesměrováni na GitHub pro přihlášení. Pokud ano, zadejte přihlašovací údaje githubu.

1. Po zobrazení seznamu úložišť vyberte požadované ukázkové úložiště aplikací.

1. Azure Pipelines bude analyzovat úložiště a doporučit vhodnou šablonu kanálu.

#### <a name="java"></a>[Java](#tab/java)

Vyberte **počáteční** šablonu a zkopírujte níže uvedený úryvek YAML, který vytváří váš projekt Java a spouští testy s Apache Maven:

```YAML
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

Další pokyny nazvěte v části [Vytvoření aplikace Java pomocí aplikace Maven](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/java).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Vyberte **počáteční** šablonu a zkopírujte níže uvedený úryvek YAML, který vytváří obecný projekt Node.js s npm.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

Další pokyny navazte na postup v [části Vytvoření aplikace Node.js pomocí doušku](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/javascript).

- Podívejte se na potrubí, abyste zjistili, co dělá. Ujistěte se, že všechny výchozí vstupy jsou vhodné pro váš kód.

- Vyberte **Uložit a spustit**, pak vyberte Potvrdit přímo do hlavní **větve**a pak zvolte Uložit **a spustit** znovu.

- Je spuštěnnový běh. Počkejte na dokončení běhu.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Definování kroků cd, které se mají nasadit do virtuálního počítače s Linuxem

1. Upravte výše uvedený kanál a zahrňte [úlohu nasazení](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs) odkazem na prostředí a prostředky virtuálního počítače, které jste dříve použili pomocí syntaxe YAML níže:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web1
     strategy:
   ```
2. Můžete vybrat konkrétní sady virtuálních počítačů z prostředí pro příjem nasazení zadáním **značek,** které jste definovali pro každý virtuální počítač v prostředí.
[Zde](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#deployment-job) je kompletní schéma YAML pro úlohu nasazení.

3. Můžete zadat `runOnce` eithor `rolling` nebo jako strategii nasazení. 

   `runOnce`je nejjednodušší strategie nasazení, v jejíž insazii jsou všechny háčky životního cyklu, konkrétně `preDeploy` `deploy`, `routeTraffic`a `postRouteTraffic`, provedeny jednou. Potom buď `on:` `success` `on:` `failure` nebo je spuštěn.

   Níže je uveden příklad fragmentu YAML pro `runOnce` :
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Níže je uveden příklad fragmentu YAML, který můžete použít k definování strategie postupného používání virtuálních počítačů, která aktualizuje až 5 cílů v každé iteraci. `maxParallel`určí počet cílů, které mohou být nasazeny, paralelně. Výběr představuje absolutní počet nebo procento cílů, které musí zůstat k dispozici kdykoli s výjimkou cílů, které jsou nasazeny. Používá se také k určení podmínek úspěchu a selhání během nasazení.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   Při každém spuštění této úlohy se `<environment name>` historie nasazení zaznamenává proti prostředí, které jste vytvořili a zaregistrovali virtuální počítače.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Spuštění kanálu a získání zobrazení sledovatelnosti v prostředí
Zobrazení nasazení prostředí poskytuje úplnou sledovatelnost potvrzení a pracovních položek a historii nasazení mezi kanály pro každý prostředí nebo prostředek.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Další kroky
- Můžete pokračovat [přizpůsobit kanál, který](https://docs.microsoft.com/azure/devops/pipelines/customize-pipeline) jste právě vytvořili.
- Další informace o tom, co dalšího můžete dělat v kanálech YAML, najdete v [tématu odkaz na schéma YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema).
- Pokud chcete zjistit, jak nasadit sadu LAMP (Linux, Apache, MySQL a PHP), přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Nasazení zásobníku LAMP](tutorial-lamp-stack.md)
