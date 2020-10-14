---
title: Rychlý Start – sestavování a nasazování aplikací do jarního cloudu Azure
description: Popisuje nasazení aplikace do jarního cloudu Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: d368c938e805164b70c7d3a22f9229b5efd89dde
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045394"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Rychlý Start: sestavování a nasazování aplikací do jarního cloudu Azure

::: zone pivot="programming-language-csharp"
V tomto rychlém startu sestavíte a nasadíte aplikace mikroslužeb do jarního cloudu Azure pomocí Azure CLI.

## <a name="prerequisites"></a>Požadované součásti

* Dokončete předchozí rychlé starty v této sérii:

  * [Zřídit Azure jaře cloudovou službu](spring-cloud-quickstart-provision-service-instance.md).
  * [Nastavte server pro konfiguraci jarního cloudu Azure](spring-cloud-quickstart-setup-config-server.md).

## <a name="download-the-sample-app"></a>Stažení ukázkové aplikace

Pokud používáte Azure Cloud Shell až do tohoto bodu, přejděte k následujícímu příkazovému řádku na místní příkazový řádek.

1. Vytvořte novou složku a naklonujte úložiště ukázkové aplikace.

   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```console
   git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples
   ```

1. Přejděte do adresáře úložiště.

   ```console
   cd Azure-Spring-Cloud-Samples
   ```

## <a name="deploy-planetweatherprovider"></a>Nasazení PlanetWeatherProvider

1. Vytvořte aplikaci pro projekt PlanetWeatherProvider v instanci cloudu Azure jaře.

   ```azurecli
   az spring-cloud app create --name planet-weather-provider --runtime-version NetCore_31
   ```

   Pokud chcete povolit automatickou registraci služby, měla by aplikace mít stejný název jako hodnota `spring.application.name` v *appsettings.jsprojektu v* souboru:

   ```json
   "spring": {
     "application": {
       "name": "planet-weather-provider"
     }
   }
   ```

   Spuštění tohoto příkazu může trvat několik minut.

1. Změňte adresář na `PlanetWeatherProvider` složku projektu.

   ```console
   cd steeltoe-sample/src/planet-weather-provider
   ```

1. Vytvořte binární soubory a soubor *. zip* , který chcete nasadit.

   ```console
   dotnet publish -c release -o ./publish
   ```

   > [!TIP]
   > Soubor projektu obsahuje následující kód XML pro zabalení binárních souborů do souboru *. zip* po jejich zápis do složky *./Publish* :
   >
   > ```xml
   > <Target Name="Publish-Zip" AfterTargets="Publish">
   >   <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/publish-deploy-planet.zip" Overwrite="true" />
   > </Target>
   > ```

1. Nasaďte do Azure.

   Před spuštěním následujícího příkazu se ujistěte, že je příkazový řádek ve složce projektu.

   ```console
   az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip
   ```

   `--main-entry`Možnost určuje relativní cestu z kořenové složky souboru *. zip* k souboru *. dll* , který obsahuje vstupní bod aplikace. Poté, co služba nahraje soubor *. zip* , extrahuje všechny soubory a složky a pokusí se spustit vstupní bod v zadaném souboru *. dll* .

   Spuštění tohoto příkazu může trvat několik minut.

## <a name="deploy-solarsystemweather"></a>Nasazení SolarSystemWeather

1. Vytvořte v instanci Azure jaře cloudu jinou aplikaci, tentokrát pro projekt SolarSystemWeather:

   ```azurecli
   az spring-cloud app create --name solar-system-weather --runtime-version NetCore_31
   ```

   `solar-system-weather` je název, který je určen v `SolarSystemWeather` *appsettings.js* souboru projektu.

   Spuštění tohoto příkazu může trvat několik minut.

1. Změňte adresář na `SolarSystemWeather` projekt.

   ```console
   cd ../solar-system-weather
   ```

1. Vytvořte binární soubory a soubor *. zip* , který chcete nasadit.

   ```console
   dotnet publish -c release -o ./publish
   ```

1. Nasaďte do Azure.

   ```console
   az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip
   ```
   
   Spuštění tohoto příkazu může trvat několik minut.

## <a name="assign-public-endpoint"></a>Přiřadit veřejný koncový bod

K otestování aplikace odešlete požadavek HTTP GET do `solar-system-weather` aplikace z prohlížeče.  K tomu potřebujete veřejný koncový bod pro požadavek.

1. Pokud chcete koncový bod přiřadit, spusťte následující příkaz.

   ```azurecli
   az spring-cloud app update -n solar-system-weather --is-public true
   ```

