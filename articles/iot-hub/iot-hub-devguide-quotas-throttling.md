---
title: Vysvětlení kvót a omezení pro Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – popis kvót, které platí pro IoT Hub a očekávané chování omezení.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.custom:
- 'Role: Cloud Development'
- 'Role: Operations'
- 'Role: Technical Support'
ms.openlocfilehash: 2f2ab3c55c5532b76c45a18054fd653dd8fe8137
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92504070"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Referenční kvóty a omezení IoT Hub

Tento článek vysvětluje kvóty pro IoT Hub a poskytuje informace, které vám pomůžou pochopit, jak omezování funguje.

## <a name="quotas-and-throttling"></a>Kvóty a omezování

Každé předplatné Azure může mít maximálně 50 IoT Hub a maximálně 1 bezplatné centrum.

Každé centrum IoT se zřizuje s určitým počtem jednotek na konkrétní úrovni. Úroveň a počet jednotek určují maximální denní kvótu zpráv, které můžete odeslat. Velikost zprávy, která se používá k výpočtu denní kvóty, je 0,5 KB pro rozbočovač úrovně Free a 4KB pro všechny ostatní úrovně. Další informace najdete v tématu [ceny služby Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

Vrstva také určuje omezení omezování, která IoT Hub vynutila pro všechny operace.

## <a name="iot-plug-and-play"></a>IoT Plug and Play

Zařízení IoT technologie Plug and Play odesílají pro každé rozhraní alespoň jednu zprávu telemetrie, včetně kořene, což může zvýšit počet zpráv započítaných do vaší kvóty zpráv.

## <a name="operation-throttles"></a>Omezení operací

Omezení operací jsou omezení četnosti, která se uplatní v minutách a jsou určená k tomu, aby se zabránilo zneužití. Vztahují se také na [tvar provozu](#traffic-shaping).

V následující tabulce jsou uvedena vynutila omezení. Hodnoty odkazují na jednotlivé rozbočovače.

| Omezení | Free, B1 a S1 | B2 a S2 | B3 a S3 | 
| -------- | ------- | ------- | ------- |
| [Operace registru identit](#identity-registry-operations-throttle) (vytvoření, načtení, výpis, aktualizace, odstranění) | 1.67/s/jednotku (100/min/Unit) | 1.67/s/jednotku (100/min/Unit) | 83.33/s/jednotku (5 000/min/jednotku) |
| [Nová připojení zařízení](#device-connections-throttle) (Tato omezení se vztahují na rychlost _nových připojení_, ne na celkový počet připojení) | Vyšší z 100/s nebo 12/s/jednotka <br/> Například dvě jednotky S1 jsou 2 \* 12 = 24 nových připojení/s, ale v rámci jednotek máte minimálně 100 nových připojení za sekundu. S devíti jednotkami S1 máte v rámci svých jednotek 108 nových připojení za sekundu (9 \* 12). | 120 nových připojení/s/jednotku | 6 000 nových připojení/s/jednotku |
| Odesílání typu zařízení-cloud | Vyšší z 100 operací odeslání/s nebo 12 operací odeslání za sekundu/jednotku <br/> Například dvě jednotky S1 jsou 2 \* 12 = 24/s, ale v rámci jednotek máte minimálně 100 operací odeslání za sekundu. S devíti jednotkami S1 máte 108 operací odeslání za sekundu (9 \* 12) napříč vašimi jednotkami. | 120 operace odeslání za sekundu/jednotku | 6 000 operace odeslání za sekundu/jednotku |
| Z cloudu do zařízení odesílá<sup>1</sup> . | 1,67 operace odeslání za sekundu/jednotku (100 zpráv/min/jednotku) | 1,67 operace odeslání za sekundu/jednotku (100 operací odeslání/min/jednotku) | 83,33 operace odeslání za sekundu/jednotku (5 000 operací odeslání/min/jednotku) |
| Cloud-zařízení obdrží<sup>1</sup> <br/> (jenom když zařízení používá protokol HTTPS)| 16,67 přijatých operací za sekundu (celkem 1 000 operací přijetí za minutu/min/jednotku) | 16,67 přijatých operací za sekundu (celkem 1 000 operací přijetí za minutu/min/jednotku) | 833,33 přijatých operací za sekundu (celkem 50 000 operací přijetí za minutu/min/jednotku) |
| Nahrání souboru | 1,67 inicializace nahrávání souborů/s/jednotka (100/min/Unit) | 1,67 inicializace nahrávání souborů/s/jednotka (100/min/Unit) | 83,33 inicializace nahrávání souborů/s/jednotka (5 000/min/jednotku) |
| Přímé metody<sup>1</sup> | 160KB/s/jednotka<sup>2</sup> | 480KB/s/jednotka<sup>2</sup> | 24MB/s/jednotka<sup>2</sup> | 
| Dotazy | 20/min/jednotku | 20/min/jednotku | 1000/min/jednotku |
| Dvojitá (zařízení a modul) čtení<sup>1</sup> | 100/s | Vyšší z 100/s nebo 10/s/jednotka | 500/s/jednotka |
| Dvojitá aktualizace (zařízení a modul)<sup>1</sup> | 50/s | Vyšší z 50/s nebo 5/s/jednotku | 250/s/jednotku |
| Operace s úlohami<sup>1</sup> <br/> (vytvoření, aktualizace, výpis, odstranění) | 1.67/s/jednotku (100/min/Unit) | 1.67/s/jednotku (100/min/Unit) | 83.33/s/jednotku (5 000/min/jednotku) |
| Úlohy operací zařízení<sup>1</sup> <br/> (aktualizovat dvojitou metodu, vyvolat přímou metodu) | 10/s | Vyšší z 10/s nebo 1/s/jednotka | 50/s/jednotku |
| Konfigurace a nasazení Edge<sup>1</sup> <br/> (vytvoření, aktualizace, výpis, odstranění) | 0.33/s/jednotku (20/min/jednotku) | 0.33/s/jednotku (20/min/jednotku) | 0.33/s/jednotku (20/min/jednotku) |
| Rychlost iniciace datového proudu zařízení<sup>1</sup> | 5 nových proudů za sekundu | 5 nových proudů za sekundu | 5 nových proudů za sekundu |
| Maximální počet souběžně připojených zařízení proudů<sup>1</sup> | 50 | 50 | 50 |
| Maximální přenos dat datového proudu zařízení<sup>1</sup> (agregovaný objem za den) | 300 MB | 300 MB | 300 MB |

<sup>1</sup> Tato funkce není k dispozici na úrovni Basic IoT Hub. Další informace najdete v tématu [Jak zvolit správnou IoT Hub](iot-hub-scaling.md). <br/><sup>2</sup> . Velikost měřiče omezení je 4 KB. Omezování je založeno jenom na velikosti datové části požadavku.

### <a name="throttling-details"></a>Podrobnosti o omezování

* Velikost měřiče určuje, v jakém přírůstcích se vaše omezení spotřebovává. Pokud je datová část přímého volání mezi 0 a 4 KB, počítá se jako 4 KB. Než začnete s limitem 160 KB/s/jednotkou, můžete provést až 40 volání za sekundu na jednotku.

   Podobně platí, že pokud je datová část v rozmezí 4 KB až 8 KB, jednotlivé účty volání po dobu 8 KB a až 20 volání za sekundu na jednotku můžete zvýšit tak, aby se dosáhlo maximálního limitu.

   Nakonec, pokud je velikost datové části mezi 156KB a 160 KB, budete moct ve svém rozbočovači udělat jenom 1 volání za sekundu na jednotku, abyste mohli zvýšit počet 160 KB/s/jednotku.

*  Pro úlohy, které se týkají *operací zařízení (metoda aktualizace s dvojitou platností, vyvolání přímé metody)* pro vrstvu S2, 50/s/jednotku platí jenom při vyvolání metod pomocí úloh. Pokud přímo vyvoláte přímé metody, použije se původní limit pro omezení na 24 MB/s/jednotku (pro S2).

*  **Kvóta** je agregovaný počet zpráv, které můžete poslat v rámci svého centra *za den*. Omezení kvóty vašeho centra najdete pod **celkovým počtem zpráv za den** na [stránce s cenami IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

*  Omezení typu cloud-zařízení a zařízení-Cloud určují maximální *rychlost* , kterou můžete odesílat zprávy--počet zpráv bez ohledu na bloky o velikosti 4 KB. Každá zpráva může mít až 256 KB, což je [maximální velikost zprávy](iot-hub-devguide-quotas-throttling.md#other-limits).

*  Je dobrým zvykem omezit volání, abyste nedosáhli limitů omezování. Pokud jste dosáhli limitu, IoT Hub odpoví kódem chyby 429 a klient se může znovu zapnout. Tato omezení platí pro jednotlivé rozbočovače (nebo v některých případech na střed a jednotku). Další informace najdete v tématu [Správa připojení a spolehlivé vzorce pro zasílání zpráv a opakování](iot-hub-reliability-features-in-sdks.md#retry-patterns).

### <a name="traffic-shaping"></a>Tvarování provozu

Aby bylo možné přizpůsobit shlukový přenos, IoT Hub přijmout požadavky nad omezením po dobu omezeného času. Prvních pár těchto požadavků se zpracovává okamžitě. Pokud však počet požadavků pokračuje v rozporu s omezením, IoT Hub začne umísťovat požadavky do fronty a zpracovány při dosažení limitu. Tento efekt se nazývá *Změna provozu*. Kromě toho je velikost této fronty omezená. Pokud dojde k porušení omezení, nakonec se fronta vyplní a IoT Hub spustí zamítnutí požadavků `429 ThrottlingException` .

Například můžete použít simulované zařízení k 200 posílání zpráv typu zařízení-Cloud za sekundu do IoT Hub S1 (která má limit 100/s D2C posílá). Pro první minutu nebo dvě se zprávy zpracovávají okamžitě. Vzhledem k tomu, že zařízení pokračuje v posílání více zpráv, než je limit omezení, IoT Hub začne pouze zpracovávat zprávy 100 za sekundu a zbývající ve frontě vloží. Začnete všímáte zvýšené latence. Nakonec začnete s tím, `429 ThrottlingException` jak se bude fronta vyplňovat, a v [metrikách IoT Hub](iot-hub-metrics.md) se začne zvyšovat počet chyb omezení.

### <a name="identity-registry-operations-throttle"></a>Omezení operací v registru identit

Operace v registru identit zařízení jsou určené pro použití za běhu ve scénářích správy a zřizování zařízení. Načítání a aktualizace velkého počtu identit zařízení je podporované prostřednictvím [úloh importu a exportu](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Omezení připojení zařízení

Omezení *připojení zařízení* určuje rychlost, s jakou je možné vytvořit nové připojení zařízení pomocí služby IoT Hub. Omezení počtu *připojení zařízení* neurčuje maximální počet současně připojených zařízení. Omezení rychlosti *připojení zařízení* závisí na počtu jednotek, které jsou zřízené pro Centrum IoT.

Pokud například koupíte jednu jednotku S1, získáte omezení 100 připojení za sekundu. Proto pokud chcete připojit 100 000 zařízení, trvá aspoň 1 000 sekund (přibližně 16 minut). Můžete ale mít tolik současně připojených zařízení, kolik máte zařízení zaregistrovaná v registru identit.

## <a name="other-limits"></a>Další omezení

IoT Hub vynutila jiné provozní limity:

| Operace | Omezení |
| --------- | ----- |
| Zařízení | Celkový počet zařízení plus modulů, které se dají zaregistrovat do jednoho centra IoT Hub, je omezené na 1 000 000. Jediným způsobem, jak tento limit zvýšit, je kontaktovat [Podpora Microsoftu](https://azure.microsoft.com/support/options/).|
| Nahrání souborů | 10 souběžných nahrávání souborů na zařízení. |
| Úlohy<sup>1</sup> | Maximální počet souběžných úloh je 1 (pro Free a S1), 5 (pro S2) a 10 (pro S3). Maximální počet souběžných [úloh importu/exportu zařízení](iot-hub-bulk-identity-mgmt.md) je ale 1 pro všechny úrovně. <br/>Historie úlohy se uchovává až po dobu 30 dnů. |
| Další koncové body | Placené rozbočovače SKU můžou mít 10 dalších koncových bodů. Rozbočovače volných SKU můžou mít jeden další koncový bod. |
| Dotazy směrování zpráv | Placené rozbočovače SKU můžou mít 100 dotazů na směrování. Rozbočovače volných SKU můžou mít pět dotazů směrování. |
| Obohacení zpráv | Placené rozbočovače SKU můžou mít až 10 rozšíření zpráv. Bezplatné rozbočovače SKU můžou mít až 2 rozšíření zpráv.|
| Zasílání zpráv ze zařízení do cloudu | Maximální velikost zprávy 256 KB |
| Zasílání zpráv z cloudu na zařízení<sup>1</sup> | Maximální velikost zprávy 64 KB. Maximální počet nevyřízených zpráv pro doručení je 50 na jedno zařízení. |
| Přímá metoda<sup>1</sup> | Maximální velikost datové části přímé metody je 128 KB. |
| Automatická konfigurace zařízení a modulů<sup>1</sup> | Konfigurace 100 na placené centrum SKU 20 konfigurací na rozbočovač volných SKU |
| IoT Edge automatické nasazení<sup>1</sup> | moduly 50 na jedno nasazení. 100 nasazení (včetně vrstev nasazení) na placené centrum SKU 10 nasazení na rozbočovač volných SKU |
| Dvojitá vlákna<sup>1</sup> | Maximální velikost požadovaných vlastností a sekcí hlášených vlastností jsou 32 KB. Oddíl maximální velikost značek je 8 KB. |
| Zásady sdíleného přístupu | Maximální počet zásad sdíleného přístupu je 16. |
| certifikáty CA x509 | Maximální počet certifikátů CA x509, které mohou být registrovány v IoT Hub je 25. |

<sup>1</sup> Tato funkce není k dispozici na úrovni Basic IoT Hub. Další informace najdete v tématu [Jak zvolit správnou IoT Hub](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Zvýšení kvóty nebo omezení omezení

V libovolném okamžiku můžete zvýšit počet kvót nebo omezení omezení [zvýšením počtu zřízených jednotek ve službě IoT Hub](iot-hub-upgrade.md).

## <a name="latency"></a>Latence

IoT Hub usiluje o zajištění nízké latence pro všechny operace. Vzhledem k podmínkám sítě a jiným nepředvídatelným faktorům však nemůže zaručit určitou latenci. Při návrhu řešení byste měli:

* Nevytvářejte žádné předpoklady týkající se maximální latence jakékoli IoT Hub operace.
* Zřiďte službu IoT Hub v oblasti Azure, která je nejblíže vašim zařízením.
* Zvažte použití Azure IoT Edge k provádění operací citlivých na latenci v zařízení nebo v bráně blízko zařízení.

Více jednotek IoT Hub ovlivňuje omezení popsané dříve, ale neposkytuje žádné další výhody a záruky latence.

Pokud se zobrazí neočekávaná zvýšení latence operace, obraťte se na [Podpora Microsoftu](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Další kroky

Podrobné informace o chování omezení IoT Hub najdete v příspěvku na blogu [IoT Hub omezování a vás](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Další referenční témata v tomto IoT Hub příručce pro vývojáře zahrnují:

* [Koncové body IoT Hubu](iot-hub-devguide-endpoints.md)
