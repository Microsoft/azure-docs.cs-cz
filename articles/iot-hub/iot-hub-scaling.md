---
title: Škálování Azure IoT Hub | Microsoft Docs
description: Postup škálování služby IoT hub pro podporu propustnost předpokládaného zpráv a požadované funkce. Obsahuje souhrn podporovaných propustnosti pro každou vrstvu a možnosti pro horizontálního dělení.
services: iot-hub
documentationcenter: ''
author: kgremban
manager: timlt
editor: ''
ms.assetid: e7bd4968-db46-46cf-865d-9c944f683832
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2018
ms.author: kgremban
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01e6c3a6fb922a649f0ae139af9c8515fcb8b2e0
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Vyberte správné úroveň služby IoT Hub pro vaše řešení

Každé řešení IoT se liší, takže Azure IoT Hub nabízí několik možností na základě ceny a škálování. Tento článek slouží k vám pomoct vyzkoušet potřeb IoT Hub. Informace o úrovních služby IoT Hub o cenách naleznete [IoT Hub ceny](https://azure.microsoft.com/pricing/details/iot-hub). 

Rozhodněte, které vrstvě služby IoT Hub je nejvhodnější pro vaše řešení, položte si dvě otázky:

**Jaké funkce plánu použít?**
Azure IoT Hub nabízí dvě úrovně, basic a standard, které se liší v řadu funkcí, které podporují. Pokud je vaše řešení IoT založená kolem shromažďování dat ze zařízení a centrálně ho analýza úroveň basic je pravděpodobně pro vás nejvhodnější. Pokud chcete použít pokročilejší konfigurace pro vzdálené řízení zařízení IoT nebo distribuovat některé z vašich úloh do zařízení sami měli byste zvážit na plán úrovně standard. Pro podrobné rozdělení, které funkce jsou zahrnuté v jednotlivých vrstvách nadále [úrovně Basic a standard](#basic-and-standard-tiers).

**Kolik dat je plánu každý den přesunout?**
Každá úroveň služby IoT Hub je k dispozici ve třech úrovních, na základě kolem kolik propustnost dat dokáže zpracovat jakýkoli den. Tyto velikosti jsou číselné hodnoty označeny jako 1, 2 a 3. Jednotlivé jednotky IoT hub úroveň 1 například může zpracovávat 400 tisíc zpráv denně, zatímco jednotky úroveň 3 dokáže zpracovat miliony 300. Další podrobnosti o pokyny data nadále [propustnost zpráv](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Úrovně Basic a standard

Centra IoT na plán úrovně standard umožňuje všechny funkce a je vyžadovaný pro jakékoli řešení IoT, které mají být pomocí možnosti jednosměrnou komunikaci. Základní úroveň umožňuje podmnožinu funkcí a je určena pro řešení IoT, které stačí jednosměrnou komunikaci ze zařízení do cloudu. Obou vrstvách nabízí stejné funkce zabezpečení a ověřování.

Po vytvoření služby IoT hub můžete upgradovat ze základní vrstvě do vrstvy se standardní bez přerušení existující operace. Další informace najdete v tématu [postup upgradu služby IoT hub](iot-hub-upgrade.md).

| Schopnost | Úroveň Basic | Úroveň Standard |
| ---------- | ---------- | ------------- |
| [Telemetrie zařízení cloud](iot-hub-devguide-messaging.md) | Ano | Ano |
| [Identita na zařízení.](iot-hub-devguide-identity-registry.md) | Ano | Ano |
| [Směrování zpráv](iot-hub-devguide-messages-read-custom.md) a [integrace událostí mřížky](iot-hub-event-grid.md) | Ano | Ano |
| [Protokoly HTTP, AMQP a MQTT](iot-hub-devguide-protocols.md) | Ano | Ano |
| [Zřizování služby zařízení](../iot-dps/about-iot-dps.md) | Ano | Ano |
| [Monitorování a Diagnostika](iot-hub-monitor-resource-health.md) | Ano | Ano |
| [Zasílání zpráv typu cloud zařízení](iot-hub-devguide-c2d-guidance.md) |   | Ano |
| [Dvojčata zařízení](iot-hub-devguide-device-twins.md), [modulu dvojčata](iot-hub-devguide-module-twins.md) a [Správa zařízení](iot-hub-device-management-overview.md) |   | Ano |
| [Azure IoT Edge](../iot-edge/how-iot-edge-works.md) |   | Ano |

Centrum IoT také nabízí volné vrstvy, která je určená pro testování a vyhodnocení. Obsahuje všechny funkce úrovně standard, ale omezená přídavky zasílání zpráv. Nelze upgradovat z úroveň free na basic nebo standard. 

### <a name="iot-hub-rest-apis"></a>Rozhraní REST API Centra IoT

Podporované schopnosti rozdíl mezi úrovně basic a standard IoT Hub znamená, že některé volání rozhraní API není práci s rozbočovači základní vrstvě. V následující tabulce jsou uvedeny API, které jsou k dispozici: 

| Rozhraní API | Úroveň Basic | Úroveň Standard |
| --- | ---------- | ------------- |
| [Odstranění zařízení](https://docs.microsoft.com/rest/api/iothub/deviceapi/deletedevice) | Ano | Ano |
| [Získání zařízení](https://docs.microsoft.com/rest/api/iothub/deviceapi/getdevice) | Ano | Ano |
| Odstranit modul | Ano | Ano |
| Získat modul | Ano | Ano |
| [Získat statistiku registru](https://docs.microsoft.com/rest/api/iothub/deviceapi/getregistrystatistics) | Ano | Ano |
| [Získat statistiku služeb](https://docs.microsoft.com/rest/api/iothub/deviceapi/getservicestatistics) | Ano | Ano |
| [Umístit zařízení](https://docs.microsoft.com/rest/api/iothub/deviceapi/putdevice) | Ano | Ano |
| Vložení modulu | Ano | Ano |
| [Dotaz zařízení](https://docs.microsoft.com/rest/api/iothub/deviceapi/querydevices) | Ano | Ano |
| Moduly dotazu | Ano | Ano |
| [Vytvoření nahrávání souborů identifikátor URI pro SAS](https://docs.microsoft.com/rest/api/iothub/httpruntime/createfileuploadsasuri) | Ano | Ano |
| [Vázaný oznámení](https://docs.microsoft.com/rest/api/iothub/httpruntime/receivedeviceboundnotification) | Ano | Ano |
| [Odeslat událost zařízení](https://docs.microsoft.com/rest/api/iothub/httpruntime/senddeviceevent) | Ano | Ano |
| Odeslat událost modulu | Ano | Ano |
| [Aktualizovat stav nahrávání souborů](https://docs.microsoft.com/rest/api/iothub/httpruntime/updatefileuploadstatus) | Ano | Ano |
| [Operace hromadného zařízení](https://docs.microsoft.com/rest/api/iothub/deviceapi/bulkdeviceoperation) | Ano, s výjimkou možnosti IoT Edge | Ano | 
| [Vyprázdnění příkaz fronty](https://docs.microsoft.com/rest/api/iothub/deviceapi/purgecommandqueue) |   | Ano |
| [Získat dvojče zařízení](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/getdevicetwin) |   | Ano |
| Získat modul twin |   | Ano |
| [Volání metody zařízení](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/invokedevicemethod) |   | Ano |
| [Aktualizovat dvojče zařízení](https://docs.microsoft.com/rest/api/iothub/devicetwinapi/updatedevicetwin) |   | Ano | 
| Aktualizace modulu twin |   | Ano | 
| [Zrušte zařízení vázaný oznámení](https://docs.microsoft.com/rest/api/iothub/httpruntime/abandondeviceboundnotification) |   | Ano |
| [Dokončení zařízení vázaný oznámení](https://docs.microsoft.com/rest/api/iothub/httpruntime/completedeviceboundnotification) |   | Ano |
| [Zrušení úlohy](https://docs.microsoft.com/rest/api/iothub/jobapi/canceljob) |   | Ano |
| [Vytvoření úlohy](https://docs.microsoft.com/rest/api/iothub/jobapi/createjob) |   | Ano |
| [Získat úlohu](https://docs.microsoft.com/rest/api/iothub/jobapi/getjob) |   | Ano |
| [Dotaz úlohy](https://docs.microsoft.com/rest/api/iothub/jobapi/queryjobs) |   | Ano |

## <a name="message-throughput"></a>Propustnost zpráv

Nejlepší způsob, jak velikost do řešení IoT Hub je vyhodnotit provozu na základě za jednotku. Zejména zvažte propustnosti vyžaduje ve špičce operací v těchto kategoriích:

* Zprávy typu zařízení-cloud
* Zprávy typu cloud zařízení
* Operace registru identit

Provoz se měří na základě za jednotku, nikoli k rozbočovači. Úrovně 1 nebo 2 instance IoT Hub může mít až 200 jednotky s ním spojená. Úroveň 3 instance IoT Hub může mít až 10 jednotky. Po vytvoření služby IoT hub můžete změnit počet jednotek nebo přesunutí mezi 1, 2 a 3 velikosti v rámci konkrétní úroveň, aniž by to ovlivnilo vaše stávající operace. Další informace najdete v tématu [postup upgradu služby IoT Hub](iot-hub-upgrade.md).

Jako příklad možností provoz každé úrovni zpráv typu zařízení cloud, postupujte podle následujících pokynů propustnost:

| Vrstva | Propustnost | Udržovanou rychlost |
| --- | --- | --- |
| B1, S1 |Až 1111 KB za minutu za jednotku<br/>(1,5 GB/den/unit) |Průměr 278 zpráv za minutu za jednotku<br/>(400 000 zprávy dny a hodiny za jednotku) |
| B2, S2 |Až 16 MB za minutu za jednotku<br/>(22.8 GB/den/unit) |Průměr 4,167 zpráv za minutu za jednotku<br/>(6 milionu zprávy dny a hodiny za jednotku) |
| B3, S3 |Až 814 MB za minutu za jednotku<br/>(1144.4 GB/den/unit) |Průměr 208,333 zpráv za minutu za jednotku<br/>(300 milionů zprávy dny a hodiny za jednotku) |

Kromě těchto informací propustnost, najdete v části [IoT Hub kvóty a omezení] [ IoT Hub quotas and throttles] a navrhněte řešení odpovídajícím způsobem.

### <a name="identity-registry-operation-throughput"></a>Propustnost operaci registru identit
Operace s registrem identit služby IoT Hub není by mělo být spuštění operace, jako se většinou vztahují k zřizování zařízení.

Pro konkrétní shluků čísla výkonu, najdete v části [IoT Hub kvóty a omezení][IoT Hub quotas and throttles].

## <a name="sharding"></a>Sharding
Během jedné IoT hub lze škálovat na miliony zařízení, někdy řešení vyžaduje konkrétní výkonové charakteristiky, které nemůže zaručit jeden IoT hub. V takovém případě můžete oddílu zařízení napříč více centra IoT. Více centra IoT funkce smooth shluky provoz a získat požadovaná propustnost nebo sazby operace, které jsou požadovány.

## <a name="next-steps"></a>Další postup

* Další informace o možnostech IoT Hub a podrobnosti o výkonu, najdete v tématu [IoT Hub ceny] [odkaz ceny] nebo [IoT Hub kvóty a omezení][IoT Hub quotas and throttles].
* Chcete-li změnit váš vrstvy služby IoT Hub, postupujte podle kroků v [upgradu služby IoT hub](iot-hub-upgrade.md).

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[IoT Hub quotas and throttles]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
