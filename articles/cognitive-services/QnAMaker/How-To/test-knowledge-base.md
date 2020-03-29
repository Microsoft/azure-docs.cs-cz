---
title: Jak otestovat znalostní bázi - QnA Maker
description: Testování znalostní báze QnA Maker je důležitou součástí iterativního procesu ke zlepšení přesnosti vrácených odpovědí. Znalostní bázi můžete otestovat prostřednictvím vylepšeného rozhraní chatu, které také umožňuje provádět úpravy.
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: da4988ced0b077952ce64e6227d16e58d40ae329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927277"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Otestujte svou znalostní bázi v QnA Makeru

Testování znalostní báze QnA Maker je důležitou součástí iterativního procesu ke zlepšení přesnosti vrácených odpovědí. Znalostní bázi můžete otestovat prostřednictvím vylepšeného rozhraní chatu, které také umožňuje provádět úpravy.

## <a name="interactively-test-in-qna-maker-portal"></a>Interaktivní testování na portálu QnA Maker

1. Přístup ke své znalostní bázi výběrem jejího názvu na stránce **Moje znalostní báze.**
1. Chcete-li získat přístup k panelu Testovat vysunutí, vyberte **testovat** v horním panelu aplikace.
1. Zadejte dotaz do textového pole a vyberte Enter.
1. Jako odpověď je vrácena odpověď s nejlepší odpovídající odpovědi ze znalostní báze.

### <a name="clear-test-panel"></a>Vymazat testovací panel

Chcete-li vymazat všechny zadané testovací dotazy a jejich výsledky z testovací konzoly, vyberte **spustit znovu** v levém horním rohu testovacího panelu.

### <a name="close-test-panel"></a>Zavření testovacího panelu

Chcete-li panel Test zavřít, znovu vyberte tlačítko **Testovat.** Když je panel Test otevřený, nelze upravovat obsah znalostní báze Knowledge Base.

### <a name="inspect-score"></a>Zkontrolovat skóre

Podrobnosti o výsledku testu zkontrolujte v panelu Kontrola.

1.  Když je otevřený panel Testovat vysunutí, vyberte **Zkontrolovat** další podrobnosti o této odpovědi.

    ![Kontrola odpovědí](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Zobrazí se kontrolní panel. Panel obsahuje hlavní záměr hodnocení, stejně jako všechny identifikované entity. Panel zobrazuje výsledek vybrané utterance.

### <a name="correct-the-top-scoring-answer"></a>Opravit nejlepší bodovací odpověď

Pokud je odpověď nejvyššího hodnocení nesprávná, vyberte správnou odpověď ze seznamu a vyberte **Uložit a trénovat**.

![Opravit nejlepší bodovací odpověď](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Přidání alternativních otázek

K dané odpovědi můžete přidat alternativní formy otázky. Do textového pole zadejte alternativní odpovědi a kliknutím na Enter je přidejte. Chcete-li uložit **a trénovat,** vyberte uložit a trénovat aktualizace.

![Přidání alternativních otázek](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Přidání nové odpovědi

Novou odpověď můžete přidat, pokud jsou některé z existujících odpovědí, které byly spárovány, nesprávné nebo odpověď neexistuje ve znalostní bázi (žádná dobrá shoda nalezena v KB).

V dolní části seznamu odpovědí zadejte novou odpověď pomocí textového pole a stisknutím klávesy Enter ji přidejte.

Chcete-li tuto odpověď zachovat, vyberte **možnost Uložit a Trénovat.** Do znalostní báze byla nyní přidána nová dvojice odpovědí na otázky.

> [!NOTE]
> Všechny úpravy vaší znalostní báze se uloží pouze po stisknutí tlačítka **Uložit a Vlak.**

### <a name="test-the-published-knowledge-base"></a>Testování publikované znalostní báze

Publikovanou verzi znalostní báze můžete otestovat v testovacím podokně. Po publikování KB vyberte **políčko Publikovaná kb** a odešlete dotaz pro získání výsledků z publikované KB.

![Test proti publikované KB](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Dávkový test s nástrojem

Dávkové testování použijte, pokud chcete:

* určit nejvyšší odpověď a skóre pro sadu otázek
* ověřit očekávanou odpověď na sadu otázek

Přečtěte si [kurz](../Quickstarts/batch-testing.md) testování dávky podrobné pokyny.

Dávkové testování je dodáváno s dávkovým testovacím nástrojem. Tento nástroj je k dispozici jako [zip spustitelný soubor](https://aka.ms/qnamakerbatchtestingtool) ke stažení nebo jako [c# zdrojový kód](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/batchtesting).

[Referenční dokumentace k nástroji](../reference-tsv-format-batch-testing.md) obsahuje:

* příklad příkazového řádku nástroje
* formát pro vstupní a outfile soubory TSV

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Publikování znalostní báze](./publish-knowledge-base.md)
