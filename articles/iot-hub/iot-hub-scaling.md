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
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: Operations'
ms.openlocfilehash: 725fc422bc11a19bebcbb8895cc0a9bedd58afed
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91577760"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Volba vhodné vrstvy služby IoT Hub pro vaše řešení

Každé řešení IoT se liší, takže Azure IoT Hub nabízí několik možností na základě cen a škálování. Tento článek vám může pomáhat při vyhodnocování vašich IoT Hubch potřeb. Informace o cenách IoT Hub úrovní najdete v tématu [IoT Hub ceny](https://azure.microsoft.com/pricing/details/iot-hub).

Pokud chcete určit, která IoT Hub úroveň pro vaše řešení je nejvhodnější, položte si dva dotazy:

**Jaké funkce mám v plánu použít?**

Azure IoT Hub nabízí dvě úrovně Basic a Standard, které se liší podle počtu funkcí, které podporují. Pokud je vaše řešení IoT založené na shromažďování dat ze zařízení a jejich centrální analýze, je pro vás pravděpodobně nejvhodnější úroveň Basic. Pokud chcete použít pokročilejší konfigurace pro vzdálenou kontrolu zařízení IoT nebo jejich distribuci do samotných zařízení, měli byste zvážit úroveň Standard. Podrobný rozpis funkcí zahrnutých do jednotlivých vrstev pokračuje v [úrovni Basic a Standard](#basic-and-standard-tiers).

**Kolik dat mám naplánovat, aby se každý den přesunul?**

Každá IoT Hubová vrstva je dostupná ve třech velikostech, a to na základě toho, kolik propustnosti dat může v daném dni zvládnout. Tyto velikosti se číslně identifikují jako 1, 2 a 3. Například každá jednotka centra IoT úrovně 1 může zpracovávat 400 000 zpráv denně, zatímco jednotka úrovně 3 může zpracovávat 300 000 000. Další podrobnosti o pravidlech pro data můžete pokračovat v [propustnosti zpráv](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Úrovně Basic a Standard

Úroveň Standard IoT Hub povoluje všechny funkce a je vyžadována pro všechna řešení IoT, která chtějí využívat možnosti obousměrné komunikace. Základní úroveň nabízí jen podmnožinu funkcí. Je určená pro řešení IoT, která potřebují jenom jednosměrnou komunikaci ze zařízení do cloudu. Obě úrovně nabízejí stejné bezpečnostní a ověřovací funkce.

Pro jednu IoT Hub lze zvolit pouze jeden typ [edice](https://azure.microsoft.com/pricing/details/iot-hub/) v rámci vrstvy. Můžete například vytvořit IoT Hub s více jednotkami S1, ale ne se směsí jednotek z různých edic, například S1 a S2.

| Schopnost | Základní úroveň | Úroveň Free/Standard |
| ---------- | ---------- | ------------- |
| [Telemetrie ze zařízení do cloudu](iot-hub-devguide-messaging.md) | Ano | Ano |
| [Identita vázaná na zařízení](iot-hub-devguide-identity-registry.md) | Ano | Ano |
| [Směrování zpráv](iot-hub-devguide-messages-read-custom.md), [rozšiřování zpráv](iot-hub-message-enrichments-overview.md)a [integrace Event Grid](iot-hub-event-grid.md) | Ano | Ano |
| [Protokoly HTTP, AMQP a MQTT](iot-hub-devguide-protocols.md) | Ano | Ano |
| [Device Provisioning Service](../iot-dps/about-iot-dps.md) | Ano | Ano |
| [Monitorování a diagnostika](iot-hub-monitor-resource-health.md) | Ano | Ano |
| [Zasílání zpráv z cloudu na zařízení](iot-hub-devguide-c2d-guidance.md) |   | Ano |
| [Vlákna zařízení](iot-hub-devguide-device-twins.md), [vlákna modulu](iot-hub-devguide-module-twins.md)a [Správa zařízení](iot-hub-device-management-overview.md) |   | Ano |
| [Datové proudy zařízení (Preview)](iot-hub-device-streams-overview.md) |   | Ano |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Ano |
| [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) |   | Ano |

IoT Hub také nabízí bezplatnou úroveň, která je určena pro testování a vyhodnocení. Má všechny možnosti úrovně Standard, ale omezené odchylky pro zasílání zpráv. Nemůžete upgradovat z úrovně Free na Basic nebo Standard.

## <a name="partitions"></a>Oddíly

Centra IoT Azure obsahují mnoho základních součástí [Azure Event Hubs](../event-hubs/event-hubs-features.md), včetně [oddílů](../event-hubs/event-hubs-features.md#partitions). Datové proudy událostí pro centra IoT jsou obvykle vyplněny příchozími daty telemetrie, která jsou uvedena v různých zařízeních IoT. Rozdělení datového proudu událostí se používá ke snížení kolizí, ke kterým dochází při současném čtení a zápisu do datových proudů událostí.

Limit oddílu se zvolí při vytvoření IoT Hub a nedá se změnit. Maximální omezení oddílu IoT Hub úrovně Basic a úrovně Standard IoT Hub je 32. Většina rozbočovačů IoT potřebuje jenom 4 oddíly. Další informace o tom, jak určit oddíly, najdete v Event Hubs Nejčastější dotazy, [kolik oddílů](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need) potřebuji?

## <a name="tier-upgrade"></a>Upgrade vrstvy

Po vytvoření služby IoT Hub můžete upgradovat z úrovně Basic na úroveň Standard, aniž by došlo k přerušení stávajících operací. Další informace najdete v tématu [Postup upgradu služby IoT Hub](iot-hub-upgrade.md).

Pokud migrujete z úrovně Basic na úroveň Standard, zůstane konfigurace oddílu beze změny.

> [!NOTE]
> Úroveň Free nepodporuje upgrade na Basic nebo Standard.

## <a name="iot-hub-rest-apis"></a>Rozhraní REST API pro IoT Hub

Rozdíl v podporovaných možnostech mezi úrovněmi Basic a Standard IoT Hub znamená, že některá volání rozhraní API nefungují s centry na úrovni Basic. Následující tabulka uvádí, která rozhraní API jsou k dispozici:

| rozhraní API | Základní úroveň | Úroveň Free/Standard |
| --- | ---------- | ------------- |
| [Odstranit zařízení](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#deletedevice-string--models-registrymanagerdeletedeviceoptionalparams-) | Ano | Ano |
| [Získat zařízení](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-getdevice) | Ano | Ano |
| [Odstranit modul](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletemodule) | Ano | Ano |
| [Získat modul](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.registrymanager.getmodule?view=azure-java-stable) | Ano | Ano |
| [Získat statistiku registru](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#getdevicestatistics-msrest-requestoptionsbase-) | Ano | Ano |
| [Získat statistiku služeb](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#getservicestatistics-msrest-requestoptionsbase-) | Ano | Ano |
| [Vytvořit nebo aktualizovat zařízení](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#createorupdatedevice-string--device--servicecallback-device--) | Ano | Ano |
| [Vytvořit nebo aktualizovat modul](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#createorupdatemodule-string--string--module--models-registrymanagercreateorupdatemoduleoptionalparams-) | Ano | Ano |
| [IoT Hub dotazů](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.registrymanager?view=azure-dotnet) | Ano | Ano |
| [Vytvořit soubor SAS SAS pro nahrání souboru](https://docs.microsoft.com/rest/api/iothub/device/createfileuploadsasuri) | Ano | Ano |
| [Přijmout oznámení vázané na zařízení](https://docs.microsoft.com/rest/api/iothub/device/receivedeviceboundnotification) | Ano | Ano |
| [Událost odeslání zařízení](https://docs.microsoft.com/rest/api/iothub/device/senddeviceevent) | Ano | Ano |
| Událost odeslání modulu | Jenom AMQP a MQTT | Jenom AMQP a MQTT |
| [Aktualizovat stav nahrávání souboru](https://docs.microsoft.com/rest/api/iothub/device/updatefileuploadstatus) | Ano | Ano |
| [Operace hromadného zařízení](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#bulkdevicecrud-exportimportdevice----msrest-requestoptionsbase-) | Ano, s výjimkou možností IoT Edge | Ano |
| [Zrušit import úlohy exportu](https://docs.microsoft.com/rest/api/iothub/service/jobs/cancelimportexportjob) | Ano | Ano |
| [Vytvořit úlohu exportu importu](https://docs.microsoft.com/rest/api/iothub/service/jobs/createimportexportjob) | Ano | Ano |
| [Získat úlohu exportu importu](https://docs.microsoft.com/rest/api/iothub/service/jobs/getimportexportjob) | Ano | Ano |
| [Získat úlohy exportu importu](https://docs.microsoft.com/rest/api/iothub/service/jobs/getimportexportjobs) | Ano | Ano |
| [Vyprázdnit frontu příkazů](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/registrymanager?view=azure-node-latest#purgecommandqueue-string--msrest-requestoptionsbase-) |   | Ano |
| [Získat dvojitou dvojici zařízení](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin?view=azure-java-stable) |   | Ano |
| [Získat nevlákenný modul](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-getmoduletwin) |   | Ano |
| [Vyvolat metodu zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-direct-methods) |   | Ano |
| [Aktualizovat dvojitou dvojici zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins) |   | Ano |
| [Aktualizovat modul na vlákna](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-updatemoduletwin) |   | Ano |
| [Opustit oznámení vázané na zařízení](https://docs.microsoft.com/rest/api/iothub/device/abandondeviceboundnotification) |   | Ano |
| [Dokončit oznámení vázané na zařízení](https://docs.microsoft.com/rest/api/iothub/device/completedeviceboundnotification) |   | Ano |
| [Zrušit úlohu](https://docs.microsoft.com/rest/api/media/jobs/canceljob) |   | Ano |
| [Vytvoření úlohy](https://docs.microsoft.com/rest/api/media/jobs/create) |   | Ano |
| [Získat úlohu](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.service.jobs.jobclient.getjob?view=azure-java-stable) |   | Ano |
| [Dotazy na úlohy](https://docs.microsoft.com/javascript/api/azure-iot-digitaltwins-service/jobclient?view=azure-node-latest#queryjobs-jobclientqueryjobsoptionalparams--servicecallback-queryresult--) |   | Ano |

## <a name="message-throughput"></a>Propustnost zprávy

Nejlepším způsobem, jak velikost IoT Hub řešení, je vyhodnotit provoz na základě jednotlivých jednotek. Zvažte zejména požadovanou propustnost ve špičce pro následující kategorie operací:

* Zprávy typu zařízení-cloud
* Zprávy z cloudu na zařízení
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
