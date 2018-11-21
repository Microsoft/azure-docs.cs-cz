---
title: SKU pro SAP HANA v Azure (velké instance) | Dokumentace Microsoftu
description: SKU pro SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/20/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9358f280efb847bdce802cc3a20e64710ffc3214
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52275380"
---
# <a name="available-skus-for-hli"></a>Dostupné skladové položky pro HLI

SAP HANA v Azure (velké instance) služba je k dispozici v několika konfigurací v oblasti Azure USA – západ a USA – východ, Austrálie – východ, Austrálie – jihovýchod, západní Evropa, Severní Evropa, Japonsko – východ a Japonsko – západ.

[Velké instance HANA SKU s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) seznamu jako:

| Řešení SAP | Procesor | Memory (Paměť) | Úložiště | Dostupnost |
| --- | --- | --- | --- | --- |
| Optimalizováno pro OLAP: SAP BW, BW/4HANA<br /> nebo SAP HANA pro obecné úlohy OLAP | SAP HANA v Azure S72<br /> – 2 x procesor Intel® Xeon® E7-8890 v3<br /> 36 Procesorových jader a 72 vlákna CPU |  768 GB |  3 TB | K dispozici. |
| --- | SAP HANA v Azure S144<br /> – 4 x procesor Intel® Xeon® E7-8890 v3<br /> 72 jader procesoru a 144 vlákna CPU |  1,5 TB |  6 TB | Nenabízí se už |
| --- | SAP HANA v Azure S192<br /> – 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 jader procesoru a 192 vlákna CPU |  2.0 TB |  8 TB | K dispozici. |
| --- | SAP HANA v Azure S384<br /> –. 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 Procesorových jader a 384 vlákna CPU |  4.0 TB |  16 TB | K dispozici. |
| Optimalizováno pro OLTP: SAP Business Suite<br /> v SAP HANA nebo S/4HANA (OLTP)<br /> Obecné OLTP | SAP HANA v Azure S72m<br /> – 2 x procesor Intel® Xeon® E7-8890 v3<br /> 36 Procesorových jader a 72 vlákna CPU |  1,5 TB |  6 TB | K dispozici. |
|---| SAP HANA v Azure S144m<br /> – 4 x procesor Intel® Xeon® E7-8890 v3<br /> 72 jader procesoru a 144 vlákna CPU |  3.0 TB |  12 TB | Nenabízí se už |
|---| SAP HANA v Azure S192m<br /> – 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 jader procesoru a 192 vlákna CPU  |  4.0 TB |  16 TB | K dispozici. |
|---| SAP HANA v Azure S384m<br /> –. 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 Procesorových jader a 384 vlákna CPU |  6.0 TB |  18 TB | K dispozici. |
|---| SAP HANA v Azure S384xm<br /> –. 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 Procesorových jader a 384 vlákna CPU |  8.0 TB |  22 TB |  K dispozici. |
|---| SAP HANA v Azure S576m<br /> – 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 Procesorových jader a 576 vlákna CPU |  12.0 TB |  28 TB | K dispozici. |
|---| SAP HANA v Azure S768m<br /> až 16 x procesor Intel® Xeon® E7-8890 v4<br /> 384 Procesorových jader a 768 vlákna CPU |  16.0 TB |  36 TB | K dispozici. |
|---| SAP HANA v Azure S960m<br /> – 20 x procesor Intel® Xeon® E7-8890 v4<br /> 480 Procesorových jader a 960 vlákna CPU |  20.0 TB |  46 TB | K dispozici. |


V části TDIv5 SAP HANA SAP umožňuje zákaznického velikosti a zákaznické projekty, které by mohly vést k konfigurace serveru, které nejsou uvedeny jako certifikuje:

