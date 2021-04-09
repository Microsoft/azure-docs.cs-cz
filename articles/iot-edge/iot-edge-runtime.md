---
title: Přečtěte si, jak modul runtime spravuje zařízení – Azure IoT Edge | Microsoft Docs
description: Přečtěte si, jak IoT Edge runtime spravuje moduly, zabezpečení, komunikaci a vytváření sestav na vašich zařízeních.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 74cfe4ba3c92d8d96dd196ef6f612b9ed7c0da9d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103496248"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Pochopení Azure IoT Edge runtime a jeho architektury

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Modul runtime IoT Edge je kolekce programů, které ze zařízení udělají zařízení IoT Edge. Souhrnně IoT Edge komponenty modulu runtime umožňují zařízením IoT Edge přijímat kód, který se má spustit na hranici a sdělit výsledky.

IoT Edge runtime zodpovídá za následující funkce na IoT Edge zařízeních:

* Instalace a aktualizace úloh na zařízení.

* Udržujte na zařízení standardy zabezpečení Azure IoT Edge.

* Ujistěte se, že [moduly IoT Edge](iot-edge-modules.md) jsou vždycky spuštěné.

* Oznamte stav modulu v cloudu pro vzdálené monitorování.

* Spravujte komunikaci mezi zařízeními pro příjem dat a IoT Edgemi zařízeními.

* Spravujte komunikaci mezi moduly na zařízení IoT Edge.

* Spravujte komunikaci mezi IoT Edgem zařízením a cloudem.
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* Spravujte komunikaci mezi IoT Edgemi zařízeními.
::: moniker-end

