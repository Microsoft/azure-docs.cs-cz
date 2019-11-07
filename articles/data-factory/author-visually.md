---
title: Vytváření vizuálů v Azure Data Factory
description: Naučte se používat tvorbu vizuálů v Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 71f0e283c9609e949cc9b09f20a16c6f5c9c3e50
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681614"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Vytváření vizuálů v Azure Data Factory

Prostředí uživatelského rozhraní Azure Data Factory (UX) umožňuje vizuálně vytvářet a nasazovat prostředky pro datovou továrnu bez nutnosti psát jakýkoli kód. Aktivity můžete přetáhnout na plátno kanálu, provádět testovací běhy, ladit je iterativní a nasazovat a monitorovat spuštění kanálu.

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
