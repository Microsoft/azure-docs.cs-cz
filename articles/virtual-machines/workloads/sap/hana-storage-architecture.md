---
title: Architektura úložiště SAP HANA v Azure (velké instance) | Microsoft Docs
description: Architektura úložiště, jak nasadit SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cff0a674e4f07dd38eaab2ffdbafe1f39e44e6ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676854"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Architektura úložiště SAP HANA (velké instance)

Rozložení úložiště pro SAP HANA v Azure (velké instance) se konfiguruje pomocí SAP HANA v modelu nasazení Classic podle doporučených pokynů pro SAP. Pokyny jsou popsány v dokumentu White Paper [požadavky na úložiště SAP HANA](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) .

Velká instance HANA třídy Type se dodává se čtyřnásobným objemem paměti jako svazek úložiště. Pro třídu typu II velkých instancí HANA není úložiště čtyřikrát víc. Jednotky se dodávají se svazkem, který je určen pro ukládání záloh protokolu transakcí HANA. Další informace najdete v tématu [instalace a konfigurace SAP Hana (velké instance) v Azure](hana-installation.md).

V následující tabulce najdete informace o přidělení úložiště. Tabulka uvádí hrubou kapacitu pro různé svazky dodávané s různými jednotkami velkých instancí HANA.

| SKU velkých instancí HANA | Hana/data | Hana/protokol | Hana/Shared | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3 328 GB | 768 GB |1 280 GB | 768 GB |
| S96 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4 608 GB | 1 024 GB | 1 536 GB | 1 024 GB |
| S192m | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S192xm |  11 520 GB |  1 536 GB |  1 792 GB |  1 536 GB |
| S384 | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S384m | 12 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xm | 16 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xxm |  20 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S576m | 20 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S576xm | 31 744 GB | 4 096 GB | 2 048 GB | 4 096 GB |
| S768m | 28 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S768xm | 40 960 GB | 6 144 GB | 4 096 GB | 6 144 GB |
| S960m | 36 000 GB | 4 100 GB | 2 050 GB | 4 100 GB |
| S896m | 33 792 GB | 512 GB | 1 024 GB | 512 GB |

Novější SKU velkých instancí HANA se dodávají s konfiguracemi úložiště, které vypadají takto:

| SKU velkých instancí HANA | Hana/data | Hana/protokol | Hana/Shared | Hana/logbackups |
| --- | --- | --- | --- | --- |
| S224 | 4 224 GB | 512 GB | 1 024 GB | 512 GB |
| S224oo | 6 336 GB | 512 GB | 1 024 GB | 512 GB |
| S224m | 8 448 GB | 512 GB | 1 024 GB | 512 GB |
| S224om | 8 448 GB | 512 GB | 1 024 GB | 512 GB |
| S224ooo | 10 560 GB | 512 GB | 1 024 GB | 512 GB |
| S224oom | 12 672 GB | 512 GB | 1 024 GB | 512 GB |
| S448 | 8 448 GB | 512 GB | 1 024 GB | 512 GB |
| S448oo | 12 672 GB | 512 GB | 1 024 GB | 512 GB |
| S448m | 16 896 GB | 512 GB | 1 024 GB | 512 GB |
| S448om | 16 896 GB | 512 GB | 1 024 GB | 512 GB |
| S448ooo | 21 120 GB | 512 GB | 1 024 GB | 512 GB |
| S448oom | 25 344 GB | 512 GB | 1 024 GB | 512 GB |
| S672 | 12 672 GB | 512 GB | 1 024 GB | 512 GB |
| S672oo | 19 008 GB | 512 GB | 1 024 GB | 512 GB |
| S672m | 25 344 GB | 512 GB | 1 024 GB | 512 GB |
| S672om | 25 344 GB | 512 GB | 1 024 GB | 512 GB |
| S672ooo | 31 680 GB | 512 GB | 1 024 GB | 512 GB |
| S672oom | 38 016 GB | 512 GB | 1 024 GB | 512 GB |
| S896 | 16 896 GB | 512 GB | 1 024 GB | 512 GB |
| S896oo | 25 344 GB | 512 GB | 1 024 GB | 512 GB |
| S896om | 33 792 GB | 512 GB | 1 024 GB | 512 GB |
| S896ooo | 42 240 GB | 512 GB | 1 024 GB | 512 GB |
| S896oom | 50 688 GB | 512 GB | 1 024 GB | 512 GB |


Skutečné nasazené svazky se můžou lišit v závislosti na nasazení a na nástroji, který se používá k zobrazení velikosti svazků.

Pokud rozdělíte skladovou jednotku velkých instancí HANA, může to mít několik příkladů možných dělení:

| Oddíl paměti v GB | Hana/data | Hana/protokol | Hana/Shared | Hana/protokol/zálohování |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| 1 024 | 1 792 GB | 640 GB | 1 024 GB | 640 GB |
| 1 536 | 3 328 GB | 768 GB | 1 280 GB | 768 GB |


Tyto velikosti představují Přibližná čísla svazků, která se mírně liší v závislosti na nasazení a nástrojích, které se používají k prohlédnutí na svazky. K dispozici jsou také jiné velikosti oddílů, například 2,5 TB. Tyto velikosti úložiště se vypočtou vzorcem podobným tomu, který se používá pro předchozí oddíly. Pojem "oddíly" neznamená, že je operační systém, paměť nebo prostředky procesoru jakýmkoli způsobem rozdělen do oddílů. Označuje oddíly úložiště pro různé instance HANA, které můžete chtít nasadit na jednu jednotku velkých instancí HANA. 

