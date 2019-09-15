---
title: Zjistěte, jak modul runtime spravuje zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jak modul runtime Azure IoT Edge spravuje moduly, zabezpečení, komunikace a vytváření sestav v zařízeních
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/06/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 677ff7ffab22eebdace67151d703ba83c2146602
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "70998611"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Pochopení runtime Azure IoT Edge a jeho architektura

Modul runtime IoT Edge je kolekce programů, které přepínají zařízení do IoT Edge zařízení. Souhrnně IoT Edge komponenty modulu runtime umožňují zařízením IoT Edge přijímat kód, který se má spustit na hraničních zařízeních, a sdělit výsledky. 

Modul runtime IoT Edge zajišťuje následující funkce na zařízeních IoT Edge:

* Instalace a aktualizace úloh na zařízení.
* Udržujte na zařízení standardy zabezpečení Azure IoT Edge.
* Ujistěte se, že [moduly IoT Edge](iot-edge-modules.md) jsou vždycky spuštěné.
* Oznamte stav modulu v cloudu pro vzdálené monitorování.
* Usnadňuje komunikaci mezi navazujícími a IoT Edge zařízeními.
* Usnadňuje komunikaci mezi moduly na zařízení IoT Edge.
* Usnadňuje komunikaci mezi IoT Edgem zařízením a cloudem.

