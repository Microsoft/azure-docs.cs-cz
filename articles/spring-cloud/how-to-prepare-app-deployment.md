---
title: Příprava aplikace pro nasazení v jarním cloudu Azure
description: Naučte se, jak připravit aplikaci pro nasazení do jarního cloudu Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: a90d4c2a8649e270365af9b11fa408b865945f92
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104877898"
---
# <a name="prepare-an-application-for-deployment-in-azure-spring-cloud"></a>Příprava aplikace pro nasazení v jarním cloudu Azure

::: zone pivot="programming-language-csharp"
Jarní cloud Azure poskytuje robustní služby pro hostování, monitorování, škálování a aktualizaci aplikace Steeltoe. Tento článek ukazuje, jak připravit existující aplikaci Steeltoe pro nasazení do jarního cloudu Azure. 

Tento článek vysvětluje závislosti, konfiguraci a kód, které jsou nutné ke spuštění aplikace .NET Core Steeltoe ve jarním cloudu Azure. Informace o tom, jak nasadit aplikaci do jarního cloudu Azure, najdete v tématu [nasazení první aplikace pro jarní Cloud v Azure](spring-cloud-quickstart.md).

>[!Note]
> Podpora Steeltoe pro jarní cloud Azure je teď nabízená jako verze Public Preview. Nabídky veřejné verze Preview umožňují zákazníkům experimentovat s novými funkcemi před jejich oficiální verzí.  Funkce a služby verze Public Preview nejsou určeny pro produkční použití.  Další informace o podpoře v rámci verzí Preview najdete v tématu [Nejčastější dotazy](https://azure.microsoft.com/support/faq/) nebo soubor a [support Request](../azure-portal/supportability/how-to-create-azure-support-request.md).

##  <a name="supported-versions"></a>Podporované verze

Azure jarní Cloud podporuje:

* .NET Core 3.1
* Steeltoe 2,4 a 3,0

## <a name="dependencies"></a>Závislosti

Pro Steeltoe 2,4 přidejte do souboru projektu nejnovější balíček [Microsoft. Azure. SpringCloud. Client 1. x. x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) :

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="1.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="2.4.4" />
  <PackageReference Include="Steeltoe.Management.ExporterCore" Version="2.4.4" />
</ItemGroup>
```

Pro Steeltoe 3,0 přidejte do souboru projektu nejnovější balíček [Microsoft. Azure. SpringCloud. Client 2. x. x](https://www.nuget.org/packages/Microsoft.Azure.SpringCloud.Client/) :

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
  <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Extensions.Configuration.ConfigServerCore" Version="3.0.0" />
  <PackageReference Include="Steeltoe.Management.TracingCore" Version="3.0.0" />
</ItemGroup>
```

## <a name="update-programcs"></a>Update program. cs

V `Program.Main` metodě zavolejte `UseAzureSpringCloudService` metodu.

Pro Steeltoe 2.4.4 volejte `UseAzureSpringCloudService` po a po volání metody, `ConfigureWebHostDefaults` `AddConfigServer` Pokud je volána:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer()
        .UseAzureSpringCloudService();
```

Pro Steeltoe 3.0.0 volejte `UseAzureSpringCloudService` před `ConfigureWebHostDefaults` a před libovolným kódem konfigurace Steeltoe:

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .UseAzureSpringCloudService()
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseStartup<Startup>();
        })
        .AddConfigServer();
```

## <a name="enable-eureka-server-service-discovery"></a>Povolit zjišťování služby serveru Eureka

Ve zdroji konfigurace, který se použije při spuštění aplikace ve jarním cloudu Azure, nastavte `spring.application.name` na stejný název jako aplikace Azure pro jarní Cloud, do které se projekt nasadí.

Například pokud nasadíte projekt .NET s názvem `EurekaDataProvider` do jarní cloudové aplikace Azure s názvem `planet-weather-provider` *appSettings.jsv* souboru, měl by obsahovat následující kód JSON:

```json
"spring": {
  "application": {
    "name": "planet-weather-provider"
  }
}
```

## <a name="use-service-discovery"></a>Použití zjišťování služby

Chcete-li volat službu pomocí zjišťování služby serveru Eureka, proveďte požadavky HTTP na `http://<app_name>` místo, kde `app_name` je hodnota `spring.application.name` cílové aplikace. Například následující kód volá `planet-weather-provider` službu:

```csharp
using (var client = new HttpClient(discoveryHandler, false))
{
    var responses = await Task.WhenAll(
        client.GetAsync("http://planet-weather-provider/weatherforecast/mercury"),
        client.GetAsync("http://planet-weather-provider/weatherforecast/saturn"));
    var weathers = await Task.WhenAll(from res in responses select res.Content.ReadAsStringAsync());
    return new[]
    {
        new KeyValuePair<string, string>("Mercury", weathers[0]),
        new KeyValuePair<string, string>("Saturn", weathers[1]),
    };
}
```
::: zone-end

::: zone pivot="programming-language-java"
V tomto tématu se dozvíte, jak připravit existující aplikaci pružiny v jazyce Java pro nasazení do jarního cloudu Azure. Pokud jsou správně nakonfigurovány, poskytuje Azure jarní Cloud robustní služby pro monitorování, škálování a aktualizaci vaší aplikace v Java pro jarní Cloud.

Před spuštěním tohoto příkladu můžete vyzkoušet [základní rychlý Start](spring-cloud-quickstart.md).

Další příklady vysvětlují, jak nasadit aplikaci do jarního cloudu Azure při konfiguraci souboru POM. 
* [Spuštění první aplikace](spring-cloud-quickstart.md)
* [Sestavování a spouštění mikroslužeb](spring-cloud-quickstart-sample-app-introduction.md)

Tento článek vysvětluje požadované závislosti a jejich přidání do souboru POM.

## <a name="java-runtime-version"></a>Běhová verze Java

V Azure jaře cloudu se dají spouštět jenom pružinové a Java aplikace.

Azure jarní Cloud podporuje Java 8 i Java 11. Hostující prostředí obsahuje nejnovější verzi Azul Zulu OpenJDK pro Azure. Další informace o Azul Zulu OpenJDK pro Azure najdete v tématu [instalace JDK](/azure/developer/java/fundamentals/java-jdk-install).

## <a name="spring-boot-and-spring-cloud-versions"></a>Jarní spouštěcí a jarní verze cloudu

K přípravě existující aplikace pro nasazování do Azure jarního cloudu je třeba, aby se v souboru POM aplikace napravily síťové závislosti pružinového a jarního cloudu, jak je znázorněno v následujících oddílech.

Jarní cloud Azure podporuje pouze aplikace pružiny pro spuštění, které jsou ve verzi 2,1 nebo 2,2. V následující tabulce jsou uvedeny podporované kombinace pružinové spouštěcí a jarní cloudové sady:

Jarní spouštěcí verze | Jarní cloudová verze
---|---
2,2 | Hoxton.SR8
2.3 | Hoxton.SR8
2.4.1 + | 2020.0.0

> [!NOTE]
> Zjistili jsme problém se jarním spouštěním 2.4.0 při ověřování TLS mezi vašimi aplikacemi a Eureka, použijte prosím 2.4.1 nebo vyšší. Pokud si nejste s používáním 2.4.0, přečtěte si naše [Nejčastější dotazy týkající](./spring-cloud-faq.md?pivots=programming-language-java#development) se řešení.

### <a name="dependencies-for-spring-boot-version-2223"></a>Závislosti pro jaře Boot verze 2.2/2.3

Pro jaře Booting verze 2,2 přidejte do souboru POM aplikace následující závislosti.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.4.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>Hoxton.SR8</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### <a name="dependencies-for-spring-boot-version-24"></a>Závislosti pro jaře Booting verze 2,4

Pro jaře Booting verze 2,2 přidejte do souboru POM aplikace následující závislosti.

```xml
    <!-- Spring Boot dependencies -->
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.4.1.RELEASE</version>
    </parent>

    <!-- Spring Cloud dependencies -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>2020.0.0</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

> [!WARNING]
> Nezadávejte `server.port` v konfiguraci. U jarního cloudu Azure se toto nastavení overide na pevné číslo portu. Respektujte prosím toto nastavení a v kódu nespecifikujte port serveru.

## <a name="other-recommended-dependencies-to-enable-azure-spring-cloud-features"></a>Další doporučené závislosti pro povolení funkcí Azure jarního cloudu

Pokud chcete pro distribuované trasování povolit integrované funkce Azure jarního cloudu z registru služby, musíte do své aplikace zahrnout taky následující závislosti. Některé z těchto závislostí můžete odstranit, pokud nepotřebujete odpovídající funkce pro konkrétní aplikace.

### <a name="service-registry"></a>Registr služby

Pokud chcete použít spravovanou službu Azure Service Registry, zahrňte `spring-cloud-starter-netflix-eureka-client` závislost do souboru pom.xml, jak je znázorněno zde:

```xml
    <dependency>
        <groupId>org.springframework.cloud</groupId>
        <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
    </dependency>
```

Koncový bod serveru registru služby se do vaší aplikace automaticky vloží jako proměnné prostředí. Aplikace se mohou registrovat pomocí serveru registru služby a zjišťovat další závislé mikroslužby.


#### <a name="enablediscoveryclient-annotation"></a>EnableDiscoveryClient anotace

Přidejte následující anotaci do zdrojového kódu aplikace.
```java
@EnableDiscoveryClient
```
Podívejte se například na aplikaci piggymetrics z předchozích příkladů:
```java
package com.piggymetrics.gateway;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.netflix.zuul.EnableZuulProxy;

@SpringBootApplication
@EnableDiscoveryClient
@EnableZuulProxy

public class GatewayApplication {
    public static void main(String[] args) {
        SpringApplication.run(GatewayApplication.class, args);
    }
}
```

### <a name="distributed-configuration"></a>Distribuovaná konfigurace

Pokud chcete povolit distribuovanou konfiguraci, zahrňte `spring-cloud-config-client` do části závislosti v souboru pom.xml následující závislost:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-client</artifactId>
</dependency>
```

> [!WARNING]
> Nezadávejte `spring.cloud.config.enabled=false` v konfiguraci Bootstrap. V opačném případě přestane vaše aplikace pracovat s konfiguračním serverem.

### <a name="metrics"></a>Metriky

Zahrňte `spring-boot-starter-actuator` závislost do oddílu závislosti v souboru pom.xml, jak je znázorněno zde:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

 Metriky jsou pravidelně načítány z koncových bodů JMX. Metriky můžete vizualizovat pomocí Azure Portal.

 > [!WARNING]
 > Zadejte prosím `spring.jmx.enabled=true` vlastnost Configuration. V opačném případě se metriky nedají vizuálně vyAzure Portal.

### <a name="distributed-tracing"></a>Distribuované trasování

Musíte taky povolit instanci Azure Application Insights, abyste mohli pracovat s instancí cloudové služby Azure na jaře. Informace o tom, jak používat Application Insights s využitím služby Azure Pramenitého cloudu, najdete v [dokumentaci k distribuovanému trasování](spring-cloud-tutorial-distributed-tracing.md).

#### <a name="spring-boot-2223"></a>Jarní spuštění 2.2/2.3
`spring-cloud-starter-sleuth` `spring-cloud-starter-zipkin` Do oddílu závislosti v souboru pom.xml zahrňte následující a závislosti:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-sleuth</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zipkin</artifactId>
</dependency>
```

#### <a name="spring-boot-24"></a>Jarní spuštění 2,4
`spring-cloud-sleuth-zipkin`Do části závislosti v souboru pom.xml zahrňte následující závislost:

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-sleuth-zipkin</artifactId>
</dependency>
```

## <a name="see-also"></a>Viz také
* [Analýza protokolů a metrik aplikace](./diagnostic-services.md)
* [Nastavení konfiguračního serveru](spring-cloud-tutorial-config-server.md)
* [Použití distribuovaného trasování u jarního cloudu Azure](spring-cloud-tutorial-distributed-tracing.md)
* [Průvodce rychlým startem pružiny](https://spring.io/quickstart)
* [Dokumentace ke jarnímu spuštění](https://spring.io/projects/spring-boot)

## <a name="next-steps"></a>Další kroky

V tomto tématu jste zjistili, jak nakonfigurovat svoji aplikaci v jazyce Java pro nasazení do jarního cloudu Azure. Informace o tom, jak nastavit instanci konfiguračního serveru, najdete v tématu [Nastavení instance konfiguračního serveru](spring-cloud-tutorial-config-server.md).

Další ukázky jsou k dispozici na GitHubu: [ukázky Azure pro jarní Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).
::: zone-end