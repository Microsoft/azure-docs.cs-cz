---
title: Azure Data Factory mapování agregační transformace toku dat
description: Azure Data Factory Data toku agregační transformace
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 3793c49da32845d559d73faa25c571d3f86e062f
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271676"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Azure Data Factory mapování agregační transformace toku dat

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Transformace agregace je, kde definujete agregace sloupců v datové proudy. V okně Tvůrce výrazů můžete definovat různé druhy agregace (například SUM, MIN, MAX, počet atd.) a vytvořit nové pole ve výstupu, který zahrnuje tyto agregace pomocí pole volitelné Group.

![Možnosti transformace agregace](media/data-flow/agg.png "agregovat 1")

## <a name="group-by"></a>Seskupit podle
(Volitelné) Zvolte klauzule Group by pro vaše agregace a použijte název existujícího sloupce nebo nový název. Použití "přidat sloupec" přidat další klauzule group by a klikněte na textové pole vedle názvu sloupce pro spuštění Tvůrce buď vyberte právě existující sloupec, kombinaci sloupce nebo výrazy pro seskupení.

## <a name="the-aggregate-column-tab"></a>Na kartě agregačních sloupec 
(Povinné) Klepněte na kartu agregovaný sloupec pro sestavení výrazy agregace. Můžete zvolit existující sloupec přepsat hodnotu agregaci, nebo vytvořit nové pole s novým názvem pro agregaci. Výraz, který chcete použít pro agregaci zadáme do pole vpravo vedle názvu selektor sloupců. Kliknutím na toto textové pole se otevře Tvůrce výrazů.

![Možnosti transformace agregace](media/data-flow/agg2.png "agregátoru")

## <a name="data-preview-in-expression-builder"></a>Náhled dat v Tvůrce výrazů

V režimu ladění Tvůrce výrazů nejde produkovat data náhledy agregační funkce. Chcete-li zobrazit náhledy dat pro agregační transformace, zavřete Tvůrce výrazů a profil dat z Návrháře toku dat zobrazení.
