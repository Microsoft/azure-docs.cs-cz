---
title: Vytváření vizuálních prvků
description: Naučte se používat tvorbu vizuálů v Azure Data Factory
ms.service: data-factory
ms.topic: conceptual
author: dcstwh
ms.author: weetok
ms.date: 09/08/2020
ms.openlocfilehash: 704360ac37e016de9efe2248181f7db358f5a7cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100371477"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Vytváření vizuálů v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Prostředí uživatelského rozhraní Azure Data Factory (UX) umožňuje vizuálně vytvářet a nasazovat prostředky pro datovou továrnu bez nutnosti psát jakýkoli kód. Můžete přetahovat aktivity na plátno kanálu, provádět testovací běhy, iterativně ladit a nasazovat a monitorovat spuštění kanálu.

V současné době je Azure Data Factory UX podporován pouze v Microsoft Edge a Google Chrome.

## <a name="authoring-canvas"></a>Plátno pro vytváření obsahu

**Plátno pro vytváření obsahu** otevřete kliknutím na ikonu tužky. 

![Plátno pro vytváření obsahu](media/author-visually/authoring-canvas.png)

Tady vytváříte kanály, aktivity, datové sady, propojené služby, toky dat, triggery a prostředí Integration runtime, které tvoří vaši továrnu. Pokud chcete začít vytvářet kanál pomocí plátna pro vytváření obsahu, přečtěte si téma [kopírování dat pomocí aktivity kopírování](tutorial-copy-data-portal.md). 

Výchozí prostředí pro vytváření vizuálů funguje přímo s Data Factory službou. Integrace s Azure Repos Git nebo GitHubem je taky podporovaná tak, aby umožňovala správu zdrojového kódu a spolupráci pro práci na kanálech datové továrny. Další informace o rozdílech mezi těmito prostředími pro vytváření najdete [v tématu Správa zdrojového kódu v Azure Data Factory](source-control.md).

### <a name="properties-pane"></a>Podokno Vlastnosti

U prostředků nejvyšší úrovně, jako jsou kanály, datové sady a toky dat, jsou vlastnosti vysoké úrovně editovatelné v podokně vlastnosti na pravé straně plátna. Podokno vlastnosti obsahuje vlastnosti, jako je název, popis, poznámky a další vlastnosti na nejvyšší úrovni. Podzdroje, jako jsou aktivity kanálu a transformace toku dat, se upravují pomocí panelu v dolní části plátna. 

![Podokno Vlastnosti](media/author-visually/properties-pane.png)

V podokně vlastnosti se při vytváření prostředků otevírá pouze standardně. Pokud ho chcete upravit, klikněte na ikonu podokna Vlastnosti umístěnou v pravém horním rohu plátna.

### <a name="related-resources"></a>Související prostředky

V podokně vlastnosti vidíte, které prostředky jsou na vybraném prostředku závislé, a to tak, že vyberete kartu **související** . Tady se zobrazí všechny prostředky, které odkazují na aktuální prostředek.

![Související prostředky](media/author-visually/related-resources.png)

Například na výše uvedeném obrázku jeden kanál a dva toky dat používají aktuálně vybranou datovou sadu.

## <a name="management-hub"></a>Centrum správy

Centrum pro správu, ke kterému přistupovala karta *Spravovat* v prostředí Azure Data Factory, je portál, který hostuje globální akce správy pro vaši datovou továrnu. Tady můžete spravovat připojení k úložištím dat a externím výpočtům, konfiguraci správy zdrojového kódu a nastavením aktivačních událostí. Další informace najdete v informacích o možnostech [centra pro správu](author-management-hub.md).

![Správa propojených služeb](media/author-management-hub/management-hub-linked-services.png)

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
