---
title: IP adresy, které používá Application Insights a Log Analytics | Microsoft Docs
description: Výjimky brány firewall serveru vyžadované Application Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 44d989f8-bae9-40ff-bfd5-8343d3e59358
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: mbullwin
ms.openlocfilehash: fad16ab9b556a1aa802cd9f84a06a88e61fd84b3
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744435"
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>IP adresy, které používá Application Insights a Log Analytics
Služba [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) používá několik IP adres. Pokud je aplikace, kterou sledujete, hostovaná za bránou firewall, může být potřeba tyto adresy znát.

> [!NOTE]
> I když jsou tyto adresy statické, je možné, že je budete muset kdykoli změnit. Veškerý provoz Application Insights představuje odchozí provoz s výjimkou monitorování dostupnosti a webhooků, které vyžadují pravidla brány firewall pro příchozí připojení.
> 
> 

> [!TIP]
> Přihlaste se k odběru této stránky jako informačního kanálu RSS přidáním https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom do oblíbeného čtecího modulu RSS/Atom, abyste se dostali k oznámení o nejnovějších změnách.
> 
> 

## <a name="outgoing-ports"></a>Odchozí porty
Musíte otevřít některé Odchozí porty v bráně firewall serveru, aby sada SDK Application Insights a/nebo Monitorování stavu mohla odesílat data na portál:

| Účel | URL | IP | Porty |
| --- | --- | --- | --- |
| Telemetrie |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.162.117<br/>40.73.171.20<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.203.248 | 443 |
| Live Metrics Stream |rt.services.visualstudio.com<br/>rt.applicationinsights.microsoft.com |23.96.28.38<br/>13.92.40.198 |443 |

## <a name="status-monitor"></a>Monitorování stavu
Monitorování stavu konfigurace – vyžaduje se jenom při provádění změn.

| Účel | URL | IP | Porty |
| --- | --- | --- | --- |
| Konfiguraci |`management.core.windows.net` | |`443` |
| Konfiguraci |`management.azure.com` | |`443` |
| Konfiguraci |`login.windows.net` | |`443` |
| Konfiguraci |`login.microsoftonline.com` | |`443` |
| Konfiguraci |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Konfiguraci |`auth.gfx.ms` | |`443` |
| Konfiguraci |`login.live.com` | |`443` |
| Instalace | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`,`dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>Testy dostupnosti
Toto je seznam adres, ze kterých se spouští [webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) . Pokud chcete spustit webové testy v aplikaci, ale webový server je omezen na obsluhu konkrétních klientů, pak budete muset povolit příchozí provoz z našich testovacích serverů dostupnosti.

Otevřete porty 80 (http) a 443 (https) pro příchozí provoz z těchto adres (IP adresy se seskupí podle umístění):

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

```  

## <a name="application-insights-api"></a>Rozhraní API pro Application Insights
| Účel | Identifikátor URI | IP | Porty |
| --- | --- | --- | --- |
| rozhraní API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Dokumentace k rozhraní API |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Rozšíření pro kanály kanálu Azure |aigs1.aisvc.visualstudio.com |dynamická|443 |

## <a name="log-analytics-api"></a>Rozhraní API pro Log Analytics

| Účel | Identifikátor URI | IP | Porty |
| --- | --- | --- | --- |
| rozhraní API |api.loganalytics.io<br/>*.api.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |
| Dokumentace k rozhraní API |dev.loganalytics.io<br/>docs.loganalytics.io<br/>www.loganalytics.io |23.96.58.253<br/>13.78.151.158<br/>40.74.59.40<br/>40.70.42.246<br/>40.117.198.0<br/>137.116.226.91<br/>52.163.88.44<br/>52.189.210.240<br/>13.77.201.34<br/>13.78.149.206<br/>52.232.28.146<br/>52.175.241.170<br/>20.36.36.66<br/>52.147.29.101<br/>40.115.155.252<br/>20.188.34.152<br/>52.141.32.103 |80,443 |

## <a name="application-insights-analytics"></a>Analýzy Application Insights

| Účel | Identifikátor URI | IP | Porty |
| --- | --- | --- | --- |
| Portál Analytics | analytics.applicationinsights.io | dynamická | 80,443 |
| CDN | applicationanalytics.azureedge.net | dynamická | 80,443 |
| CDN médií | applicationanalyticsmedia.azureedge.net | dynamická | 80,443 |

Poznámka: doména *. applicationinsights.io je vlastněna týmem Application Insights.

## <a name="log-analytics-portal"></a>Portál Log Analytics

| Účel | Identifikátor URI | IP | Porty |
| --- | --- | --- | --- |
| Portál | portal.loganalytics.io | dynamická | 80,443 |
| CDN | applicationanalytics.azureedge.net | dynamická | 80,443 |

Poznámka: doména *. loganalytics.io je vlastněna týmem Log Analytics.

## <a name="application-insights-azure-portal-extension"></a>Rozšíření Application Insights Azure Portal

| Účel | Identifikátor URI | IP | Porty |
| --- | --- | --- | --- |
| Rozšíření Application Insights | stamp2.app.insightsportal.visualstudio.com | dynamická | 80,443 |
| CDN rozšíření Application Insights | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dynamická | 80,443 |

## <a name="application-insights-sdks"></a>Sady Application Insights SDK

| Účel | Identifikátor URI | IP | Porty |
| --- | --- | --- | --- |
| CDN SDK pro Application Insights JS | az416426.vo.msecnd.net | dynamická | 80,443 |
| Application Insights Java SDK | aijavasdk.blob.core.windows.net | dynamická | 80,443 |

## <a name="alert-webhooks"></a>Webhooky výstrahy

| Účel | IP | Porty
| --- | --- | --- |
| Zobrazení výstrah | 23.96.11.4 | 443 |

## <a name="profiler"></a>Profiler

| Účel | Identifikátor URI | IP | Porty |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73 | 443
| Portál | gateway.azureserviceprofiler.net | dynamická | 443
| Storage | *.core.windows.net | dynamická | 443

## <a name="snapshot-debugger"></a>Snapshot Debugger

> [!NOTE]
> Profiler a Snapshot Debugger sdílejí stejnou sadu IP adres.

| Účel | Identifikátor URI | IP | Porty |
| --- | --- | --- | --- |
| Agent | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73 | 443
| Portál | ppe.gateway.azureserviceprofiler.net | dynamická | 443
| Storage | *.core.windows.net | dynamická | 443
