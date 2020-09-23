---
title: Rychlý start – nasazení první aplikace Azure Spring Cloudu
description: V tomto rychlém startu nasadíme jarní cloudovou aplikaci do jarního cloudu Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 464879f5962b240ec82fb80957d146cadbf2b1bd
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90904310"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Rychlý Start: nasazení první aplikace pro cloudovou službu Azure jaře

::: zone pivot="programming-language-csharp"
V tomto rychlém startu se dozvíte, jak nasadit jednoduchou službu Azure jaře Cloud mikroslužeb, která se bude spouštět v Azure.

>[!NOTE]
> Podpora Steeltoe pro jarní cloud Azure je teď nabízená jako verze Public Preview. Nabídky veřejné verze Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiální verzí.  Funkce a služby verze Public Preview nejsou určeny pro produkční použití.  Další informace o podpoře v rámci verzí Preview najdete v tématu [Nejčastější dotazy](https://azure.microsoft.com/support/faq/) nebo soubor a [support Request](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

V tomto rychlém startu se dozvíte, jak:

> [!div class="checklist"]
> * Vygenerování základního projektu Steeltoe .NET Core
> * Zřízení instance služby jarní cloudová služba Azure
> * Sestavení a nasazení aplikace s veřejným koncovým bodem
> * Streamování protokolů v reálném čase

Kód aplikace použitý v tomto rychlém startu je jednoduchá aplikace vytvořená pomocí šablony projektu webového rozhraní API .NET Core. Po dokončení tohoto příkladu bude aplikace dostupná online a bude ji možné spravovat prostřednictvím Azure Portal a Azure CLI.

## <a name="prerequisites"></a>Požadavky

* Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Sada .NET Core 3,1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1). Služba pružinového cloudu Azure podporuje .NET Core 3,1 a novější verze.
* [Azure CLI verze 2.0.67 nebo novější](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).
* [Git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Instalace rozšíření Azure CLI

Ověřte, jestli je verze Azure CLI 2.0.67 nebo novější:

```azurecli
az --version
```

Pomocí následujícího příkazu nainstalujte rozšíření Azure jaře Cloud pro rozhraní příkazového řádku Azure:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

1. Přihlášení k rozhraní příkazového řádku Azure

    ```azurecli
    az login
    ```

1. Pokud máte více než jedno předplatné, vyberte ho, který chcete použít pro tento rychlý Start.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="generate-a-steeltoe-net-core-project"></a>Generování projektu Steeltoe .NET Core

V aplikaci Visual Studio vytvořte ASP.NET Core webové aplikace s názvem "Hello-World" se šablonou projektu rozhraní API. Všimněte si prosím, že se automaticky vygenerovala WeatherForecastController, která bude náš testovací koncový bod později.

1. Vytvořte složku pro zdrojový kód projektu a vygenerujte projekt.
 
   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```dotnetcli
   dotnet new webapi -n hello-world --framework netcoreapp3.1
   ```

1. Přejděte do adresáře projektu.

   ```console
   cd hello-world
   ```

1. Úpravou *appSettings.jsv*   souboru přidejte následující nastavení:

   ```json
   "spring": {
     "application": {
       "name": "hello-world"
     }
   },
   "eureka": {
     "client": {
       "shouldFetchRegistry": true,
       "shouldRegisterWithEureka": true
     }
   }
   ```

1. Také v *appsettings.jszapnuto*, změňte úroveň protokolu pro `Microsoft` kategorii z `Warning` na `Information` . Tato změna zajistí, že se protokoly vytvoří při zobrazení protokolů streamování v pozdějším kroku.

   *appsettings.jsv* souboru teď vypadá podobně jako v následujícím příkladu:

   ```json
   {
     "Logging": {
       "LogLevel": {
         "Default": "Information",
         "Microsoft": "Information",
         "Microsoft.Hosting.Lifetime": "Information"
       }
     },
     "AllowedHosts": "*",
     "spring": {
       "application": {
         "name": "hello-world"
       }
     },
     "eureka": {
       "client": {
         "shouldFetchRegistry": true,
         "shouldRegisterWithEureka": true
       }
     }
   }
   ```
   
1. Přidejte závislosti a `Zip` úlohu do souboru *. csproj* :

   ```xml
   <ItemGroup>
     <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
     <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-alpha.1" />
   </ItemGroup>
   <Target Name="Publish-Zip" AfterTargets="Publish">
     <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/deploy.zip" Overwrite="true" />
   </Target>
   ```

   Balíčky jsou pro zjišťování služby Steeltoe a klientskou knihovnu pro Azure jaře Cloud. `Zip`Úloha je určena k nasazení do Azure. Když `dotnet publish` příkaz spustíte, vygeneruje binární soubory ve složce pro *publikování* a tato úloha zips složku pro *publikování* do souboru *. zip* , který nahrajete do Azure.

3. V souboru *program.cs* přidejte `using` direktivu a kód, který používá knihovnu klienta Azure pro jarní Cloud:

   ```csharp
   using Microsoft.Azure.SpringCloud.Client;
   ```

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
               Host.CreateDefaultBuilder(args)
                   .ConfigureWebHostDefaults(webBuilder =>
                   {
                       webBuilder.UseStartup<Startup>();
                   })
                   .UseAzureSpringCloudService();
   ```

4. V souboru *Startup.cs* přidejte `using` direktivu a kód, který používá zjišťování služby Steeltoe na konci `ConfigureServices` `Configure` metody a:

   ```csharp
   using Steeltoe.Discovery.Client;
   ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       // Template code not shown.

       services.AddDiscoveryClient(Configuration);
   }
   ```

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       // Template code not shown.

       app.UseDiscoveryClient();
   }
   ```

