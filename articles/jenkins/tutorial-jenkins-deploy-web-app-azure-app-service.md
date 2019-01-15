---
title: Kurz – nasazení z Githubu do služby Azure App Service s využitím Jenkinse
description: Nastavení Jenkinse pro průběžnou integraci (CI) z webu GitHub a průběžného nasazování (CD) do služby Azure App Service pro webové aplikace v Javě
services: jenkins
ms.service: jenkins
author: tomarchermsft
ms.author: tarcher
manager: jeconnoc
ms.topic: tutorial
ms.date: 11/15/2018
ms.openlocfilehash: 3554151490215f128efc6725b7aaa0628d503366
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2019
ms.locfileid: "54260110"
---
# <a name="tutorial-deploy-from-github-to-azure-app-service-with-jenkins-continuous-integration-and-deployment"></a>Kurz: Nasazení z Githubu do služby Azure App Service pomocí Jenkinse průběžnou integraci a nasazování

V tomto kurzu se nasadí webová aplikace Java ukázky z Githubu do [Azure App Service v Linuxu](/azure/app-service/containers/app-service-linux-intro) nastavením průběžné integrace (CI) a průběžného nasazování (CD) Jenkinse. Když aktualizujete aplikaci formou potvrzení Githubu, Jenkins automaticky sestaví a znovu publikuje uzamkl vaší aplikace do služby Azure App Service. Ukázková aplikace v tomto kurzu byla vyvinuta pomocí [Spring Boot](http://projects.spring.io/spring-boot/) rozhraní framework. 

![Přehled](media/tutorial-jenkins-deploy-web-app-azure-app-service/overview.png)

V tomto kurzu dokončíte tyto úlohy:

> [!div class="checklist"]
> * Instalace Jenkinse moduly plug-in z Githubu, abyste mohli sestavit nasazení do služby Azure App Service a dalších souvisejících úloh.
> * Ukázkového úložiště Githubu rozvětvit, takže budete mít funkční kopie.
> * Jenkins připojení ke Githubu.
> * Vytvoření Azure instančního objektu služby, aby Jenkins měli přístup k Azure bez pomocí svých přihlašovacích údajů.
> * Přidání instančního objektu služby do Jenkinse.
> * Vytvoření kanálu Jenkins, který vytvoří a nasadí ukázkovou aplikaci pokaždé, když aktualizujete aplikaci ve službě GitHub.
> * Vytvoření sestavení a nasazení souborů pro kanálu Jenkins.
> * Nasměrujte kanálu Jenkins na skript sestavení a nasazení.
> * Nasazení ukázkové aplikace do Azure pomocí ruční sestavení.
> * Nabízené aktualizace aplikace v Githubu, který aktivuje Jenkinse k sestavení a opětovné nasazení do Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, budete potřebovat tyto položky:

* A [Jenkins](https://jenkins.io/) serveru pomocí nástroje Java Development Kit (JDK) a Maven nainstalovaný na virtuálním počítači Azure s Linuxem

  Pokud nemáte Jenkins server, proveďte tyto kroky teď na webu Azure portal: [Vytvoření serveru Jenkins na virtuálním počítači Azure s Linuxem](/azure/jenkins/install-jenkins-solution-template)

* A [Githubu](https://github.com) účtem, takže můžete získat pracovní kopie ([forku](#fork)) pro ukázkovou webovou aplikaci Java. 

* [Azure CLI](/cli/azure/install-azure-cli), který můžete spustit buď z místního příkazového řádku nebo [Azure Cloud Shell](/azure/cloud-shell/overview)

## <a name="install-jenkins-plug-ins"></a>Instalace modulů plug-in Jenkinse

1. Přihlaste se k webové konzole Jenkinse na tomto místě:

   `https://<Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`

1. Na hlavní stránce Jenkinse vyberte **spravovat Jenkins** > **Správa modulů plug-in**.

   ![Správa modulů plug-in Jenkinse](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-plugins.png)

1. Na **dostupné** kartu, vyberte tyto moduly plug-in:

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [Zdroj větve na Githubu](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [Injector prostředí modulu plug-in](https://plugins.jenkins.io/envinject)
   - [Azure Credentials](https://plugins.jenkins.io/azure-credentials)

   Pokud se tyto moduly plug-in nezobrazují, ujistěte se, že jste ještě není nainstalované kontrolou **nainstalováno** kartu.

1. Chcete-li nainstalovat vybrané modulů plug-in, zvolte **stáhnout a nainstalovat po restartování**.

1. Až budete hotovi, v nabídce Jenkinse vyberte **spravovat Jenkins** tak, aby se vrátit na stránku pro správu Jenkinse pro budoucí kroky.

## <a name="fork-sample-github-repo"></a>Vytvořit fork ukázkového úložiště GitHub

1. [Přihlaste se k úložišti Githubu pro ukázkovou aplikaci Spring Boot](https://github.com/spring-guides/gs-spring-boot). 

1. V pravém horním rohu v Githubu, zvolte **Forku**.

   ![Rozvětvení úložiště ukázky z Githubu](media/tutorial-jenkins-deploy-web-app-azure-app-service/fork-github-repo.png)

1. Postupujte podle pokynů dokončit větvení a vyberte účet GitHub.

Potom si nastavte Jenkinse pomocí svých přihlašovacích údajů Githubu.

## <a name="connect-jenkins-to-github"></a>Připojení ke Githubu Jenkinse

Chcete-li mít Jenkinse, Githubu monitorování a reakce při získání nové potvrzení vložení do webové aplikace ve vašem forku Githubu, povolit [webhooky Githubu](https://developer.github.com/webhooks/) v Jenkinsu.

> [!NOTE]
> 
> Tyto kroky vytvoří osobní přístupový token přihlašovacích údajů pro Jenkinse, aby fungoval s Githubem pomocí Githubu uživatelské jméno a heslo. 
> Pokud účet GitHub pomocí dvojúrovňového ověřování, ale vytvoření tokenu v Githubu a Jenkinse nastavit místo toho použít tento token. 
> Další informace najdete v tématu [modulu plug-in Jenkinse Githubu](https://wiki.jenkins.io/display/JENKINS/GitHub+Plugin) dokumentaci.

1. Z **spravovat Jenkins** stránce **konfigurovat systém**. 

   ![Konfigurace systému](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-jenkins-configure-system.png)

1. V **Githubu** části, zadejte podrobnosti pro váš server na Githubu. Z **přidat Server Githubu** seznamu vyberte **serveru GitHub**. 

   ![Přidání serveru GitHub](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-GitHub-server.png)

1. Pokud **spravovat háky** vlastnost není vybraná, vyberte tuto vlastnost. Zvolte **Upřesnit** umožňující vám zadat ostatní nastavení. 

   ![Zvolte možnost "Pokročilé" pro další nastavení](media/tutorial-jenkins-deploy-web-app-azure-app-service/advanced-GitHub-settings.png)

1. Z **spravovat další akce Githubu** seznamu vyberte **převést přihlašovací jméno a heslo na token**.

   ![Zvolte možnost "Spravovat další Githubu akce"](media/tutorial-jenkins-deploy-web-app-azure-app-service/manage-additional-actions.png)

1. Vyberte **z přihlašovací jméno a heslo** mohli zadat svoje uživatelské jméno v Githubu a heslo. Jakmile budete hotovi, zvolte **vytvořit token přihlašovacích údajů**, vytváří [Githubu osobní přístupový token PAT](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Vytvořte token PAT Githubu z přihlašovací jméno a heslo](media/tutorial-jenkins-deploy-web-app-azure-app-service/create-github-token-credentials.png)

1. V **serveru GitHub** části z **pověření** vyberte nový token. Zkontrolujte, že ověřování je funkční výběrem **Test připojení**.

   ![Zkontrolujte připojení k serveru Githubu s nový token PAT](media/tutorial-jenkins-deploy-web-app-azure-app-service/check-github-connection.png)

Dále vytvořte Azure instanční objekt používající Jenkins pro ověřování a přístup k prostředkům Azure.

## <a name="create-service-principal"></a>Vytvoření instančního objektu

V další části Vytvoření úlohy Jenkinse kanálu, který vytváří aplikace z Githubu a nasadí vaši aplikaci do služby Azure App Service. Mít přístup k Azure bez nutnosti zadávat svoje přihlašovací údaje, vytvořte Jenkinse [instanční objekt služby](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) ve službě Azure Active Directory pro Jenkinse. Instanční objekt služby je samostatný identita, která Jenkinse slouží pro ověřování přístupu k prostředkům Azure. Chcete-li vytvořit tento instanční objekt, spusťte příkaz rozhraní příkazového řádku Azure [ **`az ad sp create-for-rbac`** ](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest), buď z místního příkazového řádku nebo Azure Cloud Shell, například: 

```azurecli-interactive
az ad sp create-for-rbac --name "yourAzureServicePrincipalName" --password yourSecurePassword
```

Nezapomeňte že použít uvozovky kolem hlavní název služby. Také vytvořit silné heslo podle [pravidla pro hesla Azure Active Directory a omezení](/azure/active-directory/active-directory-passwords-policy). Pokud heslo nezadáte, rozhraní příkazového řádku Azure pro vás vytvoří heslo. 

Zde je výstup generovaný **`create-for-rbac`** příkaz: 

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
> Pokud již máte instanční objekt služby, můžete využít tuto identitu.
> Při zadávání hodnoty instančního objektu služby pro ověřování, použijte `appId`, `password`, a `tenant` hodnot vlastností. 
> Při vyhledávání existujícího instančního objektu, použijte `displayName` hodnotu vlastnosti.

## <a name="add-service-principal-to-jenkins"></a>Přidání instančního objektu s Jenkinsem

1. Na hlavní stránce Jenkinse vyberte **pověření** > **systému**. 

1. Na **systému** stránce v části **domény**vyberte **globální přihlašovací údaje (neomezená)**.

1. V nabídce vlevo vyberte **přidat přihlašovací údaje**.

1. Z **druh** seznamu vyberte **instančního objektu Azure**.

1. Zadejte informace pro vaše předplatné služby instančního objektu a Azure ve vlastnostech popsaného v tabulce v tomto kroku:

   ![Přidat přihlašovací údaje instančního objektu služby Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/add-service-principal-credentials.png)

   | Vlastnost | Hodnota | Popis | 
   |----------|-------|-------------| 
   | **ID předplatného** | <*yourAzureSubscription-ID*> | Hodnota identifikátoru GUID pro vaše předplatné Azure <p>**Tip**: Pokud si nejste jisti ID vašeho předplatného Azure, spusťte tento příkaz rozhraní příkazového řádku Azure z příkazového řádku nebo ve službě Cloud Shell a potom použijte `id` hodnota identifikátoru GUID: <p>`az account list` | 
   | **ID klienta** | <*yourAzureServicePrincipal-ID*> | `appId` Hodnota identifikátoru GUID dříve generovány instančního objektu služby Azure | 
   | **Tajný klíč klienta** | <*yourSecurePassword*> | `password` Hodnotu "tajný klíč" můžete zadat nebo instančního objektu služby Azure | 
   | **ID tenanta** | <*yourAzureActiveDirectoryTenant-ID*> | `tenant` Hodnota identifikátoru GUID pro vašeho tenanta Azure Active Directory | 
   | **ID** | <*yourAzureServicePrincipalName*> | `displayName` Hodnotu instančního objektu služby Azure | 
   |||| 

1. Chcete-li ověřit, že funguje instančního objektu služby, zvolte **ověřte instanční objekt služby**. Jakmile budete hotovi, zvolte **OK**.

Dále vytvořte kanál Jenkinse, který vytvoří a nasadí vaši aplikaci.

## <a name="create-jenkins-pipeline"></a>Vytvoření kanálu Jenkinse

V Jenkinsovi se vytvořte úlohu kanálu pro sestavování a nasazení vaší aplikace.

1. Vraťte se na domovskou stránku Jenkins a vyberte **nová položka**. 

   ![Vyberte "Nové položky"](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-new-item.png)

1. Zadejte název pro vaši úlohu kanálu, například "My-Java-Web-App" a vyberte **kanálu**. V dolní části, zvolte **OK**.  

   ![Vyberte "Kanál"](media/tutorial-jenkins-deploy-web-app-azure-app-service/jenkins-select-pipeline.png)

1. Nastavení Jenkinse pomocí instančního objektu služby, Jenkins můžete nasadit do Azure bez použití vlastní přihlašovací údaje.

   1. Na **Obecné** kartu, vyberte možnost **Příprava prostředí pro běh**. 

   1. V **vlastnosti obsahu** pole, které se zobrazí, přidejte tyto proměnné prostředí a jejich hodnoty. 

      ```text
      AZURE_CRED_ID=yourAzureServicePrincipalName
      RES_GROUP=yourWebAppAzureResourceGroupName
      WEB_APP=yourWebAppName
      ```

      ![Vyberte "Příprava prostředí pro běh" a nastavení proměnných prostředí](media/tutorial-jenkins-deploy-web-app-azure-app-service/prepare-environment-for-run.png)

1. Jakmile budete hotoví, vyberte **Uložit**.

Dále vytvořte skripty pro sestavení a nasazení jenkinse.

## <a name="create-build-and-deployment-files"></a>Vytvoření souborů sestavení a nasazení

Teď vytvořte soubory, které Jenkins používá pro vytváření a nasazení vaší aplikace.

1. Ve vašem forku Githubu `src/main/resources/` složku, vytvořte tento konfigurační soubor aplikace s názvem `web.config`, který obsahuje tento kód XML, ale nahraďte `$(JAR_FILE_NAME)` s `gs-spring-boot-0.1.0.jar`:

   ```xml
   <?xml version="1.0" encoding="UTF-8">
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. V kořenové složce vašeho forku Githubu, vytvořte tento skript sestavení a nasazení s názvem `Jenkinsfile`, který obsahuje tento text ([zdroje na Githubu tady](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```text  
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

1. Potvrdit obě `web.config` a `Jenkinsfile` soubory do vašeho Githubu vytvořit fork a odešlete své změny.

## <a name="point-pipeline-at-script"></a>Bod kanálu na skript

Teď zadejte skript sestavení a nasazení má Jenkinse k použití.

1. Jenkins vyberte úlohu dříve vytvořený kanál. 

   ![Vyberte kanál úlohy pro vaši webovou aplikaci](media/tutorial-jenkins-deploy-web-app-azure-app-service/select-pipeline-job.png)

1. V nabídce vlevo vyberte **konfigurovat**.

1. Na **kanálu** kartě z **definice** seznamu vyberte **kanálu skriptu ze Správce řízení služeb**.

   1. V **SCM** pole, která se zobrazí, vyberte **Git** jako vaší správy zdrojového kódu. 

   1. V **úložišť** části pro **adresa URL úložiště**, zadejte adresu URL vašeho forku Githubu, například: 

      `https://github.com/<your-GitHub-username>/gs-spring-boot`

   1. Pro **pověření**, vyberte dříve vytvořenou osobní přístupový token Githubu.

   1. V **cesta ke skriptu** pole, přidejte cestu ke skriptu "Souboru Jenkinsfile".

   Jakmile budete hotovi, vaše definice kanálu vypadá jako v tomto příkladu: 

   ![Bod kanálu na skript](media/tutorial-jenkins-deploy-web-app-azure-app-service/set-up-jenkins-github.png)

1. Jakmile budete hotoví, vyberte **Uložit**.

V dalším kroku sestavení a nasazení vaší aplikace do služby Azure App Service. 

## <a name="build-and-deploy-to-azure"></a>Sestavení a nasazení do Azure

1. Pomocí Azure CLI, buď z příkazového řádku nebo Azure Cloud Shell, vytvořte [webová aplikace Azure App Service v Linuxu](/azure/app-service/containers/app-service-linux-intro) kde Jenkins nasadí webové aplikace po dokončení sestavení. Ujistěte se, že vaše webová aplikace má jedinečný název.

   ```azurecli-interactive
   az group create --name yourWebAppAzureResourceGroupName --location yourAzureRegion
   az appservice plan create --name yourLinuxAppServicePlanName --resource-group yourWebAppAzureResourceGroupName --is-linux
   az webapp create --name yourWebAppName --resource-group yourWebAppAzureResourceGroupName --plan yourLinuxAppServicePlanName --runtime "java|1.8|Tomcat|8.5"
   ```

   Další informace o těchto příkazech rozhraní příkazového řádku Azure najdete v těchto stránek:

   * [**`az group create`**](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create)

   * [**`az appservice plan create`**](https://docs.microsoft.com/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create)

   * [**`az webapp create`**](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-create)

1. V Jenkinsovi se vyberte kanál úlohy a vyberte **Build Now**.

   Po dokončení sestavení Jenkins nasadí aplikaci, která je teď v provozu v Azure na adrese URL publikování, například: 

   `http://<your-Java-web-app>.azurewebsites.net`

   ![Zobrazení nasazené aplikace v Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-unedited.png)

## <a name="push-changes-and-redeploy"></a>Nasdílení změn a opětovné nasazení

1. Ve webovém prohlížeči přejděte na této pozici ve forku Githubu vaší webové aplikace:

   `complete/src/main/java/Hello/Application.java`
   
1. V pravém horním rohu v Githubu, zvolte **tento soubor upravit**.

1. Ujistěte se, aby se provedená změna `commandLineRunner()` metoda a potvrzení změn do příslušného úložiště `master` větve. Toto potvrzení v `master` větve v Jenkinsovi se spustí sestavení. 
   
   ```java
   System.out.println("Let's inspect the beans provided by Spring Boot on Azure");
   ```

1. Po dokončení sestavení a znovu nasadí Jenkinse do Azure, aktualizujte stránku aplikace, které se teď zobrazuje aktualizace.

   ![Zobrazení nasazené aplikace v Azure](media/tutorial-jenkins-deploy-web-app-azure-app-service/greetings-edited.png)

## <a name="troubleshooting-the-jenkins-plug-in"></a>Řešení potíží s modulu plug-in Jenkinse

Pokud narazíte na jakékoli chyby s moduly plug-in Jenkins, založte problém v [Jenkins JIRA](https://issues.jenkins-ci.org/) pro konkrétní komponentu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Použití virtuálních počítačů Azure jako agentů sestavení](/azure/jenkins/jenkins-azure-vm-agents)