![Modul runtime komunikuje s přehledy a stavem modulu IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Odpovědnosti modulu runtime IoT Edge spadají do dvou kategorií: komunikace a Správa modulů. Tyto dvě role provádí dvě komponenty, které jsou součástí modulu runtime IoT Edge. *Agent IoT Edge* nasazuje a monitoruje moduly, zatímco *Centrum IoT Edge* zodpovídá za komunikaci.

Agent IoT Edge i Centrum IoT Edge jsou moduly, stejně jako jakýkoli jiný modul běžící na IoT Edgem zařízení. Někdy se označují jako *běhové moduly*.

## <a name="iot-edge-agent"></a>Agent IoT Edge

Agent IoT Edge je jedním ze dvou modulů, které tvoří modul runtime Azure IoT Edge. Zodpovídá za vytváření instancí modulů, zajišťuje, že budou nadále běžet, a oznamuje stav modulů zpět do IoT Hub. Tato konfigurační data jsou zapsána jako vlastnost vlákna modulu IoT Edgeho agenta.

[Démon zabezpečení IoT Edge](iot-edge-security-manager.md) spustí agenta IoT Edge při spuštění zařízení. Agent z IoT Hub načte svůj modul s nevlákenou a zkontroluje manifest nasazení. Manifest nasazení je soubor JSON, který deklaruje moduly, které musí být spuštěny.

Každá položka v manifestu nasazení obsahuje konkrétní informace o modulu, kterou používá agent IoT Edge k řízení životního cyklu modulu. Další informace o všech vlastnostech, které používá agent IoT Edge k řízení modulů, najdete v článku o [vlastnostech IoT Edge agenta a o tom, IoT Edge modul rozbočovače](module-edgeagent-edgehub.md).

Agent IoT Edge odesílá odezvu modulu runtime do IoT Hub. Tady je seznam možných odpovědí:
  
* 200 – OK
* 400 – konfigurace nasazení je poškozená nebo neplatná.
* 417 – zařízení nemá nastavenou konfiguraci nasazení.
* 412 – verze schématu v konfiguraci nasazení není platná.
* 406 – IoT Edge zařízení je offline nebo neodesílá zprávy o stavu.
* 500 – při IoT Edge modulu runtime došlo k chybě.

Další informace o vytváření manifestů nasazení najdete v tématu [Naučte se nasadit moduly a vytvořit trasy v IoT Edge](module-composition.md).

### <a name="security"></a>Zabezpečení

Agent IoT Edge hraje důležitou roli v zabezpečení zařízení IoT Edge. Například provede akce, jako je ověření obrázku modulu před jeho spuštěním.

Další informace o rozhraní Azure IoT Edge Security Framework najdete v článku o [IoT Edge Security Manager](iot-edge-security-manager.md).

## <a name="iot-edge-hub"></a>Centrum IoT Edge

IoT Edge centrum je druhý modul, který tvoří modul runtime Azure IoT Edge. Funguje jako místní proxy server pro IoT Hub tím, že zveřejňuje stejné koncové body protokolu jako IoT Hub. Tato konzistence znamená, že se klienti mohou připojit k modulu IoT Edge runtime stejným způsobem jako IoT Hub.

Centrum IoT Edge není plná verze IoT Hub spuštěná místně. Centrum IoT Edge tiše deleguje některé úkoly, které je potřeba IoT Hub. Například Centrum IoT Edge automaticky stáhne informace o autorizaci z IoT Hub při prvním připojení, aby bylo možné zařízení připojit. Po navázání prvního připojení se informace o autorizaci ukládají místně do mezipaměti IoT Edge hub. Budoucí připojení z tohoto zařízení mají oprávnění, aniž byste museli stahovat autorizační informace z cloudu znovu.

### <a name="cloud-communication"></a>Cloudová komunikace

Aby se snížila šířka pásma, kterou používá vaše IoT Edge řešení, Centrum IoT Edge optimalizuje, kolik skutečných připojení se do cloudu provedlo. Centrum IoT Edge přijímá logická připojení z modulů nebo podřízených zařízení a kombinuje je s jedním fyzickým připojením ke cloudu. Podrobnosti tohoto procesu jsou pro zbytek řešení transparentní. Klienti si budou myslet, že mají vlastní připojení ke cloudu, i když se všechny odesílají přes stejné připojení. Rozbočovač IoT Edge může buď použít protokol AMQP nebo MQTT ke komunikaci s cloudem, nezávisle na protokolech používaných zařízeními pro příjem dat. Centrum IoT Edge aktuálně podporuje pouze kombinování logických připojení do jednoho fyzického připojení pomocí AMQP jako nadřazeného protokolu a jeho možností multiplexování. AMQP je výchozí nadřazený protokol.

![Centrum IoT Edge je brána mezi fyzickými zařízeními a IoT Hub](./media/iot-edge-runtime/gateway-communication.png)

Centrum IoT Edge může určit, jestli je připojené k IoT Hub. Pokud dojde ke ztrátě připojení, IoT Edge centrum ukládá zprávy nebo bude mít zdvojené aktualizace místně. Jakmile je připojení znovu navázáno, synchronizuje všechna data. Umístění používané pro tuto dočasnou mezipaměť je určeno vlastností vlákna modulu IoT Edgeového rozbočovače. Velikost mezipaměti není omezené a bude růst, dokud má zařízení kapacitu úložiště. Další informace najdete v tématu [Možnosti offline](offline-capabilities.md).

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>Komunikace s modulem

Centrum IoT Edge usnadňuje komunikaci s modulem. Použití centra IoT Edge jako zprostředkovatel zpráv uchovává moduly nezávislé na sobě. Moduly stačí pouze zadat vstupy, na kterých přijímá zprávy, a výstupy, na které zapisují zprávy. Vývojář řešení může spojit tyto vstupy a výstupy, aby moduly zpracovával data v pořadí určeném pro toto řešení.

![IoT Edge hub usnadňuje komunikaci mezi moduly a moduly.](./media/iot-edge-runtime/module-endpoints.png)

Chcete-li odesílat data do centra IoT Edge, modul volá metodu SendEventAsync. První argument určuje, na který výstup bude zpráva odeslána. Následující pseudokódu odešle zprávu na **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Chcete-li získat zprávu, zaregistrujte zpětné volání, které zpracovává zprávy přicházející do konkrétního vstupu. Následující pseudokódu zaregistruje funkci messageProcessor, která se má použít ke zpracování všech zpráv přijatých v **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Další informace o třídě ModuleClient a jejích metodách komunikace najdete v referenčních informacích k rozhraní API pro preferovaný jazyk sady SDK: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)nebo [Node.js](/javascript/api/azure-iot-device/moduleclient).

