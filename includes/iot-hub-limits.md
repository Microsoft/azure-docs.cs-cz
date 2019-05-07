---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 104849557a8580e16fa1860b7919d1c0252debe9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65150358"
---
Následující tabulka uvádí omezení spojená s různými úrovněmi služeb S1, S2, S3 a F1. Informace o nákladech jednotlivých *jednotky* v každé úrovni najdete v tématu [ceny služby Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Resource | S1 Standard | S2 Standard | S3 Standard | F1 Free |
| --- | --- | --- | --- | --- |
| Počet zpráv za den |400 000 |6 000 000 |300 000 000 |8 000 |
| Maximální počet jednotek |200 |200 |10 |1 |

> [!NOTE]
> Pokud očekáváte více než 200 jednotek pomocí centra vrstvy S1 nebo S2 nebo 10 jednotek v centru úrovně S3, kontaktujte Microsoft Support.
> 
> 

Následující tabulka uvádí omezení, které se vztahují na prostředky služby IoT Hub.

| Resource | Omezení |
| --- | --- |
| Maximální počet placených služeb IoT Hub na předplatné Azure |50 |
| Maximální počet bezplatných služeb IoT Hub na předplatné Azure |1 |
| Maximální počet znaků v ID zařízení | 128 |
| Maximální počet identit zařízení<br/> vrácených v jednom volání |1 000 |
| Maximální doba uchování zpráv typu zařízení-cloud ve službě IoT Hub |7 dní |
| Maximální velikost zprávy typu zařízení-cloud |256 kB |
| Maximální velikost dávky typu zařízení-cloud |AMQP a HTTP: 256 KB pro celý batch <br/>MQTT: 256 KB pro každou zprávu |
| Maximální počet zpráv v dávce typu zařízení-cloud |500 |
| Maximální velikost zprávy typu cloud-zařízení |64 kB |
| Maximální hodnota TTL pro zprávy typu cloud-zařízení |2 dny |
| Maximální počet doručení zpráv typu <br/> cloud-zařízení |100 |
| Maximální počet doručení zpráv se zpětnou vazbou <br/> v reakci na zprávu typu cloud-zařízení |100 |
| Maximální hodnota TTL pro zprávy se zpětnou vazbou <br/> v reakci na zprávu typu cloud-zařízení |2 dny |
| [Maximální velikost dvojčete zařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) <br/> (značky, ohlášené vlastnosti a požadované vlastnosti) | 8 kB |
| Maximální velikost řetězcové hodnoty dvojčete zařízení | 4 KB |
| [Maximální hloubka objektu ve dvojčeti zařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 5 |
| Maximální velikost datové části přímé metody | 128 KB |
| Maximální doba uchování historie úlohy | 30 dní |
| Maximální počet souběžných úloh | 10 (pro S3), 5 (pro S2), 1 (pro S1) |
| Maximální počet dalších koncových bodů | 10 (pro S1, S2 a S3) |
| Maximální počet pravidel směrování zpráv | 100 (pro S1, S2 a S3) |
| Maximální počet současně připojených zařízení datových proudů | 50 (pro S1, S2, S3 a pouze F1) |
| Přenos dat stream maximální zařízení | 300 MB za den (pro S1, S2, S3 a pouze F1) |

> [!NOTE]
> Pokud potřebujete více než 50 placených služeb IoT hub v rámci předplatného Azure, obraťte se na Microsoft Support.

> [!NOTE]
> Maximální počet zařízení, která se můžete připojit k jedno centrum IoT v současné době je 1 000 000. Pokud chcete tento limit zvýšit, obraťte se na [Microsoft Support](https://azure.microsoft.com/support/options/).

IoT Hub omezuje žádosti při překročení následujících kvót.

| Omezení | Hodnota na centrum |
| --- | --- |
| Operace registru identit <br/> (vytvoření, získání, seznam, aktualizace a odstranění), <br/> jednotlivý nebo hromadný import/export |83.33/sec/Unit (5 000/min/jednotku) (pro S3). <br/> 1.67/sec/Unit (100/min/jednotku) (pro S1 a S2). |
| Připojení zařízení |6 000/s/jednotku (pro S3), 120/s/jednotku (pro S2), 12/s/jednotku (pro S1). <br/>Minimálně 100/s |
| Odesílání typu zařízení-cloud |6 000/s/jednotku (pro S3), 120/s/jednotku (pro S2), 12/s/jednotku (pro S1). <br/>Minimálně 100/s |
| Odesílání typu cloud-zařízení | 83.33/sec/Unit (5 000/min/jednotku) (pro S3), 1.67/sec/unit (100/min/jednotku) (pro S1 a S2). |
| Příjem typu cloud-zařízení |833.33/sec/Unit (50 000/min/jednotku) (pro S3), 16.67/sec/unit (1 000/min/jednotku) (pro S1 a S2). |
| Operace nahrávání souborů |(pro S1 a S2) odeslat 83.33 soubor oznámení/s/jednotku (5 000/min/jednotku) (pro S3), souboru 1.67 odesílání oznámení/s/jednotku (100/min/jednotku). <br/> 10 000 identifikátorů URI SAS může být si pro účet služby Azure Storage v jednom okamžiku.<br/> Najednou může existovat 10 identifikátorů URI SAS/zařízení. |
| Přímé metody | 24 MB/s/jednotku (pro S3), 480 KB/s/jednotku (pro S2), 160 KB/s/jednotku (pro S1).<br/> Podle velikosti 8 KB omezení velikosti měřidla. |
| Čtení dvojčat zařízení | 500/s/jednotku (pro S3), maximálně 100/s nebo 10/s/jednotku (pro S2), 100/s (pro S1) |
| Aktualizace dvojčat zařízení | 250/s/jednotku (pro S3), maximálně 50/s nebo 5/s/jednotku (pro S2), 50/s (pro S1) |
| Operace úloh <br/> (vytvoření, aktualizace, seznam a odstranění) | (5 000/min/jednotku) (pro S3), 1.67/sec/unit 83.33/sec/Unit (100/min/jednotku) (pro S2), 1.67/sec/unit (100/min/jednotku) (pro S1). |
| Propustnost operací úloh jednotlivých zařízení | 50/s/jednotku (pro S3), maximálně 10/s nebo 1/s/jednotku (pro S2), 10/s (pro S1). |
| Rychlost zahájení streamování zařízení | 5 nových datových proudů za sekundu (pro S1, S2, S3 a pouze F1). |
