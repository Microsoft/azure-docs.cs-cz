---
title: Nastavení CI/CD pro aplikaci Azure Service Fabric v Javě | Microsoft Docs
description: V tomto kurzu zjistíte, jak pomocí Jenkinse nastavit průběžnou integraci pro nasazení aplikace Service Fabric v Javě.
services: service-fabric
documentationcenter: java
author: suhuruli
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/26/2018
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: bd986b8741b55a10018f7400c9415e7aedfbf119
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
ms.locfileid: "29949833"
---
# <a name="tutorial-set-up-a-jenkins-environment-with-service-fabric"></a>Kurz: Nastavení prostředí Jenkins s využitím Service Fabric
Tento kurz je pátou částí série. Ukazuje použití Jenkinse k nasazování upgradů do aplikace. V tomto kurzu se pomocí modulu plug-in Jenkinse pro Service Fabric v kombinaci s úložištěm GitHub hostujícím hlasovací aplikaci nasadí tato aplikace do clusteru. 

V páté části této série se naučíte:
> [!div class="checklist"]
> * Nasazení kontejneru s Jenkinsem pro Service Fabric na místní počítač
> * Nastavení prostředí Jenkins pro nasazení do Service Fabric
> * Upgrade aplikace

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * [Sestavení aplikace Service Fabric Reliable Services v Javě](service-fabric-tutorial-create-java-app.md)
> * [Nasazení a ladění aplikace v místním clusteru](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Nasazení aplikace do clusteru Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Nastavit monitorování a diagnostiku aplikace](service-fabric-tutorial-java-elk.md)
> * Nastavení CI/CD


