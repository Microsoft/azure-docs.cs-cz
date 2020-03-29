---
title: Správa nastavení modelu a výuky – personalizátor
description: Strojově navedutou model a nastavení učení lze exportovat pro zálohování ve vašem vlastním systému správy zdrojového kódu.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: c544d058019c8d507f65dc6bfd854376b364abd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624294"
---
# <a name="how-to-manage-model-and-learning-settings"></a>Jak spravovat nastavení modelu a učení

Strojově navedutou model a nastavení učení lze exportovat pro zálohování ve vašem vlastním systému správy zdrojového kódu.

## <a name="export-the-personalizer-model"></a>Export modelu personalisty

V části Správa zdrojů pro **model a nastavení učení**zkontrolujte vytvoření modelu a datum poslední aktualizace a exportujte aktuální model. K exportu souboru modelu pro účely archivace můžete použít portál Azure nebo uživatelská api.

![Exportovat aktuální model personalisty](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Vymazání dat pro vaši učební smyčku

1. Na webu Azure Portal vyberte pro prostředek personalisty na stránce **Model a nastavení učení** možnost Vymazat **data**.
1. Chcete-li vymazat všechna data a obnovit smyčku učení do původního stavu, zaškrtněte všechna 3 políčka.

    ![Na webu Azure Portal vymažte data z prostředků personalisty.](./media/settings/clear-data-from-personalizer-resource.png)

    |Hodnota|Účel|
    |--|--|
    |Protokolovaná data přizpůsobení a odměn.|Tato data protokolování se používají v offline hodnocení. Pokud resetujete prostředek, vymažte data.|
    |Resetujte model personalisty.|Tento model se mění při každém přeškolení. Tato frekvence školení je specifikována v **četnosti nahraných modelů** na stránce **Konfigurace.** |
    |Nastavte výchozí zásady učení.|Pokud jste změnili zásady učení jako součást offline hodnocení, obnoví se původní zásady učení.|

1. Chcete-li zahájit proces vymazání, vyberte **vymazat vybraná data.** Stav se hlásí v oznámeních Azure v navigaci vpravo nahoře.

## <a name="import-a-new-learning-policy"></a>Import nové zásady učení

Nastavení [zásad učení](concept-active-learning.md#understand-learning-policy-settings) určuje _hyperparametry_ trénování modelu. Proveďte [offline hodnocení](how-to-offline-evaluation.md) a vyhledejte nové zásady učení.

1. Otevřete [portál Azure](https://portal.azure.com)a vyberte prostředek personalisty.
1. V části **Správa zdrojů** vyberte nastavení modelu **a výuky.**
1. V **nastavení importu výuky** vyberte soubor, který jste vytvořili ve výše určeném formátu JSON, a pak vyberte tlačítko **Nahrát.**

    Počkejte na oznámení, že zásady učení byla odeslána úspěšně.

## <a name="export-a-learning-policy"></a>Export zásad učení

1. Otevřete [portál Azure](https://portal.azure.com)a vyberte prostředek personalisty.
1. V části **Správa zdrojů** vyberte nastavení modelu **a výuky.**
1. V **nastavení importu výuky** vyberte tlačítko **Exportovat nastavení výuky.** Tím uložíte `json` soubor do místního počítače.

## <a name="next-steps"></a>Další kroky

[Přečtěte si, jak spravovat zásady učení](how-to-manage-model.md)
