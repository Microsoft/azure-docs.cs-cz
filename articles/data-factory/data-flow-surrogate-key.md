---
title: Transformace náhradního klíče datového toku mapování
description: Jak použít transformaci klíče pro mapování toku dat Azure Data Factory k vygenerování sekvenčních hodnot klíče
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930203"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Transformace náhradního klíče datového toku mapování



Pomocí transformace náhradního klíče můžete do sady řádků toku dat přidat přírůstkovou hodnotu libovolného klíče, který není v obchodním podniku. To je užitečné při navrhování tabulek dimenzí v analytickém datovém modelu schématu hvězdičky, kde každý člen v tabulkách dimenzí musí mít jedinečný klíč, který je neobchodní klíč, a to část metodologie Kimball DW.

![Transformace náhradního klíče](media/data-flow/surrogate.png "Transformace náhradního klíče")

"Klíčový sloupec" je název, který udělíte vašemu novému sloupci náhradního klíče.

"Počáteční hodnota" je počátečním bodem přírůstkové hodnoty.

## <a name="increment-keys-from-existing-sources"></a>Zvýšit klíče z existujících zdrojů

Pokud chcete sekvenci spustit z hodnoty, která existuje ve zdroji, můžete použít transformaci odvozeného sloupce hned po transformaci náhradního klíče a přidat tyto dvě hodnoty dohromady:

![SK – přidat max](media/data-flow/sk006.png "Transformace náhradních klíčů – přidat max")

K osazení hodnoty klíče s předchozím maximem můžete použít dvě metody:

### <a name="database-sources"></a>Zdroje databáze

Pomocí možnosti dotaz vyberte MAX () ze zdroje pomocí transformace zdroje:

![Dotaz na náhradní klíč](media/data-flow/sk002.png "Dotaz na transformaci náhradního klíče")

### <a name="file-sources"></a>Zdroje souborů

Pokud je předchozí maximální hodnota v souboru, můžete použít transformaci zdrojového kódu společně s agregovanou transformací a pomocí funkce výrazu MAX () získat předchozí maximální hodnotu:

![Soubor náhradního klíče](media/data-flow/sk008.png "Soubor náhradního klíče")

V obou případech je třeba spojit vaše příchozí nová data společně se zdrojem, který obsahuje předchozí maximální hodnotu:

![Připojení náhradních klíčů](media/data-flow/sk004.png "Připojení náhradních klíčů")

## <a name="next-steps"></a>Další kroky

V těchto příkladech se používají transformace sloupců [Join](data-flow-join.md) a [Derived](data-flow-derived-column.md) .
