---
title: Azure Application Insights – Automatická kolekce závislostí | Microsoft Docs
description: Application Insights automatické shromažďování a vizualizace závislostí
ms.topic: reference
ms.custom: devx-track-dotnet
ms.date: 05/06/2020
ms.openlocfilehash: 8a4d79e52465e93fb4db2625217cb37a06917218
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91930862"
---
# <a name="dependency-auto-collection"></a>Automatické shromažďování závislostí

Níže je uveden aktuálně podporovaný seznam volání závislostí, která jsou automaticky rozpoznána jako závislosti, aniž by bylo nutné provádět další úpravy kódu vaší aplikace. Tyto závislosti jsou vizuální v zobrazení Application Insights [Mapa aplikace](./app-map.md) a [Diagnostika transakcí](./transaction-diagnostics.md) . Pokud vaše závislost není v seznamu níže, můžete ji stále sledovat ručně pomocí [volání sledování závislostí](./api-custom-events-metrics.md#trackdependency).

## <a name="net"></a>.NET

| Aplikační architektury| Verze |
| ------------------------|----------|
| ASP.NET WebForms | 4.5 + |
| ASP.NET MVC | 4 + |
| ASP.NET WebAPI | 4.5 + |
| ASP.NET Core | 1.1 + |
| <b> Komunikační knihovny</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5 +, .NET Core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0 +, NuGet 4.3.0 |
| [Microsoft. data. SqlClient](https://www.nuget.org/packages/Microsoft.Data.SqlClient/1.1.2)| 1.1.0 – nejnovější stabilní verze. (Viz poznámka níže)
| [Klientská sada SDK pro EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [Klientská sada SDK pro ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Klienti úložiště</b>|  |
| ADO.NET | 4.5 + |

> [!NOTE]
> Došlo k [známému problému](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1347) se staršími verzemi Microsoft. data. SqlClient. Pro zmírnění tohoto problému doporučujeme používat 1.1.0 nebo novější. Entity Framework Core nemusí nutně dodávat nejnovější stabilní verzi Microsoft. data. SqlClient, proto doporučujeme potvrdit, že máte aspoň 1.1.0, abyste se tomuto problému vyhnuli.   


## <a name="java"></a>Java
| Aplikační servery | Verze |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Aplikační architektury </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9 +<sup>*</sup> |
| Java servlet | 3.1 + |
| <b>Komunikační knihovny</b> |  |
| [Klient Apache http](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Klienti úložiště</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [PostgreSQL (podpora beta verze)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Knihovny protokolování</b> | |
| [Logback](https://logback.qos.ch/) | 1 + |
| [Log4j](https://logging.apache.org/log4j/) | 1,2 + |
| <b>Knihovny metrik</b> |  |
| JMX | 1,0 + |

> [!NOTE]
> * S výjimkou reaktivní podpory programování.
> <br>† Vyžaduje instalaci [agenta JVM](./java-agent.md#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Komunikační knihovny | Verze |
| ------------------------|----------|
| [http](https://nodejs.org/api/http.html), [https](https://nodejs.org/api/https.html) | 0.10 + |
| <b>Klienti úložiště</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDB](https://www.npmjs.com/package/mongodb); [Jádro MongoDB](https://www.npmjs.com/package/mongodb-core) | 2. x-3. x |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0-2.16. x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6. x-7. x |
| [PG – fond](https://www.npmjs.com/package/pg-pool) | 1. x-2. x |
| <b>Knihovny protokolování</b> | |
| [stromu](https://nodejs.org/api/console.html) | 0.10 + |
| [Bunyan](https://www.npmjs.com/package/bunyan) | verze |
| [Winston](https://www.npmjs.com/package/winston) | 2. x-3. x |

## <a name="javascript"></a>JavaScript

| Komunikační knihovny | Verze |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Vše |

## <a name="next-steps"></a>Další kroky

- Nastavte vlastní sledování závislostí pro [.NET](./asp-net-dependencies.md).
- Nastavte vlastní sledování závislostí pro [Java](./java-agent.md).
- Nastavte vlastní sledování závislostí pro [OpenCensus Python](./opencensus-python-dependency.md).
- [Zápis vlastní telemetrie závislosti](./api-custom-events-metrics.md#trackdependency)
- Viz [datový model](./data-model.md) pro typy Application Insights a datový model.
- Podívejte se na [platformy](./platforms.md) podporované nástrojem Application Insights.

