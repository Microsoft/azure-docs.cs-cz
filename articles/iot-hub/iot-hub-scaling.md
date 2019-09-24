---
title: Škálování služby Azure IoT Hub | Microsoft Docs
description: Jak škálovat centrum IoT tak, aby podporovalo očekávanou propustnost zpráv a požadované funkce. Obsahuje souhrn podporované propustnosti pro každou úroveň a možnosti pro horizontálního dělení.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.openlocfilehash: 8d7bb201a9d01725f933105a4a0beb85c82ca105
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203712"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Volba správné IoT Hub úrovně pro vaše řešení

Každé řešení IoT se liší, takže Azure IoT Hub nabízí několik možností na základě cen a škálování. Tento článek vám může pomáhat při vyhodnocování vašich IoT Hubch potřeb. Informace o cenách IoT Hub úrovní najdete v tématu [IoT Hub ceny](https://azure.microsoft.com/pricing/details/iot-hub).

Pokud chcete určit, která IoT Hub úroveň pro vaše řešení je nejvhodnější, položte si dva dotazy:

**Jaké funkce mám v plánu použít?**

Azure IoT Hub nabízí dvě úrovně Basic a Standard, které se liší podle počtu funkcí, které podporují. Pokud je vaše řešení IoT založené na shromažďování dat ze zařízení a jejich centrální analýze, je pro vás pravděpodobně nejvhodnější úroveň Basic. Pokud chcete použít pokročilejší konfigurace pro vzdálenou kontrolu zařízení IoT nebo jejich distribuci do samotných zařízení, měli byste zvážit úroveň Standard. Podrobný rozpis funkcí zahrnutých do jednotlivých vrstev pokračuje v [úrovni Basic a Standard](#basic-and-standard-tiers).

**Kolik dat mám naplánovat, aby se každý den přesunul?**

Každá IoT Hubová vrstva je dostupná ve třech velikostech, a to na základě toho, kolik propustnosti dat může v daném dni zvládnout. Tyto velikosti se číslně identifikují jako 1, 2 a 3. Například každá jednotka centra IoT úrovně 1 může zpracovávat 400 000 zpráv denně, zatímco jednotka úrovně 3 může zpracovávat 300 000 000. Další podrobnosti o pravidlech pro data můžete pokračovat v [propustnosti zpráv](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Úrovně Basic a Standard

Úroveň Standard IoT Hub povoluje všechny funkce a je vyžadována pro všechna řešení IoT, která chtějí využívat možnosti obousměrné komunikace. Úroveň Basic umožňuje podmnožinu funkcí a je určena pro řešení IoT, která potřebují pouze jednosměrnou komunikaci ze zařízení do cloudu. Obě úrovně nabízejí stejné funkce zabezpečení a ověřování.

Pro jednu IoT Hub lze zvolit pouze jeden typ [edice](https://azure.microsoft.com/pricing/details/iot-hub/) v rámci vrstvy. Můžete například vytvořit IoT Hub s více jednotkami S1, ale ne se směsí jednotek z různých edic, například S1 a S2.

| Funkce | Úroveň Basic | Úroveň Free/Standard |
| ---------- | ---------- | ------------- |
| [Telemetrie ze zařízení do cloudu](iot-hub-devguide-messaging.md) | Ano | Ano |
| [Identita vázaná na zařízení](iot-hub-devguide-identity-registry.md) | Ano | Ano |
| [Směrování zpráv](iot-hub-devguide-messages-read-custom.md) a [Event Grid integrace](iot-hub-event-grid.md) | Ano | Ano |
| [Protokoly HTTP, AMQP a MQTT](iot-hub-devguide-protocols.md) | Ano | Ano |
| [Služba Device Provisioning](../iot-dps/about-iot-dps.md) | Ano | Ano |
| [Monitorování a diagnostika](iot-hub-monitor-resource-health.md) | Ano | Ano |
| [Zasílání zpráv z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md) |   | Ano |
| [Vlákna zařízení](iot-hub-devguide-device-twins.md), [vlákna modulu](iot-hub-devguide-module-twins.md)a [Správa zařízení](iot-hub-device-management-overview.md) |   | Ano |
| [Datové proudy zařízení (Preview)](iot-hub-device-streams-overview.md) |   | Ano |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Ano |
| [technologie Plug and Play IoT Preview](../iot-pnp/overview-iot-plug-and-play.md) |   | Ano |

IoT Hub také nabízí bezplatnou úroveň, která je určena pro testování a vyhodnocení. Má všechny možnosti úrovně Standard, ale omezené odchylky pro zasílání zpráv. Nemůžete upgradovat z úrovně Free na Basic nebo Standard.

## <a name="partitions"></a>Oddíly

Centra IoT Azure obsahují mnoho základních součástí [Azure Event Hubs](../event-hubs/event-hubs-features.md), včetně [oddílů](../event-hubs/event-hubs-features.md#partitions). Datové proudy událostí pro centra IoT jsou obvykle vyplněny příchozími daty telemetrie, která jsou uvedena v různých zařízeních IoT. Rozdělení datového proudu událostí se používá ke snížení kolizí, ke kterým dochází při současném čtení a zápisu do datových proudů událostí.

Limit oddílu se zvolí při vytvoření IoT Hub a nedá se změnit. Maximální omezení oddílu IoT Hub úrovně Basic a úrovně Standard IoT Hub je 32. Většina rozbočovačů IoT potřebuje jenom 4 oddíly. Další informace o tom, jak určit oddíly, najdete v Event Hubs Nejčastější dotazy, [kolik oddílů](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need) potřebuji?

## <a name="tier-upgrade"></a>Upgrade vrstvy

Po vytvoření služby IoT Hub můžete upgradovat z úrovně Basic na úroveň Standard, aniž by došlo k přerušení stávajících operací. Další informace najdete v tématu [Postup upgradu služby IoT Hub](iot-hub-upgrade.md).

Pokud migrujete z úrovně Basic na úroveň Standard, zůstane konfigurace oddílu beze změny.

> [!NOTE]
> Úroveň Free nepodporuje upgrade na Basic nebo Standard.

## <a name="iot-hub-rest-apis"></a>Rozhraní REST API Centra IoT

Rozdíl v podporovaných možnostech mezi úrovněmi Basic a Standard IoT Hub znamená, že některá volání rozhraní API nefungují s centry na úrovni Basic. Následující tabulka uvádí, která rozhraní API jsou k dispozici:

| rozhraní API | Úroveň Basic | Úroveň Free/Standard |
| --- | ---------- | ------------- |
| [Odstranit zařízení](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | Ano | Ano |
| [Získat zařízení](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | Ano | Ano |
| [Odstranit modul](https://docs.microsoft.com/rest/api/iothub/service/deletemodule) | Ano | Ano |
| [Získat modul](https://docs.microsoft.com/rest/api/iothub/service/getmodule) | Ano | Ano |
| [Získat statistiku registru](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | Ano | Ano |
| [Získat statistiku služeb](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | Ano | Ano |
| [Vytvořit nebo aktualizovat zařízení](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | Ano | Ano |
| [Vytvořit nebo aktualizovat modul](https://docs.microsoft.com/rest/api/iothub/service/createorupdatemodule) | Ano | Ano |
| [IoT Hub dotazů](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | Ano | Ano |
| [Vytvořit soubor SAS SAS pro nahrání souboru](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Ano | Ano |
| [Přijmout oznámení vázané na zařízení](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Ano | Ano |
| [Událost odeslání zařízení](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Ano | Ano |
| Událost odeslání modulu | Jenom AMQP a MQTT | Jenom AMQP a MQTT |
| [Aktualizovat stav nahrávání souboru](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Ano | Ano |
| [Operace hromadného zařízení](https://docs.microsoft.com/rest/api/iothub/service/bulkcreateorupdatedevices) | Ano, s výjimkou možností IoT Edge | Ano |
| [Zrušit import úlohy exportu](https://docs.microsoft.com/rest/api/iothub/service/cancelimportexportjob) | Ano | Ano |
| [Vytvořit úlohu exportu importu](https://docs.microsoft.com/rest/api/iothub/service/createimportexportjob) | Ano | Ano |
| [Získat úlohu exportu importu](https://docs.microsoft.com/rest/api/iothub/service/getimportexportjob) | Ano | Ano |
| [Získat úlohy exportu importu](https://docs.microsoft.com/rest/api/iothub/service/getimportexportjobs) | Ano | Ano |
| [Vyprázdnit frontu příkazů](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | Ano |
| [Získat dvojitou dvojici zařízení](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | Ano |
| [Získat nevlákenný modul](https://docs.microsoft.com/rest/api/iothub/service/getmoduletwin) |   | Ano |
| [Vyvolat metodu zařízení](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | Ano |
| [Aktualizovat dvojitou dvojici zařízení](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | Ano |
| [Aktualizovat modul na vlákna](https://docs.microsoft.com/rest/api/iothub/service/updatemoduletwin) |   | Ano |
| [Opustit oznámení vázané na zařízení](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Ano |
| [Dokončit oznámení vázané na zařízení](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Ano |
| [Zrušit úlohu](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | Ano |
| [Vytvořit úlohu](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | Ano |
| [Získat úlohu](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | Ano |
| [Dotazy na úlohy](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | Ano |

## <a name="message-throughput"></a>Propustnost zprávy

Nejlepším způsobem, jak velikost IoT Hub řešení, je vyhodnotit provoz na základě jednotlivých jednotek. Zvažte zejména požadovanou propustnost ve špičce pro následující kategorie operací:

* Zprávy typu zařízení-cloud
* Zprávy typu cloud zařízení
* Operace registru identit

Provoz se měří pro Centrum IoT na jednotlivých jednotkách. Když vytváříte centrum IoT, zvolíte jeho úroveň a edici a nastavíte počet dostupných jednotek. Pro edici B1, B2, S1 nebo S2 můžete zakoupit až 200 jednotek a až 10 jednotek pro edici B3 nebo S3. Po vytvoření centra IoT můžete změnit počet jednotek dostupných v rámci své edice, upgradovat nebo downgradovat mezi edicemi v rámci své vrstvy (B1 až B2) nebo upgradovat z úrovně Basic na úroveň Standard (B1 až S1), aniž by došlo k přerušení stávajících operací. Další informace najdete v tématu [Postup upgradu služby IoT Hub](iot-hub-upgrade.md).  

Jako příklad provozu jednotlivých vrstev se zprávy typu zařízení-Cloud řídí těmito zásadami trvalé propustnosti:

| Úroveň edice | Přetrvávající propustnost | Míra trvalého odeslání |
| --- | --- | --- |
| B1, S1 |Až 1111 KB za minutu na jednotku<br/>(1,5 GB za den/jednotku) |Průměr zpráv 278 za minutu na jednotku<br/>(400 000 zpráv za den na jednotku) |
| B2, S2 |Až 16 MB za minutu na jednotku<br/>(22,8 GB za den/jednotku) |Průměr zpráv 4 167 za minutu na jednotku<br/>(6 000 000 zpráv za den na jednotku) |
| B3, S3 |Až 814 MB za minutu na jednotku<br/>(1144,4 GB za den/jednotku) |Průměr zpráv 208 333 za minutu na jednotku<br/>(300 000 000 zpráv za den na jednotku) |

Propustnost typu zařízení-Cloud je jenom jedna z metrik, kterou potřebujete vzít v úvahu při navrhování řešení IoT. Komplexnější informace najdete v tématu [IoT Hub kvót a omezení](iot-hub-devguide-quotas-throttling.md).

### <a name="identity-registry-operation-throughput"></a>Propustnost operací v registru identit

IoT Hub operací registru identity by neměly být operace za běhu, protože se většinou týkají zřizování zařízení.

Konkrétní čísla výkonu pro nárůst zatížení najdete v tématu [IoT Hub kvóty a omezení](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Automatické škálování

Pokud se ve službě IoT Hub blížíte povolenému limitu zpráv, můžete pomocí těchto [kroků automaticky škálovat](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) IoT Hub jednotku ve stejné IoT Hub vrstvě.

## <a name="next-steps"></a>Další kroky

* Další informace o funkcích IoT Hub a podrobnostech o výkonu najdete v článku [IoT Hub ceny](https://azure.microsoft.com/pricing/details/iot-hub) nebo [IoT Hub kvóty a omezení](iot-hub-devguide-quotas-throttling.md).

* Pokud chcete změnit úroveň IoT Hub, postupujte podle kroků v části [upgrade služby IoT Hub](iot-hub-upgrade.md).
