---
title: Přidat ID korelace pro zprávy IoT s distribuované trasování (preview)
description: ''
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.openlocfilehash: d97171003507ea0d7412c0706f9deea02fe06c0d
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418534"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Trasování zpráv typu zařízení cloud Azure IoT s distribuované trasování (preview)

Microsoft Azure IoT Hub v současné době podporuje jako distribuované trasování [funkce ve verzi preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

IoT Hub je jedním z první služby Azure pro podporu distribuované trasování. Jak dalších služeb Azure podporují distribuované trasování, budete mít trasovací zprávy IoT v rámci služby Azure zahrnuté ve vašem řešení. Další informace o distribuované trasování, naleznete v tématu [distribuované trasování](../azure-monitor/app/distributed-tracing.md).

Povolení distribuované trasování pro službu IoT Hub vám umožňuje:

- Přesně sledovat průběh jednotlivých zpráv prostřednictvím služby IoT Hub pomocí [trasování kontextu](https://github.com/w3c/trace-context). Tento kontext trasování zahrnuje ID, které umožňují korelaci událostí mezi jednotlivými komponentami s událostmi z jiné součásti korelace. Můžete ho použít pro podmnožinu nebo všechny zprávy typu zařízení IoT pomocí [dvojče zařízení](iot-hub-devguide-device-twins.md).
- Automatické protokolování trasování kontext, který má [diagnostické protokoly Azure monitoru](iot-hub-monitor-resource-health.md).
- Měřit a chápat tok zpráv a latence ze zařízení do služby IoT Hub a směrování koncových bodů.
- Spuštění, vzhledem k tomu, jak chcete implementujte distribuované trasování pro služby mimo Azure v rámci vašeho řešení IoT.

V tomto článku budete používat [zařízení Azure IoT SDK pro jazyk C](./iot-hub-device-sdk-c-intro.md) s distribuované trasování. Podpora distribuované trasování je stále probíhá pro jiné sady SDK.

## <a name="prerequisites"></a>Požadavky

- Náhled distribuované trasování je momentálně podporována pouze pro vytvoření centra IoT hub v těchto oblastech:

  - **Severní Evropa**
  - **Jihovýchodní Asie**
  - **USA – západ 2**

- Tento článek předpokládá, že jste obeznámeni s odesílání telemetrických dat do služby IoT hub. Ujistěte se, že jste dokončili [odesílání telemetrických dat C Quickstart](./quickstart-send-telemetry-c.md).

- Zaregistrujte si zařízení ve vaší službě IoT hub (kroky k dispozici v každé rychlý start) a poznamenejte připojovací řetězec.

- Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Konfigurace centra IoT

V této části nakonfigurujete službu IoT Hub do protokolu distribuované trasování atributy (ID korelace a časové razítko).

1. Přejděte do centra IoT [webu Azure portal](https://portal.azure.com/).

1. V levém podokně centra IoT, přejděte dolů k položce **monitorování** části a klikněte na tlačítko **nastavení diagnostiky**.

1. Pokud již nejsou zapnuté nastavení diagnostiky, klikněte na tlačítko **zapnout diagnostiku**. Pokud už jste povolili nastavení diagnostiky, klikněte na tlačítko **přidejte nastavení diagnostiky**.

1. V **název** pole, zadejte název nové nastavení diagnostiky. Například **DistributedTracingSettings**.

1. Vyberte jednu nebo více z následujících možností, které určují, kde se odešlou protokolování:

    - **Archivovat do účtu úložiště**: Nakonfigurujte účet úložiště, který obsahuje informace o protokolování.
    - **Stream do centra událostí**: Konfigurace centra událostí obsahuje informace o protokolování.
    - **Odeslání do Log Analytics**: Konfigurovat pracovní prostor log analytics obsahuje informace o protokolování.

1. V **protokolu** vyberte operace, které chcete informace o protokolování.

    Nezapomeňte uvést **DistributedTracing**a nakonfigurovat **uchování** kolik dní má protokolování zachovány. Uchovávání protokolů ovlivní náklady na úložiště.

    ![Snímek obrazovky ukazující, kde je DistributedTracing kategorii pro nastavení diagnostiky IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Klikněte na tlačítko **Uložit** nové nastavení.

1. (Volitelné) Zobrazit zprávy tok na různých místech, nastavit [pravidel směrování, která měly aspoň dva různé koncové body](iot-hub-devguide-messages-d2c.md).

Po zapnutí protokolování služby IoT Hub záznamy protokolu zprávu obsahující vlastnosti platný trasování vyskytne v některém z následujících situací:

- Zprávy dorazí ve službě IoT Hub gateway.
- Zpráva zpracování ve službě IoT Hub.
- Zpráva směruje do vlastní koncové body. Směrování musí být povolena.

Další informace o těchto protokolů a jejich schémata, naleznete v tématu [distribuované trasování v diagnostické protokoly služby IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Nainstalovat zařízení

V této části Příprava vývojového prostředí pro použití se službou [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Potom upravte jednou z ukázek povolit distribuované trasování na vaše zařízení telemetrické zprávy.

Tyto pokyny jsou určené pro sestavení ukázky na Windows. Jiné prostředí, najdete v části [zkompilujte sadu SDK C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) nebo [balené C SDK pro konkrétní vývojové platformy](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Klonování zdrojového kódu a inicializace

1. Nainstalujte ["vývoj desktopových aplikací pomocí C++"](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2017) pro Visual Studio 2015 nebo 2017.

1. Nainstalujte [CMake](https://cmake.org/). Ujistěte se, že je ve vaší `PATH` zadáním `cmake -version` z příkazového řádku.

1. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkaz pro naklonování úložiště GitHub sady [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c):

    ```cmd
    git clone https://github.com/Azure/azure-iot-sdk-c.git --recursive -b public-preview
    ```

    Velikost tohoto úložiště je aktuálně přibližně 220 MB. Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

1. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky.

    ```cmd
    cd azure-iot-sdk-c    
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Pokud `cmake` nejde najít váš kompilátor C++, může při spuštění výše uvedeného příkazu dojde k chybám sestavení. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

    Po úspěšném sestavení by posledních pár řádků výstupu mělo vypadat přibližně takto:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Upravit ukázkové odesílání telemetrie povolit distribuované trasování

1. Chcete-li otevřít pomocí editoru `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` zdrojový soubor.

1. Vyhledejte deklaraci konstanty `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Nahraďte hodnotu `connectionString` konstantní připojovacím řetězcem zařízení jste si poznamenali z v [zaregistrovat zařízení](./quickstart-send-telemetry-c.md#register-a-device) část [odesílání telemetrických dat C Quickstart](./quickstart-send-telemetry-c.md).

1. Změnit `MESSAGE_COUNT` definují `5000`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Vyhledejte řádek kódu, který volá `IoTHubDeviceClient_LL_SetConnectionStatusCallback` zaregistrovat funkci zpětného volání stav připojení před odesílací smyčky zpráv. Přidejte kód pod tento řádek, jak je znázorněno níže volat `IoTHubDeviceClient_LL_EnablePolicyConfiguration` povolení distribuované trasování pro zařízení:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    `IoTHubDeviceClient_LL_EnablePolicyConfiguration` Funkce povolí zásady pro konkrétní funkce IOT hub, které jsou nakonfigurované přes [dvojčata zařízení](./iot-hub-devguide-device-twins.md). Jednou `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` je povolená s řádkem kódu výše, bude odrážet trasování chování zařízení distribuované trasování změny provedené ve dvojčeti zařízení.

1. Aby vaše ukázková aplikace spuštěná bez použití do vaší kvóty, přidání zpoždění sekundových na konci odesílací smyčky zpráv:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Kompilace a spuštění

1. Přejděte *iothub_ll_telemetry_sample* adresář projektu v adresáři CMake (`azure-iot-sdk-c/cmake`) jste vytvořili dříve a zkompilovat ukázkový:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Spusťte aplikaci. Zařízení odesílá telemetrická data podporující distribuované trasování.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Nechte aplikaci spuštěnou. Volitelně můžete sledujte zpráv odesílaných do služby IoT Hub zobrazením okna konzoly.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="using-third-party-clients"></a>Pomocí třetí strany klientů

Pokud nepoužíváte SDK pro jazyk C a přesto chcete pro službu IoT Hub ve verzi preview distribuované trasování, vytvořit zprávu tak, aby obsahovala `tracestate` vlastnost aplikace s časem vytvoření zprávy ve formátu unix timestamp. Například, `tracestate=timestamp=1539243209`. K řízení procento zprávy obsahující tuto vlastnost, implementujte logiku pro naslouchání událostem jako je aktualizace dvojčete s iniciované cloudu.

## <a name="update-sampling-options"></a>Možnosti vzorkování aktualizace 

Chcete-li změnit procento zprávy k trasování z cloudu, je nutné aktualizovat dvojče zařízení. Můžete provést toto více způsoby včetně JSON editor v portálu a sady SDK služby IoT Hub. Následující pododdíl obsahují příklady.

### <a name="update-using-the-portal"></a>Aktualizovat pomocí portálu

1. Přejděte do služby IoT hub v [webu Azure portal](https://portal.azure.com/), pak klikněte na tlačítko **zařízení IoT**.

1. Klikněte na své zařízení.

1. Vyhledejte **povolit distribuované trasování (preview)** a pak vyberte **povolit**.

    ![Povolit distribuované trasování na webu Azure portal](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Zvolte **vzorkovací frekvenci** mezi 0 a 100 %.

1. Klikněte na **Uložit**.

1. Počkejte několik sekund a stiskněte tlačítko **aktualizovat**, pak pokud úspěšně projdou potvrzením většinovým zařízení, zobrazí se synchronizační ikonu se zaškrtnutím.

1. Vraťte se do okna konzoly pro aplikaci telemetrické zprávy. Zobrazí se zprávy s `tracestate` ve vlastnostech aplikace.

    ![Sledování stavu](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Volitelné) Změna míry vzorkování na jinou hodnotu a sledovat změny v frekvencí, která zprávy zahrnují `tracestate` ve vlastnostech aplikace.

### <a name="update-using-azure-iot-hub-toolkit-for-vs-code"></a>Aktualizace pomocí sady Azure IoT Hub Toolkit pro VS Code

1. Instalace Visual Studio Code a potom nainstalujte nejnovější verzi sady Azure IoT Hub Toolkit pro VS Code z [tady](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

1. Otevřít VS Code a [nastavit připojovací řetězec služby IoT Hub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Rozbalte zařízení a vyhledejte **distribuované trasování nastavení (Preview)**. Klikněte **aktualizace distribuované trasování nastavení (Preview)** sub uzlu.

    ![Povolit distribuované trasování v sadě Azure IoT Hub Toolkit](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. V automaticky otevíraném okně vyberte **povolit**, stiskněte klávesu Enter k potvrzení 100 jako vzorkovací frekvenci.

    ![Režim vzorkování aktualizace](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Vzorkovací frekvence aktualizace ](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Hromadná aktualizace pro různá zařízení

Chcete-li aktualizovat konfiguraci odběru vzorků distribuované trasování pro různá zařízení, použijte [automatické konfigurace](iot-hub-auto-device-config.md). Ujistěte se, že postupujte podle tohoto schématu dvojčete:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Název elementu | Požaduje se | Typ | Popis |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Ano | Integer | Dvě hodnoty režimu se momentálně podporují pro vypnutí a zapnutí odběru vzorků. `1` zapnutý a `2` je vypnuté. |
| `sampling_rate` | Ano | Integer | Tato hodnota je procento. Pouze hodnoty z `0` k `100` (včetně) nejsou povoleny.  |

## <a name="query-and-visualize"></a>Dotazování a vizualizace

Pokud chcete zobrazit všechna trasování zaznamenané služby IoT Hub, dotaz protokolu úložiště, které jste vybrali v nastavení diagnostiky. Tato část vás provede několik různých možností.

### <a name="query-using-log-analytics"></a>Dotazování pomocí Log Analytics

Pokud jste nastavili [Log Analytics s využitím diagnostických protokolů](../azure-monitor/platform/diagnostic-logs-stream-log-store.md), tím, že hledají protokoly v dotazu `DistributedTracing` kategorie. Například tento dotaz zobrazí všechna trasování přihlášení:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Příklad protokoly, jak je znázorněno v Log Analytics:

| TimeGenerated | OperationName | Kategorie | Úroveň | CorrelationId | DurationMs | Vlastnosti |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Informační | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Informační | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Informační | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Různé typy protokolů najdete v tématu [diagnostické protokoly Azure IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

### <a name="application-map"></a>Mapa aplikace

Vizualizace toku zpráv IoT, nastavte ukázkovou aplikaci mapy aplikace. Ukázková aplikace odešle protokoly distribuované trasování, které mají [Mapa aplikace](../application-insights/app-insights-app-map.md) pomocí funkce Azure functions a centra událostí.

> [!div class="button"]
<a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Získat ukázky na Githubu</a>

Tento obrázek níže znázorňuje distribuované trasování v mapě aplikace pomocí tří směrování koncových bodů:

![IoT distribuované trasování v Mapa aplikace](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Vysvětlení, že Azure IoT distribuované trasování

### <a name="context"></a>Kontext

Mnoho řešení IoT, včetně našim vyrovnají [referenční architektuře](https://aka.ms/iotrefarchitecture) (jenom v angličtině), obvykle postupují podle jeho variantě [architekturu mikroslužeb](https://docs.microsoft.com/azure/architecture/microservices/). S růstem řešení IoT složitější, skončíte používání deseti nebo další mikroslužeb. Tyto mikroslužeb může nebo nemusí být z Azure. Přesné určení, kde zpomalení nebo vyřazením zprávy IoT může být náročná. Například máte řešení IoT, která používá 5 různých služeb Azure a 1 500 aktivních zařízení. Každé zařízení odesílá zprávy za sekundu 10 typu zařízení cloud (pro celkový počet 15 000 zpráv za sekundu), ale Všimněte si, že vaše webová aplikace vidí jenom 10 000 zpráv za sekundu. Kde je problém? Jak najít nadměrné spotřeby?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Model distribuované trasování v architektuře mikroslužeb

K rekonstrukci tok zpráv IoT napříč různými službami, by měla každá služba šíření *ID korelace* , který jednoznačně identifikuje zprávy. Po shromáždění do centralizovaného systému, ID korelace umožňují zobrazit tok zpráv. Tato metoda je volána [distribuované trasování vzor](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing).

Pro přechod na širší podporu pro distribuované trasování, Microsoft přispívá k [W3C standard návrhu pro distribuované trasování](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Podpora služby IoT Hub

Po povolení bude distribuované trasování podpory pro službu IoT Hub, postupujte podle tento tok:

1. Zpráva je vygenerována na zařízení IoT.
1. Zařízení IoT rozhodne (díky pomoci od cloudu), že by mělo být přiřazeno tuto zprávu s kontextem trasování.
1. Přidá sada SDK `tracestate` aplikace vlastnosti zprávy, který obsahuje časové razítko vytvoření zprávy.
1. Zařízení IoT odešle zprávu do služby IoT Hub.
1. Zprávy dorazí ve službě IoT hub gateway.
1. Vyhledá službu IoT Hub `tracestate` ve vlastnosti zprávy aplikace a kontroluje, jestli je ve správném formátu.
1. Pokud ano, služby IoT Hub generuje a protokolů `trace-id` a `span-id` pro diagnostické protokoly Azure monitoru v kategorii `DiagnosticIoTHubD2C`.
1. Po dokončení zpracování zpráv služby IoT Hub vytvoří jiné `span-id` a protokoly spolu s existující `trace-id` v kategorii `DiagnosticIoTHubIngress`.
1. Pokud je povoleno směrování pro zprávy, zapisuje je do vlastního koncového bodu služby IoT Hub a jiné protokoly `span-id` se stejným `trace-id` v kategorii `DiagnosticIoTHubEgress`.
1. Výše uvedené kroky se opakuje pro každou zprávu, generována.

## <a name="public-preview-limits-and-considerations"></a>Ve verzi Public preview omezení a důležité informace

- Návrh standardu W3C trasování kontextu je aktuálně pracovní koncept.
- V současné době pouze vývojový jazyk podporovaný klientskou sadou SDK je C.
- Není k dispozici pro dvojče typu cloud zařízení [základní úrovně služby IoT Hub](iot-hub-scaling.md#basic-and-standard-tiers). IoT Hub bude však nadále protokolovat do Azure monitoru Pokud zobrazí hlavičku kontextu správně složené trasování.
- Zajistit efektivní operace služby IoT Hub bude ukládat omezení na frekvenci protokolování, které můžou nastat jako součást distribuované trasování.

## <a name="next-steps"></a>Další postup

- Další informace o vzoru obecné distribuované trasování v mikroslužbách najdete v tématu [vzor architektury Mikroslužeb: distribuované trasování](https://microservices.io/patterns/observability/distributed-tracing.html).
- Nastavení konfigurace pro aplikaci nastavení distribuované trasování na velký počet zařízení, najdete v článku [konfigurovat a monitorovat zařízení IoT ve velkém měřítku](iot-hub-auto-device-config.md).
- Další informace o monitorování Azure, najdete v článku [co je Azure Monitor?](../azure-monitor/overview.md).
