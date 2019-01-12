---
title: Zjistěte, jak modul runtime spravuje zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Zjistěte, jak modul runtime Azure IoT Edge spravuje moduly, zabezpečení, komunikace a vytváření sestav v zařízeních
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/13/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a2412a286015cb403fe9a2af7754c7e5346fe98c
ms.sourcegitcommit: a512360b601ce3d6f0e842a146d37890381893fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54230420"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Pochopení runtime Azure IoT Edge a jeho architektura

Modul runtime IoT Edge je kolekce programy, které je potřeba nainstalovat na zařízení, aby se považovat za zařízení IoT Edge. Dále souhrnně nazývané součásti modulu runtime IoT Edge povolit zařízení přijímat kód pro spuštění na hraničních zařízeních IoT Edge a komunikovat výsledky. 

Modul runtime IoT Edge zajišťuje následující funkce na zařízeních IoT Edge:

* Instaluje a aktualizuje na zařízení úlohy.
* Udržuje na zařízení standardy zabezpečení Azure IoT Edge.
* Zajišťuje, že [moduly IoT Edge](iot-edge-modules.md) nepřetržitý provoz.
* Hlásí do cloudu stav modulů pro účely vzdáleného monitorování.
* Usnadňuje komunikaci mezi podřízenými zařízeními typu list a zařízení IoT Edge.
* Usnadňuje komunikaci mezi moduly v příslušném hraničním zařízení IoT.
* Usnadňuje komunikaci mezi příslušným hraničním zařízením IoT a cloudem.

