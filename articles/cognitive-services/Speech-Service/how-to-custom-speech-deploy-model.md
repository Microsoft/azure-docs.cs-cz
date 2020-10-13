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
ms.openlocfilehash: c7f03027abf7f3c5e330e5cd95075cce1152a7d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85130411"
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
> [!NOTE]
>Data protokolování jsou k dispozici po dobu 30 dnů v úložišti vlastněných společností Microsoft a budou odebrána později. V případě, že účet úložiště vlastněné zákazníkem je propojený s předplatným služby rozpoznávání služeb, data protokolování se automaticky neodstraní.

## <a name="next-steps"></a>Další kroky

* Naučte se používat vlastní model [tady](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Další zdroje

* [Příprava a testování dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
* [Trénování modelu](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)
