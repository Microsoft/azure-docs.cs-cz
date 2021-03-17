---
title: Aktivita Power Query v Azure Data Factory
description: Naučte se používat aktivitu Power Query pro funkce tahání dat v kanálu Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: fc4f20db55f8e7e0b2f92cb8309c1c128b235089
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385451"
---
# <a name="power-query-activity-in-data-factory"></a>Aktivita Power Query v datové továrně

Aktivita Power Query umožňuje sestavovat a spouštět Power Query zpracování, aby bylo možné spouštět data tahání ve velkém měřítku v kanálu Data Factory. Novou Power Query můžete vytvořit zpracování z nabídky nové prostředky nebo přidáním aktivity napájení do vašeho kanálu.

![Snímek obrazovky, který ukazuje Power Query v podokně prostředky továrny.](media/data-flow/power-query-wrangling.png)

Dříve byla data tahání v Azure Data Factory vytvořená z možnosti nabídky tok dat. Tato změna se změnila na vytváření z nové aktivity Power Query. Můžete pracovat přímo v Power Query editoru zpracování, abyste mohli provádět interaktivní zkoumání dat a pak svou práci uložit. Po dokončení můžete převzít aktivitu Power Query a přidat ji do kanálu. Azure Data Factory bude automaticky škálovat a zprovoznění vaše data tahání pomocí prostředí Sparku pro datové toky Azure Data Factory.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Převod do skriptu toku dat

Pokud chcete dosáhnout škálování s vaší aktivitou Power Query, Azure Data Factory transformuje ```M``` skript do skriptu toku dat, aby bylo možné spouštět Power Query ve velkém měřítku s využitím prostředí Spark toku dat Azure Data Factory. Vytvářejte tahání data Flow pomocí přípravy dat bez kódu. Seznam dostupných funkcí naleznete v tématu [transformační funkce](wrangling-functions.md).

## <a name="next-steps"></a>Další kroky

Další informace o konceptech tahání dat pomocí [Power Query v Azure Data Factory](wrangling-tutorial.md)
