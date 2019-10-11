---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: f0de7aedc3883978582da1950e95aaabac9a1695
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72272974"
---
V následující tabulce jsou uvedené limity spojené s různými úrovněmi služeb S1, S2, S3 a F1. Informace o nákladech na každou *jednotku* v každé úrovni najdete v tématu [ceny za Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

| Partner | Standard S1 | S2 Standard | S3 Standard | F1 zdarma |
| --- | --- | --- | --- | --- |
| Zprávy za den |400 000 |6 000 000 |300 000 000 |8 000 |
| Maximální počet jednotek |200 |200 |10pruhový |první |

> [!NOTE]
> Pokud předpokládáte, že používáte více než 200 jednotek s centrem vrstev S1 nebo S2 nebo 10 jednotkami v centru vrstev S3, kontaktujte podpora Microsoftu.
> 
> 

Následující tabulka uvádí omezení, která platí pro IoT Hub prostředky.

| Partner | Počtu |
| --- | --- |
| Maximální počet placených Center IoT na předplatné Azure |50 |
| Maximální počet bezplatných rozbočovačů IoT na předplatné Azure |první |
| Maximální počet znaků v ID zařízení | 128 |
| Maximální počet identit zařízení<br/> vráceno v jednom volání |1 000 |
| Maximální doba uchování zpráv ze zařízení do cloudu IoT Hub zprávy |7 dní |
| Maximální velikost zprávy typu zařízení-Cloud |256 KB |
| Maximální velikost dávky typu zařízení-Cloud |AMQP a HTTP: 256 KB pro celou dávku <br/>MQTT: 256 KB pro každou zprávu |
| Maximální počet zpráv v dávce ze zařízení do cloudu |500 |
| Maximální velikost zprávy typu cloud-zařízení |64 KB |
| Maximální hodnota TTL pro zprávy z cloudu na zařízení |2 dny |
| Maximální počet doručování pro cloudové zařízení <br/> messages |100 |
| Maximální hloubka fronty cloudového zařízení na zařízení |50 |
| Maximální počet doručení pro zprávy zpětné vazby <br/> v reakci na zprávu typu cloud-zařízení |100 |
| Maximální hodnota TTL pro zprávy zpětné vazby v <br/> odezva na zprávu typu cloud-zařízení |2 dny |
| [Maximální velikost vlákna zařízení](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) <br/> (značky, hlášené vlastnosti a požadované vlastnosti) | 8 KB |
| Maximální velikost hodnoty nevlákenního řetězce zařízení | 4 KB |
| [Maximální hloubka objektu v zařízení je dvojitá.](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 5 |
| Maximální velikost datové části přímé metody | 128 KB |
| Maximální doba uchování historie úlohy | 30 dní |
| Maximální počet souběžných úloh | 10 (pro S3), 5 pro (S2), 1 (pro S1) |
| Maximální počet dalších koncových bodů | 10 (pro S1, S2 a S3) |
| Maximální počet pravidel směrování zpráv | 100 (pro S1, S2 a S3) |
| Maximální počet souběžně připojených datových proudů zařízení | 50 (jenom pro S1, S2, S3 a F1) |
| Maximální přenos dat datového proudu zařízení | 300 MB za den (jenom pro S1, S2, S3 a F1) |

> [!NOTE]
> Pokud v předplatném Azure potřebujete více než 50 placených Center IoT Hub, kontaktujte podpora Microsoftu.

> [!NOTE]
> V současné době je maximální počet zařízení, která můžete připojit k jednomu centru IoT Hub, 1 000 000. Pokud chcete tento limit zvýšit, kontaktujte [Podpora Microsoftu](https://azure.microsoft.com/support/options/).

IoT Hub omezuje požadavky při překročení následujících kvót.

| Omezení | Hodnota pro jednotlivé rozbočovače |
| --- | --- |
| Operace registru identit <br/> (vytvořit, načíst, vypsat, aktualizovat a odstranit), <br/> jednotlivý nebo hromadný import/export |83.33/s/jednotku (5 000/min/jednotku) (pro S3) <br/> 1.67/s/jednotku (100/min/jednotku) (pro S1 a S2) |
| Připojení zařízení |6000/s/jednotku (pro S3), 120/s/jednotku (pro S2), 12/s/jednotku (pro S1). <br/>Minimálně 100/s |
| Odesílání ze zařízení do cloudu |6000/s/jednotku (pro S3), 120/s/jednotku (pro S2), 12/s/jednotku (pro S1). <br/>Minimálně 100/s |
| Posílání z cloudu do zařízení | 83.33/s/jednotku (5 000/min/jednotku) (pro S3), 1.67/s/jednotku (pro S1 a S2) (100/min/Unit) (pro S1 a S2). |
| Příjem z cloudu do zařízení |833.33/s/jednotku (50 000/min/jednotku) (pro S3), 16.67/s/jednotku (pro S1 a S2) (1 000/min/jednotku) (pro S1 a S2). |
| Operace nahrávání souborů |83,33 inicializace nahrávání souborů/s/jednotka (5 000/min/jednotka) (pro S3), 1,67 inicializace nahrávání souborů/s/jednotka (100/min/Unit) (pro S1 a S2) <br/> 10 000 identifikátorů URI SAS může být v jednom okamžiku pro účet Azure Storage.<br/> 10 identifikátorů URI SAS/zařízení může být v jednom okamžiku. |
| Přímé metody | 24 MB/s/jednotku (pro S3), 480 KB/s/jednotku (pro S2), 160 KB/s/jednotku (pro S1).<br/> Na základě velikosti měřiče 8 KB. |
| Dvojitá čtení zařízení | 500/s/jednotku (pro S3), maximálně 100/s nebo 10/s/jednotku (pro S2), 100/s (pro S1) |
| Aktualizace s dvojitým zařízením | 250/s/jednotku (pro S3), maximálně 50/s nebo 5/s/jednotku (pro S2), 50/s (pro S1) |
| Operace úloh <br/> (vytvoření, aktualizace, vypsání a odstranění) | 83.33/s/jednotku (5 000/min/jednotku) (pro S3), 1.67/s/jednotku (pro S2/min/jednotku) (pro S2), 1.67/s/jednotku (100/min/jednotku) (pro S1). |
| Propustnost operací s úlohami na zařízení | 50/s/jednotku (pro S3), maximálně 10/s nebo 1/s/jednotku (pro S2), 10/s (pro S1). |
| Frekvence iniciace datového proudu zařízení | 5 nových proudů za sekundu (jenom pro S1, S2, S3 a F1) |
