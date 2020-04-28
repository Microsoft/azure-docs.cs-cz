---
title: Vytváření vizuálních prvků
description: Naučte se používat tvorbu vizuálů v Azure Data Factory
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: anandsub
ms.date: 12/19/2019
ms.openlocfilehash: e7de92878dac72470c0b65d1cf18c1a2d526a0bb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81418486"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Vytváření vizuálů v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Prostředí uživatelského rozhraní Azure Data Factory (UX) umožňuje vizuálně vytvářet a nasazovat prostředky pro datovou továrnu bez nutnosti psát jakýkoli kód. Aktivity můžete přetáhnout na plátno kanálu, provádět testovací běhy, ladit je iterativní a nasazovat a monitorovat spuštění kanálu.

V současné době je Azure Data Factory UX podporován pouze v Microsoft Edge a Google Chrome.

## <a name="authoring-canvas"></a>Plátno pro vytváření obsahu

**Plátno pro vytváření obsahu**otevřete kliknutím na ikonu tužky. 

![Plátno pro vytváření obsahu](media/author-visually/authoring-canvas.png)

Tady budete vytvářet kanály, aktivity, datové sady, propojené služby, toky dat, triggery a prostředí Integration runtime, které tvoří vaši továrnu. Pokud chcete začít vytvářet kanál pomocí plátna pro vytváření obsahu, přečtěte si téma [kopírování dat pomocí aktivity kopírování](tutorial-copy-data-portal.md). 

Výchozí prostředí pro vytváření vizuálů funguje přímo s Data Factory službou. Integrace s Azure Repos Git nebo GitHubem je taky podporovaná tak, aby umožňovala správu zdrojového kódu a spolupráci pro práci na kanálech datové továrny. Další informace o rozdílech mezi těmito prostředími pro vytváření najdete [v tématu Správa zdrojového kódu v Azure Data Factory](source-control.md).

## <a name="expressions-and-functions"></a>Výrazy a funkce

Výrazy a funkce lze místo statických hodnot použít k určení mnoha vlastností v Azure Data Factory.

Chcete-li zadat výraz pro hodnotu vlastnosti, vyberte možnost **Přidat dynamický obsah** nebo při zaměření na pole klikněte na tlačítko **ALT + P** .

![Přidat dynamický obsah](media/author-visually/dynamic-content-1.png)

Otevře se **Data Factory Tvůrce výrazů** , kde můžete vytvářet výrazy z podporovaných systémových proměnných, výstupu aktivity, funkcí a uživatelem zadaných proměnných nebo parametrů. 

![Tvůrce výrazů](media/author-visually/dynamic-content-2.png)

Informace o jazyce výrazů naleznete [v tématu Expressions and Functions in Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Vyberte **zpětnou vazbu** pro komentář k funkcím nebo upozorněte společnost Microsoft na problémy s nástrojem:

![Váš názor](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Další kroky

Další informace o monitorování a správě kanálů najdete v tématu [monitorování a Správa kanálů prostřednictvím kódu programu](monitor-programmatically.md).
