---
title: Průběžné vytváření linuxových aplikací pomocí Jenkinse
description: Průběžné vytváření a integrace pro vaši aplikaci Service Fabric Linux pomocí Jenkinse
keywords: jenkins, azurové, devops, cicd, linux
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 96e8c3721b5849b874878ea6a846271fb6760ffd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77675236"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Použití Jenkinse k vytváření a nasazování linuxových aplikací

Tento kurz popisuje několik možných způsobů nastavení prostředí Jenkins, stejně jako různé způsoby nasazení aplikace do clusteru Service Fabric po jeho vytvoření. Podle těchto obecných kroků můžete úspěšně nakonfigurovat Jenkinse, načítat změny z GitHubu, vytvářet aplikaci a nasazovat ji do clusteru:

1. Ujistěte se, že nainstalujete [požadavky](#prerequisites).
1. Potom podle pokynů v jedné z těchto částí nastavte Jenkinse:
   * [Nastavení Jenkinse uvnitř clusteru Service Fabric](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Nastavte Jenkins mimo cluster Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster)nebo
   * [Nainstalujte modul plug-in Service Fabric do existujícího prostředí Jenkinse](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
1. Po nastavení Jenkinse postupujte podle kroků v [části Vytvoření a konfigurace úlohy Jenkinse,](#create-and-configure-a-jenkins-job) která nastaví GitHub tak, aby aktivoval Jenkins při provádění změn ve vaší aplikaci, a nakonfigurujte kanál úloh Jenkinse pomocí kroku sestavení, který provede vyžádat změny z GitHubu a sestaví vaši aplikaci. 
1. Nakonec nakonfigurujte krok úlohy Jenkinse po sestavení k nasazení aplikace do clusteru Service Fabric. Jenkins může nakonfigurovat tak, aby vaši aplikaci nasazoval do clusteru, dvěma způsoby:    
   * Pro vývojová a testovací prostředí použijte [možnost Konfigurovat nasazení pomocí koncového bodu správy clusteru](#configure-deployment-using-cluster-management-endpoint). Toto je nejjednodušší způsob nasazení nastavit.
   * Pro produkční prostředí použijte [možnost Konfigurovat nasazení pomocí přihlašovacích údajů Azure](#configure-deployment-using-azure-credentials). Microsoft doporučuje tuto metodu pro produkční prostředí, protože s přihlašovacími údaji Azure můžete omezit přístup, který má úloha Jenkinse k vašim prostředkům Azure. 

## <a name="prerequisites"></a>Požadavky

- Ujistěte se, že je Git nainstalovaný místně. Příslušnou verzi Gitu můžete nainstalovat [na stránce stahování Git](https://git-scm.com/downloads) na základě vašeho operačního systému. Pokud s Gitem tečunete, přečtěte si o něm další informace v dokumentaci k [Gitu](https://git-scm.com/docs).
- Tento článek používá *ukázku začínáme služby* na GitHubu: [https://github.com/Azure-Samples/service-fabric-java-getting-started](https://github.com/Azure-Samples/service-fabric-java-getting-started) pro aplikaci k sestavení a nasazení. Můžete vyklopit toto úložiště sledovat spolu, nebo s některými úpravami pokynů, použijte vlastní projekt GitHub.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Instalace modulu plug-in Service Fabric v existujícím prostředí Jenkinse

Pokud přidáváte plugin Service Fabric do existujícího prostředí Jenkinse, musíte provést následující kroky:

- [Service Fabric CLI (sfctl)](../service-fabric/service-fabric-cli.md). Nainstalujte rozhraní příkazového příkazu na úrovni systému, nikoli na úrovni uživatele, takže Jenkins může spouštět příkazy příkazového příkazu. 
- Chcete-li nasadit java aplikace, nainstalujte [gradle i otevřenou JDK 8.0](../service-fabric/service-fabric-get-started-linux.md#set-up-java-development). 
- Chcete-li nasadit aplikace .NET Core 2.0, nainstalujte sadu [.NET Core 2.0 SDK](../service-fabric/service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Po instalaci předpokladů potřebných pro vaše prostředí můžete vyhledat modul plug-in Azure Service Fabric na webu Jenkins marketplace a nainstalovat ho.

Po instalaci pluginu přeskočte na [vytvoření a konfiguraci úlohy Jenkinse](#create-and-configure-a-jenkins-job).

## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Nastavení Jenkinse uvnitř clusteru Service Fabric

Jenkinse můžete nastavit uvnitř clusteru Service Fabric nebo mimo něj. Následující části ukazují, jak ji nastavit uvnitř clusteru při použití účtu úložiště Azure k uložení stavu instance kontejneru.

1. Ujistěte se, že máte nainstalován cluster Service Fabric Linux s Dockerem. Clustery Service Fabric spuštěné v Azure už mají nainstalovaný Docker. Pokud cluster spouštěte místně (dev prostředí OneBox), zkontrolujte, jestli `docker info` je v počítači s příkazem nainstalován Docker. Pokud není nainstalována, nainstalujte ji pomocí následujících příkazů:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Ujistěte se, že port 8081 je určen jako vlastní koncový bod v clusteru. Pokud používáte místní cluster, ujistěte se, že port 8081 je otevřený v hostitelském počítači a že má veřejnou adresu IP.
   >

1. Klonovat aplikaci pomocí následujících příkazů:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

1. Zachovávají stav kontejneru Jenkins ve sdílené složce:
   1. Vytvořte účet úložiště Azure **ve stejné oblasti** jako `sfjenkinsstorage1`cluster s názvem, jako je například .
   1. Vytvořte **sdílenou složku** pod účtem `sfjenkins`úložiště s názvem, jako je například .
   1. Klikněte na **Připojit** pro soubor-share a poznamenejte si hodnoty, které se zobrazí v části **Připojení z Linuxu**, hodnota by měla vypadat podobně jako u níže uvedeného:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Chcete-li připojit sdílené složky CIFs, musíte mít v uzlech clusteru nainstalovaný balíček CIFS-utils.      
   >

1. Aktualizujte zástupné hodnoty `setupentrypoint.sh` ve skriptu podrobnostmi o úložišti azure z kroku 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Nahraďte `[REMOTE_FILE_SHARE_LOCATION]` `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` hodnotou z výstupu připojení v kroku 2 výše.
   * Nahraďte `[FILE_SHARE_CONNECT_OPTIONS_STRING]` `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` hodnotou z výše uvedeného kroku 2.

1. **Pouze zabezpečený cluster:** 
   
   Chcete-li nakonfigurovat nasazení aplikací v zabezpečeném clusteru od jenkinse, musí být certifikát clusteru přístupný v kontejneru Jenkins. V souboru *ApplicationManifest.xml* přidejte pod značkou **ContainerHostPolicies** tento odkaz na certifikát a aktualizujte hodnotu kryptotisku hodnotou certifikátu clusteru.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Dále přidejte následující řádky pod značku **ApplicationManifest** (root) do souboru *ApplicationManifest.xml* a aktualizujte hodnotu kryptotisku hodnotou certifikátu clusteru.

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
   Předchozí příkaz přebírá certifikát ve formátu PEM. Pokud je váš certifikát ve formátu PFX, můžete jej převést pomocí následujícího příkazu. Pokud váš soubor PFX není chráněn heslem, `-passin pass:`zadejte parametr **passin** jako .
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
   > Stažení obrázku Jenkinse v clusteru může trvat několik minut.
   >

1. V prohlížeči přejděte na `http://PublicIPorFQDN:8081`. Najdete tam cestu k počátečnímu heslu správce vyžadovanému k přihlášení. 
1. Podívejte se na Service Fabric Explorer určit, na kterém uzlu kontejner Jenkins je spuštěn. Zabezpečené prostředí (SSH) přihlášení k tomuto uzlu.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
1. Získejte ID instance kontejneru pomocí příkazu `docker ps -a`.
1. Zabezpečené prostředí (SSH) se přihlaste do kontejneru a vložte cestu, která se vám zobrazila na portálu Jenkins. Pokud například na portálu ukazuje `PATH_TO_INITIAL_ADMIN_PASSWORD`cestu , spusťte následující příkazy:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
1. Na stránce Začínáme s Jenkinsem zvolte možnost Vybrat pluginy k instalaci, zaškrtněte políčko **Žádný** a klikněte na Instalovat.
1. Vytvořte uživatele nebo vyberte, zda chcete pokračovat jako správce.

Po nastavení Jenkinse přeskočte na [Vytvoření a konfigurace jenkinsovy úlohy](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Nastavení Jenkinse mimo cluster Service Fabric

Jenkinse můžete nastavit uvnitř clusteru Service Fabric nebo mimo něj. Následující části vysvětlují, jak ho nastavit mimo cluster.

1. Ujistěte se, že Docker je `docker info` nainstalován na vašem počítači spuštěním v terminálu. Výstup označuje, pokud je spuštěna služba Docker.

1. Pokud není Docker nainstalovaný, spusťte následující příkazy:

    ```sh
    sudo apt-get install wget
    wget -qO- https://get.docker.io/ | sh
    ```

1. Stáhněte si image kontejneru s Jenkinsem pro Service Fabric: `docker pull rapatchi/jenkins:latest`. Tato image je součástí instalace modulu plug-in Jenkinse pro Service Fabric.
1. Spusťte image kontejneru: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
1. Získejte ID instance image kontejneru. Pomocí příkazu `docker ps –a` můžete vypsat všechny kontejnery Dockeru.
1. Přihlaste se k portálu Jenkins pomocí následujících kroků:

   1. Přihlaste se ke shellu Jenkins od svého hostitele. Použijte první čtyři číslice ID kontejneru. Pokud je `2d24a73b5964`například ID kontejneru , použijte `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   1. Z prostředí Jenkins získat heslo správce pro instanci kontejneru:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   1. Pokud se chcete přihlásit k řídicímu panelu `http://<HOST-IP>:8080`Jenkinse, otevřete ve webovém prohlížeči následující adresu URL: . Použijte heslo z předchozího kroku k odemknutí Jenkinse.
   1. (Nepovinné.) Po prvním přihlášení si můžete vytvořit vlastní uživatelský účet a použít jej pro následující kroky, nebo můžete účet správce používat i nadále. Pokud vytvoříte uživatele, budete muset pokračovat s tímto uživatelem.
1. Nastavte GitHub pro práci s Jenkinsem pomocí kroků v [části Generování nového klíče SSH a jeho přidání do agenta SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Pomocí pokynů z GitHubu vygenerujte klíč SSH a přidejte ho do účtu GitHubu, který je hostitelem úložiště.
   * Příkazy popsané u předchozího odkazu spusťte v prostředí Jenkins Dockeru (ne na hostiteli).
   * Pokud se chcete k prostředí Jenkinse přihlásit z hostitele, použijte následující příkaz:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Ujistěte se, že cluster nebo počítač, kde je umístěna image kontejneru Jenkins má veřejnou adresu IP. To umožňuje, aby instance Jenkinse dostávala oznámení z GitHubu.

Po nastavení Jenkinse pokračujte další částí [Vytvoření a konfigurace jenkinsovy úlohy](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Vytvoření a konfigurace úlohy Jenkinse

Kroky v této části ukazují, jak nakonfigurovat úlohu Jenkinse tak, aby reagovala na změny v úložišti GitHub, načítaly změny a vytvářely je. Na konci této části budete přesměrováni na poslední kroky konfigurace úlohy pro nasazení aplikace na základě toho, zda nasazujete do vývojového/testovacího prostředí nebo do produkčního prostředí. 

1. Na řídicím panelu Jenkins klikněte na **Nová položka**.
1. Zadejte název položky (třeba **MyJob**). Vyberte **free-style project** (volný styl projektu) a klikněte na **OK**.
1. Otevře se stránka Konfigurace úlohy. (Chcete-li přejít ke konfiguraci z řídicího panelu Jenkinse, klikněte na úlohu a potom klikněte na **konfigurovat).**

1. Na kartě **Obecné** zaškrtněte políčko pro **projekt GitHub**a zadejte adresu URL projektu GitHub. Tato adresa je hostitelem aplikace Service Fabric v Javě, kterou chcete integrovat s postupy průběžného nasazování a integrace (CI/CD) Jenkinse (např. `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

1. Na kartě **Správa zdrojového kódu** vyberte **Git**. Zadejte adresu URL úložiště, které je hostitelem aplikace Service Fabric v Javě, kterou chcete integrovat s postupy CI/CD Jenkinse (např. `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Můžete také určit, kterou větev `/master`chcete sestavit (například ).
1. Nakonfigurujte si úložiště *GitHub* tak, aby mluvilo s Jenkinsem:

   a. Na stránce úložiště GitHub přejděte na**Integrace a služby** **nastavení** > .

   b. Vyberte **Add Service** (Přidat službu), zadejte **Jenkins** a vyberte **Jenkins-Github plugin** (Modul plug-in Jenkins-Github).

   c. Zadejte adresu URL webhooku Jenkinse (ve výchozím nastavení by měla být `http://<PublicIPorFQDN>:8081/github-webhook/`). Klikněte na **add/update service** (Přidat/aktualizovat službu).

   d. Do instance Jenkinse se odešle testovací událost. V GitHubu by se měla vedle webhooku zobrazit zelená značka zaškrtnutí a váš projekt se sestaví.

1. Na kartě **Aktivace sestavení** v Jenkinsi vyberte, kterou možnost sestavení chcete. V tomto příkladu chcete spustit sestavení vždy, když dojde k nabízení do úložiště, takže vyberte **aktivační událost zavěšení GitHub pro dotazování GITSCM**. (Dřív se tato možnost nazývala **Build when a change is pushed to GitHub** (Sestavit při vložení změny metodou Push do GitHubu).
1. Na kartě **Sestavení** proveďte jednu z následujících akcí v závislosti na tom, zda vytváříte aplikaci Java nebo aplikaci .NET Core:

   * **Pro java aplikace:** V rozevíracím souboru **Přidat krok sestavení** vyberte Vyvolat skript **gradle**. Klikněte na tlačítko **Upřesnit**. V rozšířené nabídce zadejte cestu ke **skriptu vytvoření root** pro vaši aplikaci. Sestavení si ze zadané cesty vezme soubor build.gradle a bude pracovat odpovídajícím způsobem. Pro [aplikaci ActorCounter](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter)je `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`toto: .

     ![Akce sestavení v Jenkinsu pro Service Fabric](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png)

   * **Pro základní aplikace .NET:** V rozevíracím souboru **Přidat krok sestavení** vyberte Spustit **prostředí**. V příkazovém poli, které se zobrazí, je třeba nejprve změnit adresář na cestu, kde je umístěn build.sh soubor. Po změně adresáře lze spustit skript build.sh a vytvoří aplikaci.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Následující snímek obrazovky ukazuje příklad příkazů, které se používají k vytvoření [ukázky služby čítače](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) s názvem úlohy Jenkins CounterServiceApplication.

      ![Akce sestavení v Jenkinsu pro Service Fabric](./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png)

1. Chcete-li nakonfigurovat Jenkinse k nasazení aplikace do clusteru Service Fabric v akcích po sestavení, potřebujete umístění certifikátu tohoto clusteru v kontejneru Jenkinse. V závislosti na tom, jestli je kontejner Jenkins spuštěn uvnitř nebo vně clusteru, zvolte jednu z následujících možností a poznamenejte si umístění certifikátu clusteru:

   * **Pro Jenkinse spuštěné uvnitř clusteru:** Cestu k certifikátu lze nalézt ozvěnou hodnoty proměnné *prostředí Certificates_JenkinsOnSF_Code_MyCert_PEM* z kontejneru.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Pro Jenkinse spuštěné mimo váš cluster:** Chcete-li zkopírovat certifikát clusteru do kontejneru, postupujte takto:
     1. Certifikát musí být ve formátu PEM. Pokud nemáte soubor PEM, můžete jej vytvořit ze souboru PFX certifikátu. Pokud váš soubor PFX není chráněn heslem, spusťte z hostitele následující příkaz:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
        ``` 

        Pokud je soubor PFX chráněn heslem, zahrňte heslo do parametru. `-passin` Například:

        ```sh
        openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
        ``` 

     1. Chcete-li získat ID kontejneru `docker ps` pro kontejner Jenkins, spusťte z hostitele.
     1. Zkopírujte soubor PEM do kontejneru pomocí následujícího příkazu Dockeru:
    
        ```sh
        docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
        ``` 

Už jsi skoro hotov! Nechte Jenkinse otevřenou. Jedinou zbývající úlohou je konfigurace kroků po sestavení pro nasazení aplikace do clusteru Service Fabric:

* Chcete-li nasadit do vývojového nebo testovacího prostředí, postupujte podle pokynů v [části Konfigurace nasazení pomocí koncového bodu správy clusteru](#configure-deployment-using-cluster-management-endpoint).
* Chcete-li se nasadit do produkčního prostředí, postupujte podle pokynů v [části Konfigurace nasazení pomocí přihlašovacích údajů Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurace nasazení pomocí koncového bodu správy clusteru

Pro vývojová a testovací prostředí můžete použít koncový bod správy clusteru k nasazení aplikace. Konfigurace akce po sestavení s koncovým bodem správy clusteru k nasazení aplikace vyžaduje nejmenší množství nastavení. Pokud nasazujete do produkčního prostředí, přeskočte na [konfigurovat nasazení pomocí přihlašovacích údajů Azure](#configure-deployment-using-azure-credentials) a nakonfigurujte objekt zabezpečení služby Azure Active Directory, který se použije během nasazení.    

1. V úloze Jenkins klikněte na kartu **Akce po sestavení.** 
1. V rozevírací nabídce **Post-Build Actions** (Akce po sestavení) vyberte **Deploy Service Fabric Project** (Nasazení projektu Service Fabric). 
1. V části **Konfigurace clusteru Service Fabric**vyberte **přepínací** tlačítko Vyplnit koncový bod služby Fabric Management.
1. V **případě hostitele správy**zadejte koncový bod připojení pro cluster. například `{your-cluster}.eastus.cloudapp.azure.com`.
1. U **klientského klíče** a **certifikátu klienta**zadejte umístění souboru PEM v kontejneru Jenkins; například `/var/jenkins_home/clustercert.pem`. (Zkopírovali jste umístění certifikátu v posledním kroku [vytvoření a konfigurace úlohy Jenkins](#create-and-configure-a-jenkins-job).)
1. V části **Konfigurace aplikace**nakonfigurujte pole **Název aplikace**, Typ **aplikace**a (relativní) cesta **k manifestu aplikace.**

   ![Koncový bod pro správu konfigurace konfigurace akce Service Fabric Jenkins po sestavení](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

1. Klepněte na **tlačítko Ověřit konfiguraci**. Při úspěšném ověření klepněte na tlačítko **Uložit**. Váš kanál úloh Jenkins je nyní plně nakonfigurován. Přejděte na [další kroky](#next-steps) a otestujte nasazení.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurace nasazení pomocí přihlašovacích údajů Azure

Pro produkční prostředí se důrazně doporučuje konfigurace pověření Azure pro nasazení vaší aplikace. Tato část ukazuje, jak nakonfigurovat objekt zabezpečení služby Azure Active Directory pro nasazení aplikace v akci po sestavení. Chcete-li omezit oprávnění úlohy Jenkinse, můžete k rolím v adresáři přiřadit objekty zabezpečení služeb. 

Pro vývojová a testovací prostředí můžete nakonfigurovat přihlašovací údaje Azure nebo koncový bod správy clusteru pro nasazení vaší aplikace. Podrobnosti o konfiguraci koncového bodu správy clusteru naleznete v [tématu Konfigurace nasazení pomocí koncového bodu pro správu clusteru](#configure-deployment-using-cluster-management-endpoint).   

1. Pokud chcete vytvořit objekt zabezpečení služby Azure Active Directory a přiřadit mu oprávnění ve vašem předplatném Azure, postupujte podle pokynů v [části Vytvoření aplikace a instancí Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)pomocí portálu . Věnujte pozornost následujícím uvažem:

   * Při následujících krocích v tématu nezapomeňte zkopírovat a uložit následující hodnoty: *ID aplikace*, *Aplikační klíč*, *ID adresáře (ID klienta)* a *ID předplatného*. Potřebujete je ke konfiguraci přihlašovacích údajů Azure v Jenkinsi.
   * Pokud nemáte [požadovaná oprávnění](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) ve vašem adresáři, budete muset požádat správce, aby vám buď udělil oprávnění, nebo vytvořil instanční objekt pro vás, nebo budete muset nakonfigurovat koncový bod správy pro váš cluster v **akcích po sestavení** pro vaši úlohu v Jenkinsi.
   * V části [Vytvořit aplikaci Azure Active Directory](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) můžete zadat libovolnou adresu URL ve formátu pro přihlašovací adresu **URL**.
   * V části [Přiřadit aplikaci k](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) roli můžete aplikaci přiřadit roli *Čtenář* ve skupině prostředků pro váš cluster.

1. V úloze Jenkinse klikněte na kartu **Akce po sestavení.**
1. V rozevírací nabídce **Post-Build Actions** (Akce po sestavení) vyberte **Deploy Service Fabric Project** (Nasazení projektu Service Fabric). 
1. V části **Konfigurace clusteru Prostředků služby**klepněte na **tlačítko Vybrat cluster prostředků infrastruktury služby**. Klikněte na **Přidat** vedle **přihlašovacích údajů Azure**. Kliknutím na **Jenkins** vyberte zprostředkovatele pověření Jenkinse.
1. V zprostředkovateli pověření Jenkinse vyberte **hlavní povinný služeb Microsoft Azure** v rozevíracím souboru **Druh.**
1. Pomocí hodnot, které jste uložili při nastavování instančního objektu v kroku 1, nastavte následující pole:

   * **ID klienta**: *ID aplikace*
   * **Tajný klíč klienta**: *Klíč aplikace*
   * **ID klienta**: *ID adresáře*
   * **ID předplatného:** *ID předplatného*
1. Zadejte popisné **ID,** které použijete k výběru pověření v Jenkinsi, a stručný **popis**. Potom klepněte na tlačítko **Ověřit instanční objekt**. Pokud je ověření úspěšné, klepněte na tlačítko **Přidat**.

   ![Service Fabric Jenkins zadá přihlašovací údaje Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
1. V části **Konfigurace clusteru Service Fabric**zkontrolujte, zda je pro pověření **Azure**vybrána vaše nová pověření . 
1. V rozevíracím seznamu **Skupina prostředků** vyberte skupinu prostředků clusteru, do kterého chcete aplikaci nasadit.
1. V rozevíracím panelu **Service Fabric** vyberte cluster, do kterého chcete aplikaci nasadit.
1. Pro **klientský klíč** a **certifikát klienta**zadejte umístění souboru PEM v kontejneru Jenkins. Například `/var/jenkins_home/clustercert.pem`. 
1. V části **Konfigurace aplikace**nakonfigurujte pole **Název aplikace**, Typ **aplikace**a (relativní) cesta **k manifestu aplikace.**
    ![Akce Jenkins e-build service fabric – konfigurace přihlašovacích údajů Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
1. Klepněte na **tlačítko Ověřit konfiguraci**. Při úspěšném ověření klepněte na tlačítko **Uložit**. Váš kanál úloh Jenkins je nyní plně nakonfigurován. Pokračujte [k dalším krokům](#next-steps) a otestujte nasazení.

## <a name="troubleshooting-the-jenkins-plugin"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud v modulech plug-in Jenkinse narazíte na nějaké chyby, založte problém na stránce [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="ideas-to-try"></a>Nápady vyzkoušet

GitHub a Jenkins jsou teď nakonfigurované. Zvažte provedení některé `reliable-services-actor-sample/Actors/ActorCounter` ukázkové změny v projektu v https://github.com/Azure-Samples/service-fabric-java-getting-startedrozsekace úložiště, . Zasuňte `master` změny do vzdálené větve (nebo libovolné větve, se kterou jste nakonfigurovali práci). Tím se aktivuje nakonfigurovaná úloha Jenkinse `MyJob`. Načte změny z GitHubu, vytvoří je a nasadí aplikaci do clusteru, který jste zadali v akcích po sestavení.  

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jenkins v Azure](/azure/Jenkins/)