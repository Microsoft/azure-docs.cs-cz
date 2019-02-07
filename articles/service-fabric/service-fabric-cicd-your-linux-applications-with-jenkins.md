---
title: Průběžné sestavování a průběžná integrace pro aplikace Azure Service Fabric s Linuxem pomocí Jenkinse | Dokumentace Microsoftu
description: Průběžné sestavování a průběžná integrace pro aplikace Service Fabric s Linuxem pomocí Jenkinse
services: service-fabric
documentationcenter: java
author: sayantancs
manager: jpconnock
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/31/2018
ms.author: saysa
ms.openlocfilehash: 7abc15264a44c969f57071e84ffcedca30d326fb
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55766312"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Použití Jenkinse k vytvoření a nasazení Linuxové aplikace
Jenkins je oblíbený nástroj pro průběžnou integraci a nasazování aplikací. Tady je postup, kterým můžete sestavit a nasadit aplikaci Azure Service Fabric s využitím Jenkinse.

## <a name="topic-overview"></a>Přehled tématu
Tento článek se týká několik možné způsoby, jak se nastavení prostředí Jenkins i jiné způsoby, jak nasadit aplikaci do clusteru Service Fabric, jakmile byl vytvořen. Postupujte podle následujících obecných kroků úspěšně nastavení Jenkinse, vložit změny z Githubu, sestavení aplikace a nasaďte ji do vašeho clusteru:

