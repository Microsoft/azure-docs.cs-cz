---
title: Azure Application Insights – Automatická kolekce závislostí | Microsoft Docs
description: Application Insights automatické shromažďování a vizualizace závislostí
ms.topic: reference
author: nikmd23
ms.author: nimolnar
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.openlocfilehash: eaafe19f5112b433d50a34aa551aa84d196726a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77665812"
---
# <a name="dependency-auto-collection"></a>Automatické shromažďování závislostí

Níže je uveden aktuálně podporovaný seznam volání závislostí, která jsou automaticky rozpoznána jako závislosti, aniž by bylo nutné provádět další úpravy kódu vaší aplikace. Tyto závislosti jsou vizuální v zobrazení Application Insights [Mapa aplikace](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) a [Diagnostika transakcí](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) . Pokud vaše závislost není v seznamu níže, můžete ji stále sledovat ručně pomocí [volání sledování závislostí](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Aplikační architektury| Verze |
| ------------------------|----------|
| ASP.NET WebForms | 4.5 + |
| ASP.NET MVC | 4 + |
| ASP.NET WebAPI | 4.5 + |
| ASP.NET Core | 1.1 + |
| <b>Komunikační knihovny</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5 +, .NET Core 1.1 + |
| [SqlClient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Core 1.0 +, NuGet 4.3.0 |
| [Klientská sada SDK pro EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [Klientská sada SDK pro ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Klienti úložiště</b>|  |
| ADO.NET | 4.5 + |

## <a name="java"></a>Java
| Aplikační servery | Verze |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Aplikační architektury</b> |  |
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
> <br>† Vyžaduje instalaci [agenta JVM](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

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
| [XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Všechny |

## <a name="next-steps"></a>Další kroky

- Nastavte vlastní sledování závislostí pro [.NET](../../azure-monitor/app/asp-net-dependencies.md).
- Nastavte vlastní sledování závislostí pro [Java](../../azure-monitor/app/java-agent.md).
- Nastavte vlastní sledování závislostí pro [OpenCensus Python](../../azure-monitor/app/opencensus-python-dependency.md).
- [Zápis vlastní telemetrie závislosti](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Viz [datový model](../../azure-monitor/app/data-model.md) pro typy Application Insights a datový model.
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované nástrojem Application Insights.
