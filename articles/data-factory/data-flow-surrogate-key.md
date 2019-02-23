---
title: Azure Data Factory mapování klíčů transformace náhradní toku dat
description: Azure Data Factory mapování klíčů transformace náhradní toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 6243905857f0450168541f556636d90bb4d855f7
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734944"
---
# <a name="azure-data-factory-mapping-data-flow-surrogate-key-transformation"></a>Azure Data Factory mapování klíčů transformace náhradní toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Přidání narůstající libovolné klíčové hodnoty neobchodní vaší sady řádků toku dat pomocí transformace náhradní klíč. To je užitečné při návrhu tabulky dimenzí v analytické datový model hvězdicového schématu kde každý člen v tabulkách dimenze musí mít jedinečný klíč, který je součástí klíče, neobchodní metodologie Kimball datového skladu.

![Náhradní klíče transformace](media/data-flow/surrogate.png "náhradní klíče transformace")

"Klíčový sloupec" je název, který vám poskytne do nového sloupce klíče náhrady.

"Počáteční hodnota" se počáteční bod inkrementální hodnotu.
