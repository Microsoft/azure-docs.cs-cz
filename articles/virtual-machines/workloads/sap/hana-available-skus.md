---
title: SKU pro SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
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
ms.date: 02/21/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aad4e3ff3df8b4aeecbbbee7883ba383b9fd0d9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617267"
---
# <a name="available-skus-for-hli"></a>Dostupné skladové položky pro HLI

Služba SAP HANA on Azure (Large Instances) založená na razítkách revize 3 je dostupná v několika konfiguracích v oblastech Azure:

- USA – západ
- USA – východ
- Austrálie – východ
- Austrálie – jihovýchod
- Západní Evropa
- Severní Evropa
- Japonsko – východ
- Japonsko – západ

Služba SAP HANA on Azure (Large Instances) založená na razítkách revize 4 je dostupná v několika konfiguracích v oblastech Azure:

- USA – západ 2
- USA – východ
- USA – středojih
- Západní Evropa
- Severní Evropa



[SAP HANA certifikované skum hana velké instance](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) seznamu, jako jsou:

| Řešení SAP | Procesor | Memory (Paměť) | Úložiště | Dostupnost |
| --- | --- | --- | --- | --- |
| Optimalizováno pro OLAP: SAP BW, BW/4HANA<br /> nebo SAP HANA pro obecné úlohy OLAP | SAP HANA v Azure S72<br /> – 2 x Intel® Xeon® procesor E7-8890 v3<br /> 36 procesorových jader a 72 vláken procesoru |  768 GB |  3 TB | Již není nabízeno |
| --- | SAP HANA v Azure S144<br /> – 4 x Intel® Xeon® procesor E7-8890 v3<br /> 72 procesorových jader a 144 vláken procesoru |  1,5 TB |  6 TB | Již není nabízeno |
| --- | SAP HANA v Azure S192<br /> – 4 x Intel® Xeon® procesor E7-8890 v4<br /> 96 procesorových jader a 192 vláken procesoru |  2,0 TB |  8 TB | Již není nabízeno |
| --- | SAP HANA v Azure S224<br /> – 4 x Procesor Intel® Xeon® Platinum 8276 (také známý jako Cascade lake)<br /> 112 procesorových jader a 224 vláken procesoru |  3,0 TB |  6,3 TB | K dispozici v razítkech Revision3 a Revision4  |
| --- | SAP HANA v Azure S384<br /> – 8 x Intel® Xeon® procesor E7-8890 v4<br /> 192 procesorových jader a 384 vláken procesoru |  4,0 TB |  16 TB | K dispozici v razítkách Revision4 |
| Optimalizováno pro OLTP: SAP Business Suite<br /> na SAP HANA nebo S/4HANA (OLTP),<br /> obecný OLTP | SAP HANA v Azure S72m<br /> – 2 x Intel® Xeon® procesor E7-8890 v3<br /> 36 procesorových jader a 72 vláken procesoru |  1,5 TB |  6 TB | Již není nabízeno |
|---| SAP HANA v Azure S144m<br /> – 4 x Intel® Xeon® procesor E7-8890 v3<br /> 72 procesorových jader a 144 vláken procesoru |  3,0 TB |  12 TB | Již není nabízeno |
|---| SAP HANA v Azure S192m<br /> – 4 x Intel® Xeon® procesor E7-8890 v4<br /> 96 procesorových jader a 192 vláken procesoru  |  4,0 TB |  16 TB | Již není nabízeno |
| --- | SAP HANA v Azure S224m<br /> – 4 x Procesor Intel® Xeon® Platinum 8276 (také známý jako Cascade lake)<br /> 112 procesorových jader a 224 vláken procesoru |  6,0 TB |  10,5 TB | K dispozici v razítkech Revision3 a Revision4  |
|---| SAP HANA v Azure S384m<br /> – 8 x Intel® Xeon® procesor E7-8890 v4<br /> 192 procesorových jader a 384 vláken procesoru |  6,0 TB |  18 TB | K dispozici v razítkách Revision4|
|---| SAP HANA v Azure S384xm<br /> – 8 x Intel® Xeon® procesor E7-8890 v4<br /> 192 procesorových jader a 384 vláken procesoru |  8,0 TB |  22 TB |  K dispozici v razítkách Revision4 |
|---| SAP HANA v Azure S576m<br /> – 12 x Procesor Intel® Xeon® E7-8890 v4<br /> 288 procesorových jader a 576 vláken procesoru |  12,0 TB |  28 TB | K dispozici v razítkách Revision4|
|---| SAP HANA v Azure S768m<br /> – 16 x Intel® Xeon® procesor E7-8890 v4<br /> 384 procesorových jader a 768 vláken procesoru |  16,0 TB |  36 TB | K dispozici v razítkách Revision4|
|---| SAP HANA v Azure S960m<br /> – 20 x Intel® Xeon® procesor E7-8890 v4<br /> 480 procesorových jader a 960 vláken procesoru |  20,0 TB |  46 TB | K dispozici v razítkách Revision4|


