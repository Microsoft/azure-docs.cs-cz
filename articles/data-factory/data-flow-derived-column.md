---
title: Transformace odvozeného sloupce v datovém toku mapování – Azure Data Factory | Microsoft Docs
description: Naučte se, jak transformovat data ve velkém měřítku v Azure Data Factory pomocí transformace sloupce s odvozeným datovým tokem.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aacd6f1799f1813e168bd04e78f18cf60ad5243f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026847"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Odvozená transformace sloupce v toku mapování dat

Pomocí transformace odvozeného sloupce můžete vygenerovat nové sloupce v toku dat nebo upravit existující pole.

## <a name="derived-column-settings"></a>Nastavení odvozeného sloupce

Pokud chcete přepsat existující sloupec, vyberte ho přes rozevírací seznam sloupec. V opačném případě použijte pole výběru sloupce jako textové pole a zadejte název nového sloupce. Chcete-li vytvořit výraz odvozeného sloupce, klikněte na pole zadejte výraz pro otevření [Tvůrce výrazů toku dat](concepts-data-flow-expression-builder.md).

Nastavení ![odvozených sloupců](media/data-flow/dc1.png "odvozená nastavení sloupců")

Chcete-li přidat další odvozené sloupce, najeďte myší na existující odvozený sloupec a klikněte na tlačítko "+". Pak zvolte buď přidat sloupec nebo přidat vzor sloupce. Vzorce sloupce mohou být užitečné, pokud jsou názvy sloupců z vašich zdrojů proměnné. Další informace najdete v tématu [vzory sloupců](concepts-data-flow-column-pattern.md).

![Nový odvozený výběr sloupce](media/data-flow/columnpattern.png "Nový odvozený výběr sloupce")

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [jazyce výrazu Mapping data Flow](data-flow-expression-functions.md).
