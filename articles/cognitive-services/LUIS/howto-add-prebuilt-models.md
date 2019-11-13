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
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: 0a733aff14b71c6378971c0b72a9ca1f3cd4cfc3
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74013595"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>Přidat předem připravené modely pro běžné scénáře použití 

LUIS zahrnuje sadu předem připravených modelů pro rychlé přidávání běžných scénářů pro konverzační uživatele. Toto je rychlý a snadný způsob, jak přidat možnosti do klientské aplikace v konverzaci, aniž byste museli navrhovat modely pro tyto možnosti. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-a-prebuilt-domain"></a>Přidání předem připravené domény

1. Na **Moje aplikace** stránky, vyberte svou aplikaci. Tím se otevře aplikaci tak, aby **sestavení** části aplikace. 

1. Na levém panelu nástrojů vyberte **předem sestavené domény** . 

1. Najděte doménu, kterou chcete přidat do aplikace, a pak vyberte tlačítko **Přidat doménu** .

    > [!div class="mx-imgBorder"]
    > ![přidat předem sestavenou doménu](./media/luis-prebuilt-domains/add-prebuilt-domain.png) kalendáře

## <a name="add-a-prebuilt-intent"></a>Přidat předem připravených záměr

1. Na **Moje aplikace** stránky, vyberte svou aplikaci. Tím se otevře aplikaci tak, aby **sestavení** části aplikace. 

1. Na stránce **záměry** vyberte **Přidat předem sestavený záměr domény** z panelu nástrojů nad seznamem záměry. 

1. Vyberte **Utilities.Cancel** záměru v místním dialogovém okně. 

    > [!div class="mx-imgBorder"]
    > ![přidat předdefinovaný záměr](./media/luis-prebuilt-domains/add-prebuilt-domain-intents.png)

1. Vyberte **provádí** tlačítko.

## <a name="add-a-prebuilt-entity"></a>Přidat předem připravených entit

1. Otevřete aplikaci klepnutím na jeho název na **Moje aplikace** stránce a potom klikněte na tlačítko **entity** na levé straně. 

1. Na **entity** klikněte na **přidat předem připravených entit**.

1. V dialogovém okně **Přidat předem připravené entity** vyberte předem vytvořenou entitu. 

    > [!div class="mx-imgBorder"]
    > Dialogové okno ![přidat předem vytvořenou entitu](./media/luis-prebuilt-domains/add-prebuilt-entity.png)

1. Vyberte **Done** (Hotovo). Po přidání entity není potřeba trénování aplikace. 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>Publikování pro zobrazení představěného modelu z předpovědi koncového bodu

Nejjednodušší způsob, jak zobrazit hodnotu předem vytvořeného modelu, je dotaz z publikovaného koncového bodu. 

## <a name="entities-containing-a-prebuilt-entity-token"></a>Entity obsahující předem sestavený token entity
 
Pokud máte entitu získanou počítačem, která je omezená předem vytvořenou entitou, přidejte do ní podsoučást a pak přidejte omezení předem sestavené entity.

## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Sestavení modelu ze souboru. csv s rozhraními REST API](./luis-tutorial-node-import-utterances-csv.md)
