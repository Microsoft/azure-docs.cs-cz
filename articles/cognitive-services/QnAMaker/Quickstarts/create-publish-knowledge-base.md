---
title: 'Rychlý Start: vytvoření, výuka a publikování znalostní báze Knowledge Base – QnA Maker'
description: Znalostní bázi služby QnA Maker můžete vytvořit s použitím vlastního obsahu, jako jsou nejčastější dotazy nebo příručky k produktům. Tento článek obsahuje příklad vytvoření QnA Maker znalostní báze z jednoduché webové stránky s nejčastějšími dotazy pro odpovědi na otázky QnA Maker.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 5759cd80278d20ee383fbe5922e43d250babe09f
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380670"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Rychlý Start: vytvoření, výuka a publikování znalostní báze QnA Maker Knowledge Base

Znalostní bázi služby QnA Maker můžete vytvořit s použitím vlastního obsahu, jako jsou nejčastější dotazy nebo příručky k produktům. Tento článek obsahuje příklad vytvoření QnA Maker znalostní báze z jednoduché webové stránky s nejčastějšími dotazy pro odpovědi na otázky QnA Maker.

## <a name="prerequisites"></a>Požadavky

> [!div class="checklist"]
> * Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/) před tím, než začnete.
> * QnA Maker [prostředek](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) vytvořený v Azure Portal. Mějte na paměti, že vaše Azure Active Directory ID, předplatné a název prostředku QnA jste zvolili při vytváření prostředku.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Vytvoření prvního QnA Maker znalostní báze Knowledge Base

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

1. Přihlaste se k portálu [QnAMaker.AI](https://QnAMaker.ai) pomocí svých přihlašovacích údajů Azure.

2. Na portálu QnA Maker vyberte **vytvořit znalostní bázi**.

3. Na stránce **vytvořit** přeskočte **Krok 1** , pokud již máte prostředek QnA maker.

    Pokud jste prostředek ještě nevytvořili, vyberte **vytvořit službu QnA**. Budete přesměrování na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), kde můžete nastavit službu QnA Maker ve vašem předplatném. Mějte na paměti, že vaše Azure Active Directory ID, předplatné a název prostředku QnA jste zvolili při vytváření prostředku.

    Až skončíte s vytvářením prostředku v Azure Portal, vraťte se na portál QnA Maker, aktualizujte stránku prohlížeče a pokračujte **krokem 2**.

4. V **kroku 2** vyberte službu Active Directory, předplatné, službu (prostředek) a jazyk pro všechny znalostní báze vytvořené ve službě.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/qnaservice-selection.png" alt-text="Snímek obrazovky s výběrem znalostní báze služby QnA Maker":::

5. V **kroku 3** pojmenujte svůj znalostní báze **Moje ukázková QnA KB**.

6. V **kroku 4** nakonfigurujte nastavení pomocí následující tabulky:

    |Nastavení|Hodnota|
    |--|--|
    |**Povolí vícenásobné extrakce z adres URL, souborů PDF a DOCX.**|Zaškrtnuto|
    |**Vícenásobné zapnutí výchozího textu**| Vybrat a možnost|
    |**+ Přidat adresu URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Povídání**|Vybrat **Professional**|

7. V **kroku 5** vyberte **vytvořit znalostní báze**.

    Pro extrakci dokumentu a identifikaci otázek a odpovědí trvá proces extrakce chvíli.

    Jakmile QnA Maker úspěšně vytvoří znalostní bázi, otevře se stránka **znalostní báze** . Na této stránce můžete upravit obsah znalostní báze.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

1. Přihlaste se k portálu [QnAMaker.AI](https://QnAMaker.ai) pomocí svých přihlašovacích údajů Azure.

2. Na portálu QnA Maker vyberte **vytvořit znalostní bázi**.

3. Na stránce **vytvořit** přeskočte **Krok 1** , pokud již máte prostředek QnA maker.

    Pokud jste prostředek ještě nevytvořili, vyberte **vytvořit službu QnA**. Budete přesměrování na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), kde můžete nastavit službu QnA Maker ve vašem předplatném. Mějte na paměti, že vaše Azure Active Directory ID, předplatné a název prostředku QnA jste zvolili při vytváření prostředku.

    Až skončíte s vytvářením prostředku v Azure Portal, vraťte se na portál QnA Maker, aktualizujte stránku prohlížeče a pokračujte **krokem 2**.

