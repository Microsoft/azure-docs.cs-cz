---
title: Kurz – CI/CD do virtuálních počítačů Azure pomocí Azure Pipelines
description: V tomto kurzu se naučíte nastavit průběžnou integraci (CI) a průběžné nasazování (CD) Node.js aplikace do virtuálních počítačů Azure pomocí kanálu Azure založeného na YAML.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines
ms.collection: linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops, devx-track-js
ms.openlocfilehash: 49282bf6cbc7c24b75fbe3f1bbe68bd1fac62ae3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552486"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Kurz: nasazení aplikace do virtuálních počítačů se systémem Linux v Azure pomocí Azure DevOps Services a Azure Pipelines

Průběžná integrace (CI) a průběžné nasazování (CD) tvoří kanál, pomocí kterého můžete sestavit, vydávat a nasazovat kód po každém potvrzení kódu. Tento dokument obsahuje kroky spojené s nastavením kanálu CI/CD pro nasazení na více počítačů pomocí Azure Pipelines.

Azure Pipelines poskytuje kompletní, plně vybavenou sadu nástrojů pro automatizaci CI/CD pro nasazení do virtuálních počítačů, a to v Prem i v jakémkoli cloudu.

V tomto kurzu nastavíte kanál CI/CD založený na YAML, který nasadí vaši aplikaci do [prostředí](/azure/devops/pipelines/process/environments) Azure Pipelines s virtuálními počítači Linux jako prostředky, z nichž každý slouží jako webové servery ke spuštění aplikace.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Získejte ukázkovou aplikaci.
> * Vytvořte Azure Pipelines kanál CI založený na YAML pro vytvoření ukázkové aplikace.
> * Vytvoření prostředí Azure Pipelines pro virtuální počítače Azure
> * Vytvořte Azure Pipelines kanál CD.
> * Ruční nasazení nebo nasazení aktivované přes CI

## <a name="before-you-begin"></a>Než začnete

