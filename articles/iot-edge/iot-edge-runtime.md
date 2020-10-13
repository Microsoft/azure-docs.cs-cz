---
title: Přečtěte si, jak modul runtime spravuje zařízení – Azure IoT Edge | Microsoft Docs
description: Přečtěte si, jak IoT Edge runtime spravuje moduly, zabezpečení, komunikaci a vytváření sestav na vašich zařízeních.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/08/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 8cbfc374a5964983c43594fef5d97986e51c0d83
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91971689"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Pochopení Azure IoT Edge runtime a jeho architektury

Modul runtime IoT Edge je kolekce programů, které ze zařízení udělají zařízení IoT Edge. Souhrnně IoT Edge komponenty modulu runtime umožňují zařízením IoT Edge přijímat kód, který se má spustit na hranici a sdělit výsledky.

IoT Edge runtime zodpovídá za následující funkce na IoT Edge zařízeních:

* Instalace a aktualizace úloh na zařízení.
* Udržujte na zařízení standardy zabezpečení Azure IoT Edge.
* Ujistěte se, že [moduly IoT Edge](iot-edge-modules.md) jsou vždycky spuštěné.
* Oznamte stav modulu v cloudu pro vzdálené monitorování.
* Spravujte komunikaci mezi zařízeními pro příjem dat a IoT Edgemi zařízeními.
* Spravujte komunikaci mezi moduly na zařízení IoT Edge.
* Spravujte komunikaci mezi IoT Edgem zařízením a cloudem.

![Modul runtime komunikuje s přehledy a stavem modulu IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Odpovědnosti modulu runtime IoT Edge spadají do dvou kategorií: komunikace a Správa modulů. Tyto dvě role provádí dvě komponenty, které jsou součástí modulu runtime IoT Edge.*Centrum IoT Edge* zodpovídá za komunikaci, zatímco *Agent IoT Edge* nasazuje a monitoruje moduly.

IoT Edge centrum i agent IoT Edge jsou moduly, stejně jako jakýkoli jiný modul běžící na IoT Edgem zařízení. Někdy se označují jako *běhové moduly*.

## <a name="iot-edge-hub"></a>Centrum IoT Edge

IoT Edge centrum je jedním ze dvou modulů, které tvoří modul runtime Azure IoT Edge. Funguje jako místní proxy server pro IoT Hub tím, že zveřejňuje stejné koncové body protokolu jako IoT Hub. Tato konzistence znamená, že se klienti (zařízení nebo moduly) mohou připojit k modulu IoT Edge runtime stejným způsobem jako IoT Hub.

>[!NOTE]
> Centrum IoT Edge podporuje klienty, kteří se připojují pomocí MQTT nebo AMQP. Nepodporuje klienty, kteří používají protokol HTTP.

Centrum IoT Edge není plná verze IoT Hub spuštěná místně. Centrum IoT Edge tiše deleguje některé úkoly, které je potřeba IoT Hub. Například Centrum IoT Edge předá žádosti o ověření do IoT Hub při prvním pokusu o připojení zařízení. Po navázání prvního připojení se informace o zabezpečení ukládají místně do mezipaměti IoT Edge hub. Budoucí připojení z tohoto zařízení jsou povolená, aniž byste museli znovu ověřovat Cloud.

Aby se snížila šířka pásma, kterou používá vaše IoT Edge řešení, Centrum IoT Edge optimalizuje, kolik skutečných připojení se do cloudu provedlo. Centrum IoT Edge přijímá logická připojení z modulů nebo podřízených zařízení a kombinuje je s jedním fyzickým připojením ke cloudu. Podrobnosti tohoto procesu jsou pro zbytek řešení transparentní. Klienti si budou myslet, že mají vlastní připojení ke cloudu, i když se všechny odesílají přes stejné připojení.

![Centrum IoT Edge je brána mezi fyzickými zařízeními a IoT Hub](./media/iot-edge-runtime/Gateway.png)

Centrum IoT Edge může určit, jestli je připojené k IoT Hub. Pokud dojde ke ztrátě připojení, IoT Edge centrum ukládá zprávy nebo bude mít zdvojené aktualizace místně. Jakmile je připojení znovu navázáno, synchronizuje všechna data. Umístění používané pro tuto dočasnou mezipaměť je určeno vlastností vlákna modulu IoT Edgeového rozbočovače. Velikost mezipaměti není omezené a bude růst, dokud má zařízení kapacitu úložiště.Další informace najdete v tématu [Možnosti offline](offline-capabilities.md).

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

Další informace o třídě ModuleClient a jejích metodách komunikace najdete v referenčních informacích k rozhraní API pro preferovaný jazyk sady SDK: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)nebo [Node.js](/javascript/api/azure-iot-device/moduleclient).