## <a name="prerequisites"></a>Požadavky
- Nainstalovaný Git ze [stránky pro stažení Gitu](https://git-scm.com/downloads) na místním počítači. Další informace o Gitu najdete v [dokumentaci pro Git](https://git-scm.com/docs).
- Praktické znalosti [Jenkinse](https://jenkins.io/).
- Vytvořený účet [GitHub](https://github.com/) a znalost práce s GitHubem.
- Nainstalovaný [Docker](https://www.docker.com/community-edition) na místním počítači.

## <a name="pull-and-deploy-service-fabric-jenkins-container-image"></a>Stažení a nasazení image kontejneru s Jenkinsem pro Service Fabric
Jenkinse můžete nastavit uvnitř clusteru Service Fabric nebo mimo něj. Následující pokyny ukazují jeho nastavení mimo cluster s použitím poskytnuté image Dockeru. Můžete však použít i předkonfigurované prostředí pro sestavení Jenkinse. Následující image kontejneru je součástí instalace modulu plug-in pro Service Fabric a je připravená k okamžitému použití se Service Fabric. 

1. Stáhněte si image kontejneru s Jenkinsem pro Service Fabric: ``docker pull rapatchi/jenkins:v10``. Tato image je součástí instalace modulu plug-in Jenkinse pro Service Fabric.

2. Spusťte image kontejneru s připojeným umístěním, kde se na místním počítači nacházejí vaše certifikáty.

    ```bash
    docker run -itd -p 8080:8080 -v /Users/suhuruli/Documents/Work/Samples/service-fabric-java-quickstart/AzureCluster:/tmp/myCerts rapatchi/jenkins:v10
    ```

3. Získejte ID instance image kontejneru. Pomocí příkazu ``docker ps –a`` můžete vypsat všechny kontejnery Dockeru.

4. Spuštěním následujícího příkazu načtěte heslo vaší instance Jenkinse:

    ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```

    Pokud ID kontejneru je 2d24a73b5964, použijte 2d24.
    * Toto heslo se vyžaduje pro přihlášení k řídicímu panelu Jenkinse z portálu, což je ``http://<HOST-IP>:8080``.
    * Jakmile se poprvé přihlásíte, můžete si vytvořit vlastní uživatelský účet nebo použít účet správce.
    
5. Nastavte GitHub pro práci s Jenkinsem, a to pomocí kroků uvedených v tématu věnovaném [vygenerování nového klíče SSH a jeho přidání k agentovi SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/). Vzhledem k tomu, že se příkazy spouští z kontejneru Dockeru, postupujte podle pokynů pro prostředí Linuxu. 
    * Podle pokynů uvedených na GitHubu vygenerujte klíč SSH. Pak přidejte klíč SSH do účtu GitHubu, který je hostitelem úložiště.
    * Příkazy popsané u předchozího odkazu spusťte v prostředí Jenkins Dockeru (ne na hostiteli).
    * Pokud se chcete k prostředí Jenkinse přihlásit z hostitele, použijte následující příkazy:

    ```sh
    docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
    ```

    Zkontrolujte, že cluster nebo počítač, ve kterém se hostuje image kontejneru Jenkinse, má veřejnou IP adresu. Veřejná IP adresa umožňuje, aby instance Jenkinse dostávala oznámení z GitHubu.    

## <a name="create-and-configure-a-jenkins-job"></a>Vytvoření a konfigurace úlohy Jenkinse

1. Nejprve vytvořte úložiště, které můžete použít k hostování projektu Voting na GitHubu, pokud žádné nemáte. Ve zbývající části tohoto kurzu má úložiště název **dev_test**.

2. Na řídicím panelu Jenkinse vytvořte **novou položku**.

3. Zadejte název položky (třeba **MyJob**). Vyberte **free-style project** (volný styl projektu) a klikněte na **OK**.

4. Přejděte na stránku úlohy a klikněte na **Configure** (Konfigurovat).

   a. V části s obecnými informacemi zaškrtněte políčko **GitHub project** (Projekt GitHub) a zadejte adresu URL vašeho projektu GitHub. Tato adresa je hostitelem aplikace Service Fabric v Javě, kterou chcete integrovat s postupy průběžného nasazování a integrace (CI/CD) Jenkinse (např. ``https://github.com/testaccount/dev_test``).

   b. V části **Source Code Management** (Správa zdrojového kódu) vyberte **Git**. Zadejte adresu URL úložiště, které je hostitelem aplikace Service Fabric v Javě, kterou chcete integrovat s postupy CI/CD Jenkinse (např. *https://github.com/testaccount/dev_test.git*). V této části můžete také zadat, jaká větev se má sestavit (například **/master**).

5. Nakonfigurujte *GitHub* (který je hostitelem úložiště) tak, aby mohl komunikovat s Jenkinsem. Použijte k tomu následující postup:

   a. Přejděte na stránku vašeho úložiště Github. Přejděte do části **Settings** (Nastavení) > **Integrations and Services** (Integrace a služby).

   b. Vyberte **Add Service** (Přidat službu), zadejte **Jenkins** a vyberte **Jenkins-Github plugin** (Modul plug-in Jenkins-Github).

   c. Zadejte adresu URL webhooku Jenkinse (ve výchozím nastavení by měla být ``http://<PublicIPorFQDN>:8081/github-webhook/``). Klikněte na **add/update service** (Přidat/aktualizovat službu).

   d. Do instance Jenkinse se odešle testovací událost. V GitHubu by se měla vedle webhooku zobrazit zelená značka zaškrtnutí a váš projekt se sestaví.

   ![Konfigurace Jenkinse pro Service Fabric](./media/service-fabric-tutorial-java-jenkins/jenkinsconfiguration.png)

6. V části **Build Triggers** (Triggery sestavení) vyberte požadovanou možnost sestavení. V tomto příkladě použití chcete aktivovat sestavení při každém vložení metodou Push do úložiště. Proto vyberete **GitHub hook trigger for GITScm polling** (Trigger webhooku GitHubu pro cyklické dotazování GitHubu).

7. V části **Build** (Sestavení) z rozevírací nabídky **Add build step** (Přidat krok sestavení) vyberte možnost **Invoke Gradle Script** (Vyvolání skriptu Gradle). Ve widgetu, který se zobrazí, otevřete rozšířenou nabídku a do pole **Root build script** (Kořenový skript sestavení) zadejte cestu ke kořenovému skriptu sestavení pro vaši aplikaci. Sestavení si ze zadané cesty vezme soubor build.gradle a bude pracovat odpovídajícím způsobem.

    ![Akce sestavení v Jenkinsu pro Service Fabric](./media/service-fabric-tutorial-java-jenkins/jenkinsbuildscreenshot.png)
  
8. V rozevírací nabídce **Post-Build Actions** (Akce po sestavení) vyberte **Deploy Service Fabric Project** (Nasazení projektu Service Fabric). V této části je potřeba zadat podrobnosti o clusteru, ve kterém se bude nasazovat aplikace Service Fabric zkompilovaná Jenkinsem. Cesta k certifikátu je cesta, ke které se připojil svazek (/tmp/myCerts). 
   
    Můžete také zadat další podrobnosti, které se použijí k nasazení aplikace. Příklad podrobností o aplikaci najdete na následujícím snímku obrazovky:

    ![Akce sestavení v Jenkinsu pro Service Fabric](./media/service-fabric-tutorial-java-jenkins/sfjenkins.png)

    > [!NOTE]
    > Zde uvedený cluster může být stejný jako cluster, který hostuje aplikaci Jenkins typu kontejner, pokud k nasazení image kontejneru Jenkins používáte Service Fabric.
    >

## <a name="update-your-existing-application"></a>Aktualizace existující aplikace 

1. V souboru *VotingApplication/VotingWebPkg/Code/wwwroot/index.html* aktualizujte nadpis HTML na **Service Fabric Voting Sample V2**. 

    ```html 
    <div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
        <div class="container-fluid">
            <div class="row">
                <div class="col-xs-8 col-xs-offset-2 text-center">
                    <h2>Service Fabric Voting Sample V2</h2>
                </div>
            </div>
        </div>
    </div>
    ```

2. V souboru *Voting/VotingApplication/ApplicationManifest.xml* aktualizujte verze **ApplicationTypeVersion** a **ServiceManifestVersion** na hodnotu **2.0.0**. 

    ```xml
    <?xml version="1.0" encoding="utf-8" standalone="no"?>
    <ApplicationManifest xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingApplicationType" ApplicationTypeVersion="2.0.0">
      <Description>Voting Application</Description>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="2.0.0"/>
      </ServiceManifestImport>
      <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="VotingDataServicePkg" ServiceManifestVersion="1.0.0"/>
        </ServiceManifestImport>
        <DefaultServices>
          <Service Name="VotingWeb">
             <StatelessService InstanceCount="1" ServiceTypeName="VotingWebType">
                <SingletonPartition/>
             </StatelessService>
          </Service>      
       <Service Name="VotingDataService">
                <StatefulService MinReplicaSetSize="3" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="3">
                    <UniformInt64Partition HighKey="9223372036854775807" LowKey="-9223372036854775808" PartitionCount="1"/>
                </StatefulService>
            </Service>
        </DefaultServices>      
    </ApplicationManifest>
    ```

3. V souboru *Voting/VotingApplication/VotingWebPkg/ServiceManifest.xml* aktualizujte pole **Version** ve značce **ServiceManifest** a pole **Version** ve značce **CodePackage** na hodnotu **2.0.0**.

    ```xml
    <CodePackage Name="Code" Version="2.0.0">
    <EntryPoint>
        <ExeHost>
        <Program>entryPoint.sh</Program>
        </ExeHost>
    </EntryPoint>
    </CodePackage>
    ```

4. Úlohu Jenkinse, která provádí upgrade aplikace, inicializujete nasdílením nových změn do vašeho úložiště GitHub. 

5. V Service Fabric Exploreru klikněte na rozevírací seznam **Aplikace**. Pokud chcete zobrazit stav upgradu, klikněte na kartu **Probíhající upgrady**.

    ![Probíhající upgrade](./media/service-fabric-tutorial-create-java-app/upgradejava.png)

6. Když teď přejdete na adresu **http://\<IP_adresa_hostitele>:8080**, zobrazí se spuštěná hlasovací aplikace se všemi funkcemi. 

    ![Místně spuštěná hlasovací aplikace](./media/service-fabric-tutorial-java-jenkins/votingv2.png)

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Nasazení kontejneru s Jenkinsem pro Service Fabric na místní počítač
> * Nastavení prostředí Jenkins pro nasazení do Service Fabric
> * Upgrade aplikace

* Podívejte se na další [ukázky v Javě](https://github.com/Azure-Samples/service-fabric-java-getting-started).