---
title: IP adresy, které používá Azure Monitor
description: Výjimky brány firewall serveru vyžadované Application Insights
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 72f825630ec94b5c32a949a4395c431318afa87f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584100"
---
# <a name="ip-addresses-used-by-azure-monitor"></a>IP adresy, které používá Azure Monitor
[Azure monitor](../overview.md) používá několik IP adres. Azure Monitor se skládá z metrik základní platformy a přihlašování k Log Analytics a Application Insights. Pokud je aplikace nebo infrastruktura, kterou sledujete, hostována za bránou firewall, může být potřeba tyto adresy znát.

> [!NOTE]
> I když jsou tyto adresy statické, je možné, že je budete muset kdykoli změnit. Veškerý provoz Application Insights představuje odchozí provoz s výjimkou monitorování dostupnosti a webhooků, které vyžadují pravidla brány firewall pro příchozí připojení.

> [!TIP]
> Pokud používáte skupiny zabezpečení sítě Azure, můžete ke správě přístupu použít [značky síťové služby](../../virtual-network/service-tags-overview.md) Azure. Pokud spravujete přístup pro hybridní a místní prostředky, můžete si stáhnout stejné seznamy IP adres jako [soubory JSON](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) , které se aktualizují každý týden:. Chcete-li pokrýt všechny výjimky v tomto článku, musíte použít značky služby: ' Action ', ' ApplicationInsightsAvailability ', ' AzureMonitor '.

Alternativně se můžete přihlásit k odběru této stránky jako informační kanál RSS přidáním https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom do oblíbeného čtecího modulu RSS/Atom, abyste se dostali k oznámení o nejnovějších změnách.


## <a name="outgoing-ports"></a>Odchozí porty
Musíte otevřít některé Odchozí porty v bráně firewall serveru, aby sada SDK Application Insights a/nebo Monitorování stavu mohla odesílat data na portál:

| Účel | URL | IP adresa | Porty |
| --- | --- | --- | --- |
| Telemetrie |dc.applicationinsights.azure.com<br/>dc.applicationinsights.microsoft.com<br/>dc.services.visualstudio.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248<br/>13.69.66.140<br/>13.77.52.29<br/>51.107.59.180<br/>40.71.12.235<br/>20.44.8.10<br/>40.71.13.169<br/>13.66.141.156<br/>40.71.13.170<br/>13.69.65.23<br/>20.44.17.0<br/>20.36.114.207 <br/>51.116.155.246 <br/>51.107.155.178 <br/>51.140.212.64 <br/>13.86.218.255 <br/>20.37.74.240 <br/>65.52.250.236 <br/>13.69.229.240 <br/>52.236.186.210<br/>52.167.107.65<br/>40.71.12.237<br/>40.78.229.32<br/>40.78.229.33<br/>51.105.67.161<br/>40.124.64.192<br/>20.44.12.194<br/>20.189.172.0<br/>13.69.106.208<br/>40.78.253.199<br/>40.78.253.198<br/>40.78.243.19 | 443 |
| Live Metrics Stream | live.applicationinsights.azure.com<br/>rt.applicationinsights.microsoft.com<br/>rt.services.visualstudio.com|23.96.28.38<br/>13.92.40.198<br/>40.112.49.101<br/>40.117.80.207<br/>157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113| 443 |

## <a name="status-monitor"></a>Monitorování stavu
Monitorování stavu konfigurace – vyžaduje se jenom při provádění změn.

| Účel | URL | IP adresa | Porty |
| --- | --- | --- | --- |
| Konfigurace |`management.core.windows.net` | |`443` |
| Konfigurace |`management.azure.com` | |`443` |
| Konfigurace |`login.windows.net` | |`443` |
| Konfigurace |`login.microsoftonline.com` | |`443` |
| Konfigurace |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| Konfigurace |`auth.gfx.ms` | |`443` |
| Konfigurace |`login.live.com` | |`443` |
| Instalace | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`, `dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>Testy dostupnosti
Toto je seznam adres, ze kterých se spouští [webové testy dostupnosti](./monitor-web-app-availability.md) . Pokud chcete spustit webové testy v aplikaci, ale webový server je omezen na obsluhu konkrétních klientů, pak budete muset povolit příchozí provoz z našich testovacích serverů dostupnosti.


