---
title: Konverzace s vícenásobným zapnete – QnA Maker
description: Pomocí výzev a kontextu můžete spravovat vícenásobná vypnutí, označovaná jako vícenásobný křížek, pro robota z jedné otázky do druhé. Vícenásobné zapínání je schopnost mít v případě potřeby konverzaci, kde kontext předchozí otázky ovlivňuje další otázku a odpověď.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 313d1a390c30e7e5612b8d9bab7783b6698c35fd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102618487"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Použití následných dotazů k vytvoření konverzace s několika směry

Pomocí následných výzev a kontextu můžete spravovat vícenásobná zapínání _, která se označují jako u_ robotů z jedné otázky do druhé.

Pokud chcete zjistit, jak funguje vícenásobně, podívejte se na následující ukázkové video:

[![Vícenásobná konverzace v QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Co je vícenásobná konverzace?

Na některé otázky se nedají odpovědět jediným zahnutím. Při navrhování konverzací klientské aplikace (chat bot) může uživatel položit otázku, kterou je třeba filtrovat nebo upravit, aby bylo možné určit správnou odpověď. Provedete to tak, že si projdete možné otázky tím, že uživateli *zobrazíte následné výzvy*.

Když si uživatel vyžádá otázku, QnA Maker vrátí odpověď _a_ všechny následné výzvy. Tato odpověď vám umožní prezentovat reakce na dotazy jako možnosti.

> [!CAUTION]
> V dokumentech s nejčastějšími dotazy se neextrahují výzvy s vícenásobným zapnutím. Pokud potřebujete vícenásobné extrakce, odeberte otazníky, které určují páry QnA jako nejčastější dotazy.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Příklad víceřádkové konverzace s robotem chatu

Pomocí vícenásobného zapínání robota v konverzaci spravuje konverzaci s uživatelem, aby určila konečnou odpověď, jak je znázorněno na následujícím obrázku:

![Dialogové okno s výzvou k vícenásobnému zapnutí s výzvami, která provedou uživatele prostřednictvím konverzace](../media/conversational-context/conversation-in-bot.png)

Na předchozím obrázku uživatel zahájil konverzaci zadáním **mého účtu**. Znalostní báze obsahuje tři propojené páry otázek a odpovědí. K upřesnění odpovědi uživatel vybere jednu ze tří možností ve znalostní bázi. Otázka (#1) obsahuje tři výzvy pro následné zpracování, které jsou prezentovány ve robotovi chatu jako tři možnosti (#2).

Když uživatel vybere možnost (#3), zobrazí se další seznam možností upřesnění (#4). Tato sekvence pokračuje (#5), dokud uživatel neurčí správnou odpověď (#6).

### <a name="use-multi-turn-in-a-bot"></a>Použití vícenásobného zapínání v robotu

Po publikování KB můžete vybrat tlačítko **vytvořit robota** a nasadit QnA maker bot do Azure bot Service. Výzvy se zobrazí v klientech chatu, které jste povolili pro robota.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Vytvoření vícenásobné konverzace ze struktury dokumentu

Když vytvoříte znalostní bázi, v oddílu **naplnění** v rámci znalostní báze se zobrazí zaškrtávací políčko **Povolit vícenásobné extrakce z adres URL, soubory. PDF nebo. docx** .

![Zaškrtávací políčko pro povolení extrakce s vícenásobným zapnutím](../media/conversational-context/enable-multi-turn.png)

Když vyberete tuto možnost, QnA Maker extrahuje hierarchii přítomnou ve struktuře dokumentu. Hierarchie se převede v nástroji na výzvy pro následné zpracování a kořen hierarchie slouží jako nadřazené QnA. V některých dokumentech neobsahuje kořen hierarchie obsah, který by mohl sloužit jako odpověď, můžete zadat výchozí text odpovědi, který se použije jako náhrada za text odpovědi pro extrakci takových hierarchií.

Strukturu vícenásobného navýšení můžete odvodit jenom z adres URL, souborů PDF nebo souborů DOCX. Příklad struktury najdete v obrazovém [souboru PDF Microsoft Surface User Manually](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf).

:::image type="content" source="../media/conversational-context/import-file-with-conversational-structure.png" alt-text="Snímek obrazovky ukazuje příklad struktury v uživatelské příručce." lightbox="../media/conversational-context/import-file-with-conversational-structure.png":::

### <a name="building-your-own-multi-turn-document"></a>Sestavení vlastního dokumentu s vícenásobným zahnutím

Pokud vytváříte dokument s vícenásobným zahnutím, pamatujte na následující pokyny:

* Použijte záhlaví a dílčí záhlaví k označení hierarchie. Například můžete chtít, aby se QnAy, které jsou v nadřazeném prvku, a přiřadí se QnA, který by měl být proveden jako výzva. Použijte malou velikost nadpisu k označení další hierarchie. Nepoužívejte styly, barvy ani jiný mechanismus pro určení struktury v dokumentu, QnA Maker neextrahuje výzvy vícenásobného zapnutí.

* První znak nadpisu musí být velkými písmeny.

* Nekončit záhlavím otazníkem, `?` .

* [Vzorový dokument](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) můžete použít jako příklad k vytvoření vlastního dokumentu vícenásobného vypínání.

### <a name="adding-files-to-a-multi-turn-kb"></a>Přidání souborů do vícenásobného zapnutí KB

Když přidáte hierarchický dokument, QnA Maker určí následné výzvy ze struktury k vytvoření toku konverzace.

1. V QnA Maker vyberte existující znalostní bázi, která se vytvořila pomocí **Povolení extrakce z adres URL, souborů. PDF nebo. docx z více než** . umožněn.
1. Přejít na stránku **Nastavení** , vyberte soubor nebo adresu URL, které chcete přidat.
1. **Uložte a výuka** znalostní báze.

> [!Caution]
> Podpora pro použití exportovaného souboru. základní znalostní báze ve formátu TSV nebo XLS jako zdroj dat pro novou nebo prázdnou znalostní bázi není podporována. Tento typ souboru je potřeba **importovat** na stránce **nastavení** na portálu QnA maker, aby se do znalostní báze přidaly exportované výzvy s vícenásobným zapnutím.

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Vytvoření znalostní báze s použitím s více zapnutími výzev pomocí rozhraní API pro vytvoření

Můžete vytvořit znalostní případ s vícenásobnými výzvami, a to pomocí [QnA Maker vytvořit rozhraní API](/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Výzvy se přidávají do `context` `prompts` pole vlastnosti.

## <a name="show-questions-and-answers-with-context"></a>Zobrazit otázky a odpovědi s použitím kontextu

Zmenšete zobrazené páry otázek a odpovědí jenom na ty, které mají kontextové konverzace.

Vyberte **Možnosti zobrazení** a pak vyberte **Zobrazit kontext**. V seznamu se zobrazí páry otázek a odpovědí, které obsahují následné výzvy.

![Filtrovat páry otázek a odpovědí pomocí kontextových konverzací](../media/conversational-context/filter-question-and-answers-by-context.png)

V prvním sloupci se zobrazí kontext vícenásobného zapnutí.

:::image type="content" source="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png" alt-text="Snímek obrazovky ukazuje zvýrazněný kontextový oddíl." lightbox="../media/conversational-context/surface-manual-pdf-follow-up-prompt.png":::

V předchozím obrázku **#1** označuje tučný text ve sloupci, který označuje aktuální otázku. Nadřazená otázka je nejvyšší položka na řádku. Všechny dotazy pod ní jsou propojené páry otázek a odpovědí. Tyto položky jsou vybrané, takže můžete okamžitě přejít k ostatním položkám kontextu.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Přidat existující pár otázek a odpovědí jako následnou výzvu

Původní otázka, **můj účet** má následné výzvy, jako jsou **účty a přihlašování**.

![Odpovědi a výzvy pro následné přihlášení](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Přidejte následnou výzvu na existující pár otázek a odpovědí, který není aktuálně propojený. Vzhledem k tomu, že se otázka neodkazuje na žádnou dvojici dotazů a odpovědí, je nutné změnit aktuální nastavení zobrazení.

1. Pokud chcete propojit existující dvojici otázek a odpovědí jako následné výzvy, vyberte řádek pro pár otázek a odpovědí. V případě ručního povrchu vyhledejte seznam **odhlášení** , abyste snížili seznam.
1. V řádku pro **odhlášení** klikněte ve sloupci **odpověď** na **Přidat následnou výzvu**.
1. Do polí v místním okně **výzvy pro zpracování výzvy** zadejte následující hodnoty:

    |Pole|Hodnota|
    |--|--|
    |Zobrazený text|Zadejte **vypnout zařízení**. Toto je vlastní text, který se zobrazí v následném dotazu.|
    |Pouze kontext| Zaškrtněte toto políčko. Odpověď je vrácena pouze v případě, že otázka určuje kontext.|
    |Odkaz na odpověď|Pokud chcete najít existující pár otázek a odpovědí, zadejte **použít přihlašovací obrazovku** .|

1.  Vrátí se jedna shoda. Vyberte tuto odpověď jako následnou a potom vyberte **Uložit**.

    ![Stránka "následné výzvy (PREVIEW)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Po přidání následné výzvy vyberte možnost **Uložit a výuka** v horním navigačním panelu.

### <a name="edit-the-display-text"></a>Upravit zobrazený text

Když se vytvoří následná výzva a jako **odkaz na odpověď** se zadá existující pár otázek a odpovědí, můžete zadat nový **zobrazený text**. Tento text nenahrazuje stávající otázku a nepřidá novou alternativní otázku. Je oddělená od těchto hodnot.

1. Chcete-li upravit zobrazený text, vyhledejte a vyberte otázku v poli **Context** .
1. V řádku této otázky vyberte v sloupci odpověď následnou výzvu.
1. Vyberte zobrazený text, který chcete upravit, a pak vyberte **Upravit**.

    ![Příkaz Edit pro zobrazený text](../media/conversational-context/edit-existing-display-text.png)

1. V místním okně **výzvy k následnému** zobrazení změňte existující zobrazený text.
1. Až budete hotovi s úpravou zobrazovaného textu, vyberte **Uložit**.
1. V horním navigačním panelu **uložte a prohlaste**.

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Přidání nového páru otázek a odpovědí jako následné výzvy

Když do znalostní báze přidáte novou dvojici otázek a odpovědí, každá dvojice by měla být propojená s existující otázkou jako následná výzva.

1. Na panelu nástrojů znalostní báze vyhledejte a vyberte existující pár otázek a odpovědí pro **účty a přihlaste** se.

1. Ve sloupci **odpověď** pro tuto otázku vyberte **Přidat následnou výzvu**.
1. V části **výzva k následnému zobrazení (Preview)** vytvořte novou následnou výzvu zadáním následujících hodnot:

    |Pole|Hodnota|
    |--|--|
    |Zobrazený text|*Vytvořte účet systému Windows*. Vlastní text, který se má zobrazit v následných dotazech|
    |Pouze kontext|Zaškrtněte toto políčko. Tato odpověď se vrátí pouze v případě, že otázka určuje kontext.|
    |Odkaz na odpověď|Jako odpověď zadejte následující text:<br>*[Vytvořte](https://account.microsoft.com/) účet systému Windows s novým nebo existujícím e-mailovým účtem*.<br>Při ukládání a učení databáze se tento text převede. |
    |||

    ![Vytvořit novou otázku a odpověď na výzvu](../media/conversational-context/create-child-prompt-from-parent.png)

1. Vyberte **vytvořit novou** a potom vyberte **Uložit**.

    Tato akce vytvoří novou dvojici otázek a odpovědí a propojí vybranou otázku jako následnou výzvu. **Kontextový** sloupec pro obě otázky indikuje vztah následné výzvy.

1. Vyberte **Možnosti zobrazení** a pak vyberte [**Zobrazit kontext (Preview)**](#show-questions-and-answers-with-context).

    Nová otázka ukazuje, jak je propojena.

    ![Vytvoření nové následné výzvy](../media/conversational-context/new-qna-follow-up-prompt.png)

    Nadřazený dotaz zobrazí novou otázku jako jednu z možností.

    :::image type="content" source="../media/conversational-context/child-prompt-created.png" alt-text="Snímek obrazovky zobrazující kontextový sloupec pro obě otázky indikuje vztah následné výzvy." lightbox="../media/conversational-context/child-prompt-created.png":::

1. Po přidání následné výzvy vyberte v horním navigačním panelu možnost **Uložit a výuka** .

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Zobrazit vícenásobné zapínání během testování následných výzev

Když otestujete dotaz pomocí následných výzev v podokně **test** , odpověď zahrnuje následné výzvy.

![Odpověď zahrnuje následné výzvy.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Požadavek JSON, který vrátí úvodní odpověď a výzvy pro následné zpracování

Pomocí prázdného `context` objektu si vyžádejte odpověď na otázku uživatele a zahrňte následné výzvy.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Odpověď JSON, která vrátí úvodní odpověď a výzvy pro následné zpracování

Předchozí část požadovala odpověď a jakékoli následné výzvy k zadání **účtů a přihlášení**. Odpověď obsahuje informace o příkazovém řádku, který se nachází v *odpovědi [0]. kontext*, a text, který se má uživateli zobrazit.

```JSON
{
    "answers": [
        {
            "questions": [
                "Accounts and signing in"
            ],
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way he or she likes. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 100.0,
            "id": 15,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n  Go to Start, and right-click your name. Then select Sign out. ",
            "score": 38.01,
            "id": 18,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 16,
                        "qna": null,
                        "displayText": "Turn off the device"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 27.53,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

`prompts`Pole poskytuje text v této `displayText` vlastnosti a `qnaId` hodnotě. Tyto odpovědi můžete zobrazit jako další zobrazené možnosti v toku konverzace a potom odeslat vybrané `qnaId` zpět do QnA maker v následující žádosti.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Požadavek JSON, který vrátí nepočáteční odpověď a výzvy pro následné zpracování

Vyplňte `context` objekt tak, aby zahrnoval předchozí kontext.

V následující žádosti JSON se v aktuální otázce *k přihlášení používá Windows Hello* a předchozí otázka byla *účty a přihlašování*.

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "qnaId": 17,
  "context": {
    "previousQnAId": 15,
    "previousUserQuery": "accounts and signing in"
  }
}
```

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Odpověď JSON, která vrátí nepočáteční odpověď a výzvy pro následné zpracování

Odpověď QnA Maker _GenerateAnswer_ JSON zahrnuje následné výzvy ve `context` Vlastnosti první položky v `answers` objektu:

```JSON
{
    "answers": [
        {
            "questions": [
                "Use Windows Hello to sign in"
            ],
            "answer": "**Use Windows Hello to sign in**\n\nSince Surface Pro 4 has an infrared (IR) camera, you can set up Windows Hello to sign in just by looking at the screen. \n\nIf you have the Surface Pro 4 Type Cover with Fingerprint ID (sold separately), you can set up your Surface sign you in with a touch. \n\nFor more info, see What is Windows Hello? on Windows.com. ",
            "score": 100.0,
            "id": 17,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Meet Surface Pro 4"
            ],
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere's a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you're ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3" display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth&reg; \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
            "score": 21.92,
            "id": 3,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 4,
                        "qna": null,
                        "displayText": "Ports and connectors"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 19.04,
            "id": 16,
            "source": "product-manual.pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ]
}
```

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Dotazování znalostní báze s ID QnA Maker

Pokud vytváříte vlastní aplikaci pomocí funkce vícenásobného zapnutí. V reakci prvotní otázky se vrátí všechny následné výzvy a její přidružené výzvy `qnaId` . Teď, když máte ID, můžete to předat v textu žádosti o následné výzvy. Pokud tělo požadavku obsahuje `qnaId` , a kontextový objekt (který obsahuje předchozí vlastnosti QnA maker), vrátí GenerateAnswer přesný dotaz podle ID namísto použití algoritmu řazení k vyhledání odpovědi textem otázky.

## <a name="display-order-is-supported-in-the-update-api"></a>V rozhraní API pro aktualizaci se podporuje pořadí zobrazení.

[Zobrazení textu a pořadí zobrazení](/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto)vrácené v odpovědi JSON je podporované pro úpravy pomocí [rozhraní Update API](/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Přidání nebo odstranění výzev s vícenásobným zahnutím pomocí aktualizačního rozhraní API

Pomocí [rozhraní API pro QnA maker Update](/rest/api/cognitiveservices/qnamaker/knowledgebase/update)můžete přidat nebo odstranit výzvy k vícenásobnému zapínání.  Výzvy se přidávají do `context` `promptsToAdd` pole vlastnosti a `promptsToDelete` pole.

## <a name="export-knowledge-base-for-version-control"></a>Exportovat znalostní bázi pro správu verzí

QnA Maker podporuje správu verzí zahrnutím kroků vícenásobného konverzace do exportovaného souboru.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o kontextových konverzacích z tohoto [dialogového okna ukázka](https://github.com/microsoft/BotBuilder-Samples/blob/master/samples/csharp_dotnetcore/adaptive-dialog/07.qnamaker/QnAMaker.csproj) nebo si přečtěte další informace o [návrhu koncepčního robota pro vícenásobné konverzace](/azure/bot-service/bot-builder-conversations).

> [!div class="nextstepaction"]
> [Migrace znalostní báze](../Tutorials/migrate-knowledge-base.md)