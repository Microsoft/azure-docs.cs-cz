---
title: Mapování transformace náhradního klíče toku dat
description: Jak pomocí mapování datového toku Azure Data Factory pro transformaci náhradního klíče ke generování sekvenčních hodnot klíčů
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930203"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapování transformace náhradního klíče toku dat



Pomocí transformace náhradního klíče přidejte do sady řádků toku dat přírůstkovou hodnotu libovolného klíče, která nepodniká. To je užitečné při navrhování tabulek dimenzí v analytickém datovém modelu schématu hvězd, kde každý člen v tabulkách dimenzí musí mít jedinečný klíč, který je neobchodní klíč, který je součástí metodiky Kimball DW.

![Transformace náhradního klíče](media/data-flow/surrogate.png "Transformace náhradního klíče")

"Klíčový sloupec" je název, který přidáte novému sloupci náhradního klíče.

"Počáteční hodnota" je počátečníbod přírůstkové hodnoty.

## <a name="increment-keys-from-existing-sources"></a>Přírůstek klíče z existujících zdrojů

Pokud chcete spustit sekvenci z hodnoty, která existuje ve zdroji, můžete použít transformaci odvozeného sloupce bezprostředně po transformaci náhradního klíče a přidat dvě hodnoty dohromady:

![SK přidat Max](media/data-flow/sk006.png "Náhradní transformace klíče Přidat max")

Chcete-li osivo hodnotu klíče s předchozí max, existují dvě techniky, které můžete použít:

### <a name="database-sources"></a>Zdroje databáze

Pomocí možnosti "Dotaz" vyberte max() ze zdroje pomocí transformace zdroj:

![Dotaz na náhradní klíč](media/data-flow/sk002.png "Dotaz na transformaci náhradního klíče")

### <a name="file-sources"></a>Zdroje souborů

Pokud je předchozí maximální hodnota v souboru, můžete použít transformaci Source společně s agregační transformací a použít funkci výrazu MAX() k získání předchozí maximální hodnoty:

![Soubor náhradního klíče](media/data-flow/sk008.png "Soubor náhradního klíče")

V obou případech je nutné spojit příchozí nová data spolu se zdrojem, který obsahuje předchozí maximální hodnotu:

![Připojení náhradního klíče](media/data-flow/sk004.png "Připojení náhradního klíče")

## <a name="next-steps"></a>Další kroky

Tyto příklady používají [spojení](data-flow-join.md) a [odvozené sloupce](data-flow-derived-column.md) transformace.