4. V **kroku 2** vyberte službu Active Directory, předplatné, službu (prostředek) a jazyk pro všechny znalostní báze vytvořené ve službě.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/connect-your-knowledge-base.png" alt-text="Snímek obrazovky s výběrem služby QnA Maker Knowledge Base Managed Preview":::

5. Pokud v **kroku 2** vytváříte první znalostní bázi pro vaši službu, budete mít možnost nastavit jazykové nastavení specifické pro každou znalostní bázi. Po definování nastavení jazyka pro první znalostní bázi se nebudete moct později změnit nastavení služby.

6. V **kroku 3** pojmenujte svůj znalostní báze **Moje ukázková QnA KB**. 

7. V **kroku 4** nakonfigurujte nastavení pomocí následující tabulky:

    |Nastavení|Hodnota|
    |--|--|
    |**Povolí vícenásobné extrakce z adres URL, souborů PDF a DOCX.**|Zaškrtnuto|
    |**Vícenásobné zapnutí výchozího textu**| Vybrat a možnost|
    |**+ Přidat adresu URL**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Povídání**|Vybrat **Professional**|

8. V **kroku 5** vyberte **vytvořit znalostní báze**.

    Pro extrakci dokumentu a identifikaci otázek a odpovědí trvá proces extrakce chvíli.

    Jakmile QnA Maker úspěšně vytvoří znalostní bázi, otevře se stránka **znalostní báze** . Na této stránce můžete upravit obsah znalostní báze.

---

## <a name="add-a-new-question-and-answer-set"></a>Přidat novou sadu otázek a odpovědí

1. Na portálu QnA Maker na stránce **Upravit** vyberte **+ Přidat dvojici QnA** z panelu nástrojů kontextu.
1. Přidejte následující otázku:

    `How many Azure services are used by a knowledge base?`

1. Přidejte odpověď formátovanou pomocí _Markdownu_ :

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png" alt-text="Přidejte otázku jako text a odpověď formátovanou pomocí Markdownu.":::

    Symbol Markdownu se `*` používá pro body odrážek. `\n`Používá se pro nový řádek.

    Na stránce pro **Úpravy** se zobrazí Markdownu. Když použijete **testovací** panel později, zobrazí se Markdownu správně.

## <a name="save-and-train"></a>Uložení a natrénování

V pravém horním rohu vyberte **Uložit a výuka** a uložte úpravy a výukové QnA maker. Úpravy se nezachovají, pokud se neuloží.

## <a name="test-the-knowledge-base"></a>Testování znalostní báze

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

1. Na portálu QnA Maker v pravém horním rohu vyberte **test** , abyste otestovali, že provedené změny se projevily.
2. Do textového pole zadejte ukázkový dotaz uživatele.

    `How many Azure services are used by a knowledge base?`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png" alt-text="Do textového pole zadejte ukázkový dotaz uživatele.":::

3. Pokud chcete odpověď prozkoumat podrobněji, vyberte **Inspect** (Prozkoumat). Testovací okno slouží k otestování změn ve znalostní bázi před publikováním znalostní báze.

4. Vyberte znovu **test** pro zavření **testovacího** panelu.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

1. Na portálu QnA Maker v pravém horním rohu vyberte **test** , abyste otestovali, že provedené změny se projevily.
2. Do textového pole zadejte ukázkový dotaz uživatele.

    `How many Azure services are used by a knowledge base?`

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/enable-short-answer.png" alt-text="Snímek obrazovky s dialogovým oknem s uživatelským rozhraním chatu otázka: kolik stránek bude mít nepatrný cestovní pas? a odpověď: jenom 36 stránek brožura Passport se vydávají nezletilým.":::

3. Pokud povolíte funkci MRC pro vaši znalostní bázi, výběrem možnosti **Povolit krátkou odpověď (experimentální)** se zobrazí také přesná odpověď, je-li k dispozici, spolu s průchodem odpovědí v podokně test. 

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/enable-short-answer-checked.png" alt-text="Snímek obrazovky s dialogovým oknem s uživatelským rozhraním chatu otázka: kolik stránek bude mít nepatrný cestovní pas? a odpověď: 36. na jeden řádek následovaný druhým řádkem, který uvádí: jenom 36 stránek brožura Passport se vydávají jen pro děti.":::

