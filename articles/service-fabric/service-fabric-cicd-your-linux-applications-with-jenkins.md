---
title: Průběžné sestavení a integraci pro vaše aplikace Azure Service Fabric Linux pomocí volaných | Microsoft Docs
description: Průběžné sestavení a integraci pro vaše aplikace Service Fabric Linux pomocí volaných
services: service-fabric
documentationcenter: java
author: sayantancs
manager: timlt
editor: ''
ms.assetid: 02b51f11-5d78-4c54-bb68-8e128677783e
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: saysa
ms.openlocfilehash: 047b3d00da4f192febeeab79c9c87b67a8a0489b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207957"
---
# <a name="use-jenkins-to-build-and-deploy-your-linux-applications"></a>Použití volaných sestavení a nasazení aplikací systému Linux
Jenkins je oblíbený nástroj pro průběžnou integraci a nasazování aplikací. Tady je postup, kterým můžete sestavit a nasadit aplikaci Azure Service Fabric s využitím Jenkinse.

## <a name="topic-overview"></a>Přehled tématu
Tento článek se zabývá několik možné způsoby, jak nastavení prostředí volaných a také jiné způsoby, jak nasadit aplikaci na cluster Service Fabric, jakmile byl vytvořen. Následujícím postupem obecné úspěšně nastavit volaných, změny pro vyžádání obsahu z Githubu, sestavit aplikaci a nasadíte ho do clusteru:

