---
title: Zapnutí více konverzace
titleSuffix: Azure Cognitive Services
description: Použít ke správě více zapne, označované jako více zapnout pro jednu otázku robotům výzvy a kontext. Více zapněte je schopnost zálohování a dále konverzace, kde předchozí otázce kontextu ovlivňuje další otázky a odpovědi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/12/2019
ms.author: diberry
ms.openlocfilehash: 7c7d7c480068b754413fd2309a2251d1e3855106
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67075178"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Pomocí zpracování pokynů můžete vytvořit více zapne konverzaci

Umožňuje spravovat více zapne, označované jako zpracování pokynů a kontext _více zapnout_, pro svého robota od jedné otázky do jiného.

Podívejte se na následující ukázkové video a zjistěte, jak se to dělá.

[![](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Co je konverzaci více zapnout?

Některé dotazy nelze odpovědi v jednom tahu. Při návrhu konverzací klientské aplikace (chatovací robot) může uživatel položit otázku, kterou je potřeba filtrovaná nebo vylepšili, aby bylo možné zjistit správné odpovědi. Tento tok prostřednictvím na otázky, které je možné tím, že uživatel předloží **zpracování vyzve**.

Když uživatel požádá na otázku, nástroje QnA Maker se vrátí odpověď _a_ výzev zpracování. To umožňuje zpracování dotazů jako možnosti k dispozici. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Příklad více zapnout konverzace s chatovací robot

Chatovací robot spravuje konverzace s uživatelem, dotaz otázku, k určení konečné odpovědí.

![V rámci konverzační flow Správa stavu konverzace v systému s více zapnout dialogové okno tím, že poskytuje výzvy v rámci odpovědi zobrazí jako možnosti, jak pokračovat v konverzaci.](../media/conversational-context/conversation-in-bot.png)

Na předchozím obrázku, uživatel zadal `My account`. Znalostní báze se 3 propojené QnA dvojice. Uživatel musí vybrat jednu z tři možnosti pro upřesnění odpověď. Znalostní báze knowledge base na otázku (č. 1), má tři zpracování pokynů, zobrazen v chatovací robot jako tři možnosti (č. 2). 

Když uživatel vybere možnost (č. 3), se zobrazí další seznam upřesnění možnosti (č. 4). (5) to může pokračovat, dokud určit správné a finální odpovědí (6).

Na předchozím obrázku má **povolit více zapnout** vybraná zobrazí výzvy. 

### <a name="using-multi-turn-in-a-bot"></a>Použití více zapnout v robota

Je třeba změnit klientská aplikace mohla spravovat kontextové konverzace. Budete muset přidat [kód pro svého robota](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-prompting) zobrazíte pokynů.  

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Vytvoření více zapnout konverzace ze struktury dokumentu.

Při vytváření znalostní báze, zobrazí se volitelné zaškrtávací políčko Povolit více zapnout extrakce. 

![Při vytváření znalostní báze, zobrazí se volitelné zaškrtávací políčko Povolit více zapnout extrakce.](../media/conversational-context/enable-multi-turn.png)

Pokud vyberete tuto možnost, při importu dokumentu, můžete více zapnout konverzace odvozen z strukturu. Pokud danou strukturu existuje, vytvoří nástroj QnA Maker zpracování výzvy dvojice QnA za vás. 

Struktura více zapnout lze odvodit pouze z adresy URL a PDF, DOCX soubory. 

Na následujícím obrázku Microsoft Surface [souboru PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) je určen pro použití jako ruční. Kvůli velikosti tento soubor PDF, prostředek Azure QnA Maker vyžaduje vyhledávání cenová úroveň B (15 indexy) nebo vyšší. 

![! [Pokud importujete dokumentu, kontextové konverzace může být implicitní ze struktury. Pokud danou strukturu existuje, nástroj QnA Maker vytvoří zpracování výzvy QnA dvojice, jako součást import dokumentu.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Při importu dokumentu PDF, určuje QnA Maker zpracování pokynů ze struktury k vytvoření konverzační toku. 

1. V **kroku 1**vyberte **vytvoření znalostní báze** v horním navigačním panelu.
1. V **kroku 2**, vytvořit nebo použít existující službu QnA. Ujistěte se, že služba QnA použití se službou Search b (15 indexy) nebo vyšší, protože soubor PDF ruční Surface je příliš velký pro nižší úroveň.
1. V **kroku 3**, zadejte název pro znalostní báze, například `Surface manual`.
1. V **kroku 4**vyberte **povolit více zapnout extrakce z adres URL, soubory .pdf nebo .docx.** Vyberte adresu URL pro Surface ruční

    ```text
    https://github.com/Azure-Samples/cognitive-services-sample-data-files/raw/master/qna-maker/data-source-formats/product-manual.pdf
    ```

1. Vyberte **vytvořit znalostní BÁZÍ** tlačítko. 

    Jakmile se vytvoří ve znalostní bázi, zobrazí se páry otázek a odpovědí.

## <a name="show-questions-and-answers-with-context"></a>Zobrazit dotazy a odpovědi s kontextem

Snižte páry otázek a odpovědí zobrazí jenom na ty s kontextové konverzace. 

1. Vyberte **zobrazit možnosti**a pak vyberte **zobrazit kontext (PREVIEW)** . Tento seznam obsahuje páry otázek a odpovědí obsahující zpracování pokynů. 

    ![Filtrovat dotaz a odpověď dvojice pomocí kontextové konverzace](../media/conversational-context/filter-question-and-answers-by-context.png)

2. Kontext více zapnutí se zobrazí v prvním sloupci.

    ![! [Při importu dokumentu PDF, nástroj QnA Maker určuje zpracování pokynů ze struktury k vytvoření konverzační toku. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

    Na předchozím obrázku #1 znamená tučně ve sloupci, který označuje, že aktuální dotaz. Nadřazené otázka je začátek položky na řádku. Všechny níže uvedené otázky jsou propojené páry otázek a odpovědí. Tyto položky jsou volitelné, takže můžete okamžitě přejít na další kontext položky. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Přidat existující pár QnA zpracování příkazovém řádku

Původní dotaz z `My account` , jako je zpracování pokynů `Accounts and signing in`. 

![Správně vrátí původní dotaz "Účet" "Účty a přihlášení" Odpovědět a již má zpracování pokynů propojený.](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Přidání zpracování řádku do existující dvojice QnA, který není aktuálně propojený. Protože dotaz není propojený s nějaká dvojice QnA, je potřeba změnit aktuální nastavení zobrazení.

1. Pokud chcete propojit existující dvojice QnA zpracování příkazovém řádku, vyberte řádek pár otázek a odpovědí. Zařízení Surface manuální, vyhledejte si `Sign out` ke snížení seznamu.
1. V řádku `Signout`vyberte **přidat zpracování řádku** z **odpovědí** sloupce.
1. V **pokračování řádku (PREVIEW)** automaticky otevírané okno, zadejte následující:

    |Pole|Hodnota|
    |--|--|
    |Zobrazení textu|`Turn off the device`. Toto je vlastní text, který budete chtít zobrazit v následné řádku.|
    |Pouze pro kontext|Vybrat. Tato odpověď se vrátí, pouze pokud dotaz určuje kontext.|
    |Odkaz na odpověď|Zadejte `Use the sign-in screen` najít existující QnA dvojice.|


1.  Jedna shoda bude vrácena. Vyberte tuto odpověď jako sledování a potom vyberte **Uložit**. 

    ![Hledejte řádku následné akce odkaz do dialogového okna odpovědí pro existující odpověď, pomocí textu odpovědi.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Po přidání řádku následné, nezapomeňte vybrat **uložit a jejich trénování** v horním navigačním panelu.
  
### <a name="edit-the-display-text"></a>Upravit zobrazení textu 

Při zpracování řádku se vytvoří a je vybraná existující dvojice QnA **odkaz na odpověď**, můžete zadat nový **zobrazení textu**. Tento text nenahrazuje existující dotaz a nepřidá alternativní novou otázku. Je oddělená od těchto hodnot. 

1. Chcete-li upravit text k zobrazení, vyhledejte a vyberte otázku v **kontextu** pole.
1. Na tuto otázku řádek vyberte ve sloupci odpovědí zpracování řádku. 
1. Vyberte zobrazení textu, který chcete upravit a pak vyberte **upravit**.

    ![Vyberte zobrazovaný text, který chcete upravit a pak vyberte Upravit.](../media/conversational-context/edit-existing-display-text.png)

1. **Řádku následné akce** automaticky otevírané okno umožňuje změnit existující zobrazovaný text. 
1. Po dokončení úprav textu zobrazení, vyberte **Uložit**. 
1. Nezapomeňte zaškrtnout **uložit a jejich trénování** v horním navigačním panelu.


<!--

## To find best prompt answer, add metadata to follow-up prompts 

If you have several follow-up prompts for a given QnA pair, but you know as the knowledge base manager, that not all prompts should be returned, use metadata to categorize the prompts in the knowledge base, then send the metadata from the client application as part of the GenerateAnswer request.

In the knowledge base, when a question-and-answer pair is linked to follow-up prompts, the metadata filters are applied first, then the follow-ups are returned.

1. For the two follow-up QnA pairs, add metadata to each one:

    |Question|Add metadata|
    |--|--|
    |`Feedback on an QnA Maker service`|"Feature":"all"|
    |`Feedback on an existing feature`|"Feature":"one"|
    
    ![Add metadata to follow-up prompt so it can be filtered in conversation response from service](../media/conversational-context/add-metadata-feature-to-follow-up-prompt.png) 

1. Save and train. 

    When you send the question `Give feedback` with the metadata filter `Feature` with a value of `all`, only the QnA pair with that metadata will be returned. Both QnA pairs are not returned because they both do not match the filter. 

-->

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Přidejte novou dvojici QnA jako zpracování řádku

Zadat nový pár QnA znalostní báze. Pár QnA by měly být propojeny na existující dotaz zpracování příkazovém řádku.

1. Z panelu nástrojů ve znalostní bázi, vyhledejte a vyberte existující pár QnA pro `Accounts and Signing In`. 

1. V **odpovědí** sloupce pro tento dotaz, vyberte **přidat zpracování řádku**. 
1. **Pokračování řádku (PREVIEW)** , vytvořte nového řádku následné zadáním následujících hodnot: 

    |Textové pole|Hodnota|
    |--|--|
    |**Zobrazení textu**|`Create a Windows Account`. Toto je vlastní text, který budete chtít zobrazit v následné řádku.|
    |**Pouze pro kontext**|Vybrat. Tato odpověď se vrátí, pouze pokud dotaz určuje kontext.|
    |**Odkaz na**|Jako odpověď, zadejte následující text:<br>`[Create](https://account.microsoft.com/) a Windows account with a new or existing email account.`<br>Při uložení a trénování databáze, tento text bude převeden na |
    |||

    ![Vytvořit nové výzvy QnA](../media/conversational-context/create-child-prompt-from-parent.png)


1. Vyberte **vytvořit nový** vyberte **Uložit**. 

    To vytvoří nový pár otázku a odpověď a propojené vybrané otázky zpracování příkazovém řádku. **Kontextu** sloupec pro oba dotazy označuje zpracování výzvy vztah. 

1. Změnit **zobrazit možnosti** k [zobrazit kontext](#show-questions-and-answers-with-context).

    Nový dotaz ukazuje, jak je propojena.

    ![Vytvoření nového řádku zpracování ](../media/conversational-context/new-qna-follow-up-prompt.png)

    Dotaz nadřazené ukazuje novou otázku jako jeden z jeho možnosti.

    ![! [Sloupci kontext pro oba dotazy označuje vztah zpracování příkazový řádek.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Po přidání řádku následné, nezapomeňte vybrat **uložit a jejich trénování** v horním navigačním panelu.

## <a name="enable-multi-turn-when-testing-follow-up-prompts"></a>Povolit více zapnout při vyzve k testování následnou akci

Při testování dotazu s následnou akci výzvy v **testovací** vyberte **povolit více zapnout**a zadejte svůj dotaz. Odpověď obsahuje zpracování pokynů.

![Při testování v testovací podokno dotaz, odpověď obsahuje zpracování pokynů.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

Pokud nepovolíte více zapnout, vrátí se odpověď, ale nebudou zobrazeny zpracování pokynů.

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>JSON požadavek vrátit počáteční odpovědí a následné výzvy

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

## <a name="json-response-to-return-initial-answer-and-follow-up-prompts"></a>Odpověď JSON má být vrácena počáteční odpovědí a následné výzvy

V předchozí části Požadovaná odpověď a všechny následné pokynů `Accounts and signing in`. Odpověď obsahuje výzvy informace o umístění `answers[0].context`, patří text, který se zobrazí uživateli. 

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
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 17,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 18,
                        "qna": null,
                        "displayText": "Sign out"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 79,
                        "qna": null,
                        "displayText": "Create a Windows Account"
                    }
                ]
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
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

`prompts` Pole obsahuje text `displayText` vlastnost a `qnaId` tok hodnotu, takže můžete zobrazit tyto odpovědi jako další možnosti zobrazené v konverzaci, odešlete vybranou hodnotou a nástroje QnA Maker v následujícím požadavku. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>JSON požadavek vrátit jiné počáteční odpovědí a zpracování pokynů

Zadejte `context` objektu, který chcete zahrnout předchozí kontext.

V následujícím požadavku JSON je aktuální dotaz `Use Windows Hello to sign in` a byl předchozí otázce `Accounts and signing in`. 

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

##  <a name="json-response-to-return-non-initial-answer-and-follow-up-prompts"></a>Odpověď JSON má být vrácena mimo počáteční odpovědí a následné výzvy

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

## <a name="query-the-knowledge-base-with-the-qna-id"></a>Dotazování ve znalostní bázi s ID nástroje QnA

V odpovědi na první otázku, všechny následné výzvy a jeho přidružených `qnaId` je vrácena. Teď, když máte ID, můžete předat toto v textu požadavku na zpracování řádku. Pokud tělo žádosti obsahuje `qnaId`a objektu context (který obsahuje vlastnosti předchozí QnA), pak GenerateAnswer vrátí přesný dotaz podle ID, namísto použití algoritmu řazení najít odpověď text otázky. 

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Zobrazení výzvy a odesílání kontextu v klientské aplikaci 

Jsme přidali výzvy ve znalostní bázi a otestovat tok v testovací podokno. Teď budete muset použít tyto výzvy v klientské aplikaci. Pro rozhraní Bot Framework pokynů automaticky spouštět nebude zobrazovat v klientských aplikacích. Můžete zobrazit pokynů jako doporučené akce nebo tlačítka jako součást odpověď na dotaz uživatele v klientovi aplikace včetně to [Bot Framework ukázka](https://aka.ms/qnamakermultiturnsample) ve vašem kódu. Klientská aplikace uložit aktuální ID QnA a uživatelský dotaz a předat je do [objekt kontextu API GenerateAnswer](#json-request-to-return-non-initial-answer-and-follow-up-prompts) pro další uživatele dotaz. 

## <a name="display-order-supported-in-api"></a>Pořadí zobrazení, které jsou podporovány v rozhraní API

[Zobrazení textu a pořadí zobrazení](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), vrátila v odpovědi JSON, se podporuje pro úpravy [Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). 

<!--

FIX - Need to go to parent, then answer column, then edit answer. 

-->

## <a name="next-steps"></a>Další postup

Další informace o kontextových konverzace od [dialogové okno ukázkové](https://aka.ms/qnamakermultiturnsample) nebo si přečtěte o [koncepční bot návrh pro více zapnout konverzace](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrace znalostní báze](../Tutorials/migrate-knowledge-base.md)
