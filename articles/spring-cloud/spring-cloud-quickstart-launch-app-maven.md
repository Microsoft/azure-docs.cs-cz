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
ms.openlocfilehash: 01140e94e8d0cc47570824970801bdd0043324d7
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72166521"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Rychlý Start: spuštění aplikace pro jarní Cloud z Azure pomocí modulu plug-in Maven

Modul plug-in Maven pro Azure jaře Cloud umožňuje snadno vytvořit a aktualizovat aplikace služby jarních cloudových služeb Azure. Když nasadíte určitou konfiguraci, můžete nasadit aplikace do své stávající služby pro jarní cloudy Azure. V tomto článku používáme k demonstraci této funkce ukázkovou aplikaci s názvem PiggyMetrics.

>[!Note]
> Před zahájením tohoto rychlého startu se ujistěte, že vaše předplatné Azure má přístup k jarnímu cloudu Azure.  Jako služba ve verzi Preview požádáme zákazníky, aby se na nás dostali, abychom mohli přidat vaše předplatné do našeho seznamu povolených.  Pokud chcete prozkoumat možnosti Azure jarního cloudu, [vyplňte prosím tento formulář](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR-LA2geqX-ZLhi-Ado1LD3tUNDk2VFpGUzYwVEJNVkhLRlcwNkZFUFZEUS4u
).

>[!TIP]
> Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku.  Má předinstalované běžné nástroje Azure, včetně nejnovějších verzí Git, JDK, Maven a Azure CLI. Pokud jste přihlášeni ke svému předplatnému Azure, spusťte [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Další informace o Azure Cloud Shell najdete v [naší dokumentaci](../cloud-shell/overview.md) .

K provedení kroků v tomto kurzu Rychlý start je potřeba:

1. [Nainstalovat Git](https://git-scm.com/).
2. [Nainstalovat JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Nainstalujte Maven 3,0 nebo novější.](https://maven.apache.org/download.cgi)
4. [Instalace rozhraní příkazového řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Registrace předplatného Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Pomocí následujícího příkazu nainstalujte rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure.

```Azure CLI
az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
```

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Zřízení instance služby na Azure Portal

1. Ve webovém prohlížeči otevřete [Azure Portal](https://portal.azure.com)a přihlaste se ke svému účtu.

1. Vyhledejte **Azure jaře Cloud** a vyberte ho, abyste přešli na stránku Přehled. Začněte tím, že vyberete tlačítko **vytvořit** .

1. Vyplňte formulář a zvažte následující pokyny:
    - Název služby: zadejte název instance služby.  Název musí být dlouhý 4 až 32 znaků a může obsahovat jenom malá písmena, číslice a spojovníky.  První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.
    - Předplatné: vyberte předplatné, které chcete pro tento prostředek fakturovat.  Zajistěte, aby bylo toto předplatné přidané do našeho seznamu povolených pro Azure jaře Cloud.
    - Skupina prostředků: vytváření nových skupin prostředků pro nové prostředky je osvědčeným postupem.
    - Umístění: vyberte umístění pro instanci služby. Aktuálně podporovaná umístění zahrnují Východní USA, Západní USA 2, Západní Evropa a jihovýchodní Asie.
    
Nasazení služby trvá asi 5 minut.  Po nasazení se zobrazí stránka s **přehledem** instance služby.

## <a name="set-up-your-configuration-server"></a>Nastavení konfiguračního serveru

1. Přejít na stránku **Přehled** služby a vyberte možnost **konfigurační server**.
1. V části **výchozí úložiště** nastavte možnost **URI** na "https://github.com/Azure-Samples/piggymetrics", nastavte **jmenovku** na "config" a pak změny uložte kliknutím na **použít** .

## <a name="clone-and-build-the-sample-application-repository"></a>Klonování a sestavení ukázkového úložiště aplikace

1. Naklonujte úložiště Git spuštěním následujícího příkazu.

    ```azurecli
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Změňte adresář a sestavte projekt spuštěním následujícího příkazu.

    ```azurecli
    cd PiggyMetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-and-deploy-the-azure-spring-cloud-configuration"></a>Generování a nasazení konfigurace jarního cloudu Azure

1. Přidejte následující do `pom.xml` nebo `settings.xml`, abyste Maven mohli pracovat se službou Azure Pramenitého cloudu.

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

1. Spuštěním následujícího příkazu vygenerujte konfiguraci.

    ```azurecli
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:config
    ```

    1. Vyberte moduly `gateway`, `auth-service` a `account-service`.

    1. Vyberte své předplatné a cluster jarních cloudových služeb Azure.

    1. V seznamu poskytnutých projektů zadejte číslo, které odpovídá `gateway`, aby získal přístup k veřejnému přístupu.
    
    1. Potvrďte konfiguraci.

1. Nasaďte aplikace pomocí následujícího příkazu:

   ```azurecli
   mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:0.1.0-SNAPSHOT:deploy
   ```

1. K PiggyMetrics můžete přistupovat pomocí adresy URL, která je k dispozici ve výstupu z předchozího příkazu.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jarní cloudovou aplikaci z úložiště Maven.  Další informace o jarním cloudu Azure najdete v kurzu Příprava aplikace na nasazení.

> [!div class="nextstepaction"]
> [Příprava aplikace pro jarní cloudy Azure pro nasazení](spring-cloud-tutorial-prepare-app-deployment.md)
