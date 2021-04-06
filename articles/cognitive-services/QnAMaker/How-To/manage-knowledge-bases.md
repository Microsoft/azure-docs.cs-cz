---
title: Správa znalostní báze – QnA Maker
description: QnA Maker umožňuje spravovat vaše znalostní báze tím, že poskytuje přístup k nastavení a obsahu znalostní báze.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: c7bb79cbd67ba2fb151641ddeeb31b54e399b906
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96938252"
---
# <a name="create-knowledge-base-and-manage-settings"></a>Vytvoření znalostní báze a Správa nastavení

QnA Maker umožňuje spravovat vaše znalostní báze tím, že poskytuje přístup k nastavení znalostní báze a zdrojům dat.

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/cognitive-services/).
> * [QnA maker prostředek](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) vytvořený v Azure Portal. Mějte na paměti, že vaše Azure Active Directory ID, předplatné a název prostředku QnA jste zvolili při vytváření prostředku.

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze

1. Přihlaste se k portálu [QnAMaker.AI](https://QnAMaker.ai) pomocí svých přihlašovacích údajů Azure.

1. Na portálu QnA Maker vyberte **vytvořit znalostní bázi**.

1. Na stránce **vytvořit** přeskočte **Krok 1** , pokud již máte prostředek QnA maker.

    Pokud jste prostředek ještě nevytvořili, vyberte **vytvořit službu QnA**. Budete přesměrování na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), kde můžete nastavit službu QnA Maker ve vašem předplatném. Mějte na paměti, že vaše Azure Active Directory ID, předplatné a název prostředku QnA jste zvolili při vytváření prostředku.

    Až skončíte s vytvářením prostředku v Azure Portal, vraťte se na portál QnA Maker, aktualizujte stránku prohlížeče a pokračujte **krokem 2**.

1. V **kroku 3** vyberte službu Active Directory, předplatné, službu (prostředek) a jazyk pro všechny znalostní báze vytvořené ve službě.

   ![Snímek obrazovky s výběrem znalostní báze služby QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. V **kroku 3** pojmenujte znalostní bázi `My Sample QnA KB` .

1. V **kroku 4** nakonfigurujte nastavení pomocí následující tabulky:

    |Nastavení|Hodnota|
    |--|--|
    |**Povolí vícenásobné extrakce z adres URL, souborů PDF a DOCX.**|Zaškrtnuto|
    |**Výchozí text odpovědi**| `Quickstart - default answer not found.`|
    |**+ Přidat adresu URL**|`https://azure.microsoft.com/en-us/support/faq/`|
    |**Povídání**|Vybrat **Professional**|

1. V **kroku 5** vyberte **vytvořit znalostní báze**.

    Pro extrakci dokumentu a identifikaci otázek a odpovědí trvá proces extrakce chvíli.

    Jakmile QnA Maker úspěšně vytvoří znalostní bázi, otevře se stránka **znalostní báze** . Na této stránce můžete upravit obsah znalostní báze.

## <a name="edit-knowledge-base"></a>Úprava znalostní báze

1.  V horním navigačním panelu vyberte **Moje znalostní** báze.

       Všechny služby, které jste vytvořili nebo sdíleli s vámi, můžete zobrazit v sestupném pořadí podle data **Poslední úpravy** .

       ![Moje znalostní báze](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Vyberte konkrétní znalostní bázi, abyste mohli provádět úpravy.

1.  Vyberte **Nastavení**. Následující seznam obsahuje pole, která můžete změnit.

       |Cíl|Akce|
       |--|--|
       |Přidat adresu URL|Kliknutím na **Spravovat znalostní bázi > + přidat adresu URL** můžete přidat nové adresy URL a přidat tak nový obsah nejčastějších dotazů do znalostní báze.|
       |Odstranit adresu URL|Existující adresy URL můžete odstranit tak, že vyberete ikonu Odstranit, odpadkový koš.|
       |Aktualizovat obsah|Pokud chcete, aby znalostní báze procházela nejnovější obsah existujících adres URL, zaškrtněte políčko **aktualizovat** . Tím se aktualizuje znalostní báze s nejnovějším obsahem adresy URL. Tato možnost nenastavuje pravidelný plán aktualizací.|
       |Přidat soubor|Podporovaný dokument, který je součástí znalostní báze, můžete přidat tak, že vyberete **Spravovat znalostní bázi** a pak vyberete **+ Přidat soubor** .|
    |Import|Stávající znalostní bázi můžete také importovat výběrem tlačítka **importovat znalostní bázi** . |
    |Aktualizace|Aktualizace znalostní báze závisí na **cenové úrovni správy** používané při vytváření QnA maker služby přidružené k vaší znalostní bázi. V případě potřeby můžete také aktualizovat úroveň správy z Azure Portal.

  1. Až provedete změny ve znalostní bázi, vyberte **Uložit a vytvořit vlak** v pravém horním rohu stránky, aby se změny zachovaly.

       ![Uložení a výuka](../media/qnamaker-how-to-edit-kb/save-and-train.png)

       >[!CAUTION]
       >Pokud ponecháte stránku před výběrem možnosti **Uložit a výuka**, ztratí se všechny změny.



## <a name="manage-large-knowledge-bases"></a>Správa rozsáhlých základů znalostní báze

* **Skupiny zdrojů dat**: QnAs jsou seskupeny podle zdroje dat, ze kterého byly extrahovány. Zdroj dat můžete rozbalit nebo sbalit.

    ![Pro sbalení a rozbalení otázek a odpovědí zdrojů dat použijte panel zdroje dat QnA Maker.](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Hledání ve znalostní bázi**: znalostní báze můžete vyhledat zadáním do textového pole v horní části tabulky znalostní báze. Kliknutím na tlačítko ENTER můžete vyhledat dotaz, odpověď nebo obsah metadat. Kliknutím na ikonu X odeberte vyhledávací filtr.

    ![Pomocí vyhledávacího pole QnA Maker nad dotazy a odpovědi zmenšíte zobrazení jenom na položky, které odpovídají filtrům.](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Stránkování**: rychlé přesunutí ze zdrojů dat za účelem správy rozsáhlých základů znalostní báze

    ![Procházení stránek otázek a odpovědí pomocí funkcí QnA Maker stránkování nad rámec otázek a odpovědí](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Odstranění znalostní báze

Odstranění znalostní báze (KB) je trvalá operace. Nedá se vrátit zpátky. Před odstraněním znalostní báze byste měli znalostní bázi exportovat ze stránky **Nastavení** na portálu QnA maker.

Pokud vaše znalostní báze sdílíte se spolupracovníky,] (spolupráce – znalostní báze base.md), pak se odstraní a všichni ztratí přístup k KB.

## <a name="next-steps"></a>Další kroky

Seznamte [se se správou jazyka](../index.yml) všech databází znalostí v prostředku.

* Upravit páry QnA
* Správa prostředků Azure používaných QnA Maker