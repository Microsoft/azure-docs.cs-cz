---
title: Nasazení modelu pro Custom Speech – hlasové služby
titlesuffix: Azure Cognitive Services
description: V tomto dokumentu se dozvíte, jak vytvořit a nasadit pomocí portálu pro zpracování řeči vlastní koncový bod.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: fc51c1d9d47340da85d42f7c398c8ee21c601beb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65025867"
---
# <a name="deploy-a-custom-model"></a>Nasazení vlastního modelu

Po nahrání a prozkoumat data, vyhodnotí přesnost a školení vlastního modelu, můžete nasadit vlastní koncový bod pro použití s aplikací, nástroje a produkty. V tomto dokumentu se dozvíte, jak vytvořit a nasadit pomocí portálu pro zpracování řeči vlastní koncový bod.

## <a name="create-a-custom-endpoint"></a>Vytvoření vlastního koncového bodu

Chcete-li vytvořit nové vlastní koncový bod, vyberte **nasazení** Custom Speech nabídce v horní části stránky. Pokud je toto vaše první práce, můžete si všimnout, že neexistují žádné koncové body uvedené v tabulce. Po vytvoření koncového bodu, budete ke sledování jednotlivých nasazeného koncového bodu na této stránce.

V dalším kroku vyberte **přidat koncový bod** a zadejte **název** a **popis** pro váš vlastní koncový bod. Vyberte vlastní model, který chcete přidružit k tomuto koncovému bodu. Z této stránky můžete také povolit protokolování. Protokolování umožňuje monitorování provozu koncový bod. Pokud je zakázané, se nebudou ukládat provoz.

![Nasazení modelu](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Nezapomeňte přijměte podmínky použití a podrobnosti o cenách.

V dalším kroku vyberte **vytvořit**. Tím se vrátíte na **nasazení** stránky. Tabulka teď obsahuje záznam, který odpovídá vlastní koncový bod. Zobrazí se stav koncového bodu jejím aktuálním stavu. Může trvat až 30 minut pro vytvoření instance nového koncového bodu pomocí vlastních modelů. Když se změní stav nasazení pro **Complete**, koncový bod je připraven k použití.

Po nasazení je váš koncový bod, název koncového bodu se zobrazí jako odkaz. Klikněte na odkaz zobrazíte informace o konkrétní do vašeho koncového bodu, jako jsou klíče koncového bodu, adresa URL koncového bodu a ukázky kódu.

## <a name="view-logging-data"></a>Protokolování dat zobrazení

Protokolování dat je k dispozici ke stažení v rámci **koncový bod > Podrobnosti o**.

## <a name="next-steps"></a>Další postup

* Použít vlastní koncový bod s [sadou SDK pro řeč](speech-sdk.md)

## <a name="additional-resources"></a>Další materiály

* [Připravit a otestovat vašich dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení vašich dat](how-to-custom-speech-evaluate-data.md)
* [Vyzkoušejte svůj model](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)
