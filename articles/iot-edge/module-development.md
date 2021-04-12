---
title: Vývoj modulů pro Azure IoT Edge | Microsoft Docs
description: Vývoj vlastních modulů pro Azure IoT Edge, které mohou komunikovat s modulem runtime a IoT Hub
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6274d0f0265220877938775ab6efd72ff22b84e4
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031117"
---
# <a name="develop-your-own-iot-edge-modules"></a>Vývoj vlastních modulů IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge moduly se můžou připojit k ostatním službám Azure a přispívat do vašeho většího cloudového datového kanálu. Tento článek popisuje, jak můžete vyvíjet moduly pro komunikaci s IoT Edge modulem runtime a IoT Hub a tedy i zbytek cloudu Azure.

## <a name="iot-edge-runtime-environment"></a>Prostředí IoT Edge runtime

Modul runtime IoT Edge poskytuje infrastrukturu pro integraci funkcí více modulů IoT Edge a jejich nasazení na IoT Edge zařízení. Každý program může být zabalen jako modul IoT Edge. Aby bylo možné plně využít výhod IoT Edge komunikace a správy, program spuštěný v modulu může použít sadu SDK pro zařízení Azure IoT k připojení k místnímu centru IoT Edge.
::: moniker range=">=iotedge-2020-11"
Moduly můžou použít taky libovolného klienta MQTT pro připojení k místnímu zprostředkovateli služby MQTT centra IoT Edge.
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>Balení programu jako modulu IoT Edge

