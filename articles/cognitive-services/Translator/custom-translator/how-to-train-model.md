---
title: Trénování modelu - Vlastní překladač
titleSuffix: Azure Cognitive Services
description: Trénování modelu je důležitým krokem při vytváření modelu překladu. Školení probíhá na základě dokumentů, které vyberete pro tato školení.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: a875cc8f5e69be8b940f9d177d3b915bfaa2c973
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595677"
---
# <a name="train-a-model"></a>Učení modelu

Trénování modelu je důležitým krokem k vytvoření modelu překladu, protože bez školení nelze model sestavit. Školení probíhá na základě dokumentů, které vyberete pro školení.

Jak trénovat model:

1.  Vyberte projekt, ve kterém chcete vytvořit model.

2.  Na kartě Data pro projekt se zobrazí všechny příslušné dokumenty pro dvojici jazyků projektu. Ručně vyberte dokumenty, které chcete použít k trénování modelu. Na této obrazovce můžete vybrat dokumenty školení, ladění a testování. Také stačí vybrat tréninkovou sadu a mít vlastní překladač vytvořit tuning a testovací sady pro vás.

    -  Název dokumentu: Název dokumentu.

    -  Párování: Pokud je tento dokument paralelní nebo jednojazyčný dokument. Jednojazyčné dokumenty nejsou v současné době podporovány pro školení.

    -  Typ dokumentu: Může být školení, ladění, testování nebo slovník.

    -  Dvojice jazyků: Zobrazí zdrojový a cílový jazyk projektu.

    -  Zdrojové věty: Zobrazuje počet vět extrahovaných ze zdrojového souboru.

    -  Cílové věty: Zobrazuje počet vět extrahovaných z cílového souboru.

    ![Trénování modelu](media/how-to/how-to-train-model.png)

3.  Klikněte na tlačítko Vlak.

4.  V dialogovém okně zadejte název modelu.

5.  Klikněte na Model vlaku.

    ![Dialogové okno modelu vlaku](media/how-to/how-to-train-model-2.png)

6.  Vlastní Překladač odešle školení a zobrazí stav školení v kartě modely.

    ![Stránka modelu vlaku](media/how-to/how-to-train-model-3.png)

>[!Note]
>Vlastní překladač podporuje 10 souběžných školení v rámci pracovního prostoru v libovolném okamžiku.


## <a name="edit-a-model"></a>Úprava modelu

Model můžete upravit pomocí odkazu Upravit na stránce Podrobnosti modelu.

1.  Klikněte na ikonu Tužka.

    ![Upravit model](media/how-to/how-to-edit-model.png)

2.  V dialogu změnit,

    1.  Název modelu (povinné): Pojmenujte model smysluplným názvem.

        ![Dialogové okno Upravit další](media/how-to/how-to-edit-model-dialog.png)

3.  Klikněte na Uložit.


## <a name="next-steps"></a>Další kroky

- Přečtěte [si, jak zobrazit podrobnosti o modelu](how-to-view-model-details.md).
