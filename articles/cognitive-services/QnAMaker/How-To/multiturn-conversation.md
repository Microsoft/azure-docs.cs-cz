---
title: Vícenásobné konverzace – QnA Maker
description: Pomocí výzev a kontextu můžete spravovat více násobných otočení, známých jako vícenásobné, pro vašeho robota z jedné otázky na druhou. Multi-turn je schopnost mít tam a zpět konverzaci, kde předchozí otázka kontext ovlivňuje další otázku a odpověď.
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: 8ef244e1b6baf480189bb90ea5ff53138a6f377a
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261461"
---
# <a name="use-follow-up-prompts-to-create-multiple-turns-of-a-conversation"></a>Použití následných dotazů k vytvoření konverzace s několika směry

Pomocí následných výzev a kontextu můžete spravovat více násobných otáček, označovaných jako _vícenásobné,_ pro vašeho robota z jedné otázky do druhé.

Chcete-li zjistit, jak funguje víceotočení, podívejte se na následující ukázkové video:

[![Vícestranná konverzace v QnA Makeru](../media/conversational-context/youtube-video.png)](https://aka.ms/multiturnexample)

## <a name="what-is-a-multi-turn-conversation"></a>Co je víceobratová konverzace?

Některé otázky nelze zodpovědět v jednom kroku. Při návrhu konverzace klientské aplikace (chat bot) může uživatel položit otázku, která je třeba filtrovat nebo upřesnit k určení správné odpovědi. Tento tok můžete prostřednictvím otázky možné tím, že předloží uživateli *následné-up výzvy*.

Když se uživatel zeptá na otázku, QnA Maker vrátí odpověď _a_ všechny následné výzvy. Tato odpověď umožňuje prezentovat následné otázky jako volby.

> [!CAUTION]
> Vícesměrové výzvy nejsou extrahovány z dokumentů faq. Pokud potřebujete extrakci s více otočeními, odstraňte otazníky, které označují dvojice QnA jako časté otázky.

## <a name="example-multi-turn-conversation-with-chat-bot"></a>Příklad vícestranné konverzace s chatovacím robotem

S multi-turn, chat bot spravuje konverzaci s uživatelem určit konečnou odpověď, jak je znázorněno na následujícím obrázku:

![Vícesměrový dialog s výzvami, které uživatele provedou konverzací](../media/conversational-context/conversation-in-bot.png)

Na předchozím obrázku uživatel zahájil konverzaci zadáním nastavení **můj účet**. Znalostní báze má tři propojené páry otázek a odpovědí. Chcete-li upřesnit odpověď, uživatel vybere jednu ze tří možností ve znalostní bázi. Otázka (#1) má tři následné výzvy, které jsou prezentovány v chatovacím robotu jako tři možnosti (#2).

Když uživatel vybere možnost (#3), zobrazí se další seznam možností zpřesnění (#4). Tato sekvence pokračuje (#5), dokud uživatel neurčí správnou, konečnou odpověď (#6).


### <a name="use-multi-turn-in-a-bot"></a>Použití vícenásobného otočení v robotovi

Po publikování kb, můžete vybrat **tlačítko Vytvořit bot** nasadit QnA Maker bot do služby Azure bot. Výzvy se zobrazí v klientech chatu, které jste povolili pro svého robota.

## <a name="create-a-multi-turn-conversation-from-a-documents-structure"></a>Vytvoření vícestranné konverzace ze struktury dokumentu

Při vytváření znalostní báze se v části **Naplnit kb** zobrazí zaškrtávací políčko **Povolit víceotočení extrakce z adres URL, pdf nebo .docx.**

![Zaškrtávací políčko pro povolení vícenásobné extrakce](../media/conversational-context/enable-multi-turn.png)

Když vyberete tuto možnost, QnA Maker extrahuje hierarchii přítomnou ve struktuře dokumentu. Hierarchie je převedena v zjišnit výzvy a kořen hierarchie slouží jako nadřazené QnA. V některých dokumentech kořen hierarchie nemá obsah, který by mohl sloužit jako odpověď, můžete poskytnout "Výchozí text odpovědi", který má být použit jako náhradní text odpovědi k extrahování těchto hierarchií.

Vícestrannou strukturu lze odvodit pouze z adres URL, souborů PDF nebo souborů DOCX. Příklad struktury naleznete v obrázku [uživatelského souboru PDF](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)aplikace Microsoft Surface .

![! [Příklad struktury v uživatelské příručce] (.. /media/conversational-context/import-file-with-conversational-structure.png)](../media/conversational-context/import-file-with-conversational-structure.png#lightbox)

### <a name="building-your-own-multi-turn-document"></a>Vytvoření vlastního víceúčelového dokumentu

Pokud vytváříte vícenásobný dokument, mějte na paměti následující pokyny:

* K označení hierarchie použijte nadpisy a podnadpisy. Například Můžete h1 označuje nadřazené QnA a h2 označuje QnA, které by měly být brány jako výzva. Malá velikost nadpisu označuje následnou hierarchii. Nepoužívejte styl, barvu nebo jiný mechanismus, který by naznačoval strukturu v dokumentu, QnA Maker nebude extrahovat vícesměrové výzvy.

* První znak nadpisu musí být velkými písmeny.

* Nezakončuj nadpis `?`otazníkem .

* [Ukázkový dokument](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx) můžete použít jako příklad k vytvoření vlastního vícesměrového dokumentu.

### <a name="adding-files-to-a-multi-turn-kb"></a>Přidání souborů do vícesměrové kb

Když přidáte hierarchický dokument, QnA Maker určí výzvy zpracování ze struktury k vytvoření konverzační tok.

1. V qnA makeru vyberte existující znalostní bázi, která byla vytvořena pomocí **možnosti Povolit vícenásobnou extrakci z adres URL, souborů PDF nebo .docx.** Povoleno.
1. Přejděte na stránku **Nastavení,** vyberte soubor nebo adresu URL, kterou chcete přidat.
1. **Uložte a vyškolte** znalostní bázi.

> [!Caution]
> Podpora použití exportovaného víceúčelového souboru znalostní báze TSV nebo XLS jako zdroje dat pro novou nebo prázdnou znalostní bázi není podporována. Chcete-li přidat exportované vícesměrové výzvy do znalostní báze, je třeba **importovat** tento typ souboru ze stránky **Nastavení** portálu QnA Maker.


## <a name="create-knowledge-base-with-multi-turn-prompts-with-the-create-api"></a>Vytvoření znalostní báze s vícenásobnými výzvami pomocí rozhraní Create API

Případ znalostí můžete vytvořit pomocí vícenásobných výzev pomocí [rozhraní QnA Maker Create API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create). Výzvy jsou přidání `context` do `prompts` pole vlastnosti.

## <a name="show-questions-and-answers-with-context"></a>Zobrazení otázek a odpovědí s kontextem

Snižte zobrazené dvojice otázek a odpovědí pouze na ty, které mají kontextové konverzace.

Vyberte **Zobrazit možnosti**a pak vyberte **Zobrazit kontext**. V seznamu jsou zobrazeny dvojice otázek a odpovědí, které obsahují výzvy pro zpracování.

![Filtrování dvojic otázek a odpovědí podle kontextových konverzací](../media/conversational-context/filter-question-and-answers-by-context.png)

Vícesměrový kontext je zobrazen v prvním sloupci.

![! [Sloupec Kontext (PREVIEW)" (.. /media/conversational-context/surface-manual-pdf-follow-up-prompt.png)](../media/conversational-context/surface-manual-pdf-follow-up-prompt.png#lightbox)

V předchozím obrázku **#1** označuje tučný text ve sloupci, což znamená aktuální otázku. Nadřazená otázka je horní položka v řádku. Všechny otázky níže jsou propojené páry otázek a odpovědí. Tyto položky lze vybrat, takže můžete okamžitě přejít na ostatní položky kontextu.

## <a name="add-an-existing-question-and-answer-pair-as-a-follow-up-prompt"></a>Přidání existující dvojice otázek a odpovědí jako výzvy pro zpracování

Původní otázka **Můj účet**má výzvy ke zpracování, například **Účty a přihlášení**.

![Odpovědi na "Účty a přihlášení" a výzvy k následným opatřením](../media/conversational-context/detected-and-linked-follow-up-prompts.png)

Přidejte výzvu pro zpracování existující dvojice otázek a odpovědí, která není aktuálně propojena. Vzhledem k tomu, že otázka není propojena s žádným párem otázek a odpovědí, je třeba změnit aktuální nastavení zobrazení.

1. Chcete-li propojit existující dvojici otázek a odpovědí jako výzvu pro zpracování, vyberte řádek pro dvojici otázek a odpovědí. V příručce pro Surface **vyhledejte možnost Odhlásit** se a seznam zmenšit.
1. Na řádku pro **Odhlášení**vyberte ve sloupci **Odpověď** možnost **Přidat výzvu pro zpracování**.
1. Do polí v rozbalovacím okně **výzvy k zpracování** zadejte následující hodnoty:

    |Pole|Hodnota|
    |--|--|
    |Zobrazený text|Zadejte **Vypnout zařízení**. Toto je vlastní text, který se má zobrazit v následné výzvě.|
    |Pouze kontext| Zaškrtněte toto políčko. Odpověď je vrácena pouze v případě, že otázka určuje kontext.|
    |Odkaz na odpověď|**Zadejte: Pomocí přihlašovací obrazovky** vyhledejte existující dvojici otázek a odpovědí.|


1.  Je vrácena jedna shoda. Tuto odpověď vyberte jako zpracování a pak vyberte **Uložit**.

    ![Stránka "Výzva ke zpracování (PREVIEW)"](../media/conversational-context/search-follow-up-prompt-for-existing-answer.png)

1. Po přidání výzvy pro zpracování vyberte uložit **a trénovat** v horní navigaci.

### <a name="edit-the-display-text"></a>Úprava textu zobrazení

Po vytvoření výzvy pro zpracování a zazadání se jako odkaz **na odpověď**existující dvojice otázek a odpovědí , můžete zadat nový **obsahový text**. Tento text nenahrazuje existující otázku a nepřidává novou alternativní otázku. Je oddělenod těchto hodnot.

1. Chcete-li upravit zobrazovaný text, vyhledejte a vyberte otázku v poli **Kontext.**
1. V řádku pro tuto otázku vyberte výzvu pro zpracování ve sloupci odpovědi.
1. Vyberte zobrazovaný text, který chcete upravit, a pak vyberte **Upravit**.

    ![Příkaz Upravit pro zobrazovaný text](../media/conversational-context/edit-existing-display-text.png)

1. V rozbalovacím okně **s výzvou k zpracování** změňte existující obsahový text.
1. Po dokončení úprav zobrazeného textu vyberte **Uložit**.
1. Na horním navigačním panelu **je Uložit a trénovat**.


## <a name="add-a-new-question-and-answer-pair-as-a-follow-up-prompt"></a>Přidání nové dvojice otázek a odpovědí jako výzvy pro zpracování

Přidáte-li do znalostní báze nový pár otázek a odpovědí, měla by být každá dvojice propojena s existující otázkou jako výzva pro zpracování.

1. Na panelu nástrojů znalostní báze vyhledejte a vyberte existující dvojici otázek a odpovědí pro **účty a přihlášení**.

1. Ve sloupci **Odpověď** pro tuto otázku vyberte **Přidat výzvu k zpracování**.
1. V části **Výzva pro zpracování (PREVIEW)** vytvořte novou výzvu pro zpracování zadáním následujících hodnot:

    |Pole|Hodnota|
    |--|--|
    |Zobrazený text|*Vytvořte účet systému Windows*. Vlastní text, který se má zobrazit v následné výzvě.|
    |Pouze kontext|Zaškrtněte toto políčko. Tato odpověď je vrácena pouze v případě, že otázka určuje kontext.|
    |Odkaz na odpověď|Jako odpověď zadejte následující text:<br>* [Vytvořte](https://account.microsoft.com/) si účet systému Windows s novým nebo existujícím e-mailovým účtem*.<br>Při uložení a trénování databáze bude tento text převeden. |
    |||

    ![Vytvoření nové otázky a odpovědi na výzvu](../media/conversational-context/create-child-prompt-from-parent.png)


1. Vyberte **Vytvořit nový**a pak vyberte **Uložit**.

    Tato akce vytvoří nový pár otázek a odpovědí a propojí vybranou otázku jako výzvu pro zpracování. **Sloupec Kontext** pro obě otázky označuje vztah výzvy pro zpracování.

1. Vyberte **Zobrazit volby**a pak vyberte [**Zobrazit kontext (PREVIEW).**](#show-questions-and-answers-with-context)

    Nová otázka ukazuje, jak je propojena.

    ![Vytvoření nové výzvy pro zpracování](../media/conversational-context/new-qna-follow-up-prompt.png)

    Nadřazená otázka zobrazí novou otázku jako jednu z jejích možností.

    ![! [Sloupec Kontext pro obě otázky označuje vztah výzvy pro zpracování] (.. /media/conversational-context/child-prompt-created.png)](../media/conversational-context/child-prompt-created.png#lightbox)

1. Po přidání výzvy pro zpracování vyberte uložit **a trénovat** na horním navigačním panelu.

<a name="enable-multi-turn-during-testing-of-follow-up-prompts"></a>

## <a name="view-multi-turn-during-testing-of-follow-up-prompts"></a>Zobrazit vícenásobné otočení během testování následných výzev

Při testování otázky s výzvami pro zpracování v podokně **test** odpověď obsahuje výzvy pro zpracování.

![Odpověď zahrnuje následné výzvy](../media/conversational-context/test-pane-with-question-having-follow-up-prompts.png)

## <a name="a-json-request-to-return-an-initial-answer-and-follow-up-prompts"></a>Žádost JSON o vrácení počáteční odpovědi a následných výzev

Pomocí prázdného `context` objektu můžete požádat o odpověď na otázku uživatele a zahrnout výzvy pro zpracování.

```JSON
{
  "question": "accounts and signing in",
  "top": 10,
  "userId": "Default",
  "isTest": false,
  "context": {}
}
```

## <a name="a-json-response-to-return-an-initial-answer-and-follow-up-prompts"></a>Odpověď JSON pro vrácení počáteční odpovědi a následných výzev

Předchozí část požadovala odpověď a všechny výzvy k zpracování **účtů a přihlášení**. Odpověď obsahuje informace výzvy, které jsou umístěny v *kontextu odpovědí[0].* a text, který se má uživateli zobrazit.

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

Pole `prompts` poskytuje text `displayText` ve vlastnosti a hodnotu. `qnaId` Tyto odpovědi můžete zobrazit jako další zobrazené volby v `qnaId` toku konverzace a potom odeslat vybrané zpět do QnA Maker v následující žádosti.

<!--

The `promptsToDelete` array provides the ...

-->

## <a name="a-json-request-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Žádost JSON o vrácení nepočáteční odpovědi a následných výzev

Vyplňte `context` objekt, aby zahrnoval předchozí kontext.

V následujícím požadavku JSON je aktuální otázkou *přihlásit se pomocí programu Windows Hello* a předchozí otázkou byly účty a *přihlášení*.

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

##  <a name="a-json-response-to-return-a-non-initial-answer-and-follow-up-prompts"></a>Odpověď JSON pro vrácení nepočáteční odpovědi a následných výzev

QnA Maker _GenerateAnswer_ JSON odpověď obsahuje výzvy `context` zpracování ve vlastnosti `answers` první položky v objektu:

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

## <a name="query-the-knowledge-base-with-the-qna-maker-id"></a>Dotaz na znalostní bázi pomocí ID výrobce QnA Maker

Pokud vytváříte vlastní aplikaci pomocí funkce více otočení. V odpovědi na počáteční otázku jsou vráceny všechny `qnaId` výzvy pro zpracování a její přidružené. Nyní, když máte ID, můžete předat to v textu požadavku následné výzvy. Pokud tělo požadavku `qnaId`obsahuje , a kontextový objekt (který obsahuje předchozí qnA maker vlastnosti), pak GenerateAnswer vrátí přesnou otázku podle ID, namísto použití algoritmu řazení najít odpověď text emitovaného.


## <a name="display-order-is-supported-in-the-update-api"></a>Pořadí zobrazení je podporováno v rozhraní UPDATE API

[Zobrazení textu a pořadí zobrazení](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#promptdto), vrácené v odpovědi JSON, je podporováno pro úpravy [rozhraním UPDATE API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update).

## <a name="add-or-delete-multi-turn-prompts-with-the-update-api"></a>Přidání nebo odstranění vícenásobných výzev pomocí rozhraní UPDATE API

Pomocí rozhraní [QnA Maker Update API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update)můžete přidat nebo odstranit výzvy s více otočeními .  Výzvy jsou přidání `context` v `promptsToAdd` poli vlastnosti `promptsToDelete` a pole.

## <a name="export-knowledge-base-for-version-control"></a>Export znalostní báze pro správu verzí

QnA Maker podporuje správu verzí zahrnutím víceobratných konverzačních kroků do exportovaného souboru.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o kontextových konverzacích z této [ukázky dialogového okna](https://aka.ms/qnamakermultiturnsample) nebo další informace o [koncepčním návrhu robota pro víceobratné konverzace](https://docs.microsoft.com/azure/bot-service/bot-builder-conversations?view=azure-bot-service-4.0).

> [!div class="nextstepaction"]
> [Migrace znalostní báze](../Tutorials/migrate-knowledge-base.md)