V rámci SAP HANA TDIv5 sap umožňuje specifické pro zákazníka velikosti a specifické pro zákazníka projekty, které by mohly vést k konfiguraci serveru, které nejsou uvedeny jako certifikované v:

- [Zařízení s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/appliances.html)
- [Platformy IaaS s certifikací SAP HANA](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)

V mnoha případech tyto konfigurace serveru specifické pro zákazníka nesou více paměti než serverové jednotky certifikované systémem SAP. Při práci se společností SAP mají zákazníci možnost získat podporu SAP a certifikovat pro konfigurace serverů specifické pro zákazníka. V Azure jsou k dispozici následující standardní sloky hana velké instance a v ceníku Microsoftu pro takové projekty velikosti specifické pro zákazníka TDIv5.

| Skladová jednotka (SKU)|Procesor | Memory (Paměť) | Úložiště | Dostupnost |
| ---| --- | --- | --- | --- |
| S96 | SAP HANA v Azure S96<br /> – 2 x Intel® Xeon® procesor E7-8890 v4<br /> 48 procesorových jader a 96 vláken procesoru |  768 GB |  3 TB | K dispozici v razítkech Revision3 a Revision4|


| Původní Skladová položka, kterou lze <br /> rozšířené v paměti | Procesor | Memory (Paměť) | Úložiště | Dostupnost |
| --- | --- | --- | --- | --- |
| S192m lze rozšířit na | SAP HANA v Azure S192xm<br /> – 4 x Intel® Xeon® procesor E7-8890 v4<br /> 96 procesorových jader a 192 vláken procesoru |  6,0 TB |  16 TB | Již není nabízeno |
| S384xm lze rozšířit na | SAP HANA v Azure S384xxm<br /> – 8 x Intel® Xeon® procesor E7-8890 v4<br /> 192 procesorových jader a 384 vláken procesoru |  12,0 TB |  28 TB | K dispozici v razítkách Revision4 |
| S576m lze rozšířit na | SAP HANA v Azure S576xm<br /> – 12 x Procesor Intel® Xeon® E7-8890 v4<br /> 288 procesorových jader a 576 vláken procesoru |  18,0 TB |  41 TB | K dispozici v razítkách Revision4|
| S768m lze rozšířit na | SAP HANA v Azure S768xm<br /> – 16 x Intel® Xeon® procesor E7-8890 v4<br /> 384 procesorových jader a 768 vláken procesoru |  24,0 TB |  56 TB | K dispozici v razítkách Revision4 |

- Jádra procesoru = součet procesorových jader bez hypervláken součtu procesorů serverové jednotky.
- Podprocesů procesoru = součet výpočetních vláken poskytovaných jádry procesoru s hyper vlákny součtu procesorů serverové jednotky. Většina jednotek je ve výchozím nastavení konfigurována tak, aby používala technologii Hyper-Threading.
- Na základě doporučení dodavatele S768m, S768xm a S960m nejsou nakonfigurovány pro použití Hyper-Threading pro spuštění SAP HANA.


