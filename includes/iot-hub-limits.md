---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224428"
---
The following table lists the limits associated with the different service tiers S1, S2, S3, and F1. For information about the cost of each *unit* in each tier, see [Azure IoT Hub pricing](https://azure.microsoft.com/pricing/details/iot-hub/).

| Prostředek | S1 Standard | S2 Standard | S3 Standard | F1 Free |
| --- | --- | --- | --- | --- |
| Počet zpráv za den |400 000 |6 000 000 |300 000 000 |8 000 |
| Maximální počet jednotek |200 |200 |10 |1\. místo |

> [!NOTE]
> If you anticipate using more than 200 units with an S1 or S2 tier hub or 10 units with an S3 tier hub, contact Microsoft Support.
> 
> 

The following table lists the limits that apply to IoT Hub resources.

| Prostředek | škálování |
| --- | --- |
| Maximální počet placených služeb IoT Hub na předplatné Azure |100 |
| Maximální počet bezplatných služeb IoT Hub na předplatné Azure |1\. místo |
| Maximum number of characters in a device ID | 128 |
| Maximální počet identit zařízení<br/> vrácených v jednom volání |1 000 |
| Maximální doba uchování zpráv typu zařízení-cloud ve službě IoT Hub |7 dní |
| Maximální velikost zprávy typu zařízení-cloud |256 kB |
| Maximální velikost dávky typu zařízení-cloud |AMQP and HTTP: 256 KB for the entire batch <br/>MQTT: 256 KB for each message |
| Maximální počet zpráv v dávce typu zařízení-cloud |500 |
| Maximální velikost zprávy typu cloud-zařízení |64 kB |
| Maximální hodnota TTL pro zprávy typu cloud-zařízení |2 dny |
| Maximální počet doručení zpráv typu <br/> cloud-zařízení |100 |
| Maximum cloud-to-device queue depth per device |50 |
| Maximální počet doručení zpráv se zpětnou vazbou <br/> v reakci na zprávu typu cloud-zařízení |100 |
| Maximální hodnota TTL pro zprávy se zpětnou vazbou <br/> v reakci na zprávu typu cloud-zařízení |2 dny |
| [Maximum size of device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB for tags section, and 32 KB for desired and reported properties sections each |
| Maximum length of device twin string key | 1 KB |
| Maximum length of device twin string value | 4 KB |
| [Maximum depth of object in device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Maximální velikost datové části přímé metody | 128 KB |
| Maximální doba uchování historie úlohy | 30 dní |
| Maximální počet souběžných úloh | 10 (pro S3), 5 (pro S2), 1 (pro S1) |
| Maximální počet dalších koncových bodů | 10 (for S1, S2, and S3) |
| Maximální počet pravidel směrování zpráv | 100 (for S1, S2, and S3) |
| Maximum number of concurrently connected device streams | 50 (for S1, S2, S3, and F1 only) |
| Maximum device stream data transfer | 300 MB per day (for S1, S2, S3, and F1 only) |

> [!NOTE]
> If you need more than 100 paid IoT hubs in an Azure subscription, contact Microsoft Support.

> [!NOTE]
> Currently, the total number of devices plus modules that can be registered to a single IoT hub is capped at 1,000,000. If you want to increase this limit, contact [Microsoft Support](https://azure.microsoft.com/support/options/).

IoT Hub throttles requests when the following quotas are exceeded.

| Omezení | Hodnota na centrum |
| --- | --- |
| Operace registru identit <br/> (create, retrieve, list, update, and delete), <br/> jednotlivý nebo hromadný import/export |83.33/sec/unit (5,000/min/unit) (for S3). <br/> 1.67/sec/unit (100/min/unit) (for S1 and S2). |
| Připojení zařízení |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>Minimálně 100/s |
| Odesílání typu zařízení-cloud |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>Minimálně 100/s |
| Odesílání typu cloud-zařízení | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S1 and S2). |
| Příjem typu cloud-zařízení |833.33/sec/unit (50,000/min/unit) (for S3), 16.67/sec/unit (1,000/min/unit) (for S1 and S2). |
| Operace nahrávání souborů |83.33 file upload initiations/sec/unit (5,000/min/unit) (for S3), 1.67 file upload initiations/sec/unit (100/min/unit) (for S1 and S2). <br/> 10,000 SAS URIs can be out for an Azure Storage account at one time.<br/> Najednou může existovat 10 identifikátorů URI SAS/zařízení. |
| Přímé metody | 24 MB/sec/unit (for S3), 480 KB/sec/unit (for S2), 160 KB/sec/unit (for S1).<br/> Based on 8-KB throttling meter size. |
| Čtení dvojčat zařízení | 500/sec/unit (for S3), Maximum of 100/sec or 10/sec/unit (for S2), 100/sec (for S1) |
| Aktualizace dvojčat zařízení | 250/sec/unit (for S3), Maximum of 50/sec or 5/sec/unit (for S2), 50/sec (for S1) |
| Operace úloh <br/> (create, update, list, and delete) | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S2), 1.67/sec/unit (100/min/unit) (for S1). |
| Propustnost operací úloh jednotlivých zařízení | 50/sec/unit (for S3), maximum of 10/sec or 1/sec/unit (for S2), 10/sec (for S1). |
| Device stream initiation rate | 5 new streams/sec (for S1, S2, S3, and F1 only). |
