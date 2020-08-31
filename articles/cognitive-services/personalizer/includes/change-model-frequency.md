---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 08/25/2020
ms.openlocfilehash: 4eacc1c4e863ad1a278a4974bb0f6c101aafe7e0
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055368"
---
### <a name="change-the-model-update-frequency"></a>Změna frekvence aktualizace modelu

V Azure Portal změňte v prostředku přizpůsobeném na stránce **Konfigurace** **Četnost aktualizace modelu** na 10 sekund. Tato krátká doba bude službu vytvářet rychle, což vám umožní zjistit, jak se hlavní akce mění pro každou iteraci.

![Změna frekvence aktualizace modelu](../media/settings/configure-model-update-frequency-settings.png)

Když se poprvé vytvoří instance smyčky pro přizpůsobení, neexistuje žádný model, protože neexistovala žádná Neplatní volání rozhraní API pro vlak. Volání Rank budou pro každou položku vracet stejné pravděpodobnosti. Vaše aplikace by měla stále vždy Seřadit obsah pomocí výstupu RewardActionId.