1. Ujistěte se, že instalujete [požadavky](#prerequisites).
2. Potom postupujte podle kroků v jedné z těchto částí nastavit volaných:
   * [Nastavení v rámci clusteru Service Fabric volaných](#set-up-jenkins-inside-a-service-fabric-cluster), 
   * [Nastavit volaných mimo cluster Service Fabric](#set-up-jenkins-outside-a-service-fabric-cluster), nebo
   * [Instalace modulu plug-in Service Fabric ve stávajícím prostředí volaných](#install-service-fabric-plugin-in-an-existing-jenkins-environment).
3. Poté, co jste nastavili volaných, postupujte podle kroků v [vytvořit a nakonfigurovat úlohu volaných](#create-and-configure-a-jenkins-job) nastavení Githubu spustí volaných při provedení změn do vaší aplikace a konfigurace vašeho kanálu volaných úlohy prostřednictvím krok sestavení na vyžádání obsahu změní z Githubu a sestavit aplikaci. 
4. Nakonec nakonfigurujte krok volaných úlohy po sestavení pro nasazení aplikace do clusteru Service Fabric. Ke konfiguraci volaných pro nasazení aplikace do clusteru se dvěma způsoby:    
   * Pro vývojové a testovací prostředí, použijte [nakonfigurovat nasazení pomocí koncový bod správy clusteru](#configure-deployment-using-cluster-management-endpoint). Toto je nejjednodušší způsob nasazení k nastavení.
   * Pro produkční prostředí, použijte [nakonfigurovat nasazení pomocí přihlašovacích údajů Azure](#configure-deployment-using-azure-credentials). Společnost Microsoft doporučuje tuto metodu pro provozní prostředí, protože pomocí přihlašovacích údajů služby Azure můžete omezit přístup, který má úloha volaných vašich prostředků Azure. 

## <a name="prerequisites"></a>Požadavky

- Zajistěte, aby byl že místně nainstalován Git. Můžete nainstalovat na příslušnou verzi Git z [Gitu stáhne stránky](https://git-scm.com/downloads) založený na operačním systému. Pokud jste ještě Git, další informace o z [Git dokumentaci](https://git-scm.com/docs).
- Tento článek používá *ukázkové s získávání spuštění nástroje Service Fabric* na Githubu: [ https://github.com/Azure-Samples/service-fabric-java-getting-started ](https://github.com/Azure-Samples/service-fabric-java-getting-started) aplikace k sestavení a nasazení. Můžete se podle nich zorientujete toto úložiště rozvětvit, nebo, v některých změn pokyny použijte projektu Githubu.


## <a name="install-service-fabric-plugin-in-an-existing-jenkins-environment"></a>Instalace modulu plug-in Service Fabric ve stávajícím prostředí volaných
Pokud přidáváte do existujícího prostředí volaných modulu plug-in Service Fabric, budete potřebovat následující:

- [Service Fabric rozhraní příkazového řádku](service-fabric-cli.md) (sfctl).

   > [!NOTE]
   > Nezapomeňte nainstalovat rozhraní příkazového řádku na úrovni systému, nikoli na úrovni uživatele, takže volaných můžete spustit příkazy rozhraní příkazového řádku. 
   >

- K nasazení aplikací v jazyce Java, obě nainstalovat [Gradle a otevřete 8.0 JDK](service-fabric-get-started-linux.md#set-up-java-development). 
- Chcete-li nasadit aplikace .NET Core 2.0, nainstalujte [.NET Core 2.0 SDK](service-fabric-get-started-linux.md#set-up-net-core-20-development). 

Po instalaci součásti potřebné pro vaše prostředí, můžete vyhledat modul plug-in služby infrastruktury Azure Marketplace volaných a nainstalujte ji.

Po instalaci modulu plug-in, přeskočit na [vytvořit a nakonfigurovat úlohu volaných](#create-and-configure-a-jenkins-job).


## <a name="set-up-jenkins-inside-a-service-fabric-cluster"></a>Nastavení Jenkinse uvnitř clusteru Service Fabric

Jenkinse můžete nastavit uvnitř clusteru Service Fabric nebo mimo něj. Následující části vysvětlují, jak nastavit je uvnitř clusteru při použití účtu úložiště Azure pro uložení stavu instance kontejneru.

### <a name="prerequisites"></a>Požadavky
- Máte cluster Service Fabric Linux s Docker nainstalována. Clustery Service Fabric už běží v Azure mají Docker nainstalována. Pokud používáte cluster místně (OneBox vývojová prostředí), zkontrolujte, zda je na počítači s nainstalován Docker `docker info` příkaz. Pokud nainstalovaná není, nainstalujte ji pomocí následujících příkazů:

   ```sh
   sudo apt-get install wget
   wget -qO- https://get.docker.io/ | sh
   ``` 

   > [!NOTE]
   > Ujistěte se, že 8081 port je zadán jako vlastní koncový bod v clusteru. Pokud používáte místní cluster, ujistěte se, zda je port 8081 otevřete na hostitelském počítači a zda má veřejnou IP adresu.
   >

### <a name="steps"></a>Kroky
1. Klonování aplikace, pomocí následujících příkazů:
   ```sh
   git clone https://github.com/suhuruli/jenkins-container-application.git
   cd jenkins-container-application
   ```

3. Zachování stavu kontejneru volaných ve sdílené složce:
   1. Vytvoření účtu úložiště Azure v **stejné oblasti** jako cluster s názvem, jako `sfjenkinsstorage1`.
   2. Vytvoření **sdílené složky** v části úložiště účet s názvem, jako `sfjenkins`.
   3. Klikněte na **připojit** pro sdílené složky a Poznámka hodnoty zobrazuje v části **připojení z Linux**, hodnota by měla vypadat podobná té následující:

      ```sh
      sudo mount -t cifs //sfjenkinsstorage1.file.core.windows.net/sfjenkins [mount point] -o vers=3.0,username=sfjenkinsstorage1,password=<storage_key>,dir_mode=0777,file_mode=0777
      ```

   > [!NOTE]
   > Pro sdílené složky cifs připojení musíte mít cifs utils balíček nainstalován v uzlu clusteru.      
   >

4. Aktualizujte zástupný symbol hodnoty v `setupentrypoint.sh` skriptu s podrobnostmi o službě azure-storage z kroku 2.
   ```sh
   vi JenkinsSF/JenkinsOnSF/Code/setupentrypoint.sh
   ```
   * Nahraďte `[REMOTE_FILE_SHARE_LOCATION]` s hodnotou `//sfjenkinsstorage1.file.core.windows.net/sfjenkins` z výstupu connect v kroku 2 výše.
   * Nahraďte `[FILE_SHARE_CONNECT_OPTIONS_STRING]` s hodnotou `vers=3.0,username=sfjenkinsstorage1,password=GB2NPUCQY9LDGeG9Bci5dJV91T6SrA7OxrYBUsFHyueR62viMrC6NIzyQLCKNz0o7pepGfGY+vTa9gxzEtfZHw==,dir_mode=0777,file_mode=0777` z kroku 2 výše.

5. **Pouze zabezpečené clusteru:** 
   
   Na clusteru s podporou zabezpečení z volaných nakonfigurovat nasazení aplikací, musí být přístupné v kontejneru volaných certifikát clusteru. V *ApplicationManifest.xml* souborů, v části **ContainerHostPolicies** značku přidat odkaz na tento certifikát a aktualizujte hodnotu kryptografický otisk se u certifikátu clusteru.

   ```xml
   <CertificateRef Name="MyCert" X509FindValue="[Thumbprint]"/>
   ```

   Kromě toho přidejte následující řádky v části **ApplicationManifest** tag (uživatel root) *ApplicationManifest.xml* souboru a aktualizujte hodnotu kryptografický otisk se u certifikátu clusteru.

   ```xml
   <Certificates>
     <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[Thumbprint]" />
   </Certificates> 
   ```

6. Připojte se ke clusteru a nainstalujte aplikaci kontejneru.

   **Zabezpečení clusteru**
   ```sh
   sfctl cluster select --endpoint https://PublicIPorFQDN:19080  --pem [Pem] --no-verify # cluster connect command
   bash Scripts/install.sh
   ```
   Předchozí příkaz trvá certifikát ve formátu PEM. Pokud je váš certifikát ve formátu PFX, můžete ji převést následující příkaz. Pokud soubor PFX není chráněn heslem, zadejte **passin** parametr jako `-passin pass:`.
   ```sh
   openssl pkcs12 -in cert.pfx -out cert.pem -nodes -passin pass:MyPassword1234!
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

7. V prohlížeči přejděte na `http://PublicIPorFQDN:8081`. Najdete tam cestu k počátečnímu heslu správce vyžadovanému k přihlášení. 
2. Podívejte se na Service Fabric Explorer k určení, na který uzel kontejneru volaných běží. Secure Shell (SSH) přihlášení pro tento uzel.
   ```sh
   ssh user@PublicIPorFQDN -p [port]
   ``` 
3. Získejte ID instance kontejneru pomocí příkazu `docker ps -a`.
4. Secure Shell (SSH) přihlášení do kontejneru a vložte cestu, kterou se zobrazí na portálu volaných. Například, pokud na portálu se zobrazí cesta `PATH_TO_INITIAL_ADMIN_PASSWORD`, spusťte následující příkazy:

   ```sh
   docker exec -t -i [first-four-digits-of-container-ID] /bin/bash   # This takes you inside Docker shell
   ```
   ```sh
   cat PATH_TO_INITIAL_ADMIN_PASSWORD # This displays the password value
   ```
5. Na stránce Začínáme volaných zvolte vyberte modulů plug-in pro instalaci možnost vyberte **žádné** zaškrtávací políčko a klikněte na tlačítko nainstalovat.
6. Vytvořte uživatele nebo vyberte, chcete-li pokračovat, protože správce.

Poté, co jste nastavili volaných, přeskočit na [vytvořit a nakonfigurovat úlohu volaných](#create-and-configure-a-jenkins-job).  

## <a name="set-up-jenkins-outside-a-service-fabric-cluster"></a>Nastavení Jenkinse mimo cluster Service Fabric

Jenkinse můžete nastavit uvnitř clusteru Service Fabric nebo mimo něj. Následující části vysvětlují, jak ho nastavit mimo cluster.

### <a name="prerequisites"></a>Požadavky
- Ujistěte se, že Docker je nainstalovaný na počítači. Pomocí následujících příkazů můžete Docker nainstalovat z terminálu:

  ```sh
  sudo apt-get install wget
  wget -qO- https://get.docker.io/ | sh
  ```

  Při spuštění `docker info` v terminálu, by měl zobrazit výstup, zda je spuštěna služba Docker.

### <a name="steps"></a>Kroky
1. Stáhněte si image kontejneru s Jenkinsem pro Service Fabric: `docker pull rapatchi/jenkins:latest`. Tato image je součástí instalace modulu plug-in Jenkinse pro Service Fabric.
2. Spusťte image kontejneru: `docker run -itd -p 8080:8080 rapatchi/jenkins:latest`
3. Získejte ID instance image kontejneru. Pomocí příkazu `docker ps –a` můžete vypsat všechny kontejnery Dockeru.
4. Přihlaste se k portálu volaných pomocí následujících kroků:

   1. Přihlaste se k prostředí volaných z vaší hostitele. Použít první čtyři číslice ID kontejneru. Například, pokud je ID kontejneru `2d24a73b5964`, použijte `2d24`.

      ```sh
      docker exec -it [first-four-digits-of-container-ID] /bin/bash
      ```
   2. Z prostředí volaných získáte heslo správce pro instanci kontejneru:

      ```sh
      cat /var/jenkins_home/secrets/initialAdminPassword
      ```      
   3. Chcete-li přihlásit se k řídicímu panelu volaných, otevřete následující adresu URL ve webovém prohlížeči: `http://<HOST-IP>:8080`. Volaných odemknout pomocí hesla z předchozího kroku.
   4. (Volitelné.) Po přihlášení poprvé, můžete vytvořit svůj vlastní uživatelský účet a použijte následující postup, nebo můžete nadále používat účet správce. Pokud vytvoříte uživatele, budete muset pokračovat s tímto uživatelem.
5. Nastavit GitHub pro práci s volaných pomocí kroků v [generování nového klíče SSH a její přidání do agenta SSH](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/).
   * Pomocí pokynů z GitHubu vygenerujte klíč SSH a přidejte ho do účtu GitHubu, který je hostitelem úložiště.
   * Příkazy popsané u předchozího odkazu spusťte v prostředí Jenkins Dockeru (ne na hostiteli).
   * Pokud se chcete k prostředí Jenkinse přihlásit z hostitele, použijte následující příkaz:

      ```sh
      docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
      ```

Ujistěte se, že cluster nebo počítač je hostitelem kontejneru bitovou kopii volaných má veřejnou IP adresu. To umožňuje, aby instance Jenkinse dostávala oznámení z GitHubu.

Poté, co jste nastavili volaných, pokračujte v další části [vytvořit a nakonfigurovat úlohu volaných](#create-and-configure-a-jenkins-job).

## <a name="create-and-configure-a-jenkins-job"></a>Vytvoření a konfigurace úlohy Jenkinse

Kroky v této části ukazují, jak nakonfigurovat úlohu volaných pro odpověď na změny v úložišti GitHub, načtení změny a jejich vytvoření. Na konci této části se nasměruje na poslední kroky konfigurace úlohu pro nasazení aplikace založené na tom, jestli nasazujete pro vývoj a testovací prostředí nebo v produkčním prostředí. 

1. Na řídicím panelu volaných, klikněte na tlačítko **novou položku**.
2. Zadejte název položky (třeba **MyJob**). Vyberte **free-style project** (volný styl projektu) a klikněte na **OK**.
3. Otevře se stránka konfigurace úlohy. (Získat konfiguraci z řídicího panelu volaných, klikněte na úlohu a potom klikněte na **konfigurace**).

4. Na **Obecné** kartě, zaškrtněte políčko pro **Githubu projektu**, a zadejte svoji adresu URL projektu Githubu. Tato adresa je hostitelem aplikace Service Fabric v Javě, kterou chcete integrovat s postupy průběžného nasazování a integrace (CI/CD) Jenkinse (např. `https://github.com/{your-github-account}/service-fabric-java-getting-started`).

5. Na **správu zdrojového kódu** vyberte **Git**. Zadejte adresu URL úložiště, které je hostitelem aplikace Service Fabric v Javě, kterou chcete integrovat s postupy CI/CD Jenkinse (např. `https://github.com/{your-github-account}/service-fabric-java-getting-started`). Můžete také určit, která větev k sestavení (například `/master`).
6. Konfigurace vaší *Githubu* úložiště, aby komunikoval s volaných:

   a. Na stránce úložiště Githubu, přejděte na **nastavení** > **integrace služby a služby**.

   b. Vyberte **Add Service** (Přidat službu), zadejte **Jenkins** a vyberte **Jenkins-Github plugin** (Modul plug-in Jenkins-Github).

   c. Zadejte adresu URL webhooku Jenkinse (ve výchozím nastavení by měla být `http://<PublicIPorFQDN>:8081/github-webhook/`). Klikněte na **add/update service** (Přidat/aktualizovat službu).

   d. Do instance Jenkinse se odešle testovací událost. V GitHubu by se měla vedle webhooku zobrazit zelená značka zaškrtnutí a váš projekt se sestaví.

7. Na **sestavení aktivační události** ve volaných, vyberte, které možnost, kterou chcete vytvořit. V tomto příkladu, kterou chcete aktivovat build vždy, když se stane push do úložiště, takže vyberte **Githubu háku aktivační událost pro dotazování GITScm**. (Dřív se tato možnost nazývala **Build when a change is pushed to GitHub** (Sestavit při vložení změny metodou Push do GitHubu).
8. Na **sestavení** proveďte jednu z následujících akcí v závislosti na tom, zda vytváříte aplikaci Java nebo .NET Core:

   * **Pro aplikace Java:** z **přidat krok sestavení** rozevíracího seznamu, vyberte **vyvolání skriptu Gradle**. Klikněte na tlačítko **rozšířené**. V nabídce pokročilé zadejte cestu k **kořenové sestavení skriptu** pro vaši aplikaci. Sestavení si ze zadané cesty vezme soubor build.gradle a bude pracovat odpovídajícím způsobem. Pro [ActorCounter aplikace](https://github.com/Azure-Samples/service-fabric-java-getting-started/tree/master/reliable-services-actor-sample/Actors/ActorCounter), to je: `${WORKSPACE}/reliable-services-actor-sample/Actors/ActorCounter`.

     ![Akce sestavení v Jenkinsu pro Service Fabric][build-step]

   * **Pro aplikace .NET Core:** z **přidat krok sestavení** rozevíracího seznamu, vyberte **spustit prostředí**. V poli příkaz, který se zobrazí adresáři nejprve musí změnit tak, aby cestu, kde je umístěn soubor build.sh. Jakmile změníte adresáři build.sh skript lze spustit a bude sestavení aplikace.

      ```sh
      cd /var/jenkins_home/workspace/[Job Name]/[Path to build.sh]  # change directory to location of build.sh file
      ./build.sh
      ```

     Následující snímek obrazovky ukazuje příklad příkazy, které se používají k vytvoření [čítač služby](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started/tree/master/Services/CounterService) ukázku s názvem úlohy volaných CounterServiceApplication.

      ![Akce sestavení v Jenkinsu pro Service Fabric][build-step-dotnet]

9. Ke konfiguraci volaných k nasazení vaší aplikace na cluster Service Fabric v akce po sestavení, potřebujete umístění certifikátu daného clusteru v vaší volaných kontejneru. Vyberte jednu z následujících akcí v závislosti na tom, jestli vaše volaných kontejneru běží uvnitř nebo vně vašeho clusteru a poznamenejte si umístění certifikátu clusteru:

   * **Pro spuštění v rámci clusteru volaných:** lze najít cestu k certifikátu zobrazování hodnotu *Certificates_JenkinsOnSF_Code_MyCert_PEM* proměnnou prostředí z kontejneru uvnitř tohoto kontejneru.

      ```sh
      echo $Certificates_JenkinsOnSF_Code_MyCert_PEM
      ```
   
   * **Pro volaných běží mimo cluster:** pomocí následujícího postupu zkopírujte certifikát clusteru do vašeho kontejneru:
      1. Certifikát musí být ve formátu PEM. Pokud nemáte soubor PEM, můžete vytvořit jeden ze souboru certifikátu PFX. Pokud soubor PFX není chráněn heslem, spusťte následující příkaz z vaší hostitele:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:
         ``` 

      Pokud soubor PFX je chráněný heslem, zadejte heslo v `-passin` parametr. Příklad:

         ```sh
         openssl pkcs12 -in clustercert.pfx -out clustercert.pem -nodes -passin pass:MyPassword1234!
         ``` 

      2. Chcete-li získat ID kontejneru pro vaše volaných kontejneru, spusťte `docker ps` z vaší hostitele.
      3. Zkopírujte soubor PEM, který do vašeho kontejneru pomocí následujícího příkazu Docker:
    
         ```sh
         docker cp clustercert.pem [first-four-digits-of-container-ID]:/var/jenkins_home
         ``` 

Budete hotovi téměř! Nechte úlohu volaných otevřené. Jenom zbývající úloh je konfigurace po sestavení postup nasazení aplikace do clusteru Service Fabric:

* K nasazení na vývojovém nebo testovacím prostředí, postupujte podle kroků v [nakonfigurovat nasazení pomocí koncový bod správy clusteru](#configure-deployment-using-cluster-management-endpoint).
* Pokud chcete nasadit do produkčního prostředí, postupujte podle kroků v [nakonfigurovat nasazení pomocí přihlašovacích údajů Azure](#configure-deployment-using-azure-credentials).

## <a name="configure-deployment-using-cluster-management-endpoint"></a>Konfigurace nasazení pomocí koncový bod správy clusteru
Pro vývojové a testovací prostředí můžete koncový bod správy clusteru pro nasazení aplikace. Konfigurace akce po sestavení s koncovým bodem správy clusteru pro nasazení aplikace vyžaduje minimálně množství nastavení. Pokud nasazujete do provozního prostředí, přeskočit na [nakonfigurovat nasazení pomocí přihlašovacích údajů Azure](#configure-deployment-using-azure-credentials) konfigurace objektu zabezpečení služby Azure Active Directory k použití během nasazování.    

1. V úloze volaných, klikněte na tlačítko **akce po sestavení** kartě. 
2. V rozevírací nabídce **Post-Build Actions** (Akce po sestavení) vyberte **Deploy Service Fabric Project** (Nasazení projektu Service Fabric). 
3. V části **konfigurace clusteru Service Fabric**, vyberte **vyplnění koncový bod služby prostředků infrastruktury správy** přepínač.
4. Pro **správy hostitele**, zadejte koncového bodu připojení pro váš cluster; například `{your-cluster}.eastus.cloudapp.azure.com`.
5. Pro **klíč klienta** a **klientského certifikátu**, zadejte umístění souboru PEM ve vašem volaných kontejneru; například `/var/jenkins_home/clustercert.pem`. (Jste zkopírovali umístění certifikátu poslední krok [vytvořit a nakonfigurovat úlohu volaných](#create-and-configure-a-jenkins-job).)
6. V části **konfigurace aplikace**, nakonfigurujte **název aplikace**, **typ aplikace**a (relativní) **cestu k Application Manifest** pole.

   ![Akce po sestavení volaných prostředků infrastruktury služby konfigurace koncového bodu správy](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-endpoint.png)

7. Klikněte na tlačítko **ověření konfigurace**. Na úspěšné ověřování, klikněte na tlačítko **Uložit**. Svůj volaných úlohy kanál je nyní plně nakonfigurován. Přeskočit na [další kroky](#next-steps) nasazení otestovat.

## <a name="configure-deployment-using-azure-credentials"></a>Konfigurace nasazení pomocí přihlašovacích údajů služby Azure
Pro produkční prostředí konfiguraci Azure pověření pro nasazení aplikace důrazně doporučujeme. V této části se dozvíte, jak nakonfigurovat objektu služby Azure Active Directory umožňuje použít k nasazení aplikace v akci po sestavení. Objekty služby můžete přiřadit role ve vašem adresáři omezit oprávnění volaných úlohy. 

Pro vývojové a testovací prostředí můžete nakonfigurovat přihlašovací údaje Azure nebo koncový bod správy clusteru pro nasazení aplikace. Podrobnosti o tom, jak nakonfigurovat správu koncový bod clusteru najdete v tématu [nakonfigurovat nasazení pomocí koncový bod správy clusteru](#configure-deployment-using-cluster-management-endpoint).   

1. Vytvořit objekt služby Azure Active Directory a přiřadit jí oprávnění ve vašem předplatném Azure, postupujte podle kroků v [vytvořit Azure Active Directory objekt aplikace a služby pomocí portálu](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal). Věnujte pozornost následujícímu:

   * Při postupovat podle kroků v tématu, nezapomeňte zkopírovat a uložit následující hodnoty: *ID aplikace*, *klíč aplikace*, *ID adresáře (ID klienta)* a *ID předplatného*. Musíte je nakonfigurovat přihlašovací údaje Azure v volaných.
   * Pokud nemáte [požadovaná oprávnění](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) na adresáře, budete muset požádat správce, aby vám udělil oprávnění nebo vytvořit objekt služby pro vás, nebo budete muset nakonfigurovat koncový bod správy pro vaše v clusteru **akce po sestavení** pro vaši úlohu v volaných.
   * V [vytvořit aplikaci Azure Active Directory](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#create-an-azure-active-directory-application) části, můžete zadat všechny ve správném formátu adresy URL pro **přihlašovací adresa URL**.
   * V [přiřazení aplikace k roli](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) části, můžete přiřadit aplikaci *čtečky* role ve skupině prostředků clusteru.

2. Zpět v volaných úlohu, klikněte na **akce po sestavení** kartě.
3. V rozevírací nabídce **Post-Build Actions** (Akce po sestavení) vyberte **Deploy Service Fabric Project** (Nasazení projektu Service Fabric). 
4. V části **konfigurace clusteru Service Fabric**, vyberte **vyberte Service Fabric Cluster** přepínač. Klikněte na tlačítko **přidat** vedle **přihlašovací údaje Azure**. Klikněte na tlačítko **volaných** vybrat zprostředkovatele volaných přihlašovací údaje.
5. Ve zprostředkovateli volaných přihlašovací údaje, vyberte **Microsoft Azure Service Principal** z **druh** rozevíracího seznamu.
6. Použijte hodnoty, který jste uložili při nastavování si vaše instančního objektu v kroku 1, chcete-li nastavit následující pole:

   * **ID klienta**: *ID aplikace*
   * **Tajný klíč klienta**: *klíč aplikace*
   * **ID klienta**: *ID adresáře*
   * **ID předplatného**: *ID předplatného*
6. Zadejte popisný **ID** , které umožňují vyberte přihlašovací údaje ve volaných a brief **popis**. Pak klikněte na tlačítko **ověřte instanční objekt**. Pokud bude ověření úspěšné, klikněte na tlačítko **přidat**.

   ![Zadejte přihlašovací údaje Azure Service Fabric volaných](./media/service-fabric-cicd-your-linux-application-with-jenkins/enter-azure-credentials.png)
7. Zpět v části **konfigurace clusteru Service Fabric**, ujistěte se, že je vybraný svoje nové přihlašovací údaje pro **přihlašovací údaje Azure**. 
8. Z **skupiny prostředků** rozevíracího seznamu, vyberte skupinu prostředků clusteru, kterou chcete nasadit aplikaci.
9. Z **Service Fabric** rozevíracího seznamu, vyberte cluster, který chcete aplikaci nasadit.
10. Pro **klíč klienta** a **klientského certifikátu**, zadejte umístění souboru PEM ve vaší volaných kontejneru. Například `/var/jenkins_home/clustercert.pem`. 
11. V části **konfigurace aplikace**, nakonfigurujte **název aplikace**, **typ aplikace**a (relativní) **cestu k Application Manifest** pole.
    ![Akce po sestavení volaných prostředků infrastruktury služby konfigurace přihlašovacích údajů služby Azure](./media/service-fabric-cicd-your-linux-application-with-jenkins/post-build-credentials.png)
12. Klikněte na tlačítko **ověření konfigurace**. Na úspěšné ověřování, klikněte na tlačítko **Uložit**. Svůj volaných úlohy kanál je nyní plně nakonfigurován. Pokračujte na [další kroky](#next-steps) nasazení otestovat.

## <a name="next-steps"></a>Další postup
GitHub a Jenkins jsou teď nakonfigurované. Zvažte některé ukázkové v změní `reliable-services-actor-sample/Actors/ActorCounter` projektu ve vaší rozvětvení úložiště, https://github.com/Azure-Samples/service-fabric-java-getting-started. Odešlete své změny ke vzdálenému `master` větve (nebo všechny firemní pobočky, který jste nakonfigurovali pro práci s). Tím se aktivuje nakonfigurovaná úloha Jenkinse `MyJob`. Načte změny z Githubu, je sestavení a nasadí aplikaci do clusteru, který jste zadali v akce po sestavení.  

  <!-- Images -->
  [build-step]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step.png
  [build-step-dotnet]: ./media/service-fabric-cicd-your-linux-application-with-jenkins/build-step-dotnet.png

