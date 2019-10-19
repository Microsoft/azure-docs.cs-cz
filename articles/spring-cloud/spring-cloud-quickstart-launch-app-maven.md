---
title: 'Rychlý Start: spuštění aplikace s využitím Maven – jarního cloudu Azure'
description: Spuštění ukázkové aplikace pomocí Maven
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/05/2019
ms.author: v-vasuke
ms.openlocfilehash: e773b997cca3fa9a1f11fec2ac449e1fc11c5364
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554568"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-by-using-the-maven-plug-in"></a>Rychlý Start: spuštění aplikace pro jarní Cloud z Azure pomocí modulu plug-in Maven

S modulem plug-in Azure jaře Cloud Maven můžete snadno vytvářet a aktualizovat aplikace služby jarní cloudové služby Azure. Když nasadíte konfiguraci, můžete nasadit aplikace do své stávající služby pro jarní cloudy Azure. V tomto článku můžete tuto funkci předvést pomocí ukázkové aplikace s názvem PiggyMetrics.

>[!Note]
> Než začnete s tímto rychlým startem, ujistěte se, že vaše předplatné Azure má přístup k jarnímu cloudu Azure. Jako služba ve verzi Preview vás zve na nás, abychom mohli přidat vaše předplatné do seznamu povolených adres. Pokud chcete prozkoumat možnosti Azure jarního cloudu, vyplňte a odešlete [formulář zájmu Azure jaře Cloud (Private Preview)](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u).

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít ke spuštění příkazů v tomto článku. Má předinstalované běžné nástroje Azure, včetně nejnovějších verzí Gitu, sady Java Development Kit (JDK), Maven a Azure CLI. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com). Další informace najdete v tématu [přehled Azure Cloud Shell](../cloud-shell/overview.md).

K provedení kroků v tomto kurzu Rychlý start je potřeba:

1. [Nainstalovat Git](https://git-scm.com/).
2. [Nainstalujte JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Nainstalujte Maven 3,0 nebo novější](https://maven.apache.org/download.cgi).
4. [Nainstalujte rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
5. [Zaregistrujte si bezplatné předplatné Azure](https://azure.microsoft.com/free/).

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Nainstalujte rozšíření Azure jaře Cloud pro Azure CLI pomocí následujícího příkazu:

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Zřízení instance služby na Azure Portal

1. Ve webovém prohlížeči otevřete [Azure Portal](https://portal.azure.com)a přihlaste se ke svému účtu.

1. Vyhledejte a potom vyberte **Azure jaře Cloud**. 
1. Na stránce Přehled vyberte **vytvořit**a pak postupujte takto:  

    a. Do pole **název služby** zadejte název vaší instance služby. Název musí být dlouhý 4 až 32 znaků a může obsahovat jenom malá písmena, číslice a spojovníky. První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.  

    b. V rozevíracím seznamu **předplatné** vyberte předplatné, které chcete pro tento prostředek fakturovat. Zajistěte, aby bylo toto předplatné přidané do seznamu povolených pro Azure jaře Cloud.  

    c. V poli **Skupina prostředků** vytvořte novou skupinu prostředků. Osvědčeným postupem je vytvoření skupin prostředků pro nové prostředky.  

    d. V rozevíracím seznamu **umístění** vyberte umístění pro instanci služby. Aktuálně podporovaná umístění zahrnují Východní USA, Západní USA 2, Západní Evropa a jihovýchodní Asie.
    
Nasazení služby trvá asi 5 minut. Po nasazení služby se zobrazí stránka s **přehledem** instance služby.

## <a name="set-up-your-configuration-server"></a>Nastavení konfiguračního serveru

1. Na stránce **Přehled** služby vyberte možnost **konfigurační server**.
1. V části **výchozí úložiště** nastavte **URI** na **https://github.com/Azure-Samples/piggymetrics** , nastavte **jmenovku** na **config**a pak klikněte na **použít** , aby se změny uložily.

## <a name="clone-and-build-the-sample-application-repository"></a>Klonování a sestavení ukázkového úložiště aplikace

1. Naklonujte úložiště Git spuštěním následujícího příkazu:

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Změňte adresář a sestavte projekt spuštěním následujícího příkazu:

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Generování a nasazení konfigurace jarního cloudu Azure

1. Pokud chcete povolit, aby Maven spolupracovaly se službou Azure Pramenitého cloudu, přidejte do souboru *pom. XML* nebo *Settings. XML* následující kód.

    ```xml
    <pluginRepositories>
      <pluginRepository>
        <id>maven.snapshots</id>
        <name>Maven Central Snapshot Repository</name>
        <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        <releases>
          <enabled>false</enabled>
        </releases>
        <snapshots>
          <enabled>true</enabled>
        </snapshots>
      </pluginRepository>
    </pluginRepositories>
    ```

1. Spuštěním následujícího příkazu vygenerujte konfiguraci:

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    a. Vyberte moduly `gateway`, `auth-service` a `account-service`.

    b. Vyberte své předplatné a cluster jarních cloudových služeb Azure.

    c. V seznamu poskytnutých projektů zadejte číslo, které odpovídá `gateway`, aby získalo veřejný přístup.
    
    d. Potvrďte konfiguraci.

1. Nasaďte aplikace pomocí následujícího příkazu:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. K PiggyMetrics můžete přistupovat pomocí adresy URL, která je k dispozici ve výstupu z předchozího příkazu.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jarní cloudovou aplikaci z úložiště Maven. Další informace o jarním cloudu Azure najdete v kurzu o přípravě aplikace na nasazení.

> [!div class="nextstepaction"]
> [Příprava aplikace pro jarní cloudy Azure pro nasazení](spring-cloud-tutorial-prepare-app-deployment.md)
