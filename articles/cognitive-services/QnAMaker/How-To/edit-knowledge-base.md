---
title: Úprava znalostní báze – QnA Maker
description: QnA Maker umožňuje spravovat obsah vaší znalostní báze tím, že poskytuje snadno použitelné editační prostředí.
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 223ad3a607adc20bbe608598da9742d56788b2c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131658"
---
# <a name="edit-qna-sets-in-your-knowledge-base"></a>Úprava sad QnA ve znalostní bázi

QnA Maker umožňuje spravovat obsah vaší znalostní báze tím, že poskytuje snadno použitelné editační prostředí.

QnA sady jsou přidány ze zdroje dat, jako je například soubor nebo adresa URL, nebo přidány jako redakční zdroj. Redakční zdroj označuje, že sada QnA byla přidána na portál QnA ručně. Všechny sady QnA jsou k dispozici pro úpravy.

## <a name="add-an-editorial-qna-set"></a>Přidání redakční sady QnA
1. Přihlaste se k [portálu QnA](https://www.qnamaker.ai/)a vyberte znalostní bázi, do které chcete přidat nastavenou hodnotu QnA.
1. Na stránce **EDIT** znalostní báze vyberte **Přidat sadu QnA a** přidejte novou sadu QnA.

1. Do nového řádku sady QnA přidejte požadovaná pole **Otázky** a **Odpověď.** Ostatní pole jsou nepovinná. Všechna pole lze kdykoli změnit.

1. Volitelně můžete přidat **alternativní formulace**. Alternativní formulace je jakákoli forma otázky, která se výrazně liší od původní otázky, ale měla by poskytnout stejnou odpověď.

    Když je vaše znalostní báze publikována a máte zapnuté aktivní učení, QnA Maker shromažďuje alternativní možnosti formulací, které můžete přijmout. Tyto volby jsou vybrány za účelem zvýšení přesnosti předpovědi.

1. Volitelně můžete přidat **metadata**. Chcete-li zobrazit metadata, vyberte **Zobrazit možnosti** v místní nabídce. Metadata poskytuje filtry na odpovědi, které poskytuje klientská aplikace, jako je například chatovací robot.

1. Volitelně můžete přidat **výzvy k zpracování**. Výzvy ke zpracování poskytují další cesty konverzace do klientské aplikace prezentovat uživateli.

1. Chcete-li zobrazit předpovědi včetně nové sady QnA, vyberte možnost **Uložit a trénovat.**

## <a name="edit-a-qna-set"></a>Úprava sady QnA

Libovolné pole v libovolné sadě QnA lze upravit bez ohledu na původní zdroj dat. Některá pole nemusí být viditelná z důvodu aktuálního nastavení **možností zobrazení,** které se nachází v panelu nástrojů kontextu.

## <a name="delete-a-qna-set"></a>Odstranění sady QnA

Chcete-li odstranit QnA, klikněte na ikonu **odstranění** zcela vpravo od řádku QnA. Jedná se o trvalou operaci. Nedá se to vrátit. Před odstraněním sad zvažte export kb ze stránky **Publikovat.**

![Odstranit sadu QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-set-id"></a>Vyhledání ID sady QnA

Pokud potřebujete najít ID sady QnA, najdete ho na dvou místech:

* Najeďte na ikonu odstranění na řádku sady QnA, který vás zajímá. Text při hvrhu obsahuje ID sady QnA.
* Exportujte znalostní bázi. Každá sada QnA ve znalostní bázi obsahuje ID sady QnA.

## <a name="add-alternate-questions"></a>Přidání alternativních otázek

Přidejte alternativní otázky do existující sady QnA, abyste zvýšili pravděpodobnost shody s uživatelským dotazem.

![Přidat alternativní otázky](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-sets"></a>Propojení sad QnA

Propojení sad QnA je opatřeno [výzvami pro zpracování](multiturn-conversation.md). Jedná se o logické spojení mezi sadami QnA, které jsou spravovány na úrovni znalostní báze. Na portálu QnA Maker můžete upravit výzvy pro zpracování.

V metadatech odpovědi nelze propojit sady QnA.

## <a name="add-metadata"></a>Přidání metadat

Přidejte sady metadat tak, že nejprve vyberete **možnosti zobrazení**a potom vyberete **Zobrazit metadata**. Zobrazí se sloupec metadat. Dále vyberte **+** znaménko pro přidání sady metadat. Tato sada se skládá z jednoho klíče a jedné hodnoty.

## <a name="save-changes-to-the-qna-sets"></a>Uložení změn do sad QnA

Pravidelně vyberte **uložit a trénovat** po provedení úprav, aby nedošlo ke ztrátě změn.

![Přidat metadata](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spolupráce na znalostní bázi](./collaborate-knowledge-base.md)

* [Správa prostředků Azure používaných qnA makerem](set-up-qnamaker-service-azure.md)