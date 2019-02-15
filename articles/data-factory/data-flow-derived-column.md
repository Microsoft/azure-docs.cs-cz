---
title: Azure Data Factory Data tok transformace odvozených sloupců
description: Azure Data Factory Data tok transformace odvozených sloupců
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aee005aed52563e214e24148af2563fd7869de76
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271668"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Azure Data Factory Data tok transformace odvozených sloupců

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Pomocí transformace odvozených sloupců, vytvořte nové sloupce ve svém toku dat nebo upravovat existující pole.

![odvození sloupce](media/data-flow/dc1.png "odvozené sloupce")

V jedné transformaci odvozených sloupců můžete provádět několik akcí odvozené sloupce. Klikněte na tlačítko "Přidat sloupec" k transformaci více než 1 sloupec v jedné transformace kroku.

V poli sloupce vyberte existující sloupec přepsat s novou hodnotou odvozené, nebo klikněte na "Vytvořit nový sloupec" generovat nový sloupec s hodnotou nově odvozené.

Textové pole výraz se otevře Tvůrce výrazů, kde můžete vytvářet ve výrazu odvozených sloupců pomocí výrazu funkce.
