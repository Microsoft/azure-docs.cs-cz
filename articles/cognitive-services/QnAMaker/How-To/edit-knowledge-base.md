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
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: f927e5b7ff65b82aef9d4224d22296e0fa48ad59
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451880"
---
# <a name="edit-a-knowledge-base"></a>Úprava znalostní báze

Nástroj QnA Maker umožňuje spravovat obsah znalostní báze tím, že poskytuje editační rozhraní snadným ovládáním.

## <a name="edit-your-knowledge-base-content"></a>Upravit obsah znalostní báze

1.  Vyberte **Moje znalostních bází** v horním navigačním panelu. 

    Zobrazí se všechny služby vytvořené nebo nasdílené můžete seřadit v sestupném pořadí **naposledy změněno** datum.

    ![Moje znalostních bází](../media/qnamaker-how-to-edit-kb/my-kbs.png)

2. Vyberte konkrétní znalostní bázi, provádět úpravy.
 
3. Klikněte na **Nastavení**.

   Tady můžete upravit povinného pole název služby.
  
   Můžete přidat nové adresy URL k přidání nové nejčastější dotazy k obsahu na webech kliknutím **webech spravovat -> ' + přidat adresu URL'** odkaz.
   
   Existující adresy URL můžete odstranit kliknutím na **ikona odstranění**.
   
   Pokud chcete webech k procházení nejnovější obsah stávající adresy URL, zaškrtněte políčko název **'Aktualizovat'**, tím se aktualizuje webech s nejnovější obsah adresy URL.
   
Můžete přidat soubor s podporovaným dokument jako součást webech, kliknutím na **webech spravovat -> ' + přidat soubor '**

Můžete také importovat všechny existující webech kliknutím **"Ímport webech** tlačítko. 
   
Aktualizace webech závisí na **cenovou úroveň účtu správy** používá při vytváření přidružené k vaší knowledgbase služba QnA Maker. V případě potřeby můžete také aktualizovat úroveň správy z webu Azure portal.

4. Po dokončení změn ve znalostní bázi, klikněte na **uložit a jejich trénování** v pravém horním rohu stránky zachová tak změny.    

    ![Uložit a jejich trénování](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!NOTE]
    Opuštění stránky před kliknutím na tlačítko při uložení a trénování nebude zachová tak změny.

## <a name="add-a-qna-pair"></a>Přidat pár QnA

Vyberte **QnA přidat pár** přidáte nový řádek do tabulky znalostní báze knowledge base.

![Přidat dvojici QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Odstranit pár QnA

Pokud chcete odstranit QnA, klikněte na tlačítko **odstranit** ikonu vpravo na konci řádku QnA.

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
2. Je možné **hledání** znalostní báze tak, že zadáte do textového pole v horní části tabulce znalostní báze Knowledge Base. Chcete-li vyhledat obsah otázku a odpověď metadat, klikněte na enter. Klikněte na ikonu X odebrat vyhledávací filtr.
3. **Stránkování** umožňuje spravovat velké znalostních bází

    ![Hledání, stránkování, skupiny](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Spolupráce na znalostní báze](./collaborate-knowledge-base.md)
