---
title: Použití metrik ke sledování služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Jak používat metriky Azure IoT Hub k posouzení a monitorování celkového stavu vašich center IoT.
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: jlian
ms.openlocfilehash: ec8a00460b4a750339f929eb6879ac6eb63cac8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284431"
---
# <a name="understand-iot-hub-metrics"></a>Principy metrik centra IoT Hub

Metriky Služby IoT Hub poskytují lepší data o stavu prostředků Azure IoT ve vašem předplatném Azure. Metriky služby IoT Hub umožňují posoudit celkový stav služby IoT Hub a zařízení k ní připojeným. Statistiky pro uživatele jsou důležité, protože vám pomůžou zjistit, co se děje s vaším centrem IoT, a pomoct problémům s hlavní příčinou, aniž byste museli kontaktovat podporu Azure.

Metriky jsou ve výchozím nastavení povoleny. Metriky služby IoT Hub můžete zobrazit na webu Azure Portal.

> [!NOTE]
> Metriky IoT Hubu můžete použít k zobrazení informací o zařízeních IoT Plug and Play připojených k vašemu IoT Hubu. Zařízení IoT Plug and Play jsou součástí veřejné verze [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md).

## <a name="how-to-view-iot-hub-metrics"></a>Jak zobrazit metriky ioT hubu

1. Vytvořte centrum IoT. Najdete pokyny, jak vytvořit službu IoT hub v [rozhraní Send telemetrie ze zařízení do průvodce službou IoT Hub.](quickstart-send-telemetry-dotnet.md)

2. Otevřete čepel svého centra IoT hub. Odtud klikněte na **metriky**.
   
    ![Snímek obrazovky znázorňující, kde je možnost metrik na stránce prostředků Centra IoT Hub](./media/iot-hub-metrics/enable-metrics-1.png)

3. V okně metriky můžete zobrazit metriky pro centrum IoT a vytvořit vlastní zobrazení metrik. 
   
    ![Snímek obrazovky se stránkou metrik pro Centrum IoT Hub](./media/iot-hub-metrics/enable-metrics-2.png)
    
4. Data metrik můžete odeslat do koncového bodu Centra událostí nebo do účtu Azure Storage kliknutím na **Nastavení diagnostiky**a **potom na Přidat diagnostické nastavení.**

   ![Snímek obrazovky znázorňující, kde je tlačítko nastavení diagnostiky](./media/iot-hub-metrics/enable-metrics-3.png)

## <a name="iot-hub-metrics-and-how-to-use-them"></a>Metriky IoT Hubu a jejich použití

