---
title: Zjistěte, jak runtime spravuje zařízení – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Zjistěte, jak moduly, zabezpečení, komunikaci a vytváření sestav na vašich zařízeních spravuje moduly, zabezpečení, komunikaci a vytváření sestav
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c184972789c412406f264f725f8b94e1f7f162ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284899"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Principy runtime Azure IoT Edge a jeho architektury

Modul runtime IoT Edge je kolekce programů, které ze zařízení udělají zařízení IoT Edge. Komponenty runtime IoT Edge společně umožňují zařízením IoT Edge přijímat kód ke spuštění na hraničních zařízeních a sdělování výsledků.

Runtime IoT Edge je zodpovědný za následující funkce na zařízeních IoT Edge:

* Nainstalujte a aktualizujte úlohy v zařízení.
* Udržujte na zařízení standardy zabezpečení Azure IoT Edge.
* Ujistěte se, že [moduly IoT Edge](iot-edge-modules.md) jsou vždy spuštěné.
* Oznamte stav modulu do cloudu pro vzdálené monitorování.
* Spravujte komunikaci mezi navazujícími zařízeními a zařízeními IoT Edge.
* Spravujte komunikaci mezi moduly na zařízení IoT Edge.
* Spravujte komunikaci mezi zařízením IoT Edge a cloudem.

