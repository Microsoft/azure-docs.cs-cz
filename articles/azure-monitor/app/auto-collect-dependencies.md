---
title: Azure Application Insights – automatického sběru závislost | Dokumentace Microsoftu
description: Application Insights automaticky shromažďovat a vizualizace závislostí
services: application-insights
documentationcenter: .net
author: nikmd23
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: reference
ms.date: 08/13/2018
ms.reviewer: mbullwin
ms.author: nimolnar
ms.openlocfilehash: 68a504f43463a25a0aba8d7d72881cddbcfba816
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2019
ms.locfileid: "53999335"
---
# <a name="dependency-auto-collection"></a>Automatické shromažďování závislostí

Níže je seznam aktuálně podporovaných volání závislostí, které jsou automaticky zjištěny jako závislosti nevyžaduje žádné další změny kódu aplikace. To se skládá z odchozích volání komunikačních knihoven, klienti úložiště, protokolování a metriky knihovny, jakož i příchozí volání do aplikační architektury a servery. Ve službě Application Insights jsou vizualizována tyto závislosti [Mapa aplikace](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) a [Diagnostika transakcí](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) zobrazení. Pokud vaše závislosti není v níže uvedeném seznamu, můžete dál sledovat ručně pomocí [sledování volání závislosti](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Aplikační architektury| Verze |
| ------------------------|----------|
| Webových formulářů ASP.NET | 4.5 + |
| ASP.NET MVC | 4 + |
| ASP.NET WebAPI | 4.5 + |
| Jádro ASP.NET | 1.1 + |
| <b> Komunikačních knihoven</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5 +, .NET core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET core 1.0 +, NuGet 4.3.0 |
| [EventHubs Client SDK](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [Klientská sada SDK pro služby Service Bus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Klienti úložiště</b>|  |
| ADO.NET | 4.5 + |
| <b>Protokolování knihovny</b> |  |
| ILogger | 1.1 + |
| System.Diagnostics.Trace | 4.5 + |
| [nLog](https://www.nuget.org/packages/NLog/) | 4.4.12+ |
| [log4net](https://www.nuget.org/packages/log4net/) | 2.0.8+ na NetStandard 1.3, 2.0.6+ na rozhraní .NET 4.5 + |

## <a name="java"></a>Java
| Servery aplikace | Verze |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Aplikační architektury </b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1 + |
| <b>Komunikačních knihoven</b> |  |
| [Klient Apache Http](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3 +<sup>†</sup> |
| <b>Klienti úložiště</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1 +<sup>†</sup> |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1 +<sup>†</sup> |
| [MySql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1 +<sup>†</sup> |
| <b>Protokolování knihovny</b> | |
| [Logback](https://logback.qos.ch/) | 1 + |
| [Log4j](https://logging.apache.org/log4j/) | 1.2 + |
| <b>Metriky knihovny</b> |  |
| JMX | 1.0 + |

> [!NOTE]
> * S výjimkou reaktivního programování na podporu.
> <br>Instalace †requires [JVM agenta](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Komunikačních knihoven | Verze |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0.10 + |
| <b>Klienti úložiště</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Core](https://www.npmjs.com/package/mongodb-core) | 2.0.0 - 2.3.0 |
| [MySQL](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.14.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6.x |
| [fond PG](https://www.npmjs.com/package/pg-pool) | 1.x |
| <b>Protokolování knihovny</b> | |
| [Konzoly](https://nodejs.org/api/console.html) | 0.10 + |
| [Bunyan](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winstona](https://www.npmjs.com/package/winston) | 2.x |

## <a name="javascript"></a>JavaScript

| Komunikačních knihoven | Verze |
| ------------------------|----------|
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Vše |

## <a name="next-steps"></a>Další postup

- Nastavit vlastní závislost sledování [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Nastavit vlastní závislost sledování [Java](../../azure-monitor/app/java-agent.md).
- [Napište vlastní závislost telemetrii](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Zobrazit [datový model](../../azure-monitor/app/data-model.md) pro typy a datový model Application Insights.
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované službou Application Insights.
