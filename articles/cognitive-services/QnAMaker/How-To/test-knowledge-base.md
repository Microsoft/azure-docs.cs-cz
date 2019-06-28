---
title: Testování znalostní báze – QnA Maker
titlesuffix: Azure Cognitive Services
description: Testování znalostní báze QnA Maker je důležitou součástí iterativní proces, aby se zlepšila přesnost odpovědi se vrací. Můžete otestovat ve znalostní bázi prostřednictvím rozhraní rozšířené chatu, který také umožňuje že provádět úpravy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 4d9c00c4ea7fd0494d00551dc37b186e1a357037
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67439726"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>Testování znalostní báze interaktivně v nástroje QnA Maker

Testování znalostní báze QnA Maker je důležitou součástí iterativní proces, aby se zlepšila přesnost odpovědi se vrací. Můžete otestovat ve znalostní bázi prostřednictvím rozhraní rozšířené chatu, který také umožňuje že provádět úpravy.

## <a name="test-answer-matching"></a>Odpovídající odpověď testu

1. Výběrem názvu na přístup ke znalostní báze **Moje znalostních bází** stránky.
1. Chcete-li získat přístup k panelu snímku na více instancí testu, vyberte **Test** v horním panelu vaší aplikace.
1. Zadejte dotaz do textového pole a stiskněte Enter.
1. Nejlépe odpovídající odpověď na základě znalostní báze se vrátí jako odpověď.

## <a name="clear-test-panel"></a>Vymazat test panelu

Chcete-li vymazat všechny zadané testovací dotazy a jejich výsledky z testovací konzole, vyberte **začít od začátku** v levém horním rohu panelu Test.

## <a name="close-test-panel"></a>Test Zavřít panel

Zavřít panel testu, vyberte **Test** tlačítko znovu. Při otevření panelu Test nelze upravovat obsah znalostní báze Knowledge Base.

## <a name="inspect-score"></a>Kontrola skóre

Si prohlédnout podrobnosti o výsledku testu na panelu zkontrolujte, jestli se.

1.  Test snímku na více instancí panelu otevřete, vyberte **zkontrolujte, jestli se** podrobné informace o této odpovědi.

    ![Zkontrolovat odpovědi](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Otevře se panel kontroly. Na panelu zahrnuje nejvyšší hodnocení záměr, stejně jako všechny zjištěné entity. Na panelu zobrazuje výsledek vybraný utterance.

## <a name="correct-the-top-scoring-answer"></a>Opravte nejvyšší hodnocení odpovědí

Pokud nejvyšší hodnocení odpověď je nesprávná, vyberte ze seznamu a vyberte správnou odpověď **uložit a jejich trénování**.

![Opravte nejvyšší hodnocení odpovědí](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Přidat alternativní otázky

Můžete přidat alternativní formy dotaz na danou odpovědí. Zadejte alternativní odpovědi na textové pole a klikněte na enter je přidat. Vyberte **uložit a jejich trénování** pro ukládání aktualizací.

![Přidat alternativní otázky](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Přidat nové odpovědi

Pokud některý z existujících odpovědi, které se shoda našla, nejsou správné nebo odpověď neexistuje znalostní báze knowledge base (dobré nalezena žádná shoda v KB), můžete přidat nové odpovědi. 

V dolní části seznamu odpovědi použijte textové pole k zadání odpovědi na nový a stiskněte klávesu enter a přidejte ji. 

Vyberte **uložit a jejich trénování** zachovat tuto odpověď. Nový pár otázka – odpověď byl přidán do znalostní báze. 

> [!NOTE]
> Všechny úpravy do znalostní báze získat uloží jenom po stisknutí klávesy **uložit a jejich trénování** tlačítko.

## <a name="test-the-published-knowledge-base"></a>Testování publikované znalostní báze

Testování publikované verze znalostní báze knowledge base v testovací podokno. Po publikování KB, vyberte **publikované KB** pole a odeslat dotaz pro získání výsledků z publikovaných KB.

![Testování publikované KB](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Publikování znalostní báze](./publish-knowledge-base.md)
