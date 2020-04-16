---
title: Vizuální vytváření
description: Přečtěte si, jak používat vizuální vytváření ve službě Azure Data Factory
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418486"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Vizuální vytváření ve Službě Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Prostředí uživatelského rozhraní Azure Data Factory (UX) umožňuje vizuálně vytvářet a nasazovat prostředky pro vaši datovou továrnu, aniž byste museli psát jakýkoli kód. Můžete přetáhnout aktivity na plátno kanálu, provádět testovací jízdy, ladění iterativně a nasadit a sledovat spuštění kanálu.

V současné době je ux Azure Data Factory podporované jenom v Microsoft Edge a Google Chrome.

## <a name="authoring-canvas"></a>Vytváření plátna

Chcete-li otevřít **vývojové plátno**, klikněte na ikonu tužky. 

![Vytváření plátna](media/author-visually/authoring-canvas.png)

Zde vytvoříte kanály, aktivity, datové sady, propojené služby, toky dat, aktivační události a integrační runtimesy, které tvoří vaši továrnu. Informace o tom, jak začít vytvářet kanál pomocí vývojového plátna, najdete v [tématu Kopírování dat pomocí kopírování Aktivita](tutorial-copy-data-portal.md). 

Výchozí vizuální vytváření prostředí je přímo pracovat se službou Data Factory. Integrace Azure Repos Git nebo GitHub je taky podporovaná, aby umožňovala správou zdrojového kódu a spolupráci při práci na vašich kanálech datové továrny. Další informace o rozdílech mezi těmito zkušenostmi vytváření najdete [v tématu Source control v Azure Data Factory](source-control.md).

## <a name="expressions-and-functions"></a>Výrazy a funkce

Výrazy a funkce lze použít namísto statických hodnot k určení mnoha vlastností v Azure Data Factory.

Chcete-li určit výraz pro hodnotu vlastnosti, vyberte **Přidat dynamický obsah** nebo klepněte na Alt + **P** při zaostřování na pole.

![Přidání dynamického obsahu](media/author-visually/dynamic-content-1.png)

Tím se otevře **Tvůrce výrazů data factory,** kde můžete vytvářet výrazy z podporovaných systémových proměnných, výstupu aktivity, funkcí a proměnných nebo parametrů zadaných uživatelem. 

![Tvůrce výrazů](media/author-visually/dynamic-content-2.png)

Informace o jazyku výrazu najdete [v tématu Výrazy a funkce v Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Vyberte **Zpětná vazba,** která chcete komentovat funkce nebo upozornit společnost Microsoft na problémy s nástrojem:

![Váš názor](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Další kroky

Další informace o monitorování a správě kanálů najdete v tématu [Programové sledování a správa kanálů](monitor-programmatically.md).
