---
title: Úprava znalostní báze – QnA Maker
description: QnA Maker vám umožní spravovat obsah znalostní báze tím, že nabízí snadno použitelné prostředí pro úpravy.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 8d9f54c1fcd142c77fb73c294b81bf0f11ee083f
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99557694"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Úprava párů QnA ve znalostní bázi

QnA Maker vám umožní spravovat obsah znalostní báze tím, že nabízí snadno použitelné prostředí pro úpravy.

Páry QnA jsou přidány ze zdroje dat, jako je například soubor nebo adresa URL, nebo přidány jako redakční zdroj. Redakční zdroj indikuje, že dvojice QnA byla přidána na portál QnA ručně. Všechny páry QnA jsou k dispozici pro úpravy.

<a name="add-an-editorial-qna-set"></a>

## <a name="question-and-answer-pairs"></a>Dvojice otázek a odpovědí

Znalostní báze se skládá z párů otázek a odpovědí (QnA).  Každý pár má jednu odpověď a pár obsahuje všechny informace spojené s touto _odpovědí_. Odpověď může volně napodobovat řádek databáze nebo instanci datové struktury. **Požadovaná** nastavení dvojice otázek a odpovědí (QnA) jsou:

* **dotazový** text dotazu, který se používá k QnA maker strojového učení, pro zarovnání textu otázky uživatele s různou formulací, ale stejná odpověď
* **odpověď** – odpověď páru je odpověď, která se vrátí, když se dotaz uživatele shoduje s přidruženou otázkou.

Každý pár je reprezentován **identifikátorem**.

Mezi **volitelná** nastavení pro pár patří:

* **Alternativní formy otázky** – to pomáhá QnA maker vrátit správnou odpověď na širší řadu otázek.
* **Metadata**: metadata jsou značky přidružené k páru QnA a jsou reprezentovány jako páry klíč-hodnota. Značky metadat slouží k filtrování párů QnA a omezení sady, u které se provádí párování dotazů.
* **Víceřádkové výzvy**, které se používají k pokračování vícenásobného zapínání konverzace