1. Pokud chcete získat adresu URL koncového bodu, spusťte následující příkaz.

   Windows:

   ```azurecli
   az spring-cloud app show -n solar-system-weather -o table
   ```

   Linux:

   ```azurecli
   az spring-cloud app show --name solar-system-weather | grep url
   ```

## <a name="test-the-application"></a>Testování aplikace

Odeslat požadavek GET do `solar-system-weather` aplikace V prohlížeči přejděte na veřejnou adresu URL, která se `/weatherforecast` připojí ke konci. Příklad:

```
https://servicename-solar-system-weather.azuremicroservices.io/weatherforecast
```

Výstup je JSON:

```json
[{"Key":"Mercury","Value":"very warm"},{"Key":"Venus","Value":"quite unpleasant"},{"Key":"Mars","Value":"very cool"},{"Key":"Saturn","Value":"a little bit sandy"}]
```

Tato odpověď ukazuje, že obě aplikace mikroslužeb fungují. `SolarSystemWeather`Aplikace vrátí data načtená z `PlanetWeatherProvider` aplikace.
::: zone-end

::: zone pivot="programming-language-java"
Tento dokument vysvětluje, jak vytvářet a nasazovat aplikace mikroslužeb do služby Azure jaře Cloud pomocí:
* Azure CLI
* Modul plug-in Maven
* IntelliJ

Před nasazením pomocí Azure CLI nebo Maven dokončete příklady, které [zřídí instanci Azure jaře cloudu](spring-cloud-quickstart-provision-service-instance.md) , a [nastavte konfigurační server](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Požadované součásti

* [Nainstalovat JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [Registrace předplatného Azure](https://azure.microsoft.com/free/)
* Volitelné [Nainstalujte rozhraní příkazového řádku Azure CLI 2.0.67 nebo vyšší verze](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) a nainstalujte rozšíření pro jarní cloud Azure pomocí příkazu: `az extension add --name spring-cloud`
* Volitelné [Nainstalujte Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) a [přihlaste](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in) se.

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
    git clone https://github.com/Azure-Samples/piggymetrics
    ```
  
1. Změňte adresář a sestavte projekt spuštěním následujícího příkazu:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generování konfigurací a nasazení do jarního cloudu Azure

1. Generujte konfigurace spuštěním následujícího příkazu v kořenové složce PiggyMetrics obsahujícího nadřazený POM. Pokud jste se už přihlásili pomocí Azure CLI, příkaz automaticky vybere přihlašovací údaje. V opačném případě vás bude přihlašovat pomocí pokynů pro příkazový řádek. Další informace najdete na naší [stránce wikiwebu](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.3.0:config
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

1. Do pole **název** přidejte *: Brána* k existujícímu **názvu**.
1. V textovém poli **artefaktu** vyberte *com. piggymetrics: Gateway: 1.0-Snapshot*.
1. V textovém poli **odběr** ověřte své předplatné.
1. V textovém poli pole **jarního cloudu** vyberte instanci služby Azure jaře Cloud, kterou jste vytvořili v části [zřízení instance cloudové služby Azure jaře](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Nastavte **veřejný koncový bod** , který se má *Povolit*.
1. V poli **aplikace:** textové pole vyberte **vytvořit aplikaci...**.
1. Zadejte *bránu*a pak klikněte na **OK**.

    ![Nasadit do Azure v pořádku](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. V části **před spuštěním** klikněte dvakrát na možnost *Spustit Maven cíl*.
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

Přejděte na adresu URL poskytnutou ve výstupu předchozích kroků pro přístup k aplikaci PiggyMetrics. Příklad: `https://<service instance name>-gateway.azuremicroservices.io`

![Přístup k PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Můžete také přejít na Azure Portal a najít tak adresu URL. 
1. Přejít ke službě
2. Vybrat **aplikace**
3. Vybrat **bránu**

    ![Navigace v aplikaci](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Najít adresu URL v **bráně | Stránka Přehled**

    ![Přejít k druhé aplikaci](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

::: zone-end

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili prostředky Azure, které budou nadále účtovat poplatky, pokud zůstanou ve vašem předplatném. Pokud nechcete pokračovat k dalšímu rychlému startu, přečtěte si téma [vyčištění prostředků](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). V opačném případě přejděte k dalšímu rychlému startu:

> [!div class="nextstepaction"]
> [Protokoly, metriky a trasování](spring-cloud-quickstart-logs-metrics-tracing.md)
