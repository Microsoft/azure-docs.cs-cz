---
title: Nasazení modelu pro službu Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: V tomto dokumentu se dozvíte, jak vytvořit a nasadit koncový bod pomocí portálu Custom Speech.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: e41653e4644b09cc357510223e5ee86c3806ae2c
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806109"
---
# <a name="deploy-a-custom-model"></a>Nasazení vlastního modelu

Po nahrání a kontrole dat, vyhodnocení přesnosti a školení vlastního modelu můžete nasadit vlastní koncový bod pro použití s aplikacemi, nástroji a produkty. V tomto dokumentu se dozvíte, jak vytvořit a nasadit koncový bod pomocí [portálu Custom Speech](https://speech.microsoft.com/customspeech).

## <a name="create-a-custom-endpoint"></a>Vytvoření vlastního koncového bodu

Pokud chcete vytvořit nový vlastní koncový bod, přihlaste se k [portálu Custom Speech](https://speech.microsoft.com/customspeech) a v horní části stránky vyberte **nasazení** v nabídce Custom Speech. Pokud se jedná o první spuštění, všimnete si, že v tabulce nejsou uvedeny žádné koncové body. Po vytvoření koncového bodu použijete tuto stránku ke sledování každého nasazeného koncového bodu.

V dalším kroku vyberte **přidat koncový bod** a zadejte **název** a **Popis** vlastního koncového bodu. Pak vyberte vlastní model, který byste chtěli přidružit k tomuto koncovému bodu. Na této stránce můžete také povolit protokolování. Protokolování umožňuje monitorovat provoz koncového bodu. Pokud je tato zakázaná, provoz se neuloží.

![Postup nasazení modelu](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Nezapomeňte přijmout podmínky použití a podrobnosti o cenách.

V dalším kroku vyberte **vytvořit**. Tato akce vrátí na stránku **nasazení** . Tabulka teď obsahuje položku, která odpovídá vašemu vlastnímu koncovému bodu. Stav koncového bodu zobrazuje aktuální stav. Vytvoření instance nového koncového bodu pomocí vlastních modelů může trvat až 30 minut. Když se změní stav nasazení na **dokončeno**, bude koncový bod připravený k použití.

Po nasazení koncového bodu se název koncového bodu zobrazí jako odkaz. Kliknutím na odkaz zobrazíte informace, které jsou specifické pro váš koncový bod, jako je klíč koncového bodu, adresa URL koncového bodu a ukázkový kód.

## <a name="view-logging-data"></a>Zobrazit data protokolování

Data protokolování jsou k dispozici ke stažení v části **koncový bod > podrobnosti**.

## <a name="next-steps"></a>Další kroky

* Použití vlastního koncového bodu se sadou [Speech SDK](speech-sdk.md)

## <a name="additional-resources"></a>Další zdroje informací:

* [Příprava a testování dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
* [Výuka modelu](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)