1. Ujistěte se, že instalujete [požadavky](#prerequisites).
1. Potom postupujte podle pokynů v některém z těchto oddílech Jenkinse nastavit:
   * [Nastavení Jenkinse uvnitř clusteru Service Fabric](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Nastavení Jenkinse mimo cluster Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster), nebo
   * [Instalace modulu plug-in Service Fabric ve stávajícím prostředí Jenkins](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Po nastavení Jenkinse, postupujte podle kroků v [vytvoření a konfigurace úlohy Jenkinse](#create-and-configure-a-jenkins-job) nastavte GitHub pro aktivační událost Jenkinse, když dojde ke změně do vaší aplikace a nakonfigurovat svůj kanál úlohy Jenkinse pomocí kroku sestavení na vyžádání změny z Githubu a sestavení aplikace. 
1. A konečně nakonfigurujte krok po sestavení úlohy Jenkinse k nasazení aplikace do clusteru Service Fabric. Konfigurace Jenkinse k nasazení aplikace do clusteru se dvěma způsoby:    
   * Pro vývojová a testovací prostředí, použijte [nakonfigurovat nasazení pomocí koncového bodu správy clusteru](#configure-deployment-using-cluster-management-endpoint). Toto je nejjednodušší metodu nasazení pro nastavení.
   * Pro produkční prostředí, použijte [nakonfigurovat nasazení pomocí přihlašovacích údajů Azure](#configure-deployment-using-azure-credentials). Společnost Microsoft doporučuje tuto metodu pro produkční prostředí, protože pomocí přihlašovacích údajů Azure můžete omezit přístup, který se má úloha Jenkinse s vašimi prostředky Azure. 

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je místně nainstalovaný Git. Můžete nainstalovat odpovídající verzi Gitu z [stránky pro stažení Gitu](https://git-scm.com/downloads) založený na operačním systému. Pokud jste ještě Git, další informace najdete [dokumentace pro Git](https://git-scm.com/docs).
- Tento článek používá *Service Fabric získávání Getting* na Githubu: [ https://github.com/Azure-Samples/service-fabric-java-getting-started ](https://github.com/Azure-Samples/service-fabric-java-getting-started) pro aplikaci sestavit a nasadit. Můžete vytvořit fork tohoto úložiště, pokud chcete postup sledovat, nebo, s určitými úpravami na pokyny k použití projektu Githubu.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Instalace modulu plug-in Service Fabric ve stávajícím prostředí Jenkins
Pokud přidáváte do existujícího prostředí Jenkins modul plug-in Service Fabric, budete potřebovat následující:

- [Service Fabric CLI](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Nezapomeňte nainstalovat rozhraní příkazového řádku na úrovni systému, spíše než na úrovni uživatele, takže Jenkins můžete spustit příkazy rozhraní příkazového řádku. 
   >

- Pokud chcete nasadit aplikace v Javě, nainstalujte [Gradle a otevřete sadu JDK 8.0](service-fabric-get-started-linux.md#set-up-java-development). 
- Nasazení aplikací .NET Core 2.0, nainstalujte [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Po instalaci součásti potřebné pro vaše prostředí, můžete vyhledat Azure modulu plug-in Service Fabric na webu marketplace Jenkins a ji nainstalovat.

Po instalaci modulu plug-in, přeskočte k části [vytvoření a konfigurace úlohy Jenkinse](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Nastavení Jenkinse uvnitř clusteru Service Fabric

Jenkinse můžete nastavit uvnitř clusteru Service Fabric nebo mimo něj. Následující části vysvětlují, jak ho nastavit uvnitř clusteru při použití účtu služby Azure storage pro uložení stavu instance kontejneru.

### <a name="prerequisites"></a>Požadavky
- Máte cluster Service Fabric s Linuxem s nainstalovaným Dockerem. Clustery Service Fabric běžící v Azure už mít nainstalovaný Docker. Pokud používáte cluster místně (OneBox dev environment), zkontrolujte, jestli je Docker nainstalovaný na vašem počítači využít `docker info` příkazu. Pokud není nainstalovaný, nainstalujte ho pomocí následujících příkazů:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Ujistěte se, zda je zadán 8081 port jako vlastní koncový bod v clusteru. Pokud používáte místní cluster, ujistěte se, že port 8081 je otevřený na hostitelském počítači a má veřejnou IP adresu.
   >

### <a name="steps"></a>Kroky
1. Klonování aplikace, pomocí následujících příkazů:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Zachování stavu kontejneru Jenkinse ve sdílené složce:
   1. Vytvořit účet úložiště Azure **stejné oblasti** jako cluster s názvem, jako `sfjenkinsstorage1`.
   1. Vytvoření **sdílené** pod úložiště účtu s názvem, jako `sfjenkins`.
   1. Klikněte na **připojit** pro sdílení souborů a Všimněte si hodnot zobrazuje v části **připojení z Linuxu**, hodnota by měla vypadat podobně jako následující:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Do sdílené složky cifs připojení musíte mít nainstalovaný v uzlech clusteru balíček cifs utils.      
   >

1. Aktualizovat hodnoty zástupných symbolů v `setupentrypoint.sh` skript s podrobnostmi o služby azure storage z kroku 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Nahraďte `[REMOTE_FILE_SHARE_LOCATION]` s hodnotou `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` z výstupu connect v kroku 2.
   * Nahraďte `[FILE_SHARE_CONNECT_OPTIONS_STRING]` s hodnotou `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` z kroku 2.

1. **Zabezpečení clusteru jenom:** 
   
   Pokud chcete nakonfigurovat nasazení aplikace na zabezpečeném clusteru z Jenkins, musí být certifikát clusteru dostupný v rámci kontejner s Jenkinsem. V *ApplicationManifest.xml* souboru, v části **ContainerHostPolicies** značku přidat tento odkaz na certifikát a aktualizujte hodnotu kryptografický otisk, který certifikátu clusteru.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Kromě toho přidejte následující řádky pod **ApplicationManifest** značku (uživatel root) *ApplicationManifest.xml* souboru a aktualizujte hodnotu kryptografického otisku certifikátu clusteru.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Připojení ke clusteru a instalaci aplikace typu kontejner.

   **Secure Cluster**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Ve výstupu předchozího příkazu trvá certifikát ve formátu PEM. Pokud váš certifikát není ve formátu PFX, můžete ho převést následující příkaz. Není-li váš soubor PFX chráněný heslem, zadejte **passin** parametr jako `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Nezabezpečenému clusteru**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Tím se do clusteru nainstaluje kontejner s Jenkinsem, který můžete monitorovat pomocí Service Fabric Exploreru.

   > [!NOTE]
   > Může trvat několik minut, než se image s Jenkinsem pro ke stažení v clusteru.
   >

1. V prohlížeči přejděte na `http://PublicIPorFQDN:8081`. Najdete tam cestu k počátečnímu heslu správce vyžadovanému k přihlášení. 
1. Podívejte se na Service Fabric Exploreru můžete určit, na který uzel je spuštěný kontejner s Jenkinsem. Secure Shell (SSH) se k tomuto uzlu.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Získejte ID instance kontejneru pomocí příkazu `docker ps -a`.
1. Secure Shell (SSH) Přihlaste se ke kontejneru a vložte cestu, která se zobrazila na portálu Jenkinse. Například, pokud na portálu zobrazila cesta `PATH_TO_INITIAL_ADMIN_PASSWORD`, spusťte následující příkazy:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Na stránce Začínáme Jenkinse vyberte vyberte moduly plug-in k instalaci možnost vyberte **žádný** zaškrtávací políčko a klikněte na tlačítko nainstalovat.
1. Vytvořte uživatele nebo vyberte pokračujte jako správce.

Po nastavení Jenkinse, přeskočte k části [vytvoření a konfigurace úlohy Jenkinse](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Nastavení Jenkinse mimo cluster Service Fabric

Jenkinse můžete nastavit uvnitř clusteru Service Fabric nebo mimo něj. Následující části vysvětlují, jak ho nastavit mimo cluster.

### <a name="prerequisites"></a>Požadavky
- Ujistěte se, že je Docker nainstalovaný na vašem počítači. Pomocí následujících příkazů můžete Docker nainstalovat z terminálu:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Při spuštění `docker info` v terminálu, by měl zobrazit výstup, že je spuštěná služba Docker.

### <a name="steps"></a>Kroky
1. Stáhněte si image kontejneru s Jenkinsem pro Service Fabric: `docker pull rapatchi/jenkins:latest`. Tato image je součástí instalace modulu plug-in Jenkinse pro Service Fabric.
1. Spusťte image kontejneru: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Získejte ID instance image kontejneru. Pomocí příkazu `docker ps –a` můžete vypsat všechny kontejnery Dockeru.
1. Přihlaste se k portálu Jenkinse pomocí následujících kroků:

   1. Přihlaste se k prostředí Jenkinse z hostitele. Použít první čtyři číslice ID kontejneru. Například, pokud je ID kontejneru `2d24a73b5964`, použijte `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. V prostředí Jenkins Získejte heslo správce pro vaši instanci kontejneru:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Přihlásit se k řídicímu panelu Jenkinse, otevřete ve webovém prohlížeči na následující adrese URL: `http://<HOST-IP>:8080`. Použijte heslo z předchozího kroku k odemknutí Jenkinse.
   1. (Volitelné.) Po přihlášení poprvé, můžete vytvořit vlastní uživatelský účet a použít pro následující kroky, nebo můžete dál používat účet správce. Pokud vytvoříte uživatele, musíte pokračovat s tímto uživatelem.
1. Nastavte GitHub pro práci s Jenkinsem pomocí postupu v [vygenerování nového klíče SSH a jeho přidání k agentovi SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Pomocí pokynů z GitHubu vygenerujte klíč SSH a přidejte ho do účtu GitHubu, který je hostitelem úložiště.
   * Příkazy popsané u předchozího odkazu spusťte v prostředí Jenkins Dockeru (ne na hostiteli).
   * Pokud se chcete k prostředí Jenkinse přihlásit z hostitele, použijte následující příkaz:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Ujistěte se, že cluster nebo počítač, kde se hostuje image kontejneru Jenkins má veřejnou IP adresu. To umožňuje, aby instance Jenkinse dostávala oznámení z GitHubu.

Po nastavení Jenkinse, pokračujte k další části [vytvoření a konfigurace úlohy Jenkinse](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Vytvoření a konfigurace úlohy Jenkinse

Kroky v této části ukazují, jak konfigurace úlohy Jenkinse reagovat na změny v úložišti GitHub, načíst změny a sestaveny. Na konci této části budete přesměrováni na závěrečné kroky konfigurace úlohy pro nasazení aplikace založené na tom, jestli nasazujete vývojářské/testovací prostředí nebo do produkčního prostředí. 

1. Na řídicím panelu Jenkinse klikněte na tlačítko **nová položka**.
1. Zadejte název položky (třeba **MyJob**). Vyberte **free-style project** (volný styl projektu) a klikněte na **OK**.
1. Otevře se stránka konfigurace úlohy. (Získáte na konfigurace z řídicího panelu Jenkinse klikněte na úlohu a potom klikněte na tlačítko **konfigurovat**).

1. Na **Obecné** kartu, zaškrtněte políčko u **projektu z Githubu**, a zadejte adresu URL vašeho projektu Githubu. Tato adresa je hostitelem aplikace Service Fabric v Javě, kterou chcete integrovat s postupy průběžného nasazování a integrace (CI/CD) Jenkinse (např. `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Na **Správa zdrojového kódu** kartu, vyberte možnost **Git**. Zadejte adresu URL úložiště, které je hostitelem aplikace Service Fabric v Javě, kterou chcete integrovat s postupy CI/CD Jenkinse (např. `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Můžete také určit, jaká větev se má sestavit (například `/master`).
1. Konfigurace vašeho *Githubu* úložiště komunikovat s Jenkinsem:

   a. Přejděte na stránku vašeho úložiště GitHub **nastavení** > **integrace a služby**.

   b. Vyberte **Add Service** (Přidat službu), zadejte **Jenkins** a vyberte **Jenkins-Github plugin** (Modul plug-in Jenkins-Github).

   c. Zadejte adresu URL webhooku Jenkinse (ve výchozím nastavení by měla být `http://<PublicIPorFQDN>:8081/github-webhook/`). Klikněte na **add/update service** (Přidat/aktualizovat službu).

   d. Do instance Jenkinse se odešle testovací událost. V GitHubu by se měla vedle webhooku zobrazit zelená značka zaškrtnutí a váš projekt se sestaví.

1. Na **sestavit aktivační události** Jenkinse kartě, vyberte možnost, chcete, aby sestavení. V tomto příkladu chcete aktivovat sestavení při každém metodou push do úložiště, takže vyberte **GitHub hook trigger pro dotazování GITScm**. (Dřív se tato možnost nazývala **Build when a change is pushed to GitHub** (Sestavit při vložení změny metodou Push do GitHubu).
1. Na **sestavení** kartu, proveďte jednu z následujících možností v závislosti na tom, ať už vytváříte aplikace v Javě nebo aplikaci .NET Core:

   * **Pro aplikace Java:** Z **přidat krok sestavení** rozevíracího seznamu, vyberte **vyvolání skriptu Gradle**. Klikněte na tlačítko **Advanced**. V nabídce Upřesnit, zadejte cestu k **kořenové sestavit skript** pro vaši aplikaci. Sestavení si ze zadané cesty vezme soubor build.gradle a bude pracovat odpovídajícím způsobem. Pro [ActorCounter aplikace](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), toto je: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Akce sestavení v Jenkinsu pro Service Fabric][build-step]

   * **Pro aplikace .NET Core:** Z **přidat krok sestavení** rozevíracího seznamu, vyberte **spustit prostředí**. Do pole příkazu, který se zobrazí adresář nejprve musí změnit tak, aby cesta kde je umístěn soubor build.sh. Jakmile se změnil adresář, build.sh skript můžete spustit a sestaví aplikaci.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Následující snímek obrazovky ukazuje příklad příkazy, které se používají k vytvoření [služba čítače](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) ukázku s názvem úlohy Jenkinse CounterServiceApplication.

      ![Akce sestavení v Jenkinsu pro Service Fabric][build-step-dotnet]

1. Konfigurace Jenkinse k nasazení aplikace do clusteru Service Fabric v akcích po sestavení, je třeba umístění certifikátu daný cluster ve vašem kontejneru Jenkinse. Vyberte jednu z následujících možností v závislosti na tom, jestli váš kontejner s Jenkinsem běží uvnitř nebo vně vašeho clusteru a poznamenejte si umístění certifikátu clusteru:

   * **Pro Jenkinse spuštěných ve vašem clusteru:** Cesta k certifikátu, můžete zobrazit tak přečtou hodnotu *Certificates_JenkinsOnSF_Code_MyCert_PEM* proměnné prostředí z v rámci kontejneru.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Pro Jenkins běží mimo cluster:** Použijte následující postup zkopírujte certifikát clusteru do kontejneru:
      1. Certifikát musí být ve formátu PEM. Pokud nemáte soubor PEM, můžete vytvořit jednu ze souboru certifikátu PFX. Pokud váš soubor PFX není chráněn heslem, spusťte následující příkaz z hostitele:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
         ``` 

      Pokud je soubor PFX chráněný heslem, obsahovat hesla `-passin` parametru. Příklad:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
         ``` 

      1. Chcete-li získat ID kontejneru pro váš kontejner s Jenkinsem, spusťte `docker ps` z hostitele.
      1. Zkopírujte soubor PEM, který do vašeho kontejneru pomocí následujícího příkazu Docker:
    
         ```sh
         docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
         ``` 

Budete téměř hotovi! Ponechte otevřené úlohy Jenkinse. Jediný zbývající úkolu je konfigurace po sestavení kroky k nasazení aplikace do clusteru Service Fabric:

* Pokud chcete nasadit pro vývojové nebo testovací prostředí, postupujte podle kroků v [nakonfigurovat nasazení pomocí koncového bodu správy clusteru](#configure-deployment-using-cluster-management-endpoint).
* Pokud chcete nasadit do produkčního prostředí, postupujte podle kroků v [nakonfigurovat nasazení pomocí přihlašovacích údajů Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurace nasazení pomocí koncového bodu správy clusteru
Pro vývojová a testovací prostředí můžete použít koncový bod správy clusteru k nasazení vaší aplikace. Konfigurace akce po sestavení ke koncovému bodu správy clusteru k nasazení aplikace vyžaduje minimální množství nastavení. Pokud nasazení provádíte do produkčního prostředí, přeskočte k části [nakonfigurovat nasazení pomocí přihlašovacích údajů Azure](#configure-deployment-using-azure-credentials) konfigurace instanční objekt Azure Active Directory k použití během nasazování.    

1. V úloze Jenkinse, klikněte na tlačítko **akcích po sestavení** kartu. 
1. V rozevírací nabídce **Post-Build Actions** (Akce po sestavení) vyberte **Deploy Service Fabric Project** (Nasazení projektu Service Fabric). 
1. V části **konfigurace clusteru Service Fabric**, vyberte **zadejte koncový bod Service Fabric správu** přepínač.
1. Pro **hostitele pro správu**, zadejte koncový bod připojení pro vašeho clusteru, například `{your-cluster}.eastus.cloudapp.azure.com`.
1. Pro **klientský klíč** a **klientský certifikát**, zadejte umístění souboru PEM ve vašem kontejneru Jenkinse; například `/var/jenkins_home/clustercert.pem`. (Jste zkopírovali posledním kroku umístění certifikátu [vytvoření a konfigurace úlohy Jenkinse](#create-and-configure-a-jenkins-job).)
1. V části **konfigurace aplikace**, nakonfigurovat **název_aplikace**, **typ aplikace**a (relativní) **cestu k manifestu aplikace** pole.

   ![Akce po sestavení Jenkinse prostředky infrastruktury služby konfigurace koncového bodu správy](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Klikněte na tlačítko **ověřit konfiguraci**. Na úspěšné ověřování, klikněte na tlačítko **Uložit**. Úlohy kanálu Jenkins je teď plně nakonfigurované. Přeskočte k části [další kroky](#next-steps) k otestování nasazení.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurace nasazení pomocí přihlašovacích údajů pro Azure
Pro produkční prostředí konfiguraci Azure přihlašovacích údajů pro nasazení aplikace je důrazně doporučujeme. Tato část ukazuje, jak nakonfigurovat instanční objekt Azure Active Directory používat k nasazení aplikace v akci po sestavení. Instanční objekty můžete přiřadit role ve vašem adresáři a omezit oprávnění úlohy Jenkinse. 

Pro vývojová a testovací prostředí můžete nakonfigurovat přihlašovací údaje Azure nebo koncový bod správy clusteru k nasazení vaší aplikace. Podrobnosti o tom, jak nakonfigurovat koncový bod správy clusteru najdete v tématu [nakonfigurovat nasazení pomocí koncového bodu správy clusteru](#configure-deployment-using-cluster-management-endpoint).   

1. K vytvoření instančního objektu služby Azure Active Directory a přiřadit jí oprávnění ve vašem předplatném Azure, postupujte podle kroků v [použití portálu k vytvoření aplikace Azure Active Directory a instančního objektu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Věnujte pozornost následujícím:

   * Během provádění kroků v tématu, nezapomeňte zkopírovat a uložit následující hodnoty: *ID aplikace*, *klíč aplikace*, *ID adresáře (ID Tenanta)*, a *ID předplatného*. Budete potřebovat ke konfiguraci přihlašovacích údajů Azure v Jenkinsu.
   * Pokud nemáte k dispozici [požadovaná oprávnění](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) ve vašem adresáři, budete muset požádat správce, aby vám udělil oprávnění nebo vytvořit instanční objekt služby pro vás nebo budete muset nakonfigurovat koncový bod správy pro vašeho v clusteru **akcích po sestavení** pro vaše úlohy v Jenkinsu.
   * V [vytvoření aplikace Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) oddílu, můžete zadat libovolný ve správném formátu adresy URL pro **přihlašovací adresa URL**.
   * V [přiřaďte aplikaci roli](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) oddílu, můžete přiřadit aplikace *čtečky* role pro skupinu prostředků pro váš cluster.

1. Zpět v rámci úlohy Jenkinse, klikněte na tlačítko **akcích po sestavení** kartu.
1. V rozevírací nabídce **Post-Build Actions** (Akce po sestavení) vyberte **Deploy Service Fabric Project** (Nasazení projektu Service Fabric). 
1. V části **konfigurace clusteru Service Fabric**, vyberte **vyberte Service Fabric Cluster** přepínač. Klikněte na tlačítko **přidat** vedle **přihlašovací údaje Azure**. Klikněte na tlačítko **Jenkins** výběr poskytovatele přihlašovacích údajů Jenkinse.
1. V poskytovateli pověření Jenkinse vyberte **instanční objekt služby Microsoft Azure** z **druh** rozevíracího seznamu.
1. Použijte hodnoty, který jste uložili při nastavování nahoru instančního objektu služby v kroku 1, chcete-li nastavit následující pole:

   * **ID klienta**: *ID aplikace*
   * **Tajný kód klienta**: *Klíč aplikace*
   * **ID tenanta**: *ID adresáře*
   * **ID předplatného**: *ID předplatného*
1. Zadejte popisný **ID** , můžete použít k výběru pověření v Jenkins a stručný **popis**. Pak klikněte na tlačítko **ověřte instanční objekt služby**. Pokud ověření úspěšné, klikněte na tlačítko **přidat**.

   ![Zadejte přihlašovací údaje Azure Jenkinse pro Service Fabric](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. Zpět v části **konfigurace clusteru Service Fabric**, ujistěte se, že je vybraná nový přihlašovací údaj **přihlašovací údaje Azure**. 
1. Z **skupiny prostředků** rozevíracího seznamu, vyberte skupinu prostředků, kterou chcete nasadit aplikaci do clusteru.
1. Z **Service Fabric** rozevíracího seznamu, vyberte, kterou chcete nasadit aplikaci do clusteru.
1. Pro **klientský klíč** a **klientský certifikát**, zadejte umístění souboru PEM do kontejner s Jenkinsem. Například `/var/jenkins_home/clustercert.pem`. 
1. V části **konfigurace aplikace**, nakonfigurovat **název_aplikace**, **typ aplikace**a (relativní) **cestu k manifestu aplikace** pole.
    ![Akce po sestavení Jenkinse prostředky infrastruktury služby nakonfigurovat přihlašovací údaje Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Klikněte na tlačítko **ověřit konfiguraci**. Na úspěšné ověřování, klikněte na tlačítko **Uložit**. Úlohy kanálu Jenkins je teď plně nakonfigurované. Pokračovat k [další kroky](#next-steps) k otestování nasazení.

## <a name="troubleshooting-the-jenkins-plugin"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další postup
GitHub a Jenkins jsou teď nakonfigurované. Zvažte učinění nějaké ukázkové změny v `reliable-services-actor-sample/Actors/ActorCounter` projektu ve vašem forku úložiště, https://github.com/Azure-Samples/service-fabric-java-getting-started. Uložit změny do vzdáleného `master` větev (nebo jakékoli větve, kterou jste nakonfigurovali pro práci s). Tím se aktivuje nakonfigurovaná úloha Jenkinse `MyJob`. Úloha načte změny z Githubu, je k sestavení a nasadí aplikaci do clusteru, který jste zadali v akcích po sestavení.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

