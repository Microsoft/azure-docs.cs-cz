---
title: Kolekce IP adres Azure Application Insights | Microsoft Docs
description: Porozumění způsobu, jakým jsou zpracovávány IP adresy a geografická umístění s využitím Azure Application Insights
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: c7a4506c6a4246edc007a5ea2158998b472ec316
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85807124"
---
# <a name="geolocation-and-ip-address-handling"></a>Geografická poloha a zpracování IP adres

V tomto článku se dozvíte, jak se k vyhledávání geografického umístění a manipulaci s IP adresou dochází v Application Insights spolu s postupem, jak změnit výchozí chování.

## <a name="default-behavior"></a>Výchozí chování

Ve výchozím nastavení jsou dočasně shromažďovány IP adresy, ale nejsou uloženy v Application Insights. Základní proces je tento:

IP adresy se odesílají do Application Insights jako součást dat telemetrie. Po dosažení koncového bodu příjmu v Azure se IP adresa používá k prohledání geografického umístění pomocí [GeoLite2 z Maxmind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Výsledky tohoto vyhledávání slouží k naplnění následujících polí, `client_City` `client_StateOrProvince` , `client_CountryOrRegion` . V tomto okamžiku se IP adresa zahodí a `0.0.0.0` do pole se zapíše `client_IP` .

* Telemetrie prohlížeče: dočasně shromáždíme IP adresu odesílatele. IP adresa se počítá pomocí koncového bodu pro přijímání.
* Telemetrie serveru: modul Application Insights dočasně shromažďuje IP adresu klienta. Není shromažďována, pokud `X-Forwarded-For` je nastavena.

Toto chování je záměrné, což umožňuje vyhnout se zbytečné kolekci osobních údajů. Kdykoli je to možné, doporučujeme vyhnout se shromažďování osobních údajů. 

## <a name="overriding-default-behavior"></a>Přepsání výchozího chování

I když je výchozím chováním minimalizace shromažďování osobních údajů, stále nabízíme flexibilitu pro shromažďování a ukládání dat IP adres. Než se rozhodnete ukládat osobní údaje, jako jsou IP adresy, důrazně doporučujeme ověřit, že se neruší žádné požadavky na dodržování předpisů ani místní předpisy, na které se může vztahovat. Další informace o zpracování osobních údajů v Application Insights najdete v [pokynech k osobním údajům](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt).

## <a name="storing-ip-address-data"></a>Ukládání dat IP adresy

Aby bylo možné povolit shromažďování a ukládání IP adres, `DisableIpMasking` musí být vlastnost Application Insights komponenty nastavena na `true` . Tuto vlastnost lze nastavit buď prostřednictvím Azure Resource Manager šablon, nebo voláním REST API. 

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

Pokud potřebujete pouze změnit chování pro jeden Application Insights prostředek, nejjednodušší způsob, jak to provést, je prostřednictvím Azure Portal.  

1. Projděte si šablonu pro **Settings**  >  **Export** nastavení > prostředků Application Insights. 

    ![Exportovat šablonu](media/ip-collection/export-template.png)

2. Vybrat **nasazení**

    ![Tlačítko nasadit zvýrazněné červeně](media/ip-collection/deploy.png)

3. Vyberte **Upravit šablonu**. (Pokud má vaše šablona další vlastnosti nebo prostředky, které se v této ukázkové šabloně nezobrazují, postupujte opatrně a zajistěte, aby všechny prostředky přijímaly nasazení šablony jako přírůstkové změny nebo aktualizace.)

    ![Úprava šablony](media/ip-collection/edit-template.png)

4. Proveďte následující změny ve formátu JSON pro váš prostředek a potom klikněte na **Uložit**:

    ![Snímek obrazovky přidá čárku za "IbizaAIExtension" a přidá nový řádek pod "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Pokud dojde k chybě, která říká: ** _Skupina prostředků je v umístění, které není podporováno jedním nebo více prostředky v šabloně. Zvolte prosím jinou skupinu prostředků._** Dočasně vyberte jinou skupinu prostředků z rozevíracího seznamu a pak znovu vyberte původní skupinu prostředků, abyste mohli chybu vyřešit.

5. Vyberte **Souhlasím**s  >  **nákupem**. 

    ![Úprava šablony](media/ip-collection/purchase.png)

    V tomto případě se nic nekupuje, jenom aktualizujeme konfiguraci existujícího prostředku Application Insights.

6. Po dokončení nasazení se budou zaznamenávat nová data telemetrie.

    Pokud byste chtěli znovu vybrat a upravit šablonu, měli byste zobrazit jenom výchozí šablonu a nevidíte nově přidanou vlastnost a její přidruženou hodnotu. Pokud nevidíte data IP adresy a chcete potvrdit, že `"DisableIpMasking": true` je nastavená. Spusťte následující PowerShell: (nahraďte `Fabrikam-dev` odpovídajícím názvem prostředku a skupiny prostředků.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    V důsledku toho se vrátí seznam vlastností. Jedna z vlastností by měla být přečtena `DisableIpMasking: true` . Pokud spustíte PowerShell před nasazením nové vlastnosti pomocí Azure Resource Manager, vlastnost neexistuje.

### <a name="rest-api"></a>Rozhraní REST API

Datová část [rozhraní REST API](https://docs.microsoft.com/rest/api/azure/) pro provádění stejných úprav je následující:

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

Pokud potřebujete pružnější alternativu než `DisableIpMasking` při zaznamenávání všech nebo částí IP adres, můžete pomocí [inicializátoru telemetrie](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) zkopírovat veškerou nebo část IP adresy do vlastního pole. 

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
> Pokud nemůžete získat přístup `ISupportProperties` , zkontrolujte a ujistěte se, že používáte nejnovější stabilní verzi Application Insights SDK. `ISupportProperties`jsou určené pro vysoké hodnoty mohutnosti, zatímco `GlobalProperties` jsou vhodnější pro hodnoty nízké mohutnosti, jako je název oblasti, název prostředí atd. 

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

### <a name="client-side-javascript"></a>JavaScript na straně klienta

Na rozdíl od sad SDK na straně serveru nevypočítá sada JavaScript SDK na straně klienta IP adresu. Ve výchozím nastavení se výpočet IP adresy pro telemetrii na straně klienta provádí v koncovém bodu příjmu v Azure po doručení telemetrie. To znamená, že pokud jste odeslali data na straně klienta na proxy server a pak předáte do koncového bodu pro příjem dat, může se na základě výpočtu IP adres zobrazovat IP adresa proxy serveru, ne klienta. Pokud se nepoužije žádný proxy server, nemělo by se jednat o problém.

Pokud chcete vypočítat IP adresu přímo na straně klienta, je třeba přidat vlastní logiku pro provedení tohoto výpočtu a použít výsledek k nastavení `ai.location.ip` značky. Pokud `ai.location.ip` je nastavená, neprovádí se výpočet IP adresy pomocí koncového bodu ingestování a je dodržena zadaná IP adresa, která se používá k provádění geografického vyhledávání. V tomto scénáři bude ve výchozím nastavení tato IP adresa pořád nulová. 

Pokud chcete zachovat celou IP adresu vypočítanou z vlastní logiky, můžete použít inicializátor telemetrie, který by zkopíroval data IP adresy, která jste zadali do `ai.location.ip` samostatného vlastního pole. Ale na rozdíl od sad SDK na straně serveru, aniž byste se museli spoléhat na knihovny třetích stran nebo na vlastní logiku shromažďování IP adres na straně klienta, sada SDK na straně klienta nevypočítá IP adresu za vás.    


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

### <a name="view-the-results-of-your-telemetry-initializer"></a>Zobrazit výsledky inicializátoru telemetrie

Pokud pak na svém webu aktivujete nový provoz a počkejte přibližně 2-5 minut, než se zajistěte, aby se zajistilo jejich ingestování, můžete spustit dotaz Kusto a zjistit, jestli shromažďování IP adres funguje:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nově shromážděné IP adresy by se měly zobrazit ve `customDimensions_client-ip` sloupci. Výchozí `client-ip` sloupec bude mít stále všechny 4 oktety buď vyplněné, nebo jenom první tři oktety v závislosti na tom, jak jste nakonfigurovali kolekci IP adres na úrovni součásti. Pokud testujete místně po implementaci inicializátoru telemetrie a hodnota, kterou vidíte, se jedná o `customDimensions_client-ip` `::1` očekávané chování. `::1`představuje adresu zpětné smyčky v protokolu IPv6. Je ekvivalentní s `127.0.01` protokolem IPv4 a je to výsledek, který se zobrazí při testování z místního hostitele.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [shromažďování osobních údajů](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) v Application Insights.

* Přečtěte si další informace o tom, jak [shromažďování IP adres](https://apmtips.com/posts/2016-07-05-client-ip-address/) v Application Insights funguje. (Jedná se o starší externí Blogový příspěvek napsaný jedním z našich inženýrů. Předchází aktuálnímu výchozímu chování, při kterém je zaznamenána IP adresa `0.0.0.0` , ale má větší hloubku v rámci integrovaného mechanismu `ClientIpHeaderTelemetryInitializer` .)
