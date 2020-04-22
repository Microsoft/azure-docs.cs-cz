---
title: Přidání ID korelace do zpráv IoT s nebo distribuovaným trasováním (pre)
description: Zjistěte, jak pomocí možnosti distribuovaného trasování sledovat zprávy IoT v rámci služeb Azure používaných vaším řešením.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 2b1dc7873140f885ec3efac11dec5fbf6aab7aa9
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732564"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Sledování zpráv Azure IoT mezi zařízeními a cloudy pomocí distribuovaného trasování (preview)

Microsoft Azure IoT Hub v současné době podporuje distribuované trasování jako [funkci náhledu](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

IoT Hub je jednou z prvních služeb Azure pro podporu distribuovaného trasování. Jako další služby Azure podporují distribuované trasování, budete moct trasovat zprávy IoT v rámci služeb Azure zapojených do vašeho řešení. Pozadí distribuovaného trasování naleznete v tématu [Distribuované trasování](../azure-monitor/app/distributed-tracing.md).

Povolení distribuovaného trasování pro IoT Hub vám dává možnost:

- Přesně sledovat tok každé zprávy prostřednictvím služby IoT Hub pomocí [kontextu trasování](https://github.com/w3c/trace-context). Tento kontext trasování zahrnuje ID korelace, které umožňují korelovat události z jedné součásti s událostmi z jiné součásti. Lze použít pro podmnožinu nebo všechny zprávy zařízení IoT pomocí [dvojčete zařízení](iot-hub-devguide-device-twins.md).
- Automaticky protokolovat kontext trasování do [diagnostických protokolů Azure Monitor .](iot-hub-monitor-resource-health.md)
- Změřte a pochopte tok zpráv a latenci ze zařízení do služby IoT Hub a koncových bodů směrování.
- Začněte zvažovat, jak chcete implementovat distribuované trasování pro služby než Azure ve vašem řešení IoT.

V tomto článku použijete [azure iot zařízení SDK pro C](iot-hub-device-sdk-c-intro.md) s distribuovaným trasování. Pro ostatní sady SDK stále probíhá podpora distribuovaného trasování.

## <a name="prerequisites"></a>Požadavky

- Náhled distribuovaného trasování je momentálně podporován pouze pro centra IoT Hub vytvořených v následujících oblastech:

  - **Severní Evropa**
  - **Jihovýchodní Asie**
  - **USA – západ 2**

- Tento článek předpokládá, že jste obeznámeni s odesíláním telemetrických zpráv do služby IoT hub. Ujistěte se, že jste dokončili [odeslat telemetrii C Rychlý start](quickstart-send-telemetry-c.md).

- Zaregistrujte zařízení pomocí služby IoT hub (kroky dostupné v každém rychlém startu) a poznamenejte si připojovací řetězec.

- Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Konfigurace služby IoT Hub

V této části nakonfigurujete službu IoT Hub pro protokolování distribuovaných trasovacích atributů (ID korelace a časová razítka).

1. Přejděte do svého centra IoT hub na [webu Azure Portal](https://portal.azure.com/).

1. V levém podokně centra IoT přejděte dolů do části **Monitorování** a klikněte na **Nastavení diagnostiky**.

1. Pokud nastavení diagnostiky ještě nejsou zapnutá, klikněte **na Zapnout diagnostiku**. Pokud jste již povolili nastavení diagnostiky, klepněte na **tlačítko Přidat diagnostické nastavení**.

1. Do pole **Název** zadejte název nového diagnostického nastavení. Například **DistributedTracingSettings**.

1. Zvolte jednu nebo více z následujících možností, které určují, kam bude protokolování odesláno:

    - **Archivovat do účtu úložiště**: Nakonfigurujte účet úložiště tak, aby obsahoval informace o protokolování.
    - **Streamování do centra událostí:** Nakonfigurujte centrum událostí tak, aby obsahovalo informace o protokolování.
    - **Odeslat do analýzy protokolů**: Nakonfigurujte pracovní prostor analýzy protokolů tak, aby obsahoval informace o protokolování.

1. V části **Protokol** vyberte operace, pro které chcete získat informace o protokolování.

    Nezapomeňte zahrnout **DistributedTracing**a nakonfigurovat **uchovávání informací** o tom, kolik dní chcete protokolování zachovat. Uchovávání protokolu má vliv na náklady na úložiště.

    ![Snímek obrazovky znázorňující, kde je kategorie DistributedTracing pro nastavení diagnostiky IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Pro nové nastavení klikněte na **Uložit.**

1. (Nepovinné) Chcete-li zobrazit tok zpráv na různá místa, nastavte [pravidla směrování alespoň dva různé koncové body](iot-hub-devguide-messages-d2c.md).

Po zapnutí protokolování služba IoT Hub zaznamená protokol, když se v některé z následujících situací vyskytne zpráva obsahující platné vlastnosti trasování:

- Zprávy dorazí na bránu služby IoT Hub.
- Zpráva je zpracována službou IoT Hub.
- Zpráva je směrována do vlastních koncových bodů. Směrování musí být povoleno.

Další informace o těchto protokolech a jejich schématech naleznete [v tématu Distribuované trasování v diagnostických protokolech centra IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Nastavení zařízení

V této části připravíte vývojové prostředí pro použití s [sadou Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Potom upravit jeden z ukázek povolit distribuované trasování na telemetrických zpráv vašeho zařízení.

Tyto pokyny jsou určeny pro vytváření ukázky v systému Windows. Další prostředí naleznete [v tématu Kompilace sady C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) nebo [předbalené sady C SDK pro vývoj specifických pro platformu](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Klonovat zdrojový kód a inicializovat

1. Nainstalujte [úlohu "Vývoj plochy s C++"](https://docs.microsoft.com/cpp/build/vscpp-step-0-installation?view=vs-2019) pro Visual Studio 2019. Visual Studio 2017 a 2015 jsou také podporovány.

1. Nainstalujte [CMake](https://cmake.org/). Ujistěte se, `PATH` že je `cmake -version` ve vašem zadáním z příkazového řádku.

1. Otevřete prostředí příkazového řádku nebo Git Bash. Spusťte následující příkazy pro klonování nejnovější verze úložiště [GitHub Azure IoT C SDK:](https://github.com/Azure/azure-iot-sdk-c)

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

1. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. Spusťte z adresáře následující příkazy: `azure-iot-sdk-c`

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Pokud `cmake` nemůžete najít kompilátor Jazyka C++, může dojít k chybám sestavení při spuštění výše uvedeného příkazu. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Úprava ukázky telemetrie pro povolení distribuovaného trasování

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Získání ukázky na GitHubu</a>

1. K otevření zdrojového `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` souboru použijte editor.

1. Vyhledejte deklaraci konstanty `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Nahraďte hodnotu `connectionString` konstanty připojovacím řetězcem zařízení, který jste si poznamenali v registru části [zařízení](./quickstart-send-telemetry-c.md#register-a-device) v [úvodním úvodním spuštění Odeslat telemetrii C](./quickstart-send-telemetry-c.md).

1. Změňte `MESSAGE_COUNT` definici na `5000`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Najděte řádek kódu, `IoTHubDeviceClient_LL_SetConnectionStatusCallback` který volá zaregistrovat funkci zpětného volání stavu připojení před odesláním smyčky zpráv. Přidejte kód pod tento řádek, jak je znázorněno níže, pro volání `IoTHubDeviceClient_LL_EnablePolicyConfiguration` povolení distribuovaného trasování pro zařízení:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    Funkce `IoTHubDeviceClient_LL_EnablePolicyConfiguration` umožňuje zásady pro konkrétní funkce IoTHub, které jsou konfigurovány prostřednictvím [dvojčata zařízení](./iot-hub-devguide-device-twins.md). Jakmile `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` je povolena s řádkem kódu výše, trasování chování zařízení bude odrážet distribuované trasování změny provedené na dvojčezařízení.

1. Chcete-li zachovat spuštěnou ukázkovou aplikaci bez využití celé kvóty, přidejte na konec smyčky odeslání zprávy jednosekundové zpoždění:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Kompilace a spuštění

1. Přejděte do adresáře *projektu iothub_ll_telemetry_sample* `azure-iot-sdk-c/cmake`z dříve vytvořeného adresáře CMake ( ) a zkompilujte ukázku:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Spusťte aplikaci. Zařízení odesílá telemetrická data podporující distribuované trasování.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Udržujte aplikaci spuštěnou. Volitelně sledovat zprávy odesílané do služby IoT Hub při pohledu na okno konzoly.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Řešení pro klienty třetích stran

**Není triviální** zobrazit náhled funkce distribuovaného trasování bez použití sady C SDK. Tento přístup se tedy nedoporučuje.

Nejprve je nutné implementovat všechny primitivprotokolu IoT Hub ve zprávách podle průvodce dev [vytvořit a číst zprávy IoT Hub](iot-hub-devguide-messages-construct.md). Potom upravte vlastnosti protokolu ve zprávách MQTT/AMQP, které chcete přidat `tracestate` jako vlastnost **systému**. Konkrétně:

* Pro MQTT `%24.tracestate=timestamp%3d1539243209` přidejte do tématu zprávy, kde `1539243209` by měl být nahrazen čas vytvoření zprávy ve formátu unix časové razítko. Jako příklad se podívejte na implementaci [v C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* Pro AMQP `key("tracestate")` přidejte a `value("timestamp=1539243209")` jako poznámku zprávy. Referenční implementaci naleznete [zde](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Chcete-li řídit procento zpráv obsahujících tuto vlastnost, implementujte logiku pro naslouchání událostem iniciovatcím cloudem, jako jsou aktualizace dvojčat.

## <a name="update-sampling-options"></a>Aktualizovat možnosti vzorkování 

Chcete-li změnit procento zpráv, které mají být sledovány z cloudu, je nutné aktualizovat dvojče zařízení. Toho lze provést několika způsoby, včetně editoru JSON na portálu a sady SDK služby IoT Hub. Následující pododdíly obsahují příklady.

### <a name="update-using-the-portal"></a>Aktualizace pomocí portálu

1. Přejděte do svého Centra IoT na [Webu Azure Portal](https://portal.azure.com/)a klikněte na zařízení **IoT**.

1. Klikněte na zařízení.

1. Vyhledejte možnost **Povolit distribuované trasování (náhled) a**pak vyberte **Povolit**.

    ![Povolení distribuovaného trasování na webu Azure Portal](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Zvolte **vzorkovací frekvenci** mezi 0 % a 100 %.

1. Klikněte na **Uložit**.

1. Počkejte několik sekund a stiskněte **tlačítko Aktualizovat**, pak pokud je zařízení úspěšně potvrzeno, zobrazí se ikona synchronizace se zaškrtnutím.

1. Vraťte se do okna konzoly pro aplikaci zprávy telemetrie. Ve vlastnostech `tracestate` aplikace se zobrazí zprávy odesílané.

    ![Stav trasování](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (Nepovinné) Změňte vzorkovací frekvenci na jinou hodnotu a `tracestate` sledujte změnu frekvence, kterou zprávy obsahují ve vlastnostech aplikace.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Aktualizace pomocí služby Azure IoT Hub pro kód VS

1. Nainstalujte Kód VS a pak [nainstalujte](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)nejnovější verzi služby Azure IoT Hub pro kód VS odtud .

1. Otevřete kód VS a [nastavte připojovací řetězec ioT hubu](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Rozbalte zařízení a vyhledejte **nastavení distribuovaného trasování (náhled).** Pod ním klikněte na **Aktualizovat nastavení distribuovaného trasování (náhled)** dílčího uzlu.

    ![Povolení distribuovaného trasování v rozšíření Služby Azure IoT Hub](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. V místní mačkárně vyberte **Povolit**a stisknutím klávesy Enter potvrďte rychlost vzorkování 100.

    ![Aktualizovat režim vzorkování](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Aktualizovat vzorkovací frekvenci](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Hromadná aktualizace pro více zařízení

Chcete-li aktualizovat distribuovanou konfiguraci vzorkování trasování pro více zařízení, použijte [automatickou konfiguraci zařízení](iot-hub-auto-device-config.md). Ujistěte se, že postupujte podle tohoto schématu dvojčat:

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
| `sampling_mode` | Ano | Integer | Pro zapnutí a vypnutí vzorkování jsou aktuálně podporovány dvě hodnoty režimu. `1`je zapnuto `2` a je vypnuto. |
| `sampling_rate` | Ano | Integer | Tato hodnota je procento. Povoleny `0` jsou `100` pouze hodnoty od do (včetně).  |

## <a name="query-and-visualize"></a>Dotaz a vizualizace

Chcete-li zobrazit všechny trasování zaznamenané službou IoT Hub, dotaz ujte do úložiště protokolů, které jste vybrali v nastavení diagnostiky. Tato část prochází několika různými možnostmi.

### <a name="query-using-log-analytics"></a>Dotaz pomocí analýzy protokolů

Pokud jste nastavili [Analýzu protokolů s diagnostickými protokoly](../azure-monitor/platform/resource-logs-collect-storage.md), `DistributedTracing` dotaz ujte hledáním protokolů v kategorii. Tento dotaz například zobrazuje všechny protokolované trasování:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Příkladprotokoly, jak je uvedeno log Analytics:

| TimeGenerated | OperationName | Kategorie | Úroveň | CorrelationId | DurationMs | Vlastnosti |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnostikaIoTHubD2C | Distribuované trasování | Informační | 00-8cd869a412459a25f5b4f3131223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | Distribuované trasování | Informační | 00-8cd869a412459a25f5b4f3131223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | Distribuované trasování | Informační | 00-8cd869a412459a25f5b4f3131223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

Informace o různých typech protokolů najdete v [tématu diagnostické protokoly služby Azure IoT Hub](iot-hub-monitor-resource-health.md#distributed-tracing-preview).

### <a name="application-map"></a>Mapa aplikace

Chcete-li vizualizovat tok zpráv IoT, nastavte ukázkovou aplikaci Mapu aplikací. Ukázková aplikace odešle protokoly distribuovaného trasování do [mapy aplikací](../application-insights/app-insights-app-map.md) pomocí funkce Azure a centra událostí.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Získání ukázky na GitHubu</a>

Tento obrázek níže znázorňuje distribuované trasování v mapě aplikací se třemi koncovými body směrování:

![IoT distribuované trasování v mapě aplikací](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Principy distribuovaného trasování Azure IoT

### <a name="context"></a>Kontext

Mnoho řešení IoT, včetně naší vlastní [referenční architektury](https://aka.ms/iotrefarchitecture) (pouze v angličtině), obecně následuje variantu [architektury mikroslužeb](https://docs.microsoft.com/azure/architecture/microservices/). Jako řešení IoT roste složitější, můžete nakonec pomocí tucet nebo více mikroslužeb. Tyto mikroslužby může nebo nemusí být z Azure. Určení místa, kde zprávy IoT klesají nebo zpomalují, může být náročné. Například máte řešení IoT, které používá 5 různých služeb Azure a 1500 aktivních zařízení. Každé zařízení odesílá 10 zpráv zařízení cloud za sekundu (celkem 15 000 zpráv za sekundu), ale zjistíte, že vaše webová aplikace zobrazuje pouze 10 000 zpráv za sekundu. Kde je problém? Jak najdete viníka?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Distribuovaný vzor trasování v architektuře mikroslužeb

Chcete-li rekonstruovat tok zprávy IoT napříč různými službami, každá služba by měla šířit *ID korelace,* která jednoznačně identifikuje zprávu. Po shromáždění v centralizovaném systému umožňují ID korelace zobrazit tok zpráv. Tato metoda se nazývá [distribuovaný vzorek trasování](https://docs.microsoft.com/azure/architecture/microservices/logging-monitoring#distributed-tracing).

Pro podporu širšího přijetí distribuovaného trasování přispívá společnost Microsoft standardním [návrhem W3C pro distribuované trasování](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Podpora pro IoT Hub

Po povolení bude podpora distribuovaného trasování pro Službu IoT Hub následovat tento tok:

1. Na zařízení IoT se vygeneruje zpráva.
1. Zařízení IoT rozhodne (s pomocí z cloudu), že tato zpráva by měla být přiřazena s kontextu trasování.
1. Sada SDK `tracestate` přidá a do vlastnosti aplikace zprávy, obsahující časové razítko vytvoření zprávy.
1. Zařízení IoT odešle zprávu do služby IoT Hub.
1. Zpráva dorazí na bránu služby IoT hub.
1. Služba IoT Hub `tracestate` vyhledá vlastnosti aplikace ve zprávě a zkontroluje, jestli je ve správném formátu.
1. Pokud ano, služba IoT Hub `trace-id` generuje globálně jedinečný `span-id` pro zprávu, a pro "hop" a protokoly `DiagnosticIoTHubD2C`je do protokolů azure monitoru diagnostiky v rámci operace .
1. Po dokončení zpracování zpráv služba IoT Hub `span-id` vygeneruje další a `trace-id` zaznamená `DiagnosticIoTHubIngress`jej spolu s existující v rámci operace .
1. Pokud je směrování povoleno pro zprávu, Služba IoT Hub ji zapíše do vlastního koncového bodu a zaznamená jiný `span-id` se stejným `trace-id` v rámci kategorie `DiagnosticIoTHubEgress`.
1. Výše uvedené kroky se opakují pro každou vygenerovanou zprávu.

## <a name="public-preview-limits-and-considerations"></a>Omezení a důležité informace o verzi preview ve veřejném náhledu

- Návrh standardu W3C Trace Context je v současné době pracovním konceptem.
- V současné době je jediným vývojovým jazykem podporovaným sadou SDK klienta C.
- Funkce dvojčete cloud-to-device není dostupná pro [základní úroveň služby IoT Hub](iot-hub-scaling.md#basic-and-standard-tiers). Služba IoT Hub se však stále přihlásí k Azure Monitoru, pokud se zobrazí správně sestavená hlavička kontextu trasování.
- Aby byl zajištěn efektivní provoz, služby IoT Hub uvalí omezení rychlosti protokolování, ke které může dojít v rámci distribuovaného trasování.

## <a name="next-steps"></a>Další kroky

- Další informace o obecném distribuovaném způsobu trasování v mikroslužbách naleznete v [tématu Vzor architektury mikroslužeb: distribuované trasování](https://microservices.io/patterns/observability/distributed-tracing.html).
- Pokud chcete nastavit konfiguraci pro použití nastavení distribuovaného trasování u velkého počtu zařízení, přečtěte si část [Konfigurace a monitorování zařízení IoT ve velkém měřítku](iot-hub-auto-device-config.md).
- Další informace o Azure Monitoru najdete v [tématu Co je Azure Monitor?](../azure-monitor/overview.md).