![QnA Maker základy znalostí](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="add-an-editorial-qna-pair"></a>Přidat redakční pár QnA

Pokud nemáte již existující obsah k naplnění znalostní báze, můžete přidat páry QnA v redakčním portálu na portálu QnA Maker.

1. Přihlaste se k [portálu QnA](https://www.qnamaker.ai/)a pak vyberte znalostní bázi, do které chcete přidat dvojici QnA.
1. Na stránce pro **Úpravy** znalostní báze vyberte **Přidat dvojici QnA** a přidejte nový pár QnA.

    > [!div class="mx-imgBorder"]
    > ![Přidat pár QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. Do nového řádku dvojice QnA přidejte pole požadovaná otázka a odpověď. Ostatní pole jsou volitelná. Všechna pole lze kdykoli změnit.

1. Volitelně můžete přidat **[alternativní formulování](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)**. Alternativní formulace je libovolná forma otázky, která se výrazně liší od původní otázky, ale měla by poskytnout stejnou odpověď.

    Když je vaše znalostní báze publikována a máte zapnuté [aktivní učení](use-active-learning.md) , QnA maker shromažďuje alternativní formulované možnosti, které můžete přijmout. Tyto možnosti jsou vybrány, aby bylo možné zvýšit přesnost předpovědi.

1. Volitelně můžete přidat **[metadata](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)**. Chcete-li zobrazit metadata, v místní nabídce vyberte možnost **Možnosti zobrazení** . Metadata poskytují filtry na odpovědi, které poskytuje klientská aplikace, jako je například robot v chatu.

1. Volitelně můžete přidat **[výzvy pro následné](multiturn-conversation.md)** zpracování. Následné výzvy poskytují další cesty pro konverzaci k klientské aplikaci, která má být prezentována uživateli.

1. Pokud chcete zobrazit předpovědi včetně nového páru QnA, vyberte **Uložit a výuka** .

## <a name="rich-text-editing-for-answer"></a>Pro odpověď se upraví formátovaného textu.

Textové úpravy textu odpovědi vám umožní Markdownu styl z jednoduchého panelu nástrojů.

1. Vyberte textovou oblast pro odpověď. na řádku dvojice QnA se zobrazí panel nástrojů s bohatým textem.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s editorem formátovaného textu s otázkou a odpovědí řádku dvojice QnA](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    Libovolný text, který se už v odpovědi zobrazuje, se zobrazí správně, jak ho uživatel uvidí od robota.

1. Upravte text. Na panelu nástrojů pro úpravy formátovaného textu vyberte funkce formátování nebo pomocí přepínací funkce přepněte na Markdownu syntax.

    > [!div class="mx-imgBorder"]
    > ![Pomocí Rich Text Editor pište a formátujte text a uložte ho jako Markdownu.](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |Funkce editoru formátovaného textu|Klávesová zkratka|
    |--|--|
    |Přepínání mezi bohatým editorem textu a Markdownu. `</>`|CTRL+M|
    |Psaného. **B**|z centra a za kg|
    |Kurzíva, označená kurzívou ****|CTRL + I|
    |Neseřazený seznam||
    |Seřazený seznam||
    |Styl odstavce||
    |Image – přidejte obrázek, který je k dispozici z veřejné adresy URL.|CTRL + G|
    |Přidá odkaz na veřejně dostupnou adresu URL.|CTRL + K|
    |Emotikony – přidat z výběru emotikon.|CTRL + E|
    |Nabídka Upřesnit – zpět|CTRL + Z|
    |Nabídka Upřesnit – znovu|CTRL + Y|

1. Přidejte obrázek k odpovědi pomocí ikony obrázku na panelu nástrojů s bohatým textem. Místní Editor potřebuje adresu URL veřejně přístupného obrázku a alternativní text pro obrázek.


    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky zobrazuje Editor v místě s veřejně přístupným URL obrázkem a alternativním textem pro zadanou bitovou kopii.](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. Přidejte odkaz na adresu URL, a to tak, že vyberete text v odpovědi, pak na panelu nástrojů vyberete ikonu odkazu nebo na panelu nástrojů vyberete ikonu odkazu a pak zadáte nový text a adresu URL.

    > [!div class="mx-imgBorder"]
    > ![Použijte Editor formátovaného textu a přidejte veřejně přístupný obrázek a jeho alternativní text.](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>Úprava páru QnA

Jakékoli pole v jakémkoli páru QnA lze upravovat bez ohledu na původní zdroj dat. Některá pole se nemusí zobrazit v důsledku aktuálního nastavení **možností zobrazení** , která se nachází na panelu nástrojů kontextu.

## <a name="delete-a-qna-pair"></a>Odstranit pár QnA

Pokud chcete odstranit QnA, klikněte na ikonu **Odstranit** na pravé straně řádku QnA. Toto je trvalá operace. Nedá se vrátit zpátky. Než budete páry odstranit, zvažte možnost exportovat si KB ze stránky **publikování** .

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

Přidejte páry metadat tak, že nejprve vyberete **Možnosti zobrazení** a pak vyberete **zobrazit metadata**. Tím se zobrazí sloupec metadata. Pak vyberte **+** znaménko a přidejte dvojici metadat. Tento pár se skládá z jednoho klíče a jedné hodnoty.

Další informace o metadatech v rychlém startu portálu QnA Maker pro metadata:
* [Vytváření – přidání metadat do páru QnA](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Prediktivní dotazování – filtrování odpovědí podle metadat](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>Uložení změn do párů QnA

Po provedení úprav pravidelně vybírejte možnost **Uložit a výuka** , aby nedošlo ke ztrátě změn.

![Přidat metadata](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Kdy použít úpravy formátovaného textu oproti Markdownu

[S bohatou úpravou](#add-an-editorial-qna-set) odpovědí můžete jako autor použít panel formátování k rychlému výběru a formátování textu.

[Markdownu](../reference-markdown-format.md) je lepší nástroj, pokud potřebujete automaticky vygenerovat obsah a vytvořit znalostní bázi, který se má importovat jako součást kanálu CI/CD nebo pro [dávkové testování](../index.yml).

## <a name="editing-your-knowledge-base-locally"></a>Místní úprava znalostní báze

Po vytvoření znalostní báze se doporučuje provádět úpravy textu znalostní báze na [portálu QnA maker](https://qnamaker.ai)a nemusíte je exportovat a znovu importovat pomocí místních souborů. Nicméně může nastat situace, kdy budete muset znalostní bázi upravit místně.

Vyexportujte znalostní bázi ze stránky **Nastavení** a pak upravte znalostní bázi v aplikaci Microsoft Excel. Pokud se rozhodnete pro úpravu exportovaného souboru použít jinou aplikaci, může aplikace způsobit chyby syntaxe, protože není plně kompatibilní s požadavky na TSV. Soubory TSV v aplikaci Microsoft Excel obecně nezavádějí žádné chyby formátování.

Až budete s úpravami hotovi, znovu naimportujte soubor TSV ze stránky **Nastavení** . Tato akce zcela nahradí aktuální znalostní bázi importovanou znalostní bázi.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Spolupráce na znalostní bázi](../index.yml)

* [Správa prostředků Azure používaných QnA Maker](set-up-qnamaker-service-azure.md)
