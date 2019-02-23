---
title: Jak zvolit režim rozpoznávání řeči Bingu | Dokumentace Microsoftu
titlesuffix: Azure Cognitive Services
description: Jak zvolit nejlepší režim rozpoznávání ve zpracování řeči Bingu.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 99e69691d9904285ecff356523813b426c98fca2
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669716"
---
# <a name="bing-speech-recognition-modes"></a>Režimy rozpoznávání řeči Bingu

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Bing Speech to Text API podporovat více režimů rozpoznávání řeči. Zvolte režim, který poskytne nejlepší výsledky rozpoznávání pro vaši aplikaci.

| Mode | Popis |
|---|---|
| *Interaktivní* | "Příkazy a ovládání" rozpoznávání pro interaktivní uživatelské scénáře aplikací. Uživatelům číst obsah krátké fráze určené jako příkazy, které aplikace. |
| *Diktování* | Průběžné rozpoznávání pro scénáře diktování. Uživatelé mluvit delší věty, které se zobrazují jako text. Uživatelé přijmou formálnější styl mluvy. |
| *Konverzace* | Průběžné rozpoznávání zdlouhavého přepisování pořídili konverzace mezi lidi. Uživatelé přijmou méně formální styl mluvy a může přepínat mezi delší věty a kratší frází.

> [!NOTE]
> Při použití těchto režimech platí [rozhraní REST API](../GetStarted/GetStartedREST.md). [Klientské knihovny](../GetStarted/GetStartedClientLibraries.md) použít jiné parametry pro určení režimu rozpoznávání. Další informace najdete v klientské knihovně podle vašeho výběru.

Další informace najdete v tématu [rozpoznávání režimy](../concepts.md#recognition-modes) stránky.
