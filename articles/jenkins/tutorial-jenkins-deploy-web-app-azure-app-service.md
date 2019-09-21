---
title: 'Kurz: Nasazení z GitHubu do Azure App Service pomocí Jenkinse'
description: Nastavení Jenkinse pro průběžnou integraci (CI) z GitHubu a průběžného nasazování (CD) pro Azure App Service pro webové aplikace v jazyce Java
services: jenkins
ms.service: jenkins
author: tomarchermsft
ms.author: tarcher
manager: jeconnoc
ms.topic: tutorial
ms.date: 11/15/2018
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: c4e4a984adc0ec6af99667ff36c009ca730acf48
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172839"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Kurz: Nasazení z GitHubu do Azure App Service s průběžnou integrací a nasazením Jenkinse

V tomto kurzu se nasadí Ukázková webová aplikace v jazyce Java z GitHubu do [Azure App Service na Linux](/azure/app-service/containers/app-service-linux-intro) tím, že se nastaví průběžná integrace (CI) a průběžné nasazování (CD) v Jenkinse. Když aplikaci aktualizujete vložením potvrzení do GitHubu, Jenkinse automaticky vytvoří a znovu publikuje vaši aplikaci, aby Azure App Service. Ukázková aplikace v tomto kurzu byla vyvinutá pomocí rozhraní pro [spouštění pružiny](https://projects.spring.io/spring-boot/) . 

![Přehled nasazení GitHubu na Azure App Service](media/tutorial-jenkins-deploy-web-app-azure-app-service/azure-continuous-integration-deployment-overview.png)

V tomto kurzu dokončíte tyto úlohy:

> [!div class="checklist"]
> * Nainstalujte moduly plug-in Jenkinse, abyste mohli sestavovat z GitHubu, nasadit na Azure App Service a další související úlohy.
> * Rozvětvení ukázkového úložiště GitHub, abyste měli pracovní kopii.
> * Připojte Jenkinse k GitHubu.
> * Vytvořte instanční objekt Azure, aby Jenkinse mohl přistupovat k Azure bez použití vašich přihlašovacích údajů.
> * Přidejte objekt služby do Jenkinse.
> * Vytvořte kanál Jenkinse, který sestaví a nasadí ukázkovou aplikaci pokaždé, když aplikaci aktualizujete na GitHubu.
> * Vytvořte soubory sestavení a nasazení pro Jenkinse kanál.
> * Nasměrujte svůj kanál Jenkinsee na skript sestavení a nasazení.
> * Nasaďte ukázkovou aplikaci do Azure spuštěním ručního sestavení.
> * Nahrajte aktualizaci aplikace do GitHubu, která spustí Jenkinse k sestavení a opětovnému nasazení do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu budete potřebovat tyto položky:

* Server [Jenkinse](https://jenkins.io/) se sadou Java Development Kit (JDK) a Maven Tools nainstalovanými na virtuálním počítači Azure Linux

  Pokud nemáte server Jenkinse, proveďte tyto kroky nyní v Azure Portal: [Vytvoření serveru Jenkinse na virtuálním počítači Azure Linux](/azure/jenkins/install-jenkins-solution-template)

* Účet [GitHub](https://github.com) , abyste pro ukázkovou webovou aplikaci v jazyce Java mohli získat pracovní kopii (rozvětvení). 

* [Azure CLI](/cli/azure/install-azure-cli), které můžete spustit z místního příkazového řádku nebo [Azure Cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>Instalace modulů plug-in Jenkinse

1. Přihlaste se k webové konzoli Jenkinse v tomto umístění:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Na hlavní stránce Jenkinse vyberte **Spravovat Jenkinse** > **Správa modulů plug-in**.

   ![Správa modulů plug-in Jenkinse](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. Na kartě **k dispozici** vyberte tyto moduly plug-in:

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [Zdroj větve GitHubu](https://plugins.jenkins.io/github-branch-source)
   - [Modul plug-in Jenkinse prostředí pro vložení prostředí](https://plugins.jenkins.io/envinject)
   - [Přihlašovací údaje Azure](https://plugins.jenkins.io/azure-credentials)

   Pokud se tyto moduly plug-in nezobrazí, ujistěte se, že jste ještě nenainstalovali, a to tak, že zkontrolujete kartu nainstalováno.

1. Pokud chcete nainstalovat vybrané moduly plug-in, vyberte **Stáhnout hned a po restartování nainstalujte**.

1. Až to budete mít, vyberte v nabídce Jenkinse možnost **Spravovat Jenkinse** , abyste se na stránce pro správu Jenkinse vrátili k budoucím krokům.

## <a name="fork-sample-github-repo"></a>Ukázka větvení úložiště GitHubu

1. [Přihlaste se k úložišti GitHub pro ukázkovou aplikaci pro pružinové spouštění](https://github.com/spring-guides/gs-spring-boot). 

1. V pravém horním rohu GitHubu vyberte **rozvětvení**.

   ![Ukázkové úložiště pro rozvětvení z GitHubu](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Podle pokynů vyberte svůj účet GitHub a dokončete rozvětvení.

Dále nastavte Jenkinse s přihlašovacími údaji k GitHubu.

## <a name="connect-jenkins-to-github"></a>Připojení Jenkinse k GitHubu

Pokud chcete, aby nástroj Jenkinse monitoroval GitHub a reagovali, když se nové potvrzení do vaší webové aplikace vloží do [](https://developer.github.com/webhooks/) vaší větve GitHubu, povolte Webhooky GitHubu v Jenkinse.

> [!NOTE]
> 
> Tyto kroky vytvoří přihlašovací údaje k osobnímu přístupovému tokenu pro Jenkinse pro práci s GitHubem pomocí uživatelského jména a hesla GitHubu. 
> Pokud však váš účet GitHub používá dvojúrovňové ověřování, vytvořte token v GitHubu a nastavte Jenkinse na použití tohoto tokenu. 
> Další informace najdete v dokumentaci k [modulům plug-in GitHubu Jenkinse](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin) .

1. Na stránce **Spravovat Jenkinse** vyberte **Konfigurovat systém**. 

   ![Konfigurace systému v Jenkinse](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. V části **GitHub** zadejte podrobnosti pro váš server GitHub. V seznamu **Přidat server GitHub** vyberte **GitHub Server**. 

   ![Přidání serveru GitHubu v Jenkinse](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Pokud není vybraná vlastnost **Spravovat háky** , vyberte tuto vlastnost. Vyberte **Upřesnit** , abyste mohli zadat další nastavení. 

   ![Zadat Pokročilá nastavení Jenkinse pro server GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. V seznamu **spravovat další akce GitHubu** vyberte **převést přihlašovací jméno a heslo na token**.

   ![Převést přihlašovací údaje a heslo na token pro GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Vyberte **přihlášení a heslo** , abyste mohli zadat své uživatelské jméno a heslo GitHubu. Až to budete mít, vyberte **vytvořit přihlašovací údaje tokenu**. tím se vytvoří [token Pat GitHubu](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Vytvoření PAT GitHubu z přihlašovacího jména a hesla](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. V části **Server GitHub** v seznamu **přihlašovací údaje** vyberte svůj nový token. Ověřte, že ověřování funguje, výběrem možnosti **Testovat připojení**.

   ![Ověřte připojení k serveru GitHub s novou PAT.](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

V dalším kroku vytvoříte instanční objekt Azure, který Jenkinse používá pro ověřování a přístup k prostředkům Azure.

## <a name="create-service-principal"></a>Vytvoření instančního objektu

V pozdější části vytvoříte úlohu kanálu Jenkinse, která sestaví vaši aplikaci z GitHubu a nasadí vaši aplikaci na Azure App Service. Pokud chcete mít Jenkinse přístup k Azure bez zadání vašich přihlašovacích údajů, vytvořte [instanční objekt](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) v Azure Active Directory pro Jenkinse. Instanční objekt je samostatná identita, kterou může Jenkinse použít k ověřování přístupu k prostředkům Azure. Pokud chcete vytvořit tento instanční objekt, spusťte příkaz [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)Azure CLI, a to buď z místního příkazového řádku, nebo Azure Cloud Shell, například: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Ujistěte se, že jste k hlavnímu názvu služby používali uvozovky. Můžete také vytvořit silné heslo na základě [pravidel a omezení pro Azure Active Directory hesla](/azure/active-directory/active-directory-passwords-policy). Pokud heslo nezadáte, rozhraní příkazového řádku Azure pro vás vytvoří heslo. 

Zde je výstup generovaný **`create-for-rbac`** příkazem: 

```json
{
   "appId": "yourAzureServicePrincipal-ID", // A GUID such as AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA
   "displayName": "yourAzureServicePrincipalName", // A user-friendly name for your Azure service principal
   "name": "http://yourAzureServicePrincipalName",
   "password": "yourSecurePassword",
   "tenant": "yourAzureActiveDirectoryTenant-ID" // A GUID such as BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBBB
}
```

> [!TIP]
> 
> Pokud již máte instanční objekt, můžete místo toho použít tuto identitu.
> Při poskytování hodnot instančního objektu pro ověřování použijte `appId`hodnoty vlastností, `password`a `tenant` . 
> Při hledání existujícího objektu služby použijte `displayName` hodnotu vlastnosti.

## <a name="add-service-principal-to-jenkins"></a>Přidání instančního objektu do Jenkinse

1. Na hlavní stránce Jenkinse vyberte možnost**systém** **přihlašovacích údajů** > . 

1. Na stránce **systém** v části **doména**vyberte **globální přihlašovací údaje (neomezeno)** .

1. V nabídce vlevo vyberte **Přidat přihlašovací údaje**.

1. V seznamu **druh** vyberte položku **instanční objekt Azure**.

1. Zadejte informace o instančním objektu a předplatném Azure ve vlastnostech popsaných v tabulce v tomto kroku:

   ![Přidat pověření instančního objektu služby Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Vlastnost | Value | Popis | 
   |----------|-------|-------------| 
   | **ID předplatného** | <*yourAzureSubscription-ID*> | Hodnota GUID předplatného Azure <p>**Tip**: Pokud neznáte ID předplatného Azure, spusťte tento příkaz Azure CLI z příkazového řádku nebo v Cloud Shell a pak použijte `id` hodnotu GUID: <p>`az account list` | 
   | **ID klienta** | <*yourAzureServicePrincipal-ID*> | Hodnota `appId` GUID, která se dřív vygenerovala pro váš instanční objekt Azure | 
   | **Tajný kód klienta** | <*yourSecurePassword*> | `password` Hodnota nebo "tajný klíč", který jste zadali pro instanční objekt Azure | 
   | **ID tenanta** | <*yourAzureActiveDirectoryTenant-ID*> | Hodnota `tenant` GUID pro vašeho tenanta Azure Active Directory | 
   | **ID** | <*yourAzureServicePrincipalName*> | `displayName` Hodnota pro objekt služby Azure | 

1. Pokud chcete potvrdit, že váš instanční objekt funguje, vyberte **ověřit instanční objekt**. Až to bude hotové, vyberte **OK**.

Dále vytvořte kanál Jenkinse, který sestaví a nasadí vaši aplikaci.

## <a name="create-jenkins-pipeline"></a>Vytvoření kanálu Jenkinse

V Jenkinse vytvořte úlohu kanálu pro sestavení a nasazení vaší aplikace.

1. Vraťte se na domovskou stránku Jenkinse a vyberte možnost **Nová položka**. 

   ![Vytvoření kanálu Jenkinse](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Zadejte název úlohy kanálu, například "My-Java-Web-App" a vyberte **kanál**. V dolní části vyberte **OK**.  

   ![Pojmenování úlohy kanálu Jenkinse](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Nastavte si Jenkinse s instančním objektem, aby se Jenkinse mohla nasadit do Azure bez použití vlastních přihlašovacích údajů.

   1. Na kartě **Obecné** vyberte **Příprava prostředí pro spuštění**. 

   1. V zobrazeném okně **obsah vlastností** přidejte tyto proměnné prostředí a jejich hodnoty. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Příprava prostředí pro spuštění a nastavení proměnných prostředí](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-jenkins-run.png)

1. Jakmile budete mít hotovo, vyberte **Uložit**.

Dále vytvořte skripty sestavení a nasazení pro Jenkinse.

## <a name="create-build-and-deployment-files"></a>Vytvoření souborů sestavení a nasazení

Teď vytvořte soubory, které Jenkinse používá pro sestavování a nasazování vaší aplikace.

1. Ve `src/main/resources/` složce rozvětvení GitHubu vytvořte tento konfigurační soubor aplikace s `web.config`názvem, který obsahuje `gs-spring-boot-0.1.0.jar`tento kód XML `$(JAR_FILE_NAME)` , ale nahraďte:

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. V kořenové složce rozvětvení GitHubu vytvořte tento skript sestavení a nasazení s `Jenkinsfile`názvem, který obsahuje tento text ([zdroj v GitHubu tady](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```groovy
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. `web.config` PotvrďtesouboryidorozvětveníGitHubu`Jenkinsfile` a nahrajte změny.

## <a name="point-pipeline-at-script"></a>Kanál bodu na skriptu

Nyní zadejte skript sestavení a nasazení, který má Jenkinse použít.

1. V Jenkinse vyberte dříve vytvořenou úlohu kanálu. 

   ![Výběr úlohy kanálu Jenkinse pro webovou aplikaci](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. V nabídce vlevo vyberte **Konfigurovat**.

1. Na kartě **kanál** v seznamu **definice** vyberte možnost **skript kanálu z SCM**.

   1. V okně **SCM** , které se zobrazí, vyberte **Git** jako správu zdrojového kódu. 

   1. V části úložiště v poli **Adresa URL úložiště**zadejte adresu URL rozvětvení GitHubu, například: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. V případě **přihlašovacích údajů**vyberte dříve vytvořený osobní přístupový token GitHubu.

   1. Do pole **cesta ke skriptu** přidejte cestu k vašemu skriptu "Jenkinsfile".

   Až budete hotovi, vaše definice kanálu bude vypadat jako v tomto příkladu: 

   ![Nasměrujte svůj kanál Jenkinse ve skriptu](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Jakmile budete mít hotovo, vyberte **Uložit**.

V dalším kroku Sestavte a nasaďte aplikaci pro Azure App Service. 

## <a name="build-and-deploy-to-azure"></a>Sestavování a nasazování do Azure

1. Pomocí Azure CLI, a to buď z příkazového řádku nebo Azure Cloud Shell, vytvořte [Azure App Service webovou aplikaci v systému Linux](/azure/app-service/containers/app-service-linux-intro) , kde Jenkinse po dokončení sestavení nasadí vaši webovou aplikaci. Ujistěte se, že vaše webová aplikace má jedinečný název.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Další informace o těchto příkazech rozhraní příkazového řádku Azure najdete na těchto stránkách:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. V Jenkinse vyberte svoji úlohu kanálu a vyberte **sestavit hned**.

   Po dokončení sestavení Jenkinse nasadí vaši aplikaci, která je teď v Azure živá, na adrese URL publikace, například: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Zobrazení nasazené aplikace v Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Nasdílení změn a opětovné nasazení

1. Ve webovém prohlížeči přejdete do tohoto umístění ve větvi GitHubu vaší webové aplikace:

   `complete/src/main/java/Hello/Application.java`
   
1. V pravém horním rohu GitHubu vyberte **Upravit tento soubor**.

1. Proveďte tuto změnu na `commandLineRunner()` metodu a potvrďte změnu ve `master` větvi úložiště. Toto potvrzení ve `master` větvi spustí sestavení v Jenkinse. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Po dokončení sestavení a Jenkinse se znovu nasadí do Azure, aktualizujte svou aplikaci, ve které se teď zobrazí vaše aktualizace.

   ![Zobrazení nasazené aplikace v Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Řešení potíží s modulem plug-in Jenkinse

Pokud narazíte na chyby s moduly plug-in Jenkinse, zapište problém v [JENKINSE JIRA](https://issues.jenkins-ci.org/) pro konkrétní součást.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití virtuálních počítačů Azure jako agentů sestavení](/azure/jenkins/jenkins-azure-vm-agents)