Konkrétní konfigurace vybrané jsou závislé na zatížení, prostředky procesoru a požadované paměti. Je možné pro úlohy OLTP použít skum, které jsou optimalizovány pro zatížení OLAP. 

Hardwarová základna pro nabídky, s výjimkou jednotek pro projekty velikosti specifické pro zákazníka, jsou sap HANA TDI certifikované. Dvě různé třídy hardwaru rozdělují skustonové sady na:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 a S224m, které jsou označovány jako "třída typu I" SKU.
- S384, S384m, S384xm, S384xxm, S576m, S576xm S768m, S768xm a S960m, které jsou označovány jako "třída typu II" SKU.
- Máte-li zájem o další nabídky S224 SKU od 4,5 TB do 9 TB s Optane, obraťte se na tým účtu Microsoft a získejte další informace. 


Úplné razítko velké instance HANA není přiděleno výhradně pro jednoho zákazníka,&#39;s použitím. Tato skutečnost platí pro racky výpočetních prostředků a prostředků úložiště připojených prostřednictvím síťové infrastruktury nasazené v Azure. Infrastruktura velké instance HANA, jako &quot;je&quot; Azure, nasazuje různé klienty zákazníků, kteří jsou od sebe izolováni v následujících třech úrovních:

- **Síť**: Izolace prostřednictvím virtuálních sítí v rámci razítka velké instance HANA.
- **Úložiště**: Izolace prostřednictvím virtuálních počítačů úložiště, které mají přiřazené svazky úložiště a izolovat svazky úložiště mezi klienty.
- **Výpočetní doba**: Vyhrazené přiřazení serverových jednotek jednomu klientovi. Žádné tvrdé nebo měkké dělení serverových jednotek. Žádné sdílení jednoho serveru nebo hostitelské jednotky mezi klienty. 

Nasazení jednotek velké instance HANA mezi různými tenanty nejsou viditelné navzájem. Jednotky velké instance HANA nasazené v různých tenantech nemohou komunikovat přímo mezi sebou na úrovni razítka velké instance HANA. Pouze hana velké instance jednotky v rámci jednoho klienta můžete komunikovat mezi sebou na úrovni razítka velké instance HANA.

Nasazený tenant v razítku velké instance se přiřadí k jednomu předplatnému Azure pro účely fakturace. Pro síť je přístupná z virtuálních sítí jiných předplatných Azure v rámci stejné registrace Azure. Pokud nasadíte s jiným předplatným Azure ve stejné oblasti Azure, můžete také požádat o oddělené ho hana velké instance tenanta.

Existují významné rozdíly mezi spuštěním SAP HANA na velké instanci HANA a SAP HANA spuštěným na virtuálních počítačích nasazených v Azure:

- Neexistuje žádná vrstva virtualizace pro SAP HANA v Azure (velké instance). Získáte výkon základního holého hardwaru.
- Na rozdíl od Azure je server SAP HANA v Azure (velké instance) vyhrazen pro konkrétního zákazníka. Není možné, že serverová jednotka nebo hostitel je tvrdý nebo měkký rozdělen. V důsledku toho hana velké instance jednotky se používá jako přiřazenjako celek k klientovi a s tím na vás. Restartování nebo vypnutí serveru nevede automaticky k nasazení operačního systému a SAP HANA na jiném serveru. (Pro skutážní jednotek třídy Typu I je jedinou výjimkou, pokud server narazí na problémy a je třeba provést opětovné nasazení na jiném serveru.)
- Na rozdíl od Azure, kde jsou vybrány typy hostitelských procesorů pro nejlepší poměr cena/výkon, typy procesorů vybrané pro SAP HANA v Azure (velké instance) jsou nejvýkonnější z řady procesorů Intel E7v3 a E7v4.

**Další kroky**
- Odkazovat [NA VELIKOSTHLI](hana-sizing.md)