Aby bylo možné program nasadit na IoT Edge zařízení, musí být nejprve kontejnerem a spuštěn s modulem kompatibilním s Docker. IoT Edge používá [Moby](https://github.com/moby/moby)open source projekt za Docker, jako jeho modul kompatibilní s Docker. Stejné parametry, které jste použili pro použití Docker, lze předat do vašich IoT Edgech modulů. Další informace najdete v tématu [jak nakonfigurovat možnosti vytváření kontejnerů pro IoT Edge moduly](how-to-use-create-options.md).

## <a name="using-the-iot-edge-hub"></a>Použití centra IoT Edge

Centrum IoT Edge poskytuje dvě hlavní funkce: proxy a IoT Hub a místní komunikaci.

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>Připojení k centru IoT Edge z modulu

Připojení k místnímu rozbočovači IoT Edge z modulu zahrnuje stejné kroky připojení jako pro všechny klienty. Další informace najdete v tématu [připojení k centru IoT Edge](iot-edge-runtime.md#connecting-to-the-iot-edge-hub).

Pokud chcete použít IoT Edge směrování přes AMQP nebo MQTT, můžete použít ModuleClient ze sady Azure IoT SDK. Vytvořte instanci ModuleClient pro připojení modulu k rozbočovači IoT Edge běžícímu na zařízení, podobně jako instance DeviceClient připojují zařízení IoT k IoT Hub. Další informace o třídě ModuleClient a jejích metodách komunikace najdete v referenčních informacích k rozhraní API pro preferovaný jazyk sady SDK: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)nebo [Node.js](/javascript/api/azure-iot-device/moduleclient).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Pokud chcete použít IoT Edge Broker MQTT, musíte si přenést vlastního klienta MQTT a iniciovat připojení sami s informacemi, které načtete z rozhraní API úlohy démona IoT Edge. <!--Need to add details here-->

Další informace o volbě mezi směrováním nebo publikováním/odběrem služby MQTT Broker najdete v tématu [místní komunikace](iot-edge-runtime.md#local-communication).

### <a name="mqtt-broker-primitives"></a>Primitiva služby MQTT Broker

#### <a name="send-a-message-on-a-user-defined-topic"></a>Odeslání zprávy v uživatelsky definovaném tématu

Pomocí zprostředkovatele IoT Edge MQTT můžete publikovat zprávy v jakémkoli uživatelsky definovaná témata. Provedete to tak, že autorizujete modul pro publikování v konkrétních tématech a pak získáte token z rozhraní API pro úlohy, který se použije jako heslo při připojování ke zprostředkovateli MQTT, a nakonec publikovat zprávy v autorizovaných tématech s klientem MQTT podle vašeho výběru.

#### <a name="receive-messages-on-a-user-defined-topic"></a>Přijímání zpráv v uživatelsky definovaném tématu

U zprostředkovatele IoT Edge MQTT je příjem zpráv podobný. Nejdřív se ujistěte, že je váš modul autorizovaný pro přihlášení k odběru konkrétních témat, a pak Získejte token z rozhraní API úlohy, který se použije jako heslo při připojování ke zprostředkovateli MQTT, a nakonec se přihlásit k odběru zpráv v autorizovaných tématech s klientem MQTT podle vašeho výběru.

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT Hub primitivních elementů

IoT Hub uvidí instanci modulu obdobně na zařízení v tom smyslu, že:

* může posílat [zprávy ze zařízení do cloudu](../iot-hub/iot-hub-devguide-messaging.md);
* může přijímat [přímé metody](../iot-hub/iot-hub-devguide-direct-methods.md) zaměřené na svou identitu.
* má nevlákenný modul, který je odlišný a izolovaný od [vlákna zařízení](../iot-hub/iot-hub-devguide-device-twins.md) a druhý modul je v tomto zařízení vyzdvojený.

V současné době moduly nemůžou přijímat zprávy z cloudu na zařízení nebo používat funkci pro nahrání souborů.

Při psaní modulu se můžete připojit k centru IoT Edge a používat IoT Hub primitiv jako při použití IoT Hub s aplikací pro zařízení. Jediným rozdílem mezi IoT Edge moduly a aplikacemi IoT Device je, že musíte odkazovat na identitu modulu místo identity zařízení.

#### <a name="device-to-cloud-messages"></a>Zprávy typu zařízení-cloud

Modul IoT Edge může posílat zprávy do cloudu prostřednictvím centra IoT Edge, které funguje jako místní zprostředkovatel a šíří zprávy do cloudu. Aby bylo možné povolit složité zpracování zpráv typu zařízení-Cloud, IoT Edge modul může také zachytit a zpracovat zprávy odesílané jinými moduly nebo zařízeními do svého místního centra IoT Edge a odesílat nové zprávy se zpracovanými daty. Řetězy IoT Edgech modulů je proto možné vytvořit pro vytváření místních kanálů zpracování.

Pokud chcete odesílat zprávy telemetrie typu zařízení-Cloud pomocí směrování, použijte ModuleClient sady Azure IoT SDK. Pomocí sady Azure IoT SDK má každý modul koncept *vstupních* a *výstupních* koncových bodů modulu, který se MAPUJE na speciální témata MQTT. Použijte `ModuleClient.sendMessageAsync` metodu a odešle zprávy na výstupní koncový bod vašeho modulu. Pak nakonfigurujte trasu v edgeHub pro odeslání tohoto výstupního koncového bodu do IoT Hub.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Posílání zpráv telemetrie ze zařízení do cloudu pomocí zprostředkovatele MQTT se podobá publikování zpráv u uživatelsky definovaných témat, ale pro váš modul používá následující IoT Hub speciální téma: `devices/<device_name>/<module_name>/messages/events` . Autorizace se musí nastavit správně. Pro přeposílání zpráv v tomto tématu do cloudu musí být také nakonfigurován most MQTT.

::: moniker-end

Pokud chcete zpracovávat zprávy pomocí směrování, nejdřív nastavte trasu pro odesílání zpráv přicházejících z jiného koncového bodu (modulu nebo zařízení) do vstupního koncového bodu vašeho modulu a pak naslouchat zprávám ve vstupním koncovém bodu vašeho modulu. Pokaždé, když se nová zpráva vrátí zpátky, aktivuje se funkce zpětného volání v sadě Azure IoT SDK. Zpracujte zprávu pomocí této funkce zpětného volání a volitelně odešlete nové zprávy ve frontě koncového bodu modulu.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Zpracování zpráv pomocí zprostředkovatele MQTT se podobá přihlášení k odběru zpráv v uživatelsky definovaném textu, ale pomocí IoT Edge zvláštních témat z výstupní fronty vašeho modulu: `devices/<device_name>/<module_name>/messages/events` . Autorizace se musí nastavit správně. Volitelně můžete odesílat nové zprávy podle vámi zvolených témat.

::: moniker-end

#### <a name="twins"></a>Dvojčata

Vlákna jsou jedním z primitivních elementů, které poskytuje IoT Hub. K dispozici jsou dokumenty JSON, které obsahují informace o stavu, včetně metadat, konfigurací a podmínek. Každý modul nebo zařízení má vlastní vlákna.

Chcete-li vytvořit modul s využitím sady Azure IoT SDK, zavolejte `ModuleClient.getTwin` metodu.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Aby bylo možné vytvořit modul bez jakéhokoli klienta MQTT, je zapojena trochu více práce, protože získání vlákna není typický vzor MQTT. Modul se musí nejdřív přihlásit k odběru IoT Hub speciálnímu tématu `$iothub/twin/res/#` . Tento název tématu se dědí z IoT Hub a všechna zařízení a moduly se musí přihlásit k odběru stejného tématu. Neznamená to, že zařízení dostanou vlákna navzájem. IoT Hub a edgeHub ví, která vlákna by měla být doručena tam, kde, i když všechna zařízení naslouchají stejnému názvu tématu. Po vytvoření předplatného musí modul vyžadovat, aby vystavil dvojitou zprávu publikováním zprávy do následujících IoT Hub speciální téma s ID požadavku `$iothub/twin/GET/?$rid=1234` . ID tohoto požadavku je libovolné ID (tj. identifikátor GUID), které se IoT Hub pošle spolu s požadovanými daty. V takovém případě může klient spárovat své žádosti s odpověďmi. Kód výsledku je stavový kód podobný protokolu HTTP, kde úspěch je kódován jako 200.

::: moniker-end

Pokud chcete přijmout nefunkční opravu modulu se sadou Azure IoT SDK, implementujte funkci zpětného volání a zaregistrujte ji pomocí `ModuleClient.moduleTwinCallback` metody ze sady Azure IoT SDK tak, aby se funkce zpětného volání aktivovala při každém spuštění zdvojené opravy.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Aby se získala dvojitá oprava modulu s jakýmkoli klientem MQTT, proces je velmi podobný jako příjem úplných vláken: klient se musí přihlásit k odběru speciálního IoT Hub tématu `$iothub/twin/PATCH/properties/desired/#` . Po vytvoření předplatného, když IoT Hub odešle změnu požadované části vlákna, klient obdrží.

::: moniker-end

#### <a name="receive-direct-methods"></a>Příjem přímých metod

Pokud chcete získat přímou metodu se sadou Azure IoT SDK, implementujte funkci zpětného volání a zaregistrujte ji pomocí `ModuleClient.methodCallback` metody ze sady Azure IoT SDK tak, aby se funkce zpětného volání aktivovala pokaždé, když do ní přijde přímá metoda.

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

Aby bylo možné získat přímou metodu s jakýmkoli klientem MQTT, proces je velmi podobný jako při přijímání dvojitých oprav. Klient musí potvrdit, že přijal hovor, a může poslat zpátky nějaké informace současně. Speciální téma IoT Hub k přihlášení k odběru `$iothub/methods/POST/#` .

::: moniker-end

## <a name="language-and-architecture-support"></a>Podpora jazyků a architektur

IoT Edge podporuje různé operační systémy, architektury zařízení a vývojové jazyky, abyste mohli sestavit scénář vyhovující vašim potřebám. V této části můžete porozumět vašim možnostem při vývoji vlastních IoT Edgech modulů. Další informace o podpoře a požadavcích nástrojů pro jednotlivé jazyky získáte v [přípravě vývojového a testovacího prostředí pro IoT Edge](development-environment.md).

### <a name="linux"></a>Linux

Pro všechny jazyky v následující tabulce IoT Edge podporuje vývoj pro kontejnery AMD64 a ARM32 Linux.

| Vývojový jazyk | Vývojářské nástroje |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Podpora pro vývoj a ladění pro kontejnery ARM64 Linux je ve [verzi Public Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Další informace najdete v tématu [vývoj a ladění ARM64 IoT Edgech modulů v Visual Studio Code (Preview)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

### <a name="windows"></a>Windows

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Pro všechny jazyky v následující tabulce IoT Edge podporuje vývoj pro kontejnery Windows AMD64.

| Vývojový jazyk | Vývojářské nástroje |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (žádné možnosti ladění)<br>Visual Studio 2017/2019 |
:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

IoT Edge 1,1 LTS je poslední kanál verze, který podporuje kontejnery Windows. Od verze 1,2 nejsou kontejnery Windows podporované.

Informace o vývoji v kontejnerech Windows najdete v článku verze [IoT Edge 1,1](?view=iotedge-2018-06&preserve-view=true) tohoto článku.

:::moniker-end
<!-- end 1.2 -->

## <a name="next-steps"></a>Další kroky

[Příprava vývojového a testovacího prostředí pro IoT Edge](development-environment.md)

[Použití sady Visual Studio k vývoji modulů C# pro IoT Edge](how-to-visual-studio-develop-module.md)

[Použití Visual Studio Code k vývoji modulů pro IoT Edge](how-to-vs-code-develop-module.md)

[Principy a použití sad SDK služby Azure IoT Hub](../iot-hub/iot-hub-devguide-sdks.md).
