---
title: Testování znalostní báze – QnA Maker
description: Testování znalostní báze QnA Maker je důležitou součástí iterativní proces, aby se zlepšila přesnost odpovědi se vrací. Můžete otestovat ve znalostní bázi prostřednictvím rozhraní rozšířené chatu, který také umožňuje že provádět úpravy.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 9cbd5b790299f080a64bab332bb031543c2de160
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2020
ms.locfileid: "78927277"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Otestujte znalostní bázi v QnA Maker

Testování znalostní báze QnA Maker je důležitou součástí iterativní proces, aby se zlepšila přesnost odpovědi se vrací. Můžete otestovat ve znalostní bázi prostřednictvím rozhraní rozšířené chatu, který také umožňuje že provádět úpravy.

## <a name="interactively-test-in-qna-maker-portal"></a>Interaktivní testování na portálu QnA Maker

1. K znalostní bázi se dostanete tak, že na stránce **Moje základy znalostí** vyberete její název.
1. Pro přístup k panelu testovacího snímku vyberte v horním panelu aplikace **test** .
1. Zadejte dotaz do textového pole a stiskněte Enter.
1. Nejlépe odpovídající odpověď na základě znalostní báze se vrátí jako odpověď.

### <a name="clear-test-panel"></a>Vymazat test panelu

Chcete-li vymazat všechny zadané testovací dotazy a jejich výsledky z konzoly test, vyberte možnost **začít znovu** v levém horním rohu panelu Test.

### <a name="close-test-panel"></a>Test Zavřít panel

Chcete-li panel testu zavřít, vyberte tlačítko **test** znovu. Při otevření panelu Test nelze upravovat obsah znalostní báze Knowledge Base.

### <a name="inspect-score"></a>Kontrola skóre

Podrobnosti o výsledku testu si můžete prohlédnout na panelu Kontrola.

1.  Na panelu Test snímků na více instancí otevřete možnost **zkontrolovat** , kde najdete další podrobnosti o této odpovědi.

    ![Zkontrolovat odpovědi](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Zobrazí se panel kontrola. Na panelu zahrnuje nejvyšší hodnocení záměr, stejně jako všechny zjištěné entity. Na panelu zobrazuje výsledek vybraný utterance.

### <a name="correct-the-top-scoring-answer"></a>Opravte nejvyšší hodnocení odpovědí

Pokud je odpověď na nejvyšší vyhodnocování nesprávná, vyberte správnou odpověď ze seznamu a vyberte **Uložit a výuka**.

![Opravte nejvyšší hodnocení odpovědí](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Přidat alternativní otázky

Můžete přidat alternativní formy dotaz na danou odpovědí. Zadejte alternativní odpovědi na textové pole a klikněte na enter je přidat. Vyberte **Uložit a výuka** pro uložení aktualizací.

![Přidat alternativní otázky](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Přidat nové odpovědi

Pokud některý z existujících odpovědi, které se shoda našla, nejsou správné nebo odpověď neexistuje znalostní báze knowledge base (dobré nalezena žádná shoda v KB), můžete přidat nové odpovědi.

V dolní části seznamu odpovědí zadejte novou odpověď pomocí textového pole a stisknutím klávesy ENTER ji přidejte.

Vyberte **Save (Uložit) a výuka a** zachovejte tuto odpověď. Nový pár otázka – odpověď byl přidán do znalostní báze.

> [!NOTE]
> Všechny úpravy ve znalostní bázi se budou ukládat jenom při stisknutí tlačítka **Uložit a výuka** .

### <a name="test-the-published-knowledge-base"></a>Testování publikované znalostní báze

Publikovanou verzi znalostní báze můžete otestovat v podokně test. Po publikování znalostní báze vyberte pole **zveřejněné znalostní báze** a odešlete dotaz, který získá výsledky z PUBLIKOVANÉHO znalostní báze.

![Test proti publikované znalostní bázi](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Batch test s nástrojem

Nástroj Batch test použijte, když chcete:

* určení nejlepší odpovědi a skóre pro sadu otázek
* ověřit očekávanou odpověď pro sadu otázek

Podrobné pokyny najdete v [kurzu](../Quickstarts/batch-testing.md) dávkového testování.

Dávkové testování se poskytuje pomocí nástroje Batch Testing. Tento nástroj je k dispozici jako [spustitelný soubor zip](https://aka.ms/qnamakerbatchtestingtool) ke stažení nebo jako [ C# zdrojový kód](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting).

[Referenční dokumentace k nástroji](../reference-tsv-format-batch-testing.md) zahrnuje:

* příklad příkazového řádku nástroje
* formát pro vstupní a výstupní soubory TSV

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Publikování znalostní báze](./publish-knowledge-base.md)
