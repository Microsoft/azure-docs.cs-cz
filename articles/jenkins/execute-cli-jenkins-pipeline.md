---
title: Spuštění Azure CLI s využitím Jenkinse
description: Zjistěte, jak pomocí Azure CLI v kanálu Jenkinse nasadit webovou aplikaci v Javě do Azure.
ms.service: jenkins
keywords: jenkins, azure, devops, app service, cli
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 6/7/2017
ms.openlocfilehash: 05e21083b060e00602f58e1a17f52f40ced7d34e
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54075205"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Nasazení do Azure App Service pomocí Jenkinse a Azure CLI
K nasazení webové aplikace v Javě do Azure můžete použít Azure CLI v [kanálu Jenkinse](https://jenkins.io/doc/book/pipeline/). V tomto kurzu vytvoříte kanál CI/CD na virtuálním počítači Azure a také se naučíte:

> [!div class="checklist"]
> * Vytvořit virtuální počítač Jenkins
> * Konfigurace Jenkinse
> * Vytvoření webové aplikace v Azure
> * Příprava úložiště GitHub
> * Vytvoření kanálu Jenkinse
> * Spuštění kanálu a ověření webové aplikace

Tento kurz vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgradovat, přečtěte si článek [Instalace Azure CLI]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Vytvoření a konfigurace instance Jenkinse
Pokud ještě nemáte hlavní server Jenkinse, začněte [šablonou řešení](install-jenkins-solution-template.md), která ve výchozím nastavení zahrnuje požadovaný modul plug-in [Azure Credentials](https://plugins.jenkins.io/azure-credentials). 

Modul plug-in Azure Credentials umožňuje ukládat přihlašovací údaje instančních objektů Microsoft Azure v Jenkinsu. Ve verzi 1.2 jsme pro kanál Jenkinse přidali podporu získání přihlašovacích údajů Azure. 

Ujistěte se, že máte verzi 1.2 nebo novější:
* Na řídicím panelu Jenkinse klikněte na **Manage Jenkins -> Plugin Manager** (Správa Jenkinse -> Správa modulů plug-in) a vyhledejte **Azure Credential**. 
* Pokud máte starší verzi než 1.2, aktualizujte modul plug-in.

Na hlavním serveru Jenkinse se vyžaduje také sada Java JDK a Maven. Pokud je chcete nainstalovat, pomocí SSH se přihlaste k hlavnímu serveru Jenkinse a spusťte následující příkazy:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Přidání instančního objektu Azure do přihlašovacích údajů Jenkinse

Ke spuštění Azure CLI jsou potřeba přihlašovací údaje Azure.

* Na řídicím panelu Jenkinse klikněte na **Credentials -> System** (Přihlašovací údaje -> Systém). Klikněte na **Global credentials (unrestricted)** (Globální přihlašovací údaje (neomezené)).
* Klikněte na **Add Credentials** (Přidat přihlašovací údaje) a přidejte [instanční objekt Microsoft Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) tím, že vyplníte Subscription ID (ID předplatného), Client ID (ID klienta), Client Secret (Tajný klíč klienta) a OAuth 2.0 Token Endpoint (Koncový bod tokenu OAuth 2.0). Zadejte ID, které použijete v dalším kroku.

![Přidání přihlašovacích údajů](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Vytvoření služby Azure App Service pro nasazení webové aplikace v Javě

Pomocí příkazu [az appservice plan create](/cli/azure/appservice/plan#az-appservice-plan-create) rozhraní příkazového řádku vytvořte plán služby App Service s cenovou úrovní **FREE**. Plán služby App Service definuje fyzické prostředky používané k hostování vašich aplikací. Všechny aplikace přiřazené k plánu služby App Service sdílí tyto prostředky, a tím umožňují snížení nákladů při hostování více aplikací. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Jakmile bude plán připravený, v Azure CLI se zobrazí podobný výstup jako v následujícím příkladu:

```json
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "North Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "North Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  ...
  < Output has been truncated for readability >
} 
``` 

### <a name="create-an-azure-web-app"></a>Vytvoření webové aplikace Azure

 Ve službě Cloud Shell pomocí příkazu [az webapp create](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) rozhraní příkazového řádku vytvořte definici webové aplikace v plánu služby App Service `myAppServicePlan`. Definice webové aplikace poskytuje adresu URL pro přístup k aplikaci a konfiguruje několik možností pro nasazení kódu do Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Zástupný text `<app_name>` nahraďte vlastním jedinečným názvem aplikace. Tento jedinečný název je součástí výchozího názvu domény pro příslušnou webovou aplikaci, proto musí být mezi všemi aplikacemi v Azure jedinečný. Na webovou aplikaci můžete namapovat vlastní název domény, než ji zpřístupníte uživatelům.

Jakmile bude definice webové aplikace připravená, v Azure CLI se zobrazí podobné informace jako v následujícím příkladu: 

```json 
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "enabled": true,
   ...
  < Output has been truncated for readability >
}
```

### <a name="configure-java"></a>Konfigurace Javy 

Ve službě Cloud Shell pomocí příkazu [az appservice web config update](/cli/azure/webapp/config#az-appservice-web-config-update) nastavte konfiguraci modulu runtime Java podle potřeb vaší aplikace.

Následující příkaz nakonfiguruje spouštění webové aplikace na nejnovější sadě Java 8 JDK a [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Příprava úložiště GitHub
Otevřete úložiště s [jednoduchou webovou aplikace v Javě pro Azure](https://github.com/azure-devops/javawebappsample). Pokud chcete vytvořit fork úložiště do svého vlastního účtu GitHub, klikněte na tlačítko **Fork** (Vytvořit fork) v pravém horním rohu.

* Ve webovém uživatelském rozhraní GitHubu otevřete soubor **Jenkinsfile**. Klikněte na ikonu tužky a upravte tento soubor tak, že na řádcích 20 a 21 aktualizujete skupinu prostředků a název vaší webové aplikace.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Na řádku 23 aktualizujte ID přihlašovacích údajů ve vaší instanci Jenkinse.

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Vytvoření kanálu Jenkinse
Otevřete Jenkinse ve webovém prohlížeči a klikněte na **New Item** (Nová položka). 

* Zadejte název úlohy a vyberte **Pipeline** (Kanál). Klikněte na **OK**.
* Potom klikněte na kartu **Pipeline** (Kanál). 
* V části **Definition** (Definice) vyberte **Pipeline script from SCM** (Skript kanálu z SCM).
* V části **SCM** vyberte **Git**.
* Zadejte adresu URL vašeho forku úložiště GitHub: https:\<váš_fork_úložiště\>.git
* Klikněte na **Uložit**.

## <a name="test-your-pipeline"></a>Test kanálu
* Přejděte k vytvořenému kanálu a klikněte na **Build Now** (Sestavit).
* Během několika sekund by se mělo sestavení úspěšně dokončit. Pak můžete přejít k sestavení a kliknutím na **Console Output** (Výstup konzoly) zobrazit podrobnosti.

## <a name="verify-your-web-app"></a>Ověření webové aplikace
Pokud chcete ověřit úspěšné nasazení souboru WAR do vaší webové aplikace, otevřete webový prohlížeč:

* Přejděte na adresu http://&lt;název_aplikace>.azurewebsites.net/api/calculator/ping.  
Zobrazí se následující:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Přejděte na adresu http://&lt;název_aplikace>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (místo &lt;x> a &lt;y> zadejte libovolná čísla), kde se zobrazí součet hodnot x a y.

![Kalkulačka: součet](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Nasazení do služby Azure Web App on Linux
Teď, když víte, jak používat Azure CLI v kanálu Jenkinse, můžete skript upravit tak, aby provedl nasazení do služby Azure Web App on Linux.

Web App on Linux podporuje jiný způsob nasazení, který spočívá v použití Dockeru. K nasazení je potřeba určit soubor Dockerfile, který zabalí vaši webovou aplikaci s modulem runtime služby do image Dockeru. Modul plug-in pak image sestaví, odešle do registru Dockeru a nasadí do vaší webové aplikace.

* Podle [těchto](../app-service/containers/quickstart-nodejs.md) kroků vytvořte webovou aplikaci Azure spuštěnou v Linuxu.
* Nainstalujte do své instance Jenkinse Docker podle pokynů v tomto [článku](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Podle [těchto](/azure/container-registry/container-registry-get-started-azure-cli) kroků vytvořte na webu Azure Portal službu Container Registry.
* Ve svém forku úložiště s [jednoduchou webovou aplikací v Javě pro Azure](https://github.com/azure-devops/javawebappsample) upravte soubor **Jenkinsfile2**:
    * Na řádcích 18 až 21 aktualizujte názvy vaší skupiny prostředků, webové aplikace a služby ACR. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Na řádku 24 aktualizuje \<azsrvprincipal\> na vaše ID přihlašovacích údajů.
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Stejně jako při nasazování do webové aplikace ve Windows vytvořte nový kanál Jenkinse, jenom tentokrát použijte soubor **Jenkinsfile2**.
* Spusťte svou novou úlohu.
* Ověření provedete tak, že v Azure CLI spustíte:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Zobrazí se následující výsledek:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Přejděte na adresu http://&lt;název_aplikace>.azurewebsites.net/api/calculator/ping. Zobrazí se tato zpráva: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Přejděte na adresu http://&lt;název_aplikace>.azurewebsites.net/api/calculator/add?x=&lt;x>&y=&lt;y> (místo &lt;x> a &lt;y> zadejte libovolná čísla), kde se zobrazí součet hodnot x a y.
    
## <a name="next-steps"></a>Další postup
V tomto kurzu jste nakonfigurovali kanál Jenkinse, který rezervuje zdrojový kód v úložišti GitHub. Potom spustí Maven, který sestaví soubor war, a sestavený soubor pak pomocí Azure CLI nasadí do služby Azure App Service. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvořit virtuální počítač Jenkins
> * Konfigurace Jenkinse
> * Vytvoření webové aplikace v Azure
> * Příprava úložiště GitHub
> * Vytvoření kanálu Jenkinse
> * Spuštění kanálu a ověření webové aplikace
