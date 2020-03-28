---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.author: diberry
ms.openlocfilehash: 7ef219e6b5f7547029612ec3898efec51abd4712
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76122891"
---
## <a name="change-the-model-update-frequency"></a>Změna četnosti aktualizací modelu

Na webu Azure Portal změňte v prostředku Personalikátor na stránce **Konfigurace** **frekvenci aktualizace modelu** na 10 sekund. Tato krátká doba trvání bude trénovat službu rychle, což vám umožní zobrazit, jak se změní maximální akce pro každou iteraci.

![Změna četnosti aktualizací modelu](../media/settings/configure-model-update-frequency-settings.png)

Při relaziční smyčky personalizátoru je první instance, neexistuje žádný model, protože nedošlo k žádné volání rozhraní API odměny trénovat z. Pořadí volání vrátí stejné pravděpodobnosti pro každou položku. Vaše aplikace by měla vždy řadit obsah pomocí výstupu RewardActionId.