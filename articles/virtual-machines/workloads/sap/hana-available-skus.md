---
title: SKU pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: SKU pro SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7961578a1daf67176312d4257a4e86a7091082f0
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2019
ms.locfileid: "67869255"
---
# <a name="available-skus-for-hli"></a>Dostupné skladové položky pro HLI

Služba SAP HANA v Azure (velké instance) založené na revizi 3 razítka je k dispozici v několika konfiguracích v oblastech Azure v nástroji:

- USA – západ
- East US
- Austrálie – východ
- Austrálie – jihovýchod
- Západní Evropa
- Severní Evropa
- Japonsko – východ
- Japonsko – západ

SAP HANA v Azure (velké instance) založené na razítkech revize 4 jsou k dispozici v několika konfiguracích v oblastech Azure v:

- USA – západ 2
- East US
- Západní Evropa
- Severní Evropa



[SAP HANA certifikované SKU seznamu velkých instancí Hana,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) jako je:

| Řešení SAP | Procesor | Memory (Paměť) | Storage | Dostupnost |
| --- | --- | --- | --- | --- |
| Optimalizováno pro OLAP: SAP BW, ČERNOBÍLÉ/4HANA<br /> nebo SAP HANA pro obecné úlohy OLAP | SAP HANA v Azure S72<br /> – 2 x Intel® Xeon® procesor E7-8890 V3<br /> 36 procesorových jader a 72 PROCESORových vláken |  768 GB |  3 TB | Už se nenabízí |
| --- | SAP HANA v Azure S144<br /> – 4 x Intel® Xeon® procesor E7-8890 V3<br /> 72 procesorových jader a 144 PROCESORových vláken |  1,5 TB |  6 TB | Už se nenabízí |
| --- | SAP HANA v Azure S192<br /> – 4 x Intel® Xeon® procesor E7-8890 v4<br /> 96 procesorových jader a 192 PROCESORových vláken |  2,0 TB |  8 TB | K dispozici |
| --- | SAP HANA v Azure S384<br /> – 8 x Intel® Xeon® procesor E7-8890 v4<br /> 192 procesorových jader a 384 PROCESORových vláken |  4,0 TB |  16 TB | K dispozici |
| Optimalizováno pro OLTP: SAP Business Suite<br /> v SAP HANA nebo S/4HANA (OLTP),<br /> Obecné OLTP | SAP HANA v Azure S72m<br /> – 2 x Intel® Xeon® procesor E7-8890 V3<br /> 36 procesorových jader a 72 PROCESORových vláken |  1,5 TB |  6 TB | Už se nenabízí |
|---| SAP HANA v Azure S144m<br /> – 4 x Intel® Xeon® procesor E7-8890 V3<br /> 72 procesorových jader a 144 PROCESORových vláken |  3,0 TB |  12 TB | Už se nenabízí |
|---| SAP HANA v Azure S192m<br /> – 4 x Intel® Xeon® procesor E7-8890 v4<br /> 96 procesorových jader a 192 PROCESORových vláken  |  4,0 TB |  16 TB | K dispozici |
|---| SAP HANA v Azure S384m<br /> – 8 x Intel® Xeon® procesor E7-8890 v4<br /> 192 procesorových jader a 384 PROCESORových vláken |  6,0 TB |  18 TB | K dispozici |
|---| SAP HANA v Azure S384xm<br /> – 8 x Intel® Xeon® procesor E7-8890 v4<br /> 192 procesorových jader a 384 PROCESORových vláken |  8,0 TB |  22 TB |  K dispozici |
|---| SAP HANA v Azure S576m<br /> – 12 x Intel® Xeon® procesor E7-8890 v4<br /> 288 procesorových jader a 576 PROCESORových vláken |  12,0 TB |  28 TB | K dispozici |
|---| SAP HANA v Azure S768m<br /> – 16 x Intel® Xeon® procesor E7-8890 v4<br /> 384 procesorových jader a 768 PROCESORových vláken |  16,0 TB |  36 TB | K dispozici |
|---| SAP HANA v Azure S960m<br /> – 20 x Intel® Xeon® procesor E7-8890 v4<br /> 480 procesorových jader a 960 PROCESORových vláken |  20,0 TB |  46 TB | K dispozici |


V rámci SAP HANA TDIv5 podporuje SAP specifické pro konkrétní zákazníky a pro konkrétní zákazníky, což může vést k konfiguracím serveru, které nejsou uvedené jako certifikované v:

- [Zařízení s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [SAP HANA certifikované platformy IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

V mnoha případech tyto konfigurace serveru specifické pro zákazníka mají více paměti, než je počet certifikovaných jednotek serveru SAP. Při práci s produktem SAP mají zákazníci možnost získat podporu SAP a certifikovat jejich konfigurace serveru pro konkrétní zákazníky. V Azure jsou k dispozici následující standardní skladové jednotky HANA pro velké instance a v ceníku Microsoftu pro konkrétní TDIv5 projekty specifické pro zákazníky.

| SKU|Procesor | Memory (Paměť) | Storage | Dostupnost |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA v Azure S96<br /> – 2 x Intel® Xeon® procesor E7-8890 v4<br /> 48 procesorových jader a 96 PROCESORových vláken |  768 GB |  3 TB | K dispozici |


| Původní SKU, které může být <br /> rozšířené v paměti | Procesor | Memory (Paměť) | Storage | Dostupnost |
| --- | --- | --- | --- | --- |
| S192m se dá rozšířit na | SAP HANA v Azure S192xm<br /> – 4 x Intel® Xeon® procesor E7-8890 v4<br /> 96 procesorových jader a 192 PROCESORových vláken |  6,0 TB |  16 TB | K dispozici |
| S384xm se dá rozšířit na | SAP HANA v Azure S384xxm<br /> – 8 x Intel® Xeon® procesor E7-8890 v4<br /> 192 procesorových jader a 384 PROCESORových vláken |  12,0 TB |  28 TB | K dispozici |
| S576m se dá rozšířit na | SAP HANA v Azure S576xm<br /> – 12 x Intel® Xeon® procesor E7-8890 v4<br /> 288 procesorových jader a 576 PROCESORových vláken |  18,0 TB |  41 TB | K dispozici |
| S768m se dá rozšířit na | SAP HANA v Azure S768xm<br /> – 16 x Intel® Xeon® procesor E7-8890 v4<br /> 384 procesorových jader a 768 PROCESORových vláken |  24,0 TB |  56 TB | K dispozici |

- PROCESORová jádra = součet procesorů nevyužívajících technologii Hyper-v součtu procesorů serverové jednotky.
- Vlákna procesoru = součet výpočetních vláken poskytovaných jádry procesoru s technologií Hyper-v součtu procesorů serverové jednotky. Většina jednotek je ve výchozím nastavení nakonfigurovaná tak, aby používala technologii Hyper-Threading.
- Na základě doporučení dodavatele S768m, S768xm a S960m nejsou nakonfigurována pro použití technologie Hyper-Threading pro spouštění SAP HANA.


Konkrétní zvolené konfigurace závisí na úlohách, prostředcích procesoru a požadované paměti. U úlohy OLTP je možné použít skladové jednotky, které jsou optimalizované pro úlohy OLAP. 

Hardwarová základna pro nabídky s výjimkou jednotek pro projekty velikosti specifické pro zákazníka jsou SAP HANA TDI certifikovány. Dvě různé třídy hardwaru rozdělují SKU do:

- S72, S72m, S96, S144, S144m, S192, S192m a S192xm, které se označují jako třída typu SKU.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm a S960m, které jsou označovány jako "Type II Class" SKU.

Kompletní razítko pro velké instance HANA není výhradně přiděleno pro jednoho&#39;zákazníka s použitím. Tato skutečnost se vztahuje také na racky výpočetních a úložných prostředků propojených prostřednictvím síťové infrastruktury nasazené v Azure. Infrastruktura velkých instancí Hana, jako je Azure, nasadí v &quot;následujících třech&quot; úrovních různé klienty, které jsou izolované od sebe.

- **Síť**: Izolace přes virtuální sítě v rámci razítka velké instance HANA
- **Úložiště**: Izolace prostřednictvím virtuálních počítačů úložiště, které mají přiřazené svazky úložiště a izolují svazky úložiště mezi klienty.
- **Výpočetní**prostředí: Vyhrazené přiřazení serverových jednotek k jednomu klientovi. Žádný pevný nebo měkký oddíl jednotek serveru. Žádné sdílení jednoho serveru nebo jednotky hostitele mezi klienty. 

Nasazení velkých jednotek instancí HANA mezi různými klienty nejsou vzájemně viditelná. Jednotky velkých instancí HANA nasazené v různých klientech nemůžou vzájemně komunikovat na úrovni razítka velké instance v HANA přímo mezi sebou. U vysoké úrovně razítka instance HANA můžou vzájemně komunikovat jenom jednotky velkých instancí služby HANA v rámci jednoho tenanta.

Nasazený tenant v označení velkých instancí je přiřazený k jednomu předplatnému Azure pro účely fakturace. V případě sítě je k ní možné přistupovat z virtuálních sítí jiných předplatných Azure v rámci stejného zápisu Azure. Pokud nasadíte s jiným předplatným Azure ve stejné oblasti Azure, můžete si také vybrat, jestli se chcete zeptat na odděleného tenanta rozsáhlých instancí HANA.

Existují významné rozdíly mezi spouštěním SAP HANA velké instance HANA a SAP HANA spuštěné na virtuálních počítačích nasazených v Azure:

- Pro SAP HANA v Azure není k dispozici žádná vrstva virtualizace (velké instance). Získáte výkon základního hardwaru holých počítačů.
- Na rozdíl od Azure je Server SAP HANA v Azure (velké instance) vyhrazený pro konkrétního zákazníka. Není možné, aby jednotka nebo hostitel serveru byly pevně nebo měkké. V důsledku toho se jednotka velkých instancí HANA používá jako celek přiřazená klientovi a s tím. Restartování nebo vypnutí serveru nevede k operačnímu systému automaticky a SAP HANA nasazen na jiném serveru. (U SKU třídy Type, jediná výjimka je, pokud server narazí na problémy a že je nutné provést opětovné nasazení na jiném serveru.)
- Na rozdíl od Azure, kde jsou vybrané typy procesorů hostitelů pro nejlepší poměr cen a výkonu, typy procesorů zvolené pro SAP HANA v Azure (velké instance) jsou nejvyšším výkonem řady procesorů Intel E7v3 a E7v4.

**Další postup**
- Odkazy na [Velikost HLI](hana-sizing.md)
