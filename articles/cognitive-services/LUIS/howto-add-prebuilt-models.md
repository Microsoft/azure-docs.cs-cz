---
title: Předem připravené modely pro Language Understanding
titleSuffix: Azure Cognitive Services
description: LUIS zahrnuje sadu předem připravených modelů pro rychlé přidávání běžných scénářů pro konverzační uživatele.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: abf149d621d283744a71e08ea4ac2199a1994d57
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680950"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Přidat předem připravené modely pro běžné scénáře použití

LUIS zahrnuje sadu předem připravených modelů pro rychlé přidávání běžných scénářů pro konverzační uživatele. Toto je rychlý a snadný způsob, jak přidat možnosti do klientské aplikace v konverzaci, aniž byste museli navrhovat modely pro tyto možnosti.

## <a name="add-a-prebuilt-domain"></a>Přidání předem připravené domény

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.

1. Na levém panelu nástrojů vyberte **předem sestavené domény** .

1. Najděte doménu, kterou chcete přidat do aplikace, a pak vyberte tlačítko **Přidat doménu** .

    > [!div class="mx-imgBorder"]
    > ![Přidat předem vytvořenou doménu kalendáře](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

## <a name="add-a-prebuilt-intent"></a>Přidat předem sestavený záměr

1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.

1. Na stránce **záměry** vyberte **Přidat předem sestavený záměr domény** z panelu nástrojů nad seznamem záměry.

1. V místním dialogovém okně vyberte záměr.

    > [!div class="mx-imgBorder"]
    > ![Přidat předdefinovaný záměr](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Vyberte tlačítko **Hotovo** .

## <a name="add-a-prebuilt-entity"></a>Přidat předem vytvořenou entitu
1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Na levé straně vyberte **entity** .

1. Na stránce **entity** vyberte **Přidat předem vytvořenou entitu**.

1. V dialogovém okně **Přidat předem připravené entity** vyberte předem vytvořenou entitu.

    > [!div class="mx-imgBorder"]
    > ![Dialogové okno Přidat předem vytvořenou entitu](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Vyberte **Done** (Hotovo). Po přidání entity není nutné tuto aplikaci podávat.

## <a name="add-a-prebuilt-domain-entity"></a>Přidat předem sestavenou doménovou entitu
1. Přihlaste se k [portálu Luis](https://www.luis.ai)a vyberte své **předplatné** a **prostředek pro vytváření obsahu** , abyste viděli aplikace přiřazené k tomuto zdrojovému prostředku.
1. Otevřete svou aplikaci tak, že na stránce **Moje aplikace** vyberete její název.
1. Na levé straně vyberte **entity** .

1. Na stránce **entity** vyberte **Přidat předem vytvořenou entitu domény**.

1. V dialogovém okně **Přidat předem připravené doménové modely** vyberte předem vytvořenou entitu domény.

1. Vyberte **Done** (Hotovo). Po přidání entity není nutné tuto aplikaci podávat.

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publikování pro zobrazení představěného modelu z předpovědi koncového bodu

Nejjednodušší způsob, jak zobrazit hodnotu předem vytvořeného modelu, je dotaz z publikovaného koncového bodu.

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entity obsahující předem sestavený token entity

Pokud máte entitu strojového učení, která potřebuje požadovanou funkci předem připravené entity, přidejte podentitu do entity Machine Learning a pak přidejte _požadovanou_ funkci předem připravené entity.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Sestavení modelu ze souboru. csv s rozhraními REST API](./luis-tutorial-node-import-utterances-csv.md)
