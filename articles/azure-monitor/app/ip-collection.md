---
title: Kolekce IP adres Azure Application Insights | Dokumenty společnosti Microsoft
description: Principy způsobu zpracování IP adres a geografické polohy pomocí Azure Application Insights
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 969061ec89ddd0f13caa675bc324207c6c5d8843
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656513"
---
# <a name="geolocation-and-ip-address-handling"></a>Zpracování geolokace a IP adres

Tento článek vysvětluje, jak geolocation vyhledávání a zpracování IP adres dojít v Application Insights spolu s jak změnit výchozí chování.

## <a name="default-behavior"></a>Výchozí chování

Ve výchozím nastavení jsou adresy IP dočasně shromažďovány, ale nejsou uloženy v Application Insights. Základní proces je tento:

IP adresy jsou odesílány do Application Insights jako součást telemetrických dat. Po dosažení koncového bodu ingestování v Azure se IP adresa používá k provedení geografického vyhledávání pomocí [GeoLite2 z MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Výsledky tohoto vyhledávání se používají k vyplnění `client_City` `client_StateOrProvince`následujících polí , . `client_CountryOrRegion`. V tomto okamžiku je adresa IP `0.0.0.0` zahozena `client_IP` a zapsána do pole.

* Telemetrie prohlížeče: Dočasně shromažďujeme IP adresu odesílatele. IP adresa se vypočítá podle koncového bodu ingestování.
* Telemetrie serveru: Modul Application Insights dočasně shromažďuje IP adresu klienta. Není shromažďována, `X-Forwarded-For` pokud je nastavena.

Toto chování je záměrné, aby se zabránilo zbytečnému shromažďování osobních údajů. Pokud je to možné, doporučujeme vyhnout se shromažďování osobních údajů. 

## <a name="overriding-default-behavior"></a>Přepsání výchozího chování

Zatímco výchozí chování je minimalizovat shromažďování osobních údajů, stále nabízíme flexibilitu při shromažďování a ukládání dat IP adres. Než se rozhodnete ukládat jakékoli osobní údaje, jako jsou IP adresy, důrazně doporučujeme ověřit, zda tím nejsou splněny žádné požadavky na dodržování předpisů nebo místní předpisy, které vám mohou podléhat. Chcete-li se dozvědět více o zpracování osobních údajů v Application Insights, podívejte se na [pokyny pro osobní údaje](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt).

## <a name="storing-ip-address-data"></a>Ukládání dat IP adres

Chcete-li povolit shromažďování a `DisableIpMasking` ukládání IP adres, musí `true`být vlastnost komponenty Application Insights nastavena na . Tuto vlastnost lze nastavit buď prostřednictvím šablon Azure Resource Manager nebo voláním rozhraní REST API. 

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

Pokud potřebujete upravit pouze chování pro jeden prostředek Application Insights nejjednodušší způsob, jak toho dosáhnout, je prostřednictvím portálu Azure.  

1. Přejít na prostředek Application Insights >**šablona exportu** **nastavení** >  

    ![Exportovat šablonu](media/ip-collection/export-template.png)

2. Vybrat **nasazení**

    ![Tlačítko Nasadit zvýrazněné červeně](media/ip-collection/deploy.png)

3. Vyberte **Upravit šablonu**. (Pokud šablona obsahuje další vlastnosti nebo prostředky, které se nezobrazují v této ukázkové šabloně, postupujte opatrně a ujistěte se, že všechny prostředky budou přijímat nasazení šablony jako přírůstkovou změnu nebo aktualizaci.)

    ![Úprava šablony](media/ip-collection/edit-template.png)

4. Proveďte následující změny v zařízení json pro váš zdroj a klikněte na **Uložit**:

    ![Snímek obrazovky přidá čárku za "IbizaAIExtension" a přidat nový řádek níže s "DisableIpMasking": true](media/ip-collection/save.png)

    > [!WARNING]
    > Pokud dojde k chybě, která říká: ** _Skupina prostředků je v umístění, které není podporováno jedním nebo více prostředků v šabloně. Zvolte jinou skupinu prostředků._** Dočasně vyberte jinou skupinu prostředků z rozevíracího souboru a potom znovu vyberte původní skupinu prostředků k vyřešení chyby.

5. Vyberte **Souhlasím** > **Nákup**. 

    ![Úprava šablony](media/ip-collection/purchase.png)

    V tomto případě se nekupuje nic nového, právě aktualizujeme konfiguraci existujícího prostředku Application Insights.

6. Po dokončení nasazení se zaznamenají nová telemetrická data.

    Pokud byste znovu vybrali a upravili šablonu, viděli byste pouze výchozí šablonu a nezobrazovali byste nově přidanou vlastnost a její přidruženou hodnotu. Pokud nevidíte data IP adresy a chcete `"DisableIpMasking": true` potvrdit, že je nastavena. Spusťte následující Prostředí `Fabrikam-dev` PowerShell: (Nahraďte příslušným názvem prostředků a skupiny prostředků.)
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    V důsledku toho bude vrácen seznam vlastností. Jedna z vlastností `DisableIpMasking: true`by měla číst . Pokud spustíte Prostředí PowerShell před nasazením nové vlastnosti pomocí Správce prostředků Azure, vlastnost nebude existovat.

### <a name="rest-api"></a>Rozhraní API pro odpočinek

Datová část [rozhraní Rest API](https://docs.microsoft.com/rest/api/azure/) pro provádění stejných úprav je následující:

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

Pokud potřebujete flexibilnější `DisableIpMasking` alternativu než k zaznamenání všech nebo části adres IP, můžete použít [telemetrický inicializátor](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#addmodify-properties-itelemetryinitializer) ke kopírování celé IP adresy nebo její části do vlastního pole. 

### <a name="aspnet--aspnet-core"></a>ASP.NET / ASP.NET jádro

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
> Pokud se vám `ISupportProperties`nedaří získat přístup , zkontrolujte a ujistěte se, že používáte nejnovější stabilní verzi sady Application Insights SDK. `ISupportProperties`jsou určeny pro vysoké hodnoty `GlobalProperties` mohutnosti, zatímco jsou vhodnější pro nízké hodnoty mohutnosti, jako je název oblasti, název prostředí atd. 

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

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>Povolení inicializátoru telemetrie pro ASP.NET core

Můžete vytvořit telemetrie inicializátor stejným způsobem pro ASP.NET Core jako ASP.NET ale povolit inicializátor, použijte následující příklad pro odkaz:

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

Na rozdíl od sad SDK na straně serveru sada Javascript sdk klienta nevypočítá adresu IP. Ve výchozím nastavení výpočet IP adresy pro telemetrii na straně klienta se provádí v koncovém bodě ingestování v Azure při doručení telemetrie. To znamená, že pokud jste odesílali data na straně klienta na proxy server a pak přesměrovávali do koncového bodu ingestování, může výpočet IP adresy zobrazit IP adresu proxy serveru a nikoli klienta. Pokud není použit žádný proxy server, nemělby to být problém.

Chcete-li vypočítat adresu IP přímo na straně klienta, budete muset přidat vlastní logiku k `ai.location.ip` provedení tohoto výpočtu a použít výsledek k nastavení značky. Je-li `ai.location.ip` nastavena, výpočet IP adresy není proveden koncovým bodem ingestování a zadaný IP adresa je dodržena a použita pro provádění geografické vyhledávání. V tomto scénáři bude adresa IP ve výchozím nastavení stále vynulována. 

Chcete-li zachovat celou adresu IP vypočtenou z vlastní logiky, můžete použít inicializátor telemetrie, která by zkopírovala data IP adresy, která jste `ai.location.ip` zadali, do samostatného vlastního pole. Ale opět na rozdíl od sady SDK na straně serveru, bez spoléhání se na knihovny třetích stran nebo vlastní logiku shromažďování IP adres na straně klienta sada SDK na straně klienta nevypočítá adresu IP za vás.    


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

### <a name="view-the-results-of-your-telemetry-initializer"></a>Zobrazení výsledků inicializátoru telemetrie

Pokud potom spustíte nový provoz proti vašemu webu a počkejte přibližně 2-5 minut, abyste zajistili, že má čas na přijetí, můžete spustit dotaz Kusto a zjistit, zda kolekce IP adres funguje:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Nově shromážděné IP adresy by `customDimensions_client-ip` se měly objevit ve sloupci. Ve `client-ip` výchozím sloupci budou stále vynulovány všechny 4 oktety nebo se zobrazují pouze první tři oktety v závislosti na tom, jak jste nakonfigurovali kolekci IP adres na úrovni komponenty. Pokud testujete místně po implementaci inicializátoru telemetrie a hodnota, kterou vidíte, `customDimensions_client-ip` je `::1` to očekávané chování. `::1`představuje adresu zpětné smyčky v iPv6. Je ekvivalentní `127.0.01` v IPv4 a je výsledek uvidíte při testování z localhost.

## <a name="next-steps"></a>Další kroky

* Další informace o [shromažďování osobních údajů](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt) najdete v Application Insights.

* Přečtěte si další informace o [tom,](https://apmtips.com/blog/2016/07/05/client-ip-address/) jak funguje kolekce IP adres v Application Insights. (Jedná se o starší externí blogový příspěvek napsaný jedním z našich inženýrů. Předchází aktuální výchozí chování, kde `0.0.0.0`IP adresa je zaznamenána jako , ale jde `ClientIpHeaderTelemetryInitializer`do větší hloubky na mechaniku stavěný z-do .)
