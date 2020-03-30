---
title: Architektura úložiště SAP HANA v Azure (velké instance) | Dokumenty společnosti Microsoft
description: Architektura úložiště, jak nasadit SAP HANA v Azure (velké instance).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a12c454906d6c6ff702b7f635a91361bbe3994c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616895"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Architektura úložiště SAP HANA (velké instance)

Rozložení úložiště pro SAP HANA v Azure (velké instance) je nakonfigurováno SAP HANA na klasickém modelu nasazení podle doporučených pokynů SAP. Pokyny jsou zdokumentovány v bílé knize [požadavků na úložiště SAP HANA.](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)

Velká instance HANA třídy Typu I je dodávána se čtyřnásobným objemem paměti jako svazek úložiště. Pro třídu Type II jednotek velké instance HANA není úložiště čtyřikrát více. Jednotky jsou dodávány se svazkem, který je určen pro ukládání záloh protokolu transakcí HANA. Další informace najdete [v tématu Instalace a konfigurace SAP HANA (velké instance) v Azure](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Informace o přidělení úložiště naleznete v následující tabulce. V tabulce je uvedena hrubá kapacita pro různé svazky dodávané s různými jednotkami velké instance HANA.

| Velká soupoložka instance HANA | hana/data | hana/log | hana/sdílel(a) | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3 328 GB | 768 GB |1 280 GB | 768 GB |
| S96 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4 608 GB | 1 024 GB | 1 536 GB | 1 024 GB |
| S192m | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S192xm |  11 520 GB |  1 536 GB |  1 792 GB |  1 536 GB |
| S224 |  4 224 GB |  512 GB |  1 024 GB |  512 GB |
| S224m |  8 448 GB |  512 GB |  1 024 GB |  512 GB |
| S384 | 11 520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S384m | 12 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xm | 16 000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xxm |  20 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S576m | 20 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S576xm | 31 744 GB | 4 096 GB | 2 048 GB | 4 096 GB |
| S768m | 28 000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S768xm | 40 960 GB | 6 144 GB | 4 096 GB | 6 144 GB |
| S960m | 36 000 GB | 4 100 GB | 2 050 GB | 4 100 GB |


Skutečné nasazené svazky se mohou lišit v závislosti na nasazení a nástroji, který se používá k zobrazení velikosti svazku.

Pokud rozdělíte skladovou položku velké instance HANA, může vypadat několik příkladů možných divizních částí:

| Oddíl paměti v GB | hana/data | hana/log | hana/sdílel(a) | hana/log/zálohování |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| 1,024 | 1 792 GB | 640 GB | 1 024 GB | 640 GB |
| 1 536 | 3 328 GB | 768 GB | 1 280 GB | 768 GB |


Tyto velikosti jsou hrubá čísla svazků, která se mohou mírně lišit v závislosti na nasazení a nástrojích používaných k pohledu na svazky. Existují také další velikosti oddílů, například 2,5 TB. Tyto velikosti úložiště se počítají se vzorcem podobným tomu, který se používá pro předchozí oddíly. Termín "oddíly" neznamená, že operační systém, paměť nebo prostředky procesoru jsou v žádném případě rozděleny do oddílů. Označuje oddíly úložiště pro různé instance HANA, které můžete chtít nasadit na jedné jednotce velké instance HANA. 

Možná budete potřebovat více úložného prostoru. Úložiště můžete přidat zakoupením dalšího úložiště v jednotkách s 1 TB. Toto další úložiště lze přidat jako další svazek. Lze jej také rozšířit k rozšíření jednoho nebo více existujících svazků. Není možné zmenšit velikost svazků, jak byly původně nasazeny a většinou dokumentovány předchozími tabulkami. Také není možné změnit názvy svazků nebo názvů připojení. Dříve popsané svazky úložiště jsou připojeny k jednotkám velké instance HANA jako svazky NFS4.

Snímky úložiště můžete použít pro účely zálohování a obnovení a zotavení po havárii. Další informace najdete v [tématu SAP HANA (velké instance) vysoká dostupnost a zotavení po havárii v Azure](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Odkazovat [HLI podporované scénáře](hana-supported-scenario.md) pro podrobnosti rozložení úložiště pro váš scénář.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Spuštění více instancí SAP HANA na jedné jednotce velké instance HANA

Je možné hostovat více než jednu aktivní instanci SAP HANA na jednotkách velké instance HANA. Chcete-li poskytnout možnosti snímků úložiště a zotavení po havárii, taková konfigurace vyžaduje sadu svazků pro instanci. V současné době mohou být jednotky velké instance HANA rozděleny takto:

- **S72, S72m, S96, S144, S192**: V krocích po 256 GB, s 256 GB nejmenší startovací jednotkou. Různé přírůstky, například 256 GB a 512 GB, lze kombinovat s maximální pamětí jednotky.
- **S144m a S192m**: V krocích po 256 GB, s nejmenší jednotkou 512 GB. Různé přírůstky, například 512 GB a 768 GB, lze kombinovat s maximální pamětí jednotky.
- **Třída typu II**: V krocích po 512 GB s nejmenší startovací jednotkou 2 TB. Různé přírůstky, například 512 GB, 1 TB a 1,5 TB, lze kombinovat s maximální pamětí jednotky.

Několik příkladů spuštění více instancí SAP HANA může vypadat takto.

| Skladová jednotka (SKU) | Velikost paměti | Velikost úložiště | Velikosti s více databázemi |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | Instance HANA 1x768 GB<br /> nebo instance 1x512-GB + instance 1x256 GB<br /> nebo instance 3x256 GB | 
| S72m | 1,5 TB | 6 TB | Instance 3x512GB HANA<br />nebo instance 1x512-GB + instance 1x1 TB<br />nebo instance 6x256 GB<br />nebo instance 1x1.5-TB | 
| S192m | 4 TB | 16 TB | Instance 8x512 GB<br />nebo instance o velikosti 4x1 TB<br />nebo instance 4x512 GB + instance 2x1 TB<br />nebo instance 4x768 GB + instance 2x512 GB<br />nebo instance 1x4 TB |
| S384xm | 8 TB | 22 TB | Instance 4x2 TB<br />nebo instance s kapacitou 2 x 4 TB<br />nebo instance o velikosti 2x3 TB + instance 1x2 TB<br />nebo instance o velikosti 2x2,5 TB + instance 1x3 TB<br />nebo instance 1x8 TB |


Existují i jiné varianty. 

## <a name="encryption-of-data-at-rest"></a>Šifrování dat v klidovém stavu
Úložiště používané pro velké instance HANA používá transparentní šifrování pro data, protože jsou uložena na discích od konce roku 2018. V dřívějších nasazeních můžete zvolit, zda chcete svazky zašifrovat. Pokud jste se rozhodli proti této možnosti, můžete požádat o zašifrování svazků online. Přechod z nešifrovaných na šifrované svazky je transparentní a nevyžaduje prostoje. 

U třídy Skladové jednotky typu I je zašifrován svazek, na které je spouštěcí logická jednotka uložena. V revizi 3 HANA velká instance razítka pomocí třídy Type II skum hana velké instance, je třeba šifrovat spouštěcí logické jednotky s metodami operačního systému. V razítkách velké instance revize 4 HANA je pomocí jednotek typu II svazek uložen spouštěcí logická jednotka a ve výchozím nastavení je také zašifrována v klidovém stavu. 

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Požadovaná nastavení pro větší instance HANA ve velkých instancích HANA
Úložiště používané v velkých instancích HANA má omezení velikosti souboru. [Omezení velikosti je 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) na soubor. Na rozdíl od omezení velikosti souborů v systémech souborů EXT3 hana není implicitně vědoma omezení úložiště vynuceného úložištěm velkých instancí HANA. V důsledku toho HANA automaticky nevytvoří nový datový soubor po dosažení limitu velikosti souboru 16 TB. Jako HANA pokusí zvětšit soubor nad 16 TB, HANA bude hlásit chyby a indexový server se zhroutí na konci.

> [!IMPORTANT]
> Chcete-li zabránit hana pokusu o zvýšení datových souborů nad 16 TB limit velikosti souboru HANA velké instance úložiště, je třeba nastavit následující parametry v global.ini konfigurační soubor HANA
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Viz také [SAP](https://launchpad.support.sap.com/#/notes/2400005) poznámka #2400005
> - Uvědomte si poznámku SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Další kroky**
- Odkazovat [na podporované scénáře pro velké instance HANA](hana-supported-scenario.md)