Vývojář řešení zodpovídá za zadání pravidel, která určují, jak IoT Edge hub předává zprávy mezi moduly. Pravidla směrování jsou definovaná v cloudu a jsou vložená dolů do IoT Edgeho centra v jeho modulu. Stejná syntaxe pro IoT Hub trasy se používá k definování tras mezi moduly v Azure IoT Edge. Další informace najdete v tématu [Naučte se nasazovat moduly a navázat trasy v IoT Edge](module-composition.md).

![Trasy mezi moduly procházejí prostřednictvím centra IoT Edge.](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>Místní komunikace

Centrum IoT Edge usnadňuje místní komunikaci. Umožňuje vzájemnou komunikaci zařízení s modulem, modulu-modulu a komunikace zařízení a zařízení prostřednictvím zprostředkovatelských zpráv, aby se zařízení a moduly nezávisle na sobě navzájem nezávislá.

>[!NOTE]
> Funkce MQTT Broker je ve verzi Public Preview s IoT Edge verze 1,2. Musí být explicitně povolen.

Centrum IoT Edge podporuje dva mechanismy zprostředkovatele:

1. [Funkce směrování zpráv podporované nástrojem IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md) a,
2. Zprostředkovatel MQTT pro obecné účely, který splňuje [MQTT standard v 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)

#### <a name="using-routing"></a>Použití směrování

První mechanismus zprostředkovatele využívá stejné funkce směrování jako IoT Hub k určení toho, jak se zprávy předávají mezi zařízeními nebo moduly. První zařízení nebo moduly určují vstupy, na kterých budou přijímat zprávy, a výstupy, na které zapisují zprávy. Vývojář řešení pak může směrovat zprávy mezi zdrojem, například výstupy, a cílem, např. vstupy, s potenciálními filtry.

![Trasy mezi moduly procházejí prostřednictvím centra IoT Edge.](./media/iot-edge-runtime/module-endpoints-routing.png)

Směrování můžou používat zařízení nebo moduly sestavené pomocí sad SDK pro zařízení Azure IoT prostřednictvím protokolu AMQP nebo MQTT. Všechna zasílání zpráv IoT Hub primitivních, např. telemetrie, přímé metody, C2D a vlákna, jsou podporovány, ale komunikace prostřednictvím uživatelsky definovaných témat není podporována.

Další informace o trasách najdete v tématu [Naučte se nasazovat moduly a navázat trasy v IoT Edge](module-composition.md)

#### <a name="using-the-mqtt-broker"></a>Použití zprostředkovatele MQTT

Druhý mechanismus zprostředkovatele je založený na standardním zprostředkovateli MQTT. MQTT je odlehčený protokol pro přenos zpráv, který zaručuje optimální výkon na omezených zařízeních prostředků a je oblíbeným standardem pro publikování a přihlášení k odběru. Zařízení nebo moduly se přihlásí k odběru témat pro příjem zpráv publikovaných jinými zařízeními nebo moduly. IoT Edge hub implementuje svého vlastního zprostředkovatele MQTT, který následuje [za specifikacemi MQTT verze 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html).

Zprostředkovatel MQTT umožňuje dva další způsoby komunikace ve srovnání se směrováním: místní všesměrové vysílání a komunikace Point-to-Point. Místní vysílání je užitečné v případě, že jedno zařízení nebo modul potřebuje k místnímu upozorňování více zařízení nebo modulů. Komunikace Point-to-Point umožňuje dvě IoT Edge zařízení nebo dvě zařízení IoT, aby se místně komunikovala bez zpátečního přenosu do cloudu.

![Místní publikování a přihlášení k odběru pomocí centra IoT Edge](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

Zprostředkovatele MQTT můžou používat zařízení nebo moduly sestavené pomocí sad SDK pro zařízení Azure IoT, které komunikují prostřednictvím protokolu MQTT nebo všech klientů MQTT pro obecné účely. S výjimkou C2D jsou podporovány všechny primitivní IoT Hub, např. telemetrie, přímé metody a vlákna. Jsou podporována IoT Hub speciální témata používaná IoT Hub primitivními prvky, a to i v uživatelsky definovaných tématech.
Toto téma může být IoT Hub speciální téma nebo uživatelsky definované téma.

Na rozdíl od mechanismu směrování, řazení zpráv je pouze nejlepší úsilí a není zaručeno a filtrování zpráv není podporováno zprostředkovatelem. Nedostatek těchto funkcí ale umožňuje, aby zprostředkovatel MQTT byl rychlejší než směrování.

Další informace o zprostředkovateli MQTT najdete v tématu věnovaném [publikování a přihlášení k odběru pomocí IoT Edge](how-to-publish-subscribe.md)

#### <a name="comparison-between-brokering-mechanisms"></a>Porovnání mezi mechanismy zprostředkovatelů

Tady jsou funkce, které jsou dostupné u každého mechanismu zprostředkovatelů:

|Funkce  | Směrování  | Zprostředkovatel MQTT  |
|---------|---------|---------|
|Telemetrie D2C    |     &#10004;    |         |
|Místní telemetrie     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|Vyjde     |    &#10004;     |    &#10004;     |
|C2D pro zařízení     |   &#10004;      |         |
|Řazení     |    &#10004;     |         |
|Filtrování     |     &#10004;    |         |
|Uživatelsky definovaná témata     |         |    &#10004;     |
|Zařízení na zařízení     |         |    &#10004;     |
|Místní vysílání     |         |    &#10004;     |
|Výkon     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>Připojení k centru IoT Edge

Centrum IoT Edge akceptuje připojení z klientů zařízení nebo modulů, a to buď prostřednictvím protokolu MQTT, nebo pomocí protokolu AMQP.

>[!NOTE]
> Centrum IoT Edge podporuje klienty, kteří se připojují pomocí MQTT nebo AMQP. Nepodporuje klienty, kteří používají protokol HTTP.

Když se klient připojí k centru IoT Edge, dojde k následujícímu:

1. Pokud se používá TLS (Transport Layer Security) (doporučeno), je kanál TLS sestavený tak, aby navázal šifrovanou komunikaci mezi klientem a centra IoT Edge.
2. Informace o ověřování jsou odesílány z klienta nástroje do centra IoT Edge k identifikaci sebe sama.
3. Centrum IoT Edge autorizuje nebo odmítne připojení na základě zásad autorizace.

#### <a name="secure-connections-tls"></a>Zabezpečená připojení (TLS)

Ve výchozím nastavení přijímá rozbočovač IoT Edge jenom připojení zabezpečená protokolem TLS (Transport Layer Security), třeba šifrovaná připojení, která třetí strana nemůže dešifrovat.

Pokud se klient připojí k rozbočovači IoT Edge na portu 8883 (MQTTS) nebo 5671 (AMQPS), musí se vytvořit kanál TLS. Během metody handshake TLS odesílá IoT Edge rozbočovač svůj řetěz certifikátů, který musí klient ověřit. Aby bylo možné ověřit řetěz certifikátů, kořenový certifikát centra IoT Edge musí být nainstalován jako důvěryhodný certifikát na klientovi. Pokud kořenový certifikát není důvěryhodný, bude centrum klienta v IoT Edge odmítnuto s chybou ověření certifikátu.

Postup pro instalaci tohoto kořenového certifikátu zprostředkovatele na klientech zařízení je popsaný v [transparentní bráně](how-to-create-transparent-gateway.md) a v dokumentaci [Příprava na zařízení pro příjem dat](how-to-connect-downstream-device.md#prepare-a-downstream-device) . Moduly můžou používat stejný kořenový certifikát jako centrum IoT Edge, a to prostřednictvím rozhraní API pro IoT Edge démona.

#### <a name="authentication"></a>Authentication

Centrum IoT Edge akceptuje jenom připojení ze zařízení nebo modulů, které mají IoT Hub identitu, třeba zaregistrovali v IoT Hub a mají jednu ze tří metod ověřování klientů, které služba IoT Hub podporuje, aby prokázala jejich identitu: [ověřování symetrických klíčů](how-to-authenticate-downstream-device.md#symmetric-key-authentication), ověřování pomocí 509 podepsané [svým držitelem](how-to-authenticate-downstream-device.md#x509-self-signed-authentication) [x.](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).  Tyto IoT Hub identity je možné místně ověřit pomocí centra IoT Edge, aby se připojení pořád mohla provádět v offline režimu.

Poznámky:

* IoT Edge moduly aktuálně podporují pouze ověřování symetrického klíče.
* MQTT klienti s pouze místními uživatelskými jmény a hesly nejsou přijímáni službou IoT Edge hub MQTT Broker, musí používat IoT Hub identity.

#### <a name="authorization"></a>Autorizace

Po ověření mají rozbočovač IoT Edge dva způsoby, jak autorizovat připojení klientů:

* Ověřením, že klient patří do své sady důvěryhodných klientů definovaných v IoT Hub. Sada důvěryhodných klientů je určena nastavením vztahů mezi nadřazenými a podřízenými objekty nebo zařízeními/moduly v IoT Hub. Když se v IoT Edge vytvoří modul, automaticky se vytvoří vztah důvěryhodnosti mezi tímto modulem a IoT Edgeým zařízením. Toto je jediný autorizační model podporovaný mechanismem zprostředkovatele směrování.

* Nastavením zásad autorizace. Tato zásada autorizace je dokument obsahující všechny autorizované identity klientů, které mají přístup k prostředkům v centru IoT Edge. Jedná se o primární autorizační model, který používá služba IoT Edge hub MQTT Broker, i když vztahy mezi nadřazenými a podřízenými objekty a zařízeními a moduly může taky služba MQTT Broker pro IoT Hub témata.

### <a name="remote-configuration"></a>Vzdálená konfigurace

Centrum IoT Edge je zcela řízené cloudem. Načte svou konfiguraci z IoT Hub prostřednictvím jejího [nevlákenního modulu](iot-edge-modules.md#module-twins). Obsahuje:

* Konfigurace tras
* Zásady autorizace
* Konfigurace mostu MQTT

Kromě toho je možné provést několik konfigurací nastavením [proměnných prostředí v centru IoT Edge](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>Běhová telemetrie kvality

IoT Edge shromažďuje anonymní telemetrii z modulu runtime hostitele a systémových modulů za účelem zlepšení kvality produktu. Tyto informace se nazývají běhová telemetrie kvality. Shromážděná telemetrie se pravidelně posílá jako zprávy ze zařízení do cloudu IoT Hub od agenta IoT Edge. Tyto zprávy se nezobrazují v běžné telemetrie zákazníka a nevyužívají žádnou kvótu zpráv.

Agent IoT Edge a centrum generují metriky, které můžete shromáždit pro pochopení výkonu zařízení. Podmnožina těchto metrik je shromažďována agentem IoT Edge jako součást telemetrie kvality modulu runtime. Metriky shromážděné pro telemetrii kvality běhu jsou označeny značkou `ms_telemetry` . Informace o všech dostupných metrikách najdete v tématu [přístup k vestavěným metrikám](how-to-access-built-in-metrics.md).

Před nahráním se odeberou jakékoli osobní nebo organizační informace, jako jsou názvy zařízení a modulů, a zajistěte tak anonymní povahu telemetrie kvality modulu runtime.

Agent IoT Edge shromáždí každou hodinu telemetrii a pošle jednu zprávu IoT Hub každých 24 hodin.

Pokud se chcete odhlásit z odesílání telemetrie za běhu z vašich zařízení, existují dva způsoby, jak to provést:

* Nastavte `SendRuntimeQualityTelemetry` proměnnou prostředí na `false` pro **edgeAgent** nebo
* Zrušte volbu v Azure Portal během nasazování.

## <a name="next-steps"></a>Další kroky

* [Vysvětlení modulů Azure IoT Edge](iot-edge-modules.md)
* [Nasazování modulů a vytváření tras ve službě IoT Edge](module-composition.md)
* [Naučte se publikovat a předplatit pomocí IoT Edge](how-to-publish-subscribe.md)
* [Další informace o IoT Edge metriky modulu runtime](how-to-access-built-in-metrics.md)
