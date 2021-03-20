---
title: Dospělý, pikantní, gorie obsahu – Počítačové zpracování obrazu
titleSuffix: Azure Cognitive Services
description: Koncepty týkající se rozpoznávání obsahu pro dospělé v obrázcích pomocí rozhraní API pro počítačové zpracování obrazu.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5d5961ecae2fbc154ae6f1acd74df2bb74024fa1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96532614"
---
# <a name="detect-adult-content"></a>Zjistit obsah pro dospělé

Počítačové zpracování obrazu dokáže detekovat obsah pro dospělé v obrázcích, aby vývojáři mohli omezit zobrazení těchto imagí v softwaru. Příznaky obsahu jsou aplikovány se skóre mezi nulou a jedním, takže vývojáři mohou interpretovat výsledky podle jejich vlastních předvoleb.

> [!NOTE]
> Mnohé z těchto funkcí nabízí služba [Azure Content moderator](../content-moderator/overview.md) . V této Alternative najdete řešení pro přísnější scénáře Moderování obsahu, jako je například moderování textu a pracovní postupy pro lidské kontroly.

## <a name="content-flag-definitions"></a>Definice příznaků obsahu

Klasifikace "dospělý" obsahuje několik různých kategorií:

- Obrázky **pro dospělé** jsou výslovně pohlavní v podstatě a často ukazují nahotu a pohlavní úkony.
- **Pikantní** obrázky jsou pohlavní sugestivní v podstatě a často obsahují méně sexuálního obsahu než obrázky označené jako **dospělý**.
- Obrázky **gorie** ukazují krevní/Gore.

## <a name="use-the-api"></a>Použití rozhraní API

Obsah pro dospělé můžete detekovat pomocí rozhraní API pro [analýzu imagí](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Když přidáte hodnotu `Adult` do parametru dotazu **visualFeatures** , vrátí rozhraní API tři logické vlastnosti &mdash; `isAdultContent` , `isRacyContent` a `isGoryContent` &mdash; v jeho odpovědi JSON. Metoda také vrátí odpovídající vlastnosti &mdash; `adultScore` , `racyScore` a, `goreScore` &mdash; které reprezentují výsledky spolehlivosti mezi nulou a jednou pro každou příslušnou kategorii.

- [Rychlý Start: Počítačové zpracování obrazu REST API nebo klientské knihovny](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
