---
title: Zapnutí více konverzace
titleSuffix: Azure Cognitive Services
description: Použít ke správě více zapne, označované jako více zapnout pro jednu otázku robotům výzvy a kontext. Zapnutí více je možnost nechat konverzace a zpět, kde předchozí otázce kontextu ovlivňuje další otázku a odpověď.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/26/2019
ms.author: diberry
ms.openlocfilehash: 10249375922b47a40f71a60938cdd12ffe0f9b54
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508140"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Pomocí zpracování pokynů můžete vytvořit více zapne konverzaci

Umožňuje spravovat více zapne, označované jako zpracování pokynů a kontext _více zapnout_, pro svého robota od jedné otázky do jiného.

Pokud chcete zobrazit, jak funguje více zapnout, zobrazte následující video ukázku:

[![Konverzace více zapnout v nástroje QnA Maker](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Co je konverzaci více zapnout?

Některé dotazy nelze odpovědi v jednom tahu. Při návrhu konverzací klientské aplikace (chatovací robot) může uživatel položit otázku, kterou je potřeba filtrovaná nebo vylepšili o určit správnou odpověď. Chcete, aby tento tok prostřednictvím otázky možné uživateli s *zpracování vyzve*.

Když uživatel zeptá na otázku, nástroj QnA Maker se vrátí odpověď _a_ výzev zpracování. Tato odpověď umožňuje zpracování dotazů jako možnosti k dispozici. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Příklad více zapnout konverzace s chatovací robot

S více zapnout je chatovací robot spravuje konverzace s uživatele k určení konečné odpověď, jak je znázorněno na následujícím obrázku:

![Dialogové okno více zapnout výzvy, které provedou uživatele prostřednictvím konverzace](../media/conversational-context/conversation-in-bot.png)

Na předchozím obrázku, má uživatel začal(a) konverzaci tak, že zadáte **Můj účet**. Znalostní báze má tři propojené dvojice otázku a odpověď. Upřesněte odpověď, uživatel vybere jeden ze tří možností znalostní báze knowledge base. Otázka (č. 1), má tři zpracování pokynů, které jsou uvedeny v chatovací robot jako tři možnosti (č. 2). 

Když uživatel vybere možnost (č. 3), se zobrazí další seznam upřesnění možnosti (č. 4). Toto pořadí bude pokračovat (č. 5), dokud uživatel Určuje odpověď správná, konečný (6).

> [!NOTE]
> Na předchozím obrázku **povolit více zapnout** zaškrtnuto políčko k zajištění, že se zobrazují výzvy. 

### <a name="use-multi-turn-in-a-bot"></a>Použití více zapnout v robota

Chcete-li spravovat kontextové konverzace, změňte klientské aplikace pomocí [přidávání kódu do svého robota](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting). Přidání kódu umožňují uživatelům zobrazit pokynů.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Vytvoření více zapnout konverzace ze struktury dokumentu.

Při vytváření znalostní báze **naplnit znalostní BÁZÍ** části zobrazí **povolit více zapnout extrakce z adres URL, soubory .pdf nebo .docx** zaškrtávací políčko. 

![Zaškrtněte políčko pro povolení více zapnout extrakce](../media/conversational-context/enable-multi-turn.png)

Když vyberete tuto možnost pro importovaný dokument, konverzace více na řadě vy můžete odvozen z strukturu dokumentu. Pokud danou strukturu existuje, nástroj QnA Maker vytvoří zpracování řádku tohoto páry otázek a odpovědí jako součást procesu importu. 

Struktura více zapnout jde odvodit jenom z adres URL, soubory PDF, nebo soubory DOCX. Příklad struktury, zobrazte obrázek [soubor PDF ruční uživatele Microsoft Surface](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf). Kvůli velikosti tento soubor PDF, vyžaduje nástroj QnA Maker prostředků **cenová úroveň hledání** z **B** (15 indexy) nebo vyšší. 

![! [Příklad struktury v uživatelské příručce] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Při importu dokumentu PDF určuje QnA Maker zpracování vyzve ze struktury k vytvoření konverzační toku. 

1. Nástroj QnA Maker, vyberte **vytvoření znalostní báze**.
1. Vytvořit nebo použít existující službu QnA Maker. V předchozím příkladu Microsoft Surface, protože soubor PDF je moc velká pro nižší úroveň, služba QnA Maker se použít **služba Search** z **B** (15 indexy) nebo vyšší.
1. Zadejte název pro znalostní báze, například **Surface ruční**.
1. Vyberte **povolit více zapnout extrakce z adres URL, soubory .pdf nebo .docx** zaškrtávací políčko. 
1. Vyberte zařízení Surface ruční adresy URL, **https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf** .

1. Vyberte **vytvořit znalostní BÁZÍ** tlačítko. 

    Po vytvoření znalostní báze, zobrazí se zobrazení párů otázku a odpověď.

## <a name="show-questions-and-answers-with-context"></a>Zobrazit dotazy a odpovědi s kontextem

Omezte zobrazené páry otázku a odpověď jenom na ty s kontextové konverzace. 

Vyberte **zobrazit možnosti**a pak vyberte **zobrazit kontext (PREVIEW)** . V seznamu zobrazí otázky a odpovědi dvojice, které obsahují zpracování pokynů. 

![Filtrovat podle kontextové konverzace páry otázku a odpověď](../media/conversational-context/filter-question-and-answers-by-context.png)

Kontext více zapnutí se zobrazí v prvním sloupci.

![! [Sloupec "kontextu (PREVIEW)"] (.. / media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

Na předchozím obrázku **#1** označuje tučný text ve sloupci, který označuje, že aktuální dotaz. Nadřazené otázka je začátek položky na řádku. Pod ní nějaké dotazy jsou propojené páry otázku a odpověď. Tyto položky jsou volitelné, takže můžete okamžitě přejít na další kontext položky. 

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Přidat existující dvojice otázku a odpověď jako zpracování řádku

Původní dotaz **Můj účet**, má zpracování pokynů, například **účty a přihlásíte se**. 

!["Účty a přihlášení" odpovědi a následnou výzvy](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Přidání zpracování řádku do existující dvojice otázku a odpověď, která aktuálně není propojena. Protože dotaz není propojený s nějaká dvojice otázku a odpověď, je potřeba změnit aktuální nastavení zobrazení.

1. Pokud chcete propojit existující dvojice otázku a odpověď jako zpracování řádku, vyberte řádek pro dvojici otázku a odpověď. Zařízení Surface manuální, vyhledejte si **Odhlásit** ke snížení seznamu.
1. V řádku **Signout**v **odpovědí** sloupci vyberte **přidat zpracování řádku**.
1. V polích **pokračování řádku (PREVIEW)** automaticky otevírané okno, zadejte následující hodnoty:

    |Pole|Hodnota|
    |--|--|
    |Zobrazení textu|Zadejte **vypněte zařízení**. Toto je vlastní text k zobrazení v následné řádku.|
    |Pouze pro kontext| Zaškrtněte toto políčko. Odpověď se vrátí jenom v případě, že na otázku určuje kontext.|
    |Odkaz na|Zadejte **použijte přihlašovací obrazovka** najít existující pár otázku a odpověď.|


1.  Jedna shoda bude vrácena. Vyberte tuto odpověď jako sledování a pak vyberte **Uložit**. 

    ![Na stránce "Řádku následné akce (PREVIEW)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Po přidání zpracování řádku, vyberte **uložit a jejich trénování** v horním navigačním panelu.
  
### <a name="edit-the-display-text"></a>Upravit zobrazení textu 

Při zpracování řádku je vytvořen a existující dvojice otázku a odpověď je zadán jako **odkaz na odpověď**, můžete zadat nový **zobrazení textu**. Tento text nenahrazuje existující dotaz a nepřidá alternativní novou otázku. Je oddělená od těchto hodnot. 

1. Chcete-li upravit text k zobrazení, vyhledejte a vyberte otázku v **kontextu** pole.
1. V řádku pro tento dotaz vyberte následné řádek ve sloupci odpovědí. 
1. Vyberte zobrazení textu, který chcete upravit a pak vyberte **upravit**.

    ![Příkaz Upravit pro zobrazení textu](../media/conversational-context/edit-existing-display-text.png)

1. V **řádku následné akce** automaticky otevírané okno, změňte existující zobrazovaný text. 
1. Po dokončení úprav textu zobrazení, vyberte **Uložit**. 
1. V horním navigačním panelu **uložit a jejich trénování**.


<!--

## To find the best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a specific question-and-answer pair but you know, as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base. You can then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, and then the follow-ups are returned.

1. Add metadata to each of the two follow-up question-and-answer pairs:

    |Question|Add metadata|
    |--|--|
    |*Feedback on a QnA Maker service*|"Feature":"all"|
    |*Feedback on an existing feature*|"Feature":"one"|
    
    ![The "Metadata tags" column for adding metadata to a follow-up prompt](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Select **Save and train**. 

    When you send the question **Give feedback** with the metadata filter **Feature** with a value of **all**, only the question-and-answer pair with that metadata is returned. QnA Maker doesn't return both question-and-answer pairs, because both don't match the filter. 

-->

## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Zadat nový pár otázku a odpověď jako zpracování řádku

Když přidáte nový pár otázku a odpověď do znalostní báze, musí být každý pár propojena na existující dotaz zpracování příkazovém řádku.

1. Na panelu nástrojů znalostní báze knowledge base, vyhledejte a vyberte existující pár otázku a odpověď pro **účty a přihlásíte se**. 

1. V **odpovědí** sloupce pro tento dotaz, vyberte **přidat zpracování řádku**. 
1. V části **pokračování řádku (PREVIEW)** , vytvořte nového řádku následné zadáním následujících hodnot: 

    |Pole|Hodnota|
    |--|--|
    |Zobrazení textu|*Vytvoření účtu Windows*. Vlastní text k zobrazení v následné řádku.|
    |Pouze pro kontext|Zaškrtněte toto políčko. Tato odpověď se vrátí jenom v případě, že na otázku určuje kontext.|
    |Odkaz na|Jako odpověď, zadejte následující text:<br>*[Vytvoření](https://account.microsoft.com/) účet Windows s novou nebo existující e-mailový účet*.<br>Při uložení a trénování databáze, tento text bude převeden. |
    |||

    ![Vytvořit nové výzvy otázku a odpověď](../media/conversational-context/create-child-prompt-from-parent.png)


1. Vyberte **vytvořit nový**a pak vyberte **Uložit**. 

    Tato akce vytvoří jako zpracování řádku nový pár otázku a odpověď a odkazy na vybranou otázku. **Kontextu** sloupec pro oba dotazy označuje zpracování výzvy vztah. 

1. Vyberte **zobrazit možnosti**a pak vyberte [ **zobrazit kontext (PREVIEW)** ](#show-questions-and-answers-with-context).

    Nový dotaz ukazuje, jak je propojena.

    ![Vytvoření nového řádku zpracování](../media/conversational-context/new-qna-follow-up-prompt.png)

    Nadřazené dotaz zobrazí novou otázku jako jeden z jeho možnosti.

    ![! [Sloupci kontext pro oba dotazy označuje zpracování výzvy vztah] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Po přidání zpracování řádku, vyberte **uložit a jejich trénování** v horním navigačním panelu.

## <a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>Povolit více zapnout během testování zpracování dotazů

Při testování dotazu s následnou akci výzvy v **testování** vyberte **povolit více zapnout**a pak zadejte svůj dotaz. Odpověď obsahuje zpracování pokynů.

![Odpověď obsahuje zpracování pokynů](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Pokud nepovolíte více zapnout, odpověď se vrátí, ale nebudou zobrazeny zpracování pokynů.

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Žádost o JSON vrátí počáteční odpovědí a následné výzvy

Použít prázdnou `context` objekt zahrnovat zpracování pokynů a požádat o odpověď na otázku uživatele. 

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Vrátí počáteční odpovědí a zpracování pokynů odpověď ve formátu JSON

V předchozí části Požadovaná odpověď a všechny následné pokynů **účty a přihlásíte se**. Odpověď obsahuje výzvy informace, které se nacházejí v *odpovědi [0] .context*a text, který se zobrazí uživateli. 

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
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start, and right-click your name. Then select Sign out. ",
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

`prompts` Pole obsahuje text `displayText` vlastnost a `qnaId` hodnotu. Tyto odpovědi můžete zobrazit další možnosti zobrazené v konverzaci tok a pak odeslat vybraný `qnaId` zpět do nástroje QnA Maker v následujícím požadavku. 

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Žádost o JSON k vrácení odpovědí není počáteční a následné výzvy

Zadejte `context` objektu, který chcete zahrnout předchozí kontext.

V následujícím požadavku JSON je aktuální dotaz *použití Windows Hello k přihlášení* a byl předchozí otázce *účty a přihlásíte se*. 

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Odpověď ve formátu JSON k vrácení odpovědí není počáteční a následné výzvy

Nástroj QnA Maker _GenerateAnswer_ odpověď JSON obsahuje zpracování pokynů `context` vlastnost na první položku v `answers` objektu:

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
            "answer": "**Meet Surface Pro 4**\n\nGet acquainted with the features built in to your Surface Pro 4. \n\nHere’s a quick overview of Surface Pro 4 features: \n\n\n\n\n\n\n\nPower button \n\n\n\n\n\nPress the power button to turn your Surface Pro 4 on. You can also use the power button to put it to sleep and wake it when you’re ready to start working again. \n\n\n\n\n\n\n\nTouchscreen \n\n\n\n\n\nUse the 12.3” display, with its 3:2 aspect ratio and 2736 x 1824 resolution, to watch HD movies, browse the web, and use your favorite apps. \n\nThe new Surface G5 touch processor provides up to twice the touch accuracy of Surface Pro 3 and lets you use your fingers to select items, zoom in, and move things around. For more info, see Surface touchscreen on Surface.com. \n\n\n\n\n\n\n\nSurface Pen \n\n\n\n\n\nEnjoy a natural writing experience with a pen that feels like an actual pen. Use Surface Pen to launch Cortana in Windows or open OneNote and quickly jot down notes or take screenshots. \n\nSee Using Surface Pen (Surface Pro 4 version) on Surface.com for more info. \n\n\n\n\n\n\n\nKickstand \n\n\n\n\n\nFlip out the kickstand and work or play comfortably at your desk, on the couch, or while giving a hands-free presentation. \n\n\n\n\n\n\n\nWi-Fi and Bluetooth® \n\n\n\n\n\nSurface Pro 4 supports standard Wi-Fi protocols (802.11a/b/g/n/ac) and Bluetooth 4.0. Connect to a wireless network and use Bluetooth devices like mice, printers, and headsets. \n\nFor more info, see Add a Bluetooth device and Connect Surface to a wireless network on Surface.com. \n\n\n\n\n\n\n\nCameras \n\n\n\n\n\nSurface Pro 4 has two cameras for taking photos and recording video: an 8-megapixel rear-facing camera with autofocus and a 5-megapixel, high-resolution, front-facing camera. Both cameras record video in 1080p, with a 16:9 aspect ratio. Privacy lights are located on the right side of both cameras. \n\nSurface Pro 4 also has an infrared (IR) face-detection camera so you can sign in to Windows without typing a password. For more info, see Windows Hello on Surface.com. \n\nFor more camera info, see Take photos and videos with Surface and Using autofocus on Surface 3, Surface Pro 4, and Surface Book on Surface.com. \n\n\n\n\n\n\n\nMicrophones \n\n\n\n\n\nSurface Pro 4 has both a front and a back microphone. Use the front microphone for calls and recordings. Its noise-canceling feature is optimized for use with Skype and Cortana. \n\n\n\n\n\n\n\nStereo speakers \n\n\n\n\n\nStereo front speakers provide an immersive music and movie playback experience. To learn more, see Surface sound, volume, and audio accessories on Surface.com. \n\n\n\n\n",
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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Dotazování ve znalostní bázi s ID nástroje QnA Maker

V odpovědi na první otázku, všechny následné výzvy a jeho přidružených `qnaId` je vrácena. Teď, když máte ID, můžete předat toto v textu požadavku na zpracování řádku. Pokud tělo žádosti obsahuje `qnaId`a objektu context (který obsahuje vlastnosti předchozí QnA Maker), pak GenerateAnswer vrátí přesný dotaz podle ID, namísto použití algoritmu řazení najít odpověď text otázky. 

## <a name="display-prompts-and-send-context-in-the-client-application"></a>Zobrazení výzvy a odeslání kontextu v klientské aplikaci 

Jsme přidali výzvy ve znalostní bázi a otestovat tok v testovací podokno. Teď budete muset použít tyto výzvy v klientské aplikaci. Pro rozhraní Bot Framework se automaticky nezobrazují výzvy v klientských aplikacích. Můžete zobrazit pokynů doporučené akce nebo tlačítka jako součást odpověď na dotaz uživatele v klientských aplikacích včetně to [Bot Framework ukázka](https://aka.ms/qnamakermultiturnsample) ve vašem kódu. Klientská aplikace uložit aktuální ID nástroje QnA Maker a uživatelský dotaz a předat je do [objekt kontextu API GenerateAnswer](#a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts) pro další uživatele dotaz. 

## <a name="display-order-is-supported-in-the-update-api"></a>Pořadí zobrazení je podporován v rozhraní API Update

[Zobrazení textu a pořadí zobrazení](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), vrátila v odpovědi JSON, se podporuje pro úpravy [Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Vytvoření znalostní báze knowledge base s více zapnout výzvy s rozhraním API pro vytváření

Může vytvořit případ znalostní báze s více zapnout výzvy k použití [vytvořit API nástroje QnA Maker](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Přidávání pokynů `context` vlastnosti `prompts` pole. 


## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Přidání nebo odstranění více zapnout výzvy s rozhraním API pro aktualizace

Můžete přidat nebo odstranit více zapnout výzvy k použití [API nástroje QnA Maker aktualizace](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).  Přidávání pokynů `context` vlastnosti `promptsToAdd` pole a `promptsToDelete` pole. 


## <a name="next-steps"></a>Další postup

Další informace o kontextových konverzace z tohoto [dialogové okno ukázkové](https://aka.ms/qnamakermultiturnsample) nebo Další informace o [koncepční bot návrh pro více zapnout konverzace](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrace znalostní báze](../Tutorials/migrate-knowledge-base.md)