![Modul runtime komunikuje přehledy a stav modulů pro službu IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Odpovědnosti modul runtime IoT Edge spadají do dvou kategorií: modul komunikaci a správu. Tyto dvě role provádí dvě komponenty, které tvoří modul runtime IoT Edge. Centrum IoT Edge je zodpovědná za komunikaci, zatímco agenta IoT Edge spravuje nasazení a monitorování modulů. 

Centrum IoT Edge a IoT Edge agenta jsou moduly, stejně jako ostatní moduly běžící na zařízení IoT Edge. 

## <a name="iot-edge-hub"></a>Centrum IoT Edge

Centrum IoT Edge je jedním ze dvou modulů, které tvoří modul runtime Azure IoT Edge. Funguje jako místní proxy server pro službu IoT Hub zveřejněním stejné koncové body protokolu jako služby IoT Hub. Taková konzistence znamená, že klienti (ať už zařízení nebo moduly) můžete připojit k modulu runtime IoT Edge, stejně jako do služby IoT Hub. 

>[!NOTE]
> Centrum IoT Edge podporuje klienty, kteří se připojují pomocí protokolu MQTT nebo AMQP. Klienti, kteří používají protokol HTTP nepodporuje. 

Centrum IoT Edge není s plnou verzí služby IoT Hub spuštěná místně. Existuje několik věcí, které Centrum IoT Edge tiše deleguje se do služby IoT Hub. Například Centrum IoT Edge předává požadavky na ověření do služby IoT Hub, když se zařízení poprvé pokusí připojit. Po prvním připojení se informace o zabezpečení jsou místně uložené ve službě IoT Edge hub. Další připojení z těchto zařízení jsou povolené a nemusí k ověření do cloudu. 

>[!NOTE]
>Modul runtime musí být připojen pokaždé, když se pokusí o ověření zařízení.

Používá ke snížení šířky pásma vašeho řešení IoT Edge a Centrum IoT Edge optimalizuje tak počet skutečných připojení probíhají do cloudu. Centrum IoT Edge přebírá logickou připojení od klientů, jako jsou moduly nebo zařízení typu list a kombinuje je pro jedno fyzické připojení ke cloudu. Podrobnosti tohoto procesu je transparentní pro zbytek tohoto řešení. Klienti myslíte, že budou mít svoje vlastní připojení ke cloudu i v případě, že všechny se odešlou přes stejné připojení. 

![Centrum IoT Edge je brána mezi fyzickým zařízením a centrem IoT](./media/iot-edge-runtime/Gateway.png)

 Centrum IoT Edge můžete určit, jestli je připojený ke službě IoT Hub. Pokud dojde ke ztrátě připojení, Centrum IoT Edge uloží zprávy nebo aktualizace dvojčete místně. Po připojení se obnoví, synchronizuje všechna data. Umístění použité pro tato dočasná mezipaměť se určuje podle vlastnosti tohoto dvojčete modulu IoT Edge hub. Velikost mezipaměti není omezené a se zvýší, dokud zařízení má kapacitu úložiště. 

### <a name="module-communication"></a>Komunikační modul

 Centrum IoT Edge umožňuje komunikaci modulu do modulu. Pomocí IoT Edge hub jako zprostředkovatel zpráv uchovává moduly nezávisle na sobě navzájem. Moduly stačí zadat vstupy, na kterých přijetí zprávy a výstupy, ke kterým se zápis zpráv. Pro vývojáře řešení pak spojí tyto vstupy a výstupy dohromady tak, aby moduly zpracovávat data v pořadí, které jsou specifické pro příslušné řešení. 

![Centrum IoT Edge umožňuje komunikaci modulu modulu](./media/iot-edge-runtime/module-endpoints.png)

K odesílání dat do centra IoT Edge, volá modul SendEventAsync metodu. První argument určuje, na které výstupu pro odeslání zprávy. Následujícím pseudokódu odešle zprávu na output1:

   ```csharp
   ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(transportSettings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Při příjmu zprávy, zaregistrujte zpětné volání, která zpracovává zprávy přicházející na specifický vstup. Následujícím pseudokódu zaregistruje messageProcessor funkce má být použit pro všechny zprávy přijaté ve vstup1 zpracování:

   ```csharp
   await client.SetInputMessageHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Další informace o třídě ModuleClient a jeho metody komunikace najdete v referenci rozhraní API pro váš preferovaný jazyk sady SDK: [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [C a Python](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable), nebo [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest).

Řešení pro vývojáře je zodpovědná za určení pravidel, které určují, jak předává zprávy mezi moduly IoT Edge hub. Pravidla směrování jsou definovány v cloudu a přesunout dolů k centru IoT Edge na jeho dvojče zařízení. Podle stejné syntaxe pro službu IoT Hub trasy se používá k definování tras mezi moduly ve službě Azure IoT Edge. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Trasy mezi moduly projít Centrum IoT Edge](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>Agenta IoT Edge

Agenta IoT Edge je další modul, který vytvoří modul runtime Azure IoT Edge. Zodpovídá za vytvoření instance moduly, zajištění, že nadále spouštět a hlásí stav modulů zpět do služby IoT Hub. Stejně jako ostatní moduly IoT Edge agent používá jeho dvojče zařízení pro ukládání těchto dat konfigurace. 

[Démon zabezpečení IoT Edge](iot-edge-security-manager.md) při spuštění zařízení spustí agenta IoT Edge. Agent načte její dvojče zařízení ze služby IoT Hub a zkontroluje, zda obsahuje manifest nasazení. Manifest nasazení je soubor JSON, který deklaruje moduly, které je potřeba spustit. 

Každá položka v manifestu nasazení obsahuje konkrétní informace o modulu a používá agenta IoT Edge pro řízení životního cyklu modulu. Zde jsou některé další zajímavé vlastnosti: 

* **Settings.Image** – image kontejneru, který používá agent IoT Edge spuštění modulu. Agenta IoT Edge musí být nakonfigurované pomocí přihlašovacích údajů pro službu container registry, pokud na obrázku je chráněn heslem. Přihlašovací údaje pro registr kontejneru dá vzdáleně pomocí manifest nasazení, nebo na samotném zařízení IoT Edge prostřednictvím aktualizace `config.yaml` souboru ve složce program IoT Edge.
* **settings.createOptions** – řetězec, který je předán přímo do démona Dockeru, při spouštění modulu kontejneru. Přidání možnosti Docker v této vlastnosti umožňuje rozšířené možnosti, jako je port předávání nebo připojení svazků do kontejneru modulu.  
* **Stav** – stavu, ve kterém agenta IoT Edge umístí modul. Tato hodnota je obvykle nastavená *systémem* jako většina lidí má agent IoT Edge k okamžitému spuštění všech modulů na zařízení. Můžete však zadat počáteční stav modulu můžete zastavit a počkat na budoucí dobu říct IoT Edge agenta spusťte modul. IoT Edge agent zasílá vytvářené sestavy stav každého modulu, zpět do cloudu v ohlášené vlastnosti. Rozdíl mezi požadované vlastnosti a ohlášených vlastností je indikátorem identifikovala zařízení. Podporované stavy jsou:
   * Stahuje se
   * Spuštěno
   * Není v pořádku
   * Selhalo
   * Zastaveno
* **restartPolicy** – způsob, jakým modul restartování agenta IoT Edge. Možné hodnoty:
   * – Agenta IoT Edge nikdy nerestartuje modulu.
   * onFailure – Pokud dojde k chybě modulu, IoT Edge agenta restartuje ho. Modul vypnutí čistě agenta IoT Edge se nerestartuje.
   * Není v pořádku – Pokud dojde k chybě modulu nebo je považován za není v pořádku, IoT Edge agenta restartuje ho.
   * -Pokud modul dojde k chybě, se bude považovat za není v pořádku nebo ukončení žádným způsobem, agenta IoT Edge restartuje ho. 

IoT Edge agent odešle odpověď modulu runtime pro službu IoT Hub. Tady je seznam možných odpovědi:
  * 200 – OK
  * 400 - konfigurace nasazení je chybný nebo není platný.
  * 417 – zařízení nemá nastavení konfigurace nasazení.
  * 412 – verze schématu v konfiguraci nasazení je neplatná.
  * 406 – zařízení IoT Edge je offline nebo neposílá stav sestavy.
  * 500 - došlo k chybě v modulu runtime IoT Edge.

### <a name="security"></a>Zabezpečení

Agenta IoT Edge hraje důležitou roli v zabezpečení zařízení IoT Edge. Například provede akce, jako je ověření imagi modulu před jeho zahájením. 

Další informace o rozhraní zabezpečení Azure IoT Edge, přečtěte si informace o [správce zabezpečení IoT Edge](iot-edge-security-manager.md)

## <a name="next-steps"></a>Další postup

[Vysvětlení, že se certifikáty Azure IoT Edge](iot-edge-certs.md)
