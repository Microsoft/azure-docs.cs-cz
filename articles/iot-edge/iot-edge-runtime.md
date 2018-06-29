---
title: Pochopení runtime Azure IoT Edge | Microsoft Docs
description: Další informace o modulu runtime Azure IoT okraj a jak ji umožňuje hraniční zařízení
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/05/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: aa371ef2ebad01fba379675e8438f56dca9ce356
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096962"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Pochopení modulu runtime Azure IoT okraj a jeho architektura

Modul runtime IoT okraj je kolekce programy, které je potřeba nainstalovat na zařízení, aby se dalo považovat za IoT hraniční zařízení. Komponenty modulu runtime IoT Edge souhrnně, povolte IoT hraniční zařízení získat kód pro spuštění na hranici a komunikaci výsledky. 

Modul runtime IoT Edge provádí následující funkce na IoT hraniční zařízení:

* Instaluje a aktualizuje na zařízení úlohy.
* Udržuje na zařízení standardy zabezpečení Azure IoT Edge.
* Zajišťuje, že [IoT Edge moduly][lnk moduly] vždy běží.
* Hlásí do cloudu stav modulů pro účely vzdáleného monitorování.
* Usnadňuje komunikaci mezi podřízenými zařízeními typu list a příslušným hraničním zařízením IoT.
* Usnadňuje komunikaci mezi moduly v příslušném hraničním zařízení IoT.
* Usnadňuje komunikaci mezi příslušným hraničním zařízením IoT a cloudem.

![Okraj IoT runtime komunikuje stav modulu do služby IoT Hub a Statistika][1]

Odpovědnosti modulu runtime IoT Edge rozdělit do dvou kategorií: modulu správy a komunikace. Tyto dvě role jsou prováděny dvě součásti, které tvoří runtime IoT okraj. Centra IoT okraj je zodpovědná za komunikaci, při agenta IoT Edge spravuje nasazení a monitorování modulů. 

Agent okraj a okraj rozbočovače jsou moduly, stejně jako ostatní moduly systémem IoT hraniční zařízení. Další informace o fungování modulů najdete v tématu [lnk moduly]. 

## <a name="iot-edge-hub"></a>Centra IoT Edge

Centrum hraniční je mezi dvěma moduly, které tvoří runtime Azure IoT okraj. Díky zpřístupnění stejné koncové body protokolu jako IoT Hub funguje jako místní proxy server pro služby IoT Hub. Tato konzistence znamená, že klienti (jestli zařízení nebo moduly) může připojit k modulu runtime IoT Edge, stejně jako do služby IoT Hub. 

>[!NOTE]
>Hraniční rozbočovače podporuje klienty, které se připojují prostřednictvím MQTT nebo AMQP. Klienti, kteří používají protokol HTTP nepodporuje. 

Centra Edge není plnou verzi služby IoT Hub spuštěn místně. Existují některé věci, které rozbočovače Edge bezobslužně deleguje do služby IoT Hub. Hraniční rozbočovače například předává do služby IoT Hub žádosti o ověření, když se zařízení poprvé pokusí připojit. Po první připojení, informace o zabezpečení je do místní mezipaměti Edge rozbočovače. Další připojení z těchto zařízení jsou povoleny bez nutnosti ověření do cloudu. 

>[!NOTE]
>Při každém pokusu o ověření zařízení, musí být připojen modulu runtime.

Ke snížení šířky pásma vašeho řešení IoT používá, rozbočovače Edge optimalizuje, kolik skutečné připojení jsou vytvářeny do cloudu. Hraniční rozbočovače trvá logické připojení od klientů, jako jsou moduly nebo listu zařízení a kombinuje je pro jedno fyzické připojení ke cloudu. Podrobnosti tohoto procesu jsou transparentní, zbytek řešení. Klienti vezměte v úvahu, že mají svoje vlastní připojení ke cloudu i v případě, že jsou všechny odesílány přes stejné připojení. 

![Hraniční centra funguje jako brána mezi několik fyzických zařízení a cloud][2]

Hraniční rozbočovače můžete určit, jestli je připojený ke službě IoT Hub. Pokud dojde ke ztrátě připojení, uloží Edge Centra zpráv nebo twin aktualizace místně. Po připojení se znovu navázat, synchronizuje se všechna data. Umístění použitých pro toto dočasný mezipaměť je určen podle vlastnost twin modulu Edge rozbočovače. Velikost mezipaměti není uzavřeny a poroste, pokud zařízení obsahuje úložnou kapacitu. 

>[!NOTE]
>Přidání kontrolu nad další parametry ukládání do mezipaměti přidá do produktu před přejde obecné dostupnosti.

### <a name="module-communication"></a>Modul komunikace

Hraniční rozbočovače usnadňuje komunikaci modulu do modulu. Pomocí centra okraj jako zprostředkovatele zpráv zajišťuje modulů na sobě nezávislé. Moduly pouze nutné zadat vstupy, na které přijímají zprávy a výstupy, do kterých se zápis zpráv. Vývojář řešení pak spojí tyto vstupy a výstupy společně, aby moduly zpracování dat v pořadí, které jsou specifické pro toto řešení. 

![Hraniční rozbočovače usnadňuje komunikaci modulu modulu][3]

