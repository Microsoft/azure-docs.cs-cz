---
title: Postup testování znalostní báze knowledge base - QnA Maker - Azure kognitivní Services | Microsoft Docs
description: Před publikováním otestujte znalostní báze.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: cffb63666edab25e1b3b0739d0e0f2f828600f3a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343269"
---
# <a name="test-your-knowledge-base"></a>Testování znalostní báze

Testování QnA Maker znalostní báze je důležitou součástí iterativní proces k zajištění přesnosti odpovědí nebyl vrácen. Můžete otestovat ve znalostní bázi knowledge prostřednictvím chatu rozšířené rozhraní, které také umožňuje že provádět úpravy.

## <a name="test-answer-matching"></a>Testování odpovídající odpovědí

1.  Výběrem jeho název na přístup k vaší znalostní báze knowledge base **Moje znalostních bází** stránky.
2.  Chcete-li získat přístup k panelu testu snímku na více systémů, vyberte **Test** v horním panelu vaší aplikace.

    ![Test přístupu](../media/qnamaker-how-to-test-kb/access-test.png)

3.  Zadejte dotaz do textového pole a vyberte Enter.

4.  Nejlépe odpovídající odpověď ze znalostní báze se vrátí jako odpověď.

## <a name="clear-test-panel"></a>Zrušte testovací panely

Pokud chcete vymazat všechny zadané testovací dotazy a jejich výsledky z konzoly pro testovací, vyberte **začít znovu** v levém horním rohu panelu testu.

## <a name="close-test-panel"></a>Zavřete testovací panely

Zavřete testovací panelu, vyberte **Test** tlačítko znovu. Panel testu je otevřen, nelze upravovat obsah znalostní báze Knowledge Base.

## <a name="inspect-score"></a>Zkontrolujte skóre

Si prohlédnout podrobnosti výsledků testů v panelu kontroly.

1.  Test se snímku na panelu otevřete, vybrat **kontroly** další podrobnosti o této odpovědi.

    ![Kontrola odpovědí](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Zobrazí se panel kontroly. Na panelu zahrnuje horní vyhodnocování záměr, jakož i všechny zjištěné entity. Panel zobrazuje výsledek vybrané utterance.

## <a name="correct-the-top-scoring-answer"></a>Opravte horní vyhodnocování odpovědí

Pokud horní vyhodnocování odpověď není v pořádku, vyberte ze seznamu a vyberte správnou odpověď **uložte a cvičení**.

![Test přístupu](../media/qnamaker-how-to-test-kb/choose-answer.png)

## <a name="add-alternate-questions"></a>Přidat alternativní otázky

Můžete přidat alternativní formy dotaz na danou odpovědí. Typ odpovědi alternativní textového pole a klikněte na, zadejte je přidat. Vyberte **uložte a cvičení** pro ukládání aktualizací.

![Test přístupu](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

## <a name="add-a-new-answer"></a>Přidat novou odpověď

Pokud žádné z existující odpovědi, které se shodují jsou nesprávné nebo odpověď neexistuje ve znalostní bázi knowledge base (dobrý nalezena žádná shoda v KB), můžete přidat novou odpověď. Zadejte novou odpověď na otázku aktuální do textového pole a stiskněte klávesu enter ho přidejte. 

Vyberte **uložte a cvičení** udržení této odpovědi. Zadat nový pár otázku odpovědí teď přidaná do znalostní báze.

![Test přístupu](../media/qnamaker-how-to-test-kb/add-answer.png)

> [!NOTE]
> Všechny úpravy do znalostní báze získat uloží jenom po stisknutí klávesy **uložte a cvičení** tlačítko.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Publikování znalostní báze](./publish-knowledge-base.md)
