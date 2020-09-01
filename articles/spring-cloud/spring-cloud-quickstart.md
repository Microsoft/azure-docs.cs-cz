---
title: Rychlý start – nasazení první aplikace Azure Spring Cloudu
description: V tomto rychlém startu nasadíme jarní cloudovou aplikaci do jarního cloudu Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 245516e0a54865d3a6097c4bb566b850cb738ad6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89260546"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Rychlý Start: nasazení první aplikace pro cloudovou službu Azure jaře

V tomto rychlém startu se dozvíte, jak nasadit jednoduchou službu Azure jaře Cloud mikroslužeb, která se bude spouštět v Azure. 

Kód aplikace použitý v tomto kurzu je jednoduchá aplikace vytvořená s pružinou Initializr. Po dokončení tohoto příkladu bude aplikace dostupná online a bude ji možné spravovat prostřednictvím Azure Portal.

V tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
> * Vygenerujte základní projekt jarního cloudu
> * Zřízení instance služby
> * Sestavení a nasazení aplikace s veřejným koncovým bodem
> * Streamování protokolů v reálném čase

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* [Nainstalovat JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Registrace předplatného Azure](https://azure.microsoft.com/free/)
* Volitelné [Nainstalujte Azure CLI verze 2.0.67 nebo vyšší](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) a rozšíření Azure jaře cloudu pomocí příkazu: `az extension add --name spring-cloud`
* Volitelné [Instalace Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) a [přihlášení](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>Vygenerování jarního cloudového projektu

Začněte s [pružinou Initializr](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin) a vygenerujte ukázkový projekt s doporučenými závislostmi pro jarní cloud Azure. Následující obrázek ukazuje Initializr sadu pro tento ukázkový projekt.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin
```

  ![Stránka Initializr](media/spring-cloud-quickstart-java/initializr-page.png)

1. Po nastavení všech závislostí klikněte na **vygenerovat** . Stáhněte a rozbalte balíček a pak vytvořte webový řadič pro jednoduchou webovou aplikaci přidáním `src/main/java/com/example/hellospring/HelloController.java` následujícího postupu:

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Zřízení instance Azure jarního cloudu

Následující postup vytvoří instanci Azure jarního cloudu pomocí Azure Portal.

1. Na nové kartě otevřete [Azure Portal](https://ms.portal.azure.com/). 

2. V horním vyhledávacím poli vyhledejte *Azure jaře Cloud*.

3. Z výsledků vyberte *Azure jaře Cloud* .

    ![Ikona ASC – začátek](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na stránce jarní cloud Azure klikněte na **+ Přidat**.

    ![Ikona ASC přidat](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Vyplňte formulář na stránce **Vytvoření** jarního cloudu Azure.  Vezměte v úvahu následující pokyny:
    - **Předplatné**: vyberte předplatné, které chcete pro tento prostředek fakturovat.
    - **Skupina prostředků**: vytváření nových skupin prostředků pro nové prostředky je osvědčeným postupem. Tento postup bude použit v pozdějších krocích jako **\<resource group name\>** .
    - **Podrobnosti o službě/název**: zadejte **\<service instance name\>** .  Název musí být dlouhý 4 až 32 znaků a může obsahovat jenom malá písmena, číslice a spojovníky.  První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.
    - **Umístění**: Vyberte oblast pro instanci služby.

    ![Začátek portálu ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Klikněte na **Zkontrolovat a vytvořit**.

## <a name="build-and-deploy-the-app"></a>Sestavení a nasazení aplikace
    
#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)
Následující postup sestaví a nasadí aplikaci pomocí rozhraní příkazového řádku Azure CLI. Spusťte následující příkaz v kořenu projektu.

1. Sestavte projekt pomocí Maven:

    ```console
    mvn clean package -DskipTests
    ```

1. (Pokud jste ho ještě nenainstalovali) Instalace rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure:

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Vytvořte aplikaci s přiřazeným veřejným koncovým bodem:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. Nasadit soubor JAR pro aplikaci:

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
    ```
    
1. Dokončení nasazení aplikace trvá několik minut. Pokud chcete potvrdit, že se nasadil, otevřete okno **aplikace** v Azure Portal. Měl by se zobrazit stav aplikace.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Následující postup používá modul plug-in IntelliJ pro Azure jaře Cloud k nasazení ukázkové aplikace v IntelliJ nápad.  

### <a name="import-project"></a>Import projektu

1. Otevřete **uvítací** dialog IntelliJ a výběrem **Importovat projekt** otevřete Průvodce importem.
1. Vyberte `hellospring` složku.

    ![Importovat projekt](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Nasazení aplikace
Aby bylo možné nasadit nástroj do Azure, musíte se přihlásit pomocí účtu Azure a vybrat své předplatné.  Podrobnosti o přihlášení najdete v tématu [instalace a přihlášení](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. V Project Exploreru klikněte pravým tlačítkem na projekt a vyberte **Azure**  ->  **Deploy do Azure jaře Cloud**.

    [![Nasazení do Azure 1 ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png)](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. V poli **název** přijměte název aplikace. **Název** odkazuje na konfiguraci, nikoli na název aplikace. Uživatelé je obvykle nemusí měnit.
1. V textovém poli **artefaktu** vyberte *hellospring-0.0.1-Snapshot. jar*.
1. V textovém poli **odběr** ověřte své předplatné.
1. V textovém poli pole **jarního cloudu** vyberte instanci služby Azure jaře Cloud, kterou jste vytvořili v části [zřízení instance cloudové služby Azure jaře](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Nastavte **veřejný koncový bod** , který se má *Povolit*.
1. V poli **aplikace:** textové pole vyberte **vytvořit aplikaci...**.
1. Zadejte *hellospring*a pak klikněte na **OK**.

    [![Nasadit do Azure v pořádku ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png)](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. Spusťte nasazení kliknutím na tlačítko **Spustit** v dolní části dialogového okna **nasadit Azure jarní cloudovou aplikaci** . Modul plug-in spustí příkaz `mvn package` v `hellospring` aplikaci a nasadí jar vygenerované `package` příkazem.
---

Po dokončení nasazení budete mít k aplikaci přístup na adrese `https://<service instance name>-hellospring.azuremicroservices.io/` .

  [![Přístup k aplikaci z prohlížeče ](media/spring-cloud-quickstart-java/access-app-browser.png)](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Streamování protokolů v reálném čase

#### <a name="cli"></a>[Rozhraní příkazového řádku](#tab/Azure-CLI)

Pomocí následujícího příkazu můžete z aplikace získat protokoly v reálném čase.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
Protokoly se zobrazí ve výsledcích:

[![Protokoly ](media/spring-cloud-quickstart-java/streaming-logs.png) streamování](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> Slouží `az spring-cloud app logs -h` k prozkoumávání dalších parametrů a funkcí streamování protokolů.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Vyberte **Azure Explorer**a pak **jarní Cloud**.
1. Klikněte pravým tlačítkem na spuštěnou aplikaci.
1. V rozevíracím seznamu vyberte možnost **protokoly streamování** .
1. Vyberte instanci.

    [![Výběr protokolů ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) streamování](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. Protokol streamování se zobrazí v okně výstup.

    [![Výstup ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png) protokolu streamování](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

Pokročilé funkce Log Analytics najdete na kartě **protokoly** v nabídce na [Azure Portal](https://portal.azure.com/). Protokoly mají latenci během několika minut.

[![Analýza ](media/spring-cloud-quickstart-java/logs-analytics.png) protokolů](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků
V předchozích krocích jste vytvořili prostředky Azure ve skupině prostředků. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu prostředků z portálu nebo spuštěním následujícího příkazu v Cloud Shell:
```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

V tomto rychlém startu jste se naučili:

> [!div class="checklist"]
> * Vygenerujte základní projekt o jarním cloudu Azure
> * Zřízení instance služby
> * Sestavení a nasazení aplikace s veřejným koncovým bodem
> * Streamování protokolů v reálném čase
## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Sestavování a spouštění mikroslužeb](spring-cloud-quickstart-sample-app-introduction.md)

Další ukázky jsou k dispozici na GitHubu: [ukázky Azure pro jarní Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
