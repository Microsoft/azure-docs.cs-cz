---
title: Monitorování a řešení potíží odpojení pomocí Azure IoT Hub
description: Naučte se monitorovat a řešit běžné chyby s připojením zařízení pro Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: f7073fbf39344fe39e179d55a5a8f395a6ba6240
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357383"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Monitorování, diagnostika a řešení potíží odpojení pomocí Azure IoT Hub

Problémy s připojením pro zařízení IoT můžou být obtížné řešit, protože existuje mnoho možných bodů selhání. Služba Application Logic, fyzické sítě, protokoly, hardware, IoT Hub a další cloudové služby mohou způsobovat problémy. Možnost detekovat a identifikovat zdroj problému je kritická. Řešení IoT ve velkém měřítku ale může mít tisíce zařízení, takže není praktické kontrolovat jednotlivá zařízení ručně. IoT Hub se integruje se dvěma službami Azure, které vám pomůžou:

* **Azure monitor** Aby vám pomohl detekovat, diagnostikovat a řešit tyto problémy ve velkém měřítku, využijte možnosti monitorování IoT Hub poskytuje prostřednictvím Azure Monitor. To zahrnuje nastavení výstrah pro aktivaci oznámení a akcí po odpojení a konfiguraci protokolů, které můžete použít ke zjištění podmínek, které způsobily odpojení.

* **Azure Event Grid** V případě odpojení kritické infrastruktury a jednotlivých zařízení použijte Azure Event Grid k přihlášení k odběru událostí připojení a odpojení zařízení vygenerovaných IoT Hub.

V obou případech se tyto možnosti omezí na to, co IoT Hub může sledovat, proto doporučujeme, abyste pro vaše zařízení a další služby Azure dodržovali osvědčené postupy monitorování.

## <a name="event-grid-vs-azure-monitor"></a>Event Grid vs. Azure Monitor

Event Grid poskytuje řešení pro monitorování s nízkou latencí pro zařízení, které můžete použít ke sledování připojení zařízení pro kritická zařízení a infrastrukturu. Azure Monitor poskytuje metriku, *připojená zařízení* , pomocí kterých můžete monitorovat počet zařízení připojených k vaší IoT Hub a aktivovat výstrahu, když číslo klesne pod statickou prahovou hodnotu.

Při rozhodování, jestli použít Event Grid nebo Azure Monitor pro konkrétní scénář zvažte následující:

* Latence výstrahy: události připojení IoT Hub jsou během Event Grid dodány mnohem rychleji. Díky tomu je Event Grid lepší volbou pro scénáře, kde je vhodné rychlé oznámení.

* Oznámení na zařízení: Event Grid poskytuje možnost sledovat připojení a odpojení pro jednotlivá zařízení. Díky tomu je Event Grid lepší volbou pro scénáře, kdy potřebujete monitorovat připojení pro kritická zařízení.

* Odlehčená instalace: Azure Monitor výstrahy metriky poskytují odlehčené možnosti nastavení, které nevyžadují integraci s jinými službami pro doručování oznámení prostřednictvím e-mailu, SMS, hlasu a dalších oznámení.  S Event Grid je potřeba při doručování oznámení integrovat s dalšími službami Azure. Obě služby lze integrovat s jinými službami a aktivovat tak složitější akce.

Vzhledem k zařízením s nízkou latencí a možnostmi pro produkční prostředí důrazně doporučujeme použít Event Grid ke sledování připojení. Samozřejmě volba není exkluzivní, můžete použít jak výstrahy Azure Monitor metriky, tak Event Grid. Bez ohledu na to, co jste vybrali pro sledování odpojení, budete pravděpodobně používat protokoly Azure Monitor prostředků, které vám pomohou vyřešit příčiny neočekávaného odpojení zařízení. V následujících částech jsou podrobněji popsány jednotlivé možnosti.

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>Event Grid: sledování událostí připojení a odpojení zařízení

