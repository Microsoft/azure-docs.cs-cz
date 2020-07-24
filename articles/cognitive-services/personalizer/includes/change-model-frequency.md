---
title: zahrnout soubor
description: zahrnout soubor
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: include
ms.custom: include file
ms.date: 01/15/2020
ms.openlocfilehash: d03d904de68720874ea175c95244ba80c586df82
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87133802"
---
## <a name="change-the-model-update-frequency"></a>Změna frekvence aktualizace modelu

V Azure Portal změňte v prostředku přizpůsobeném na stránce **Konfigurace** **Četnost aktualizace modelu** na 10 sekund. Tato krátká doba bude službu vytvářet rychle, což vám umožní zjistit, jak se hlavní akce mění pro každou iteraci.

![Změna frekvence aktualizace modelu](../media/settings/configure-model-update-frequency-settings.png)

Když se poprvé vytvoří instance smyčky pro přizpůsobení, neexistuje žádný model, protože neexistovala žádná Neplatní volání rozhraní API pro vlak. Volání Rank budou pro každou položku vracet stejné pravděpodobnosti. Vaše aplikace by měla stále vždy Seřadit obsah pomocí výstupu RewardActionId.