IoT Hub poskytuje několik metrik, které vám poskytnou přehled o stavu vašeho centra a o celkovém počtu připojených zařízení. Můžete kombinovat informace z více metrik a nakreslit větší obrázek o stavu centra IoT hub. Následující tabulka popisuje metriky, které každý skladový systém IoT hub ukaždého sleduje, a způsob, jakým každá metrika souvisí s celkovým stavem centra IoT hub.

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Pokusy o odeslání telemetrické zprávy|Počet|Celkem|Počet telemetrických zpráv mezi zařízeními a cloudy, které se pokusily odeslat do služby IoT hub|Žádný|
|d2c.telemetry.ingress.success|Odeslané telemetrické zprávy|Počet|Celkem|Počet telemetrických zpráv mezi zařízeními a cloudy, které byly úspěšně odeslány do služby IoT hub|Žádný|
|c2d.commands.egress.complete.success|Dokončené dodávky zpráv C2D|Počet|Celkem|Počet doručování zpráv z cloudu na zařízení, které zařízení úspěšně dokončilo|Žádný|
|c2d.commands.egress.abandon.success|Zprávy C2D byly opuštěny.|Počet|Celkem|Počet zpráv mezi cloudy a zařízení, které zařízení opustilo|Žádný|
|c2d.commands.egress.reject.success|Zprávy C2D byly odmítnuty.|Počet|Celkem|Počet zpráv mezi cloudy a zařízení, které zařízení odmítlo|Žádný|
|C2DMessagesVypršela|Platnost zpráv C2D vypršela (náhled)|Počet|Celkem|Počet zpráv s prošlým platností v oblasti cloudu se zařízením|Žádný|
|devices.totalDevices|Zařízení celkem (zastaralé)|Počet|Celkem|Počet zařízení registrovaných do vašeho centra IoT hub|Žádný|
|devices.connectedDevices.allProtocol|Připojená zařízení (zastaralé) |Počet|Celkem|Počet zařízení připojených k vašemu centru IoT hub|Žádný|
|d2c.telemetry.egress.success|Směrování: doručovány telemetrické zprávy|Počet|Celkem|Počet, kolikrát byly zprávy úspěšně doručeny do všech koncových bodů pomocí směrování služby IoT Hub. Pokud je zpráva směrována do více koncových bodů, tato hodnota se zvýší o jeden pro každou úspěšnou doručení. Pokud je zpráva doručena do stejného koncového bodu vícekrát, tato hodnota se zvýší o jeden pro každou úspěšnou doručení.|Žádný|
|d2c.telemetry.egress.dropped|Směrování: zprávy telemetrie vynechány |Počet|Celkem|Počet, kolikrát zprávy byly vynechány směrování služby IoT Hub z důvodu mrtvé koncové body. Tato hodnota nepočítá zprávy doručovány do záložní trasy jako vynecháné zprávy nejsou doručovány tam.|Žádný|
|d2c.telemetry.egress.orphaned|Směrování: telemetrické zprávy osamocené |Počet|Celkem|Počet osamocených zpráv směrováním služby IoT Hub, protože neodpovídaly žádným pravidlům směrování (včetně záložního pravidla). |Žádný|
|d2c.telemetry.egress.invalid|Směrování: telemetrické zprávy nekompatibilní|Počet|Celkem|Počet směrování služby IoT Hub se nezdařilo doručit zprávy z důvodu nekompatibility s koncovým bodem. Tato hodnota nezahrnuje opakování.|Žádný|
|d2c.telemetry.egress.fallback|Směrování: zprávy doručovány do záložní|Počet|Celkem|Počet, kolikrát ioT Hub směrování doručovány zprávy do koncového bodu přidruženého k záložní trasy.|Žádný|
|d2c.endpoints.egress.eventHubs|Směrování: zprávy doručené do centra událostí|Počet|Celkem|Počet směrování služby IoT Hub úspěšně doručoval zprávy do koncových bodů centra událostí.|Žádný|
|d2c.endpoints.latency.eventHubs|Směrování: latence zpráv pro centrum událostí|Milisekund|Průměr|Průměrná čekací doba (milisekund) mezi příchozím přenosem zpráv do služby IoT Hub a příchozím přenosem zpráv do koncového bodu centra událostí.|Žádný|
|d2c.endpoints.egress.serviceBusQueues|Směrování: zprávy doručené do fronty služby Service Bus|Počet|Celkem|Počet směrování služby IoT Hub úspěšně doručoval zprávy do koncových bodů fronty služby Service Bus.|Žádný|
|d2c.endpoints.latency.serviceBusQueues|Směrování: latence zpráv pro frontu služby Service Bus|Milisekund|Průměr|Průměrná čekací doba (milisekund) mezi příchozím přenosem zprávy do služby IoT Hub a příchozím přenosem dat telemetrické zprávy do koncového bodu fronty služby Service Bus.|Žádný|
|d2c.endpoints.egress.serviceBusTémata|Směrování: zprávy doručené do tématu sběrnice|Počet|Celkem|Počet směrování služby IoT Hub úspěšně doručoval zprávy do koncových bodů tématu služby Service Bus.|Žádný|
|d2c.endpoints.latency.serviceBusTopics|Směrování: latence zpráv pro téma služby Service Bus|Milisekund|Průměr|Průměrná čekací doba (milisekund) mezi příchozím přenosem zpráv do služby IoT Hub a příchozím přenosem dat telemetrické zprávy do koncového bodu tématu služby Service Bus.|Žádný|
|d2c.endpoints.egress.builtIn.events|Směrování: zprávy doručené do zpráv/událostí|Počet|Celkem|Počet směrování služby IoT Hub úspěšně doručoval zprávy do předdefinovaného koncového bodu (zprávy nebo události).|Žádný|
|d2c.endpoints.latency.builtIn.events|Směrování: latence zpráv pro zprávy nebo události|Milisekund|Průměr|Průměrná čekací doba (milisekund) mezi příchozím přenosem zpráv do služby IoT Hub a příchozím přenosem dat telemetrické zprávy do předdefinovaného koncového bodu (zprávy/události).|Žádný|
|d2c.endpoints.egress.storage|Směrování: zprávy doručené do úložiště|Počet|Celkem|Počet směrování služby IoT Hub úspěšně doručoval zprávy koncovým bodům úložiště.|Žádný|
|d2c.endpoints.latency.storage|Směrování: latence zpráv pro úložiště|Milisekund|Průměr|Průměrná čekací doba (milisekund) mezi příchozím přenosem zpráv do služby IoT Hub a příchozím přenosem dat telemetrické zprávy do koncového bodu úložiště.|Žádný|
|d2c.endpoints.egress.storage.bytes|Směrování: data dodaná do úložiště|Bajty|Celkem|Množství dat (bajtů) Směrování služby IoT Hub dodané do koncových bodů úložiště.|Žádný|
|objekty blob d2c.endpoints.egress.storage.|Směrování: objekty BLOB dodané do úložiště|Počet|Celkem|Kolikrát směrování služby IoT Hub doručovalo objekty blob do koncových bodů úložiště.|Žádný|
|EventGridDeliveries|Dodávky v gridu událostí (náhled)|Počet|Celkem|Počet událostí ioT hub publikovaných do mřížky událostí. Dimenzi Výsledek použijte pro počet úspěšných a neúspěšných požadavků. Dimenze EventType zobrazuje typhttps://aka.ms/ioteventgrid)události ( .|Resourceid<br/>Výsledek<br/>Typ události|
|Latence eventgridu|Latence sítě událostí (náhled)|Milisekund|Průměr|Průměrná čekací doba (milisekund) od doby, kdy byla generována událost centra Iot Hub, do doby, kdy byla událost publikována do mřížky událostí. Toto číslo je průměr mezi všemi typy událostí. Pomocí dimenze EventType můžete zobrazit latenci určitého typu události.|Resourceid<br/>Typ události|
|d2c.twin.read.success|Úspěšná dvojitá čtení ze zařízení|Počet|Celkem|Počet všech úspěšných dvojčetů iniciovaných zařízením.|Žádný|
|d2c.twin.read.selhání|Selhání dvojčete čte ze zařízení|Počet|Celkem|Počet všech neúspěšných dvojčete iniciovaných zařízením.|Žádný|
|d2c.twin.read.size|Velikost odezvy dvojčete čte ze zařízení|Bajty|Průměr|Průměr, min a max všech úspěšných zařízení-inicioval dvojče čte.|Žádný|
|d2c.twin.update.success|Úspěšné aktualizace dvojčete ze zařízení|Počet|Celkem|Počet všech úspěšných aktualizací dvojčete iniciovaných zařízením.|Žádný|
|d2c.twin.update.selhání|Neúspěšné aktualizace dvojčete ze zařízení|Počet|Celkem|Počet všech neúspěšných aktualizací dvojčete iniciovaných zařízením.|Žádný|
|d2c.twin.update.size|Velikost aktualizací dvojčete ze zařízení|Bajty|Průměr|Průměrná, min a maximální velikost všech úspěšných aktualizací dvojčete iniciovaných zařízením.|Žádný|
|c2d.methods.success|Úspěšné přímé vyvolání metody|Počet|Celkem|Počet všech úspěšných volání přímé metody.|Žádný|
|c2d.methods.selhání|Neúspěšná přímá metoda vyvolání|Počet|Celkem|Počet všech neúspěšných volání přímé metody.|Žádný|
|c2d.methods.requestVelikost|Velikost požadavku na vyvolání přímé metody|Bajty|Průměr|Průměr, min a max všech úspěšných požadavků přímé metody.|Žádný|
|c2d.methods.responseVelikost|Velikost odpovědi přímé metody vyvolání|Bajty|Průměr|Průměr, min a max všech úspěšných přímých odpovědí metod.|Žádný|
|c2d.twin.read.success|Úspěšné dvojče čte z back-endu|Počet|Celkem|Počet všech úspěšných back-end iniciované dvojče čte.|Žádný|
|c2d.twin.read.selhání|Neúspěšné dvojče čte ze zadního konce|Počet|Celkem|Počet všech neúspěšných čtení dvojčete iniciovaného back-endem.|Žádný|
|c2d.twin.read.size|Velikost odpovědi dvojčete čte ze back-endu|Bajty|Průměr|Průměr, min a max všech úspěšných back-end-inicioval dvojče čte.|Žádný|
|c2d.twin.update.success|Úspěšné aktualizace dvojčat z back-endu|Počet|Celkem|Počet všech úspěšných aktualizací dvojčete iniciovaných back-endem.|Žádný|
|c2d.twin.update.selhání|Neúspěšné aktualizace dvojčete ze back-endu|Počet|Celkem|Počet všech neúspěšných aktualizací dvojčete iniciovaných back-endem.|Žádný|
|c2d.twin.update.size|Velikost twin aktualizace z back-endu|Bajty|Průměr|Průměrná, min a maximální velikost všech úspěšných aktualizací dvojčete iniciovaných back-endem.|Žádný|
|twinQueries.success|Úspěšné dotazy na dvojče|Počet|Celkem|Počet všech úspěšných twin dotazy.|Žádný|
|twinQueries.failure|Neúspěšné dotazy na dvojče|Počet|Celkem|Počet všech neúspěšných dotazů dvojčat.|Žádný|
|twinQueries.resultSize|Velikost výsledku twin queries|Bajty|Průměr|Průměr, min a max velikost výsledku všech úspěšných twin dotazů.|Žádný|
|jobs.createTwinUpdateJob.success|Úspěšné výtvory úloh s dvojčaty|Počet|Celkem|Počet všech úspěšných vytvoření úloh s dvojčaty.|Žádný|
|jobs.createTwinUpdateJob.failure|Neúspěšné vytvoření úloh s dvojčaty|Počet|Celkem|Počet všech neúspěšných vytvoření úloh s dvojčaty aktualizací.|Žádný|
|jobs.createDirectMethodJob.success|Úspěšné vytváření úloh vyvolání metody|Počet|Celkem|Počet všech úspěšných vytvoření úloh přímého vyvolání metody.|Žádný|
|jobs.createDirectMethodJob.failure|Neúspěšné vytváření úloh vyvolání metody|Počet|Celkem|Počet všech neúspěšných vytvoření úloh přímého vyvolání metody.|Žádný|
|jobs.listJobs.success|Úspěšná volání do seznamu úloh|Počet|Celkem|Počet všech úspěšných volání do seznamu úloh.|Žádný|
|jobs.listJobs.selhání|Neúspěšná volání seznam úloh|Počet|Celkem|Počet všech neúspěšných volání seznam úloh.|Žádný|
|jobs.cancelJob.success|Úspěšné zrušení úlohy|Počet|Celkem|Počet všech úspěšných volání zrušit úlohu.|Žádný|
|jobs.cancelJob.failure|Neúspěšné zrušení úlohy|Počet|Celkem|Počet všech neúspěšných volání zrušit úlohu.|Žádný|
|jobs.queryJobs.success|Dotazy na úspěšnou úlohu|Počet|Celkem|Počet všech úspěšných volání dotazovacích úloh.|Žádný|
|jobs.queryJobs.failure|Dotazy na úlohy se nezdařilo|Počet|Celkem|Počet všech neúspěšných volání dotazovacích úloh.|Žádný|
|jobs.completed|Dokončené úlohy|Počet|Celkem|Počet všech dokončených úloh.|Žádný|
|jobs.failed|Neúspěšné úlohy|Počet|Celkem|Počet všech neúspěšných úloh.|Žádný|
|d2c.telemetry.ingress.sendThrottle|Počet chyb omezení|Počet|Celkem|Počet chyb omezení z důvodu omezení propustnost zařízení|Žádný|
|dailyMessageQuotaUsed|Celkový počet použitých zpráv|Počet|Průměr|Počet celkových zpráv, které byly dnes použity. Toto je kumulativní hodnota, která je každý den nastavena na nulu v 00:00 UTC.|Žádný|
|deviceDataUsage|Celkové využití dat zařízení|Bajty|Celkem|Bajty přenesené do a z všech zařízení připojených k IotHub|Žádný|
|deviceDataUsageV2|Celkové využití dat zařízení (náhled)|Bajty|Celkem|Bajty přenesené do a z všech zařízení připojených k IotHub|Žádný|
|totalDeviceCount|Celkový počet zařízení (náhled)|Počet|Průměr|Počet zařízení registrovaných do vašeho centra IoT hub|Žádný|
|připojenýPočet zařízení|Připojená zařízení (náhled)|Počet|Průměr|Počet zařízení připojených k vašemu centru IoT hub|Žádný|
|Konfigurace|Metriky konfigurace|Počet|Celkem|Metriky pro operace konfigurace|Žádný|

## <a name="next-steps"></a>Další kroky

Teď, když jste viděli přehled metrik služby IoT Hub, natomto odkazu se dozvíte víc o správě Služby Azure IoT Hub:

* [Nastavení diagnostických protokolů](iot-hub-monitor-resource-health.md)

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Průvodce vývojáři ioT Hubu](iot-hub-devguide.md)

* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)
