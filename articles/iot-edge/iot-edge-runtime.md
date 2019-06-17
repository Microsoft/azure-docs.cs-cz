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
ms.openlocfilehash: 423825540c02d2788de7a6148ddcec3c654fd450
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66754789"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Pochopení runtime Azure IoT Edge a jeho architektura

Modul runtime IoT Edge je kolekce programy, které zapnutí zařízení do zařízení IoT Edge. Dále souhrnně nazývané součásti modulu runtime IoT Edge povolit zařízení přijímat kód pro spuštění na hraničních zařízeních IoT Edge a komunikovat výsledky. 

Modul runtime IoT Edge zajišťuje následující funkce na zařízeních IoT Edge:

* Instalace a aktualizace úlohy na zařízení.
* Udržujte standardy zabezpečení Azure IoT Edge na zařízení.
* Ujistěte se, že [moduly IoT Edge](iot-edge-modules.md) nepřetržitý provoz.
* Sestava Stav modulů do cloudu pro vzdálené monitorování.
* Usnadnění komunikace mezi podřízenými zařízeními typu list a zařízení IoT Edge.
* Usnadnění komunikace mezi moduly v zařízení IoT Edge.
* Usnadnění komunikace mezi hraničním zařízením IoT a cloudem.

![Modul runtime komunikuje přehledy a stav modulů pro službu IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Odpovědnosti modul runtime IoT Edge spadají do dvou kategorií: modul komunikaci a správu. Tyto dvě role provádí dvě komponenty, které jsou součástí modulu runtime IoT Edge. *Centrum IoT Edge* zodpovídá za komunikaci, zatímco *agenta IoT Edge* implementuje a monitoruje moduly. 

Centrum IoT Edge a IoT Edge agenta jsou moduly, stejně jako ostatní moduly běžící na zařízení IoT Edge. 

## <a name="iot-edge-hub"></a>Centrum IoT Edge

Centrum IoT Edge je jedním ze dvou modulů, které tvoří modul runtime Azure IoT Edge. Funguje jako místní proxy server pro službu IoT Hub zveřejněním stejné koncové body protokolu jako služby IoT Hub. Taková konzistence znamená, že klienti (ať už zařízení nebo moduly) můžete připojit k modulu runtime IoT Edge, stejně jako do služby IoT Hub. 

>[!NOTE]
> Centrum IoT Edge podporuje klienty, kteří se připojují pomocí protokolu MQTT nebo AMQP. Klienti, kteří používají protokol HTTP nepodporuje. 

Centrum IoT Edge není s plnou verzí služby IoT Hub spuštěná místně. Existuje několik věcí, které Centrum IoT Edge tiše deleguje se do služby IoT Hub. Například Centrum IoT Edge předává požadavky na ověření do služby IoT Hub, když se zařízení poprvé pokusí připojit. Po prvním připojení se informace o zabezpečení jsou místně uložené ve službě IoT Edge hub. Další připojení z těchto zařízení jsou povolené a nemusí k ověření do cloudu. 

Používá ke snížení šířky pásma vašeho řešení IoT Edge a Centrum IoT Edge optimalizuje tak počet skutečných připojení probíhají do cloudu. Centrum IoT Edge přebírá logickou připojení od klientů, jako jsou moduly nebo zařízení typu list a kombinuje je pro jedno fyzické připojení ke cloudu. Podrobnosti tohoto procesu je transparentní pro zbytek tohoto řešení. Klienti myslíte, že budou mít svoje vlastní připojení ke cloudu i v případě, že všechny se odešlou přes stejné připojení. 

![Centrum IoT Edge je brána mezi fyzickým zařízením a centrem IoT](./media/iot-edge-runtime/Gateway.png)

Centrum IoT Edge můžete určit, jestli je připojený ke službě IoT Hub. Pokud dojde ke ztrátě připojení, Centrum IoT Edge uloží zprávy nebo aktualizace dvojčete místně. Po připojení se obnoví, synchronizuje všechna data. Umístění použité pro tato dočasná mezipaměť se určuje podle vlastnosti tohoto dvojčete modulu IoT Edge hub. Velikost mezipaměti není omezené a se zvýší, dokud zařízení má kapacitu úložiště. Další informace najdete v tématu [Offline možnosti](offline-capabilities.md).

### <a name="module-communication"></a>Komunikační modul

Centrum IoT Edge umožňuje komunikaci modulu do modulu. Pomocí IoT Edge hub jako zprostředkovatel zpráv uchovává moduly nezávisle na sobě navzájem. Moduly stačí zadat vstupy, na kterých přijetí zprávy a výstupy, ke kterým se zápis zpráv. Pro vývojáře řešení můžete spojit tyto vstupy a výstupy dohromady tak, aby moduly zpracovávat data v pořadí, které jsou specifické pro příslušné řešení. 

![Centrum IoT Edge umožňuje komunikaci modulu modulu](./media/iot-edge-runtime/module-endpoints.png)

K odesílání dat do centra IoT Edge, volá modul SendEventAsync metodu. První argument určuje, na které výstupu pro odeslání zprávy. Následujícím pseudokódu odešle zprávu **output1**:

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Při příjmu zprávy, zaregistrujte zpětné volání, která zpracovává zprávy přicházející na specifický vstup. Zaregistruje messageProcessor funkce pro zpracování všechny zprávy přijaté v následujícím pseudokódu **vstup1**:

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Další informace o třídě ModuleClient a jeho metody komunikace najdete v referenci rozhraní API pro váš preferovaný jazyk sady SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C a Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), nebo [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Řešení pro vývojáře je zodpovědná za určení pravidel, které určují, jak předává zprávy mezi moduly IoT Edge hub. Pravidla směrování jsou definovány v cloudu a přesunout dolů k centru IoT Edge na jeho dvojče zařízení. Podle stejné syntaxe pro službu IoT Hub trasy se používá k definování tras mezi moduly ve službě Azure IoT Edge. Další informace najdete v tématu [zjistěte, jak nasadit moduly a vytvářet ve službě IoT Edge](module-composition.md).   

![Trasy mezi moduly projít Centrum IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agenta IoT Edge

Agenta IoT Edge je další modul, který vytvoří modul runtime Azure IoT Edge. Zodpovídá za vytvoření instance moduly, zajištění, že nadále spouštět a hlásí stav modulů zpět do služby IoT Hub. Stejně jako ostatní moduly IoT Edge agent používá jeho dvojče zařízení pro ukládání těchto dat konfigurace. 

[Démon zabezpečení IoT Edge](iot-edge-security-manager.md) při spuštění zařízení spustí agenta IoT Edge. Agent načte její dvojče zařízení ze služby IoT Hub a zkontroluje, zda obsahuje manifest nasazení. Manifest nasazení je soubor JSON, který deklaruje moduly, které je potřeba spustit. 

Každá položka v manifestu nasazení obsahuje konkrétní informace o modulu a používá agenta IoT Edge pro řízení životního cyklu modulu. Zde jsou některé další zajímavé vlastnosti: 

* **Settings.Image** – image kontejneru, který používá agent IoT Edge spuštění modulu. Agenta IoT Edge musí být nakonfigurované pomocí přihlašovacích údajů pro službu container registry, pokud na obrázku je chráněn heslem. Přihlašovací údaje pro registr kontejneru dá vzdáleně pomocí manifest nasazení, nebo na samotném zařízení IoT Edge prostřednictvím aktualizace `config.yaml` souboru ve složce program IoT Edge.
* **settings.createOptions** – řetězec, který je předán přímo do kontejneru démon Moby při spouštění modulu kontejneru. Přidání možnosti v této vlastnosti umožňuje pro pokročilé konfigurace, jako je port předávání nebo připojení svazků do kontejneru modulu.  
* **Stav** – stavu, ve kterém agenta IoT Edge umístí modul. Obvykle je tato hodnota nastavena *systémem* jako většina lidí má agent IoT Edge k okamžitému spuštění všech modulů na zařízení. Můžete však zadat počáteční stav modulu můžete zastavit a počkat na budoucí dobu říct IoT Edge agenta spusťte modul. IoT Edge agent zasílá vytvářené sestavy stav každého modulu, zpět do cloudu v ohlášené vlastnosti. Rozdíl mezi požadované vlastnosti a ohlášených vlastností je indikátorem identifikovala zařízení. Podporované stavy jsou:
   * Stahuje se
   * Spuštěno
   * Není v pořádku
   * Selhalo
   * Zastaveno
* **restartPolicy** – způsob, jakým modul restartování agenta IoT Edge. Možné hodnoty:
   * `never` – Agenta IoT Edge nikdy nerestartuje modulu.
   * `on-failure` – Pokud dojde k chybě modulu, agenta IoT Edge se restartuje. Modul vypnutí čistě agenta IoT Edge se nerestartuje.
   * `on-unhealthy` – Pokud dojde k chybě modulu nebo se považoval za poškozený, IoT Edge agenta restartuje ho.
   * `always` – Pokud modul dojde k chybě, se považoval za poškozený nebo ukončení žádným způsobem, agenta IoT Edge se restartuje. 

IoT Edge agent odešle odpověď modulu runtime pro službu IoT Hub. Tady je seznam možných odpovědi:
  * 200 – OK
  * 400 - konfigurace nasazení je chybný nebo není platný.
  * 417 – zařízení nemá nastavení konfigurace nasazení.
  * 412 – verze schématu v konfiguraci nasazení je neplatná.
  * 406 – zařízení IoT Edge je offline nebo neposílá stav sestavy.
  * 500 - došlo k chybě v modulu runtime IoT Edge.

Další informace najdete v tématu [zjistěte, jak nasadit moduly a vytvářet ve službě IoT Edge](module-composition.md).   

### <a name="security"></a>Zabezpečení

Agenta IoT Edge hraje důležitou roli v zabezpečení zařízení IoT Edge. Například provede akce, jako je ověření imagi modulu před jeho zahájením. 

Další informace o rozhraní zabezpečení Azure IoT Edge, přečtěte si informace o [správce zabezpečení IoT Edge](iot-edge-security-manager.md).

## <a name="next-steps"></a>Další postup

[Vysvětlení modulů Azure IoT Edge](iot-edge-modules.md)
