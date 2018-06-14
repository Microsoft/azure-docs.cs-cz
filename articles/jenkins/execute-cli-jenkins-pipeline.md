---
title: Spuštění rozhraní Azure CLI s volaných | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku Azure k nasazení webové aplikace v jazyce Java do Azure v volaných kanálu
services: app-service\web
documentationcenter: ''
author: mlearned
manager: douge
editor: ''
ms.assetid: ''
ms.service: jenkins
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 6/7/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 2b568bd22858a42178e2821e0e97a3b4ebdfccd5
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28926926"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Nasazení do Azure App Service pomocí volaných a rozhraní příkazového řádku Azure CLI
Chcete-li nasadit webovou aplikaci Java do Azure, můžete použít rozhraní příkazového řádku Azure v [volaných kanálu](https://jenkins.io/doc/book/pipeline/). V tomto kurzu vytvoříte kanál CI/CD na virtuálním počítači Azure a také se naučíte:

> [!div class="checklist"]
> * Vytvořit virtuální počítač Jenkins
> * Konfigurace Jenkinse
> * Vytvoření webové aplikace v Azure
> * Příprava úložiště GitHub
> * Vytvoření kanálu volaných
> * Spusťte kanál a ověřte webovou aplikaci

Tento kurz vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Vytvořit a nakonfigurovat volaných instance
Pokud již nemáte volaných master, začínat [šablona řešení](install-jenkins-solution-template.md), což zahrnuje požadované [přihlašovací údaje Azure](https://plugins.jenkins.io/azure-credentials) modulu plug-in ve výchozím nastavení. 

Modul plug-in přihlašovacích údajů Azure umožňuje ukládání ve volaných hlavní přihlašovací údaje služby Microsoft Azure. Ve verzi 1.2 jsme doplnili podporu, tak, že volaných kanálu můžete získat přihlašovací údaje Azure. 

Ujistěte se, že máte verze 1.2 nebo vyšší:
* V rámci volaných řídicí panel, klikněte na **volaných spravovat -> modul plug-in Manager ->** a vyhledejte **přihlašovacích údajů Azure**. 
* Tento modul plug-in aktualizace, pokud je verze starší než 1.2.

V hlavním volaných také vyžadují Java JDK a Maven. Pokud chcete nainstalovat, přihlaste se k hlavní volaných pomocí protokolu SSH a spusťte následující příkazy:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Přidat objekt zabezpečení služby Azure k volaných pověření

Azure přihlašovacích údajů je potřeba provést rozhraní příkazového řádku Azure.

* V rámci volaných řídicí panel, klikněte na **pověření -> Systém ->**. Klikněte na tlačítko **globální credentials(unrestricted)**.
* Klikněte na tlačítko **přidat přihlašovací údaje** přidat [objektu služby Microsoft Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) tak, že vyplníte ID předplatného, ID klienta, sdílený tajný klíč klienta a koncový bod tokenu OAuth 2.0. Zadejte ID pro použití v následném kroku.

![Přidejte pověření](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Vytvoření Azure App Service pro nasazení webové aplikace Java

Vytvořit plán aplikační služby Azure s **volné** cenová úroveň pomocí [vytvořit plán aplikační služby az](/cli/azure/appservice/plan#az_appservice_plan_create) rozhraní příkazového řádku příkaz. Plán aplikační služby definuje fyzické prostředky, které jsou použity k hostování vaší aplikace. Všechny aplikace, které jsou přiřazené plán služby App Service sdílení těchto prostředků, což umožňuje uložit nákladů při hostování více aplikací. 

```azurecli-interactive
az appservice plan create \
    --name myAppServicePlan \ 
    --resource-group myResourceGroup \
    --sku FREE
```

Až bude plán připravena, rozhraní příkazového řádku Azure ukazuje podobné výstupu v následujícím příkladu:

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

 Použití [az webapp vytvořit](/cli/azure/webapp?view=azure-cli-latest#az_webapp_create) rozhraní příkazového řádku příkaz k vytvoření definice webové aplikace v `myAppServicePlan` plán služby App Service. Definice webové aplikace adresa URL pro přístup k vaší aplikace pomocí poskytuje a konfiguruje celou řadu možností pro nasazení kódu do Azure. 

```azurecli-interactive
az webapp create \
    --name <app_name> \ 
    --resource-group myResourceGroup \
    --plan myAppServicePlan
```

Nahraďte `<app_name>` zástupný symbol vlastní jedinečným názvem aplikace. Tento jedinečný název je součástí výchozí název domény pro webovou aplikaci, tak název musí být jedinečný v rámci všech aplikací v Azure. Můžete namapovat zadání názvu vlastní domény do webové aplikace ještě před zveřejněním pro vaše uživatele.

Při definici webové aplikace je připraven, rozhraní příkazového řádku Azure uvádí informace podobně jako v následujícím příkladu: 

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

### <a name="configure-java"></a>Konfigurace Java 

Nastavení konfigurace modulu runtime Java, která vaše aplikace, musí se [aktualizace konfigurace webové služby App Service az](/cli/azure/appservice/web/config#az_appservice_web_config_update) příkaz.

Následující příkaz nakonfiguruje webové aplikace ke spuštění na poslední JDK 8 Java a [Apache Tomcat](http://tomcat.apache.org/) 8.0.

```azurecli-interactive
az webapp config set \ 
    --name <app_name> \
    --resource-group myResourceGroup \ 
    --java-version 1.8 \ 
    --java-container Tomcat \
    --java-container-version 8.0
```

## <a name="prepare-a-github-repository"></a>Příprava úložiště GitHub
Otevřete [jednoduché webové aplikace Java pro Azure](https://github.com/azure-devops/javawebappsample) úložišti. Chcete-li rozvětvit úložiště k účtu GitHub, klikněte **rozvětvení** tlačítko v horním pravém rohu.

* V Githubu webového uživatelského rozhraní, otevřete **Jenkinsfile** souboru. Klikněte na ikonu tužky upravit tento soubor se aktualizují skupinu prostředků a název webové aplikace na řádku 20 a 21 v uvedeném pořadí.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Změňte řádek 23 aktualizovat ID pověření v instanci volaných

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Vytvoření kanálu volaných
Otevřete volaných ve webovém prohlížeči, klikněte na **novou položku**. 

* Zadejte název pro úlohy, vyberte **kanálu**. Klikněte na **OK**.
* Klikněte **kanálu** kartě Další. 
* Pro **definice**, vyberte **kanálu skript z SCM**.
* Pro **SCM**, vyberte **Git**.
* Zadejte adresu URL webu GitHub pro vaše forked úložišti: https:\<vaše forked úložišti\>.git
* Klikněte na tlačítko **uložit**

## <a name="test-your-pipeline"></a>Test kanálu
* Přejděte do kanálu, který jste vytvořili, klikněte na tlačítko **nyní sestavení**
* Sestavení uspěli za několik sekund, a můžete přejít do sestavení a klikněte na tlačítko **výstup konzoly** a zobrazit podrobnosti

## <a name="verify-your-web-app"></a>Ověření webové aplikace
Chcete-li ověřit WAR souboru úspěšně nasazena do vaší webové aplikace. Otevřete webový prohlížeč:

* Přejděte na http://&lt;app_name >.azurewebsites.net/api/calculator/ping  
Zobrazí:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Přejděte na http://&lt;app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (Nahraďte &lt;x > a &lt;y > s všechna čísla) Chcete-li získat součet hodnot x a y

![Kalkulačky: Přidat](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Nasazení do Azure webové aplikace v systému Linux
Teď, když víte, jak používat rozhraní příkazového řádku Azure v svůj volaných kanál, můžete upravit skript, který chcete nasadit do webové aplikace Azure v systému Linux.

Webové aplikace v systému Linux podporuje jiný způsob, jak provést nasazení, který má používat Docker. Pokud chcete nasadit, musíte zadat soubor Docker, který balíčky vaší webové aplikace s běh služby do bitové kopie Docker. Tento modul plug-in pak vytvořit bitovou kopii, poslat ho přímo Docker registru a nasazení bitové kopie do vaší webové aplikace.

* Postupujte podle kroků [sem](../app-service/containers/quickstart-nodejs.md) k vytvoření webové aplikace Azure systémem Linux.
* Podle pokynů v této instalaci Docker ve vaší instanci volaných [článku](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Vytvoření kontejneru registru na portálu Azure pomocí kroků [zde](/azure/container-registry/container-registry-get-started-azure-cli).
* Ve stejném [jednoduché webové aplikace Java pro Azure](https://github.com/azure-devops/javawebappsample) forked úložišti, upravit **Jenkinsfile2** souboru:
    * Řádek 18 21, aktualizujte na názvy skupiny prostředků, webové aplikace a ACR v uvedeném pořadí. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Řádek 24, aktualizace \<azsrvprincipal\> na vaše ID přihlašovacích údajů
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Vytvořit nový kanál volaných, protože při nasazení do Azure webové aplikace v systému Windows, pouze v tomto případě, použijete **Jenkinsfile2** místo.
* Spustíte novou úlohu.
* Pokud chcete ověřit, v Azure CLI, spusťte příkaz:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Získáte následující výsledek:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Přejděte na http://&lt;app_name >.azurewebsites.net/api/calculator/ping. Zobrazí se zpráva: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Přejděte na http://&lt;app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (Nahraďte &lt;x > a &lt;y > s všechna čísla) Chcete-li získat součet hodnot x a y
    
## <a name="next-steps"></a>Další postup
V tomto kurzu jste nakonfigurovali volaných kanál, který rezervuje zdrojový kód v úložišti GitHub. Spustí Maven k sestavení souboru war a potom pomocí rozhraní příkazového řádku Azure k nasazení do Azure App Service. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvořit virtuální počítač Jenkins
> * Konfigurace Jenkinse
> * Vytvoření webové aplikace v Azure
> * Příprava úložiště GitHub
> * Vytvoření kanálu volaných
> * Spusťte kanál a ověřte webovou aplikaci
