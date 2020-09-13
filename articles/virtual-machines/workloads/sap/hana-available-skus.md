---
title: SKU pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: SKU pro SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/02/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 670ef515f0397e3cbdf4fc443ac97d7c73a4a374
ms.sourcegitcommit: 9c262672c388440810464bb7f8bcc9a5c48fa326
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89421947"
---
# <a name="available-skus-for-hli"></a>Dostupné skladové položky pro HLI

SAP HANA v Azure (velké instance) na základě revize 3 razítek, je k dispozici v několika konfiguracích v oblastech Azure v:

- Austrálie – východ
- Austrálie – jihovýchod
- Japonsko – východ
- Japonsko – západ

SAP HANA v Azure (velké instance) založené na razítkech revize 4 jsou k dispozici v několika konfiguracích v oblastech Azure v:

- Západní USA 2
- East US
- USA – východ 2
- Středojižní USA
- West Europe
- Severní Evropa



Seznam dostupných velkých instancí Azure, které jsou nabízeny jako následující:

> [!IMPORTANT]
> Mějte na paměti, že první sloupec představuje stav certifikace HANA pro každý z typů velkých instancí v seznamu. Sloupec by měl být v relaci s [SAP HANAým hardwarovým adresářem](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) pro skladové položky Azure, které začínají písmenem **s** .



