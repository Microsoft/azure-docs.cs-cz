---
title: Principy kvót služby Azure IoT Hub a omezení | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – popis kvót, které platí pro službu IoT Hub a očekávané chování omezení.
author: robinsh
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.openlocfilehash: 71a5737434e78bc39bccdfeb950e0dbc32ed0052
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284691"
---
# <a name="reference---iot-hub-quotas-and-throttling"></a>Reference – kvóty a omezení centra IoT Hub

Tento článek vysvětluje kvóty pro centrum IoT Hub a poskytuje informace, které vám pomohou pochopit, jak omezení funguje.

## <a name="quotas-and-throttling"></a>Kvóty a omezování

Každé předplatné Azure může mít maximálně 50 center IoT huba mise a maximálně 1 bezplatné centrum.

Každé centrum IoT se zřizuje s určitým počtem jednotek na konkrétní úrovni. Úroveň a počet jednotek určují maximální denní kvótu zpráv, které můžete odeslat. Velikost zprávy použitá k výpočtu denní kvóty je 0,5 KB pro rozbočovač volné úrovně a 4 KB pro všechny ostatní úrovně. Další informace najdete v [tématu Azure IoT Hub Ceny](https://azure.microsoft.com/pricing/details/iot-hub/).

Úroveň také určuje omezení omezení, které ioT Hub vynucuje na všechny operace.

### <a name="iot-plug-and-play"></a>IoT Plug and Play

Během veřejné verze Preview budou zařízení IoT Plug and Play odesílat samostatné zprávy pro jednotlivá rozhraní, což může zvýšit počet zpráv započítaných do kvóty zpráv.

## <a name="operation-throttles"></a>Provozní škrticí klapky

Provozní omezení jsou omezení rychlosti, která jsou použita v rozsahu minut a jsou určena k zabránění zneužití. Jsou také předmětem [dopravnítná úprava](#traffic-shaping).

V následující tabulce jsou uvedeny vynucené omezení. Hodnoty odkazují na jednotlivé rozbočovače.

| Omezení | Volný, B1 a S1 | B2 a S2 | B3 a S3 | 
| -------- | ------- | ------- | ------- |
| [Operace registru identit (vytvoření,](#identity-registry-operations-throttle) načtení, seznam, aktualizace, odstranění) | 1.67/sec/unit (100/min/jednotka) | 1.67/sec/unit (100/min/jednotka) | 83.33/s/jednotka (5 000/min/jednotka) |
| [Nová připojení zařízení](#device-connections-throttle) (tento limit se vztahuje na rychlost _nových připojení_, nikoli na celkový počet připojení) | Vyšší o 100/s nebo 12/s/unit <br/> Například dvě jednotky S1\*jsou 2 12 = 24 nových připojení za sekundu, ale máte alespoň 100 nových připojení za sekundu napříč jednotkami. S devíti jednotkami S1 máte napříč jednotkami\*108 nových připojení za sekundu (9 12). | 120 nových připojení/s/jednotka | 6 000 nových připojení/s/jednotka |
| Odesílání typu zařízení-cloud | Vyšší z 100 odesílacích operací/s nebo 12 odesílacích operací/s/jednotka <br/> Například dvě jednotky S1\*jsou 2 12 = 24/sec, ale máte alespoň 100 odesílání operací za sekundu mezi jednotkami. S devíti jednotkami S1 máte 108 operací\*odesílání/s (9 12) mezi jednotkami. | 120 odesílat operace/s/jednotka | 6 000 odesílačů/s/jednotka |
| Cloud-to-device odešle<sup>1</sup> | 1.67 odesílání/s/jednotka (100 zpráv/min/jednotka) | 1.67 odesílání/s/jednotka (100 odesílačů/min/jednotka) | 83.33 operace odesílání/s/jednotka (5 000 operací odesílání/min/jednotka) |
| Cloud-to-zařízení přijímá<sup>1</sup> <br/> (pouze v případě, že zařízení používá PROTOKOL HTTPS)| 16,67 operace příjmu/s/jednotka (1 000 operací příjmu/min/jednotka) | 16,67 operace příjmu/s/jednotka (1 000 operací příjmu/min/jednotka) | 833.33 operace příjmu/s/jednotka (50 000 operací příjmu/min/jednotka) |
| Nahrání souboru | 1.67 zahájení nahrávání souborů/s/jednotka (100/min/jednotka) | 1.67 zahájení nahrávání souborů/s/jednotka (100/min/jednotka) | 83.33 Zahájení nahrávání souborů/s/jednotka (5 000/min/jednotka) |
| Přímé metody<sup>1</sup> | 160 KB/s/jednotka<sup>2</sup> | 480 KB/s/jednotka<sup>2</sup> | 24 MB/s/jednotka<sup>2</sup> | 
| Dotazy | 20/min/jednotka | 20/min/jednotka | 1 000/min/jednotka |
| Twin (zařízení a modul) čte<sup>1</sup> | 100/s | Vyšší o 100/s nebo 10/s/unit | 500/s/jednotka |
| Dvojité aktualizace (zařízení a modul)<sup>1</sup> | 50/s | Vyšší o 50/s nebo 5/s/unit | 250/s/jednotka |
| Operace úloh<sup>1</sup> <br/> (vytvoření, aktualizace, výpis, odstranění) | 1.67/sec/unit (100/min/jednotka) | 1.67/sec/unit (100/min/jednotka) | 83.33/s/jednotka (5 000/min/jednotka) |
| Operace zařízení<sup>úloh1</sup> <br/> (aktualizovat dvojče, vyvolat přímou metodu) | 10/s | Vyšší o 10/s nebo 1/s/unit | 50/s/jednotka |
| Konfigurace a nasazení hran<sup>1</sup> <br/> (vytvoření, aktualizace, výpis, odstranění) | 0.33/s/jednotka (20/min/jednotka) | 0.33/s/jednotka (20/min/jednotka) | 0.33/s/jednotka (20/min/jednotka) |
| Rychlost iniciování proudu zařízení<sup>1</sup> | 5 nových streamů/s | 5 nových streamů/s | 5 nových streamů/s |
| Maximální počet souběžně připojených datových proudů zařízení<sup>1</sup> | 50 | 50 | 50 |
| Maximální přenos dat datového toku zařízení<sup>1</sup> (souhrnný objem za den) | 300 MB | 300 MB | 300 MB |

<sup>1.</sup> Tato funkce není k dispozici v základní vrstvě služby IoT Hub. Další informace najdete v [tématu Jak vybrat ten správný IoT Hub](iot-hub-scaling.md). <br/><sup>2.</sup> Velikost měřiče omezení je 4 KB.

### <a name="throttling-details"></a>Podrobnosti o omezení

* Velikost měřiče určuje, jaké přírůstky je spotřebována limit omezení. Pokud je vaše datová část přímého volání mezi 0 a 4 KB, počítá se jako 4 KB. Před dosažením limitu 160 KB/s/jednotky můžete uskutečňovat až 40 hovorů za sekundu za sekundu.

   Podobně pokud je vaše datová část mezi 4 KB a 8 KB, každý hovor účty pro 8 KB a můžete provést až 20 volání za sekundu na jednotku před dosažením maximální limit.

   A konečně, pokud je vaše velikost datového nákladu mezi 156 KB a 160 KB, budete moci provést pouze 1 volání za sekundu na jednotku v rozbočovači před dosažením limitu 160 KB/s/jednotka.

*  Pro *operace zařízení Úlohy (update twin, vyvolat přímou metodu)* pro úroveň S2, 50/sec/unit se vztahuje pouze na vyvolání metod pomocí úloh. Pokud vyvoláte přímé metody přímo, použije se původní limit omezení 24 MB/s/jednotka (pro S2).

*  **Kvóta** je souhrnný počet zpráv, které můžete odeslat v centru *za den*. Limit kvóty centra najdete ve sloupci **Celkový počet zpráv/den** na [stránce s cenami v centru IoT](https://azure.microsoft.com/pricing/details/iot-hub/)Hub .

*  Omezení cloud-to-device a zařízení-cloud určují maximální *rychlost,* jakou můžete odesílat zprávy – počet zpráv bez ohledu na 4 bloky kb. Každá zpráva může být až 256 KB, což je [maximální velikost zprávy](iot-hub-devguide-quotas-throttling.md#other-limits).

*  Je vhodné omezit volání, abyste nedosáhli nebo nepřekročili omezení omezení. Pokud narazíte na limit, IoT Hub odpoví s kódem chyby 429 a klient by měl back-off a opakujte. Tyto limity jsou na rozbočovač (nebo v některých případech na rozbočovač/jednotku). Další informace naleznete v [odkazech Správa připojení a spolehlivé hospodařící](iot-hub-reliability-features-in-sdks.md#retry-patterns)se vzory pro zasílání zpráv a opakování .

### <a name="traffic-shaping"></a>Tvarování provozu

Pro přizpůsobení burst provozu, IoT Hub přijímá požadavky nad škrticí klapky po omezenou dobu. Prvních několik z těchto požadavků jsou zpracovány okamžitě. Pokud však počet požadavků pokračuje porušovat omezení, služby IoT Hub začne umisťovat požadavky do fronty a zpracovávat je limitem. Tento efekt se nazývá *tvarování provozu*. Kromě toho je omezena velikost této fronty. Pokud narušení omezení pokračuje, nakonec se fronta zaplní a služby IoT Hub začne zamítat požadavky pomocí `429 ThrottlingException`aplikace .

Například pomocí simulovaného zařízení odesílat 200 zpráv zařízení cloud za sekundu s S1 IoT Hub (který má limit 100/s D2C odešle). Pro první minutu nebo dvě, zprávy jsou zpracovány okamžitě. Však vzhledem k tomu, že zařízení nadále odesílat více zpráv, než je limit omezení omezení, IoT Hub začne zpracovávat pouze 100 zpráv za sekundu a umístí zbytek do fronty. Začnete si všímat zvýšené latence. Nakonec začnete získat `429 ThrottlingException` jako fronty zaplní a "počet chyb omezení" v [metriky IoT Hub](iot-hub-metrics.md) začne zvyšovat.

### <a name="identity-registry-operations-throttle"></a>Omezení operací registru identity

Operace registru identit zařízení jsou určeny pro použití za běhu ve scénářích správy zařízení a zřizování. Čtení nebo aktualizace velkého počtu identit zařízení je podporována prostřednictvím [úloh importu a exportu](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

### <a name="device-connections-throttle"></a>Omezení připojení zařízení

Omezení *připojení zařízení* určuje rychlost, jakou lze vytvořit nová připojení zařízení pomocí služby IoT hub. Omezení *připojení zařízení* neurčuje maximální počet současně připojených zařízení. Omezení *rychlosti připojení zařízení* závisí na počtu jednotek, které jsou zřízeny pro službu IoT hub.

Pokud si například koupíte jednu jednotku S1, získáte omezení 100 připojení za sekundu. Proto připojit 100 000 zařízení trvá nejméně 1 000 sekund (přibližně 16 minut). Můžete však mít tolik současně připojených zařízení, kolik máte zařízení registrovaných v registru identit.

## <a name="other-limits"></a>Jiné limity

IoT Hub vynucuje další provozní limity:

| Operace | Omezení |
| --------- | ----- |
| Zařízení | Celkový počet zařízení a modulů, které lze zaregistrovat do jednoho centra IoT hub, je omezen na 1 000 000. Jediným způsobem, jak tento limit zvýšit, je kontaktovat [podporu společnosti Microsoft](https://azure.microsoft.com/support/options/).|
| Nahrání souborů | 10 souběžných souborů nahraje na zařízení. |
| Pracovní místa<sup>1</sup> | Maximální počet souběžných úloh je 1 (pro volné a S1), 5 (pro S2) a 10 (pro S3). Maximální počet souběžných [úloh importu a exportu zařízení](iot-hub-bulk-identity-mgmt.md) je však 1 pro všechny úrovně. <br/>Historie úloh je zachována až 30 dní. |
| Další koncové body | Placené rozbočovače SKU může mít 10 dalších koncových bodů. Volné rozbočovače skladových bodů mohou mít jeden další koncový bod. |
| Dotazy na směrování zpráv | Placené rozbočovače SKU mohou mít 100 směrovacích dotazů. Volné rozbočovače skladových položk může mít pět směrovacích dotazů. |
| Obohacení zpráv | Placené rozbočovače SKU mohou mít až 10 obohacení zpráv. Zdarma skladové položky huby může mít až 2 obohacení zpráv.|
| Zasílání zpráv mezi zařízeními a cloudy | Maximální velikost zprávy 256 KB |
| Zasílání zpráv mezi cloudy<sup>1</sup> | Maximální velikost zprávy 64 KB. Maximální počet nevyřízených zpráv pro doručení je 50 na zařízení. |
| Přímá metoda<sup>1</sup> | Maximální velikost datové části přímé metody je 128 KB. |
| Automatické konfigurace zařízení a modulů<sup>1</sup> | 100 konfigurací na placený rozbočovač SKU. 20 konfigurací na bezplatný rozbočovač SKU. |
| Automatická nasazení IoT Edge<sup>1</sup> | 50 modulů na nasazení. 100 nasazení (včetně vrstvených nasazení) na placené rozbočovač skladových jednotek. 10 nasazení na bezplatné rozbočovač sku. |
| Dvojčata<sup>1</sup> | Maximální velikost požadovaných vlastností a vykazožené vlastnosti oddíly jsou 32 KB každý. Maximální velikost sekce tagů je 8 KB. |
| Zásady sdíleného přístupu | Maximální počet zásad sdíleného přístupu je 16 |

<sup>1.</sup> Tato funkce není k dispozici v základní vrstvě služby IoT Hub. Další informace najdete v [tématu Jak vybrat ten správný IoT Hub](iot-hub-scaling.md).

## <a name="increasing-the-quota-or-throttle-limit"></a>Zvýšení kvóty nebo omezení omezení

V každém okamžiku můžete zvýšit kvóty nebo omezení omezení [zvýšením počtu zřízených jednotek v centru IoT](iot-hub-upgrade.md).

## <a name="latency"></a>Latence

IoT Hub se snaží poskytovat nízkou latenci pro všechny operace. Však z důvodu podmínek sítě a dalších nepředvídatelných faktorů nemůže zaručit určitou latenci. Při navrhování řešení byste měli:

* Vyhněte se vytváření předpokladů o maximální latenci jakékoli operace ioT hubu.
* Zřídit ioT hub v oblasti Azure nejblíže k vašim zařízením.
* Zvažte použití Azure IoT Edge k provádění operací citlivých na latenci na zařízení nebo na bráně v blízkosti zařízení.

Více jednotek ioT hub ovlivnit omezení, jak je popsáno výše, ale neposkytují žádné další výhody latence nebo záruky.

Pokud se zobrazí neočekávané zvýšení latence operací, obraťte se na [podporu společnosti Microsoft](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Další kroky

Podrobné informace o chování při omezování centra IoT Hub najdete v příspěvku blogu o omezení centra [IoT hub a vy](https://azure.microsoft.com/blog/iot-hub-throttling-and-you/).

Mezi další referenční témata v tomto průvodci vývojáři služby IoT Hub patří:

* [Koncové body IoT Hubu](iot-hub-devguide-endpoints.md)