Pokud chcete monitorovat události připojení a odpojení zařízení v produkčním prostředí, doporučujeme přihlásit se k odběru [událostí **DeviceConnected** a **DeviceDisconnected**](iot-hub-event-grid.md#event-types) v Event Grid, aby se aktivovaly výstrahy a sledovaly stav připojení zařízení. Event Grid poskytuje mnohem nižší latenci události než Azure Monitor a můžete monitorovat podle jednotlivých zařízení a nemusíte tak mít celkový počet připojených zařízení. Tyto faktory se Event Grid upřednostňovanou metodou monitorování důležitých zařízení a infrastruktury.

Když použijete Event Grid ke sledování nebo aktivaci výstrah při odpojení zařízení, nezapomeňte sestavit způsob, jak odfiltrovat pravidelné odpojení z důvodu obnovení tokenu SAS na zařízeních, která používají sady SDK Azure IoT. Další informace najdete v tématu [chování odpojení zařízení MQTT se sadami Azure IoT SDK](#mqtt-device-disconnect-behavior-with-azure-iot-sdks).

V následujících tématech najdete další informace o monitorování událostí připojení zařízení pomocí Event Grid:

* Přehled použití Event Grid s IoT Hub naleznete v tématu [reakce na události IoT Hub s Event Grid](iot-hub-event-grid.md). Věnujte zvláštní pozornost částem [omezení pro události připojené k zařízení a odpojené zařízení](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) .

* Kurz týkající se řazení událostí připojení zařízení najdete v tématu [pořadí událostí připojení zařízení z Azure IoT Hub pomocí Azure Cosmos DB](iot-hub-how-to-order-connection-state-events.md).

* Kurz posílání e-mailových oznámení najdete v tématu [posílání e-mailových oznámení o událostech Azure IoT Hub pomocí Event Grid a Logic Apps](/azure/event-grid/publish-iot-hub-events-to-logic-apps) v dokumentaci k Event Grid.

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor: směrování událostí připojení do protokolů

IoT Hub průběžně generuje protokoly prostředků pro několik kategorií operací. Chcete-li shromáždit tato data protokolu, je třeba vytvořit nastavení diagnostiky pro směrování do cílového umístění, kde lze analyzovat nebo archivovat. Jedno takové určení je Azure Monitor protokoly přes pracovní prostor Log Analytics ([Viz ceny](https://azure.microsoft.com/pricing/details/log-analytics/)), kde můžete analyzovat data pomocí dotazů Kusto.

Kategorie IoT Hub [protokoly prostředků připojení](monitor-iot-hub-reference.md#connections) vygeneruje operace a chyby, které je nutné provést s připojením zařízení. Následující snímek obrazovky ukazuje nastavení diagnostiky, které tyto protokoly směrují do Log Analytics pracovního prostoru:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Doporučené nastavení pro odesílání protokolů připojení do pracovního prostoru Log Analytics.":::

Doporučujeme vytvořit nastavení diagnostiky co nejdříve po vytvoření služby IoT Hub, protože, i když IoT Hub vždy generuje protokoly prostředků, nejsou shromažďovány Azure Monitor dokud je nebudete směrovat do cílového umístění.

Další informace o protokolech směrování do cíle najdete v tématu [shromažďování a směrování](monitor-iot-hub.md#collection-and-routing). Podrobné pokyny k vytvoření nastavení diagnostiky najdete v [kurzu použití metrik a protokolů](tutorial-use-metrics-and-diags.md).

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Azure Monitor: nastavení výstrah metrik pro odpojení zařízení ve velkém měřítku

Můžete nastavit výstrahy na základě metrik platforem vysílaných IoT Hub. S upozorněními na metriky můžete určit, že došlo ke splnění podmínky zájmu, a také aktivovat akce, které mohou automaticky reagovat na tuto podmínku.

Metrika [*připojených zařízení (Preview)*](monitor-iot-hub-reference.md#device-metrics) oznamuje, kolik zařízení je připojeno k vašemu IoT Hub. Můžete vytvářet výstrahy, které se aktivují, pokud metrika klesne pod prahovou hodnotu:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="Nastavení logiky výstrahy pro metriku připojených zařízení.":::

Pomocí pravidel upozornění na metriky můžete monitorovat anomálie odpojení zařízení ve velkém měřítku. To znamená, že když se neočekávaně odpojí velký počet zařízení. Pokud je takový výskyt zjištěn, můžete se podívat na protokoly, které vám pomohou problém vyřešit. Pokud chcete monitorovat odpojení podle zařízení a odpojuje je s důležitými zařízeními; je však nutné použít Event Grid. Event Grid také nabízí další možnosti v reálném čase než metriky Azure.

Další informace o výstrahách s IoT Hub najdete v tématu [výstrahy v IoT Hub monitorování](monitor-iot-hub.md#alerts). Návod k vytváření výstrah v IoT Hub najdete v [kurzu použití metrik a protokolů](tutorial-use-metrics-and-diags.md). Podrobnější přehled výstrah najdete v tématu [Přehled výstrah v Microsoft Azure](../azure-monitor/platform/alerts-overview.md) v dokumentaci k Azure monitor.

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor: k vyřešení chyb připojení použijte protokoly.

Když zjistíte odpojení zařízení, ať už se jedná o výstrahy Azure Monitor metriky nebo Event Grid, můžete k vyřešení příčiny použít protokoly. Tato část popisuje, jak vyhledat běžné problémy v protokolech Azure Monitor. Následující postup předpokládá, že jste již vytvořili [nastavení diagnostiky](#azure-monitor-route-connection-events-to-logs) pro odesílání protokolů IoT Hub připojení do pracovního prostoru Log Analytics.

Po vytvoření nastavení diagnostiky pro směrování IoT Hubch protokolů prostředků do protokolů Azure Monitor postupujte podle následujících kroků, abyste zobrazili protokoly v Azure Portal.

1. V [Azure Portal](https://portal.azure.com)přejděte do služby IoT Hub.

1. V části **monitorování** v levém podokně Centra IoT vyberte **protokoly**.

1. Pokud chcete izolovat protokoly chyb připojení pro IoT Hub, zadejte v editoru dotazů následující dotaz a pak vyberte **Spustit** :

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Pokud jsou k dispozici výsledky, vyhledejte `OperationName` , `ResultType` (kód chyby) a `ResultDescription` (chybová zpráva), abyste získali více podrobností o chybě.

   ![Příklad protokolu chyb](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

Po identifikaci chyby použijte Průvodce řešením problémů, který vám pomůže s nejběžnějšími chybami:

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>MQTT chování odpojení zařízení pomocí sad Azure IoT SDK

Sady SDK pro zařízení Azure IoT se odpojí od IoT Hub a pak se znovu připojí, když obnoví tokeny SAS prostřednictvím protokolu MQTT (a MQTT přes WebSockets). V protokolech se zobrazuje jako informativní události odpojení a připojení zařízení, které jsou někdy doprovázeny chybovými událostmi.

Ve výchozím nastavení platí, že životnost tokenu je 60 minut pro všechny sady SDK. může je však změnit vývojáři v některých sadách SDK. Následující tabulka shrnuje životnost tokenu, obnovení tokenu a chování tokenu pro jednotlivé sady SDK:

| Sada SDK | Životnost tokenu | Obnovení tokenu | Chování při obnovení |
|-----|----------|---------------------|---------|
| .NET | 60 minut, konfigurovatelný | 85% životnosti, konfigurovatelný | Sada SDK se připojuje a odpojuje s životností tokenu a období odkladu na 10 minut. Informační události a chyby generované v protokolech. |
| Java | 60 minut, konfigurovatelný | 85% životnosti, Nekonfigurovatelné | Sada SDK se připojuje a odpojuje s životností tokenu a období odkladu na 10 minut. Informační události a chyby generované v protokolech. |
| Node.js | 60 minut, konfigurovatelný | konfigurovatelné | Sada SDK se připojuje a odpojuje při obnovení tokenu. V protokolech jsou vygenerovány pouze informativní události. |
| Python | 60 minut, Nekonfigurovatelné | -- | Sada SDK se připojuje a odpojuje při životnosti tokenu. |

Následující snímky obrazovky ukazují chování při obnovení tokenu v Azure Monitor protokoly pro různé sady SDK. Životnost tokenu a prahová hodnota pro obnovení se od výchozích hodnot změnily, jak je uvedeno níže.

* Sada SDK pro zařízení .NET s 1200 sekundou (20 minut) životnosti tokenů a obnovení nastavenou na 90% životnosti. k odpojení dochází každých 30 minut:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text="Chování při obnovení tokenu přes MQTT v protokolu Azure Monitor pomocí sady .NET SDK.":::

* Sada Java SDK s 300 sekundou (5 minut) životnosti tokenu a výchozí 85% obnovování životnosti. K odpojení dochází každých 15 minut:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Chování při obnovení tokenu přes MQTT v protokolu Azure Monitor pomocí Java SDK.":::

* Sada SDK pro Node s 300 sekundou (5 minut) životnosti tokenů a obnovení tokenu, která se má provést během 3 minut. Při obnovení tokenu dojde k odpojení. Neexistují také žádné chyby, jsou generovány pouze informativní události připojení a odpojení:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Chování při obnovení tokenu přes MQTT v protokolu Azure Monitor pomocí sady SDK pro Node.":::

Následující dotaz se použil ke shromáždění výsledků. Dotaz vyextrahuje název a verzi sady SDK ze kontejneru objektů a dat. Další informace najdete v tématu [verze sady SDK v protokolu IoT Hub](monitor-iot-hub.md#sdk-version-in-iot-hub-logs).

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

Jako vývojář nebo operátor řešení IoT musíte být vědomi tohoto chování, aby bylo možné interpretovat události připojení/odpojení a související chyby v protokolech. Pokud chcete změnit životnost tokenu nebo chování při obnovení pro zařízení, zkontrolujte, jestli zařízení implementuje nastavení s dvojitým stavem zařízení, nebo metodu zařízení, která to umožňuje.

Pokud sledujete připojení zařízení s centrem událostí, ujistěte se, že jste vytvořili způsob, jak filtrovat pravidelné odpojení z důvodu obnovení tokenu SAS. například neaktivuje akce na základě odpojení, pokud za událostí odpojení následuje událost připojení v určitém časovém období.

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Vyzkoušel jsem postup, ale nefungoval

Pokud vám předchozí kroky neudělaly, zkuste:

* Pokud máte přístup k problematickým zařízením buď fyzicky, nebo vzdáleně (jako je SSH), postupujte podle pokynů v [Průvodci odstraňováním potíží na straně zařízení](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) a pokračujte v řešení potíží.

* Ověřte, jestli jsou zařízení **povolená** v Azure Portal > vaše zařízení IoT ve službě iot hub >.

* Pokud zařízení používá protokol MQTT, ověřte, že je otevřený port 8883. Další informace najdete v tématu [připojení k IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Získejte pomoc od [Microsoft Q&stránku s otázkou pro azure IoT Hub](/answers/topics/azure-iot-hub.html), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)nebo [podporu Azure](https://azure.microsoft.com/support/options/).

Pokud vám tato příručka nepomohly vylepšit dokumentaci pro všechny uživatele, ponechte v části zpětná vazba komentář.

## <a name="next-steps"></a>Další kroky

* Další informace o řešení přechodných problémů naleznete v tématu [zpracování přechodných chyb](/azure/architecture/best-practices/transient-faults).

* Další informace o sadě Azure IoT SDK a správě opakování najdete v tématu [Správa připojení a spolehlivého zasílání zpráv pomocí sad SDK pro zařízení azure IoT Hub](iot-hub-reliability-features-in-sdks.md#connection-and-retry).