| SAP HANA certifikováno | Model | Celková paměť | Paměť DRAM | Optane paměti | Storage | Dostupnost |
| --- | --- | --- | --- | --- | --- | --- |
| ANO <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA v Azure S96<br /> – 2 x Intel® Xeon® procesor E7-8890 v4 <br /> 48 procesorových jader a 96 PROCESORových vláken |  768 GB | 768 GB | --- | 3,0 TB | K dispozici |
| ANO <br /> [OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA v Azure S224<br /> – 4 x procesor Intel® Xeon® Platinum 8276 <br /> 112 procesorových jader a 224 PROCESORových vláken |  3,0 TB | 3,0 TB | --- | 6,3 TB | K dispozici |
| ANO <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA v Azure S224m<br /> – 4 x procesor Intel® Xeon® Platinum 8276 <br /> 112 procesorových jader a 224 PROCESORových vláken |  6,0 TB | 6,0 TB | --- | 10,5 TB | K dispozici |
| NO | SAP HANA v Azure S224oo<br /> – 4 x procesor Intel® Xeon® Platinum 8276 <br /> 112 procesorových jader a 224 PROCESORových vláken | 4,5 TB |  1,5 TB |  3,0 TB | 8,4 TB | K dispozici |
| NO | SAP HANA v Azure S224om<br /> – 4 x procesor Intel® Xeon® Platinum 8276 <br /> 112 procesorových jader a 224 PROCESORových vláken | 6,0 TB |  3,0 TB |  3,0 TB | 10,5 TB | K dispozici |
| NO | SAP HANA v Azure S224ooo<br /> – 4 x procesor Intel® Xeon® Platinum 8276 <br /> 112 procesorových jader a 224 PROCESORových vláken | 7,5 TB |  1,5 TB |  6,0 TB | 12,7 TB | K dispozici |
| NO | SAP HANA v Azure S224oom<br /> – 4 x procesor Intel® Xeon® Platinum 8276 <br /> 112 procesorových jader a 224 PROCESORových vláken | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB | K dispozici |
| ANO <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA v Azure S384<br /> – 8 x Intel® Xeon® procesor E7-8890 v4<br /> 192 procesorových jader a 384 PROCESORových vláken |  4,0 TB | 4,0 TB | --- | 16 TB | K dispozici |
| ANO <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | SAP HANA v Azure S384m<br /> – 8 x Intel® Xeon® procesor E7-8890 v4<br /> 192 procesorových jader a 384 PROCESORových vláken |  6,0 TB | 6,0 TB | --- | 18 TB |  K dispozici  |
| ANO <br />[OLAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984), [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA v Azure S384xm<br /> – 8 x Intel® Xeon® procesor E7-8890 v4<br /> 192 procesorových jader a 384 PROCESORových vláken |  8,0 TB | 8,0 TB | --- | 28 TB | K dispozici |
| NO | SAP HANA v Azure S448<br /> – 8 x Intel® Xeon® Platinum 8276 procesor <br /> 224 procesorových jader a 448 PROCESORových vláken | 6,0 TB |  6,0 TB |  --- | 10,5 TB | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S448m<br /> – 8 x Intel® Xeon® Platinum 8276 procesor <br /> 224 procesorových jader a 448 PROCESORových vláken | 12,0 TB |  12,0 TB |  --- | 18,9 TB | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S448oo<br /> – 8 x Intel® Xeon® Platinum 8276 procesor <br /> 224 procesorových jader a 448 PROCESORových vláken | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB  | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S448om<br /> – 8 x Intel® Xeon® Platinum 8276 procesor <br /> 224 procesorových jader a 448 PROCESORových vláken | 12,0 TB |  6,0 TB |  6,0 TB | 18,9 TB  | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S448ooo<br /> – 8 x Intel® Xeon® Platinum 8276 procesor <br /> 224 procesorových jader a 448 PROCESORových vláken | 15,0 TB |  3,0 TB |  12,0 TB | 23,2 TB  | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S448oom<br /> – 8 x Intel® Xeon® Platinum 8276 procesor <br /> 224 procesorových jader a 448 PROCESORových vláken | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | K dispozici (jenom Rev 4) |
| ANO <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA v Azure S576m<br /> – 12 x Intel® Xeon® procesor E7-8890 v4<br /> 288 procesorových jader a 576 PROCESORových vláken |  12,0 TB | 12,0 TB | --- | 28 TB | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S576xm<br /> – 12 x Intel® Xeon® procesor E7-8890 v4<br /> 288 procesorových jader a 576 PROCESORových vláken |  18,0 TB | 18.0 | --- |  41 TB | K dispozici |
| ANO <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA v Azure S768m<br /> – 16 x Intel® Xeon® procesor E7-8890 v4<br /> 384 procesorových jader a 768 PROCESORových vláken |  16,0 TB | 16,0 TB | -- | 36 TB | K dispozici |
| NO | SAP HANA v Azure S768xm<br /> – 16 x Intel® Xeon® procesor E7-8890 v4<br /> 384 procesorových jader a 768 PROCESORových vláken |  24,0 TB | 24,0 TB | --- | 56 TB | K dispozici |
| NO | SAP HANA v Azure S672<br /> – 12 x Intel® Xeon® Platinum 8276 procesor <br /> 336 procesorových jader a 672 PROCESORových vláken | 9,0 TB |  9,0 TB |  --- | 14,7 TB | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S672m<br /> – 12 x Intel® Xeon® Platinum 8276 procesor <br /> 336 procesorových jader a 672 PROCESORových vláken | 18,0 TB |  18,0 TB |  --- | 27,4 TB | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S672oo<br /> – 12 x Intel® Xeon® Platinum 8276 procesor <br /> 336 procesorových jader a 672 PROCESORových vláken | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S672om<br /> – 12 x Intel® Xeon® Platinum 8276 procesor <br /> 336 procesorových jader a 672 PROCESORových vláken | 18,0 TB |  9,0 TB |  9,0 TB | 27,4 TB  | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S672ooo<br /> – 12 x Intel® Xeon® Platinum 8276 procesor <br /> 336 procesorových jader a 672 PROCESORových vláken | 22,5 TB |  4,5 TB |  18,0 TB | 33,7 TB  | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S672oom<br /> – 12 x Intel® Xeon® Platinum 8276 procesor <br /> 336 procesorových jader a 672 PROCESORových vláken | 27,0 TB |  9,0 TB |  18,0 TB | 40,0 TB  | K dispozici (jenom Rev 4) |
| ANO <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA v Azure S896m<br /> – 16 x procesor Intel® Xeon® Platinum 8276 <br /> 448 procesorových jader a 896 PROCESORových vláken | 24,0 TB | 24,0 TB | -- | 35,8 TB | K dispozici |
| NO | SAP HANA v Azure S896<br /> – 16 x procesor Intel® Xeon® Platinum 8276 <br /> 448 procesorových jader a 896 PROCESORových vláken | 12,0 TB |  12,0 TB |  --- | 18,9 TB | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S896oo<br /> – 16 x procesor Intel® Xeon® Platinum 8276 <br /> 448 procesorových jader a 896 PROCESORových vláken | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S896om<br /> – 16 x procesor Intel® Xeon® Platinum 8276 <br /> 448 procesorových jader a 896 PROCESORových vláken | 24,0 TB |  12,0 TB |  12,0 TB | 35,8 TB  | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S896ooo<br /> – 16 x procesor Intel® Xeon® Platinum 8276 <br /> 448 procesorových jader a 896 PROCESORových vláken | 30,0 TB |  6,0 TB |  24,0 TB | 44,3 TB  | K dispozici (jenom Rev 4) |
| NO | SAP HANA v Azure S896oom<br /> – 16 x procesor Intel® Xeon® Platinum 8276 <br /> 448 procesorových jader a 896 PROCESORových vláken | 36,0 TB |  12,0 TB |  24,0 TB | 52,7 TB  | K dispozici (jenom Rev 4) |
| ANO <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA v Azure S960m<br /> – 20 x Intel® Xeon® procesor E7-8890 v4<br /> 480 procesorových jader a 960 PROCESORových vláken |  20,0 TB | 20,0 TB | -- | 46 TB | K dispozici (jenom Rev 4) |


- PROCESORová jádra = součet procesorů nevyužívajících technologii Hyper-v součtu procesorů serverové jednotky.
- Vlákna procesoru = součet výpočetních vláken poskytovaných jádry procesoru s technologií Hyper-v součtu procesorů serverové jednotky. Většina jednotek je ve výchozím nastavení nakonfigurovaná tak, aby používala technologii Hyper-Threading.
- Na základě doporučení dodavatele S768m, S768xm a S960m nejsou nakonfigurována pro použití technologie Hyper-Threading pro spouštění SAP HANA.


> [!IMPORTANT]
> Následující SKU, i když stále podporovány, už nejdou koupit: S72, S72m, S144, S144m, S192 a S192m. 

Konkrétní zvolené konfigurace závisí na úlohách, prostředcích procesoru a požadované paměti. U úlohy OLTP je možné použít skladové jednotky, které jsou optimalizované pro úlohy OLAP. 

Dvě různé třídy hardwaru rozdělují SKU do:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 a S224m, S224oo, S224om, S224ooo, S224oom, jsou označovány jako "třída I" typu SKU.
- Všechny ostatní SKU jsou označovány jako "typ II Class" SKU.
- Pokud vás zajímá SKU, které ještě nejsou uvedené v adresáři hardwaru SAP, obraťte se na tým účet Microsoft a získejte další informace. 


Úplné razítko pro velké instance HANA není výhradně přiděleno pro jednoho zákazníka&#39;s využitím. Tato skutečnost se vztahuje také na racky výpočetních a úložných prostředků propojených prostřednictvím síťové infrastruktury nasazené v Azure. Infrastruktura velkých instancí HANA, jako je Azure, nasadí &quot; &quot; v následujících třech úrovních různé klienty, které jsou izolované od sebe.

- **Síť**: izolace přes virtuální sítě v rámci razítka velké instance Hana.
- **Storage**: izolace prostřednictvím virtuálních počítačů úložiště, které mají přiřazené svazky úložiště a izolují svazky úložiště mezi klienty.
- **COMPUTE**: vyhrazené přiřazení serverových jednotek k jednomu klientovi. Žádný pevný nebo měkký oddíl jednotek serveru. Žádné sdílení jednoho serveru nebo jednotky hostitele mezi klienty. 

Nasazení velkých jednotek instancí HANA mezi různými klienty nejsou vzájemně viditelná. Jednotky velkých instancí HANA nasazené v různých klientech nemůžou vzájemně komunikovat na úrovni razítka velké instance v HANA přímo mezi sebou. U vysoké úrovně razítka instance HANA můžou vzájemně komunikovat jenom jednotky velkých instancí služby HANA v rámci jednoho tenanta.

Nasazený tenant v označení velkých instancí je přiřazený k jednomu předplatnému Azure pro účely fakturace. V případě sítě je k ní možné přistupovat z virtuálních sítí jiných předplatných Azure v rámci stejného zápisu Azure. Pokud nasadíte s jiným předplatným Azure ve stejné oblasti Azure, můžete si také vybrat, jestli se chcete zeptat na odděleného tenanta rozsáhlých instancí HANA.

Existují významné rozdíly mezi spouštěním SAP HANA velké instance HANA a SAP HANA spuštěné na virtuálních počítačích nasazených v Azure:

- Pro SAP HANA v Azure není k dispozici žádná vrstva virtualizace (velké instance). Získáte výkon základního hardwaru holých počítačů.
- Na rozdíl od Azure je Server SAP HANA v Azure (velké instance) vyhrazený pro konkrétního zákazníka. Není možné, aby jednotka nebo hostitel serveru byly pevně nebo měkké. V důsledku toho se jednotka velkých instancí HANA používá jako celek přiřazená klientovi a s tím. Restartování nebo vypnutí serveru nevede k operačnímu systému automaticky a SAP HANA nasazen na jiném serveru. (U SKU třídy Type, jediná výjimka je, pokud server narazí na problémy a že je nutné provést opětovné nasazení na jiném serveru.)
- Na rozdíl od Azure, kde jsou vybrané typy procesorů hostitelů pro nejlepší poměr cen a výkonu, typy procesorů zvolené pro SAP HANA v Azure (velké instance) jsou nejvyšším výkonem řady procesorů Intel E7v3 a E7v4.

**Další kroky**
- Odkazy na [Velikost HLI](hana-sizing.md)