- [SAP HANA certifikovaných zařízení](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Platformy IaaS s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

V mnoha případech tyto konfigurace specifické pro zákazníka server provádět více paměti, než je server jednotky certifikace SAP. Při práci s řešením SAP, zákazníci mají možnost získat podpory SAP a certifikace pro jejich konfigurace specifické pro zákazníka velikosti serveru. Následující standardní SKU velká Instance HANA v Azure jsou k dispozici a v Microsoftu ceník pro tyto projekty TDIv5 zákaznického velikosti.

| Skladová jednotka (SKU)|Procesor | Memory (Paměť) | Úložiště | Dostupnost |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA v Azure S96<br /> – 4 x procesor Intel® Xeon® E7-8890 v4<br /> 48 Procesorových jader a 96 vlákna CPU |  768 TB |  3 TB | K dispozici. |


| Původní skladovou Položku, kterou může být <br /> Rozšířené v paměti | Procesor | Memory (Paměť) | Úložiště | Dostupnost |
| --- | --- | --- | --- | --- |
| S192m je možné rozšířit na | SAP HANA v Azure S192xm<br /> – 4 x procesor Intel® Xeon® E7-8890 v4<br /> 96 jader procesoru a 192 vlákna CPU |  6.0 TB |  16 TB | K dispozici. |
| S384xm je možné rozšířit na | SAP HANA v Azure S384xxm<br /> –. 8 x procesor Intel® Xeon® E7-8890 v4<br /> 192 Procesorových jader a 384 vlákna CPU |  12.0 TB |  28 TB | K dispozici. |
| S576m je možné rozšířit na | SAP HANA v Azure S576xm<br /> – 12 x procesor Intel® Xeon® E7-8890 v4<br /> 288 Procesorových jader a 576 vlákna CPU |  18.0 TB |  41 TB | K dispozici. |
| S768m je možné rozšířit na | SAP HANA v Azure S768xm<br /> až 16 x procesor Intel® Xeon® E7-8890 v4<br /> 384 Procesorových jader a 768 vlákna CPU |  24,0 TB |  56 TB | K dispozici. |

- Jádra procesoru = SUMA bez hyper-threaded Procesorových jader součtu procesory jednotky serverů.
- Vlákna CPU = SUMA výpočetní vláken poskytuje hyper-threaded Procesorových jader součtu procesory jednotky serverů. Většina jednotky jsou nakonfigurované ve výchozím nastavení používají technologii Hyper-Threading.
- Podle doporučení dodavatele S768m, S768xm a S960m nejsou nakonfigurovaná pro použití technologie Hyper-Threading pro spuštění SAP HANA.


Jaké konkrétní konfigurace zvolené jsou závislé na úlohy, prostředky procesoru a požadované paměti. Je možné používat SKU, které jsou optimalizované pro úlohy OLAP úlohy OLTP. 

U nabídek, s výjimkou jednotky pro nastavení velikosti zákaznické projekty základní hardware jsou certifikací SAP HANA TDI. Dvě různé třídy hardwaru Rozdělit skladové jednotky do:

- S72 S72m, S96, S144, S144m, S192, S192m a S192xm, které jsou označovány jako "Typ můžu třídy" skladových položek.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm a S960m, což se označuje jako "Typ II třídy" skladových položek.

Kompletní velká Instance HANA razítko není přidělená výhradně pro jediného zákazníka&#39;použijte s. Tato skutečnost platí do stojanů úložnou a výpočetní prostředky připojenými prostřednictvím sítě fabric nasazené v Azure i. Velká Instance HANA infrastruktury, jako je Azure, nasadí různých zákazníků &quot;tenantů&quot; , které jsou izolované od sebe v následujících třech úrovních:

- **Síť**: izolace prostřednictvím virtuálními sítěmi v rámci razítka velká Instance HANA.
- **Úložiště**: izolace prostřednictvím úložiště virtuálních počítačů, které mají přiřazené svazky úložiště a izolovat svazky úložiště mezi tenanty.
- **COMPUTE**: vyhrazené přiřazení jednotek serverů do jednoho tenanta. Žádné pevné nebo obnovitelné dělení server jednotek. Bez sdílení serveru nebo hostitele celek mezi tenanty. 

Nasazení jednotky velká Instance HANA mezi různých tenantech nejsou navzájem viditelné. Velká Instance HANA jednotky nasazených v různých tenantech nemůže komunikovat s jinými na úrovni razítko velká Instance HANA. Pouze velká Instance HANA jednotky v rámci jednoho tenanta může komunikovat mezi sebou na úrovni razítko velká Instance HANA.

Nasazené tenanta v razítku velkou instanci se přiřadí na jedno předplatné Azure pro účely fakturace. Pro síť můžete přistupovat z virtuální sítě z jiných předplatných Azure v rámci stejné registrace v Azure. Pokud provádíte nasazení s jiným předplatným Azure ve stejné oblasti Azure, také můžete požádat o oddělené velká Instance HANA tenanta.

Existují významné rozdíly mezi službou SAP HANA na velká Instance HANA a SAP HANA běžící na virtuálních počítačích nasazených v Azure:

- Neexistuje žádná vrstva virtualizace pro SAP HANA v Azure (velké instance). Získáte výkonu základního hardwaru úplné obnovení systému.
- Na rozdíl od Azure SAP HANA v Azure (velké instance) server je vyhrazen pro konkrétního zákazníka. Není možné, že jednotka serveru nebo hostitele je pevně nebo obnovitelné dělit na oddíly. V důsledku toho jednotka velká Instance HANA se používá jako přiřazená jako celek na tenanta a s ním pro vás. Restartování nebo vypnutí serveru nebude automaticky vést k operačního systému a SAP HANA se nasazuje na jiný server. (Pro typ můžu třídy SKU, jedinou výjimkou je, pokud server zaznamená problémy a opětovné nasazení je třeba provést na jiném serveru.)
- Na rozdíl od Azure, kde typy procesoru hostitele jsou vybrány pro nejlepší poměr cena/výkon, jsou tyto typy procesoru, zvolená pro SAP HANA v Azure (velké instance), nejvyšší provádění řádku procesor Intel E7v3 a E7v4.

**Další kroky**
- Přečtěte si [HLI velikosti](hana-sizing.md)
