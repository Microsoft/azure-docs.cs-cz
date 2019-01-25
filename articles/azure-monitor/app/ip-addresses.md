---
title: IP adresy používané službou Application Insights a Log Analytics | Dokumentace Microsoftu
description: Výjimky brány firewall serveru vyžaduje Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 44d989f8-bae9-40ff-bfd5-8343d3e59358
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/22/2019
ms.author: mbullwin
ms.openlocfilehash: 3522cf6315e420cefbf17068cf7bc020e83c9019
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883241"
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>IP adresy používané službou Application Insights a Log Analytics
[Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) služba používá počet IP adres. Můžete potřebovat znát tyto adresy, pokud je aplikace, kterou monitorujete hostována za bránou firewall.

> [!NOTE]
> I když tyto adresy jsou statické, je možné, že budeme muset změnit čas od času. Veškerý provoz Application Insights představuje odchozí provoz s výjimkou monitorování dostupnosti a webhooky, které jsou potřebné pravidla firewallu pro příchozí provoz.
> 
> 

> [!TIP]
> Přihlásit se k odběru této stránky jako RSS přidáním https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom vaše oblíbené čtečku RSS/ATOM upozorňování nejnovější změny.
> 
> 

## <a name="outgoing-ports"></a>Odchozí porty
Je třeba otevřít některé Odchozí porty v bráně firewall serveru povolit sadu Application Insights SDK a sledování stavu k odesílání dat do portálu:

| Účel | zprostředkovatele identity | IP adresa | Porty |
| --- | --- | --- | --- |
| Telemetrická data |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233 | 443 |
| Live Metrics Stream |rt.services.visualstudio.com<br/>rt.applicationinsights.microsoft.com |23.96.28.38<br/>13.92.40.198 |443 |

## <a name="status-monitor"></a>Monitorování stavu
Stav monitoru konfigurace – je potřeba pouze při provádění změn.

| Účel | zprostředkovatele identity | IP adresa | Porty |
| --- | --- | --- | --- |
| Konfigurace |`management.core.windows.net` | |`443` |
| Konfigurace |`management.azure.com` | |`443` |
| Konfigurace |`login.windows.net` | |`443` |
| Konfigurace |`login.microsoftonline.com` | |`443` |
| Konfigurace |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Konfigurace |`auth.gfx.ms` | |`443` |
| Konfigurace |`login.live.com` | |`443` |
| Instalace |`packages.nuget.org` , `nuget.org`, `api.nuget.org`, `az320820.vo.msecnd.net` (NuGet soubory ke stažení) | |`443` |

## <a name="availability-tests"></a>Testy dostupnosti
Toto je seznam adres, ze kterých [testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md) jsou spuštěny. Pokud chcete spustit testy webu ve vaší aplikaci, ale váš webový server je omezen na poskytování konkrétních klientů, budete muset povolit příchozí provoz z našich dostupnost testovací servery.

Otevřete porty 80 (http) a 443 (https) pro příchozí provoz z těchto adres (IP adresy jsou seskupené podle umístění):

```
Australia East
13.70.83.252
13.75.150.96
13.75.153.9
13.75.158.185
104.210.65.220
52.187.246.13
52.147.30.74
52.187.250.193

Brazil South
191.232.32.122
191.232.172.45
191.232.176.218
191.232.191.225
191.232.192.35
191.232.199.76
191.232.236.210
191.237.249.118

France South
52.136.140.221
52.136.140.222
52.136.140.223
52.136.140.226

France Central
52.143.140.242
52.143.140.246
52.143.140.247
52.143.140.249
40.89.137.100
40.89.142.126
40.89.131.237
40.89.136.180
40.89.139.142
40.89.129.184

East Asia
13.75.121.122
23.99.115.153
23.99.123.38
23.102.232.186
52.175.38.49
52.175.39.103
13.75.66.249
13.70.1.3

North Europe
13.74.184.101
13.74.185.160
40.69.200.198
52.164.224.46
52.169.12.203
52.169.14.11
52.169.237.149
52.178.183.105
40.112.90.148
40.112.94.212
104.46.15.57
40.115.125.114
40.127.205.106
40.113.2.95

Japan East
52.243.33.33
52.243.33.141
52.243.35.253
52.243.41.117
13.78.35.173
13.78.34.107

West Europe
52.174.166.113
52.174.178.96
52.174.31.140
52.174.35.14
52.178.104.23
52.178.109.190
52.178.111.139
52.233.166.221
23.100.10.236
23.100.6.155
52.232.113.84
51.144.113.219
137.117.164.63
104.40.145.255
23.97.139.23
23.97.181.233


UK South
51.140.79.229
51.140.84.172
51.140.87.211
51.140.105.74
51.140.164.254
51.140.4.10
51.140.29.140
51.140.138.114

UK West
51.141.25.219
51.141.32.101
51.141.35.167
51.141.54.177
51.140.240.239
51.140.205.236
51.140.245.132
51.140.203.56
51.140.242.38
51.140.205.207
51.140.223.169
51.140.221.13

Southeast Asia
52.187.29.7
52.187.179.17
52.187.76.248
52.187.43.24
52.163.57.91
52.187.30.120
13.67.74.82
52.163.184.26
104.215.188.85
13.67.34.253

West US
104.45.228.236
104.45.237.251
13.64.152.110
13.64.156.54
13.64.232.251
13.64.236.105
13.91.94.59
40.118.131.182
40.83.189.192
40.83.215.122
104.42.39.222
104.42.145.220
104.42.60.160
104.42.248.11
40.83.163.29
104.42.195.57
40.78.19.163
40.78.23.43
104.210.55.156
40.118.239.57
40.118.225.199
104.42.199.138
40.83.213.165
40.118.230.208

Central US
52.165.130.58
52.173.142.229
52.173.147.190
52.173.17.41
52.173.204.247
52.173.244.190
52.173.36.222
52.176.1.226
104.43.251.84
40.113.236.73
40.113.230.234
40.113.195.109
104.43.215.218
104.43.240.112
168.61.186.7
23.101.121.118
168.61.217.112
168.61.222.30
40.113.223.10
40.113.217.146


North Central US
23.96.247.139
23.96.249.113
52.162.124.242
52.162.126.139
52.162.241.147
52.162.246.222
52.162.247.136
52.237.153.231
52.237.154.216
52.237.156.14
52.237.157.218
52.237.157.37
65.52.205.196
23.100.75.146
65.52.63.179
157.55.143.58
23.101.175.168
23.101.166.53
65.52.204.52
157.55.163.96
65.52.207.6
157.55.208.50

South Central US
104.210.145.106
13.84.176.24
13.84.49.16
13.85.11.137
13.85.26.248
13.85.69.145
52.171.136.162
52.171.141.253
52.171.57.172
52.171.58.140
40.124.36.120
104.210.216.32
104.215.75.92
104.215.77.186
104.210.211.148
104.215.116.120
13.66.16.58
104.214.72.169
104.215.101.235
40.124.43.29

East US
13.82.218.95
13.90.96.71
13.90.98.52
13.92.137.70
40.85.187.235
40.87.61.61
52.168.8.247
52.170.38.79
52.170.80.61
52.179.9.26
104.41.133.69
137.117.103.13
40.114.75.45
40.121.8.31
168.62.41.234
168.62.168.66
23.101.134.53
137.117.66.148
137.116.112.153
168.62.161.181
168.61.48.246
40.121.5.6

```  

