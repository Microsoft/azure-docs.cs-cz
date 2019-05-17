---
title: Principy Azure IoT Hub kvót a omezování | Dokumentace Microsoftu
description: Příručka pro vývojáře – popis kvót, které platí pro službu IoT Hub a očekávané chování při omezování.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/11/2019
ms.openlocfilehash: ea9bea8b314d00db87ad7addacc49a976e0da08e
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2019
ms.locfileid: "65550469"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Reference – IoT Hub kvóty a omezování

## <a name="quotas-and-throttling"></a>Kvóty a omezování

Každé předplatné Azure může mít maximálně 50 centra IoT hub a nejvýše 1 bezplatný rozbočovače.

Každé centrum IoT se zřizuje s určitým počtem jednotek na konkrétní úrovni. Úrovně a počtu jednotek určit maximální denní kvótu zpráv, které můžete odeslat. Velikost zprávy, který slouží k výpočtu je denní kvóty pro rozbočovač na úrovni free po 0,5 KB a 4 KB pro všechny další úrovně. Další informace najdete v tématu [ceny služby Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

Na úrovni také určuje omezení, které služby IoT Hub vynucuje pro všechny operace.

## <a name="operation-throttles"></a>Omezení operace

Omezení operace jsou frekvence omezení, které se použijí v minuta rozsahy a jsou určené k zabránění zneužití. Navíc se vztahuje [traffic shaping](#traffic-shaping).

V následující tabulce jsou uvedeny vynucené omezení. Hodnoty se vztahují jednotlivé rozbočovače.

| Omezení | Free, B1 a S1 | B2 a S2 | B3 a S3 | 
| -------- | ------- | ------- | ------- |
| [Operace registru identit](#identity-registry-operations-throttle) (vytvoření, načtení, výpis, aktualizace nebo odstranění) | 1.67/sec/Unit (100/min/jednotku) | 1.67/sec/Unit (100/min/jednotku) | 83.33/sec/Unit (5 000/min/jednotku) |
| [Nové připojení zařízení](#device-connections-throttle) (Toto omezení platí pro míru _nová připojení_, není celkový počet připojení) | Vyšší 100/s nebo 12/s/jednotku <br/> Například jsou dvě jednotky S1: 2\*12 = 24 nových připojení za sekundu, ale mají alespoň 100 nové připojení za sekundu napříč vaší jednotky. Díky devět jednotek S1, máte 108 nové připojení za sekundu (9\*12) napříč vaší jednotky. | nové 120 připojení/s/jednotku | nové 6 000 připojení/s/jednotku |
| Odesílání typu zařízení-cloud | Vyšší 100/s nebo 12/s/jednotku <br/> Například jsou dvě jednotky S1: 2\*12 = 24/sec, ale budete mít minimálně 100/sec napříč vaší jednotky. Díky devět jednotek S1, máte 108 za sekundu (9\*12) napříč vaší jednotky. | 120/s/jednotku | 6 000/s/jednotku |
| Odesílání typu cloud zařízení<sup>1</sup> | 1.67/sec/Unit (100/min/jednotku) | 1.67/sec/Unit (100/min/jednotku) | 83.33/sec/Unit (5 000/min/jednotku) |
| Cloudové zařízení obdrží<sup>1</sup> <br/> (pouze pokud zařízení používá protokol HTTPS)| 16.67/sec/Unit (1 000/min/jednotku) | 16.67/sec/Unit (1 000/min/jednotku) | 833.33/sec/Unit (50 000/min/jednotku) |
| Nahrání souboru | Soubor 1.67 odesílání oznámení/s/jednotku (100/min/jednotku) | Soubor 1.67 odesílání oznámení/s/jednotku (100/min/jednotku) | soubor 83.33 odesílání oznámení/s/jednotku (5 000/min/jednotku) |
| Přímé metody<sup>1</sup> | 160KB/s/jednotku<sup>2</sup> | 480KB/s/jednotku<sup>2</sup> | 24MB/s/jednotku<sup>2</sup> | 
| Dotazy | 20/min/jednotku | 20/min/jednotku | 1 000/min/jednotku |
| (Zařízení a modul) čtení dvojčat<sup>1</sup> | 100/s | Vyšší 100/s nebo 10/s/jednotku | 500/sec/unit |
| Dvojče aktualizace (zařízení a modul)<sup>1</sup> | 50/sec | Vyšší 50/s nebo 5/s/jednotku | 250/s/jednotku |
| Úlohy operace<sup>1</sup> <br/> (vytvoření, aktualizace, výpis, odstranění) | 1.67/sec/Unit (100/min/jednotku) | 1.67/sec/Unit (100/min/jednotku) | 83.33/sec/Unit (5 000/min/jednotku) |
| Úlohy zařízení operace<sup>1</sup> <br/> (aktualizovat dvojče, vyvolání přímé metody) | 10 za sekundu | Vyšší 10/s nebo 1/s/jednotku | 50/s/jednotku |
| Konfigurace a nasazení hraniční<sup>1</sup> <br/> (vytvoření, aktualizace, výpis, odstranění) | 0.33/sec/Unit (20/min/jednotku) | 0.33/sec/Unit (20/min/jednotku) | 0.33/sec/Unit (20/min/jednotku) |
| Rychlost zahájení streamování zařízení<sup>1</sup> | 5 nových datových proudů za sekundu | 5 nových datových proudů za sekundu | 5 nových datových proudů za sekundu |
| Maximální počet současně připojených zařízení datových proudů<sup>1</sup> | 50 | 50 | 50 |
| Přenos dat stream maximální zařízení<sup>1</sup> (agregovat svazku za den) | 300 MB | 300 MB | 300 MB |

<sup>1</sup>tato funkce není k dispozici na úrovni basic služby IoT Hub. Další informace najdete v tématu [návodu k výběru správné služby IoT Hub](iot-hub-scaling.md). <br/><sup>2</sup>omezení měřiče velikost je 4 KB.

### <a name="traffic-shaping"></a>Směrování přenosu

Tak, aby vyhovovaly shlukové přenosy, IoT Hub přijímá požadavky nad bude omezovač po omezenou dobu. Prvních tyto požadavky zpracovávají okamžitě. Však-li i nadále počet požadavků, které porušují omezení služby IoT Hub začíná umístěním požadavků ve frontě a zpracovávány v omezení četnosti. Tento efekt je volána *traffic shaping*. Kromě toho je omezená velikost této fronty. Porušení omezení i nadále, nakonec se fronta zaplní, a IoT Hub spustí odmítá žádosti s `429 ThrottlingException`. 

Například použití simulovaného zařízení k odesílání 200 zpráv typu zařízení cloud sekundu do služby IoT Hub S1 (která je omezena limitem odešle D2C 100/s). Pro první minutu nebo dvě zprávy zpracuje okamžitě. Ale vzhledem k tomu, že zařízení i nadále odesílat zprávy více než limit omezovače, začne jenom procesem 100 zpráv za sekundu služby IoT Hub a vloží zbývající ve frontě. Spuštění vašeho povšimnutí zvýší latence. Nakonec můžete začít získávat `429 ThrottlingException` jako plní fronty a "počet omezení chyb" v [metriky služby IoT Hub](iot-hub-metrics.md) spustí zvýšení.

### <a name="identity-registry-operations-throttle"></a>Omezit operace registru identit

Operace registru identit zařízení jsou určeny k použití za běhu ve správě zařízení a zřizování scénáře. Čtení nebo aktualizaci velkého počtu identit zařízení podporovaná prostřednictvím [import a export úloh](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Omezit připojení zařízení

*Připojení zařízení* řídí omezení frekvence, ve kterém je možné navázat nová připojení zařízení pomocí služby IoT hub. *Připojení zařízení* omezení neřídí maximální počet současně připojených zařízení. *Připojení zařízení* omezení frekvence závisí na počtu jednotek, které jsou zřízené pro službu IoT hub.

Například pokud zakoupíte jedné jednotky S1, můžete získat omezení 100 připojení za sekundu. Proto pro připojení 100,, 000 zařízení, trvá aspoň 1 000 sekund (přibližně 16 minut). Však může mít libovolný počet současně připojených zařízení, máte zařízení registrovaná v registru identit.


## <a name="other-limits"></a>Další omezení

IoT Hub vynucuje Další provozní omezení:

| Operace | Omezení |
| --------- | ----- |
| Zařízení | Maximální počet zařízení, která se můžete připojit k jedno centrum IoT je 1 000 000. Jediný způsob, jak tento limit zvýšit, je kontaktovat [Microsoft Support](https://azure.microsoft.com/support/options/).| 
| Nahrání souboru identifikátorů URI | 10 000 identifikátorů URI SAS může být si pro účet úložiště najednou. <br/> Najednou může existovat 10 identifikátorů URI SAS/zařízení. |
| Úlohy<sup>1</sup> | Maximální počet souběžných úloh je 1 (pro S1 a Free), 5 (pro S2) a 10 (pro S3). Nicméně maximální počet souběžných [úlohy import/export zařízení](iot-hub-bulk-identity-mgmt.md) 1 pro všechny úrovně. <br/>Historie úlohy se uchovávají až do 30 dnů. |
| Další koncové body | Placené SKU hubs může mít 10 další koncové body. Bezplatné skladové položky hubs může mít jeden další koncový bod. |
| Pravidla směrování zpráv | Placené SKU rozbočovače máte 100 pravidla směrování. Bezplatné skladové položky hubs může mít pět pravidel směrování. |
| Zasílání zpráv typu zařízení cloud | Maximální velikost 256 KB |
| Zasílání zpráv typu cloud zařízení<sup>1</sup> | Maximální velikost 64 KB. Maximální počet čekajících zpráv pro doručování je 50. |
| Přímá metoda<sup>1</sup> | Přímé metody maximální velikost datové části je 128 KB. |
| Konfigurace automatického zařízení<sup>1</sup> | 100 konfigurace na placené SKU rozbočovače. 20 konfigurace na bezplatné Centrum SKU. |
| Automatické nasazení Edge<sup>1</sup> | 20 modulů na nasazení. 100 nasazení na placené SKU rozbočovače. 20 nasazení na bezplatné Centrum SKU. |
| Dvojčata<sup>1</sup> | Maximální velikost dvojčete oddílu (značky, požadovaných vlastností, ohlášených vlastností) je 8 KB |

<sup>1</sup>tato funkce není k dispozici na úrovni basic služby IoT Hub. Další informace najdete v tématu [návodu k výběru správné služby IoT Hub](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Zvýšení limitu kvóty a omezení

V každém okamžiku, můžete zvýšit kvóty nebo omezení limitů podle [zvýšením počtu zřízených jednotek služby IoT hub](iot-hub-upgrade.md).

## <a name="latency"></a>Latence

IoT Hub se snaží poskytovat s nízkou latencí pro všechny operace. Kvůli stavu sítě a dalších faktorů, nepředvídatelné ji však nemůže zaručit některých zpoždění. Při návrhu řešení, měli byste:

* Vyhněte se vyvozování závěry ohledně maximální latence jakékoli operace služby IoT Hub.
* Zřízení služby IoT hub v oblasti Azure, které je nejblíže k vašim zařízením.
* Zvažte použití k provádění operací nízkou latenci, na zařízení nebo pro bránu co nejblíž zařízení Azure IoT Edge.

Víc jednotek služby IoT Hub vliv omezení, jak je popsáno výše, ale neposkytuje žádné výhody další latence nebo záruky.

Pokud se zobrazí neočekávaná zvýší latence operace, obraťte se na [Microsoft Support](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Další postup

Podrobné informace o službě IoT Hub chování při omezování chování, najdete v blogovém příspěvku [omezení šířky pásma služby IoT Hub a](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Další referenční témata v této příručce pro vývojáře IoT Hub patří:

* [Koncové body IoT Hubu](iot-hub-devguide-endpoints.md)
