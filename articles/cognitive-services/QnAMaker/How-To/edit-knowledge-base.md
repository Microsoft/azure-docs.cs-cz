---
title: Úprava znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker umožňuje spravovat obsah znalostní báze tím, že poskytuje editační rozhraní snadným ovládáním.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 11/08/2018
ms.author: tulasim
ms.openlocfilehash: fa5b5e1998d54c7b22d01e7cc70e84059d482cfb
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2018
ms.locfileid: "51623542"
---
# <a name="edit-a-knowledge-base"></a>Úprava znalostní báze

Nástroj QnA Maker umožňuje spravovat obsah znalostní báze tím, že poskytuje editační rozhraní snadným ovládáním.

## <a name="edit-your-knowledge-base-content"></a>Upravit obsah znalostní báze

1.  Vyberte **Moje znalostních bází** v horním navigačním panelu. 

    Zobrazí se všechny služby vytvořené nebo nasdílené můžete seřadit v sestupném pořadí **naposledy změněno** datum.

    ![Moje znalostních bází](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Vyberte konkrétní znalostní bázi, provádět úpravy.
 
1. Vyberte **nastavení**. Tady můžete upravit povinného pole název služby.
  
    |Cíl|Akce|
    |--|--|
    |Přidat adresu URL|Můžete přidat nové adresy URL přidáte nový obsah častých otázek znalostní báze kliknutím **znalostní báze spravovat -> ' + přidat adresu URL'** odkaz.|
    |Odstranit adresu URL|Existující adresy URL můžete odstranit tak, že vyberete ikonu odstranit, můžete do koše.|
    |Aktualizovat adresu URL obsahu|Pokud chcete znalostní báze k procházení nejnovější obsah stávající adresy URL, vyberte **aktualizovat** zaškrtávací políčko. Tím se aktualizují znalostní báze s nejnovější obsah adresy URL.|
    |Přidat soubor|Můžete přidat soubor s podporovaným dokumentu jako součást znalostní báze, tak, že vyberete **Správa znalostní báze**, pak vyberete **+ přidat soubor**|
    |Import|Můžete také importovat všechny existující znalostní báze tak, že vyberete **Ímport znalostní báze** tlačítko. |
    |Aktualizace|Aktualizace znalostní báze knowledge base, závisí na **cenovou úroveň účtu správy** použit při vytváření služby QnA Maker přidružené k vaší znalostní báze knowledge base. V případě potřeby můžete také aktualizovat úroveň správy z webu Azure portal.

1. Po dokončení provádění změn ve znalostní bázi, vyberte **uložit a jejich trénování** v pravém horním rohu stránky zachová tak změny.    

    ![Uložit a jejich trénování](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Pokud stránku opustit výběr **uložit a jejich trénování**, všechny změny budou ztraceny.

## <a name="add-a-qna-pair"></a>Přidání páru otázky a odpovědi

Vyberte **QnA přidat pár** přidáte nový řádek do tabulky znalostní báze knowledge base.

![Přidat dvojici QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Odstranit pár QnA

Pokud chcete odstranit QnA, klikněte na tlačítko **odstranit** ikonu vpravo na konci řádku QnA. Toto je nevratná operace. Nedá se vrátit zpět. Vezměte v úvahu export znalostní BÁZÍ z **publikovat** stránky před odstraněním dvojice. 

![Odstranit QnA pár](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Přidat alternativní otázky

Přidáte alternativní dotazy na existující dvojice QnA ke zlepšení pravděpodobnost, že shoda pro uživatelský dotaz.

![Přidat alternativní otázky](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Přidání metadat


Přidat páry metadata tak, že vyberete ikonu filtru

![Přidání metadat](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Ujistěte se, že pravidelně ukládat a trénování znalostní báze po provedení úprav, abyste se vyhnuli ztrátě změny.

## <a name="manage-large-knowledge-bases"></a>Správa velkých znalostních bází

1. Budou maximálně **seskupené** ve zdroji dat, ze kterého byly extrahovány. Můžete rozbalit nebo sbalit zdroj dat.
2. Je možné **hledání** znalostní báze tak, že zadáte do textového pole v horní části tabulce znalostní báze Knowledge Base. Chcete-li vyhledat obsah dotaz, odpověď nebo metadata, klikněte na enter. Klikněte na ikonu X odebrat vyhledávací filtr.
3. **Stránkování** umožňuje spravovat velké znalostních bází

    ![Hledání, stránkování, skupiny](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="delete-knowledge-bases"></a>Odstranit znalostních bází

Odstraňuje se znalostní bázi (KB) je nevratná operace. Nedá se vrátit zpět. Před odstraněním znalostní báze, je třeba exportovat ve znalostní bázi z **nastavení** stránky portál QnA Maker. 

Pokud sdílíte znalostní BÁZÍ s [spolupracovníci](collaborate-knowledge-base.md) odstraňte ji, všichni ztratí přístup k KB. 

## <a name="delete-azure-resources"></a>Odstranění prostředků Azure 

Pokud odstraníte všechny prostředky Azure používané pro vaše nástroje QnA Maker znalostních bází, znalostních bází nebude fungovat. Před odstraněním všechny prostředky, ujistěte se, že exportujete vaše znalostních bází z **nastavení** stránky. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Spolupráce na znalostní báze](./collaborate-knowledge-base.md)