K odesílání dat do centra Edge, modul volá metodu SendEventAsync. První argument určuje, na které výstup k odeslání zprávy. Následující pseudokódu odešle zprávu na output1:

   ```csharp
   DeviceClient client = new DeviceClient.CreateFromConnectionString(moduleConnectionString, settings); 
   await client.OpenAsync(); 
   await client.SendEventAsync(“output1”, message); 
   ```

Chcete-li přijímat zprávy, zaregistrujte zpětné volání, které zpracovává zprávy přicházející na specifický vstup. Následující pseudokódu zaregistruje messageProcessor funkce, který se má použít pro zpracování všechny zprávy přijaté na input1:

   ```csharp
   await client.SetEventHandlerAsync(“input1”, messageProcessor, userContext);
   ```

Vývojář řešení je zodpovědná za určení pravidel, které určují, jak Centrum Edge předává zprávy mezi moduly. Pravidla směrování jsou definovány v cloudu a odeslány rozbočovače Edge v jeho dvojče zařízení. Stejná syntaxe pro IoT Hub trasy se používá k definování tras mezi moduly v Azure IoT Edge. 

<!--- For more info on how to declare routes between modules, see []. --->   

![Směrování mezi moduly][4]

## <a name="iot-edge-agent"></a>Agent IoT Edge

Agent IoT okraj je další modul, který tvoří runtime Azure IoT okraj. Zodpovídá za vytvoření instance moduly, zajistíte, že budou nadále spouštět a zprávy o stavu modulů zpět do služby IoT Hub. Stejně jako ostatní moduly Edge agent používá jeho twin modulu pro uložení dat této konfigurace. 

Zahájit provádění Edge agenta, spusťte příkaz start azure-iot-edge-runtime-ctl.py. Agent načte jeho modulu twin ze služby IoT Hub a zkontroluje slovníku moduly. Slovník moduly je kolekce modulů, které je potřeba spustit. 

Každá položka ve slovníku moduly obsahuje konkrétní informace o modulu a je používána agenta Edge řízení životního cyklu modulu. Jsou některé z vlastností zajímavějšího: 

* **Settings.Image** – kontejner bitové kopie, Edge agent používá ke spuštění modulu. Agent okraje musí být nakonfigurované přihlašovací údaje pro kontejner registru Pokud bitovou kopii je chráněný heslem. Pokud chcete nakonfigurovat hraniční agenta, aktualizujte `config.yaml` souboru. V systému Linux použijte následující příkaz: `sudo nano /etc/iotedge/config.yaml`
* **settings.createOptions** – řetězec, který je předán přímo démon Docker při spouštění modulu kontejneru. Přidání možnosti Docker v této vlastnosti umožňuje rozšířené možnosti, jako je port, předávání nebo připojení svazků do kontejneru modulu.  
* **Stav** – stavu, ve kterém Edge agent umístí modul. Tato hodnota se obvykle nastavuje *systémem* jako většina lidí má agent Edge k okamžitému spuštění všech modulů na zařízení. Můžete však zadat počáteční stav modulu do zastaveny a čekat na datum v budoucnosti říct Edge agenta spusťte modul. Edge agent hlásí stav každého modulu zpět cloudu ve vlastnostech hlášené. Rozdíl mezi požadovanou vlastnost a vlastnost hlášené je slouží jako ukazatel identifikovala zařízení. Jsou podporované stavy:
   * Stahuje se
   * Spuštěno
   * Není v pořádku
   * Selhalo
   * Zastaveno
* **restartPolicy** – jak agenta Edge restartuje modul. Možné hodnoty:
   * – Agenta Edge nikdy nerestartuje modul.
   * onFailure – Pokud dojde k chybě v modulu, Edge agent restartuje ho. Modul ukončí řádně, Edge agent se nerestartuje.
   * Není v pořádku – Pokud je modul dojde k chybě nebo považovat za chybný, Edge agent restartuje ho.
   * Vždy – pokud modul dojde k chybě, se považují za není v pořádku nebo ukončí žádným způsobem, Edge agent restartuje ho. 

Agent IoT Edge odešle odpověď runtime do služby IoT Hub. Tady je seznam možných odpovědí:
  * 200 – OK
  * 400 - konfigurace nasazení je chybný nebo není platný.
  * 417 – zařízení nemá nastavit konfiguraci nasazení.
  * 412 – verze schématu v konfiguraci nasazení je neplatný.
  * 406 - hraniční zařízení je offline nebo není odesílání stav sestavy.
  * 500 - došlo k chybě v modulu runtime okraj.

### <a name="security"></a>Zabezpečení

Agenta IoT Edge hraje důležitou roli v zabezpečení IoT hraniční zařízení. Například provede akce, jako je ověření bitové kopie modul před jeho spuštění. Tyto funkce budou přidány po zavedení obecné dostupnosti. 

<!-- For more information about the Azure IoT Edge security framework, see []. -->

## <a name="next-steps"></a>Další postup

- [Pochopení moduly Azure IoT Edge][lnk moduly]

<!-- Images -->
[1]: ./media/iot-edge-runtime/Pipeline.png
[2]: ./media/iot-edge-runtime/Gateway.png
[3]: ./media/iot-edge-runtime/ModuleEndpoints.png
[4]: ./media/iot-edge-runtime/ModuleEndpointsWithRoutes.png

<!-- Links -->
[lnk moduly]: iot-edge-modules.md