> [!NOTE]
> U prostředků umístěných uvnitř privátních virtuálních sítí, které neumožňují přímou příchozí komunikaci s testovacími agenty dostupnosti ve veřejné službě Azure, je jedinou možností [Vytvoření a hostování vlastních testů dostupnosti](availability-azure-functions.md).

### <a name="service-tag"></a>Značka služby

Pokud používáte skupiny zabezpečení sítě Azure, jednoduše přidejte **pravidlo příchozího portu** , které umožní provoz z Application Insights testů dostupnosti výběrem **označení služby** jako **zdroje** a **ApplicationInsightsAvailability** jako **značky zdrojové služby**.

>[!div class="mx-imgBorder"]
>![V části nastavení vyberte příchozí pravidla zabezpečení a pak v horní části karty vyberte Přidat. ](./media/ip-addresses/add-inbound-security-rule.png)

>[!div class="mx-imgBorder"]
>![Karta přidat příchozí pravidlo zabezpečení](./media/ip-addresses/add-inbound-security-rule2.png)

Otevřete porty 80 (http) a 443 (https) pro příchozí provoz z těchto adres (IP adresy se seskupí podle umístění):

### <a name="addresses-grouped-by-location"></a>Adresy seskupené podle umístění

> [!NOTE]
> Tyto adresy jsou uvedené v seznamu použití zápisu CIDR (Classless Inter-Domain Routing). To znamená, že položka jako `51.144.56.112/28` je rovnocenná 16 IP adres počínaje `51.144.56.112` a končící na `51.144.56.127` .

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

#### <a name="azure-government"></a>Azure Government

Nepotřebujete, pokud jste zákazník veřejného cloudu Azure.

```
USGov Virginia
52.227.229.80/31


USGov Arizona
52.244.35.112/31


USGov Texas
52.243.157.80/31


USDoD Central
52.182.23.96/31


USDoD East
52.181.33.96/31

```

## <a name="application-insights--log-analytics-apis"></a>Rozhraní API pro Application Insights & Log Analytics

| Účel | Identifikátor URI |  IP adresa | Porty |
| --- | --- | --- | --- |
| Rozhraní API |`api.applicationinsights.io`<br/>`api1.applicationinsights.io`<br/>`api2.applicationinsights.io`<br/>`api3.applicationinsights.io`<br/>`api4.applicationinsights.io`<br/>`api5.applicationinsights.io`<br/>`dev.applicationinsights.io`<br/>`dev.applicationinsights.microsoft.com`<br/>`dev.aisvc.visualstudio.com`<br/>`www.applicationinsights.io`<br/>`www.applicationinsights.microsoft.com`<br/>`www.aisvc.visualstudio.com`<br/>`api.loganalytics.io`<br/>`*.api.loganalytics.io`<br/>`dev.loganalytics.io`<br>`docs.loganalytics.io`<br/>`www.loganalytics.io` |20.37.52.188 <br/> 20.37.53.231 <br/> 20.36.47.130 <br/> 20.40.124.0 <br/> 20.43.99.158 <br/> 20.43.98.234 <br/> 13.70.127.61 <br/> 40.81.58.225 <br/> 20.40.160.120 <br/> 23.101.225.155 <br/> 52.139.8.32 <br/> 13.88.230.43 <br/> 52.230.224.237 <br/> 52.242.230.209 <br/> 52.173.249.138 <br/> 52.229.218.221 <br/> 52.229.225.6 <br/> 23.100.94.221 <br/> 52.188.179.229 <br/> 52.226.151.250 <br/> 52.150.36.187 <br/> 40.121.135.131 <br/> 20.44.73.196 <br/> 20.41.49.208 <br/> 40.70.23.205 <br/> 20.40.137.91 <br/> 20.40.140.212 <br/> 40.89.189.61 <br/> 52.155.118.97 <br/> 52.156.40.142 <br/> 23.102.66.132 <br/> 52.231.111.52 <br/> 52.231.108.46 <br/> 52.231.64.72 <br/> 52.162.87.50 <br/> 23.100.228.32 <br/> 40.127.144.141 <br/> 52.155.162.238 <br/> 137.116.226.81 <br/> 52.185.215.171 <br/> 40.119.4.128 <br/> 52.171.56.178 <br/> 20.43.152.45 <br/> 20.44.192.217 <br/> 13.67.77.233 <br/> 51.104.255.249 <br/> 51.104.252.13 <br/> 51.143.165.22 <br/> 13.78.151.158 <br/> 51.105.248.23 <br/> 40.74.36.208 <br/> 40.74.59.40 <br/> 13.93.233.49 <br/> 52.247.202.90 |80,443 |
| Rozšíření pro kanály kanálu Azure |aigs1.aisvc.visualstudio.com |dynamic|443 | 