## <a name="application-insights-api"></a>Rozhraní API služby Application Insights
| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Rozhraní API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Dokumenty k rozhraní API |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Interní rozhraní API |aigs.aisvc.visualstudio.com<br/>aigs1.aisvc.visualstudio.com<br/>aigs2.aisvc.visualstudio.com<br/>aigs3.aisvc.visualstudio.com<br/>aigs4.aisvc.visualstudio.com<br/>aigs5.aisvc.visualstudio.com<br/>aigs6.aisvc.visualstudio.com |Dynamické|443 |

## <a name="log-analytics-api"></a>Rozhraní API služby log Analytics

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Rozhraní API |api.loganalytics.io<br/>*.api.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Dokumenty k rozhraní API |dev.loganalytics.io<br/>docs.loganalytics.io<br/>www.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |

## <a name="application-insights-analytics"></a>Application Insights Analytics

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Portál Analytics | analytics.applicationinsights.io | Dynamické | 80,443 |
| CDN | applicationanalytics.azureedge.net | Dynamické | 80,443 |
| Media CDN | applicationanalyticsmedia.azureedge.net | Dynamické | 80,443 |

Note: *.applicationinsights.io domain is owned by Application Insights team.

## <a name="log-analytics-portal"></a>Portál log Analytics

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Portál | portal.loganalytics.io | Dynamické | 80,443 |
| CDN | applicationanalytics.azureedge.net | Dynamické | 80,443 |

Poznámka: *. vlastní domény loganalytics.io týmu Log Analytics.

## <a name="application-insights-azure-portal-extension"></a>Portál Application Insights Azure rozšíření

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Rozšíření Application Insights | stamp2.app.insightsportal.visualstudio.com | Dynamické | 80,443 |
| Rozšíření Application Insights CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | Dynamické | 80,443 |

## <a name="application-insights-sdks"></a>Sady SDK služby Application Insights

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Application Insights JS SDK CDN | az416426.vo.msecnd.net | Dynamické | 80,443 |
| Application Insights Java SDK | aijavasdk.blob.core.windows.net | Dynamické | 80,443 |

## <a name="alert-webhooks"></a>Webhooky pro výstrahy

| Účel | IP adresa | Porty
| --- | --- | --- |
| Zobrazení výstrah | 23.96.11.4 | 443 |

## <a name="profiler"></a>Profiler

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 40.68.32.221<br/>40.85.246.0<br/>40.85.246.57<br/>40.117.252.0<br/>40.117.253.100<br/>51.140.140.162<br/>51.140.140.184<br/>51.143.96.206<br/>51.143.98.157<br/>52.161.8.88<br/>52.161.29.225<br/>52.178.147.66<br/>52.178.149.106<br/>52.230.122.9<br/>52.230.124.46<br/>104.40.217.71<br/>104.211.89.26<br/>104.211.90.232 | 443
| Portál | gateway.azureserviceprofiler.net | Dynamické | 443
| Storage | *.core.windows.net | Dynamické | 443

## <a name="snapshot-debugger"></a>Ladicí program snímků

> [!NOTE]
> Profiler a Snapshot Debugger sdílejí stejnou sadu IP adres.

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Agent | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 40.68.32.221<br/>40.85.246.0<br/>40.85.246.57<br/>40.117.252.0<br/>40.117.253.100<br/>51.140.140.162<br/>51.140.140.184<br/>51.143.96.206<br/>51.143.98.157<br/>52.161.8.88<br/>52.161.29.225<br/>52.178.147.66<br/>52.178.149.106<br/>52.230.122.9<br/>52.230.124.46<br/>104.40.217.71<br/>104.211.89.26<br/>104.211.90.232 | 443
| Portál | ppe.gateway.azureserviceprofiler.net | Dynamické | 443
| Storage | *.core.windows.net | Dynamické | 443