![Modul runtime komunikuje přehledy a stav modulů pro službu IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Odpovědnosti modul runtime IoT Edge spadají do dvou kategorií: modul komunikaci a správu. Tyto dvě role provádí dvě komponenty, které jsou součástí modulu runtime IoT Edge. *Centrum IoT Edge* zodpovídá za komunikaci, zatímco *Agent IoT Edge* nasazuje a monitoruje moduly. 

IoT Edge centrum i agent IoT Edge jsou moduly, stejně jako jakýkoli jiný modul běžící na IoT Edgem zařízení. 

## <a name="iot-edge-hub"></a>Centrum IoT Edge

IoT Edge centrum je jedním ze dvou modulů, které tvoří modul runtime Azure IoT Edge. Funguje jako místní proxy server pro službu IoT Hub zveřejněním stejné koncové body protokolu jako služby IoT Hub. Taková konzistence znamená, že klienti (ať už zařízení nebo moduly) můžete připojit k modulu runtime IoT Edge, stejně jako do služby IoT Hub. 

>[!NOTE]
> Centrum IoT Edge podporuje klienty, kteří se připojují pomocí MQTT nebo AMQP. Klienti, kteří používají protokol HTTP nepodporuje. 

IoT Edge centrum není plná verze IoT Hub spuštěná místně. K dispozici je několik věcí, které IoT Edge centrum tiše deleguje k IoT Hub. Například Centrum IoT Edge předá žádosti o ověření do IoT Hub při prvním pokusu o připojení zařízení. Po navázání prvního připojení se informace o zabezpečení ukládají místně do mezipaměti IoT Edge hub. Další připojení z těchto zařízení jsou povolené a nemusí k ověření do cloudu. 

Aby se snížila šířka pásma, kterou používá IoT Edge řešení, Centrum IoT Edge optimalizuje, kolik skutečných připojení se do cloudu provedlo. Centrum IoT Edge přijímá logická připojení od klientů, jako jsou moduly nebo listová zařízení, a kombinuje je s jedním fyzickým připojením ke cloudu. Podrobnosti tohoto procesu je transparentní pro zbytek tohoto řešení. Klienti myslíte, že budou mít svoje vlastní připojení ke cloudu i v případě, že všechny se odešlou přes stejné připojení. 

![Centrum IoT Edge je brána mezi fyzickými zařízeními a IoT Hub](./media/iot-edge-runtime/Gateway.png)

Centrum IoT Edge může určit, jestli je připojené k IoT Hub. Pokud dojde ke ztrátě připojení, IoT Edge centrum ukládá zprávy nebo bude mít zdvojené aktualizace místně. Po připojení se obnoví, synchronizuje všechna data. Umístění používané pro tuto dočasnou mezipaměť je určeno vlastností vlákna modulu IoT Edgeového rozbočovače. Velikost mezipaměti není omezené a se zvýší, dokud zařízení má kapacitu úložiště. Další informace najdete v tématu [Možnosti offline](offline-capabilities.md).

### <a name="module-communication"></a>Komunikační modul

Centrum IoT Edge usnadňuje komunikaci s modulem. Použití centra IoT Edge jako zprostředkovatel zpráv uchovává moduly nezávislé na sobě. Moduly stačí zadat vstupy, na kterých přijetí zprávy a výstupy, ke kterým se zápis zpráv. Vývojář řešení může spojit tyto vstupy a výstupy, aby moduly zpracovával data v pořadí určeném pro toto řešení. 

![IoT Edge hub usnadňuje komunikaci mezi moduly a moduly.](./media/iot-edge-runtime/module-endpoints.png)

Chcete-li odesílat data do centra IoT Edge, modul volá metodu SendEventAsync. První argument určuje, na které výstupu pro odeslání zprávy. Následující pseudokódu odešle zprávu na **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Při příjmu zprávy, zaregistrujte zpětné volání, která zpracovává zprávy přicházející na specifický vstup. Následující pseudokódu zaregistruje funkci messageProcessor, která se má použít ke zpracování všech zpráv přijatých v **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Další informace o třídě ModuleClient a jejích metodách komunikace najdete v referenčních informacích k rozhraní API pro preferovaný jazyk sady SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)nebo [Node. js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Vývojář řešení zodpovídá za zadání pravidel, která určují, jak IoT Edge hub předává zprávy mezi moduly. Pravidla směrování se definují v cloudu a přepnula se na IoT Edge hub v zařízení. Podle stejné syntaxe pro službu IoT Hub trasy se používá k definování tras mezi moduly ve službě Azure IoT Edge. Další informace najdete v tématu [Naučte se nasazovat moduly a navázat trasy v IoT Edge](module-composition.md).   

![Trasy mezi moduly procházejí prostřednictvím centra IoT Edge.](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agenta IoT Edge

Agenta IoT Edge je další modul, který vytvoří modul runtime Azure IoT Edge. Zodpovídá za vytvoření instance moduly, zajištění, že nadále spouštět a hlásí stav modulů zpět do služby IoT Hub. Stejně jako u jakéhokoli jiného modulu používá agent IoT Edge k ukládání těchto konfiguračních dat jeho modul na vlákna. 

[Démon zabezpečení IoT Edge](iot-edge-security-manager.md) spustí agenta IoT Edge při spuštění zařízení. Agent načte její dvojče zařízení ze služby IoT Hub a zkontroluje, zda obsahuje manifest nasazení. Manifest nasazení je soubor JSON, který deklaruje moduly, které je potřeba spustit. 

Každá položka v manifestu nasazení obsahuje konkrétní informace o modulu, kterou používá agent IoT Edge k řízení životního cyklu modulu. Zde jsou některé další zajímavé vlastnosti: 

* **Settings. Image** – bitová kopie kontejneru, kterou agent IoT Edge používá ke spuštění modulu. Pokud je bitová kopie chráněná heslem, musí být agent IoT Edge nakonfigurovaný s přihlašovacími údaji pro Registry kontejneru. Přihlašovací údaje pro registr kontejneru je možné nakonfigurovat vzdáleně pomocí manifestu nasazení nebo na zařízení IoT Edge samotném aktualizací `config.yaml` souboru ve složce IoT Edge programu.
* **Settings. CreateOptions** – řetězec, který je předán přímo procesu démona kontejneru Moby při spuštění kontejneru modulu. Přidání možností v této vlastnosti umožňuje pokročilé konfigurace, jako je přesměrování portu nebo připojování svazků do kontejneru modulu.  
* **stav** – stav, ve kterém agent IoT Edge umístí modul. Tato hodnota je obvykle nastavená tak, aby se *spouštěla* co nejvíce lidí, aby agent IoT Edge hned na zařízení spouštěl všechny moduly. Můžete ale zadat počáteční stav modulu, který se má zastavit, a počkat na budoucí čas, aby se agentovi IoT Edge mohl spustit modul. Agent IoT Edge hlásí stav každého modulu zpátky do cloudu v hlášených vlastnostech. Rozdíl mezi požadované vlastnosti a ohlášených vlastností je indikátorem identifikovala zařízení. Podporované stavy jsou:
   * Stahuje se
   * Spuštěno
   * Není v pořádku
   * Selhalo
   * Zastaveno
* **restartPolicy** – způsob, jakým agent IoT Edge restartuje modul. Možné hodnoty:
   * `never`– Agent IoT Edge nikdy nerestartuje modul.
   * `on-failure`– Pokud modul selže, agent IoT Edge ho restartuje. Pokud se modul vypíná čistě, agent IoT Edge ho nerestartuje.
   * `on-unhealthy`– Pokud modul selže nebo je považován za špatný, agent IoT Edge ho restartuje.
   * `always`– Pokud modul selže, je považován za špatný nebo je vypnut jakýmkoli způsobem, agent IoT Edge jej restartuje. 

IoT Edge agent odešle odpověď modulu runtime pro službu IoT Hub. Tady je seznam možných odpovědi:
  * 200 – OK
  * 400 - konfigurace nasazení je chybný nebo není platný.
  * 417 – zařízení nemá nastavenou konfiguraci nasazení.
  * 412 – verze schématu v konfiguraci nasazení je neplatná.
  * 406 – IoT Edge zařízení je offline nebo neodesílá zprávy o stavu.
  * 500 – při IoT Edge modulu runtime došlo k chybě.

Další informace najdete v tématu [Naučte se nasazovat moduly a navázat trasy v IoT Edge](module-composition.md).   

### <a name="security"></a>Zabezpečení

Agenta IoT Edge hraje důležitou roli v zabezpečení zařízení IoT Edge. Například provede akce, jako je ověření imagi modulu před jeho zahájením. 

Další informace o rozhraní Azure IoT Edge Security Framework najdete v článku o [IoT Edge Security Manager](iot-edge-security-manager.md).

## <a name="next-steps"></a>Další postup

[Vysvětlení modulů Azure IoT Edge](iot-edge-modules.md)
