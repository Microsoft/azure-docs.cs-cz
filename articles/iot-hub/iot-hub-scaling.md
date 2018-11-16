---
title: Škálování služby Azure IoT Hub | Dokumentace Microsoftu
description: Jak škálovat službu IoT hub pro podporu propustnost očekávané zpráv a požadované funkce. Obsahuje souhrn podporovaných propustnosti pro každou vrstvu a možnosti pro horizontální dělení.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2018
ms.author: wesmc
ms.openlocfilehash: c37492a42322ffc386751c4c63b981c9d93a72f6
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633372"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Zvolte správné úrovně služby IoT Hub pro vaše řešení

Každé řešení IoT se liší, takže Azure IoT Hub nabízí několik možností, jak na základě ceny a škálování. Účelem tohoto článku je vám pomůže zhodnotit vaše potřeby IoT Hub. Informace o úrovních služby IoT Hub o cenách najdete v tématu [ceny služby IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub). 

Chcete-li rozhodnout, jaké úrovně služby IoT Hub je vhodné pro vaše řešení, položte si otázku: dvě otázky:

**Jaké funkce plánu použít?**
Azure IoT Hub nabízí dvě úrovně, basic a standard, které se liší v počtu funkcí, které podporují. Je-li týkající se shromažďování dat ze zařízení a analýza centrálně řešení IoT, úroveň basic je pravděpodobně pro vás ta pravá. Pokud chcete použít rozšířené konfigurace pro vzdálené řízení zařízení IoT nebo distribuci některé z vašich úloh do samotných zařízení, měli byste zvážit úroveň standard. Které funkce jsou zahrnuté v jednotlivých vrstvách podrobný rozpis dál [úrovně Basic a standard](#basic-and-standard-tiers).

**Kolik dat je naplánovat přesunout každý den?**
Každé úrovně služby IoT Hub je k dispozici ve třech velikostech, na základě po tom, kolik propustnost dat dokáže zpracovat každý den. Tyto velikosti jsou číselně označeny jako 1, 2 a 3. Každá jednotka služby IoT hub úrovně 1 například zvládne 400 tisíc zpráv denně, zatímco úroveň 3 jednotky zvládne 300 milionů. Podrobné informace o pokynech data i nadále [propustnost zpráv](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Úrovně Basic a standard

Povolí všechny funkce na úrovni standard služby IoT Hub a je vyžadováno pro jakékoli řešení IoT, které mají být využívají možnosti obousměrnou komunikaci. Úroveň basic umožňuje podmnožinu funkcí a je určena pro řešení IoT, které potřebují pouze jednosměrnou komunikaci ze zařízení do cloudu. Obě úrovně nabízejí stejné funkce zabezpečení a ověřování.

Pouze jeden typ [edition](https://azure.microsoft.com/pricing/details/iot-hub/) v rámci úrovně je možné zvolit jednotlivé služby IoT Hub. Můžete například vytvořit IoT Hub s více jednotek úrovně S1, ale ne s kombinaci jednotek z různých edicích, jako je například S1 a K3 nebo S1 a S2.

| Schopnost | Úroveň Basic | Úroveň Standard |
| ---------- | ---------- | ------------- |
| [Telemetrická data typu zařízení cloud](iot-hub-devguide-messaging.md) | Ano | Ano |
| [Identita zařízení](iot-hub-devguide-identity-registry.md) | Ano | Ano |
| [Směrování zpráv](iot-hub-devguide-messages-read-custom.md) a [integraci služby Event Grid](iot-hub-event-grid.md) | Ano | Ano |
| [Protokoly HTTP a protokolu AMQP, MQTT](iot-hub-devguide-protocols.md) | Ano | Ano |
| [Služba Device Provisioning Service](../iot-dps/about-iot-dps.md) | Ano | Ano |
| [Monitorování a Diagnostika](iot-hub-monitor-resource-health.md) | Ano | Ano |
| [Zasílání zpráv typu cloud zařízení](iot-hub-devguide-c2d-guidance.md) |   | Ano |
| [Dvojčata zařízení](iot-hub-devguide-device-twins.md), [dvojčaty modulů](iot-hub-devguide-module-twins.md), a [správy zařízení](iot-hub-device-management-overview.md) |   | Ano |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Ano |

IoT Hub také nabízí úrovně free, která je určená pro testování a vyhodnocení. Obsahuje všechny funkce úrovně standard, ale omezené limity zasílání zpráv. Nelze upgradovat z úrovně free na basic nebo standard. 


## <a name="partitions"></a>Oddíly

Azure IoT hub obsahovat mnoho základních součástí [Azure Event Hubs](../event-hubs/event-hubs-features.md), včetně [oddíly](../event-hubs/event-hubs-features.md#partitions). Streamování událostí služby IoT hubs jsou obvykle vyplní příchozí telemetrická data, která je ohlášena různých zařízení IoT. Dělení datového proudu událostí se používá k omezení sporů, ke kterým dochází při souběžné čtení a zápis do datových proudů událostí. 

Omezení počtu oddílů se volí při vytvoření služby IoT Hub a nedá se změnit. Oddíl maximální limit pro IoT hub úrovně basic je 8 a pro úroveň standard maximum je 32. Většina centra IoT hub stačí jenom 4 oddíly. Další informace o určení oddíly, naleznete v tématu Nejčastější dotazy týkající se Event Hubs [počtu oddílů potřeba?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)


## <a name="tier-upgrade"></a>Upgrade úrovně

Po vytvoření služby IoT hub, můžete upgradovat z úrovně basic na úroveň standard bez přerušení existující operace. Další informace najdete v tématu [pokyny k upgradu služby IoT hub](iot-hub-upgrade.md).

Oddíl konfigurace zůstane beze změny, když migrujete z úrovně basic na úroveň standard.


## <a name="iot-hub-rest-apis"></a>Rozhraní REST API Centra IoT

Rozdíl v podporované možnosti mezi úrovněmi basic a standard služby IoT Hub znamená, že některá volání rozhraní API, nebudou fungovat s hubs úrovně basic. V následující tabulce jsou uvedeny API, které jsou k dispozici: 

| Rozhraní API | Úroveň Basic | Úroveň Standard |
| --- | ---------- | ------------- |
| [Odstranění zařízení](https://docs.microsoft.com/rest/api/iothub/service/deletedevice) | Ano | Ano |
| [Zařízení](https://docs.microsoft.com/rest/api/iothub/service/getdevice) | Ano | Ano |
| Odstranit modul | Ano | Ano |
| Získání modulu | Ano | Ano |
| [Získání statistiky registru](https://docs.microsoft.com/rest/api/iothub/service/getdeviceregistrystatistics) | Ano | Ano |
| [Získání statistiky služby](https://docs.microsoft.com/rest/api/iothub/service/getservicestatistics) | Ano | Ano |
| [Vytvořit nebo aktualizovat zařízení](https://docs.microsoft.com/rest/api/iothub/service/createorupdatedevice) | Ano | Ano |
| Vložit modul | Ano | Ano |
| [Dotazování služby IoT Hub](https://docs.microsoft.com/rest/api/iothub/service/queryiothub) | Ano | Ano |
| Moduly dotazu | Ano | Ano |
| [Vytvoření SAS URI pro nahrání souboru](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Ano | Ano |
| [Vázaný oznámení](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Ano | Ano |
| [Odeslat událost zařízení](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Ano | Ano |
| Odeslání události modulu | Ano | Ano |
| [Aktualizovat stav nahrávání souboru](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Ano | Ano |
| [Operace hromadného zařízení](https://docs.microsoft.com/rest/api/iot-dps/deviceenrollment/bulkoperation) | Ano, s výjimkou funkce IoT Edge | Ano | 
| [Vyprázdnit frontu příkazů](https://docs.microsoft.com/rest/api/iothub/service/purgecommandqueue) |   | Ano |
| [Získáte, dvojče zařízení](https://docs.microsoft.com/rest/api/iothub/service/gettwin) |   | Ano |
| Získáte, dvojče zařízení |   | Ano |
| [Vyvolat metodu zařízení](https://docs.microsoft.com/rest/api/iothub/service/invokedevicemethod) |   | Ano |
| [Aktualizovat dvojče zařízení](https://docs.microsoft.com/rest/api/iothub/service/updatetwin) |   | Ano | 
| Aktualizovat dvojče zařízení |   | Ano | 
| [Opustit vázán oznámení](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Ano |
| [Dokončení zařízení vázán oznámení](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Ano |
| [Zrušit úlohu](https://docs.microsoft.com/rest/api/iothub/service/canceljob) |   | Ano |
| [Vytvoření úlohy](https://docs.microsoft.com/rest/api/iothub/service/createjob) |   | Ano |
| [Získat úlohu](https://docs.microsoft.com/rest/api/iothub/service/getjob) |   | Ano |
| [Dotaz úlohy](https://docs.microsoft.com/rest/api/iothub/service/queryjobs) |   | Ano |

## <a name="message-throughput"></a>Propustnost zpráv

Nejlepší způsob, jak upravit velikost řešení IoT Hub je vyhodnotit provoz na základě na jednotku. Vezměte v úvahu zejména vyžaduje nejvyšší propustnost v těchto kategoriích operací:

* Zprávy typu zařízení-cloud
* Zprávy typu cloud zařízení
* Operace registru identit

Provoz se měří na základě na jednotku, nikoli pro rozbočovač. Instance služby IoT Hub úrovně 1 nebo 2 může mít až 200 jednotek s ním spojená. Instance služby IoT Hub úrovně 3 může mít až 10 jednotek. Po vytvoření služby IoT hub můžete změnit počet jednotek nebo přesouvat mezi 1, 2 a 3 velikosti v rámci konkrétní vrstvy, aniž by to ovlivnilo vaše existující operace. Další informace najdete v tématu [pokyny k upgradu služby IoT Hub](iot-hub-upgrade.md).

Jako příklad funkce provozu jednotlivých úrovních zprávy typu zařízení cloud, postupujte podle následujících pokynů stabilně propustnost:

| Úroveň | Propustnost | Míra odesílání zachovaného |
| --- | --- | --- |
| B1, S1 |Až 1111 KB za minutu za jednotku<br/>(1,5 GB/den/jednotku) |Průměr 278 zpráv za minutu za jednotku<br/>(400 000 zpráv/den na jednotku) |
| B2, S2 |Až 16 MB za minutu za jednotku<br/>(22.8 GB/den/jednotku) |Průměr 4,167 zpráv za minutu za jednotku<br/>(6 milionů zpráv/den na jednotku) |
| B3, S3 |Až 814 MB za minutu za jednotku<br/>(1144.4 GB/den/jednotku) |Průměr 208,333 zpráv za minutu za jednotku<br/>(300 milionů zpráv/den na jednotku) |

Kromě těchto informací o propustnosti najdete v článku [služby IoT Hub kvóty a omezení] [ IoT Hub quotas and throttles] a navrhněte řešení odpovídajícím způsobem.

### <a name="identity-registry-operation-throughput"></a>Propustnost operace registru identit
Operace registru identit služby IoT Hub se by neměl být za běhu operace, jako se většinou vztahují k zřizování zařízení.

Konkrétní burst výkonu čísla, naleznete v tématu [služby IoT Hub kvóty a omezení][IoT Hub quotas and throttles].

## <a name="auto-scale"></a>Automatické škálování
Pokud se blíží limitu povolených zpráv ve službě IoT Hub, můžete je použít [kroky k automatickému škálování](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) postupně jednotek IoT Hub v rámci stejné úrovně služby IoT Hub.

## <a name="sharding"></a>Sharding
Zatímco jedno centrum IoT můžete škálovat na miliony zařízení, někdy vaše řešení nevyžaduje konkrétní výkonové charakteristiky, které nemůže zaručit jedno centrum IoT. V takovém případě můžete dělit zařízení napříč několika IoT hubech. Několika IoT hubech vyhlazení nárůstem provozu a získávat požadované propustnosti nebo sazby za operace, které jsou požadovány.

## <a name="next-steps"></a>Další postup

* Další informace o možnostech služby IoT Hub a podrobnosti o výkonu najdete v tématu [ceny služby IoT Hub] [ lnk-pricing] nebo [služby IoT Hub kvóty a omezení] [ IoT Hub quotas and throttles].
* Chcete-li změnit vaší úrovně služby IoT Hub, postupujte podle kroků v [Upgrade služby IoT hub](iot-hub-upgrade.md).

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
