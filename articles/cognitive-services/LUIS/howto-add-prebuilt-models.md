---
title: Předem sestavené modely pro jazykové porozumění
titleSuffix: Azure Cognitive Services
description: Služba LUIS obsahuje sadu předem sestavených modelů pro rychlé přidávání běžných konverzačních uživatelských scénářů.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74013595"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Přidání předem vytvořených modelů pro běžné scénáře použití 

Služba LUIS obsahuje sadu předem sestavených modelů pro rychlé přidávání běžných konverzačních uživatelských scénářů. Jedná se o rychlý a snadný způsob, jak přidat schopnosti do konverzační klientské aplikace, aniž byste museli navrhovat modely pro tyto schopnosti. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Přidání předem připravené domény

1. Na stránce **Moje aplikace** vyberte aplikaci. Tím se aplikace otevře do části **Sestavení** aplikace. 

1. Na levém panelu nástrojů **vyberte Předem sestavené domény.** 

1. Najděte doménu, kterou chcete přidat do aplikace, a pak vyberte **Tlačítko Přidat doménu.**

    > [!div class="mx-imgBorder"]
    > ![Přidání předem vytvořené domény kalendáře](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Přidání předem sestaveného záměru

1. Na stránce **Moje aplikace** vyberte aplikaci. Tím se aplikace otevře do části **Sestavení** aplikace. 

1. Na stránce **Záměry** vyberte **přidat předem sestavený záměr domény** z panelu nástrojů nad seznamem záměrů. 

1. V rozbalovacím dialogovém okně vyberte záměr **Utilities.Cancel.** 

    > [!div class="mx-imgBorder"]
    > ![Přidání předem sestaveného záměru](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Vyberte tlačítko **Hotovo.**

## <a name="add-a-prebuilt-entity"></a>Přidání předem sestavené entity

1. Otevřete aplikaci kliknutím na její název na stránce **Moje aplikace** a potom klikněte na **Entity** na levé straně. 

1. Na stránce **Entity** klikněte na **Add prebuilt entity**.

1. V dialogovém okně **Přidat předem sestavené entity** vyberte předem sestavenou entitu. 

    > [!div class="mx-imgBorder"]
    > ![Dialogové okno Přidat předem sestavenou entitu](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Vyberte **Done** (Hotovo). Po přidání entity není nutné trénovat aplikaci. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publikovat pro zobrazení předem sestavený model z koncového bodu předpověď

Nejjednodušší způsob, jak zobrazit hodnotu předem sestaveného modelu, je dotaz z publikovaného koncového bodu. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entity obsahující předem sestavený token entity
 
Pokud máte entitu nazíanou počítačem, která je omezena předem sestavenou entitou, přidejte dílčí součást do entity načené počítačem a přidejte omezení předem sestavené entity.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Sestavení modelu z .csv s rest API](./luis-tutorial-node-import-utterances-csv.md)
