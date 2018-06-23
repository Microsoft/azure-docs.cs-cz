---
title: Úprava znalostní báze – kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Úprava znalostní bázi
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: eaa65bf3d257399fceadaa42f0d9ddbbf8afe234
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343268"
---
# <a name="edit-a-knowledge-base"></a>Upravit znalostní bázi knowledge base

QnA Maker umožňuje spravovat obsah znalostní báze tím, že poskytuje snadno použitelný úpravy prostředí.

## <a name="edit-your-knowledge-base-content"></a>Upravit obsah znalostní báze knowledge base

1.  Vyberte **Moje znalostních bází** v horním navigačním panelu. 

    Zobrazí se všechny služby vytvořené nebo sdílené s vámi seřazené v sestupném pořadí podle **poslední změny** datum.

    ![Moje znalostních bází](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. Vyberte konkrétní znalostní bázi, provádět úpravy.

3. Po dokončení změn ve znalostní bázi, klikněte na **uložte a cvičení** v pravém horním rohu stránky, aby zachovaly změny.    

    ![Uložte a školení](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    Opouštění stránce před kliknutím na Uložit a train nebude zachovaly změny.

## <a name="add-a-qna-pair"></a>Přidat dvojici QnA

Vyberte **QnA přidat pár** přidání nového řádku do tabulky znalostní báze knowledge base.

![Přidat pár QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Odstranit pár QnA

Chcete-li odstranit QnA, klikněte na tlačítko **odstranit** ikonu na pravém konci řádku QnA.

![Odstranit QnA pár](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Přidat alternativní otázky

Přidejte alternativní dotazy do existující dvojice QnA ke zlepšení pravděpodobnost shoda pro dotaz uživatele.

![Přidat alternativní otázky](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Přidání metadat


Přidat metadata páry výběrem ikony filtru

![Přidání metadat](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Zajistěte, aby pravidelně uložte a po provedení úpravy, aby nedošlo ke ztrátě změny cvičení znalostní bázi knowledge base.

## <a name="manage-large-knowledge-bases"></a>Spravovat velké znalostních bází

1. Jsou QnAs **seskupené** zdroj dat, ze kterého byly extrahovány. Můžete rozbalit nebo sbalit zdroj dat.
2. Můžete **vyhledávání** znalostní báze tak, že zadáte do textového pole v horní části tabulky znalostní báze Knowledge Base. Kliknutím zadejte pro vyhledávání na otázku, odpovědí nebo metadata obsahu. Klikněte na ikonu X odebrat vyhledávací filtr.
3. **Stránkování** umožňuje spravovat velké znalostních bází

    ![Hledání, stránkování, skupiny](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Spolupracujte na znalostní bázi](./collaborate-knowledge-base.md)