* Přihlaste se k vaší Azure DevOps Services organizaci ( **https://dev.azure.com/** ). 
  Můžete si opatřit [bezplatnou organizaci služby Azure DevOps Services](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Další informace najdete v článku o [připojení ke službě Azure DevOps Services](/azure/devops/organizations/projects/connect-to-projects).

*  Potřebujete linuxový virtuální počítač jako cíl nasazení.  Další informace najdete v článku o [vytvoření a správě linuxových virtuálních počítačů pomocí rozhraní příkazového řádku Azure](./tutorial-manage-vm.md).

*  Otevřete příchozí port 80 pro virtuální počítač. Další informace najdete v článku o [vytvoření skupin zabezpečení sítě pomocí Azure Portalu](../../virtual-network/tutorial-filter-network-traffic.md).

## <a name="get-your-sample-app-code"></a>Získání kódu ukázkové aplikace

Pokud už máte aplikaci v GitHubu, kterou chcete nasadit, můžete zkusit vytvořit kanál pro tento kód.

Pokud jste však nového uživatele, můžete začít lépe začít pomocí našeho ukázkového kódu. V takovém případě rozvětvení tohoto úložiště na GitHubu:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic je aplikace [Java pružiny](https://spring.io/guides/gs/spring-boot) , která je sestavená pomocí [Maven](https://spring.io/guides/gs/maven/).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Tato Node.js aplikace byla sestavena prostřednictvím [Yeoman](https://yeoman.io/learning/index.html). Používá Express, Bower a Grunt. Jako závislosti také obsahuje několik balíčků npm.
> Součástí této ukázky je také skript, který nainstaluje Nginx a nasadí tuto aplikaci. Spouští se na virtuálních počítačích. Tento skript konkrétně:
> 1. Nainstaluje Node, Nginx a PM2
> 2. Nakonfiguruje Nginx a PM2
> 3. Spustí aplikaci Node

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Předpoklady pro virtuální počítač se systémem Linux

Ukázkové aplikace uvedené výše byly testovány na Ubuntu 16,04 a doporučujeme použít pro tento rychlý Start stejnou verzi virtuálního počítače se systémem Linux.
Postupujte podle dalších kroků popsaných níže v závislosti na zásobníku modulu runtime používaném pro aplikaci.

#### <a name="java"></a>[Java](#tab/java)

- Pro nasazování aplikací založených na spouštěcím a jarním cloudu v jazyce Java vytvořte v Azure virtuální počítač Linux pomocí [této](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) šablony, která poskytuje plně podporovaný modul runtime založený na OpenJDK.
- Pro nasazení Java servletů na serveru Tomcat vytvořte pomocí [této](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) šablony Azure virtuální počítač se systémem Linux s jazykem Java 8 a [nakonfigurujte Tomcat 9. x jako službu](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- Pro nasazení aplikace založené na jazyce Java EE použijte šablonu Azure k vytvoření [virtuálního počítače se systémem Linux + Java + WebSphere 9. x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) nebo [Linux VM + Java + WebLogic 12. x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) nebo [Linux VM + Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) + WildFly/JBoss 14 


#### <a name="javascript"></a>[JavaScript](#tab/java-script)

K instalaci aplikace JavaScriptu nebo Node.js aplikace budete potřebovat virtuální počítač Linux s webovým serverem Nginx pro nasazení aplikace.
Pokud ještě nemáte virtuální počítač se systémem Linux s Nginx, vytvořte ho teď v Azure pomocí kroků v [tomto příkladu](./quick-create-cli.md).

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Vytvoření prostředí Azure Pipelines s virtuálními počítači Azure

Virtuální počítače se dají přidat jako prostředky v rámci [prostředí](/azure/devops/pipelines/process/environments) a můžou se zaměřit na nasazení na více počítačů. Zobrazení historie nasazení v rámci prostředí poskytují sledovatelnost z virtuálního počítače do kanálu a pak na potvrzení.

Prostředí můžete vytvořit v centru "**prostředí**" v části "**kanály**".
1.  Přihlaste se ke svojí organizaci Azure DevOps a přejděte k projektu.
2.  V projektu přejděte na stránku **kanály** . Pak zvolte **prostředí** a klikněte na **vytvořit prostředí**. Zadejte **název** (povinné) pro prostředí a **Popis**.
3.  Vyberte možnost **Virtual Machines** jako **prostředek** , který chcete přidat do prostředí, a klikněte na **Další**.
4.  Vyberte operační systém (Windows/Linux) a **zkopírujte registrační skript PS**. 
5.  Nyní spusťte zkopírovaný skript z příkazového řádku PowerShellu Správce na každém cílovém virtuálním počítači, který chcete zaregistrovat v tomto prostředí.
    > [!NOTE]
    > - Token Personal Access přihlášeného uživatele je předem vložen do skriptu, jehož platnost vyprší ke stejnému dni, v němž je zkopírovaný skript nepoužitelný.
    > - Pokud už na svém VIRTUÁLNÍm počítači běží nějaký agent, zadejte pro něj jedinečný název, který se má zaregistrovat v prostředí.
6.  Jakmile je virtuální počítač zaregistrován, začne se zobrazovat jako prostředek prostředí v části "prostředky" v prostředí.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Pokud chcete přidat víc virtuálních počítačů, můžete skript znovu zobrazit a zkopírovat kliknutím na Přidat prostředek a volbou Virtual Machines jako prostředku. Tento skript by zůstal stejný jako u všech virtuálních počítačů, které se mají do tohoto prostředí přidat. 
8.  Každý počítač komunikuje s Azure Pipelines pro koordinaci nasazení aplikace.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. K virtuálnímu počítači můžete přidat značky jako součást interaktivního registračního skriptu PowerShellu (nebo) můžete také přidat nebo odebrat stejné informace ze zobrazení prostředků kliknutím na tři tečky na konci každého prostředku virtuálního počítače v zobrazení prostředky.

   Značky, které přiřadíte, umožňují omezit nasazení na konkrétní virtuální počítače, když se prostředí používá v úloze nasazení. Jednotlivé značky jsou omezené na 256 znaků, ale počet značek, které můžete použít, není nijak omezený.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Definování kanálu sestavení CI

Budete potřebovat kanál sestavení průběžné integrace (CI), který publikuje vaši webovou aplikaci, a také skript nasazení, který se dá spustit místně na serveru Ubuntu. Nastavte kanál sestavení CI na základě modulu runtime, který chcete použít. 

1. Přihlaste se ke svojí organizaci Azure DevOps a přejděte k projektu.

1. V projektu přejděte na stránku **kanály** . Pak zvolte akci pro vytvoření nového kanálu.

1. Projděte si kroky průvodce, a to tak, že nejprve vyberete **GitHub** jako umístění vašeho zdrojového kódu.

1. Možná budete přesměrováni na GitHub a přihlásíte se. Pokud ano, zadejte své přihlašovací údaje GitHubu.

1. Když se zobrazí seznam úložišť, vyberte požadované úložiště ukázkové aplikace.

1. Azure Pipelines bude analyzovat vaše úložiště a doporučit vhodnou šablonu kanálu.

#### <a name="java"></a>[Java](#tab/java)

Vyberte **počáteční** šablonu a zkopírujte následující fragment kódu YAML, který sestaví projekt Java a spustí testy s Apache Maven:

```YAML
jobs:
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

Pro další pokyny postupujte podle kroků uvedených v části [sestavování aplikace Java pomocí Maven](/azure/devops/pipelines/ecosystems/java).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Vyberte **počáteční** šablonu a zkopírujte následující fragment kódu YAML, který vytvoří obecný projekt Node.js s npm.

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

Další pokyny najdete v postupu [sestavení aplikace Node.js pomocí Gulp](/azure/devops/pipelines/ecosystems/javascript).

- Podívejte se na kanál, abyste viděli, co dělá. Ujistěte se, že všechny výchozí vstupy jsou vhodné pro váš kód.

- Vyberte **Uložit a spustit**, pak vyberte **potvrdit přímo do hlavní větve** a pak zvolte **Uložit a spustit** znovu.

- Spustí se nový běh. Počkejte na dokončení.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Definování kroků CD pro nasazení na virtuální počítač se systémem Linux

1. Změňte soubor YAML pro výše uvedený kanál tak, aby zahrnoval [úlohu nasazení](/azure/devops/pipelines/process/deployment-jobs) , a to tak, že odkazuje na prostředí a prostředky virtuálních počítačů, které jste používali dříve pomocí syntaxe YAML níže:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web
   ```
2. Můžete vybrat konkrétní sady virtuálních počítačů z prostředí pro příjem nasazení zadáním **značek** , které jste definovali pro každý virtuální počítač v prostředí.
[Tady](/azure/devops/pipelines/yaml-schema?tabs=schema#deployment-job) je kompletní schéma YAML pro úlohu nasazení.

3. Můžete zadat buď `runOnce` `rolling` strategii nasazení, nebo. 

   `runOnce` je nejjednodušší strategii nasazení, která zaznamená, že všechny zavěšení životního cyklu, konkrétně, `preDeploy` `deploy` `routeTraffic` a `postRouteTraffic` , se spustí jednou. Pak se `on:` `success` spustí buď nebo `on:` `failure` .

   Níže je příklad fragmentu YAML pro `runOnce` :
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

4. Níže je uveden příklad fragmentu YAML, který můžete použít k definování postupné strategie pro virtuální počítače aktualizace až 5 cílů v každé iteraci. `maxParallel` určí počet cílů, které lze paralelně nasadit. Účty výběru pro absolutní číslo nebo procento cílů, které musí zůstat dostupné kdykoli, kromě cílů, které se nasazují na. Slouží také k určení stavu úspěch a selhání během nasazování.

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

   Při každém spuštění této úlohy se historie nasazení zaznamená do `<environment name>` prostředí, které jste vytvořili a zaregistrovali pro virtuální počítače.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Spuštění kanálu a získání zobrazení sledovatelnosti v prostředí
Zobrazení nasazení prostředí poskytují kompletní sledovatelnost potvrzení a pracovní položky a historii nasazení mezi kanály pro každé prostředí nebo prostředek.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Další kroky
- Můžete pokračovat k [přizpůsobení kanálu](/azure/devops/pipelines/customize-pipeline) , který jste právě vytvořili.
- Další informace o tom, co můžete dělat v kanálech YAML, najdete v tématu Referenční informace ke [schématu YAML](/azure/devops/pipelines/yaml-schema).
- Pokud chcete zjistit, jak nasadit sadu LAMP (Linux, Apache, MySQL a PHP), přejděte k dalšímu kurzu.

> [!div class="nextstepaction"]
> [Nasazení zásobníku LAMP](tutorial-lamp-stack.md)
