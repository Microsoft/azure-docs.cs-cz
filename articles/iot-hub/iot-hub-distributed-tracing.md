---
title: Přidat ID korelace do zpráv IoT s nedistribuovaným trasováním (před)
description: Naučte se používat distribuované trasování, které umožňuje sledovat zprávy IoT v rámci služeb Azure, které vaše řešení používá.
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
- fasttrack-edit
- iot
ms.openlocfilehash: f8d37cf8f23de1d0535c7a9ff4a95ac217eddf74
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452392"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Trasování zpráv ze zařízení do cloudu Azure IoT pomocí distribuované trasování (Preview)

Microsoft Azure IoT Hub aktuálně podporuje distribuované trasování jako [funkci ve verzi Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

IoT Hub je jedna z prvních služeb Azure, která podporuje distribuované trasování. Protože další služby Azure podporují distribuované trasování, budete moct sledovat zprávy IoT v rámci vašich služeb Azure, které jsou součástí vašeho řešení. Informace o pozadí v distribuovaném trasování najdete v tématu [distribuované trasování](../azure-monitor/app/distributed-tracing.md).

Povolení distribuovaného trasování pro IoT Hub vám dává možnost:

- Přesné monitorování toku každé zprávy prostřednictvím IoT Hub pomocí [kontextu trasování](https://github.com/w3c/trace-context). Tento kontext trasování zahrnuje ID korelace, které umožňují korelovat události z jedné komponenty s událostmi z jiné komponenty. Dá se použít pro podmnožinu nebo všechny zprávy zařízení IoT, které používají [zdvojené zařízení](iot-hub-devguide-device-twins.md).
- Automaticky protokoluje kontext trasování do [Azure monitor protokolů](monitor-iot-hub.md).
- Měření a pochopení toku zpráv a latence ze zařízení až po IoT Hub a směrování koncových bodů.
- Začněte s úvahami, jak chcete implementovat distribuované trasování pro služby mimo Azure ve vašem řešení IoT.

V tomto článku použijete [sadu SDK pro zařízení Azure IoT pro jazyk C](iot-hub-device-sdk-c-intro.md) s distribuovaným trasováním. Podpora distribuovaného trasování pro ostatní sady SDK stále probíhá.

## <a name="prerequisites"></a>Předpoklady

- Verze Preview distribuovaného trasování je momentálně podporovaná jenom pro centra IoT vytvořená v následujících oblastech:

  - **Severní Evropa**
  - **Southeast Asia**
  - **Západní USA 2**

- V tomto článku se předpokládá, že máte zkušenosti s posíláním zpráv telemetrie do služby IoT Hub. Ujistěte se, že jste dokončili [rychlý Start pro odeslání telemetrie C](quickstart-send-telemetry-c.md).

- Zaregistrujte zařízení ve službě IoT Hub (kroky dostupné v každém rychlém startu) a poznamenejte si připojovací řetězec.

- Nainstalujte nejnovější verzi [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Konfigurace IoT Hub

V této části nakonfigurujete IoT Hub k protokolování atributů distribuované vektorizace (ID korelace a časová razítka).

1. V [Azure Portal](https://portal.azure.com/)přejděte do služby IoT Hub.

1. V levém podokně Centra IoT přejděte dolů k části **monitorování** a klikněte na **nastavení diagnostiky**.

1. Klikněte na **Přidat nastavení diagnostiky**.

1. Do pole **název** zadejte název nového nastavení diagnostiky. Například **DistributedTracingSettings**.

1. Vyberte jednu nebo více z následujících možností, které určují, kam bude protokolování odesláno:

    - **Archivovat do účtu úložiště**: Nakonfigurujte účet úložiště tak, aby obsahoval informace o protokolování.
    - **Streamování do centra událostí**: Konfigurace centra událostí tak, aby obsahovalo informace o protokolování.
    - **Odeslat do Log Analytics**: Nakonfigurujte pracovní prostor Log Analytics tak, aby obsahoval informace o protokolování.

1. V části **protokol** vyberte operace, pro které chcete informace o protokolování.

    Ujistěte se, že jste zahrnuli **DistributedTracing** a nakonfigurovali dobu **uchování** , kterou chcete zachovávat. Uchovávání protokolů má vliv na náklady na úložiště.

    ![Snímek obrazovky zobrazující, kde je kategorie DistributedTracing pro nastavení diagnostiky IoT](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Pro nové nastavení klikněte na **Uložit** .

1. Volitelné Chcete-li zobrazit tok zpráv na různých místech, nastavte [pravidla směrování alespoň na dva různé koncové body](iot-hub-devguide-messages-d2c.md).

Jakmile je protokolování zapnuté, IoT Hub zaznamenává protokol, když se v některé z těchto situací vyskytne zpráva obsahující platné vlastnosti trasování:

- Zprávy přicházejí v bráně IoT Hub.
- Zpráva je zpracována IoT Hub.
- Zpráva je směrována do vlastních koncových bodů. Směrování musí být povolené.

Další informace o těchto protokolech a jejich schématech najdete v tématu [monitorování IoT Hub](monitor-iot-hub.md) a [distribuované trasování v protokolech prostředků IoT Hub](monitor-iot-hub-reference.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Nastavení zařízení

V této části připravíte vývojové prostředí pro použití s [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Pak upravíte jednu z ukázek a povolíte distribuované trasování ve zprávách telemetrie zařízení.

Tyto pokyny se týkají vytváření ukázek ve Windows. Další prostředí naleznete v tématu [kompilace sady c SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) nebo [Předbalená sada c SDK pro vývoj pro konkrétní platformu](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Klonovat zdrojový kód a inicializovat

1. Nainstalujte [úlohu vývoj desktopových aplikací v C++](/cpp/build/vscpp-step-0-installation?view=vs-2019) pro Visual Studio 2019. Podporují se také sady Visual Studio 2017 a 2015.

1. Nainstalujte [cmake](https://cmake.org/). Zajistěte, aby byla v aplikaci, `PATH` a to zadáním `cmake -version` z příkazového řádku.

1. Otevřete prostředí příkazového řádku nebo Git Bash. Spuštěním následujících příkazů naklonujte nejnovější verzi úložiště GitHub pro [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) :

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Buďte připravení na to, že může trvat i několik minut, než se tato operace dokončí.

1. V kořenovém adresáři úložiště Git vytvořte podadresář `cmake` a přejděte do této složky. Z adresáře spusťte následující příkazy `azure-iot-sdk-c` :

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Pokud `cmake` nemůžete najít kompilátor jazyka C++, může při spuštění výše uvedeného příkazu dojít k chybám sestavení. Pokud k tomu dojde, zkuste tento příkaz spustit v [příkazovém řádku sady Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

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

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Pokud chcete povolit distribuované trasování, upravte ukázku odeslat telemetrii.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Získání ukázky na GitHubu</a>

1. K otevření zdrojového souboru použijte Editor `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` .

1. Vyhledejte deklaraci konstanty `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Hodnotu konstanty nahraďte `connectionString` připojovacím řetězcem zařízení, na které jste si poznamenali v části [registrace zařízení](./quickstart-send-telemetry-c.md#register-a-device) v [rychlém startu pro odeslání telemetrie C](./quickstart-send-telemetry-c.md).

1. Změňte `MESSAGE_COUNT` definici na `5000` :

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Vyhledejte řádek kódu, který volá `IoTHubDeviceClient_LL_SetConnectionStatusCallback` k registraci funkce zpětného volání stavu připojení před smyčkou odeslání zprávy. Přidejte kód pod tento řádek, jak je znázorněno níže pro volání `IoTHubDeviceClient_LL_EnablePolicyConfiguration` Povolení distribuovaného trasování pro zařízení:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    `IoTHubDeviceClient_LL_EnablePolicyConfiguration`Funkce umožňuje zásady pro konkrétní funkce IoTHub, které jsou konfigurovány prostřednictvím [vláken zařízení](./iot-hub-devguide-device-twins.md). Jakmile `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` je tato funkce povolená s výše uvedeným řádkem kódu, bude chování trasování zařízení odrážet distribuované vektorizace změny provedené v zařízení.

1. Pokud chcete, aby se ukázková aplikace běžela bez použití všech kvót, přidejte na konci smyčky odeslat zprávu prodlevu o délce jednoho sekundy:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Zkompilovat a spustit

1. Přejděte do adresáře *iothub_ll_telemetry_sample* projektu z adresáře cmake (), `azure-iot-sdk-c/cmake` který jste vytvořili dříve, a zkompilujte ukázku:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Spusťte aplikaci. Zařízení odešle telemetrii podporující distribuované trasování.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Nechte aplikaci spuštěnou. Volitelně můžete sledovat zprávu odeslanou do IoT Hub tím, že prohlížíte okno konzoly.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Alternativní řešení pro klienty třetích stran

**Není triviální** k zobrazení náhledu funkce distribuované trasování bez použití sady C SDK. Proto se tento přístup nedoporučuje.

Nejdřív je nutné implementovat všechny primitivní IoT Hub protokolu ve zprávách pomocí Průvodce vývojem [pro vytváření a čtení zpráv IoT Hub](iot-hub-devguide-messages-construct.md). Pak upravte vlastnosti protokolu ve zprávách MQTT/AMQP na `tracestate` hodnotu přidat jako **systémovou vlastnost**. Konkrétně:

* V případě MQTT přidejte `%24.tracestate=timestamp%3d1539243209` do tématu zprávy, kde `1539243209` by měla být nahrazena časem vytvoření zprávy ve formátu časového razítka systému UNIX. Příklad naleznete v tématu implementace [v sadě C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761) .
* Pro AMQP přidejte `key("tracestate")` a `value("timestamp=1539243209")` jako anotaci zpráv. Referenční implementaci najdete [tady](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Chcete-li řídit procento zpráv obsahujících tuto vlastnost, implementujte logiku pro naslouchání událostem inicializovaným v cloudu, jako jsou například zdvojené aktualizace.

## <a name="update-sampling-options"></a>Možnosti vzorkování aktualizací 

Chcete-li změnit procento zpráv, které mají být trasovány z cloudu, je nutné v zařízení aktualizovat vlákna. Můžete to provést několika způsoby, včetně editoru JSON na portálu a sady SDK služby IoT Hub. Následující pododdíly obsahují příklady.

### <a name="update-using-the-portal"></a>Aktualizace pomocí portálu

1. V [Azure Portal](https://portal.azure.com/)přejděte do svého centra IoT a pak klikněte na **zařízení IoT**.

1. Klikněte na své zařízení.

1. Vyhledejte možnost **Povolit distribuované trasování (Preview)** a pak vyberte **Povolit**.

    ![Povolit distribuované trasování v Azure Portal](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Vyberte **vzorkovací frekvenci** mezi 0 a 100%.

1. Klikněte na **Uložit**.

1. Počkejte několik sekund a pak stiskněte tlačítko **aktualizovat**. po úspěšném potvrzení zařízení se zobrazí ikona synchronizace se symbolem zaškrtnutí.

1. Vraťte se do okna konzoly pro aplikaci zprávy telemetrie. Ve vlastnostech aplikace se zobrazí zprávy odesílané `tracestate` ve službě.

    ![Stav trasování](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. Volitelné Nastavte vzorkovací frekvenci na jinou hodnotu a sledujte změnu četnosti, kterou zprávy `tracestate` ve vlastnostech aplikace obsahují.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Aktualizace pomocí IoT Hub Azure pro VS Code

1. Nainstalujte VS Code a potom z [tohoto místa](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)nainstalujte nejnovější verzi Azure IoT Hub pro vs Code.

1. Otevřete VS Code a [nastavte připojovací řetězec IoT Hub](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Rozbalte zařízení a vyhledejte **Nastavení distribuované trasování (Preview)**. V takovém případě klikněte na možnost **aktualizovat nastavení distribuovaného trasování (Preview)** dílčího uzlu.

    ![Povolit distribuované trasování v rozšíření Azure IoT Hub](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. V místním okně vyberte **Povolit** a stisknutím klávesy ENTER potvrďte 100 jako vzorkovací frekvenci.

    ![Aktualizovat režim vzorkování](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Frekvence vzorkování aktualizací](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Hromadná aktualizace pro více zařízení

Pokud chcete aktualizovat konfiguraci pro vzorkování distribuovaného trasování pro více zařízení, použijte [automatickou konfiguraci zařízení](./iot-hub-automatic-device-management.md). Ujistěte se, že dodržujete toto zdvojené schéma:

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
| `sampling_mode` | Ano | Integer | Pro zapnutí a vypnutí vzorkování se aktuálně podporují dvě hodnoty režimu. `1` je zapnuto a, `2` je vypnuto. |
| `sampling_rate` | Yes | Integer | Tato hodnota je procento. Povolují se jenom hodnoty z `0` na `100` (včetně).  |

## <a name="query-and-visualize"></a>Dotazování a vizualizace

Chcete-li zobrazit všechna trasování protokolovaná IoT Hub, proveďte dotaz na úložiště protokolu, které jste vybrali v nastavení diagnostiky. Tato část vás provede několika různými možnostmi.

### <a name="query-using-log-analytics"></a>Dotaz pomocí Log Analytics

Pokud jste nastavili [Log Analytics s protokoly prostředků](../azure-monitor/platform/resource-logs.md#send-to-azure-storage), proveďte dotaz hledáním protokolů v `DistributedTracing` kategorii. Tento dotaz například zobrazí všechna zaznamenaná trasování:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Příklady protokolů, jak je znázorněno Log Analytics:

| TimeGenerated | OperationName | Kategorie | Úroveň | CorrelationId | DurationMs | Vlastnosti |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633 Z | DiagnosticIoTHubD2C | DistributedTracing | Informační | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId": "AZ3166", "messageSize": "96", "callerLocalTimeUtc": "2018-02-22T03:27:28.633 Z", "calleeLocalTimeUtc": "2018-02-22T03:27:28.687 Z"} |
| 2018-02-22T03:28:38.633 Z | DiagnosticIoTHubIngress | DistributedTracing | Informační | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled": "false"; "parentSpanId": "0144d2590aacd909"} |
| 2018-02-22T03:28:48.633 Z | DiagnosticIoTHubEgress | DistributedTracing | Informační | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType": "EventHub"; "koncový bod": "myEventHub", "parentSpanId": "0144d2590aacd909"} |

Další informace o různých typech protokolů najdete v tématu [protokoly distribuovaného trasování Azure IoT Hub](monitor-iot-hub-reference.md#distributed-tracing-preview).

### <a name="application-map"></a>Mapa aplikace

Pro vizualizaci toku zpráv IoT nastavte ukázkovou aplikaci Application map. Ukázková aplikace pošle protokoly distribuovaného trasování do [mapy aplikace](../azure-monitor/app/app-map.md) pomocí funkce Azure a centra událostí.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Získání ukázky na GitHubu</a>

Následující obrázek ukazuje distribuované trasování v mapě aplikace se třemi koncovými body směrování:

![Distribuované trasování IoT v mapě aplikací](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Vysvětlení distribuovaného trasování Azure IoT

### <a name="context"></a>Kontext

Mnoho řešení IoT, včetně naší vlastní [referenční architektury](/azure/architecture/reference-architectures/iot) (jenom v angličtině), obvykle sleduje variantu [architektury mikroslužeb](/azure/architecture/microservices/). Díky větší složitosti řešení IoT skončíte s využitím desítek a více mikroslužeb. Tyto mikroslužby můžou nebo nemusí být z Azure. Určení, kde se zprávy IoT vyřazuje nebo zpomalují, můžou být náročné. Máte například řešení IoT, které využívá 5 různých služeb Azure a 1500 aktivních zařízení. Každé zařízení odesílá 10 zpráv typu zařízení-Cloud za sekundu (celkem 15 000 zpráv za sekundu), ale Všimněte si, že vaše webová aplikace zobrazuje pouze 10 000 zpráv za sekundu. Kde se jedná o problém? Jak najít příčinou?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Model distribuovaného trasování v architektuře mikroslužeb

Aby bylo možné rekonstruovat tok zprávy IoT napříč různými službami, měla by každá služba šířit *ID korelace* , které zprávu jedinečně identifikuje. Po shromáždění v centralizovaném systému vám ID korelace umožní zobrazit tok zpráv. Tato metoda se nazývá [model distribuované vektorizace](/azure/architecture/microservices/logging-monitoring#distributed-tracing).

Pro podporu širšího přijetí pro distribuované trasování společnost Microsoft přispívá k [návrhu standardu W3C pro distribuované trasování](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>Podpora IoT Hub

Po povolení bude podpora distribuovaného trasování pro IoT Hub postupovat podle tohoto toku:

1. Na zařízení IoT se vygeneruje zpráva.
1. Zařízení IoT se rozhodne (s využitím cloudu), že by tato zpráva měla být přiřazena kontextu trasování.
1. Sada SDK přidá `tracestate` do vlastnosti Message, která obsahuje časové razítko vytvoření zprávy.
1. Zařízení IoT zprávu pošle IoT Hub.
1. Zpráva se dorazí do brány služby IoT Hub.
1. IoT Hub vyhledá `tracestate` ve vlastnostech zprávy a zkontroluje, jestli je ve správném formátu.
1. V takovém případě IoT Hub pro zprávu vytvoří globálně jedinečný `trace-id` , `span-id` pro "směrování" a zaprotokoluje je do [IoT Hub distribuovaných trasovacích protokolů](monitor-iot-hub-reference.md#distributed-tracing-preview) v rámci operace `DiagnosticIoTHubD2C` .
1. Po dokončení zpracování zprávy IoT Hub generuje další `span-id` a zaprotokoluje je spolu s existující `trace-id` operací `DiagnosticIoTHubIngress` .
1. Pokud je pro zprávu zapnuté směrování, IoT Hub zapíše do vlastního koncového bodu a zaznamená jiný `span-id` se stejnou `trace-id` kategorií `DiagnosticIoTHubEgress` .
1. Výše uvedené kroky se opakují pro každou vygenerovanou zprávu.

## <a name="public-preview-limits-and-considerations"></a>Omezení a požadavky pro veřejnou verzi Preview

- Návrh pro standardní kontext trasování W3C je aktuálně pracovní koncept.
- V současné době je jediným vývojovým jazykem podporovaným klientskou sadou SDK C.
- Pro [IoT Hub úrovně Basic](iot-hub-scaling.md#basic-and-standard-tiers)není k dispozici zdvojená schopnost cloudového zařízení. IoT Hub se však stále protokoluje Azure Monitor, pokud se zobrazí správně sestavená hlavička kontextu trasování.
- Aby se zajistila efektivita provozu, IoT Hub omezí rychlost protokolování, která se může vyskytnout jako součást distribuovaného trasování.

## <a name="next-steps"></a>Další kroky

- Další informace o obecném modelu distribuovaného trasování v mikroslužbách najdete v tématu [model architektury mikroslužeb: distribuované trasování](https://microservices.io/patterns/observability/distributed-tracing.html).
- Pokud chcete nastavit konfiguraci pro použití nastavení distribuovaného trasování na velký počet zařízení, přečtěte si téma [Konfigurace a monitorování škálování zařízení IoT](./iot-hub-automatic-device-management.md).
- Další informace o Azure Monitor najdete v tématu [co je Azure monitor?](../azure-monitor/overview.md).
- Další informace o použití Azure Monitor se službou IoT HUb najdete v tématu [monitorování IoT Hub](monitor-iot-hub.md)