---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224428"
---
V následující tabulce jsou uvedena omezení přidružená k různým úrovním služeb S1, S2, S3 a F1. Informace o nákladech na jednotlivé *jednotky* v každé vrstvě najdete v [tématu Ceny služby Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Prostředek | S1 Standard | S2 Standard | S3 Standard | F1 Free |
| --- | --- | --- | --- | --- |
| Počet zpráv za den |400 000 |6 000 000 |300 000 000 |8 000 |
| Maximální počet jednotek |200 |200 |10 |1 |

> [!NOTE]
> Pokud očekáváte použití více než 200 jednotek s rozbočovačem úrovní S1 nebo S2 nebo 10 jednotek s centrem úrovně S3, obraťte se na podporu Microsoftu.
> 
> 

V následující tabulce jsou uvedena omezení, která platí pro prostředky centra IoT Hub.

| Prostředek | Omezení |
| --- | --- |
| Maximální počet placených služeb IoT Hub na předplatné Azure |100 |
| Maximální počet bezplatných služeb IoT Hub na předplatné Azure |1 |
| Maximální počet znaků v ID zařízení | 128 |
| Maximální počet identit zařízení<br/> vrácených v jednom volání |1 000 |
| Maximální doba uchování zpráv typu zařízení-cloud ve službě IoT Hub |7 dní |
| Maximální velikost zprávy typu zařízení-cloud |256 kB |
| Maximální velikost dávky typu zařízení-cloud |AMQP a HTTP: 256 KB pro celou dávku <br/>MQTT: 256 KB pro každou zprávu |
| Maximální počet zpráv v dávce typu zařízení-cloud |500 |
| Maximální velikost zprávy typu cloud-zařízení |64 kB |
| Maximální hodnota TTL pro zprávy typu cloud-zařízení |2 dny |
| Maximální počet doručení zpráv typu <br/> cloud-zařízení |100 |
| Maximální hloubka fronty cloud-to-device na zařízení |50 |
| Maximální počet doručení zpráv se zpětnou vazbou <br/> v reakci na zprávu typu cloud-zařízení |100 |
| Maximální hodnota TTL pro zprávy se zpětnou vazbou <br/> v reakci na zprávu typu cloud-zařízení |2 dny |
| [Maximální velikost dvojčete zařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB pro tagy sekce a 32 KB pro požadované a hlášené vlastnosti oddíly každý |
| Maximální délka dvojitého řetězce klíče zařízení | 1 kB |
| Maximální délka hodnoty dvojitého řetězce zařízení | 4 kB |
| [Maximální hloubka objektu ve dvojčeti zařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Maximální velikost datové části přímé metody | 128 kB |
| Maximální doba uchování historie úlohy | 30 dní |
| Maximální počet souběžných úloh | 10 (pro S3), 5 (pro S2), 1 (pro S1) |
| Maximální počet dalších koncových bodů | 10 (pro S1, S2 a S3) |
| Maximální počet pravidel směrování zpráv | 100 (pro S1, S2 a S3) |
| Maximální počet souběžně připojených datových proudů zařízení | 50 (pouze pro S1, S2, S3 a F1) |
| Maximální přenos dat datového toku zařízení | 300 MB za den (pouze pro S1, S2, S3 a F1) |

> [!NOTE]
> Pokud potřebujete více než 100 placených center IoT hubů v rámci předplatného Azure, obraťte se na podporu Microsoftu.

> [!NOTE]
> V současné době je celkový počet zařízení a modulů, které lze zaregistrovat do jednoho centra IoT hub, omezen na 1 000 000. Chcete-li tento limit zvýšit, obraťte se na [podporu společnosti Microsoft](https://azure.microsoft.com/support/options/).

Centrum IoT omezení požadavky při překročení následující kvóty.

| Omezení | Hodnota na centrum |
| --- | --- |
| Operace registru identit <br/> (vytvořit, načíst, vypsat, aktualizovat a odstranit), <br/> jednotlivý nebo hromadný import/export |83.33/s/unit (5 000/min/jednotka) (pro S3). <br/> 1.67/sec/unit (100/min/jednotka) (pro S1 a S2). |
| Připojení zařízení |6 000/s/jednotka (pro S3), 120/s/jednotka (pro S2), 12/s/jednotka (pro S1). <br/>Minimálně 100/s |
| Odesílání typu zařízení-cloud |6 000/s/jednotka (pro S3), 120/s/jednotka (pro S2), 12/s/jednotka (pro S1). <br/>Minimálně 100/s |
| Odesílání typu cloud-zařízení | 83.33/s/unit (5,000/min/jednotka) (pro S3), 1.67/sec/unit (100/min/unit) (pro S1 a S2). |
| Příjem typu cloud-zařízení |833,33/s/jednotka (50 000/min/jednotka) (pro S3), 16,67/s/jednotka (1 000/min/jednotka) (pro S1 a S2). |
| Operace nahrávání souborů |83.33 zahájení nahrávání souborů/s/jednotka (5 000/min/jednotka) (pro S3), 1,67 zahájení nahrávání souborů/s/jednotka (100/min/jednotka) (pro S1 a S2). <br/> 10 000 identifikátorů URI SAS může být pro účet úložiště Azure najednou.<br/> Najednou může existovat 10 identifikátorů URI SAS/zařízení. |
| Přímé metody | 24 MB/s/jednotka (pro S3), 480 KB/s/jednotka (pro S2), 160 KB/s/jednotka (pro S1).<br/> Na základě velikosti měřiče omezení 8 KB. |
| Čtení dvojčat zařízení | 500/s/jednotka (pro S3), maximálně 100/s nebo 10/s/jednotka (pro S2), 100/s (pro S1) |
| Aktualizace dvojčat zařízení | 250/s/jednotka (pro S3), maximálně 50/s nebo 5/s/jednotka (pro S2), 50/s (pro S1) |
| Operace úloh <br/> (vytvořit, aktualizovat, vypsat a odstranit) | 83.33/sec/unit (5,000/min/jednotka) (pro S3), 1,67/s/jednotka (100/min/jednotka) (pro S2), 1,67/s/jednotka (100/min/jednotka) (pro S1). |
| Propustnost operací úloh jednotlivých zařízení | 50/s/jednotka (pro S3), maximálně 10/s nebo 1/s/jednotka (pro S2), 10/s (pro S1). |
| Rychlost iniciování datového proudu zařízení | 5 nových datových proudů/s (pouze pro S1, S2, S3 a F1). |
