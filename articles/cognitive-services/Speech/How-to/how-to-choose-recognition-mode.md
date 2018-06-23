---
title: Jak vybrat režim rozpoznávání | Microsoft Docs
description: Jak zvolit nejlepší rozpoznávání režim.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: 4f02b683dde16b537ae5554e6435c068f0fcb808
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342628"
---
# <a name="speech-recognition-modes"></a>Režimy rozpoznávání řeči

Společnosti Microsoft *řeči na Text* rozhraní API podporují více režimů rozpoznávání řeči. Vyberte režim, který poskytne nejlepší výsledky rozpoznávání pro vaši aplikaci.

| Mode | Popis |
|---|---|
| *interaktivní* | "Příkazy a ovládání" rozpoznávání pro scénáře interaktivního uživatele aplikace. Uživatelé řeči krátké fráze určený jako příkazy k aplikaci. |
| *diktování* | Průběžné rozpoznávání pro diktování scénáře. Uživatelé řeči delší věty, které se zobrazují jako text. Uživatelé přijmout více formální hovořícího stylu. |
| *konverzace* | Průběžné rozpoznávání pro přepisování konverzace mezi člověka. Uživatelé přijmout méně formální hovořícího styl a může alternativní mezi delší věty a kratší slovní spojení.

> [!NOTE]
> Při použití těchto režimech platí [rozhraní REST API](../GetStarted/GetStartedREST.md). [Klientské knihovny](../GetStarted/GetStartedClientLibraries.md) použít jiné parametry k určení režimu rozpoznávání. Další informace najdete v knihovně klienta podle vašeho výběru.

Další informace najdete v tématu [režimy rozpoznávání](../concepts.md#recognition-modes) stránky.
