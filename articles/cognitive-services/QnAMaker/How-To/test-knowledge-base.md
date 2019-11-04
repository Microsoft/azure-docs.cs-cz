---
title: Postup testování znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Testování QnA Maker znalostní báze je důležitou součástí iterativního procesu, který vylepšuje přesnost vrácených odpovědí. Znalostní bázi můžete testovat prostřednictvím vylepšeného rozhraní chatu, které také umožňuje provádět úpravy.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 64b4b9a4ad5ceb0b3c33ae022b34daeafda93a62
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491207"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Otestujte znalostní bázi v QnA Maker

Testování QnA Maker znalostní báze je důležitou součástí iterativního procesu, který vylepšuje přesnost vrácených odpovědí. Znalostní bázi můžete testovat prostřednictvím vylepšeného rozhraní chatu, které také umožňuje provádět úpravy.

## <a name="interactively-test-in-qna-maker-portal"></a>Interaktivní testování na portálu QnA Maker

1. K znalostní bázi se dostanete tak, že na stránce **Moje základy znalostí** vyberete její název.
1. Pro přístup k panelu testovacího snímku vyberte v horním panelu aplikace **test** .
1. Do textového pole zadejte dotaz a vyberte Enter.
1. Nejvhodnější odpověď ze znalostní báze se vrátí jako odpověď.

### <a name="clear-test-panel"></a>Vymazat testovací panel

Chcete-li vymazat všechny zadané testovací dotazy a jejich výsledky z konzoly test, vyberte možnost **začít znovu** v levém horním rohu panelu Test.

### <a name="close-test-panel"></a>Zavřít testovací panel

Chcete-li panel testu zavřít, vyberte tlačítko **test** znovu. I když je panel test otevřený, nelze upravit obsah znalostní báze.

### <a name="inspect-score"></a>Zkontrolovat skóre

Podrobnosti o výsledku testu si můžete prohlédnout na panelu Kontrola.

1.  Na panelu Test snímků na více instancí otevřete možnost **zkontrolovat** , kde najdete další podrobnosti o této odpovědi.

    ![Kontrola odpovědí](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Zobrazí se panel kontrola. Panel zahrnuje nejvyšší záměr hodnocení i všechny identifikované entity. Na panelu se zobrazí výsledek vybrané utterance.

### <a name="correct-the-top-scoring-answer"></a>Opravte odpověď na nejvyšší hodnocení.

Pokud je odpověď na nejvyšší vyhodnocování nesprávná, vyberte správnou odpověď ze seznamu a vyberte **Uložit a výuka**.

![Opravte odpověď na nejvyšší hodnocení.](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Přidat alternativní otázky

K dané odpovědi můžete přidat alternativní formy otázky. Do textového pole zadejte alternativní odpovědi a kliknutím na tlačítko ENTER je přidejte. Vyberte **Uložit a výuka** pro uložení aktualizací.

![Přidat alternativní otázky](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Přidat novou odpověď

Můžete přidat novou odpověď, pokud některé ze stávajících odpovědí, které se shodují, nejsou správné nebo odpověď ve znalostní bázi neexistuje (v KB) nebyla nalezena žádná dobrá shoda. 

V dolní části seznamu odpovědí zadejte novou odpověď pomocí textového pole a stisknutím klávesy ENTER ji přidejte. 

Vyberte **Save (Uložit) a výuka a** zachovejte tuto odpověď. Do znalostní báze se teď přidala nová dvojice otázek a odpovědí. 

> [!NOTE]
> Všechny úpravy ve znalostní bázi se budou ukládat jenom při stisknutí tlačítka **Uložit a výuka** .

### <a name="test-the-published-knowledge-base"></a>Testování publikované znalostní báze

Publikovanou verzi znalostní báze můžete otestovat v podokně test. Po publikování znalostní báze vyberte pole **zveřejněné znalostní báze** a odešlete dotaz, který získá výsledky z PUBLIKOVANÉHO znalostní báze.

![Test proti publikované znalostní bázi](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Batch test s nástrojem

Nástroj Batch test použijte, když chcete:

* určení nejlepší odpovědi a skóre pro sadu otázek
* ověřit očekávanou odpověď pro sadu otázek

Dávkové testování se poskytuje pomocí nástroje Batch Testing. Tento nástroj je k dispozici jako [spustitelný soubor zip](https://qnamakerstore.blob.core.windows.net/qnamakerdata/batchtesting/bt.zip) ke stažení nebo jako [ C# zdrojový kód](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting). 

[Referenční dokumentace k nástroji](../reference-tsv-format-batch-testing.md) zahrnuje:

* příklad příkazového řádku nástroje
* formát pro vstupní a výstupní soubory TSV 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Publikování znalostní báze](./publish-knowledge-base.md)