Možná budete potřebovat další úložiště. Úložiště můžete přidat tak, že si zakoupíte další úložiště v jednotkách o 1,5 TB. Toto dodatečné úložiště je možné přidat jako další svazek. Dá se taky použít k prodloužení jednoho nebo více stávajících svazků. Není možné zmenšit velikosti svazků v původním nasazení a hlavně zdokumentované předchozími tabulkami. Není také možné změnit názvy svazků nebo přípojných názvů. Výše popsané svazky úložiště jsou připojené k jednotkám velkých instancí HANA jako NFS4 svazky.

Snímky úložiště můžete použít pro účely zálohování a obnovení a zotavení po havárii. Další informace najdete v tématu [SAP Hana (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md).

Podrobnosti o rozložení úložiště pro váš scénář najdete v tématu [podporované scénáře HLI](hana-supported-scenario.md) .

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Spustit více instancí SAP HANA u jedné jednotky velkých instancí HANA

Je možné hostovat více než jednu instanci Active SAP HANA v jednotkách velkých instancí HANA. Aby bylo možné zajistit možnosti snímků úložiště a zotavení po havárii, tato konfigurace vyžaduje sadu svazků nastavenou na jednu instanci. V současné době je možné jednotky velkých instancí HANA rozdělit následujícím způsobem:

- **S72, S72m, S96, S144, S192**: v přírůstcích po 256 gb a 256 GB nejmenší počáteční jednotka. Jiné zvýšení hodnoty, například 256 GB a 512 GB, lze kombinovat až do maximální velikosti paměti jednotky.
- **S144m a S192m**: v přírůstcích po 256 GB, přičemž nejmenší jednotka je 512 GB. Jiné zvýšení hodnoty, například 512 GB a 768 GB, lze kombinovat až do maximální velikosti paměti jednotky.
- **Typ třídy II**: v přírůstcích po 512 GB s nejmenší počáteční jednotkou 2 TB. Různá zvýšení, například 512 GB, 1 TB a 1,5 TB, lze kombinovat až do maximální velikosti paměti jednotky.

Několik příkladů spuštění více instancí SAP HANA může vypadat podobně jako v následujícím příkladu.

| SKU | Velikost paměti | Velikost úložiště | Velikosti s více databázemi |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | instance 1x768-GB HANA<br /> nebo instance 1x512-GB + 1x256-GB<br /> nebo 3x256 Instances-GB | 
| S72m | 1,5 TB | 6 TB | instance 3x512GB HANA<br />nebo instance 1x512-GB instance + 1x1-TB<br />nebo 6x256 Instances-GB<br />nebo 1x 1,5 TB instance | 
| S192m | 4 TB | 16 TB | 8x512-GB instancí<br />nebo 4x1 instance v TB<br />nebo instance 4x512-GB + instance 2x1-TB<br />nebo 4x768 Instances + instance 2x512-GB<br />nebo instance 1x4-TB |
| S384xm | 8 TB | 22 TB | instance 4x2-TB<br />nebo 2x4 instance v TB<br />nebo instance 2x3-TB + instance 1x2-TB<br />nebo 2x 2,5-TB instancí + 1x3 Instances-TB<br />nebo instance 1x8-TB |


K dispozici jsou také jiné varianty. 

## <a name="encryption-of-data-at-rest"></a>Šifrování dat v klidovém umístění
Úložiště používané pro velkou instanci HANA používá transparentní šifrování dat, která jsou uložená na discích od konce roku 2018. V dřívějších nasazeních se můžete rozhodnout pro získání šifrovaných svazků. Pokud jste s touto možností rozhodnuti, můžete požádat o získání šifrovaných svazků online. Přesun z nešifrovaného na šifrované svazky je transparentní a nevyžaduje výpadky. 

U třídy typu SKU je svazek, na kterém je spouštěcí logická jednotka uložená, zašifrovaný. V revizi 3 HANA velká instance s použitím třídy Type II SKU velké instance HANA budete muset zašifrovat spouštěcí logickou jednotku pomocí metod operačního systému. V revizních razítkech s velkým počtem instancí revize 4 HANA používá jednotka Type II svazek, který je spouštěcí LUN uložený a ve výchozím nastavení je šifrovaný. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Požadovaná nastavení pro větší instance HANA na velkých instancích HANA
Úložiště používané ve velkých instancích HANA má omezení velikosti souboru. [Omezení velikosti je 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) na jeden soubor. Na rozdíl od omezení velikosti souborů v systémech souborů EXT3 úložiště HANA neví implicitně omezení úložiště vynutilé úložištěm velkých instancí HANA. V důsledku toho HANA nevytvoří automaticky nový datový soubor, když je dosaženo limitu velikosti souboru 16 TB. Vzhledem k tomu, že HANA se pokusí zvětšit soubor nad rámec 16 TB, HANA odešle zprávy o chybách a indexový server selže na konci.

> [!IMPORTANT]
> Aby se zabránilo tomu, že se HANA snaží rozšiřovat datové soubory nad rámec velikosti souborů o velikosti 16 TB úložiště velkých instancí HANA, musíte nastavit následující parametry v konfiguračním souboru global.ini pro HANA.
> 
> - datavolume_striping = true
> - datavolume_striping_size_gb = 15000
> - Viz také SAP – Poznámka [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Pamatujte na poznámku ke SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Další kroky**
- Odkazy [na podporované scénáře pro velké instance Hana](hana-supported-scenario.md)