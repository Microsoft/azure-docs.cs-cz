---
title: 'Rychlý start: Vytváření, trénování a publikování znalostní báze – QnA Maker'
description: Znalostní bázi služby QnA Maker můžete vytvořit s použitím vlastního obsahu, jako jsou nejčastější dotazy nebo příručky k produktům. Tento článek obsahuje příklad vytvoření znalostní báze QnA Maker z jednoduché webové stránky faq, chcete-li odpovědět na otázky QnA Maker.
ms.topic: conceptual
ms.date: 02/08/2020
ms.openlocfilehash: 5a3cd4a66ae3a7148b517050c3d1a8c6c8e7a712
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756825"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Rychlý start: Vytvoření, trénování a publikování znalostní báze QnA Maker

Znalostní bázi služby QnA Maker můžete vytvořit s použitím vlastního obsahu, jako jsou nejčastější dotazy nebo příručky k produktům. Tento článek obsahuje příklad vytvoření znalostní báze QnA Maker z jednoduché webové stránky faq, chcete-li odpovědět na otázky QnA Maker.

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.
> * [Prostředek](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) QnA Maker vytvořený na webu Azure Portal. Zapamatujte si svůj Azure Active Directory ID, předplatné, QnA název prostředku, který jste vybrali při vytváření prostředku.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Vytvořte si svou první znalostní bázi QnA Maker

1. Přihlaste se k [QnAMaker.ai](https://QnAMaker.ai) portálu pomocí přihlašovacích údajů azure.

1. Na portálu QnA Maker vyberte **Vytvořit znalostní bázi**.

1. Na stránce **Vytvořit** přeskočte **krok 1,** pokud již máte prostředek QnA Maker.

    Pokud jste zdroj ještě nevytvořili, vyberte **Vytvořit službu QnA**. Budete přesměrování na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), kde můžete nastavit službu QnA Maker ve vašem předplatném. Zapamatujte si svůj Azure Active Directory ID, předplatné, QnA název prostředku, který jste vybrali při vytváření prostředku.

    Po dokončení vytváření prostředků na webu Azure Portal se vraťte na portál QnA Maker, aktualizujte stránku prohlížeče a pokračujte **krokem 2**.

