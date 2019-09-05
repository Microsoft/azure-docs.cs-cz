---
title: Úprava znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker umožňuje spravovat obsah znalostní báze tím, že poskytuje editační rozhraní snadným ovládáním.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 1450d6b41d9a193f8069509aacd3343ae646c6ac
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307024"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Úprava znalostní báze v nástroje QnA Maker

Nástroj QnA Maker umožňuje spravovat obsah znalostní báze tím, že poskytuje editační rozhraní snadným ovládáním.

<a name="add-datasource"></a>

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

Na stránce **Upravit** vyberte **Přidat dvojici QnA** a přidejte nový řádek do tabulky znalostní báze.

![Přidat dvojici QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Odstranit pár QnA

Pokud chcete odstranit QnA, klikněte na tlačítko **odstranit** ikonu vpravo na konci řádku QnA. Toto je nevratná operace. Nedá se vrátit zpět. Vezměte v úvahu export znalostní BÁZÍ z **publikovat** stránky před odstraněním dvojice. 

![Odstranit QnA pár](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Přidat alternativní otázky

Přidáte alternativní dotazy na existující dvojice QnA ke zlepšení pravděpodobnost, že shoda pro uživatelský dotaz.

![Přidat alternativní otázky](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Přidání metadat

Přidejte páry metadat tak, že nejprve vyberete **Možnosti zobrazení**a pak vyberete **zobrazit metadata**. Tím se zobrazí sloupec metadata. Pak vyberte **+** znaménko a přidejte dvojici metadat. Tento pár se skládá z jednoho klíče a jedné hodnoty.

![Přidání metadat](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Ujistěte se, že pravidelně ukládat a trénování znalostní báze po provedení úprav, abyste se vyhnuli ztrátě změny.

## <a name="manage-large-knowledge-bases"></a>Správa velkých znalostních bází

* **Skupiny zdrojů dat**: QnAs jsou seskupeny podle zdroje dat, ze kterého byly extrahovány. Můžete rozbalit nebo sbalit zdroj dat.

    ![Pro sbalení a rozbalení otázek a odpovědí zdrojů dat použijte panel zdroje dat QnA Maker.](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Vyhledat znalostní bázi Knowledge Base**: Znalostní bázi můžete vyhledat zadáním do textového pole v horní části tabulky znalostní báze. Chcete-li vyhledat obsah dotaz, odpověď nebo metadata, klikněte na enter. Klikněte na ikonu X odebrat vyhledávací filtr.

    ![Pomocí vyhledávacího pole QnA Maker nad dotazy a odpovědi zmenšíte zobrazení jenom na položky, které odpovídají filtrům.](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Stránkování**: Rychlé procházení zdrojů dat pro správu rozsáhlých znalostní báze

    ![Procházení stránek otázek a odpovědí pomocí funkcí QnA Maker stránkování nad rámec otázek a odpovědí](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Odstranit znalostních bází

Odstraňuje se znalostní bázi (KB) je nevratná operace. Nedá se vrátit zpět. Před odstraněním znalostní báze, je třeba exportovat ve znalostní bázi z **nastavení** stránky portál QnA Maker. 

Pokud sdílíte znalostní BÁZÍ s [spolupracovníci](collaborate-knowledge-base.md) odstraňte ji, všichni ztratí přístup k KB. 

## <a name="delete-azure-resources"></a>Odstranění prostředků Azure 

Pokud odstraníte všechny prostředky Azure používané pro vaše nástroje QnA Maker znalostních bází, znalostních bází nebude fungovat. Před odstraněním všechny prostředky, ujistěte se, že exportujete vaše znalostních bází z **nastavení** stránky. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Spolupráce na znalostní báze](./collaborate-knowledge-base.md)