## <a name="application-insights-analytics"></a>Analýzy Application Insights

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Portál Analytics | analytics.applicationinsights.io | dynamic | 80,443 |
| CDN | applicationanalytics.azureedge.net | dynamic | 80,443 |
| CDN médií | applicationanalyticsmedia.azureedge.net | dynamic | 80,443 |

Poznámka: doména *. applicationinsights.io je vlastněna týmem Application Insights.

## <a name="log-analytics-portal"></a>Portál Log Analytics

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Portál | portal.loganalytics.io | dynamic | 80,443 |
| CDN | applicationanalytics.azureedge.net | dynamic | 80,443 |

Poznámka: doména *. loganalytics.io je vlastněna týmem Log Analytics.

## <a name="application-insights-azure-portal-extension"></a>Rozšíření Application Insights Azure Portal

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Rozšíření Application Insights | stamp2.app.insightsportal.visualstudio.com | dynamic | 80,443 |
| CDN rozšíření Application Insights | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | dynamic | 80,443 |

## <a name="application-insights-sdks"></a>Sady Application Insights SDK

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| CDN SDK pro Application Insights JS | az416426.vo.msecnd.net | dynamic | 80,443 |

## <a name="action-group-webhooks"></a>Webhooky skupiny akcí

Pomocí [příkazu PowerShellu Get-AzNetworkServiceTag](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag)můžete zadat dotaz na seznam IP adres, které používají skupiny akcí.

### <a name="action-groups-service-tag"></a>Značka služby skupin akcí
Správa změn u zdrojových IP adres může trvat poměrně dlouho. Použití **značek služeb** eliminuje nutnost aktualizace konfigurace. Značka služby představuje skupinu předpon IP adres z dané služby Azure. Společnost Microsoft spravuje IP adresy a automaticky aktualizuje značku služby, protože se mění adresy, což eliminuje nutnost aktualizovat pravidla zabezpečení sítě pro skupinu akcí.

1. V Azure Portal v části služby Azure vyhledejte *skupinu zabezpečení sítě*.
2. Klikněte na **Přidat** a vytvořte skupinu zabezpečení sítě.

   1. Přidejte název skupiny prostředků a zadejte *Podrobnosti o instanci*.
   1. Klikněte na tlačítko **Revize + vytvořit** a potom klikněte na tlačítko *vytvořit*.
   
   :::image type="content" source="../alerts/media/action-groups/action-group-create-security-group.png" alt-text="Příklad vytvoření skupiny zabezpečení sítě."border="true":::

3. Přejděte do skupiny prostředků a potom klikněte na *skupinu zabezpečení sítě* , kterou jste vytvořili.

    1. Vyberte *příchozí pravidla zabezpečení*.
    1. Klikněte na **Přidat**.
    
    :::image type="content" source="../alerts/media/action-groups/action-group-add-service-tag.png" alt-text="Příklad přidání značky služby"border="true":::

4. Otevře se nové okno v pravém podokně.
    1.  Vybrat zdroj: **značka služby**
    1.  Značka zdrojové služby: parametr **Action**
    1.  Klikněte na **Přidat**.
    
    :::image type="content" source="../alerts/media/action-groups/action-group-service-tag.png" alt-text="Příklad přidání značky služby"border="true":::


## <a name="profiler"></a>Profiler

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*. agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portál | gateway.azureserviceprofiler.net | dynamic | 443
| Storage | *.core.windows.net | dynamic | 443

## <a name="snapshot-debugger"></a>Ladicí program snímků

> [!NOTE]
> Profiler a Snapshot Debugger sdílejí stejnou sadu IP adres.

| Účel | Identifikátor URI | IP adresa | Porty |
| --- | --- | --- | --- |
| Agent | agent.azureserviceprofiler.net<br/>*. agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| Portál | gateway.azureserviceprofiler.net | dynamic | 443
| Storage | *.core.windows.net | dynamic | 443

