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
ms.openlocfilehash: 6ea11054e3062c0a265701f6d7fdd136cb692213
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366399"
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
