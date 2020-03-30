---
title: Škálování centra Azure IoT Hub | Dokumenty společnosti Microsoft
description: Jak škálovat službu IoT hub tak, aby podporovala očekávanou propustnost zpráv a požadované funkce. Zahrnuje souhrn podporované propustnost pro každou úroveň a možnosti pro prosazení se tvrdosti.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.openlocfilehash: ecc1ae5138fe5a1e42fed9be2e31b5afa8b6d5b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497501"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Volba vhodné vrstvy služby IoT Hub pro vaše řešení

Každé řešení IoT je jiné, takže Azure IoT Hub nabízí několik možností založených na cenách a škálování. Tento článek vám pomůže vyhodnotit vaše potřeby centra IoT Hub. Informace o cenách o úrovních IoT Hub najdete v tématu [Ceny ioT Hub .](https://azure.microsoft.com/pricing/details/iot-hub)

Chcete-li se rozhodnout, která úroveň IoT Hub je pro vaše řešení vhodná, položte si dvě otázky:

**Jaké funkce mám v plánu použít?**

Azure IoT Hub nabízí dvě úrovně, základní a standardní, které se liší v počtu funkcí, které podporují. Pokud je vaše řešení IoT založeno na shromažďování dat ze zařízení a centrální analýze, pak je základní úroveň pravděpodobně pro vás ta pravá. Pokud chcete používat pokročilejší konfigurace k vzdálenému řízení zařízení IoT nebo distribuci některých úloh do samotných zařízení, měli byste zvážit standardní úroveň. Pro podrobný rozpis, které funkce jsou zahrnuty v každé úrovni pokračovat na [základní a standardní úrovně](#basic-and-standard-tiers).

**Kolik dat mám v plánu denně přesouvat?**

Každá úroveň Služby IoT Hub je k dispozici ve třech velikostech na základě toho, kolik propustnost dat mohou v daný den zpracovat. Tyto velikosti jsou číselně označeny jako 1, 2 a 3. Například každá jednotka centra IoT úrovně 1 zvládne 400 tisíc zpráv denně, zatímco jednotka úrovně 3 zvládne 300 milionů. Další podrobnosti o pokynech pro data nadále [message propustnost](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Základní a standardní úrovně

Standardní úroveň Služby IoT Hub umožňuje všechny funkce a je vyžadována pro všechna řešení IoT, která chtějí využívat obousměrné komunikační funkce. Základní úroveň nabízí jen podmnožinu funkcí. Je určená pro řešení IoT, která potřebují jenom jednosměrnou komunikaci ze zařízení do cloudu. Obě úrovně nabízejí stejné bezpečnostní a ověřovací funkce.

Pro centrum IoT Hub lze vybrat jenom jeden typ [edice](https://azure.microsoft.com/pricing/details/iot-hub/) v rámci úrovně. Můžete například vytvořit ioT hub s více jednotkami S1, ale ne se kombinací jednotek z různých edic, jako jsou S1 a S2.

| Schopnost | Úroveň Basic | Úroveň Free/Standard |
| ---------- | ---------- | ------------- |
| [Telemetrie mezi zařízeními a cloudy](iot-hub-devguide-messaging.md) | Ano | Ano |
| [Identita pro zařízení](iot-hub-devguide-identity-registry.md) | Ano | Ano |
| [Směrování zpráv](iot-hub-devguide-messages-read-custom.md), [obohacení zpráv](iot-hub-message-enrichments-overview.md)a integrace [mřížky událostí](iot-hub-event-grid.md) | Ano | Ano |
| [Protokoly HTTP, AMQP a MQTT](iot-hub-devguide-protocols.md) | Ano | Ano |
| [Služba zřizování zařízení](../iot-dps/about-iot-dps.md) | Ano | Ano |
| [Monitorování a diagnostika](iot-hub-monitor-resource-health.md) | Ano | Ano |
| [Zasílání zpráv mezi cloudy](iot-hub-devguide-c2d-guidance.md) |   | Ano |
| [Dvojčata zařízení](iot-hub-devguide-device-twins.md), [dvojčata modulů](iot-hub-devguide-module-twins.md)a [správa zařízení](iot-hub-device-management-overview.md) |   | Ano |
| [Datové proudy zařízení (náhled)](iot-hub-device-streams-overview.md) |   | Ano |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Ano |
| [Náhled ioT plug and play](../iot-pnp/overview-iot-plug-and-play.md) |   | Ano |

IoT Hub také nabízí bezplatnou úroveň, která je určena pro testování a hodnocení. Má všechny možnosti standardní úrovně, ale omezené povolenky pro zasílání zpráv. Nelze upgradovat z úrovně free na základní nebo standardní.

## <a name="partitions"></a>Oddíly

Azure IoT Hubs obsahují mnoho základních součástí [Azure Event Hubs](../event-hubs/event-hubs-features.md), včetně [oddílů](../event-hubs/event-hubs-features.md#partitions). Datové proudy událostí pro služby IoT Hubs jsou obvykle naplněny příchozími telemetrickými daty, která jsou hlášena různými zařízeními IoT. Dělení datového proudu událostí se používá ke snížení kolizí, ke kterým dochází při souběžnéčtení a zápisu do datových proudů událostí.

Limit oddílu je vybrán při vytvoření ioT hub a nelze změnit. Maximální limit oddílu pro základní úroveň IoT Hub a standardní úroveň IoT Hub je 32. Většina center IoT potřebuje jenom 4 oddíly. Další informace o určení oddílů najdete v nejčastějších dotazech k centru událostí [Kolik oddílů potřebuji?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Upgrade úrovně

Po vytvoření centra IoT můžete upgradovat ze základní vrstvy na standardní úroveň bez přerušení stávajících operací. Další informace najdete v tématu [Jak upgradovat centrum IoT hub](iot-hub-upgrade.md).

Konfigurace oddílu zůstane beze změny při migraci ze základní vrstvy na úroveň standard.

> [!NOTE]
> Úroveň free nepodporuje upgrade na základní nebo standardní.

## <a name="iot-hub-rest-apis"></a>Api rest centra IoT Hub

Rozdíl v podporovaných možnostech mezi základní a standardní vrstvy služby IoT Hub znamená, že některá volání rozhraní API nefungují s centry základní vrstvy. V následující tabulce jsou uvedena dostupná rozhraní API:

| rozhraní API | Úroveň Basic | Úroveň Free/Standard |
| --- | ---------- | ------------- |
| [Odstranit zařízení](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletedevice) | Ano | Ano |
| [Získat zařízení](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevice) | Ano | Ano |
| [Odstranit modul](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/deletemodule) | Ano | Ano |
| [Získat modul](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getmodule) | Ano | Ano |
| [Získání statistik registru](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getdevicestatistics) | Ano | Ano |
| [Získat statistiky služeb](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/getservicestatistics) | Ano | Ano |
| [Vytvoření nebo aktualizace zařízení](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatedevice) | Ano | Ano |
| [Vytvořit nebo aktualizovat modul](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/createorupdatemodule) | Ano | Ano |
| [Centrum IoT dotazu](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/queryiothub) | Ano | Ano |
| [Vytvořit soubor nahrávání Identifikátor URI](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Ano | Ano |
| [Přijímat oznámení vázané na zařízení](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Ano | Ano |
| [Odeslat událost zařízení](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Ano | Ano |
| Odeslat událost modulu | Pouze AMQP a MQTT | Pouze AMQP a MQTT |
| [Aktualizovat stav odeslání souboru](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Ano | Ano |
| [Operace s hromadným zařízením](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/bulkdevicecrud) | Ano, s výjimkou funkcí IoT Edge | Ano |
| [Zrušit úlohu exportu importu](https://docs.microsoft.com/rest/api/iothub/service/jobclient/cancelimportexportjob) | Ano | Ano |
| [Vytvořit úlohu exportu importu](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createimportexportjob) | Ano | Ano |
| [Získat úlohu exportu importu](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjob) | Ano | Ano |
| [Získat import exportní úlohy](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getimportexportjobs) | Ano | Ano |
| [Vymazat frontu příkazů](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) |   | Ano |
| [Získat dvojče zařízení](https://docs.microsoft.com/rest/api/iothub/service/twin/getdevicetwin) |   | Ano |
| [Získat dvojče modulu](https://docs.microsoft.com/rest/api/iothub/service/twin/getmoduletwin) |   | Ano |
| [Metoda invoke zařízení](https://docs.microsoft.com/rest/api/iothub/service/devicemethod/invokedevicemethod) |   | Ano |
| [Aktualizovat dvojče zařízení](https://docs.microsoft.com/rest/api/iothub/service/twin/updatedevicetwin) |   | Ano |
| [Aktualizovat dvojče modulu](https://docs.microsoft.com/rest/api/iothub/service/twin/updatemoduletwin) |   | Ano |
| [Oznámení o opuštění zařízení](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Ano |
| [Úplné oznámení vázané na zařízení](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Ano |
| [Zrušit úlohu](https://docs.microsoft.com/rest/api/iothub/service/jobclient/canceljob) |   | Ano |
| [Vytvoření úlohy](https://docs.microsoft.com/rest/api/iothub/service/jobclient/createjob) |   | Ano |
| [Získat práci](https://docs.microsoft.com/rest/api/iothub/service/jobclient/getjob) |   | Ano |
| [Úlohy dotazu](https://docs.microsoft.com/rest/api/iothub/service/jobclient/queryjobs) |   | Ano |

## <a name="message-throughput"></a>Propustnost zprávy

Nejlepší způsob, jak velikost řešení IoT Hub je vyhodnotit provoz na základě jednotky. Zvažte zejména požadovanou propustnost špičky pro následující kategorie operací:

* Zprávy typu zařízení-cloud
* Zprávy z cloudu na zařízení
* Operace registru identit

Provoz se měří pro vaše centrum IoT na základě jednotky. Když vytvoříte centrum IoT, zvolíte jeho úroveň a edici a nastavíte počet dostupných jednotek. Pro edici B1, B2, S1 nebo S2 můžete zakoupit až 200 jednotek nebo až 10 jednotek pro edici B3 nebo S3. Po vytvoření služby IoT hub můžete změnit počet jednotek dostupných v jeho edici, upgradovat nebo downgrade mezi edicemi v rámci jeho úrovně (B1 až B2) nebo upgradovat ze základní na standardní úroveň (B1 na S1) bez přerušení stávajících operací. Další informace najdete v tématu [Jak upgradovat centrum IoT hub](iot-hub-upgrade.md).  

Jako příklad možností provozu jednotlivých úrovní se zprávy mezi zařízeními a cloudem řídí těmito trvalými pokyny pro propustnost:

| Edice úrovně | Trvalá propustnost | Míra trvalého odesílání |
| --- | --- | --- |
| B1, S1 |Až 1111 KB/min na jednotku<br/>(1,5 GB/den/jednotka) |Průměr 278 zpráv za minutu na jednotku<br/>(400 000 zpráv za den na jednotku) |
| B2, S2 |Až 16 MB/min na jednotku<br/>(22,8 GB/den/jednotka) |Průměr 4 167 zpráv za minutu na jednotku<br/>(6 milionů zpráv za den na jednotku) |
| B3, S3 |Až 814 MB/min na jednotku<br/>(1144,4 GB/den/jednotka) |Průměr 208 333 zpráv za minutu na jednotku<br/>(300 milionů zpráv za den na jednotku) |

Propustnost zařízení cloud je jenom jedna z metrik, které je třeba zvážit při navrhování řešení IoT. Podrobnější informace naleznete v tématu [Kvóty a omezení centra IoT Hub](iot-hub-devguide-quotas-throttling.md).

### <a name="identity-registry-operation-throughput"></a>Propustnost operace registru identity

Operace registru identit služby IoT Hub nemají být operace za běhu, protože většinou souvisejí s zřizováním zařízení.

Konkrétní čísla výkonu burst, viz [Kvóty a omezení služby IoT Hub](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Automatické škálování

Pokud se blížíte povolenému limitu zpráv ve službě IoT hub, můžete pomocí těchto [kroků automaticky škálovat](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) tak, aby se jednotka služby IoT Hub na stejné úrovni služby IoT Hub zvětšila.

## <a name="next-steps"></a>Další kroky

* Další informace o možnostech služby IoT Hub a podrobnosti o výkonu najdete v [tématu ioT Hub stanovení cen](https://azure.microsoft.com/pricing/details/iot-hub) nebo [kvóty a omezení služby IoT Hub](iot-hub-devguide-quotas-throttling.md).

* Pokud chcete změnit úroveň iot hubu, postupujte podle pokynů v [části Upgrade centra IoT hub](iot-hub-upgrade.md).
