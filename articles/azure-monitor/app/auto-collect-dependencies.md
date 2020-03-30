---
title: Přehledy aplikací Azure – automatické shromažďování závislostí | Dokumenty společnosti Microsoft
description: Application Insights automaticky shromažďuje a vizualizuje závislosti
ms.topic: reference
author: nikmd23
ms.author: nimolnar
ms.date: 04/29/2019
ms.reviewer: mbullwin
ms.openlocfilehash: eaafe19f5112b433d50a34aa551aa84d196726a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77665812"
---
# <a name="dependency-auto-collection"></a>Automatické shromažďování závislostí

Níže je uveden aktuálně podporovaný seznam volání závislostí, které jsou automaticky rozpoznány jako závislosti bez nutnosti dalších úprav kódu aplikace. Tyto závislosti jsou vizualizovány v [mapě aplikace](https://docs.microsoft.com/azure/application-insights/app-insights-app-map) Application Insights a zobrazení [diagnostiky transakcí.](https://docs.microsoft.com/azure/application-insights/app-insights-transaction-diagnostics) Pokud vaše závislost není v seznamu níže, můžete ji stále sledovat ručně pomocí [volání závislosti stopy](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackdependency).

## <a name="net"></a>.NET

| Architektury aplikací| Verze |
| ------------------------|----------|
| ASP.NET webové formuláře | 4,5+ |
| ASP.NET MVC | 4+ |
| ASP.NET WebAPI | 4,5+ |
| ASP.NET Core | 1,1+ |
| <b>Komunikační knihovny</b> |
| [HttpClient](https://www.microsoft.com/net/) | 4.5+, .NET Core 1.1+ |
| [Sqlclient](https://www.nuget.org/packages/System.Data.SqlClient) | .NET Jádro 1.0+, NuGet 4.3.0 |
| [Sada SDK klienta EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs) | 1.1.0 |
| [Sada ServiceBus klienta SDK](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus) | 3.0.0 |
| <b>Klienti úložiště</b>|  |
| ADO.NET | 4,5+ |

## <a name="java"></a>Java
| Aplikační servery | Verze |
|-------------|----------|
| [Tomcat](https://tomcat.apache.org/) | 7, 8 | 
| [JBoss EAP](https://developers.redhat.com/products/eap/download/) | 6, 7 |
| [Jetty](https://www.eclipse.org/jetty/) | 9 |
| <b>Architektury aplikací</b> |  |
| [Spring](https://spring.io/) | 3.0 |
| [Spring Boot](https://spring.io/projects/spring-boot) | 1.5.9+<sup>*</sup> |
| Java Servlet | 3.1+ |
| <b>Komunikační knihovny</b> |  |
| [Klient Apache Http](https://mvnrepository.com/artifact/org.apache.httpcomponents/httpclient) | 4.3+<sup>†</sup> |
| <b>Klienti úložiště</b> | |
| [SQL Server]( https://mvnrepository.com/artifact/com.microsoft.sqlserver/mssql-jdbc) | 1+<sup>†</sup> |
| [PostgreSQL (Beta podpora)](https://github.com/Microsoft/ApplicationInsights-Java/blob/master/CHANGELOG.md#version-240-beta) | |
| [Oracle]( https://www.oracle.com/technetwork/database/application-development/jdbc/downloads/index.html) | 1+<sup>†</sup> |
| [Mysql]( https://mvnrepository.com/artifact/mysql/mysql-connector-java) | 1+<sup>†</sup> |
| <b>Protokolování knihoven</b> | |
| [Přihlašování](https://logback.qos.ch/) | 1+ |
| [Protokol4j](https://logging.apache.org/log4j/) | 1.2+ |
| <b>Knihovny metrik</b> |  |
| JMX | 1.0+ |

> [!NOTE]
> *Kromě reaktivní podpory programování.
> <br>†Vyžaduje instalaci [zařízení JVM Agent](https://docs.microsoft.com/azure/application-insights/app-insights-java-agent#install-the-application-insights-agent-for-java).

## <a name="nodejs"></a>Node.js

| Komunikační knihovny | Verze |
| ------------------------|----------|
| [HTTP](https://nodejs.org/api/http.html), [HTTPS](https://nodejs.org/api/https.html) | 0.10+ |
| <b>Klienti úložiště</b> | |
| [Redis](https://www.npmjs.com/package/redis) | 2.x |
| [MongoDb](https://www.npmjs.com/package/mongodb); [MongoDb Jádro](https://www.npmjs.com/package/mongodb-core) | 2.x - 3.x |
| [Mysql](https://www.npmjs.com/package/mysql) | 2.0.0 - 2.16.x |
| [PostgreSql](https://www.npmjs.com/package/pg); | 6,x - 7,x |
| [pg-bazén](https://www.npmjs.com/package/pg-pool) | 1,x - 2,x |
| <b>Protokolování knihoven</b> | |
| [Konzoly](https://nodejs.org/api/console.html) | 0.10+ |
| [Bunyan (Chauán)](https://www.npmjs.com/package/bunyan) | 1.x |
| [Winston](https://www.npmjs.com/package/winston) | 2.x - 3.x |

## <a name="javascript"></a>JavaScript

| Komunikační knihovny | Verze |
| ------------------------|----------|
| [Požadavek XMLHttpRequest](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest) | Všechny |

## <a name="next-steps"></a>Další kroky

- Nastavte vlastní sledování závislostí pro [rozhraní .NET](../../azure-monitor/app/asp-net-dependencies.md).
- Nastavte vlastní sledování závislostí pro [jazyk Java](../../azure-monitor/app/java-agent.md).
- Nastavte vlastní sledování závislostí pro [OpenCensus Python](../../azure-monitor/app/opencensus-python-dependency.md).
- [Zapsat vlastní telemetrii závislostí](../../azure-monitor/app/api-custom-events-metrics.md#trackdependency)
- Viz [datový model](../../azure-monitor/app/data-model.md) pro typy application insights a datový model.
- Podívejte se na [platformy](../../azure-monitor/app/platforms.md) podporované Application Insights.
