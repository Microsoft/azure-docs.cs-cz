---
title: Testování znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Testování znalostní báze QnA Maker je důležitou součástí iterativní proces, aby se zlepšila přesnost odpovědi se vrací. Můžete otestovat ve znalostní bázi prostřednictvím rozhraní rozšířené chatu, který také umožňuje že provádět úpravy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 2b9c6f96cc351831bb784bb89db1bce76b01190b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68966672"
---
# <a name="test-your-knowledge-base-interactively-in-qna-maker"></a>Testování znalostní báze interaktivně v nástroje QnA Maker

Testování znalostní báze QnA Maker je důležitou součástí iterativní proces, aby se zlepšila přesnost odpovědi se vrací. Můžete otestovat ve znalostní bázi prostřednictvím rozhraní rozšířené chatu, který také umožňuje že provádět úpravy.

## <a name="test-answer-matching"></a>Odpovídající odpověď testu

1. K znalostní bázi se dostanete tak, že na stránce **Moje základy znalostí** vyberete její název.
1. Pro přístup k panelu testovacího snímku vyberte v horním panelu aplikace **test** .
1. Zadejte dotaz do textového pole a stiskněte Enter.
1. Nejlépe odpovídající odpověď na základě znalostní báze se vrátí jako odpověď.

## <a name="clear-test-panel"></a>Vymazat test panelu

Chcete-li vymazat všechny zadané testovací dotazy a jejich výsledky z konzoly test, vyberte možnost **začít znovu** v levém horním rohu panelu Test.

## <a name="close-test-panel"></a>Test Zavřít panel

Chcete-li panel testu zavřít, vyberte tlačítko **test** znovu. Při otevření panelu Test nelze upravovat obsah znalostní báze Knowledge Base.

## <a name="inspect-score"></a>Kontrola skóre

Podrobnosti o výsledku testu si můžete prohlédnout na panelu Kontrola.

1.  Na panelu Test snímků na více instancí otevřete možnost **zkontrolovat** , kde najdete další podrobnosti o této odpovědi.

    ![Zkontrolovat odpovědi](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Zobrazí se panel kontrola. Na panelu zahrnuje nejvyšší hodnocení záměr, stejně jako všechny zjištěné entity. Na panelu zobrazuje výsledek vybraný utterance.

## <a name="correct-the-top-scoring-answer"></a>Opravte nejvyšší hodnocení odpovědí

Pokud nejvyšší hodnocení odpověď je nesprávná, vyberte ze seznamu a vyberte správnou odpověď **uložit a jejich trénování**.

![Opravte nejvyšší hodnocení odpovědí](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Přidat alternativní otázky

Můžete přidat alternativní formy dotaz na danou odpovědí. Zadejte alternativní odpovědi na textové pole a klikněte na enter je přidat. Vyberte **uložit a jejich trénování** pro ukládání aktualizací.

![Přidat alternativní otázky](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Přidat nové odpovědi

Pokud některý z existujících odpovědi, které se shoda našla, nejsou správné nebo odpověď neexistuje znalostní báze knowledge base (dobré nalezena žádná shoda v KB), můžete přidat nové odpovědi. 

V dolní části seznamu odpovědí zadejte novou odpověď pomocí textového pole a stisknutím klávesy ENTER ji přidejte. 

Vyberte **uložit a jejich trénování** zachovat tuto odpověď. Nový pár otázka – odpověď byl přidán do znalostní báze. 

> [!NOTE]
> Všechny úpravy do znalostní báze získat uloží jenom po stisknutí klávesy **uložit a jejich trénování** tlačítko.

## <a name="test-the-published-knowledge-base"></a>Testování publikované znalostní báze

Publikovanou verzi znalostní báze můžete otestovat v podokně test. Po publikování znalostní báze vyberte pole **zveřejněné znalostní báze** a odešlete dotaz, který získá výsledky z PUBLIKOVANÉHO znalostní báze.

![Test proti publikované znalostní bázi](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Publikování znalostní báze](./publish-knowledge-base.md)
