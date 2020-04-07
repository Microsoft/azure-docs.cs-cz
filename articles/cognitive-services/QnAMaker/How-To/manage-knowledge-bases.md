---
title: Správa znalostních bází - QnA Maker
description: QnA Maker umožňuje spravovat znalostní báze tím, že poskytuje přístup k nastavení znalostní báze a obsahu.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 4eb07b30b10826c361f4f9b805b517e372fc5ea1
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756803"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Vytvoření znalostní báze a správa nastavení

QnA Maker umožňuje spravovat znalostní báze tím, že poskytuje přístup k nastavení znalostní báze a zdrojům dat.

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.
> * [Prostředek QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) vytvořený na webu Azure Portal. Zapamatujte si svůj Azure Active Directory ID, předplatné, QnA název prostředku, který jste vybrali při vytváření prostředku.

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

1. Přihlaste se k [QnAMaker.ai](https://QnAMaker.ai) portálu pomocí přihlašovacích údajů azure.

1. Na portálu QnA Maker vyberte **Vytvořit znalostní bázi**.

1. Na stránce **Vytvořit** přeskočte **krok 1,** pokud již máte prostředek QnA Maker.

    Pokud jste zdroj ještě nevytvořili, vyberte **Vytvořit službu QnA**. Budete přesměrování na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), kde můžete nastavit službu QnA Maker ve vašem předplatném. Zapamatujte si svůj Azure Active Directory ID, předplatné, QnA název prostředku, který jste vybrali při vytváření prostředku.

    Po dokončení vytváření prostředků na webu Azure Portal se vraťte na portál QnA Maker, aktualizujte stránku prohlížeče a pokračujte **krokem 2**.

1. V **kroku 3**vyberte službu Active Directory, předplatné, službu (prostředek) a jazyk pro všechny znalostní báze vytvořené ve službě.

   ![Snímek obrazovky s výběrem znalostní báze QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. V **kroku 3**pojmenujte znalostní báze `My Sample QnA KB`.

1. V **kroku 4**nakonfigurujte nastavení pomocí následující tabulky:

    |Nastavení|Hodnota|
    |--|--|
    |**Povolte vícenásobné extrakce z adres URL, souborů PDF nebo .docx.**|Zaškrtnuté|
    |**Výchozí text odpovědi**| `Quickstart - default answer not found.`|
    |**+ Přidat adresu URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chat**|Vybrat **profesionální**|

1. V **kroku 5**vyberte vytvořit **kb**.

    Proces extrakce trvá několik okamžiků, než si dokument přečtete a identifikujete otázky a odpovědi.

    Po úspěšném vytvoření znalostní báze QnA Maker se otevře stránka **Znalostní báze** Knowledge Base. Obsah znalostní báze můžete upravit na této stránce.

## <a name="edit-knowledge-base"></a>Úprava znalostní báze

1.  Na horním navigačním panelu vyberte **Moje znalostní báze.**

       Všechny služby, které jste vytvořili nebo s vámi sdíleli, můžete zobrazit seřazené v sestupném pořadí **posledního data změny.**

       ![Moje znalostní báze](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Vyberte konkrétní znalostní bázi, která ji provede úpravou.

1.  Vyberte **Nastavení**. Následující seznam obsahuje pole, která můžete změnit.

       |Cíl|Akce|
       |--|--|
       |Přidat adresu URL|Nové adresy URL můžete přidat do znalostní báze Knowledge Base kliknutím na **spravovat znalostní báze -> odkaz Přidat adresu URL.**|
       |Odstranit adresu URL|Existující adresy URL můžete odstranit výběrem ikony odstranění, koše.|
       |Aktualizovat obsah|Pokud chcete, aby znalostní báze procházela nejnovější obsah existujících adres URL, zaškrtněte políčko **Aktualizovat.** Tím se jednou aktualizuje znalostní báze s nejnovějším obsahem adresy URL. Toto není nastavení pravidelného plánu aktualizací.|
       |Přidat soubor|Výběrem možnosti **Spravovat znalostní báze**můžete přidat dokument podporovaný k podporovanému souboru, který bude součástí znalostní báze Knowledge Base , a pak vybrat **+ Přidat soubor.**|
    |Import|Můžete také importovat existující znalostní bázi výběrem tlačítka **Importovat znalostní báze.** |
    |Aktualizace|Aktualizace znalostní báze závisí na **cenové úrovni správy** používané při vytváření služby QnA Maker přidružené k vaší znalostní bázi. V případě potřeby můžete také aktualizovat úroveň správy z webu Azure Portal.

  1. Po dokončení provádění změn ve znalostní bázi vyberte **uložit a táct** v pravém horním rohu stránky, abyste změny zachovali.

       ![Uložit a vlak](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Pokud stránku opustíte před výběrem **možnosti Uložit a trénovat**, budou všechny změny ztraceny.



## <a name="manage-large-knowledge-bases"></a>Správa rozsáhlých znalostních bází

* **Skupiny zdrojů dat**: QnAs jsou seskupeny podle zdroje dat, ze kterého byly extrahovány. Zdroj dat můžete rozbalit nebo sbalit.

    ![Sbalte a rozbalte otázky a odpovědi na zdroje dat pomocí panelu zdroje dat QnA Maker.](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Hledání znalostní báze**: Znalostní bázi můžete prohledávat zadáním textového pole v horní části tabulky Znalostní báze. Kliknutím na Enter vyhledáte obsah otázky, odpovědi nebo metadat. Kliknutím na ikonu X odstraníte vyhledávací filtr.

    ![Pomocí vyhledávacího pole QnA Maker nad otázkami a odpověďmi zmenšete zobrazení pouze na položky odpovídající filtru.](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Stránkování**: Rychlý přechod mezi zdroji dat pro správu rozsáhlých znalostních bází

    ![Pomocí funkcí stránkování QnA Maker nad otázkami a odpověďmi můžete procházet stránkami otázek a odpovědí](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Odstranění znalostních bází

Odstranění znalostní báze (KB) je trvalá operace. Nedá se to vrátit. Před odstraněním znalostní báze byste měli znalostní bázi exportovat ze stránky **Nastavení** portálu QnA Maker.

Pokud sdílíte znalostní bázi se spolupracovníky,](spolupráce-znalosti base.md) pak ji odstranit, každý ztratí přístup k KB.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [správě jazyka](language-knowledge-base.md) všech znalostních bází v prostředku.

* Úpravy párů QnA
* Správa prostředků Azure používaných qnA makerem