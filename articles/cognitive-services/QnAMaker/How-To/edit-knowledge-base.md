---
title: Úprava znalostní báze – QnA Maker
description: QnA Maker vám umožní spravovat obsah znalostní báze tím, že nabízí snadno použitelné prostředí pro úpravy.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756713"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Úprava párů QnA ve znalostní bázi

QnA Maker vám umožní spravovat obsah znalostní báze tím, že nabízí snadno použitelné prostředí pro úpravy.

Páry QnA jsou přidány ze zdroje dat, jako je například soubor nebo adresa URL, nebo přidány jako redakční zdroj. Redakční zdroj indikuje, že dvojice QnA byla přidána na portál QnA ručně. Všechny páry QnA jsou k dispozici pro úpravy.

## <a name="add-an-editorial-qna-pair"></a>Přidat redakční pár QnA
1. Přihlaste se k [portálu QnA](https://www.qnamaker.ai/)a pak vyberte znalostní bázi, do které chcete přidat dvojici QnA.
1. Na stránce pro **Úpravy** znalostní báze vyberte **Přidat dvojici QnA** a přidejte nový pár QnA.

1. Do nového řádku dvojice QnA přidejte pole požadovaná **otázka** a **odpověď** . Ostatní pole jsou volitelná. Všechna pole lze kdykoli změnit.

1. Volitelně můžete přidat **alternativní formulování**. Alternativní formulace je libovolná forma otázky, která se výrazně liší od původní otázky, ale měla by poskytnout stejnou odpověď.

    Když je vaše znalostní báze publikována a máte zapnuté [aktivní učení](use-active-learning.md) , QnA maker shromažďuje alternativní formulované možnosti, které můžete přijmout. Tyto možnosti jsou vybrány, aby bylo možné zvýšit přesnost předpovědi.

1. Volitelně můžete přidat **metadata**. Chcete-li zobrazit metadata, v místní nabídce vyberte možnost **Možnosti zobrazení** . Metadata poskytují filtry na odpovědi, které poskytuje klientská aplikace, jako je například robot v chatu.

1. Volitelně můžete přidat **výzvy pro následné**zpracování. Následné výzvy poskytují další cesty pro konverzaci k klientské aplikaci, která má být prezentována uživateli.

1. Pokud chcete zobrazit předpovědi včetně nového páru QnA, vyberte **Uložit a výuka** .

## <a name="edit-a-qna-pair"></a>Úprava páru QnA

Jakékoli pole v jakémkoli páru QnA lze upravovat bez ohledu na původní zdroj dat. Některá pole se nemusí zobrazit v důsledku aktuálního nastavení **možností zobrazení** , která se nachází na panelu nástrojů kontextu.

## <a name="delete-a-qna-pair"></a>Odstranit pár QnA

Pokud chcete odstranit QnA, klikněte na ikonu **Odstranit** na pravé straně řádku QnA. Toto je trvalá operace. Nedá se vrátit zpátky. Než budete odstraňovat sady, zvažte export znalostní báze ze stránky **publikování** .

![Odstranit pár QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Najít ID páru QnA

Pokud potřebujete najít ID páru QnA, můžete ho najít na dvou místech:

* Najeďte myší na ikonu Odstranit na řádku dvojice QnA, na kterou vás zajímáte. Text přechodu obsahuje ID páru QnA.
* Exportujte znalostní bázi. Každý pár QnA ve znalostní bázi obsahuje ID páru QnA.

## <a name="add-alternate-questions"></a>Přidat alternativní otázky

Přidejte alternativní otázky k existujícímu páru QnA a vylepšete pravděpodobnost shody s dotazem uživatele.

![Přidat alternativní otázky](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Propojování párů QnA

Propojení QnA párů je k dispozici pomocí [následných výzev](multiturn-conversation.md). Toto je logické propojení mezi páry QnA spravované na úrovni znalostní báze. Na QnA Makerovém portálu můžete upravovat výzvy pro zpracování.

Nemůžete propojit páry QnA v metadatech odpovědi.

## <a name="add-metadata"></a>Přidat metadata

Přidejte sady metadat pomocí prvního výběru **možností zobrazení**a pak vyberte **zobrazit metadata**. Tím se zobrazí sloupec metadata. Pak vyberte **+** znaménko a přidejte sadu metadat. Tato sada se skládá z jednoho klíče a jedné hodnoty.

## <a name="save-changes-to-the-qna-pairs"></a>Uložení změn do párů QnA

Po provedení úprav pravidelně vybírejte možnost **Uložit a výuka** , aby nedošlo ke ztrátě změn.

![Přidat metadata](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spolupráce na znalostní bázi](./collaborate-knowledge-base.md)

* [Správa prostředků Azure používaných QnA Maker](set-up-qnamaker-service-azure.md)