---
title: Jak zvolit režim rozpoznávání řeči Bingu | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Jak zvolit nejlepší režim rozpoznávání ve zpracování řeči Bingu.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX
ms.openlocfilehash: a39b357a26823e322d4e902f2d99b67488bbf2df
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46950875"
---
# <a name="bing-speech-recognition-modes"></a>Režimy rozpoznávání řeči Bingu

Bing Speech to Text API podporovat více režimů rozpoznávání řeči. Zvolte režim, který poskytne nejlepší výsledky rozpoznávání pro vaši aplikaci.

| Mode | Popis |
|---|---|
| *Interaktivní* | "Příkazy a ovládání" rozpoznávání pro interaktivní uživatelské scénáře aplikací. Uživatelům číst obsah krátké fráze určené jako příkazy, které aplikace. |
| *Diktování* | Průběžné rozpoznávání pro scénáře diktování. Uživatelé mluvit delší věty, které se zobrazují jako text. Uživatelé přijmou formálnější styl mluvy. |
| *Konverzace* | Průběžné rozpoznávání zdlouhavého přepisování pořídili konverzace mezi lidi. Uživatelé přijmou méně formální styl mluvy a může přepínat mezi delší věty a kratší frází.

> [!NOTE]
> Při použití těchto režimech platí [rozhraní REST API](../GetStarted/GetStartedREST.md). [Klientské knihovny](../GetStarted/GetStartedClientLibraries.md) použít jiné parametry pro určení režimu rozpoznávání. Další informace najdete v klientské knihovně podle vašeho výběru.

Další informace najdete v tématu [rozpoznávání režimy](../concepts.md#recognition-modes) stránky.
