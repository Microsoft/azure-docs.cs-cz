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
ms.date: 11/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: cc4ead968a0ee2c9890c1cd24a6b70516b2b2e74
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326802"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Úprava znalostní báze v nástroje QnA Maker

Nástroj QnA Maker umožňuje spravovat obsah znalostní báze tím, že poskytuje editační rozhraní snadným ovládáním.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>Upravit obsah znalostní báze

1.  V horním navigačním panelu vyberte **Moje znalostní** báze. 

    Všechny služby, které jste vytvořili nebo sdíleli s vámi, můžete zobrazit v sestupném pořadí podle data **Poslední úpravy** .

    ![Moje znalostních bází](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Vyberte konkrétní znalostní bázi, provádět úpravy.
 
1. Vyberte **Nastavení**. Tady můžete upravit povinného pole název služby.
  
    |Cíl|Akce|
    |--|--|
    |Přidat adresu URL|Kliknutím na **Spravovat znalostní bázi > + přidat adresu URL** můžete přidat nové adresy URL a přidat tak nový obsah nejčastějších dotazů do znalostní báze.|
    |Odstranit adresu URL|Existující adresy URL můžete odstranit tak, že vyberete ikonu odstranit, můžete do koše.|
    |Aktualizovat obsah|Pokud chcete, aby znalostní báze procházela nejnovější obsah existujících adres URL, zaškrtněte políčko **aktualizovat** . Tím se aktualizuje znalostní báze s nejnovějším obsahem adresy URL. Tato možnost nenastavuje pravidelný plán aktualizací.|
    |Přidat soubor|Podporovaný dokument, který je součástí znalostní báze, můžete přidat tak, že vyberete **Spravovat znalostní bázi**a pak vyberete **+ Přidat soubor** .|
    |Import|Stávající znalostní bázi můžete také importovat tak, že vyberete tlačítko **znalostní báze Ímport Knowledge Base** . |
    |Aktualizace|Aktualizace znalostní báze závisí na **cenové úrovni správy** používané při vytváření QnA maker služby přidružené k vaší znalostní bázi. V případě potřeby můžete také aktualizovat úroveň správy z webu Azure portal.

1. Až provedete změny ve znalostní bázi, vyberte **Uložit a vytvořit vlak** v pravém horním rohu stránky, aby se změny zachovaly.    

    ![Uložit a jejich trénování](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Pokud ponecháte stránku před výběrem možnosti **Uložit a výuka**, ztratí se všechny změny.

## <a name="add-a-qna-pair"></a>Přidání páru otázky a odpovědi

Na stránce **Upravit** vyberte **Přidat dvojici QnA** a přidejte nový řádek do tabulky znalostní báze.

![Přidat dvojici QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Odstranit pár QnA

Pokud chcete odstranit QnA, klikněte na ikonu **Odstranit** na pravé straně řádku QnA. Toto je nevratná operace. Nedá se vrátit zpět. Než budete páry odstranit, zvažte možnost exportovat si KB ze stránky **publikování** . 

![Odstranit QnA pár](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Přidat alternativní otázky

Přidáte alternativní dotazy na existující dvojice QnA ke zlepšení pravděpodobnost, že shoda pro uživatelský dotaz.

![Přidat alternativní otázky](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Přidání metadat

Přidejte páry metadat tak, že nejprve vyberete **Možnosti zobrazení**a pak vyberete **zobrazit metadata**. Tím se zobrazí sloupec metadata. Pak vyberte znaménko **+** a přidejte dvojici metadat. Tento pár se skládá z jednoho klíče a jedné hodnoty.

![Přidání metadat](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Ujistěte se, že pravidelně ukládat a trénování znalostní báze po provedení úprav, abyste se vyhnuli ztrátě změny.

## <a name="manage-large-knowledge-bases"></a>Správa velkých znalostních bází

* **Skupiny zdrojů dat**: QnAs jsou seskupeny podle zdroje dat, ze kterého byly extrahovány. Můžete rozbalit nebo sbalit zdroj dat.

    ![Pro sbalení a rozbalení otázek a odpovědí zdrojů dat použijte panel zdroje dat QnA Maker.](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Hledání ve znalostní bázi**: znalostní báze můžete vyhledat zadáním do textového pole v horní části tabulky znalostní báze. Chcete-li vyhledat obsah dotaz, odpověď nebo metadata, klikněte na enter. Klikněte na ikonu X odebrat vyhledávací filtr.

    ![Pomocí vyhledávacího pole QnA Maker nad dotazy a odpovědi zmenšíte zobrazení jenom na položky, které odpovídají filtrům.](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Stránkování**: rychlé přesunutí ze zdrojů dat za účelem správy rozsáhlých základů znalostní báze

    ![Procházení stránek otázek a odpovědí pomocí funkcí QnA Maker stránkování nad rámec otázek a odpovědí](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Odstranit znalostních bází

Odstraňuje se znalostní bázi (KB) je nevratná operace. Nedá se vrátit zpět. Před odstraněním znalostní báze byste měli znalostní bázi exportovat ze stránky **Nastavení** na portálu QnA maker. 

Pokud sdílíte své prostředí s [spolupracovníky](collaborate-knowledge-base.md) a potom je odstraníte, všichni ztratí přístup k KB. 

## <a name="delete-azure-resources"></a>Odstranění prostředků Azure 

Pokud odstraníte všechny prostředky Azure používané pro vaše nástroje QnA Maker znalostních bází, znalostních bází nebude fungovat. Před odstraněním jakýchkoli prostředků se ujistěte, že vaše znalostní báze vyexportujete ze stránky **Nastavení** . 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spolupráce na znalostní bázi](./collaborate-knowledge-base.md)