![Runtime komunikuje přehledy a stav modulů do služby IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Odpovědnost za runtime IoT Edge spadá do dvou kategorií: komunikace a správa modulů. Tyto dvě role jsou prováděny dvěma součástmi, které jsou součástí běhu IoT Edge.Centrum *IoT Edge* je zodpovědné za komunikaci, zatímco *agent IoT Edge* nasazuje a monitoruje moduly.

Rozbočovač IoT Edge i agent IoT Edge jsou moduly, stejně jako jakýkoli jiný modul spuštěný na zařízení IoT Edge. Někdy se označují jako *moduly runtime*.

## <a name="iot-edge-hub"></a>Centrum IoT Edge

Centrum IoT Edge je jeden ze dvou modulů, které tvoří modul runtime Azure IoT Edge. Funguje jako místní proxy server pro službu IoT Hub tím, že vystavuje stejné koncové body protokolu jako službu IoT Hub. Tato konzistence znamená, že klienti (ať už zařízení nebo moduly) se mohou připojit k modulu runtime IoT Edge stejně jako k ioT hubu.

>[!NOTE]
> Centrum IoT Edge podporuje klienty, kteří se připojují pomocí MQTT nebo AMQP. Nepodporuje klienty, kteří používají protokol HTTP.

Centrum IoT Edge není plná verze iot hubu spuštěná místně. Centrum IoT Edge tiše deleguje některé úkoly do služby IoT Hub. Například centrum IoT Edge předává požadavky na ověření do služby IoT Hub, když se zařízení poprvé pokusí připojit. Po navázání prvního připojení jsou informace o zabezpečení ukládány místně pomocí centra IoT Edge. Budoucí připojení z tohoto zařízení jsou povoleny bez nutnosti ověření do cloudu znovu.

Chcete-li snížit šířku pásma, kterou vaše řešení IoT Edge používá, centrum IoT Edge optimalizuje, kolik skutečných připojení se provádí do cloudu. Služba IoT Edge hub přebírá logická připojení z modulů nebo podřizovaných zařízení a kombinuje je pro jedno fyzické připojení ke cloudu. Podrobnosti tohoto procesu jsou transparentní pro zbytek řešení. Klienti si myslí, že mají své vlastní připojení ke cloudu, i když jsou všechny odesílány přes stejné připojení.

![Centrum IoT Edge je brána mezi fyzickými zařízeními a službou IoT Hub](./media/iot-edge-runtime/Gateway.png)

Centrum IoT Edge může určit, jestli je připojené k IoT Hubu. Pokud dojde ke ztrátě připojení, služba IoT Edge hub ukládá zprávy nebo aktualizace dvojčat místně. Jakmile je připojení obnoveno, synchronizuje všechna data. Umístění používané pro tuto dočasnou mezipaměť je určeno vlastností dvojčete modulu centra IoT Edge. Velikost mezipaměti není omezena a bude růst tak dlouho, dokud má zařízení kapacitu úložiště.Další informace naleznete v tématu [Offline capabilities](offline-capabilities.md).

### <a name="module-communication"></a>Modulová komunikace

Rozbočovač IoT Edge usnadňuje komunikaci modulů s modulem. Použití centra IoT Edge jako zprostředkovatele zpráv udržuje moduly nezávislé na sobě navzájem. Moduly stačí zadat vstupy, na kterých přijímají zprávy a výstupy, na které jsou zápis zpráv. Vývojář řešení může tyto vstupy a výstupy sešít dohromady tak, aby moduly zpracovávají data v pořadí specifickém pro dané řešení.

![IoT Edge Hub usnadňuje komunikaci mezi moduly](./media/iot-edge-runtime/module-endpoints.png)

Chcete-li odeslat data do centra IoT Edge, modul volá metodu SendEventAsync. První argument určuje, na kterém výstupu chcete zprávu odeslat. Následující pseudokód odešle zprávu na **výstup1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Chcete-li obdržet zprávu, zaregistrujte zpětné volání, které zpracovává zprávy přicházející na konkrétní vstup. Následující pseudokód registruje funkci messageProcessor, který má být použit pro zpracování všech zpráv přijatých na **input1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Další informace o třídě ModuleClient a jejích komunikačních metodách naleznete v odkazu rozhraní API pro upřednostňovaný jazyk sady SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)nebo [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Vývojář řešení je zodpovědný za určení pravidel, která určují, jak centrum IoT Edge předává zprávy mezi moduly. Pravidla směrování jsou definována v cloudu a posunuta do centra IoT Edge v jeho dvojčeti modulu. Stejná syntaxe pro trasy služby IoT Hub se používá k definování tras mezi moduly v Azure IoT Edge. Další informace najdete v [tématu Naučte se nasazovat moduly a navazovat trasy v IoT Edge](module-composition.md).

![Trasy mezi moduly procházejí centrem IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agent IoT Edge

Agent IoT Edge je další modul, který tvoří modul runtime Azure IoT Edge. Je zodpovědný za vytváření instancí modulů, zajištění, že budou i nadále spuštěny, a oznamuje stav modulů zpět do ioT hubu. Tato konfigurační data se zapisují jako vlastnost dvojčete modulu agenta IoT Edge.

[Dém zabezpečení IoT Edge](iot-edge-security-manager.md) spustí agenta IoT Edge při spuštění zařízení. Agent načte dvojče modulu ze služby IoT Hub a zkontroluje manifest nasazení. Manifest nasazení je soubor JSON, který deklaruje moduly, které je třeba spustit.

Každá položka v manifestu nasazení obsahuje konkrétní informace o modulu a používá agent IoT Edge pro řízení životního cyklu modulu. Některé z nejzajímavějších vlastností jsou:

* **settings.image** – image kontejneru, který agent IoT Edge používá ke spuštění modulu. Agent IoT Edge musí být nakonfigurován s pověřeními pro registr kontejneru, pokud je bitová kopie chráněna heslem. Pověření pro registr kontejneru lze nakonfigurovat vzdáleně pomocí manifestu nasazení nebo na `config.yaml` samotném zařízení IoT Edge aktualizací souboru ve složce programu IoT Edge.
* **settings.createOptions** – řetězec, který je předán přímo daemonu kontejneru Moby při spuštění kontejneru modulu. Přidání možností v této vlastnosti umožňuje pokročilé konfigurace, jako je předávání portů nebo připojení svazků do kontejneru modulu.  
* **stav** – stav, ve kterém agent IoT Edge umístí modul. Obvykle je tato hodnota nastavena na *spuštění,* protože většina lidí chce, aby agent IoT Edge okamžitě spustil všechny moduly v zařízení. Můžete však zadat počáteční stav modulu, který má být zastaven, a čekat na budoucí čas, abyste agentovi IoT Edge řekli spuštění modulu.Agent IoT Edge hlásí stav každého modulu zpět do cloudu v ohlášené vlastnosti. Rozdíl mezi požadovanou vlastností a ohlášenou vlastností je indikátorem nesprávného chování zařízení. Podporované stavy jsou:

  * Stahování
  * Spuštěno
  * Není v pořádku
  * Failed
  * Zastaveno

* **restartPolicy** – Jak agent IoT Edge restartuje modul. Možné hodnoty zahrnují:
  
  * `never`– Agent IoT Edge nikdy nerestartuje modul.
  * `on-failure`- Pokud dojde k chybě modulu, agent IoT Edge jej restartuje. Pokud se modul vypne čistě, agent IoT Edge ho nerestartuje.
  * `on-unhealthy`- Pokud dojde k chybě modulu nebo je považován za nefunkční, agent IoT Edge jej restartuje.
  * `always`- Pokud dojde k chybě modulu, je považován za nefunkční nebo se vypne v žádném případě, agent IoT Edge restartuje jej.

* **imagePullPolicy** – zda se agent IoT Edge pokusí automaticky vytáhnout nejnovější obrázek pro modul. Pokud nezadáte hodnotu, výchozí hodnota je *naCreate*. Možné hodnoty zahrnují:

  * `on-create`- Při spuštění modulu nebo aktualizaci modulu na základě nového manifestu nasazení se agent IoT Edge pokusí vytáhnout bitovou kopii modulu z registru kontejneru.
  * `never`- Agent IoT Edge se nikdy nepokusí vytáhnout bitovou kopii modulu z registru kontejneru. S touto konfigurací pak jste zodpovědní za získání image modulu do zařízení a správu všech aktualizací bitových obrázků.

Agent IoT Edge odesílá odezvu za běhu do služby IoT Hub. Zde je seznam možných odpovědí:
  
* 200 - OK
* 400 - Konfigurace nasazení je poškozená nebo neplatná.
* 417 – zařízení nemá nastavenou konfiguraci nasazení.
* 412 – Verze schématu v konfiguraci nasazení je neplatná.
* 406 – Zařízení IoT Edge je offline nebo neodesílá zprávy o stavu.
* 500 – došlo k chybě v době běhu IoT Edge.

Další informace najdete v [tématu Naučte se nasazovat moduly a navazovat trasy v IoT Edge](module-composition.md).

### <a name="security"></a>Zabezpečení

Agent IoT Edge hraje klíčovou roli v zabezpečení zařízení IoT Edge. Například provádí akce, jako je ověření image modulu před jeho spuštěním.

Další informace o rozhraní zabezpečení Azure IoT Edge načtete o [správci zabezpečení IoT Edge](iot-edge-security-manager.md).

## <a name="next-steps"></a>Další kroky

[Vysvětlení modulů Azure IoT Edge](iot-edge-modules.md)
