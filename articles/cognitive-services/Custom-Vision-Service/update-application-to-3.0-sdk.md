---
title: Postup aktualizace projektu na rozhraní 3,0 API
titleSuffix: Azure Cognitive Services
description: Naučte se aktualizovat Custom Vision projekty z předchozí verze rozhraní API na rozhraní 3,0 API.
services: cognitive-services
author: areddish
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: areddish
ms.openlocfilehash: c134f30b124113a23df0e73cd1bbc8209e335183
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "73647506"
---
# <a name="update-to-the-30-api"></a>Aktualizace rozhraní 3,0 API

Custom Vision nyní dosáhla obecné dostupnosti a prošla aktualizací rozhraní API.
Tato aktualizace zahrnuje několik nových funkcí a důležité je několik podstatných změn:

* Předpovědi rozhraní API je teď rozdělené na dvě na základě typu projektu.
* Možnost exportu Vision AI Developer Kit (VAIDK) vyžaduje, aby projekt vytvořil konkrétní způsob.
* Výchozí iterace se odebraly namísto publikování nebo zrušení publikování pojmenované iterace.

V této příručce se dozvíte, jak aktualizovat projekty tak, aby fungovaly s novou verzí rozhraní API. Úplný seznam změn najdete v [poznámkách k verzi](release-notes.md) .

## <a name="use-the-updated-prediction-api"></a>Použití aktualizované předpovědi rozhraní API

Rozhraní API 2. x používala stejné prediktivní volání pro klasifikátory obrázků i pro projekty detektorů objektů. Oba typy projektů byly přijatelné pro volání **PredictImage** a **PredictImageUrl** . Od 3,0 jsme toto rozhraní API rozdělili tak, aby bylo nutné odpovídat typu projektu volání:

* Použijte **[ClassifyImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)** a **[ClassifyImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c14)** k získání předpovědi pro projekty klasifikace obrázků.
* K získání předpovědi pro projekty detekce objektů použijte **[DetectImage](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c19)** a **[DetectImageUrl](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c18)** .

## <a name="use-the-new-iteration-publishing-workflow"></a>Použít nový pracovní postup publikování iterace

Rozhraní API 2. x používaly výchozí iteraci nebo zadané ID iterace k výběru iterace, která se má použít pro předpověď. Počínaje 3,0 jsme přijali postup publikování, který vám při prvním publikování iterace v rámci zadaného názvu z rozhraní API pro školení umožňuje. Pak předáte název k metodám předpovědi, abyste určili, která iterace se má použít.

> [!IMPORTANT]
> Rozhraní API 3,0 nepoužívají výchozí funkci iterace. Až zařadíme starší rozhraní API, můžete i nadále používat rozhraní API 2. x k přepínání iterace jako výchozí. Tato rozhraní API budou udržována po určitou dobu a voláním metody **[UpdateIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b818)** můžete označit iteraci jako výchozí.

### <a name="publish-an-iteration"></a>Publikování iterace

Jakmile je iterace vyškolená, můžete ji zpřístupnit pro předpověď pomocí metody **[PublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c82db28bf6a2b11a8247bbc)** . K publikování iterace budete potřebovat ID prostředku předpovědi, který je k dispozici na stránce nastavení webu CustomVision.

![Stránka nastavení webu Custom Vision s ID prostředku předpovědi.](./media/update-application-to-3.0-sdk/prediction-id.png)

> [!TIP]
> Tyto informace můžete získat také z webu [Azure Portal](https://portal.azure.com) tak, že na prostředku vyberete Custom Vision předpověď a vyberete **vlastnosti**.

Jakmile je vaše iterace publikovaná, aplikace je může použít k předpovědi zadáním názvu ve svém prediktivním volání rozhraní API. K vytvoření iterace pro předpověď volání použijte rozhraní **[UnpublishIteration](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Training_3.0/operations/5c771cdcbf6a2b18a0c3b81a)** API.

## <a name="next-steps"></a>Další kroky

* [Referenční dokumentace k rozhraní API pro školení (REST)](https://go.microsoft.com/fwlink/?linkid=865446)
* [Referenční dokumentace k prediktivním rozhraním API (REST)](https://go.microsoft.com/fwlink/?linkid=865445)