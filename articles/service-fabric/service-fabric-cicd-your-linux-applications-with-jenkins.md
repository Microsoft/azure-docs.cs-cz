---
title: "Průběžné sestavení a integraci pro vaše aplikace Azure Service Fabric Linux pomocí volaných | Microsoft Docs"
description: "Průběžné sestavení a integraci pro vaše aplikace Service Fabric Linux pomocí volaných"
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: 
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: saysa
ms.openlocfilehash: 00a6d04e7ec1da48ea17af4f17d3aba3cf4407d6
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Použití volaných sestavení a nasazení aplikací systému Linux
Jenkins je oblíbený nástroj pro průběžnou integraci a nasazování aplikací. Tady je postup, kterým můžete sestavit a nasadit aplikaci Azure Service Fabric s využitím Jenkinse.

## <a name="general-prerequisites"></a>Obecné požadavky
- Máte lokálně nainstalovaný Git. Odpovídající verzi Gitu (v závislosti na operačním systému) můžete nainstalovat ze [stránky pro stažení Gitu](https://git-scm.com/downloads). Pokud jste ještě Git nepoužívali, další informace najdete v [dokumentaci ke Gitu](https://git-scm.com/docs).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Nastavení Jenkinse uvnitř clusteru Service Fabric

Jenkinse můžete nastavit uvnitř clusteru Service Fabric nebo mimo něj. Následující části vysvětlují, jak nastavit je uvnitř clusteru při použití účtu úložiště Azure pro uložení stavu instance kontejneru.

### <a name="prerequisites"></a>Požadavky
1. Máte připravený cluster Service Fabric s Linuxem. Na clusteru Service Fabric vytvořeném z webu Azure Portal je už nainstalovaný Docker. Pokud cluster spouštíte místně, můžete ke kontrole, jestli je Docker nainstalovaný, použít příkaz ``docker info``. Pokud není nainstalovaný, nainstalujte ho pomocí následujících příkazů:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ``` 

   > [!NOTE]
   > Zajistěte, aby byl 8081 port jako vlastní koncový bod v clusteru.
   >

2. Klonování aplikace, pomocí následujících kroků:
  ```sh
  git clone https://github.com/suhuruli/jenkins-container-application.git
  cd jenkins-container-application
  ```

3. Zachování stavu kontejneru volaných ve sdílené složce:
  * Vytvoření účtu úložiště Azure v **stejné oblasti** jako cluster s názvem, jako ``sfjenkinsstorage1``.
  * Vytvoření **sdílené složky** v části úložiště účet s názvem, jako ``sfjenkins``.
  * Klikněte na **připojit** pro sdílené složky a Poznámka hodnoty zobrazuje v části **připojení z Linux**, hodnota by měla vypadat podobná té následující:
  ```sh
  sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
  ```

  > [!NOTE]
  > Pro sdílené složky cifs připojení musíte mít cifs utils balíček nainstalován v uzlu clusteru.       
  >

4. Aktualizujte zástupný symbol hodnoty v ```setupentrypoint.sh``` skriptu s podrobnostmi úložiště azure od kroku 3.
```sh
vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
```
  * Nahraďte ``[REMOTE_FILE_SHARE_LOCATION]`` s hodnotou ``//sfjenkinsstorage1.file.core.windows.net/sfjenkins`` z výstupu connect v kroku 3 výše.
  * Nahraďte ``[FILE_SHARE_CONNECT_OPTIONS_STRING]`` s hodnotou ``vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777`` z kroku 3 výše.

5. **Zabezpečení clusteru pouze:** Chcete-li nakonfigurovat nasazení aplikací na zabezpečené clusteru z volaných, musí být přístupné v kontejneru volaných certifikát. Na clusterech Linux certificates(PEM) jednoduše selhání se zkopírují z obchodu určeného X509StoreName do kontejneru. V ApplicationManifest pod ContainerHostPolicies přidat odkaz na tento certifikát a aktualizujte hodnotu kryptografický otisk. Kryptografický otisk hodnota musí být, že z k certifikátu umístěné na uzlu.
  ```xml
  <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
  ```
  > [!NOTE]
  > Kryptografický otisk hodnota musí být stejný jako certifikát, který se používá k připojení do zabezpečené clusteru. 
  >

6. Připojte se ke clusteru a nainstalujte aplikaci kontejneru.

  **Zabezpečení clusteru**
  ```sh
  sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
  bash Scripts/install.sh
  ```

  **Nezabezpečená clusteru**
  ```sh
  sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
  bash Scripts/install.sh
  ```

  Tím se do clusteru nainstaluje kontejner s Jenkinsem, který můžete monitorovat pomocí Service Fabric Exploreru.

    > [!NOTE]
    > Může trvat několik minut pro bitovou kopii volaných ke stažení v clusteru.
    >

### <a name="steps"></a>Kroky
1. V prohlížeči přejděte na ``http://PublicIPorFQDN:8081``. Najdete tam cestu k počátečnímu heslu správce vyžadovanému k přihlášení. 
2. Podívejte se na Service Fabric Explorer k určení, na který uzel kontejneru volaných běží. Secure Shell (SSH) přihlášení pro tento uzel.
```sh
ssh user@PublicIPorFQDN -p [port]
``` 
3. Získejte ID instance kontejneru pomocí příkazu ``docker ps -a``.
4. Přihlaste se přes SSH (Secure Shell) ke kontejneru a vložte cestu, která se zobrazila na portálu Jenkinse. Pokud se na portálu zobrazila například cesta `PATH_TO_INITIAL_ADMIN_PASSWORD`, spusťte:

  ```sh
  docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
  ```
  ```sh
  cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the pasword value
  ```
5. Na stránce volaných získávání spuštění vyberte vyberte modulů plug-in pro instalaci možnost vyberte **žádné** zaškrtávací políčko a klikněte na tlačítko nainstalovat.
6. Vytvořte uživatele nebo vyberte, chcete-li pokračovat, protože správce.

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Nastavení Jenkinse mimo cluster Service Fabric

Jenkinse můžete nastavit uvnitř clusteru Service Fabric nebo mimo něj. Následující části vysvětlují, jak ho nastavit mimo cluster.

### <a name="prerequisites"></a>Požadavky
Potřebujete mít nainstalovaný Docker. Pomocí následujících příkazů můžete Docker nainstalovat z terminálu:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

Když teď v terminálu spustíte příkaz ``docker info``, na výstupu by se mělo zobrazit, že je spuštěná služba Docker.

### <a name="steps"></a>Kroky
  1. Bitovou kopii volaných prostředků infrastruktury služby kontejneru pro vyžádání obsahu: ``docker pull rapatchi/jenkins:v10``. Tento image obsahuje modul plug-in služby Fabric volaných předinstalovaným.
  2. Spusťte image kontejneru: ``docker run -itd -p 8080:8080 rapatchi/jenkins:v10``
  3. Získejte ID instance image kontejneru. Pomocí příkazu ``docker ps –a`` můžete vypsat všechny kontejnery Dockeru.
  4. Přihlaste se k portálu Jenkinse pomocí následujících kroků:

    * ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```
    Pokud ID kontejneru je 2d24a73b5964, použijte 2d24.
    * Toto heslo se vyžaduje pro přihlášení k řídicímu panelu Jenkinse z portálu, což je ``http://<HOST-IP>:8080``.
    * Jakmile se poprvé přihlásíte, můžete si vytvořit vlastní uživatelský účet, který budete používat pro další účely, nebo můžete dál používat účet uživatele s právy pro správu. Když vytvoříte uživatele, budete s ním muset pokračovat.
  5. Nastavte GitHub pro práci s Jenkinsem, a to pomocí kroků uvedených v tématu věnovaném [vygenerování nového klíče SSH a jeho přidání k agentovi SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
        * Pomocí pokynů z GitHubu vygenerujte klíč SSH a přidejte ho do účtu GitHubu, který je hostitelem úložiště.
        * Příkazy popsané u předchozího odkazu spusťte v prostředí Jenkins Dockeru (ne na hostiteli).
      * Pokud se chcete k prostředí Jenkinse přihlásit z hostitele, použijte následující příkazy:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Zkontrolujte, že cluster nebo počítač, ve kterém se hostuje image kontejneru Jenkinse, má veřejnou IP adresu. To umožňuje, aby instance Jenkinse dostávala oznámení z GitHubu.


## <a name="create-and-configure-a-jenkins-job"></a>Vytvoření a konfigurace úlohy Jenkinse

1. Na řídicím panelu vytvořte **novou položku**.
2. Zadejte název položky (třeba **MyJob**). Vyberte **free-style project** (volný styl projektu) a klikněte na **OK**.
3. Přejděte na stránku úlohy a klikněte na **Configure** (Konfigurovat).

   a. V části Obecné zaškrtněte políčko **Githubu projektu**, a zadejte svoji adresu URL projektu Githubu. Tato adresa je hostitelem aplikace Service Fabric v Javě, kterou chcete integrovat s postupy průběžného nasazování a integrace (CI/CD) Jenkinse (např. ``https://github.com/sayantancs/SFJenkins``).

   b. V části **Source Code Management** (Správa zdrojového kódu) vyberte **Git**. Zadejte adresu URL úložiště, které je hostitelem aplikace Service Fabric v Javě, kterou chcete integrovat s postupy CI/CD Jenkinse (např. ``https://github.com/sayantancs/SFJenkins.git``). V této části můžete také zadat, jaká větev se má sestavit (například **/master**).
4. Nakonfigurujte *GitHub* (který je hostitelem úložiště) tak, aby mohl komunikovat s Jenkinsem. Použijte k tomu následující postup:

   a. Přejděte na stránku vašeho úložiště Github. Přejděte do části **Settings** (Nastavení) > **Integrations and Services** (Integrace a služby).

   b. Vyberte **Add Service** (Přidat službu), zadejte **Jenkins** a vyberte **Jenkins-Github plugin** (Modul plug-in Jenkins-Github).

   c. Zadejte adresu URL webhooku Jenkinse (ve výchozím nastavení by měla být ``http://<PublicIPorFQDN>:8081/github-webhook/``). Klikněte na **add/update service** (Přidat/aktualizovat službu).

   d. Do instance Jenkinse se odešle testovací událost. V GitHubu by se měla vedle webhooku zobrazit zelená značka zaškrtnutí a váš projekt se sestaví.

   e. V části **Build Triggers** (Triggery sestavení) vyberte požadovanou možnost sestavení. V tomto příkladě použití chcete aktivovat sestavení při každém vložení metodou Push do úložiště. Proto vyberete **GitHub hook trigger for GITScm polling** (Trigger webhooku GitHubu pro cyklické dotazování GitHubu). (Dřív se tato možnost nazývala **Build when a change is pushed to GitHub** (Sestavit při vložení změny metodou Push do GitHubu).

   f. **Sestavení oddílu aplikací v jazyce Java:** pod **sestavení části**, z rozevírací nabídky **přidat krok sestavení**, vyberte možnost **vyvolání skriptu Gradle**. Ve widgetu, která se dodává otevření nabídky Rozšířené, zadejte cestu k **kořenové sestavení skriptu** pro vaši aplikaci. To převezme build.gradle ze zadané cesty a odpovídajícím způsobem funguje. Pokud vytvoříte projekt ``MyActor`` (pomocí modulu plug-in Eclipse nebo generátoru Yeoman), pole pro kořenový skript sestavení by mělo obsahovat ``${WORKSPACE}/MyActor``. Příklad toho, jak by to mělo vypadat, najdete na následujícím snímku:

    ![Akce sestavení v Jenkinsu pro Service Fabric][build-step]

   g. **Sestavení oddílu pro aplikace .net Core:** pod **sestavení části**, z rozevírací nabídky **přidat krok sestavení**, vyberte možnost **spustit prostředí**. V poli příkaz, který se zobrazí adresáři nejprve musí změnit tak, aby cestu, kde je umístěn soubor build.sh. Jakmile změníte adresáři build.sh skript lze spustit a bude sestavení aplikace.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

    Zde jsou exmaple příkazy, které se používají k vytvoření [čítač služby](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) ukázku s názvem úlohy volaných CounterServiceApplication.

    ![Akce sestavení v Jenkinsu pro Service Fabric][build-step-dotnet]
  
   h. V rozevírací nabídce **Post-Build Actions** (Akce po sestavení) vyberte **Deploy Service Fabric Project** (Nasazení projektu Service Fabric). V této části je potřeba zadat podrobnosti o clusteru, ve kterém se bude nasazovat aplikace Service Fabric zkompilovaná Jenkinsem. Cesta k certifikátu naleznete ve zobrazování hodnotu echo Certificates_JenkinsOnSF_Code_MyCert_PEM proměnnou prostředí z kontejneru uvnitř tohoto kontejneru. Tuto cestu můžete použít pro klíč klienta a pole klientského certifikátu.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
    Můžete také zadat další podrobnosti o aplikaci, které se používají k jejímu nasazení. Příklad toho, jak by to mělo vypadat, najdete na následujícím snímku:

    ![Akce sestavení v Jenkinsu pro Service Fabric][post-build-step]

      > [!NOTE]
      > Zde uvedený cluster může být stejný jako cluster, který hostuje aplikaci Jenkins typu kontejner, pokud k nasazení image kontejneru Jenkins používáte Service Fabric.
      >

## <a name="next-steps"></a>Další postup
GitHub a Jenkins jsou teď nakonfigurované. Můžete provést nějaké ukázkové změny v projektu například v úložišti ``MyActor``https://github.com/sayantancs/SFJenkins. Metodou Push změny vložte do vzdálené větve ``master`` (nebo jakékoli jiné větve, kterou jste si pro práci nakonfigurovali). Tím se aktivuje nakonfigurovaná úloha Jenkinse ``MyJob``. Tato úloha načte změny z GitHubu a použije je k sestavení aplikace, kterou nasadí do koncového bodu clusteru, který jste zadali v akcích po sestavení.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png
  [post-build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-step.png

