---
title: Výuka modelu a vlastního překladatele
titleSuffix: Azure Cognitive Services
description: Školení modelu je důležitým krokem při vytváření modelu překladu. Školení probíhá na základě vybraných dokumentů pro tato školení.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595677"
---
# <a name="train-a-model"></a>Učení modelu

Školení modelu je důležitým krokem pro sestavení modelu překladu, protože bez školení nejde model sestavit. Školení probíhá na základě vybraných dokumentů pro školení.

Postup při výukovém modelu:

1.  Vyberte projekt, ve kterém chcete vytvořit model.

2.  Karta data pro projekt zobrazí všechny relevantní dokumenty pro dvojici jazyka projektu. Ručně vyberte dokumenty, které chcete použít pro výuku modelu. Z této obrazovky můžete vybrat školení, ladění a testování dokumentů. Také stačí vybrat sadu školení a mít vlastní překladatel pro vás vytvořit sady pro optimalizaci a testování.

    -  Název dokumentu: Název dokumentu

    -  Párování Pokud je tento dokument paralelním nebo monolingualm dokumentem. Dokumenty Monolingual se v tuto chvíli nepodporují pro školení.

    -  Typ dokumentu: Může být školení, optimalizace, testování nebo slovník.

    -  Dvojice jazyků: Tím se zobrazí zdrojový a cílový jazyk projektu.

    -  Zdrojové věty: Zobrazuje počet vět extrahovaných ze zdrojového souboru.

    -  Cílové věty: Zobrazuje počet vět extrahovaných z cílového souboru.

    ![Model výuky](media/how-to/how-to-train-model.png)

3.  Klikněte na tlačítko výuka.

4.  V dialogovém okně zadejte název svého modelu.

5.  Klikněte na výuka model.

    ![Dialog výukového modelu](media/how-to/how-to-train-model-2.png)

6.  Vlastní Překladatel bude odesílat školení a na kartě modely zobrazí stav školení.

    ![Stránka výuka modelu](media/how-to/how-to-train-model-3.png)

>[!Note]
>Vlastní Překladatel podporuje v libovolném okamžiku 10 souběžných školení v pracovním prostoru.


## <a name="edit-a-model"></a>Úprava modelu

Model můžete upravit pomocí odkazu upravit na stránce s podrobnostmi modelu.

1.  Klikněte na ikonu tužky.

    ![Upravit model](media/how-to/how-to-edit-model.png)

2.  V dialogu se změní

    1.  Název modelu (povinné): Poskytněte vašemu modelu smysluplný název.

        ![Dialogové okno Upravit další](media/how-to/how-to-edit-model-dialog.png)

3.  Klikněte na Uložit.


## <a name="next-steps"></a>Další postup

- Podívejte [se, jak zobrazit podrobnosti modelu](how-to-view-model-details.md).
