---
title: Správa nastavení modelu a učení – přizpůsobování
description: Nastavení strojového a výukového modelu, které se naučilo počítačem, se dá exportovat pro zálohování ve vlastním systému správy zdrojového kódu.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: f82adad5273f1c5559cbeb1924f59e57c863f6b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91303450"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Jak spravovat nastavení modelu a učení

Nastavení strojového a výukového modelu, které se naučilo počítačem, se dá exportovat pro zálohování ve vlastním systému správy zdrojového kódu.

## <a name="export-the-personalizer-model"></a>Export modelu přizpůsobeného pro přizpůsobování

V části Správa prostředků pro **Nastavení modelu a učení**si prohlédněte vytváření modelů a datum poslední aktualizace a exportujte aktuální model. Pomocí Azure Portal nebo rozhraní API pro přizpůsobení můžete exportovat soubor modelu pro účely archivace.

![Exportovat aktuální model přizpůsobeného přizpůsobování](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Vymazat data pro studijní smyčku

1. V Azure Portal na stránce **Nastavení modelu a učení** vyberte u svého prostředku pro přizpůsobování možnost **Vymazat data**.
1. Pokud chcete vymazat všechna data a resetovat smyčku učení do původního stavu, zaškrtněte políčko všechna 3 zaškrtávací políčka.

    ![V Azure Portal vymažte data z prostředku pro přizpůsobování.](./media/settings/clear-data-from-personalizer-resource.png)

    |Hodnota|Účel|
    |--|--|
    |Zaprotokolované individuální přizpůsobení a data odměna.|Tato data protokolování se používají při offline hodnocení. Vymažte data při resetování prostředku.|
    |Resetujte model přizpůsobeného přizpůsobování.|Tento model se při každém přeškolení mění. Tato frekvence školení je určena v **frekvence nahrávání modelu** na stránce **Konfigurace** . |
    |Nastavte zásady učení na výchozí.|Pokud jste zásady učení změnili jako součást testování offline, tato zásada obnoví původní zásady učení.|

1. Zaškrtnutím **políčka vymazat vybraná data** zahajte proces mazání. Stav je hlášen v oznámeních Azure v pravém horním navigačním panelu.

## <a name="import-a-new-learning-policy"></a>Importovat nové zásady učení

Nastavení [zásad učení](concept-active-learning.md#understand-learning-policy-settings) určuje základní _parametry_ školení modelů. Pokud chcete najít nové zásady učení, proveďte [offline vyhodnocení](how-to-offline-evaluation.md) .

1. Otevřete [Azure Portal](https://portal.azure.com)a vyberte prostředek pro přizpůsobování.
1. V části **Správa prostředků** vyberte **Nastavení modelu a učení** .
1. V části **Import výukového nastavení** vyberte soubor, který jste vytvořili ve formátu JSON, který jste zadali výše, a pak vyberte tlačítko **nahrát** .

    Počkejte, až se oznámení, že se zásady učení úspěšně nahrály.

## <a name="export-a-learning-policy"></a>Exportovat zásady učení

1. Otevřete [Azure Portal](https://portal.azure.com)a vyberte prostředek pro přizpůsobování.
1. V části **Správa prostředků** vyberte **Nastavení modelu a učení** .
1. V části **Nastavení výuky pro import** vyberte tlačítko **Exportovat nastavení učení** . Tím se `json` soubor uloží do místního počítače.

## <a name="next-steps"></a>Další kroky

[Naučte se spravovat zásady učení.](how-to-manage-model.md)
