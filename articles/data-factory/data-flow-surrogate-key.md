---
title: Azure Data Factory transformace náhradního klíče datového toku mapování dat
description: Jak použít transformaci klíče pro mapování toku dat Azure Data Factory k vygenerování sekvenčních hodnot klíče
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 45e2d35a3b0a3f3c89913bbe70d7c43c17cbcee0
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029193"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Transformace náhradního klíče datového toku mapování



Pomocí transformace náhradního klíče můžete do sady řádků toku dat přidat přírůstkovou hodnotu libovolného klíče, který není v obchodním podniku. To je užitečné při navrhování tabulek dimenzí v analytickém datovém modelu schématu hvězdičky, kde každý člen v tabulkách dimenzí musí mít jedinečný klíč, který je neobchodní klíč, a to část metodologie Kimball DW.

Transformace náhradního(media/data-flow/surrogate.png "klíče") pro ![transformaci náhradního klíče]

"Klíčový sloupec" je název, který udělíte vašemu novému sloupci náhradního klíče.

"Počáteční hodnota" je počátečním bodem přírůstkové hodnoty.

## <a name="increment-keys-from-existing-sources"></a>Zvýšit klíče z existujících zdrojů

Pokud chcete sekvenci spustit z hodnoty, která existuje ve zdroji, můžete použít transformaci odvozeného sloupce hned po transformaci náhradního klíče a přidat tyto dvě hodnoty dohromady:

![Sk. Přidání maxima]pro(media/data-flow/sk006.png "transformaci náhradních klíčů přidat Max")

K osazení hodnoty klíče s předchozím maximem můžete použít dvě metody:

### <a name="database-sources"></a>Zdroje databáze

Pomocí možnosti dotaz vyberte MAX () ze zdroje pomocí transformace zdroje:

(media/data-flow/sk002.png "Dotaz na převod") náhradního klíče pro ![dotaz na náhradní klíč]

### <a name="file-sources"></a>Zdroje souborů

Pokud je předchozí maximální hodnota v souboru, můžete použít transformaci zdrojového kódu společně s agregovanou transformací a pomocí funkce výrazu MAX () získat předchozí maximální hodnotu:

![](media/data-flow/sk008.png "Soubor") náhradního klíče souboru náhradního klíče

V obou případech je třeba spojit vaše příchozí nová data společně se zdrojem, který obsahuje předchozí maximální hodnotu:

Připojení náhradního ![klíče připojit]k(media/data-flow/sk004.png "náhradnímu") klíči

## <a name="next-steps"></a>Další kroky

V těchto příkladech se používají transformace sloupců [Join](data-flow-join.md) a [Derived](data-flow-derived-column.md) .
