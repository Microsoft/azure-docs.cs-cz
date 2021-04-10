---
title: Kolekce IP adres Azure Application Insights | Microsoft Docs
description: Porozumění způsobu, jakým jsou zpracovávány IP adresy a geografická umístění s využitím Azure Application Insights
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js
ms.openlocfilehash: 91b3aa07720e39aa8aeeceb9c35e38205e7d7c76
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584077"
---
# <a name="geolocation-and-ip-address-handling"></a>Geografická poloha a zpracování IP adres

Tento článek vysvětluje, jak funguje vyhledávání geografického umístění a zpracování IP adres ve Application Insights společně s postupem, jak změnit výchozí chování.

## <a name="default-behavior"></a>Výchozí chování

Ve výchozím nastavení jsou dočasně shromažďovány IP adresy, ale nejsou uloženy v Application Insights. Základní proces je tento:

Po odeslání telemetrie do Azure se IP adresa použije k vyhledání geografického umístění pomocí [GeoLite2 z Maxmind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Výsledky tohoto vyhledávání slouží k naplnění polí `client_City` , `client_StateOrProvince` a `client_CountryOrRegion` . Adresa se pak zahodí a `0.0.0.0` zapíše se do tohoto `client_IP` pole.

* Telemetrie prohlížeče: dočasně shromáždíme IP adresu odesílatele. IP adresa se počítá pomocí koncového bodu pro přijímání.
* Telemetrie serveru: modul telemetrie Application Insights dočasně shromažďuje IP adresu klienta. IP adresa není shromážděna lokálně při `X-Forwarded-For` nastavení hlavičky.

Toto chování je záměrné, což umožňuje vyhnout se zbytečné kolekci osobních údajů. Kdykoli je to možné, doporučujeme vyhnout se shromažďování osobních údajů. 

## <a name="overriding-default-behavior"></a>Přepsání výchozího chování

Výchozí hodnota není shromažďovat IP adresy. Pořád nabízíme flexibilitu pro přepsání tohoto chování. Nicméně doporučujeme ověřit, že kolekce neruší žádné požadavky na dodržování předpisů ani místní předpisy. 

Další informace o zpracování osobních údajů v Application Insights najdete v [pokynech k osobním údajům](../logs/personal-data-mgmt.md).

## <a name="storing-ip-address-data"></a>Ukládání dat IP adresy

Chcete-li povolit shromažďování a ukládání IP adres, `DisableIpMasking` musí být vlastnost Application Insights komponenty nastavena na `true` . Tuto vlastnost lze nastavit prostřednictvím šablon Azure Resource Manager nebo voláním REST API. 

### <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portál 

Pokud potřebujete pouze změnit chování pro jeden prostředek Application Insights, použijte Azure Portal. 

1. Projděte si šablonu Application Insights prostředků > **Automation**  >  **Export** 

2. Vybrat **nasazení**

    ![Tlačítko se zvýrazněným slovem "nasadit" červeně](media/ip-collection/deploy.png)

3. Vyberte **Upravit šablonu**.

    ![Tlačítko se zvýrazněným slovem "Upravit" červeně](media/ip-collection/edit-template.png)

4. Proveďte následující změny ve formátu JSON pro váš prostředek a potom vyberte **Uložit**:

    ![Snímek obrazovky přidá čárku za "IbizaAIExtension" a přidá nový řádek pod "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Pokud dojde k chybě, která říká: **_Skupina prostředků je v umístění, které není podporováno jedním nebo více prostředky v šabloně. Zvolte prosím jinou skupinu prostředků._** Dočasně vyberte jinou skupinu prostředků z rozevíracího seznamu a pak znovu vyberte původní skupinu prostředků, abyste mohli chybu vyřešit.

5. Vyberte **Souhlasím** s  >  **nákupem**. 

    ![Zaškrtnuté políčko s slovy Souhlasím s uvedenými podmínkami a ujednáními, které jsou uvedené výše, zvýrazněné červeně nad tlačítkem "koupit", zvýrazněné červeně.](media/ip-collection/purchase.png)

    V takovém případě nic nového nekoupíte. Aktualizujeme jenom konfiguraci existujícího prostředku Application Insights.

6. Po dokončení nasazení se budou zaznamenávat nová data telemetrie.

    Pokud znovu vyberete a upravíte šablonu, zobrazí se jenom výchozí šablona bez nově přidané vlastnosti. Pokud nevidíte data IP adres a chcete potvrdit, že `"DisableIpMasking": true` je nastavená, spusťte následující PowerShell: 
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    V důsledku toho se vrátí seznam vlastností. Jedna z vlastností by měla být přečtena `DisableIpMasking: true` . Pokud spustíte PowerShell před nasazením nové vlastnosti pomocí Azure Resource Manager, vlastnost neexistuje.

### <a name="rest-api"></a>REST API

Datová část [rozhraní REST API](/rest/api/azure/) pro provádění stejných úprav je následující:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Inicializátor telemetrie

Pokud potřebujete pružnější alternativu než `DisableIpMasking` , můžete použít [inicializátor telemetrie](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) ke zkopírování všech nebo částí IP adres do vlastního pole. 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet--aspnet-core"></a>ASP.NET/ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Pokud nemůžete získat přístup `ISupportProperties` , zkontrolujte a ujistěte se, že používáte nejnovější stabilní verzi Application Insights SDK. `ISupportProperties` jsou určené pro vysoké hodnoty mohutnosti, zatímco `GlobalProperties` jsou vhodnější pro hodnoty nízké mohutnosti, jako je název oblasti, název prostředí atd. 

### <a name="enable-telemetry-initializer-for-aspnet"></a>Povolit inicializátor telemetrie pro ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Povolit inicializátor telemetrie pro ASP.NET Core

Inicializátor telemetrie můžete vytvořit stejným ASP.NET Core způsobem jako ASP.NET jako, ale chcete-li povolit inicializátor, použijte následující příklad pro referenci:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[JavaScript na straně klienta](#tab/javascript)

### <a name="client-side-javascript"></a>JavaScript na straně klienta

Na rozdíl od sad SDK na straně serveru, sada JavaScript SDK na straně klienta nepočítá IP adresu. Ve výchozím nastavení se počítá IP adresa pro telemetrii na straně klienta v rámci koncového bodu ingestování v Azure. 

Pokud chcete vypočítat IP adresu přímo na straně klienta, je nutné přidat vlastní logiku a použít výsledek k nastavení `ai.location.ip` značky. Pokud `ai.location.ip` je nastavená, neprovádí se výpočet IP adresy pomocí koncového bodu příjmu a zadaná IP adresa se používá pro vyhledávání geografického umístění. V tomto scénáři bude ve výchozím nastavení tato IP adresa pořád nulová. 

Chcete-li zachovat celou IP adresu vypočítanou z vlastní logiky, můžete použít inicializátor telemetrie, který by zkopíroval data IP adresy, která jste zadali do `ai.location.ip` samostatného vlastního pole. Ale na rozdíl od sad SDK na straně serveru, aniž byste se museli spoléhat na knihovny třetích stran nebo vlastní logiku vlastní kolekce, vám sada SDK na straně klienta nevypočítá tuto adresu.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

Pokud data na straně klienta procházejí proxy serverem před přesměrováním na koncový bod ingestování, může se při výpočtu IP adresy zobrazit IP adresa proxy serveru a nikoli klient. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>Zobrazit výsledky inicializátoru telemetrie

Pokud odešlete nový provoz do vaší lokality a počkejte několik minut. Pak můžete spustit dotaz pro potvrzení, že shromažďování funguje:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nově shromážděné IP adresy se zobrazí ve `customDimensions_client-ip` sloupci. Výchozí `client-ip` sloupec bude mít stále všechny čtyři oktety, které jsou buď vypočítány. 

Při testování z místního hostitele a hodnoty pro `customDimensions_client-ip` je `::1` očekávané chování této hodnoty. `::1` představuje adresu zpětné smyčky v protokolu IPv6. Je ekvivalentní s `127.0.0.1` protokolem IPv4.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [shromažďování osobních údajů](../logs/personal-data-mgmt.md) v Application Insights.

* Přečtěte si další informace o tom, jak [shromažďování IP adres](https://apmtips.com/posts/2016-07-05-client-ip-address/) v Application Insights funguje. (Tento článek popisuje starší externí Blogový příspěvek napsaný jedním z našich inženýrů. Předchází aktuálnímu výchozímu chování, při kterém je zaznamenána IP adresa `0.0.0.0` , ale má větší hloubku v rámci integrovaného mechanismu `ClientIpHeaderTelemetryInitializer` .)
