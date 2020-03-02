---
title: Mapování struktury složek na Azure File Sync topologii
description: Mapování existující struktury souborů a složek na sdílené složky Azure pro použití s Azure File Sync. Společný textový blok, který je sdílen mezi dokumenty migrace.
author: fauhse
ms.service: storage
ms.topic: conceptual
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 44fb95de88cc86c802e5ba72f0b5379b8708c506
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209555"
---
V tomto kroku vyhodnocujete, kolik sdílených souborů Azure potřebujete. Jeden server (nebo cluster) systému Windows může synchronizovat až 30 sdílených složek Azure.

V StorSimple můžete mít více složek, které aktuálně sdílíte místně jako sdílené složky SMB pro uživatele a aplikace. Nejjednodušším způsobem je, aby místní sdílená složka namapovala 1:1 na sdílenou složku Azure. Pokud je toto číslo spravovatelně malé, což znamená méně než 30 pro jeden server Windows, nebo plánujete, že máte dva servery Windows (60) atd., doporučuje se mapování 1:1.

Pokud máte více sdílených složek než 30, často není nutné namapovat místní sdílenou složku 1:1 na sdílenou složku Azure.
Vezměte v úvahu následující možnosti:

#### <a name="share-grouping"></a>Sdílet seskupení

Například pokud má vaše personální oddělení celkem 15 sdílených složek, můžete zvážit ukládání všech dat o lidských přenosů do jedné sdílené složky Azure. Uložení více místních sdílených složek v jedné sdílené složce Azure vám nebrání v vytváření obvyklých sdílených složek SMB na místním serveru Windows. To znamená, že uspořádáte kořenové složky těchto 15 sdílených složek jako podsložek v rámci společné složky. Pak tuto společnou složku synchronizujete se sdílenou složkou Azure. Tímto způsobem je pro tuto skupinu místních sdílených složek potřeba jenom jedna sdílená složka Azure v cloudu.

#### <a name="volume-sync"></a>Synchronizace svazků

Azure File Sync podporuje synchronizaci kořene svazku se sdílenou složkou Azure.
Pokud synchronizujete kořenovou složku, všechny podsložky a soubory skončí ve stejné sdílené složce Azure.

#### <a name="other-best-practices-to-consider"></a>Další osvědčené postupy, které je třeba zvážit

Kromě maximálního limitu synchronizace sdílené složky Azure na jeden server je úvodní úvaha efektivitou synchronizace.

Pokud je na vašem serveru víc sdílených složek, které se synchronizují s vlastní sdílenou složkou Azure, může synchronizace paralelně fungovat. Vektor škálování nemá velikost všech souborů v oboru synchronizace. Jedná se o počet položek (soubory a složky), které vyžadují zpracování.

Jedna sdílená složka Azure může obsahovat až 100 TiB.
Azure File Sync podporuje synchronizaci až 100 000 000 položek na sdílení souborů Azure.

Synchronizace kořenového svazku nebude vždy nejlepší odpovědí. Existují výhody synchronizace více umístění. díky tomu je udržování počtu položek snížen na rozsah synchronizace. Nastavení služby Azure File Sync s menším počtem položek není pro synchronizaci důležité, ale také přináší výhody obnovy na straně cloudu i v případě, že ztratíte Server a zřídíte novou službu, která se připojuje ke stejné službě Azure náhl. e sdílené složky.

Použijte kombinaci výše uvedených konceptů, které vám pomůžou určit, kolik sdílených složek Azure potřebujete, a které části vašich stávajících StorSimple dat budou ukončeny, ve kterých sdílení souborů Azure.

Vytvořte seznam, který zaznamenává vaše myšlenky, například na něj můžete odkazovat v dalším kroku. Umístění uspořádané tady je důležité, protože při zřizování mnoha prostředků můžete snadno ztratit podrobnosti o plánu mapování.
