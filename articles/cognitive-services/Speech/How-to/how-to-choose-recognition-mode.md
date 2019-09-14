---
title: Jak zvolit režim rozpoznávání Zpracování řeči Bingu | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Jak vybrat nejlepší režim rozpoznávání v Zpracování řeči Bingu.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1860acb571c837a5eb4c75be69a96d1b22682118
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70965679"
---
# <a name="bing-speech-recognition-modes"></a>Zpracování řeči Bingu režimy rozpoznávání

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Rozhraní API pro Bing Speech to Text podporují více režimů rozpoznávání řeči. Vyberte režim, který vytváří nejlepší výsledky pro rozpoznávání vaší aplikace.

| Režim | Popis |
|---|---|
| *Interaktivní* | Rozpoznávání příkazů a řízení pro scénáře interaktivních uživatelských aplikací. Uživatelé mluví krátké fráze určené jako příkazy aplikace. |
| *diktování* | Nepřetržité rozpoznávání scénářů diktování. Uživatelé komunikují delší věty, které se zobrazují jako text. Uživatelé dovezmou formální styl speaking. |
| *konverzace* | Průběžné rozpoznávání pro zdlouhavého přepisování konverzace mezi lidmi Uživatelé přijímají méně formální styl mluveného slova a můžou se střídat mezi delšími větami a kratšími větami.

> [!NOTE]
> Tyto režimy se použijí při použití [rozhraní REST API](../GetStarted/GetStartedREST.md). [Klientské knihovny](../GetStarted/GetStartedClientLibraries.md) používají jiné parametry pro určení režimu rozpoznávání. Další informace najdete v klientské knihovně podle vašeho výběru.

Další informace najdete na stránce [režimy rozpoznávání](../concepts.md#recognition-modes) .
