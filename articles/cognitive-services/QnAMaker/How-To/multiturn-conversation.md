---
title: Zapnutí více konverzace
titleSuffix: Azure Cognitive Services
description: Použít ke správě více zapne, označované jako více zapnout pro jednu otázku robotům výzvy a kontext. Více zapněte je schopnost zálohování a dále konverzace, kde předchozí otázce kontextu ovlivňuje další otázky a odpovědi.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.topic: article
ms.date: 05/14/2019
ms.author: diberry
ms.openlocfilehash: f12b55e9b00e933e13f84832b8cc36267a1da05f
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65954858"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Pomocí zpracování pokynů můžete vytvořit více zapne konverzaci

Umožňuje spravovat více zapne, označované jako zpracování pokynů a kontext _více zapnout_, pro svého robota od jedné otázky do jiného.

Poslechněte si, [ukázkové video](https://aka.ms/multiturnexample).

## <a name="what-is-a-multi-turn-conversation"></a>Co je konverzaci více zapnout?

Některé typy konverzace nelze dokončit v jedné zapnout. Při návrhu konverzací klientské aplikace (chatovací robot) může uživatel položit otázku, kterou je potřeba filtrovaná nebo vylepšili, aby bylo možné zjistit správné odpovědi. Tento tok prostřednictvím na otázky, které je možné tím, že uživatel předloží **zpracování vyzve**.

Když uživatel požádá na otázku, nástroje QnA Maker se vrátí odpověď _a_ výzev zpracování. To umožňuje zpracování dotazů jako možnosti k dispozici. 

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Příklad více zapnout konverzace s chatovací robot

Chatovací robot spravuje v konverzaci, dotaz otázku, s uživatelem a určit konečné odpovědí.

![V rámci konverzační flow Správa stavu konverzace v systému s více zapnout dialogové okno tím, že poskytuje výzvy v rámci odpovědi zobrazí jako možnosti, jak pokračovat v konverzaci.](../media/conversational-context/conversation-in-bot.png)

Na předchozím obrázku je potřeba kontrast před vrácením odpovědi na otázku uživatele. Znalostní báze knowledge base na otázku (č. 1), má čtyři zpracování pokynů, zobrazen v chatovací robot jako čtyř možností (č. 2). 

Když uživatel vybere možnost (č. 3), se zobrazí další seznam upřesnění možnosti (č. 4). (5) to může pokračovat, dokud určit správné a finální odpovědí (6).

Je třeba změnit klientská aplikace mohla spravovat kontextové konverzace.

## <a name="create-a-multi-turn-conversation-from-documents-structure"></a>Vytvoření více zapnout konverzace ze struktury dokumentu.
Při vytváření znalostní báze, zobrazí se volitelné zaškrtávací políčko Povolit více zapnout extrakce. Pokud vyberete tuto možnost, při importu dokumentu, můžete více zapnout konverzace odvozen z strukturu. Pokud danou strukturu existuje, vytvoří nástroj QnA Maker zpracování výzvy dvojice QnA za vás. Struktura více zapnout lze odvodit pouze z adresy URL a PDF, DOCX soubory. 

Na následujícím obrázku Microsoft Surface [souboru PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf) je určen pro použití jako ruční. 

![! [Pokud importujete dokumentu, kontextové konverzace může být implicitní ze struktury. Pokud danou strukturu existuje, nástroj QnA Maker vytvoří zpracování výzvy QnA dvojice, jako součást import dokumentu.] (.. / media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

Při importu dokumentu PDF, určuje QnA Maker zpracování pokynů ze struktury k vytvoření konverzační toku. 

![! [Při importu dokumentu PDF, nástroj QnA Maker určuje zpracování pokynů ze struktury k vytvoření konverzační toku. ](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

## <a name="show-questions-and-answers-with-context"></a>Zobrazit dotazy a odpovědi s kontextem

1. Snižte páry otázek a odpovědí zobrazí jenom na ty s kontextové konverzace. Vyberte **zobrazit možnosti**a pak vyberte **zobrazit kontext (PREVIEW)**. V seznamu bude prázdný, dokud nepřidáte první pár otázek a odpovědí s výzvou zpracování. 

    ![Filtrovat dotaz a odpověď dvojice pomocí kontextové konverzace](../media/conversational-context/filter-question-and-answers-by-context.png)

## <a name="add-new-qna-pair-as-follow-up-prompt"></a>Přidejte novou dvojici QnA jako zpracování řádku

1. Vyberte **QnA přidat pár**. 
1. Zadejte nový text otázky `Give feedback.` s odpovědí z `What kind of feedback do you have?`.

1. V **odpovědí** sloupce pro tento dotaz, vyberte **přidat zpracování řádku**. 
1. **Pokračování řádku (PREVIEW)** automaticky otevírané okno umožňuje vyhledat existující dotaz nebo zadejte novou otázku. Vytvořte nový řádek tak, že zadáte následující hodnoty: 

    |Textové pole|Hodnota|
    |--|--|
    |**Zobrazení textu**|`Feedback on an QnA Maker service`|
    |**Odkaz na**|`How would you rate QnA Maker??`|
    |||

    ![Vytvořit nové výzvy QnA](../media/conversational-context/create-child-prompt-from-parent.png)

1. Zkontrolujte **jen kontextu**. **Jen kontextu** možnost označuje, že tento uživatel text bude interpretovat _pouze_ Pokud tento parametr zadaný v reakci na předchozí otázce. V tomto scénáři text výzvy nemá žádné smysl jako samostatné otázku, pouze má smysl v kontextu předchozí otázce.
1. Vyberte **vytvořit nový** vyberte **Uložit**. 

    To vytvoří nový pár otázku a odpověď a propojené vybrané otázky zpracování příkazovém řádku. **Kontextu** sloupec pro oba dotazy označuje zpracování výzvy vztah. 

    ![! [Sloupci kontext pro oba dotazy označuje vztah zpracování příkazový řádek.] (.. / media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Vyberte **přidat zpracování řádku** pro `Give feedback` dotaz a přidat další zpracování řádku. Tím se otevře **pokračování řádku (PREVIEW)** automaticky otevírané okno.

1. Vytvořte nový řádek tak, že zadáte následující hodnoty:

    |Textové pole|Hodnota|
    |--|--|
    |**Zobrazení textu**|`Feedback on an existing feature`|
    |**Odkaz na**|`Which feature would you like to give feedback on?`|
    |||

1. Zkontrolujte **jen kontextu**. **Jen kontextu** možnost označuje, že tento uživatel text bude interpretovat _pouze_ Pokud tento parametr zadaný v reakci na předchozí otázce. V tomto scénáři text výzvy nemá žádné smysl jako samostatné otázku, pouze má smysl v kontextu předchozí otázce.

1. Vyberte **Uložit**. 

    To vytvoří novou otázku a propojí dotaz jako zpracování Otázka výzvy k `Give feedback` otázku.
    
    V tomto okamžiku hlavní dotaz má dvě zpracování pokynů líbilo k předchozí otázce `Give feedback`.

    ![! [Hlavní dotaz může v tomto okamžiku dva zpracování pokynů líbilo k předchozí otázce "Váš názor".] (.. / media/conversational-context/all-child-prompts-created.png)](../media/conversational-context/all-child-prompts-created.png#lightbox)

1. Vyberte **uložit a jejich trénování** k trénování znalostní báze pomocí nové dotazy. 

## <a name="add-existing-qna-pair-as-follow-up-prompt"></a>Přidat existující pár QnA zpracování příkazovém řádku

1. Pokud chcete propojit existující dvojice QnA zpracování příkazovém řádku, vyberte řádek pro pár otázek a odpovědí.
1. Vyberte **přidat zpracování řádku** v daném řádku.
1. V **pokračování řádku (PREVIEW)** automaticky otevírané okno, do vyhledávacího pole zadejte text odpovědi. Jsou vráceny všechny shody. Vyberte odpověď jako sledování a zkontrolujte **jen kontextu**a pak vyberte **Uložit**. 

    ![Hledejte řádku následné akce odkaz do dialogového okna odpovědí pro existující odpověď, pomocí textu odpovědi.](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

    Po přidání řádku následné, nezapomeňte vybrat **uložit a jejich trénování**.
  
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

## <a name="test-the-qna-set-to-get-all-the-follow-up-prompts"></a>Vyzve test, který QnA nastavit zobrazíte všechny následné akce

Při testování dotazu s následnou akci výzvy v **testovací** vyberte **povolit více zapnout**a zadejte svůj dotaz. Odpověď obsahuje zpracování pokynů.

![Při testování v testovací podokno dotaz, odpověď obsahuje zpracování pokynů.](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="json-request-to-return-initial-answer-and-follow-up-prompts"></a>JSON požadavek vrátit počáteční odpovědí a následné výzvy

Použít prázdnou `context` objekt zahrnovat zpracování pokynů a požádat o odpověď na otázku uživatele. 

```JSON
{
  "question": "accounts and signing in",
  "top": 30,
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
            "answer": "**Accounts and signing in**\n\nWhen you set up your Surface, an account is set up for you. You can create additional accounts later for family and friends, so each person using your Surface can set it up just the way they like. For more info, see All about accounts on Surface.com. \n\nThere are several ways to sign in to your Surface Pro 4: ",
            "score": 86.96,
            "id": 37,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 38,
                        "qna": null,
                        "displayText": "Use the sign-in screen"
                    },
                    {
                        "displayOrder": 1,
                        "qnaId": 39,
                        "qna": null,
                        "displayText": "Use Windows Hello to sign in"
                    },
                    {
                        "displayOrder": 2,
                        "qnaId": 40,
                        "qna": null,
                        "displayText": "Sign out"
                    }
                ]
            }
        },
        {
            "questions": [
                "Use the sign-in screen"
            ],
            "answer": "**Use the sign-in screen**\n\n1.  \n\nTurn on or wake your Surface by pressing the power button. \n\n2.  \n\nSwipe up on the screen or tap a key on the keyboard. \n\n3.  \n\nIf you see your account name and account picture, enter your password and select the right arrow or press Enter on your keyboard. \n\n4.  \n\nIf you see a different account name, select your own account from the list at the left. Then enter your password and select the right arrow or press Enter on your keyboard. ",
            "score": 32.27,
            "id": 38,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        },
        {
            "questions": [
                "Sign out"
            ],
            "answer": "**Sign out**\n\nHere's how to sign out: \n\n Go to Start , and right-click your name. Then select Sign out. ",
            "score": 27.0,
            "id": 40,
            "source": "surface-pro-4-user-guide-EN .pdf",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": []
            }
        }
    ],
    "debugInfo": null
}
```

`prompts` Pole obsahuje text `displayText` vlastnost a `qnaId` hodnota tak může zobrazit tyto odpovědi jako další možnosti zobrazené v toku konverzace. 

## <a name="json-request-to-return-non-initial-answer-and-follow-up-prompts"></a>JSON požadavek vrátit jiné počáteční odpovědí a zpracování pokynů

Zadejte `context` objektu, který chcete zahrnout předchozí kontext.

V následujícím požadavku JSON je aktuální dotaz `Use Windows Hello to sign in` a byl předchozí otázce `Accounts and signing in`. 

```JSON
{
  "question": "Use Windows Hello to sign in",
  "top": 30,
  "userId": "Default",
  "isTest": false,
  "qnaId": 39,
  "context": {
    "previousQnAId": 37,
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
                "Give feedback"
            ],
            "answer": "What kind of feedback do you have?",
            "score": 100.0,
            "id": 288,
            "source": "Editorial",
            "metadata": [],
            "context": {
                "isContextOnly": true,
                "prompts": [
                    {
                        "displayOrder": 0,
                        "qnaId": 291,
                        "qna": null,
                        "displayText": "Feedback on an QnA Maker service"
                    },
                    {
                        "displayOrder": 0,
                        "qnaId": 292,
                        "qna": null,
                        "displayText": "Feedback on an existing feature"
                    }
                ]
            }
        }
    ]
}
```

## <a name="displaying-prompts-and-sending-context-in-the-client-application"></a>Zobrazení výzvy a odesílání kontextu v klientské aplikaci 

Pokud jste přidali výzvy ve znalostní bázi a otestovali tok v testovací podokno, pokynů automaticky spouštět nebude zobrazovat v klientských aplikacích. Můžete zobrazit pokynů jako doporučené akce nebo tlačítka jako součást odpověď na dotaz uživatele v klientovi aplikace včetně to [Bot Framework ukázka](https://aka.ms/qnamakermultiturnsample) ve vašem kódu. Klientská aplikace uložit aktuální ID QnA a uživatelský dotaz a předat je do [objekt kontextu API GenerateAnswer](#json-request-to-return-non-initial-answer-and-follow-up-prompts) pro další uživatele dotaz.

## <a name="display-order-supported-in-api"></a>Pořadí zobrazení, které jsou podporovány v rozhraní API

Pořadí zobrazení vrácená v odpovědi JSON je podporován pro úpravy pouze rozhraní API. 

## <a name="next-steps"></a>Další postup

Další informace o kontextových konverzace od [dialogové okno ukázkové](https://aka.ms/qnamakermultiturnsample) nebo si přečtěte o [koncepční bot návrh pro více zapnout konverzace](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrace znalostní báze](../Tutorials/migrate-knowledge-base.md)
