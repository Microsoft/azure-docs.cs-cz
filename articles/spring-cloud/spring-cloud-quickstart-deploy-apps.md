---
title: Rychlý Start – sestavování a nasazování aplikací do jarního cloudu Azure
description: Popisuje nasazení aplikace do jarního cloudu Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 8931c22c3656cf9708756153268ab1d9d87b8343
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050824"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Rychlý Start: sestavování a nasazování aplikací do jarního cloudu Azure

Tento dokument vysvětluje, jak vytvářet a nasazovat aplikace mikroslužeb do služby Azure jaře Cloud pomocí:
* Azure CLI
* Modul plug-in Maven
* IntelliJ

Před nasazením pomocí Azure CLI nebo Maven dokončete příklady, které [zřídí instanci Azure jaře cloudu](spring-cloud-quickstart-provision-service-instance.md) , a [nastavte konfigurační server](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Požadavky

* [Nainstalovat JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Registrace předplatného Azure](https://azure.microsoft.com/free/)
* Volitelné [Nainstalujte rozhraní příkazového řádku Azure CLI 2.0.67 nebo vyšší verze](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a nainstalujte rozšíření pro jarní cloud Azure pomocí příkazu: `az extension add --name spring-cloud`
* Volitelné [Instalace Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) a [přihlášení](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="deployment-procedures"></a>Postupy nasazení

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>Místní sestavení aplikací mikroslužeb

1. Naklonujte úložiště ukázkové aplikace do cloudového účtu Azure.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Změňte adresář a sestavte projekt.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

Kompilace projektu trvá přibližně 5 minut. Po dokončení byste měli mít jednotlivé soubory JAR pro každou službu v příslušných složkách.

### <a name="create-and-deploy-the-apps"></a>Vytvoření a nasazení aplikací

1. Pomocí následujících příkazů nastavte výchozí název skupiny prostředků a název clusteru:

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Vytvářejte mikroslužby jarních cloudů Azure pomocí souborů JAR vytvořených v předchozím kroku. Vytvoříte tři aplikace: **Brána**, **ověřovací služba**a **služba účtu**.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Musíme nasadit aplikace vytvořené v předchozím kroku do Azure. K nasazení všech tří aplikací použijte následující příkazy:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Přiřazení veřejného koncového bodu k bráně

Potřebujeme způsob, jak získat přístup k aplikaci přes webový prohlížeč. Naše aplikace brány potřebuje veřejný koncový bod.

1. Přiřaďte koncový bod pomocí následujícího příkazu:

    ```azurecli
    az spring-cloud app update -n gateway --is-public true
    ```

2. Dotazování aplikace **brány** na veřejnou IP adresu vám umožní ověřit, jestli je aplikace spuštěná:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>Klonování a sestavení ukázkového úložiště aplikace

1. Naklonujte úložiště Git spuštěním následujícího příkazu:

    ```
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Změňte adresář a sestavte projekt spuštěním následujícího příkazu:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generování konfigurací a nasazení do jarního cloudu Azure

1. Generujte konfigurace spuštěním následujícího příkazu v kořenové složce PiggyMetrics obsahujícího nadřazený POM. Pokud jste se už přihlásili pomocí Azure CLI, příkaz automaticky vybere přihlašovací údaje. V opačném případě vás bude přihlašovat pomocí pokynů pro příkazový řádek. Další podrobnosti najdete na naší [stránce wikiwebu](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) .

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```
    
    Zobrazí se výzva k výběru:
    * **Moduly:** Vyberte `gateway` , `auth-service` a `account-service` .
    * **Předplatné:** Toto je vaše předplatné, které se používá k vytvoření instance Azure jaře cloudu.
    * **Instance služby:** Jedná se o název vaší instance vašeho cloudového cloudu Azure.
    * **Veřejný koncový bod:** V seznamu poskytnutých projektů zadejte číslo, které odpovídá `gateway` .  Tím zajistíte veřejný přístup.

1. POM nyní obsahuje závislosti a konfigurace modulu plug-in. Nasaďte aplikace pomocí následujícího příkazu. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>Importovat ukázkový projekt v IntelliJ

1. Stáhněte a rozbalte zdrojové úložiště pro tento kurz nebo ho naklonujte pomocí Gitu: `git clone https://github.com/Azure-Samples/piggymetrics` 

1. Otevřete **uvítací** dialog IntelliJ a výběrem **Importovat projekt** otevřete Průvodce importem.

1. Vyberte `piggymetric` složku.

    ![Importovat projekt](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Nasazení aplikace brány do jarního cloudu Azure
Aby bylo možné nasadit nástroj do Azure, musíte se přihlásit pomocí účtu Azure pomocí Azure Toolkit for IntelliJ a zvolit své předplatné. Podrobnosti o přihlášení najdete v tématu [instalace a přihlášení](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. V Project Exploreru klikněte pravým tlačítkem na projekt a vyberte **Azure**  ->  **Deploy do Azure jaře Cloud**.

    ![Nasazení do Azure 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. Do pole **název** připojit *: Brána* k existujícímu **názvu** odkazuje na konfiguraci.
1. V textovém poli **artefaktu** vyberte *com. piggymetrics: Gateway: 1.0-Snapshot*.
1. V textovém poli **odběr** ověřte své předplatné.
1. V textovém poli pole **jarního cloudu** vyberte instanci služby Azure jaře Cloud, kterou jste vytvořili v části [zřízení instance cloudové služby Azure jaře](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Nastavte **veřejný koncový bod** , který se má *Povolit*.
1. V poli **aplikace:** textové pole vyberte **vytvořit aplikaci...**.
1. Zadejte *bránu*a pak klikněte na **OK**.

    ![Nasadit do Azure v pořádku](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. V části **před spuštěním** otevřete dvakrát klikněte na *Spustit Maven cíl*.
1. V textovém poli **pracovní adresář** přejděte do složky *piggymetrics/Gateway* .
1. Do textového pole **příkazový řádek** zadejte *Package-DskipTests*. Klikněte na **OK**.
1. Spusťte nasazení kliknutím na tlačítko **Spustit** v dolní části dialogového okna **nasadit Azure jarní cloudovou aplikaci** . Modul plug-in spustí příkaz `mvn package` v `gateway` aplikaci a nasadí jar vygenerované `package` příkazem.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Nasazení aplikací ověřování – služba a účet – aplikace do jarního cloudu Azure
Zopakováním výše uvedených kroků můžete nasadit `auth-service` a `account-service` aplikace do Azure jaře cloudu:

1. Upravte **název** a **artefakt** pro identifikaci `auth-service` aplikace.
1. V textovém poli **aplikace:** vyberte **vytvořit aplikaci...** a vytvořte `auth-service` aplikace.
1. Ověřte, jestli je možnost **veřejný koncový bod** nastavená na *disabled (zakázáno*).
1. V části **před spuštěním** v dialogovém okně přepněte **pracovní adresář** do složky *piggymetrics/auth-Service* .
1. Spusťte nasazení kliknutím na tlačítko **Spustit** v dolní části dialogového okna **nasadit Azure jarní cloudovou aplikaci** . 
1. Opakujte tyto postupy pro konfiguraci a nasazení `account-service` .
---

Přejděte na adresu URL poskytnutou ve výstupu předchozích kroků pro přístup k aplikaci PiggyMetrics. například. `https://<service instance name>-gateway.azuremicroservices.io`

![Přístup k PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Můžete také přejít na Azure Portal a najít tak adresu URL. 
1. Přejít ke službě
2. Vybrat **aplikace**
3. Vybrat **bránu**

    ![Navigace v aplikaci](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Najít adresu URL v **bráně | Stránka Přehled**

    ![Přejít k druhé aplikaci](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu prostředků z portálu nebo spuštěním následujícího příkazu v Cloud Shell:
```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```
V předchozích krocích jste také nastavili výchozí název skupiny prostředků. Pokud chcete tuto výchozí hodnotu vymazat, spusťte v Cloud Shell následující příkaz:
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Protokoly, metriky a trasování](spring-cloud-quickstart-logs-metrics-tracing.md)