4. Pokud chcete odpověď prozkoumat podrobněji, vyberte zkontrolovat. Testovací okno slouží k otestování změn ve znalostní bázi před publikováním znalostní báze. 
5. Vyberte znovu **test** pro zavření **testovacího** panelu.
---

## <a name="publish-the-knowledge-base"></a>Publikování znalostní báze

Když publikujete znalostní bázi, obsah znalostní báze se přesune z `test` indexu do `prod` indexu ve službě Azure Search.

![Snímek obrazovky s přesunutím obsahu znalostní báze](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Na portálu QnA Maker vyberte **publikovat**. Pak publikování potvrďte tím, že na stránce vyberete **Publish** (Publikovat).

    Služba QnA Maker je teď úspěšně publikovaná. Koncový bod můžete použít v kódu aplikace nebo chatbota.

    ![Snímek obrazovky s úspěšným publikováním](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Vytvoření robota

Po publikování můžete vytvořit robota ze stránky **publikování** :

* Můžete rychle vytvořit několik robotyů, které ukazují na stejnou znalostní bázi pro různé oblasti nebo cenové tarify pro jednotlivé roboty.
* Pokud potřebujete jenom jednu robota pro znalostní bázi, použijte **zobrazení všechny roboty na odkaz Azure Portal** k zobrazení seznamu aktuálního roboty.

Když provedete změny ve znalostní bázi a znovu publikujete, nemusíte s robotem dělat další akce. Je už nakonfigurovaná pro práci se znalostní bázeí a funguje se všemi budoucími změnami ve znalostní bázi. Pokaždé, když publikujete znalostní bázi, jsou automaticky aktualizovány všechny roboty připojené k ní.

1. Na QnA Makerovém portálu na stránce **publikovat** vyberte **vytvořit robot**. Toto tlačítko se zobrazí až po publikování znalostní báze.

    ![Snímek obrazovky s vytvořením robota](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Otevře se nová karta prohlížeče pro Azure Portal se stránkou pro vytvoření Azure Bot Service. Nakonfigurujte službu Azure bot Service. Robot a QnA Maker můžou sdílet plán služby Web App Service, ale nemůžou webovou aplikaci sdílet. To znamená, že **název aplikace** pro robot se musí lišit od názvu aplikace pro službu QnA maker.

    * **Postup**
        * Změnit popisovač robota – Pokud není jedinečný
        * Vyberte jazyk sady SDK. Po vytvoření robota si můžete kód stáhnout do svého místního vývojového prostředí a pokračovat v procesu vývoje.
    * **Chybný postup**
        * Při vytváření robota změňte následující nastavení v Azure Portal. Jsou předem vyplněné pro stávající znalostní bázi:
           * QnA ověřovací klíč
           * Plán a umístění služby App Service


1. Po vytvoření robota otevřete prostředek **služby bot** .
1. V části **Správa robota** vyberte **test na webu chat**.
1. Na příkazovém řádku chatu **Zadejte zprávu** :

    `Azure services?`

    Robotka chatu odpoví odpověď z vaší znalostní báze.

    :::image type="content" source="../media/qnamaker-create-publish-knowledge-base/test-web-chat.png" alt-text="Zadejte uživatelský dotaz do testovacího webového chatu.":::

## <a name="what-did-you-accomplish"></a>Co jste provedli?

Vytvořili jste novou znalostní bázi, Přidali jste do znalostní báze veřejnou adresu URL, Přidali jste vlastní dvojici QnA, školením, otestováním a publikováním znalostní báze.

Po zveřejnění znalostní báze jste vytvořili robota a otestovali robota.

To bylo provedeno za několik minut, aniž byste museli psát kód nebo vyčistit obsah.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat dalším rychlým startem, odstraňte prostředky QnA Maker a robot Framework v Azure Portal.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přidání otázek s metadaty](add-question-metadata-portal.md)

Další informace najdete tady:

* [Formát Markdownu v odpovědích](../reference-markdown-format.md)
* QnA Maker [zdroje dat](../concepts/knowledge-base.md).