1. Sestavte projekt, abyste se ujistili, že nejsou k dispozici žádné chyby kompilace.

   ```dotnetcli
   dotnet build
   ```
 
## <a name="provision-a-service-instance"></a>Zřízení instance služby

Následující postup vytvoří instanci Azure jarního cloudu pomocí Azure Portal.

1. Otevřete web [Azure Portal](https://ms.portal.azure.com/). 

1. V horním vyhledávacím poli vyhledejte *Azure jaře Cloud*.

1. Z výsledků vyberte *Azure jaře Cloud* .

   ![Ikona ASC – začátek](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

1. Na stránce jarní cloud Azure vyberte **+ Přidat**.

   ![Ikona ASC přidat](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

1. Vyplňte formulář na stránce **Vytvoření** jarního cloudu Azure.  Vezměte v úvahu následující pokyny:

   * **Předplatné**: vyberte předplatné, které chcete pro tento prostředek fakturovat.
   * **Skupina prostředků**: Vytvořte novou skupinu prostředků. Název, který zde zadáte, bude použit v pozdějších krocích jako **\<resource group name\>** .
   * **Podrobnosti o službě/název**: zadejte **\<service instance name\>** .  Název musí být dlouhý 4 až 32 znaků a může obsahovat jenom malá písmena, číslice a spojovníky.  První znak názvu služby musí být písmeno a poslední znak musí být písmeno nebo číslo.
   * **Oblast**: Vyberte oblast pro instanci služby.

   ![Začátek portálu ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Vyberte možnost **zkontrolovat a vytvořit**.

## <a name="build-and-deploy-the-app"></a>Sestavení a nasazení aplikace

Následující postup sestaví a nasadí projekt, který jste vytvořili dříve.

1. Ujistěte se, že je příkazový řádek stále ve složce projektu.

1. Spusťte následující příkaz pro sestavení projektu, publikování binárních souborů a uložte binární soubory do souboru *. zip* ve složce projektu.

   ```dotnetcorecli
   dotnet publish -c release -o ./publish
   ```

1. Vytvořte aplikaci v instanci cloudu Azure jaře s přiřazeným veřejným koncovým bodem. Použijte stejný název aplikace "Hello World", který jste zadali v *appsettings.jszapnuto*.

   ```console
   az spring-cloud app create -n hello-world -s <service instance name> -g <resource group name> --is-public
   ```

1. Nasaďte do aplikace soubor *. zip* .

   ```azurecli
   az spring-cloud app deploy -n hello-world -s <service instance name> -g <resource group name> --runtime-version NetCore_31 --main-entry hello-world.dll --artifact-path ./deploy.zip
   ```

   `--main-entry`Možnost identifikuje soubor *. dll* , který obsahuje vstupní bod aplikace. Poté, co služba nahraje soubor *. zip* , extrahuje všechny soubory a složky a pokusí se spustit vstupní bod v souboru *. dll* určeném parametrem `--main-entry` .

   Dokončení nasazení aplikace trvá několik minut. Pokud chcete potvrdit, že se nasadil, otevřete okno **aplikace** v Azure Portal.

## <a name="test-the-app"></a>Otestování aplikace

Po dokončení nasazení přejděte k aplikaci na následující adrese URL:

```http
https://<service instance name>-hello-world.azuremicroservices.io/weatherforecast
```

Aplikace vrátí data JSON podobně jako v následujícím příkladu:

```json
[{"date":"2020-09-08T21:01:50.0198835+00:00","temperatureC":14,"temperatureF":57,"summary":"Bracing"},{"date":"2020-09-09T21:01:50.0200697+00:00","temperatureC":-14,"temperatureF":7,"summary":"Bracing"},{"date":"2020-09-10T21:01:50.0200715+00:00","temperatureC":27,"temperatureF":80,"summary":"Freezing"},{"date":"2020-09-11T21:01:50.0200717+00:00","temperatureC":18,"temperatureF":64,"summary":"Chilly"},{"date":"2020-09-12T21:01:50.0200719+00:00","temperatureC":16,"temperatureF":60,"summary":"Chilly"}]
```

## <a name="stream-logs-in-real-time"></a>Streamování protokolů v reálném čase

Pomocí následujícího příkazu můžete z aplikace získat protokoly v reálném čase.

```azurecli
az spring-cloud app logs -n hello-world -s <service instance name> -g <resource group name> --lines 100 -f
```

Protokoly se zobrazí ve výstupu:

```output
[Azure Spring Cloud] The following environment variables are loaded:
2020-09-08 20:58:42,432 INFO supervisord started with pid 1
2020-09-08 20:58:43,435 INFO spawned: 'event-gather_00' with pid 9
2020-09-08 20:58:43,436 INFO spawned: 'dotnet-app_00' with pid 10
2020-09-08 20:58:43 [Warning] No managed processes are running. Wait for 30 seconds...
2020-09-08 20:58:44,843 INFO success: event-gather_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
2020-09-08 20:58:44,843 INFO success: dotnet-app_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
←[40m←[32minfo←[39m←[22m←[49m: Steeltoe.Discovery.Eureka.DiscoveryClient[0]
      Starting HeartBeat
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://[::]:1025
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /netcorepublish/6e4db42a-b160-4b83-a771-c91adec18c60
2020-09-08 21:00:13 [Information] [10] Start listening...
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://asc-svc-hello-world.azuremicroservices.io/weatherforecast
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "Get", controller = "WeatherForecast"}. Executing controller action with signature System.Collections.Generic.IEnumerable`1[hello_world.WeatherForecast] Get() on controller hello_world.Controllers.WeatherForecastController (hello-world).
info: Microsoft.AspNetCore.Mvc.Infrastructure.ObjectResultExecutor[1]
      Executing ObjectResult, writing value of type 'hello_world.WeatherForecast[]'.
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action hello_world.Controllers.WeatherForecastController.Get (hello-world) in 1.8902ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 4.2591ms 200 application/json; charset=utf-8
```

> [!TIP]
> Slouží `az spring-cloud app logs -h` k prozkoumávání dalších parametrů a funkcí streamování protokolů.

Pokročilé funkce Log Analytics najdete na kartě **protokoly** v nabídce na [Azure Portal](https://portal.azure.com/). Protokoly mají latenci během několika minut.
[![Analýza ](media/spring-cloud-quickstart-java/logs-analytics.png) protokolů](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
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

* [Nainstalovat JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [Registrace předplatného Azure](https://azure.microsoft.com/free/)
* Volitelné [Nainstalujte Azure CLI verze 2.0.67 nebo vyšší](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) a rozšíření Azure jaře cloudu pomocí příkazu: `az extension add --name spring-cloud`
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
::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

V předchozích krocích jste vytvořili prostředky Azure, které budou nadále účtovat poplatky, i když zůstanou v rámci vašeho předplatného. Pokud neočekáváte, že tyto prostředky budete potřebovat v budoucnu, odstraňte skupinu prostředků z portálu nebo spuštěním následujícího příkazu v rozhraní příkazového řádku Azure CLI:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste se naučili:

> [!div class="checklist"]
> * Vygenerujte základní projekt o jarním cloudu Azure
> * Zřízení instance služby
> * Sestavení a nasazení aplikace s veřejným koncovým bodem
> * Streamování protokolů v reálném čase

Pokud se chcete dozvědět, jak používat další možnosti pružiny Azure, přejděte k sérii rychlý Start, která nasadí ukázkovou aplikaci do Azure jaře cloudu:

> [!div class="nextstepaction"]
> [Sestavování a spouštění mikroslužeb](spring-cloud-quickstart-sample-app-introduction.md)

Další ukázky jsou k dispozici na GitHubu: [ukázky Azure pro jarní Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
