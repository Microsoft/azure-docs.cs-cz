---
title: Testování znalostní báze – QnA Maker
titlesuffix: Azure Cognitive Services
description: Testování znalostní báze QnA Maker je důležitou součástí iterativní proces, aby se zlepšila přesnost odpovědi se vrací. Můžete otestovat ve znalostní bázi prostřednictvím rozhraní rozšířené chatu, který také umožňuje že provádět úpravy.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 12/17/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 20ebcb502e03f2d817fe18624d8c790e920c667f
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55861231"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>Testování znalostní báze interaktivně v nástroje QnA Maker

Testování znalostní báze QnA Maker je důležitou součástí iterativní proces, aby se zlepšila přesnost odpovědi se vrací. Můžete otestovat ve znalostní bázi prostřednictvím rozhraní rozšířené chatu, který také umožňuje že provádět úpravy.

## <a name="test-answer-matching"></a>Odpovídající odpověď testu

1.  Výběrem názvu na přístup ke znalostní báze **Moje znalostních bází** stránky.
2.  Chcete-li získat přístup k panelu snímku na více instancí testu, vyberte **testovací** v horním panelu vaší aplikace.

    ![Přístup k panelu testu](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Zadejte dotaz do textového pole a stiskněte Enter.

4.  Nejlépe odpovídající odpověď na základě znalostní báze se vrátí jako odpověď.

## <a name="clear-test-panel"></a>Vymazat test panelu

Chcete-li vymazat všechny zadané testovací dotazy a jejich výsledky z testovací konzole, vyberte **začít od začátku** v levém horním rohu panelu Test.

## <a name="close-test-panel"></a>Test Zavřít panel

Zavřít panel testu, vyberte **testovací** tlačítko znovu. Při otevření panelu Test nelze upravovat obsah znalostní báze Knowledge Base.

## <a name="inspect-score"></a>Kontrola skóre

Si prohlédnout podrobnosti o výsledku testu na panelu zkontrolujte, jestli se.

1.  Test snímku na více instancí panelu otevřete, vyberte **zkontrolujte, jestli se** podrobné informace o této odpovědi.

    ![Zkontrolovat odpovědi](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Otevře se panel kontroly. Na panelu zahrnuje nejvyšší hodnocení záměr, stejně jako všechny zjištěné entity. Na panelu zobrazuje výsledek vybraný utterance.

## <a name="correct-the-top-scoring-answer"></a>Opravte nejvyšší hodnocení odpovědí

Pokud nejvyšší hodnocení odpověď je nesprávná, vyberte ze seznamu a vyberte správnou odpověď **uložit a jejich trénování**.

![Opravte nejvyšší hodnocení odpovědí](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Přidat alternativní otázky

Můžete přidat alternativní formy dotaz na danou odpovědí. Zadejte alternativní odpovědi na textové pole a klikněte na enter je přidat. Vyberte **uložit a jejich trénování** pro ukládání aktualizací.

![Přidat alternativní otázky](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Přidat nové odpovědi

Pokud některý z existujících odpovědi, které se shoda našla, nejsou správné nebo odpověď neexistuje znalostní báze knowledge base (dobré nalezena žádná shoda v KB), můžete přidat nové odpovědi. Zadejte novou odpověď na aktuální otázku do textového pole a stiskněte klávesu enter a přidejte ji. 

Vyberte **uložit a jejich trénování** zachovat tuto odpověď. Nový pár otázka – odpověď byl přidán do znalostní báze.

![Přidat nový pár otázek a odpovědí](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> Všechny úpravy do znalostní báze získat uloží jenom po stisknutí klávesy **uložit a jejich trénování** tlačítko.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Publikování znalostní báze](./publish-knowledge-base.md)
