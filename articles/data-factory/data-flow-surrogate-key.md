---
title: Azure Data Factory mapování klíčů transformace náhradní toku dat
description: Azure Data Factory mapování klíčů transformace náhradní toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 62e9879f6bc75f042669ecb931ca7459d1317cc7
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271738"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Azure Data Factory mapování klíčů transformace náhradní toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Přidání narůstající libovolné klíčové hodnoty neobchodní vaší sady řádků toku dat pomocí transformace náhradní klíč. To je užitečné při návrhu tabulky dimenzí v analytické datový model hvězdicového schématu kde každý člen v tabulkách dimenze musí mít jedinečný klíč, který je součástí klíče, neobchodní metodologie Kimball datového skladu.

![Náhradní klíče transformace](media/data-flow/surrogate.png "náhradní klíče transformace")

"Klíčový sloupec" je název, který vám poskytne do nového sloupce klíče náhrady.

"Počáteční hodnota" se počáteční bod inkrementální hodnotu.