Vývojář řešení zodpovídá za zadání pravidel, která určují, jak IoT Edge hub předává zprávy mezi moduly. Pravidla směrování jsou definovaná v cloudu a jsou vložená dolů do IoT Edgeho centra v jeho modulu. Stejná syntaxe pro IoT Hub trasy se používá k definování tras mezi moduly v Azure IoT Edge. Další informace najdete v tématu [Naučte se nasazovat moduly a navázat trasy v IoT Edge](module-composition.md).

![Trasy mezi moduly procházejí prostřednictvím centra IoT Edge.](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agent IoT Edge

Agent IoT Edge je druhý modul, který tvoří modul runtime Azure IoT Edge. Zodpovídá za vytváření instancí modulů, zajišťuje, že budou nadále běžet, a oznamuje stav modulů zpět do IoT Hub. Tato konfigurační data jsou zapsána jako vlastnost vlákna modulu IoT Edgeho agenta.

[Démon zabezpečení IoT Edge](iot-edge-security-manager.md) spustí agenta IoT Edge při spuštění zařízení. Agent z IoT Hub načte svůj modul s nevlákenou a zkontroluje manifest nasazení. Manifest nasazení je soubor JSON, který deklaruje moduly, které musí být spuštěny.

Každá položka v manifestu nasazení obsahuje konkrétní informace o modulu, kterou používá agent IoT Edge k řízení životního cyklu modulu. Mezi zajímavé vlastnosti patří:

* **Settings. Image** – bitová kopie kontejneru, kterou agent IoT Edge používá ke spuštění modulu. Pokud je bitová kopie chráněná heslem, musí být agent IoT Edge nakonfigurovaný s přihlašovacími údaji pro Registry kontejneru. Přihlašovací údaje pro registr kontejneru je možné nakonfigurovat vzdáleně pomocí manifestu nasazení nebo na zařízení IoT Edge samotném aktualizací `config.yaml` souboru ve složce IoT Edge programu.
* **Settings. CreateOptions** – řetězec, který je předán přímo procesu démona kontejneru Moby při spuštění kontejneru modulu. Přidání možností v této vlastnosti umožňuje pokročilé konfigurace, jako je přesměrování portu nebo připojování svazků do kontejneru modulu.  
* **stav** – stav, ve kterém agent IoT Edge umístí modul. Tato hodnota je obvykle nastavená tak, aby se *spouštěla* co nejvíce lidí, aby agent IoT Edge hned na zařízení spouštěl všechny moduly. Můžete ale zadat počáteční stav modulu, který se má zastavit, a počkat na budoucí čas, aby se agentovi IoT Edge mohl spustit modul.Agent IoT Edge hlásí stav každého modulu zpátky do cloudu v hlášených vlastnostech. Rozdíl mezi požadovanou vlastností a nahlášenou vlastností je indikátorem nesprávného zařízení. Podporované stavy jsou:

  * Stahování
  * Spuštěno
  * Není v pořádku
  * Neúspěšný
  * Zastaveno

* **restartPolicy** – způsob, jakým agent IoT Edge restartuje modul. Mezi možné hodnoty patří:
  
  * `never` – Agent IoT Edge nikdy nerestartuje modul.
  * `on-failure` – Pokud modul selže, agent IoT Edge ho restartuje. Pokud se modul vypíná čistě, agent IoT Edge ho nerestartuje.
  * `on-unhealthy` – Pokud modul selže nebo je považován za špatný, agent IoT Edge ho restartuje.
  * `always` – Pokud modul selže, je považován za špatný nebo je vypnut jakýmkoli způsobem, agent IoT Edge jej restartuje.

* **imagePullPolicy** – určuje, zda se agent IoT Edge pokusí stáhnout nejnovější obrázek pro modul automaticky nebo ne. Pokud nezadáte hodnotu, výchozí hodnota je Create ( *vytvořit*). Mezi možné hodnoty patří:

  * `on-create` – Když spouštíte modul nebo aktualizujete modul založený na novém manifestu nasazení, agent IoT Edge se pokusí načíst image modulu z registru kontejneru.
  * `never` – Agent IoT Edge se nikdy nebude pokoušet načíst image modulu z registru kontejneru. S touto konfigurací zodpovídáte za získání image modulu na zařízení a správu všech aktualizací imagí.

Agent IoT Edge odesílá odezvu modulu runtime do IoT Hub. Tady je seznam možných odpovědí:
  
* 200 – OK
* 400 – konfigurace nasazení je poškozená nebo neplatná.
* 417 – zařízení nemá nastavenou konfiguraci nasazení.
* 412 – verze schématu v konfiguraci nasazení není platná.
* 406 – IoT Edge zařízení je offline nebo neodesílá zprávy o stavu.
* 500 – při IoT Edge modulu runtime došlo k chybě.

Další informace najdete v tématu [Naučte se nasazovat moduly a navázat trasy v IoT Edge](module-composition.md).

### <a name="security"></a>Zabezpečení

Agent IoT Edge hraje důležitou roli v zabezpečení zařízení IoT Edge. Například provede akce, jako je ověření obrázku modulu před jeho spuštěním.

Další informace o rozhraní Azure IoT Edge Security Framework najdete v článku o [IoT Edge Security Manager](iot-edge-security-manager.md).

## <a name="runtime-quality-telemetry"></a>Běhová telemetrie kvality

IoT Edge shromažďuje z modulu runtime hostitele a systémových modulů anonymní telemetrii, aby se zlepšila kvalita produktu. Tyto informace se nazývají běhová telemetrie kvality (RQT). RQT se pravidelně posílá jako zprávy ze zařízení do cloudu, aby je IoT Hub od agenta IoT Edge. Zprávy RQT se nezobrazují v běžné telemetrie zákazníka a nevyužívají žádnou kvótu zpráv.

Úplný seznam metrik shromažďovaných nástrojem edgeAgent a edgeHub je k dispozici v [části dostupné metriky v článku věnovaném metrikám přístupu IoT Edge runtime](how-to-access-built-in-metrics.md#available-metrics). Podmnožina těchto metrik je shromažďována agentem IoT Edge jako součást RQT. Metriky shromážděné jako součást RQT obsahují značku `ms_telemetry` .

V rámci anonymity se před odesláním odeberou jakékoli osobní nebo organizační informace, jako jsou názvy zařízení a modulů.

Výchozí četnost RQT je jedna zpráva odeslaná do IoT Hub každých 24 hodin a místní kolekce podle edgeAgent každou hodinu.

Pokud se chcete odhlásit z RQT, existují dva způsoby, jak to provést:

* Nastavte `SendRuntimeQualityTelemetry` proměnnou prostředí na `false` pro **edgeAgent**nebo
* Zrušte volbu v Azure Portal během nasazování.

## <a name="next-steps"></a>Další kroky

* [Vysvětlení modulů Azure IoT Edge](iot-edge-modules.md)
* [Další informace o IoT Edge metriky modulu runtime](how-to-access-built-in-metrics.md)
