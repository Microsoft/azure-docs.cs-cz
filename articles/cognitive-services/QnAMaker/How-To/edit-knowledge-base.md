---
title: Úprava znalostní báze – QnA Maker
description: QnA Maker umožňuje spravovat obsah vaší znalostní báze tím, že poskytuje snadno použitelné editační prostředí.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756713"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Úprava dvojic QnA ve znalostní bázi

QnA Maker umožňuje spravovat obsah vaší znalostní báze tím, že poskytuje snadno použitelné editační prostředí.

Dvojice QnA jsou přidány ze zdroje dat, například ze souboru nebo adresy URL, nebo přidány jako redakční zdroj. Redakční zdroj označuje, že dvojice QnA byla přidána na portál QnA ručně. Všechny dvojice QnA jsou k dispozici pro úpravy.

## <a name="add-an-editorial-qna-pair"></a>Přidání redakčnídvojice QnA
1. Přihlaste se k [portálu QnA](https://www.qnamaker.ai/)a vyberte znalostní bázi, do které chcete přidat dvojici QnA.
1. Na stránce **EDIT** znalostní báze vyberte **Přidat dvojici QnA a** přidejte nový pár QnA.

1. Do nového řádku dvojice QnA přidejte požadovaná pole **Otázky** a **Odpověď.** Ostatní pole jsou nepovinná. Všechna pole lze kdykoli změnit.

1. Volitelně můžete přidat **alternativní formulace**. Alternativní formulace je jakákoli forma otázky, která se výrazně liší od původní otázky, ale měla by poskytnout stejnou odpověď.

    Když je vaše znalostní báze publikována a máte zapnuté [aktivní učení,](use-active-learning.md) QnA Maker shromažďuje alternativní možnosti formulací, které můžete přijmout. Tyto volby jsou vybrány za účelem zvýšení přesnosti předpovědi.

1. Volitelně můžete přidat **metadata**. Chcete-li zobrazit metadata, vyberte **Zobrazit možnosti** v místní nabídce. Metadata poskytuje filtry na odpovědi, které poskytuje klientská aplikace, jako je například chatovací robot.

1. Volitelně můžete přidat **výzvy k zpracování**. Výzvy ke zpracování poskytují další cesty konverzace do klientské aplikace prezentovat uživateli.

1. Chcete-li zobrazit předpovědi včetně nové dvojice QnA, vyberte **možnost Uložit a trénovat.**

## <a name="edit-a-qna-pair"></a>Úprava dvojice QnA

Libovolné pole v libovolné dvojici QnA lze upravit bez ohledu na původní zdroj dat. Některá pole nemusí být viditelná z důvodu aktuálního nastavení **možností zobrazení,** které se nachází v panelu nástrojů kontextu.

## <a name="delete-a-qna-pair"></a>Odstranění dvojice QnA

Chcete-li odstranit QnA, klikněte na ikonu **odstranění** zcela vpravo od řádku QnA. Jedná se o trvalou operaci. Nedá se to vrátit. Před odstraněním sad zvažte export kb ze stránky **Publikovat.**

![Odstranit dvojici QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Vyhledání ID dvojice QnA

Pokud potřebujete najít ID dvojice QnA, najdete ho na dvou místech:

* Najeďte na ikonu odstranění na řádku dvojice QnA, která vás zajímá. Text přihrádání obsahuje ID dvojice QnA.
* Exportujte znalostní bázi. Každý pár QnA ve znalostní bázi obsahuje ID dvojice QnA.

## <a name="add-alternate-questions"></a>Přidání alternativních otázek

Přidejte alternativní otázky k existující dvojici QnA, abyste zvýšili pravděpodobnost shody s uživatelským dotazem.

![Přidat alternativní otázky](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Propojení párů QnA

Propojení párů QnA je opatřeno [následnými výzvami](multiturn-conversation.md). Toto je logické spojení mezi dvojicemi QnA, které jsou spravovány na úrovni znalostní báze. Na portálu QnA Maker můžete upravit výzvy pro zpracování.

V metadatech odpovědi nelze propojit dvojice QnA.

## <a name="add-metadata"></a>Přidání metadat

Přidejte sady metadat tak, že nejprve vyberete **možnosti zobrazení**a potom vyberete **Zobrazit metadata**. Zobrazí se sloupec metadat. Dále vyberte **+** znaménko pro přidání sady metadat. Tato sada se skládá z jednoho klíče a jedné hodnoty.

## <a name="save-changes-to-the-qna-pairs"></a>Uložit změny do dvojic QnA

Pravidelně vyberte **uložit a trénovat** po provedení úprav, aby nedošlo ke ztrátě změn.

![Přidat metadata](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spolupráce na znalostní bázi](./collaborate-knowledge-base.md)

* [Správa prostředků Azure používaných qnA makerem](set-up-qnamaker-service-azure.md)