1. V **kroku 3**vyberte službu Active Directory, předplatné, službu (prostředek) a jazyk pro všechny znalostní báze vytvořené ve službě.

   ![Snímek obrazovky s výběrem znalostní báze QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. V **kroku 3**pojmenujte znalostní **báze Můj vzorek QnA KB**.

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

## <a name="add-a-new-question-and-answer-set"></a>Přidání nové sady otázek a odpovědí

1. Na portálu QnA Maker vyberte na stránce **Úpravy** **+ Přidat dvojici QnA** z panelu nástrojů kontextu.
1. Přidejte následující otázku:

    `How many Azure services are used by a knowledge base?`

1. Přidejte odpověď formátovanou _markdownem_:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Přidejte otázku jako text a odpověď formátovanou markdownem.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    Symbol značky `*`, se používá pro odrážky. Používá `\n` se pro nový řádek.

    Stránka **Úpravy** zobrazuje značku dolů. Když později použijete panel **Test,** zobrazí se správně značky.

## <a name="save-and-train"></a>Uložení a natrénování

V pravém horním horním mačkání vyberte **Uložit a trénovat,** chcete-li uložit úpravy a trénovat QnA Maker . Úpravy se nezachovají, pokud se neuloží.

## <a name="test-the-knowledge-base"></a>Testování znalostní báze

1. Na portálu QnA Maker v pravém horním sekat, **chcete-li otestovat,** zda se provedené změny projevily.
1. Do textového pole zadejte ukázkový uživatelský dotaz.

    `How many Azure services are used by a knowledge base?`

    ![ Do textového pole zadejte ukázkový uživatelský dotaz. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Pokud chcete odpověď prozkoumat podrobněji, vyberte **Inspect** (Prozkoumat). Testovací okno se používá k testování změn znalostní báze před publikováním znalostní báze.

1. Vyberte **Testovat** znovu, chcete-li panel **Test** zavřít.

## <a name="publish-the-knowledge-base"></a>Publikování znalostní báze

Když publikujete znalostní bázi, obsah znalostní `test` báze se přesune z indexu do `prod` indexu ve vyhledávání Azure.

![Snímek obrazovky s přesunutím obsahu znalostní báze](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Na portálu QnA Maker vyberte **Publikovat**. Pak publikování potvrďte tím, že na stránce vyberete **Publish** (Publikovat).

    Služba QnA Maker je teď úspěšně publikovaná. Koncový bod můžete použít v kódu aplikace nebo chatbota.

    ![Snímek obrazovky s úspěšným publikováním](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Vytvoření robota

Po publikování můžete vytvořit robota ze stránky **Publikovat:**

* Můžete rychle vytvořit několik robotů, všichni odkazují na stejnou znalostní bázi pro různé oblasti nebo cenové plány pro jednotlivé roboty.
* Pokud chcete pro znalostní bázi jenom jednoho robota, použijte odkaz **Zobrazit všechny roboty na portálu Azure** a zobrazit seznam vašich aktuálních robotů.

Když provedete změny znalostní báze a znovu publikujete, nemusíte s robotem provádět další akce. Je již nakonfigurován pro práci se znalostní báze a pracuje se všemi budoucími změnami znalostní báze. Pokaždé, když publikujete znalostní bázi, všechny roboty, kteří jsou k ní připojeni, se automaticky aktualizují.

1. Na portálu QnA Maker na stránce **Publikovat** vyberte **Vytvořit robota**. Toto tlačítko se zobrazí až po publikování znalostní báze.

    ![Snímek obrazovky s vytvořením robota](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Otevře se nová karta prohlížeče pro portál Azure se stránkou pro vytvoření služby Azure Bot Service. Nakonfigurujte službu Azure bot. Bot a QnA Maker můžou sdílet plán služby webové aplikace, ale nemůžou sdílet webovou aplikaci. To znamená, že **název aplikace** pro robota se musí lišit od názvu aplikace pro službu QnA Maker.

    * **Do**
        * Změna bot rukojeť - pokud to není unikátní.
        * Vyberte jazyk sady SDK. Po vytvoření robota můžete stáhnout kód do místního vývojového prostředí a pokračovat v procesu vývoje.
    * **Chybný postup**
        * změňte následující nastavení na webu Azure Portal při vytváření robota. Jsou předem vyplněny pro vaši stávající znalostní bázi:
           * Klíč QnA Auth
           * Plán a umístění služby App service


1. Po vytvoření robota otevřete prostředek **služby Bot.**
1. V části **Správa botů**vyberte **Testovat ve webovém chatu**.
1. Na výzvu k chatu v části **Zadejte zprávu**, zadejte:

    `Azure services?`

    Chatbot odpoví odpovědí z vaší znalostní báze.

    ![Zadejte uživatelský dotaz do testovacího webového chatu.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="what-did-you-accomplish"></a>Čeho jsi dosáhl?

Vytvořili jste novou znalostní bázi, přidali veřejnou adresu URL do znalostní báze, přidali vlastní dvojici QnA, vyškolili, otestovali a publikovali znalostní bázi.

Po publikování znalostní báze jste vytvořili robota a otestovali jste robota.

To vše bylo provedeno během několika minut, aniž by bylo třeba psát jakýkoli kód nebo vyčistit obsah.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nepokračujete na další rychlý start, odstraňte prostředky architektury QnA Maker a Bot na webu Azure Portal.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání otázek s metadaty](add-question-metadata-portal.md)

Další informace najdete tady:

* [Markdown formát v odpovědích](../reference-markdown-format.md)
* [Zdroje dat](../concepts/knowledge-base.md)QnA Maker .


