---
title: Spuštění rozhraní příkazového řádku Azure pomocí Jenkinse | Dokumentace Microsoftu
description: Další informace o použití Azure CLI k nasazení webové aplikace v Javě do Azure v kanálu Jenkins
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
ms.openlocfilehash: 1796e9f76e39334c8bbdd03463a0f91e9b47cb17
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39421300"
---
# <a name="deploy-to-azure-app-service-with-jenkins-and-the-azure-cli"></a>Nasazení do Azure App Service pomocí Jenkinse a Azure CLI
Pokud chcete nasadit webové aplikace v Javě do Azure, můžete použít rozhraní příkazového řádku Azure v [kanálu Jenkins](https://jenkins.io/doc/book/pipeline/). V tomto kurzu vytvoříte kanál CI/CD na virtuálním počítači Azure a také se naučíte:

> [!div class="checklist"]
> * Vytvořit virtuální počítač Jenkins
> * Konfigurace Jenkinse
> * Vytvoření webové aplikace v Azure
> * Příprava úložiště GitHub
> * Vytvoření kanálu Jenkins
> * Spuštění kanálu a ověřte webovou aplikaci

Tento kurz vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-configure-jenkins-instance"></a>Vytvořit a konfigurovat Jenkinse instance
Pokud ještě nemáte hlavní server Jenkinse, začněte tématem [šablonu řešení](install-jenkins-solution-template.md), který obsahuje požadované [přihlašovací údaje Azure](https://plugins.jenkins.io/azure-credentials) modulu plug-in ve výchozím nastavení. 

Modul plug-in Azure Credential umožňuje ukládat přihlašovací údaje instančního objektu služby Microsoft Azure v Jenkinsu. Ve verzi 1.2 přidali jsme podporu kanálu Jenkins získali přihlašovací údaje Azure. 

Ujistěte se, že máte verze 1.2 nebo vyšší:
* V řídicím panelu Jenkinse klikněte **Jenkins spravovat -> Správce modul plug-in ->** a vyhledejte **přihlašovacích údajů Azure**. 
* Modul plug-in aktualizujte, pokud je verze starší než 1.2.

Java JDK a Maven jsou také nutné na hlavním serveru Jenkinse. Pokud chcete nainstalovat, připojte se k hlavní server Jenkinse pomocí protokolu SSH a spusťte následující příkazy:
```bash
sudo apt-get install -y openjdk-7-jdk
sudo apt-get install -y maven
```

## <a name="add-azure-service-principal-to-jenkins-credential"></a>Přidání instančního objektu Azure Jenkins přihlašovacích údajů

Azure přihlašovací údaje, je potřeba provést rozhraní příkazového řádku Azure.

* V řídicím panelu Jenkinse klikněte **pověření -> Systém ->**. Klikněte na tlačítko **globální credentials(unrestricted)**.
* Klikněte na tlačítko **přidat přihlašovací údaje** přidáte [instanční objekt služby Microsoft Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json) vyplněním ID předplatného, ID klienta, tajného klíče klienta a koncový bod tokenu OAuth 2.0. Zadejte ID pro použití v následném kroku.

![Přidání přihlašovacích údajů](./media/execute-cli-jenkins-pipeline/add-credentials.png)

## <a name="create-an-azure-app-service-for-deploying-the-java-web-app"></a>Vytvoření služby Azure App Service pro nasazení webové aplikace v Javě

Vytvoření plánu služby Azure App Service s **FREE** cenové úrovně pomocí [vytvořit plán služby App Service az](/cli/azure/appservice/plan#az-appservice-plan-create) příkazu rozhraní příkazového řádku. Plán služby App Service definuje fyzické prostředky používané k hostování vašich aplikací. Všechny aplikace přiřazené k plánu služby App Service sdílí tyto prostředky, a tím umožňují snížení nákladů při hostování více aplikací. 

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

 Použití [az webapp vytvořit](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) příkazu rozhraní příkazového řádku vytvořte definici webové aplikace v `myAppServicePlan` plán služby App Service. Definice webové aplikace poskytuje adresu URL pro přístup k aplikaci a konfiguruje několik možností pro nasazení kódu do Azure. 

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

Nastavte konfiguraci modulu runtime Java podle potřeb vaší aplikace s [az appservice web config update](/cli/azure/appservice/web/config#az-appservice-web-config-update) příkazu.

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
Otevřít [jednoduché webové aplikace v Javě pro Azure](https://github.com/azure-devops/javawebappsample) úložiště. Chcete-li vytvořit fork úložiště do vlastního účtu Githubu, klikněte na tlačítko **Forku** tlačítko v pravém horním rohu.

* V Githubu webového uživatelského rozhraní, otevřete **souboru Jenkinsfile** souboru. Klikněte na ikonu tužky a upravte tento soubor aktualizovat skupinu prostředků a název vaší webové aplikace na řádku 20 a 21 v uvedeném pořadí.

```java
def resourceGroup = '<myResourceGroup>'
def webAppName = '<app_name>'
```

* Změňte řádek 23 aktualizovat ID přihlašovacích údajů do instance Jenkinse

```java
withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
```

## <a name="create-jenkins-pipeline"></a>Vytvoření kanálu Jenkins
Otevřete Jenkinse ve webovém prohlížeči, klikněte na tlačítko **nová položka**. 

* Zadejte název projektu a vyberte **kanálu**. Klikněte na **OK**.
* Klikněte na tlačítko **kanálu** další kartu. 
* Pro **definice**vyberte **kanálu skriptu ze Správce řízení služeb**.
* Pro **SCM**vyberte **Git**.
* Zadejte adresu URL GitHub vašeho rozvětveného úložiště: https:\<vašeho rozvětveného úložiště\>.git
* Klikněte na **Uložit**.

## <a name="test-your-pipeline"></a>Test kanálu
* Přejděte do kanálu, který jste vytvořili, klikněte na tlačítko **Build Now**
* Sestavení uspěli během několika sekund, a můžete se vrátit k sestavení a klikněte na tlačítko **výstup na konzole** a zobrazit podrobnosti

## <a name="verify-your-web-app"></a>Ověřte svou webovou aplikaci
Chcete-li ověřit WAR soubor úspěšně nasadil do webové aplikace. Otevřete webový prohlížeč:

* Přejděte na http://&lt;app_name >.azurewebsites.net/api/calculator/ping  
Zobrazí:

        Welcome to Java Web App!!! This is updated!
        Sun Jun 17 16:39:10 UTC 2017

* Přejděte na http://&lt;app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (Nahraďte &lt;x > a &lt;y > s všechna čísla) zobrazíte součet hodnot x a y

![Kalkulačka: Přidat](./media/execute-cli-jenkins-pipeline/calculator-add.png)

## <a name="deploy-to-azure-web-app-on-linux"></a>Nasazení do Azure Web App on Linux
Teď, když víte, jak používat rozhraní příkazového řádku Azure ve vašem kanálu Jenkins, můžete upravit skript pro nasazení do webové aplikace Azure v Linuxu.

Webové aplikace v Linuxu podporuje jiný způsob, jak provádět nasazení, který má použít Docker. Pokud chcete nasadit, musíte zadat soubor Dockerfile, která zabalí vaší webové aplikace s modulem runtime služby do image Dockeru. Modul plug-in se pak sestavení image, nahrajete do registru Docker a nasazení bitové kopie do vaší webové aplikace.

* Postupujte podle kroků [tady](../app-service/containers/quickstart-nodejs.md) vytvořit webovou aplikaci Azure v Linuxu spuštěnou.
* Nainstalovat Docker na instance Jenkinse pomocí následujících pokynů v tomto [článku](https://docs.docker.com/engine/installation/linux/ubuntu/).
* Vytvoření registru kontejnerů na webu Azure Portal pomocí postupu [tady](/azure/container-registry/container-registry-get-started-azure-cli).
* Ve stejném [jednoduché webové aplikace v Javě pro Azure](https://github.com/azure-devops/javawebappsample) Rozvětvená úložiště, upravit **Jenkinsfile2** souboru:
    * Řádek 18 21, aktualizujte název skupiny prostředků, webové aplikace a služby ACR v uvedeném pořadí. 
        ```
        def webAppResourceGroup = '<myResourceGroup>'
        def webAppName = '<app_name>'
        def acrName = '<myRegistry>'
        ```

    * Řádek 24, aktualizace \<azsrvprincipal\> ID přihlašovacích údajů
        ```
        withCredentials([azureServicePrincipal('<mySrvPrincipal>')]) {
        ```

* Vytvořit nový kanál Jenkinse, protože při nasazování na webovou aplikaci Azure ve Windows, jenom tentokrát použijete **Jenkinsfile2** místo.
* Spuštění nové úlohy.
* Pokud chcete ověřit, v Azure CLI, spusťte:

    ```
    az acr repository list -n <myRegistry> -o json
    ```

    Můžete získat následující výsledek:
    
    ```
    [
    "calculator"
    ]
    ```
    
    Přejděte na http://&lt;app_name >.azurewebsites.net/api/calculator/ping. Zobrazí se zpráva: 
    
        Welcome to Java Web App!!! This is updated!
        Sun Jul 09 16:39:10 UTC 2017

    Přejděte na http://&lt;app_name >.azurewebsites.net/api/calculator/add?x=&lt;x > & y =&lt;y > (Nahraďte &lt;x > a &lt;y > s všechna čísla) zobrazíte součet hodnot x a y
    
## <a name="next-steps"></a>Další postup
V tomto kurzu jste nakonfigurovali Jenkins kanál, který rezervuje zdrojového kódu v úložišti na Githubu. Spustí Maven k sestavení souboru war a potom pomocí rozhraní příkazového řádku Azure k nasazení do služby Azure App Service. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Vytvořit virtuální počítač Jenkins
> * Konfigurace Jenkinse
> * Vytvoření webové aplikace v Azure
> * Příprava úložiště GitHub
> * Vytvoření kanálu Jenkins
> * Spuštění kanálu a ověřte webovou aplikaci
