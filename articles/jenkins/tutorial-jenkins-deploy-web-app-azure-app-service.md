---
title: 'Kurz: Nasazení z GitHubu do služby Azure App Service s Jenkinsem'
description: Nastavení Jenkinse pro průběžnou integraci (CI) z GitHubu a průběžného nasazení (CD) do webových aplikací Azure App Service pro Java
ms.topic: tutorial
ms.date: 10/23/2019
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 9fcf178b71ac1f07bfb58cd2502701ae5392b472
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74158405"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Kurz: Nasazení z GitHubu do služby Azure App Service s nepřetržitou integrací a nasazením Jenkinse

Tento kurz nasazuje ukázkovou webovou aplikaci Java z GitHubu do [služby Azure App Service na Linuxu](/azure/app-service/containers/app-service-linux-intro) nastavením průběžné integrace (CI) a průběžného nasazování (CD) v Jenkinsi. Když aktualizujete aplikaci odesláním potvrzení na GitHub, Jenkins automaticky vytvoří a znovu publikuje vaši aplikaci do služby Azure App Service. Ukázková aplikace v tomto kurzu byla vyvinuta pomocí rozhraní [Spring Boot](https://projects.spring.io/spring-boot/) Framework. 

![Přehled nasazení služby GitHub to Azure App Service](media/tutorial-jenkins-deploy-web-app-azure-app-service/azure-continuous-integration-deployment-overview.png)

V tomto kurzu dokončíte tyto úkoly:

> [!div class="checklist"]
> * Nainstalujte moduly plug-in Jenkins, abyste mohli vytvářet z GitHubu, nasazovat se do služby Azure App Service a další související úkoly.
> * Rozvláčku ukázkové úložiště GitHub, takže máte pracovní kopii.
> * Připojte Jenkinse k GitHubu.
> * Vytvořte instanční objekt Azure, aby jenkins měl přístup k Azure bez použití vašich přihlašovacích údajů.
> * Přidejte svůj instanční objekt do Jenkinse.
> * Vytvořte kanál Jenkins, který vytvoří a nasadí ukázkovou aplikaci pokaždé, když aktualizujete aplikaci v GitHubu.
> * Vytvořte soubory sestavení a nasazení pro váš kanál Jenkinse.
> * Namiřte svůj kanál Jenkinse na skript sestavení a nasazení.
> * Nasadíte ukázkovou aplikaci do Azure spuštěním ručního sestavení.
> * Nabízená aktualizace aplikace na GitHubu, která aktivuje Jenkinse k sestavení a opětovnému nasazení do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete tyto položky:

* Server [Jenkins](https://jenkins.io/) s nástrojem Java Development Kit (JDK) a Maven nainstalovaným na virtuálním počítači Azure Linux

  Pokud nemáte server Jenkins, proveďte tyto kroky na webu Azure Portal: [Vytvoření serveru Jenkins evi.](/azure/jenkins/install-jenkins-solution-template)

* Účet [GitHub,](https://github.com) takže můžete získat pracovní kopii (roztouš) pro ukázkovou webovou aplikaci Java. 

* [Azure CLI](/cli/azure/install-azure-cli), které můžete spustit z místního příkazového řádku nebo [Azure Cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>Instalace modulů plug-in Jenkinse

1. Přihlaste se k webové konzoli Jenkins v tomto umístění:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Na hlavní stránce Jenkinse vyberte Spravovat**moduly plug-in** **Jenkins** > .

   ![Správa modulů plug-in Jenkins](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. Na kartě **Dostupné** vyberte tyto moduly plug-in:

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [Zdroj větve GitHub](https://plugins.jenkins.io/github-branch-source)
   - [Modul plug-inktoru prostředí](https://plugins.jenkins.io/envinject) Jenkins
   - [Přihlašovací údaje Azure](https://plugins.jenkins.io/azure-credentials)

   Pokud se tyto moduly plug-in nezobrazí, zkontrolujte kartu **Nainstalováno.**

1. Chcete-li nainstalovat vybrané moduly plug-in, vyberte **možnost Stáhnout a nainstalovat po restartování**.

1. Až budete hotovi, v nabídce Jenkins vyberte **Spravovat Jenkinse,** abyste se vrátili na stránku správy Jenkinse pro budoucí kroky.

## <a name="fork-sample-github-repo"></a>Ukázka popředí úložiště GitHub

1. [Přihlaste se k úložišti GitHub pro ukázkovou aplikaci Spring Boot](https://github.com/spring-guides/gs-spring-boot). 

1. V pravém horním rohu githubu vyberte **Fork**.

   ![Úložiště ukázkové repo vidce z GitHubu](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Podle pokynů vyberte svůj účet GitHub a dokončete rukopis.

Dále nastavte Jenkins e-ruce s přihlašovacími údaji GitHubu.

## <a name="connect-jenkins-to-github"></a>Připojení Jenkinse k GitHubu

Chcete-li, aby Jenkins monitoroval GitHub a reagoval, když se nové revize dostanou do vaší webové aplikace ve vaší rozvlastnění GitHub, povolte [webové háčky GitHub](https://developer.github.com/webhooks/) u Jenkinse.

> [!NOTE]
> 
> Tyto kroky vytvoří osobní přihlašovací údaje tokenu přístupu pro Jenkinse pro práci s GitHub pomocí uživatelského jména a hesla GitHubu. 
> Pokud ale váš účet GitHub používá dvoufaktorové ověřování, vytvořte si token v GitHubu a nastavte Jenkinse, aby místo toho používal tento token. 
> Další informace najdete v dokumentaci [k modulu plug-in Jenkins GitHub.](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin)

1. Na stránce **Spravovat Jenkinsvyberte** **Konfigurovat systém**. 

   ![Konfigurace systému v Jenkinsi](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. V části **GitHub** poskytněte podrobnosti o serveru GitHub. V seznamu **Přidat server GitHub** vyberte **GitHub Server**. 

   ![Přidání serveru GitHub v Jenkinsi](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Pokud není vybraná vlastnost **Spravovat háčky,** vyberte tuto vlastnost. Vyberte **Upřesnit,** abyste mohli určit další nastavení. 

   ![Určení upřesňujících nastavení Jenkinse pro githubový server](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. V seznamu **Spravovat další akce GitHubu** vyberte **Převést přihlašovací jméno a heslo na token**.

   ![Převod přihlašovacího jména a hesla na token pro GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Vyberte **Z přihlašovacího jména a hesla,** abyste mohli zadat své uživatelské jméno a heslo GitHubu. Až budete hotovi, vyberte **Vytvořit pověření tokenu**, který vytvoří [github osobní přístupový token (PAT)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Vytvoření tokenu PAT GitHubu z přihlašovacího jména a hesla](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. V části **GitHub Server** vyberte ze seznamu **přihlašovacích údajů** nový token. Zkontrolujte, zda ověřování funguje, a to tak, že zvolíte **Testovat připojení**.

   ![Kontrola připojení k serveru GitHub pomocí nového PAT](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Dále vytvořte instanční objekt Azure, který Jenkins používá pro ověřování a přístup k prostředkům Azure.

## <a name="create-service-principal"></a>Vytvoření instančního objektu

V novější části vytvoříte úlohu kanálu Jenkinse, která vytvoří vaši aplikaci z GitHubu a nasadí vaši aplikaci do služby Azure App Service. Pokud chcete, aby Jenkins měl přístup k Azure bez zadávání přihlašovacích údajů, vytvořte [instanční objekt](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) ve službě Azure Active Directory pro Jenkinse. Instanční objekt je samostatná identita, kterou jenkins může použít k ověřování přístupu k prostředkům Azure. Chcete-li vytvořit tento instanční [**`az ad sp create-for-rbac`**](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest)objekt, spusťte příkaz Azure CLI , například z místního příkazového řádku nebo z prostředí Azure Cloud Shell: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Ujistěte se, že používáte uvozovky kolem názvu hlavního serveru servisu. Vytvořte také silné heslo založené na [pravidlech a omezeních hesla služby Azure Active Directory](/azure/active-directory/active-directory-passwords-policy). Pokud heslo nezadáte, azure cli vytvoří heslo pro vás. 

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
> Pokud již máte instanční objekt, můžete tuto identitu místo toho znovu použít.
> Při poskytování hodnot instančního `appId` `password`objektu `tenant` pro ověřování použijte hodnoty , a vlastnosti. 
> Při hledání existujícího instančního objektu použijte hodnotu vlastnosti. `displayName`

## <a name="add-service-principal-to-jenkins"></a>Přidání instančního objektu do Jenkinse

1. Na hlavní stránce Jenkinse vyberte**systém** **pověření** > . 

1. Na stránce **Systém** vyberte v části **Doména** **globální pověření (bez omezení).**

1. V levé nabídce vyberte **Přidat pověření**.

1. V seznamu **Druh** vyberte **Azure Service Principal**.

1. Zadejte informace o instančním objektu a předplatném Azure ve vlastnostech popsaných v tabulce v tomto kroku:

   ![Přidání přihlašovacích údajů k primárnímu objektu služby Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------| 
   | **ID předplatného** | <*vaše AzureSubscription-ID*> | Hodnota GUID pro vaše předplatné Azure <p>**Tip:** Pokud neznáte ID předplatného Azure, spusťte tento příkaz Azure CLI z příkazového `id` řádku nebo v prostředí Cloud Shell a pak použijte hodnotu GUID: <p>`az account list` | 
   | **ID klienta** | <*vaše AzureServicePrincipal-ID*> | Hodnota `appId` GUID dříve vygenerovaná pro váš instanční objekt Azure | 
   | **Tajný klíč klienta** | <*yourSecurePassword*> | Hodnota `password` nebo "tajný", který jste zadali pro váš hlavní povinný služeb Azure | 
   | **ID tenanta** | <*vaše AzureActiveDirectoryTenant-ID*> | Hodnota `tenant` GUID pro vašeho klienta Služby Azure Active Directory | 
   | **Id** | <*vaše AzureServicePrincipalName*> | Hodnota `displayName` pro váš primární služby Azure | 

1. Chcete-li ověřit, zda váš instanční objekt funguje, vyberte **možnost Ověřit instanční objekt**. Až to bude hotové, vyberte **OK**.

Dále vytvořte kanál Jenkinse, který vytvoří a nasadí vaši aplikaci.

## <a name="create-jenkins-pipeline"></a>Vytvoření kanálu Jenkinse

V Jenkinsi vytvořte úlohu kanálu pro vytváření a nasazování aplikace.

1. Vraťte se na domovskou stránku Jenkinse a vyberte **Nová položka**. 

   ![Vytvoření kanálu Jenkinse](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Zadejte název úlohy kanálu, například "My-Java-Web-App", a vyberte **Pipeline**. V dolní části vyberte **OK**.  

   ![Pojmenujte úlohu kanálu Jenkinse](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Nastavte Jenkinse s vaším instančním objektem, aby jenkins mohl nasadit do Azure bez použití vlastních přihlašovacích údajů.

   1. Na kartě **Obecné** vyberte **Připravit prostředí pro spuštění**. 

   1. Do zobrazeného pole **Obsah vlastností** přidejte tyto proměnné prostředí a jejich hodnoty. 

      ```ini
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Příprava prostředí pro spuštění a nastavení proměnných prostředí](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-jenkins-run.png)

1. Jakmile budete mít hotovo, vyberte **Uložit**.

Dále vytvořte skripty sestavení a nasazení pro Jenkinse.

## <a name="create-build-and-deployment-files"></a>Vytváření souborů sestavení a nasazení

Teď vytvořte soubory, které Jenkins používá k vytváření a nasazování vaší aplikace.

1. Ve `src/main/resources/` složce fork GitHub vytvořte tento `web.config`konfigurační soubor `$(JAR_FILE_NAME)` aplikace `gs-spring-boot-0.1.0.jar`s názvem , který obsahuje tento XML, ale nahradí :

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

1. V kořenové složce githubu vytvořte tento skript `Jenkinsfile`sestavení a nasazení s názvem , který obsahuje tento text[(zdroj v GitHubu zde):](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)

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

1. Posuňte obě `web.config` a `Jenkinsfile` soubory do rozbočovače GitHub a odsuňte změny.

## <a name="point-pipeline-at-script"></a>Bodový kanál ve skriptu

Nyní zadejte skript sestavení a nasazení, který má Jenkins použít.

1. V jenkinsi vyberte dříve vytvořenou úlohu kanálu. 

   ![Vyberte úlohu jenkinsového kanálu pro webovou aplikaci.](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. V levé nabídce vyberte **Konfigurovat**.

1. Na kartě **Pipeline** vyberte ze seznamu **Definice** **skript kanálu z scm**.

   1. V poli **SCM,** které se zobrazí, vyberte Jako ovládací prvek zdroj **Git.** 

   1. V části **Repozitáře** pro **adresu URL úložiště**zadejte adresu URL vaší rozbočovače GitHub, například: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. V **případě přihlašovacích údajů**vyberte dříve vytvořený token osobního přístupu GitHubu.

   1. V poli **Cesta skriptu** přidejte cestu do skriptu Jenkinsfile.

   Po dokončení definice kanálu vypadá jako tento příklad: 

   ![Namiřte svůj Jenkinsův kanál na skript](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Jakmile budete mít hotovo, vyberte **Uložit**.

Dále vytvořte a nasaďte aplikaci do služby Azure App Service. 

## <a name="build-and-deploy-to-azure"></a>Vytváření a nasazování do Azure

1. Pomocí azure cli, ať už z příkazového řádku nebo Azure Cloud Shell, vytvořte [webovou aplikaci Azure App Service na Linuxu,](/azure/app-service/containers/app-service-linux-intro) kde Jenkins nasadí vaši webovou aplikaci po dokončení sestavení. Ujistěte se, že vaše webová aplikace má jedinečný název.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Další informace o těchto příkazech Azure CLI najdete na těchto stránkách:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. V Jenkinsi vyberte úlohu kanálu a vyberte **Sestavit .**

   Po dokončení sestavení Jenkins nasadí vaši aplikaci, která je teď v Azure na adrese URL publikace, například: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Zobrazení nasazené aplikace v Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Nasdílení změn a opětovné nasazení

1. Ve webovém prohlížeči přejděte do tohoto umístění ve vidliči GitHubu webové aplikace:

   `complete/src/main/java/Hello/Application.java`
   
1. V pravém horním rohu githubu vyberte **Upravit tento soubor**.

1. Proveďte tuto `commandLineRunner()` změnu metody a potvrďte změnu `master` větev repo. Tento závazek `master` ve větvi spustí sestavení v Jenkinsi. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Po dokončení sestavení a Jenkins znovu nasadí do Azure, aktualizujte aplikaci, která teď zobrazuje vaši aktualizaci.

   ![Zobrazení nasazené aplikace v Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Poradce při potížích s modulem plug-in Jenkins

Pokud narazíte na nějaké chyby s moduly plug-in Jenkins, soubor problém v [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní součást.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Použití virtuálních počítačů Azure jako agentů sestavení](/azure/jenkins/jenkins-azure-vm-agents)
