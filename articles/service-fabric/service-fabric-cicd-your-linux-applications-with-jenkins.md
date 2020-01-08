---
title: Průběžné sestavování pro aplikace pro Linux pomocí Jenkinse
description: Průběžné sestavování a integrace pro vaši Service Fabricovou aplikaci pro Linux pomocí Jenkinse
author: sayantancs
ms.topic: conceptual
ms.date: 07/31/2018
ms.openlocfilehash: 175338fef600f6e726fd02eee6b0f416181bd9dd
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2020
ms.locfileid: "75610212"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Použití Jenkinse k sestavování a nasazování aplikací pro Linux
Jenkins je oblíbený nástroj pro průběžnou integraci a nasazování aplikací. Tady je postup, kterým můžete sestavit a nasadit aplikaci Azure Service Fabric s využitím Jenkinse.

## <a name="topic-overview"></a>Přehled tématu
Tento článek se věnuje několika možným způsobům nastavení prostředí Jenkinse a také různých způsobů, jak nasadit aplikaci do clusteru Service Fabric po sestavení. Postupujte podle těchto obecných kroků, abyste úspěšně nastavili Jenkinse, načetli změny z GitHubu, sestavíte aplikaci a nasadili ji do svého clusteru:

1. Ujistěte se, že jste nainstalovali [požadavky](#prerequisites).
1. Pak postupujte podle kroků v jedné z těchto částí a nastavte Jenkinse:
   * [Nastavte Jenkinse v clusteru Service Fabric](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Nastavte Jenkinse mimo cluster Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster)nebo
   * [Nainstalujte modul plug-in Service Fabric v existujícím prostředí Jenkinse](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Až nastavíte Jenkinse, postupujte podle kroků v části [Vytvoření a konfigurace úlohy Jenkinse](#create-and-configure-a-jenkins-job) pro nastavení GitHubu tak, aby aktivoval Jenkinse při změnách v aplikaci a konfiguraci kanálu úloh Jenkinse prostřednictvím kroku sestavení, který načte změny z GitHubu a sestaví aplikaci. 
1. Nakonec nakonfigurujte krok po sestavení úlohy Jenkinse k nasazení aplikace do clusteru Service Fabric. Existují dva způsoby, jak nakonfigurovat Jenkinse pro nasazení aplikace do clusteru:    
   * Pro vývojová a testovací prostředí použijte [konfiguraci nasazení pomocí koncového bodu správy clusteru](#configure-deployment-using-cluster-management-endpoint). Toto je nejjednodušší metoda nasazení k nastavení.
   * V produkčních prostředích použijte [konfiguraci nasazení s použitím přihlašovacích údajů Azure](#configure-deployment-using-azure-credentials). Microsoft doporučuje tuto metodu pro produkční prostředí, protože s přihlašovacími údaji Azure můžete omezit přístup, který má Jenkinse úloha na vaše prostředky Azure. 

## <a name="prerequisites"></a>Požadavky

- Zajistěte, aby byl Git nainstalovaný místně. Příslušnou verzi Gitu můžete nainstalovat ze stránky pro [stažení Git](https://git-scm.com/downloads) založené na vašem operačním systému. Pokud s Git ještě nezačínáte, přečtěte si další informace v [dokumentaci k Gitu](https://git-scm.com/docs).
- V tomto článku se používá *ukázka Service Fabric Začínáme* na GitHubu: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) pro aplikaci pro sestavení a nasazení. Toto úložiště můžete rozvětvit, aby se sledovalo spolu s některými úpravami pokynů, použijte vlastní projekt GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Instalace modulu plug-in Service Fabric v existujícím prostředí Jenkinse
Pokud přidáváte modul plug-in Service Fabric do stávajícího prostředí Jenkinse, budete potřebovat následující:

- Rozhraní příkazového [řádku Service Fabric](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Nezapomeňte nainstalovat rozhraní příkazového řádku na úrovni systému, nikoli na úrovni uživatele, takže Jenkinse může spouštět příkazy rozhraní příkazového řádku (CLI). 
   >

- Chcete-li nasadit aplikace Java, nainstalujte obě [Gradle a Open JDK 8,0](service-fabric-get-started-linux.md#set-up-java-development). 
- Pokud chcete nasadit aplikace .NET Core 2,0, nainstalujte [sadu .NET core 2,0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Po instalaci požadovaných součástí pro vaše prostředí můžete na webu Jenkinse Marketplace vyhledat modul plug-in Azure Service Fabric a nainstalovat ho.

Po instalaci modulu plug-in přejděte k části [Vytvoření a konfigurace úlohy Jenkinse](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Nastavení Jenkinse uvnitř clusteru Service Fabric

Jenkinse můžete nastavit uvnitř clusteru Service Fabric nebo mimo něj. V následujících částech se dozvíte, jak je nastavit v clusteru a přitom pomocí účtu služby Azure Storage uložit stav instance kontejneru.

### <a name="prerequisites"></a>Požadavky
- Mít nainstalovaný cluster s Service Fabric Linux s nainstalovaným Docker. Clustery Service Fabric běžící v Azure již mají nainstalovaný Docker. Pokud cluster spouštíte místně (vývojové prostředí OneBox), ověřte, jestli je Docker nainstalovaný na vašem počítači pomocí příkazu `docker info`. Pokud není nainstalovaný, nainstalujte ho pomocí následujících příkazů:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Ujistěte se, že port 8081 je zadán jako vlastní koncový bod v clusteru. Pokud používáte místní cluster, ujistěte se, že je na hostitelském počítači otevřený port 8081 a že má veřejnou IP adresu.
   >

### <a name="steps"></a>Kroky
1. Naklonujte aplikaci pomocí následujících příkazů:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Zachovat stav kontejneru Jenkinse ve sdílené složce:
   1. Vytvořte účet úložiště Azure ve **stejné oblasti** jako cluster s názvem, například `sfjenkinsstorage1`.
   1. V účtu úložiště vytvořte **sdílenou složku** s názvem, například `sfjenkins`.
   1. Klikněte na **připojit** se ke sdílené složce a Všimněte si hodnot zobrazených v části **připojení ze systému Linux**. Tato hodnota by měla vypadat podobně jako na následujícím obrázku:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Pokud chcete sdílené složky CIFS připojit, je potřeba mít v uzlech clusteru nainstalovaný balíček CIFS-utils.      
   >

1. Aktualizujte zástupné hodnoty ve skriptu `setupentrypoint.sh` s podrobnostmi o Azure Storage z kroku 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Nahraďte `[REMOTE_FILE_SHARE_LOCATION]` hodnotou `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` z výstupu připojení v kroku 2 výše.
   * Nahraďte `[FILE_SHARE_CONNECT_OPTIONS_STRING]` hodnotou `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` z kroku 2 výše.

1. **Pouze zabezpečený cluster:** 
   
   Aby bylo možné nakonfigurovat nasazení aplikací na zabezpečeném clusteru z Jenkinse, musí být certifikát clusteru přístupný v rámci kontejneru Jenkinse. V souboru *souboru ApplicationManifest. XML* pod značkou **ContainerHostPolicies** přidejte odkaz na tento certifikát a aktualizujte hodnotu kryptografického otisku pomocí certifikátu clusteru.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Kromě toho přidejte následující řádky pod značku **souboru ApplicationManifest** (root) v souboru *souboru ApplicationManifest. XML* a aktualizujte hodnotu kryptografického otisku pomocí certifikátu clusteru.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

1. Připojte se ke clusteru a nainstalujte aplikaci kontejneru.

   **Zabezpečený cluster**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Předchozí příkaz získá certifikát ve formátu PEM. Pokud je váš certifikát ve formátu PFX, můžete ho převést pomocí následujícího příkazu. Pokud soubor PFX není chráněný heslem, zadejte parametr **Passin** jako `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
   ``` 
   
   **Nezabezpečený cluster**
   ```sh
   sfctl cluster select --endpoint http://PublicIPorFQDN:19080 # cluster connect command
   bash Scripts/install.sh
   ```

   Tím se do clusteru nainstaluje kontejner s Jenkinsem, který můžete monitorovat pomocí Service Fabric Exploreru.

   > [!NOTE]
   > Stažení obrázku Jenkinse do clusteru může trvat několik minut.
   >

1. V prohlížeči přejděte na `http://PublicIPorFQDN:8081`. Najdete tam cestu k počátečnímu heslu správce vyžadovanému k přihlášení. 
1. Podívejte se na Service Fabric Explorer pro určení uzlu, na kterém je kontejner Jenkinse spuštěný. K tomuto uzlu se přihlašuje Secure Shell (SSH).
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Získejte ID instance kontejneru pomocí příkazu `docker ps -a`.
1. Secure Shell (SSH) Přihlaste se ke kontejneru a vložte cestu, která se zobrazila na portálu Jenkinse. Pokud je například na portálu zobrazená cesta `PATH_TO_INITIAL_ADMIN_PASSWORD`, spusťte následující příkazy:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Na stránce Začínáme Jenkinse zvolte možnost vyberte moduly plug-in k instalaci, zaškrtněte políčko **žádné** a klikněte na nainstalovat.
1. Vytvořte uživatele nebo ho vyberte, chcete-li pokračovat jako správce.

Po nastavení Jenkinse můžete přeskočit k [Vytvoření a konfiguraci úlohy Jenkinse](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Nastavení Jenkinse mimo cluster Service Fabric

Jenkinse můžete nastavit uvnitř clusteru Service Fabric nebo mimo něj. Následující části vysvětlují, jak ho nastavit mimo cluster.

### <a name="prerequisites"></a>Požadavky
- Ujistěte se, že je na vašem počítači nainstalovaný Docker. Pomocí následujících příkazů můžete Docker nainstalovat z terminálu:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Když spustíte `docker info` v terminálu, výstup by měl ukázat, že služba Docker je spuštěná.

### <a name="steps"></a>Kroky
1. Stáhněte si image kontejneru s Jenkinsem pro Service Fabric: `docker pull rapatchi/jenkins:latest`. Tato image je součástí instalace modulu plug-in Jenkinse pro Service Fabric.
1. Spusťte image kontejneru: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Získejte ID instance image kontejneru. Pomocí příkazu `docker ps –a` můžete vypsat všechny kontejnery Dockeru.
1. Přihlaste se na portál Jenkinse pomocí následujících kroků:

   1. Přihlaste se k prostředí Jenkinse z hostitele. Použijte první čtyři číslice ID kontejneru. Pokud je například ID kontejneru `2d24a73b5964`, použijte `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. V prostředí Jenkinse Získejte heslo správce pro vaši instanci kontejneru:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Pokud se chcete přihlásit k řídicímu panelu Jenkinse, otevřete ve webovém prohlížeči následující adresu URL: `http://<HOST-IP>:8080`. K odemknutí Jenkinse použijte heslo z předchozího kroku.
   1. (Volitelné.) Po prvním přihlášení můžete vytvořit vlastní uživatelský účet a použít ho pro následující kroky, nebo můžete dál používat účet správce. Pokud vytvoříte uživatele, musíte s tímto uživatelem pokračovat.
1. Nastavte GitHub pro práci s Jenkinse pomocí kroků v části [generování nového klíče SSH a jeho přidání k agentovi SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Pomocí pokynů z GitHubu vygenerujte klíč SSH a přidejte ho do účtu GitHubu, který je hostitelem úložiště.
   * Příkazy popsané u předchozího odkazu spusťte v prostředí Jenkins Dockeru (ne na hostiteli).
   * Pokud se chcete k prostředí Jenkinse přihlásit z hostitele, použijte následující příkaz:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Ujistěte se, že cluster nebo počítač, ve kterém je hostovaná image kontejneru Jenkinse, má veřejnou IP adresu. To umožňuje, aby instance Jenkinse dostávala oznámení z GitHubu.

Až nastavíte Jenkinse, pokračujte k další části a [vytvořte a nakonfigurujte úlohu Jenkinse](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Vytvoření a konfigurace úlohy Jenkinse

Kroky v této části ukazují, jak nakonfigurovat úlohu Jenkinse, která reaguje na změny v úložišti GitHub, načítají změny a sestavuje je. Na konci této části budete přesměrováni na poslední kroky ke konfiguraci úlohy pro nasazení aplikace na základě toho, jestli nasazujete do prostředí pro vývoj/testování nebo do produkčního prostředí. 

1. Na řídicím panelu Jenkinse klikněte na **Nová položka**.
1. Zadejte název položky (třeba **MyJob**). Vyberte **free-style project** (volný styl projektu) a klikněte na **OK**.
1. Otevře se stránka Konfigurace úlohy. (Pokud se chcete dostat na konfiguraci z řídicího panelu Jenkinse, klikněte na úlohu a pak klikněte na **Konfigurovat**).

1. Na kartě **Obecné** zaškrtněte políčko pro **projekt GitHub**a zadejte adresu URL vašeho projektu GitHubu. Tato adresa je hostitelem aplikace Service Fabric v Javě, kterou chcete integrovat s postupy průběžného nasazování a integrace (CI/CD) Jenkinse (např. `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Na kartě **Správa zdrojového kódu** vyberte **Git**. Zadejte adresu URL úložiště, které je hostitelem aplikace Service Fabric v Javě, kterou chcete integrovat s postupy CI/CD Jenkinse (např. `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Můžete také určit, která větev se má sestavit (například `/master`).
1. Konfigurace úložiště *GitHub* pro komunikaci s Jenkinse:

   a. Na stránce úložiště GitHubu přejdete na **nastavení** > **integrace a služby**.

   b. Vyberte **Add Service** (Přidat službu), zadejte **Jenkins** a vyberte **Jenkins-Github plugin** (Modul plug-in Jenkins-Github).

   c. Zadejte adresu URL webhooku Jenkinse (ve výchozím nastavení by měla být `http://<PublicIPorFQDN>:8081/github-webhook/`). Klikněte na **add/update service** (Přidat/aktualizovat službu).

   d. Do instance Jenkinse se odešle testovací událost. V GitHubu by se měla vedle webhooku zobrazit zelená značka zaškrtnutí a váš projekt se sestaví.

1. Na kartě **aktivační události sestavení** v Jenkinse vyberte, kterou možnost sestavení chcete. V tomto příkladu chcete spustit sestavení pokaždé, když dojde k vložení do úložiště, takže vyberte **Trigger vidlice pro cyklické dotazování gitscm Polling (** . (Dřív se tato možnost nazývala **Build when a change is pushed to GitHub** (Sestavit při vložení změny metodou Push do GitHubu).
1. Na kartě **sestavení** proveďte jednu z následujících akcí v závislosti na tom, zda vytváříte aplikaci Java nebo aplikaci .NET Core:

   * **Pro aplikace Java:** V rozevíracím seznamu **Přidat krok sestavení** vyberte možnost **vyvolat skript Gradle**. Klikněte na tlačítko **Advanced**. V nabídce Upřesnit zadejte cestu ke **skriptu kořenového sestavení** pro vaši aplikaci. Sestavení si ze zadané cesty vezme soubor build.gradle a bude pracovat odpovídajícím způsobem. Pro [aplikaci ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)je to: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Akce sestavení v Jenkinsu pro Service Fabric][build-step]

   * **Pro aplikace .NET Core:** V rozevíracím seznamu **Přidat krok sestavení** vyberte možnost **spustit prostředí**. V zobrazeném příkazovém řádku musí být adresář nejprve změněn na cestu, kde je umístěn soubor build.sh. Po změně adresáře se dá skript build.sh spustit a aplikace se sestaví.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Následující snímek obrazovky ukazuje příklad příkazů, které se používají k vytvoření ukázky [služby čítače](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) s názvem Jenkinse úlohy CounterServiceApplication.

      ![Akce sestavení v Jenkinsu pro Service Fabric][build-step-dotnet]

1. Pokud chcete nakonfigurovat Jenkinse pro nasazení aplikace do clusteru Service Fabric v akcích po sestavení, budete potřebovat umístění certifikátu tohoto clusteru v kontejneru Jenkinse. Vyberte jednu z následujících možností v závislosti na tom, jestli je váš kontejner Jenkinse spuštěný uvnitř nebo vně clusteru, a poznamenejte si umístění certifikátu clusteru:

   * **Pro Jenkinse běžící v rámci clusteru:** Cestu k certifikátu lze najít pomocí ozvěny hodnoty proměnné prostředí *Certificates_JenkinsOnSF_Code_MyCert_PEM* v rámci kontejneru.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Pro Jenkinse spuštěné mimo váš cluster:** Pomocí těchto kroků zkopírujte certifikát clusteru do svého kontejneru:
     1. Certifikát musí být ve formátu PEM. Pokud nemáte soubor PEM, můžete ho vytvořit ze souboru PFX certifikátu. Pokud soubor PFX není chráněný heslem, spusťte z hostitele následující příkaz:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Pokud je soubor PFX chráněný heslem, zahrňte heslo do parametru `-passin`. Příklad:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Pokud chcete získat ID kontejneru pro kontejner Jenkinse, spusťte `docker ps` z hostitele.
     1. Zkopírujte soubor PEM do svého kontejneru pomocí následujícího příkazu Docker:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Už skoro jste hotovi! Nechejte úlohu Jenkinse otevřenou. Jediným zbývajícím úkolem je nakonfigurovat kroky po sestavení pro nasazení aplikace do clusteru Service Fabric:

* Pokud chcete nasadit prostředí pro vývoj nebo testování, postupujte podle kroků v části [Konfigurace nasazení pomocí koncového bodu správy clusteru](#configure-deployment-using-cluster-management-endpoint).
* Při nasazení do produkčního prostředí postupujte podle kroků v části [Konfigurace nasazení pomocí přihlašovacích údajů Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurace nasazení pomocí koncového bodu správy clusterů
Pro vývojová a testovací prostředí můžete aplikaci nasadit pomocí koncového bodu správy clusteru. Konfigurace akce po sestavení pomocí koncového bodu správy clusteru pro nasazení aplikace vyžaduje nejmenší množství nastavení. Pokud nasazujete do provozního prostředí, přeskočte před [konfigurací nasazení pomocí přihlašovacích údajů Azure](#configure-deployment-using-azure-credentials) ke konfiguraci Azure Active Directory instančního objektu, který se má použít při nasazení.    

1. V úloze Jenkinse klikněte na kartu **akce po sestavení** . 
1. V rozevírací nabídce **Post-Build Actions** (Akce po sestavení) vyberte **Deploy Service Fabric Project** (Nasazení projektu Service Fabric). 
1. V části **Service Fabric konfigurace clusteru**zaškrtněte políčko **vyplnit přepínač koncového bodu správy Service Fabric** .
1. V části **hostitel pro správu**Zadejte koncový bod připojení pro váš cluster. například `{your-cluster}.eastus.cloudapp.azure.com`.
1. V případě **klientského klíče** a **certifikátu klienta**zadejte umístění souboru PEM do kontejneru Jenkinse; například `/var/jenkins_home/clustercert.pem`. (Zkopírovali jste umístění certifikátu v posledním kroku [Vytvoření a konfigurace úlohy Jenkinse](#create-and-configure-a-jenkins-job).)
1. V části **Konfigurace aplikace**nakonfigurujte **název aplikace**, **Typ aplikace**a (relativní) **cestu k polím manifestu aplikace** .

   ![Service Fabric Jenkinse akce po sestavení konfigurace koncového bodu správy](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Klikněte na **ověřit konfiguraci**. Po úspěšném ověření klikněte na **Uložit**. Váš kanál úlohy Jenkinse je teď plně nakonfigurovaný. Přeskočte dopředu k [dalším krokům](#next-steps) otestujete nasazení.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurace nasazení pomocí přihlašovacích údajů Azure
Pro produkční prostředí se doporučuje nakonfigurovat přihlašovací údaje Azure pro nasazení vaší aplikace. V této části se dozvíte, jak nakonfigurovat Azure Active Directory instančního objektu pro použití k nasazení aplikace v akci po sestavení. K rolím v adresáři můžete přiřadit instanční objekty, abyste omezili oprávnění úlohy Jenkinse. 

Pro vývojová a testovací prostředí můžete pro nasazení aplikace nakonfigurovat buď přihlašovací údaje Azure, nebo koncový bod správy clusteru. Podrobnosti o tom, jak nakonfigurovat koncový bod pro správu clusteru, najdete v tématu [Konfigurace nasazení pomocí koncového bodu správy clusteru](#configure-deployment-using-cluster-management-endpoint).   

1. Pokud chcete vytvořit objekt služby Azure Active Directory a přiřadit mu oprávnění k vašemu předplatnému Azure, postupujte podle kroků v části [použití portálu k vytvoření Azure Active Directory aplikace a instančního objektu](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal). Věnujte pozornost následujícímu:

   * Při provádění kroků v tématu Nezapomeňte zkopírovat a uložit následující hodnoty: *ID aplikace*, *klíč aplikace*, *ID adresáře (ID tenanta)* a *ID předplatného*. Budete je potřebovat ke konfiguraci přihlašovacích údajů Azure v Jenkinse.
   * Pokud ve svém adresáři nemáte [požadovaná oprávnění](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) , musíte požádat správce, aby vám udělil oprávnění nebo vytvořil instanční objekt, nebo musíte nakonfigurovat koncový bod správy pro váš cluster v **akcích po sestavení** pro vaši úlohu v Jenkinse.
   * V části [Vytvoření aplikace Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) můžete pro **přihlašovací adresu URL**zadat libovolnou dobře formátovanou adresu URL.
   * V části [přiřazení aplikace k roli](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) můžete aplikaci přiřadit k roli *Čtenář* ve skupině prostředků pro váš cluster.

1. Zpátky v úloze Jenkinse klikněte na kartu **akce po sestavení** .
1. V rozevírací nabídce **Post-Build Actions** (Akce po sestavení) vyberte **Deploy Service Fabric Project** (Nasazení projektu Service Fabric). 
1. V části **Service Fabric konfigurace clusteru**vyberte přepínač **Cluster Service Fabric** . Klikněte na **Přidat** vedle **přihlašovacích údajů Azure**. Klikněte na **Jenkinse** a vyberte poskytovatele přihlašovacích údajů Jenkinse.
1. Ve zprostředkovateli pověření Jenkinse vyberte v rozevíracím seznamu **druh** **Microsoft Azure instanční objekt** .
1. Použijte hodnoty, které jste uložili při nastavování instančního objektu v kroku 1 pro nastavení následujících polí:

   * **ID klienta**: *ID aplikace*
   * **Tajný kód klienta**: *klíč aplikace*
   * **ID tenanta**: *ID adresáře*
   * **ID předplatného**: *ID předplatného*
1. Zadejte popisné **ID** , které použijete k výběru přihlašovacích údajů v Jenkinse a krátkého **popisu**. Pak klikněte na **ověřit instanční objekt**. Pokud je ověření úspěšné, klikněte na tlačítko **Přidat**.

   ![Service Fabric Jenkinse zadat přihlašovací údaje Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. V části **Service Fabric konfigurace clusteru**se ujistěte, že je vybrané nové přihlašovací údaje pro **přihlašovací údaje Azure**. 
1. V rozevíracím seznamu **Skupina prostředků** vyberte skupinu prostředků clusteru, do kterého chcete aplikaci nasadit.
1. V rozevíracím seznamu **Service Fabric** vyberte cluster, do kterého chcete aplikaci nasadit.
1. V případě **klientského klíče** a **certifikátu klienta**zadejte umístění souboru PEM do kontejneru Jenkinse. Například `/var/jenkins_home/clustercert.pem`. 
1. V části **Konfigurace aplikace**nakonfigurujte **název aplikace**, **Typ aplikace**a (relativní) **cestu k polím manifestu aplikace** .
    ![Service Fabric Jenkinse akce po sestavení nakonfigurujte přihlašovací údaje Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Klikněte na **ověřit konfiguraci**. Po úspěšném ověření klikněte na **Uložit**. Váš kanál úlohy Jenkinse je teď plně nakonfigurovaný. Pokračujte k [dalším krokům](#next-steps) pro otestování nasazení.

## <a name="troubleshooting-the-jenkins-plugin"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další kroky
GitHub a Jenkins jsou teď nakonfigurované. Zvažte provedení některých ukázkových změn v `reliable-services-actor-sample/Actors/ActorCounter` projektu ve větvi úložiště, https://github.com/Azure-Samples/service-fabric-java-getting-started. Nahrajte změny do vzdálené větve `master` (nebo jakékoli větve, které jste nakonfigurovali pro práci s). Tím se aktivuje nakonfigurovaná úloha Jenkinse `MyJob`. Načte změny z GitHubu, vytvoří je a nasadí aplikaci do clusteru, který jste zadali v akcích po sestavení.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

