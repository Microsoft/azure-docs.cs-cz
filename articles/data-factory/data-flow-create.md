---
title: Vytvoření služby Azure Data Factory mapování toku dat
description: Vytvoření služby Azure Data Factory mapování toku dat
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: b706e229bed48c821d5ca772450df320fd7e0b7f
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271659"
---
# <a name="create-azure-data-factory-data-flow"></a>Vytvoření toku dat Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Mapování toků dat ve službě ADF poskytují způsob, jak transformovat data ve velkém měřítku bez kódování vyžaduje. Úlohu transformace dat v Návrháři toků dat můžete navrhnout pomocí řady transformací. Začněte s libovolným počtem zdroj transformace, za nímž následuje postup transformace dat. Potom dokončete svůj tok dat s jímky objevil výsledky v cílovém umístění.

Začněte tím, že první vytvoříte novou službu Data Factory V2 na webu Azure Portal. Jakmile vytvoříte nový objekt factory, klikněte na dlaždici "Vytvořit a monitorovat" na uživatelské rozhraní služby Data Factory.

![Možnosti toku dat](media/data-flow/v2dataflowportal.png "vytvoření toku dat")

Až se v Uživatelském rozhraní služby Data Factory, můžete použít ukázkové datové toky. Ukázky jsou k dispozici z Galerie šablon ADF. Ve službě ADF vytvoření "Kanálu z šablony" a vyberte kategorii toku dat z Galerie šablon.

![Možnosti toku dat](media/data-flow/template.png "vytvoření toku dat")

Se výzva k zadání informací o vašem účtu úložiště objektů Blob v Azure.

![Možnosti toku dat](media/data-flow/template2.png "datový tok vytvořit 2")

[Data ukládaná za účelem těchto ukázek můžete najít zde](https://github.com/kromerm/adfdataflowdocs/tree/master/sampledata). Stáhněte si ukázková data a ukládat soubory v účtu úložiště objektů Blob v Azure tak, aby bylo možné provést ukázky.

Použijte tlačítko vytvořit prostředek "znaménko plus" v Uživatelském rozhraní ADF na vytváření toků dat.

![Možnosti toku dat](media/data-flow/newresource.png "nový prostředek")

