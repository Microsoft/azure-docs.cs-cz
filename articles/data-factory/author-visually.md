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
ms.date: 05/15/2020
ms.openlocfilehash: bf0aeb3b73f2b12e723269a792d67d7f6185894f
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83674569"
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

### <a name="properties-pane"></a>Podokno Vlastnosti

U prostředků nejvyšší úrovně, jako jsou kanály, datové sady a toky dat, jsou vlastnosti vysoké úrovně editovatelné v podokně vlastnosti na pravé straně plátna. Podokno vlastnosti obsahuje vlastnosti, jako je název, popis, poznámky a další vlastnosti na nejvyšší úrovni. Podzdroje, jako jsou aktivity kanálu a transformace toku dat, se upravují pomocí panelu v dolní části plátna. 

![Plátno pro vytváření obsahu](media/author-visually/properties-pane.png)

Podokno vlastnosti bude ve výchozím nastavení otevřeno pouze při vytváření prostředku. Pokud ho chcete upravit, klikněte na ikonu podokna Vlastnosti umístěnou v pravém horním rohu plátna.

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
