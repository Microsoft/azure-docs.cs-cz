---
title: Výuka modelu a vlastního překladatele
titleSuffix: Azure Cognitive Services
description: Školení modelu je důležitým krokem při vytváření modelu překladu. Školení probíhá na základě vybraných dokumentů pro tato školení.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: 5c17f842b93fb99a6a06b94f84ae26126794b776
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896422"
---
# <a name="train-a-model"></a>Učení modelu

Školení modelu je první a nejdůležitější krok k vytvoření modelu překladu, v opačném případě model nelze sestavit. Školení probíhá na základě vybraných dokumentů pro školení. Když vyberete dokumenty typu "školení", povědomi jsme minimální požadavky 10 000 paralelních vět. Při výběru dokumentů zobrazujeme celkový počet vět, které vás provedou. Tento požadavek neplatí, pokud pro výuku modelu vyberete pouze dokumenty typu dokumentu slovníku.

Postup při výukovém modelu:

1. Vyberte projekt, ve kterém chcete vytvořit model.

2. Karta data pro projekt zobrazí všechny relevantní dokumenty pro dvojici jazyka projektu. Ručně vyberte dokumenty, které chcete použít pro výuku modelu. Z této obrazovky můžete vybrat školení, ladění a testování dokumentů. Také stačí vybrat sadu školení a mít vlastní překladatel pro vás vytvořit sady pro optimalizaci a testování.

    - Název dokumentu: název dokumentu.

    - Párování: Pokud je tento dokument paralelním nebo monolingualm dokumentem. Dokumenty Monolingual se v tuto chvíli nepodporují pro školení.

    - Typ dokumentu: může být školení, ladění, testování nebo slovník.

    - Dvojice jazyků: Zobrazuje zdrojový a cílový jazyk projektu.

    - Zdrojové věty: zobrazuje počet vět extrahovaných ze zdrojového souboru.

    - Cílové věty: zobrazuje počet vět extrahovaných z cílového souboru.

    ![Trénování modelu](media/how-to/how-to-train-model.png)

3. Klikněte na tlačítko vytvořit model.

4. V dialogovém okně zadejte název vašeho modelu. Ve výchozím nastavení se při kliknutí na tlačítko vytvořit model vybere možnost "naučit se okamžitě" spustit kanál školení. Můžete vybrat možnost Uložit jako koncept a vytvořit metadata modelu a model uvést do stavu konceptu, ale školení modelu se nespustí. Později musíte ručně vybrat modely ve stavu konceptu pro vlak.

5. Klikněte na tlačítko vytvořit model.

    ![Dialog výukového modelu](media/how-to/how-to-train-model-2.png)

6. Vlastní Překladatel bude odesílat školení a na kartě modely zobrazí stav školení.

    ![Stránka výuka modelu](media/how-to/how-to-train-model-3.png)

>[!Note]
>Vlastní Překladatel podporuje v libovolném okamžiku 10 souběžných školení v pracovním prostoru.

## <a name="modify-a-model-name"></a>Úprava názvu modelu

Název modelu můžete upravit na stránce s podrobnostmi modelu.

1. Na stránce projekty klikněte na název projektu, kde model existuje.
2. Klikněte na kartu model.
3. Kliknutím na název modelu zobrazíte podrobnosti o modelu.
4. Klikněte na ikonu tužky.

    ![Upravit model](media/how-to/how-to-edit-model.png)

5. V dialogovém okně změňte název modelu a zadejte smysluplný název modelu.

    ![Dialogové okno Upravit další](media/how-to/how-to-edit-model-dialog.png)

6. Klikněte na Uložit.

## <a name="next-steps"></a>Další kroky

- Podívejte [se, jak zobrazit podrobnosti modelu](how-to-view-model-details.md).
