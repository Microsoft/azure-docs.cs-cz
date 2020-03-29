---
title: Nasazení modelu pro vlastní řeč – řečovou službu
titleSuffix: Azure Cognitive Services
description: V tomto dokumentu se dozvíte, jak vytvořit a nasadit koncový bod pomocí portálu vlastní řeči.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: e41653e4644b09cc357510223e5ee86c3806ae2c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806109"
---
# <a name="deploy-a-custom-model"></a>Nasazení vlastního modelu

Po nahrání a kontrole dat, vyhodnocení přesnosti a trénování vlastního modelu můžete nasadit vlastní koncový bod, který se bude používat s aplikacemi, nástroji a produkty. V tomto dokumentu se dozvíte, jak vytvořit a nasadit koncový bod pomocí [portálu vlastní řeči](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Vytvoření vlastního koncového bodu

Chcete-li vytvořit nový vlastní koncový bod, přihlaste se k [portálu vlastní řeči](https://speech.microsoft.com/customspeech) a vyberte **nasazení** z nabídky Vlastní řeč v horní části stránky. Pokud se jedná o první spuštění, zjistíte, že v tabulce nejsou uvedeny žádné koncové body. Po vytvoření koncového bodu použijete tuto stránku ke sledování každého nasazeného koncového bodu.

Dále vyberte **Přidat koncový bod** a zadejte **název** a **popis** pro vlastní koncový bod. Pak vyberte vlastní model, který chcete přidružit k tomuto koncovému bodu. Na této stránce můžete také povolit protokolování. Protokolování umožňuje sledovat přenos koncového bodu. Pokud je zakázánprovoz, provoz se neuloží.

![Jak nasadit model](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Nezapomeňte přijmout podmínky použití a podrobnosti o cenách.

Dále vyberte **Vytvořit**. Tato akce vás vrátí na stránku **Nasazení.** Tabulka nyní obsahuje položku, která odpovídá vašemu vlastnímu koncovému bodu. Stav koncového bodu zobrazuje jeho aktuální stav. Vytvoření instance nového koncového bodu pomocí vlastních modelů může trvat až 30 minut. Když se stav nasazení změní na **Dokončení**, koncový bod je připraven k použití.

Po nasazení koncového bodu se název koncového bodu zobrazí jako odkaz. Kliknutím na odkaz zobrazíte informace specifické pro koncový bod, jako je klíč koncového bodu, adresa URL koncového bodu a ukázkový kód.

## <a name="view-logging-data"></a>Zobrazení dat protokolování

Data protokolování jsou k dispozici ke stažení v části **Podrobnosti o > koncového bodu**.

## <a name="next-steps"></a>Další kroky

* Použití vlastního koncového bodu se sadou [Speech SDK](speech-sdk.md)

## <a name="additional-resources"></a>Další zdroje

* [Příprava a testování dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
* [Trénování vašeho modelu](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)
