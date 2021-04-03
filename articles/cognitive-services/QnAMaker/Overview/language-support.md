---
title: Jazyková podpora – QnA Maker
titleSuffix: Azure Cognitive Services
description: Seznam jazykové verze, přirozené jazyky, které QnA Maker podporuje ve znalostní bázi. Nemíchejte jazyky ve stejné znalostní bázi.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/09/2019
ms.openlocfilehash: 1edd5ffc2578a27a53c7e9a46a4a5f1cf61331ff
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "97605055"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Jazyková podpora pro QnA Maker prostředky a základy znalostní báze

Tento článek popisuje možnosti podpory jazyků pro QnA Maker prostředky a základy znalostní báze. 

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Při vytváření první znalostní báze v prostředku je vybraný jazyk pro službu. Všechny další databáze znalostí v prostředku musí být ve stejném jazyce. 

Jazyk určuje relevanci výsledků QnA Maker poskytuje odpověď na dotazy uživatelů. Prostředek QnA Maker a všechny znalostní báze uvnitř tohoto prostředku podporují jeden jazyk. Jeden jazyk je nutný k tomu, aby poskytoval nejlepší výsledky odpovědi pro dotaz.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)

V QnA Maker spravovaném se dostanete k tomu, abyste mohli nastavit jazykové nastavení na úrovni konkrétní znalostní báze. Toto nastavení lze povolit pouze se službou Knowledge Base služby. Po nastavení nelze pro službu změnit nastavení jazyka. 

Pokud vyberete nastavení jazyka pro konkrétní znalostní bázi Knowledge Base, budete mít povoleno vytvářet znalostní báze různých jazyků v samotné službě. 

---

## <a name="single-language-per-resource"></a>Jeden jazyk na prostředek

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)

Zvažte použití těchto zdrojů:

* Služba QnA Maker a všechny její základy znalostní báze podporují jenom jeden jazyk.
* Jazyk je explicitně nastaven při vytvoření první znalostní báze služby.
* Jazyk se určí ze souborů a adres URL přidaných při vytváření znalostní báze.
* Jazyk není možné změnit pro žádné jiné znalostní báze ve službě.
* Jazyk používá služba Kognitivní hledání (#1 Rank) a služba QnA Maker (#2 Rank) k vygenerování nejlepší odpovědi na dotaz.

# <a name="qnamaker-managed-preview"></a>[Spravované Qnamakerem (Preview)](#tab/v2)
![Nastavení jazyka ve spravovaném QnA Maker](../media/language-support/language-setting-managed.png)

Pokud **nezaškrtnete políčko pro povolení nastavení jazyka na bázi znalostní báze**, vezměte v úvahu tyto informace: 
* Služba QnA Maker a všechny její znalostní báze budou podporovat jenom jeden jazyk.
* Jazyk se explicitně nastaví, když se vytvoří první znalostní báze služby.
* Jazyk se určí ze souborů a adres URL přidaných při vytváření znalostní báze.
* Jazyk není možné změnit pro žádné jiné znalostní báze ve službě.
* Jazyk používá služba Kognitivní hledání (#1 Rank) a služba QnA Maker (#2 Rank) k vygenerování nejlepší odpovědi na dotaz.

---

## <a name="supporting-multiple-languages-in-one-qna-maker-resource"></a>Podpora více jazyků v jednom prostředku QnA Maker

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabilní verze)](#tab/v1)
Tato funkce není podporovaná v naší současné všeobecně dostupné verzi (GA) stabilní. K otestování této funkce si Projděte QnA Maker spravovaná. 

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker spravované (verze Preview)](#tab/v2)
* Když vytváříte první znalostní bázi ve vaší službě, získáte možnost povolit nastavení jazyka pro jednu znalostní bázi. Zaškrtnutím políčka vytvoříte znalostní bázi patřící do různých jazyků v rámci jedné služby.
* Možnost nastavení jazyka nelze pro službu změnit, jakmile bude vytvořena první znalostní báze.
* Pokud povolíte jazyková nastavení specifická pro každou znalostní bázi, místo abyste měli jeden testovací index pro službu, budete mít jeden index testu na znalostní bázi. 

![Nastavení jazyka ve spravovaném QnA Maker](../media/language-support/language-setting-managed.png)

---

## <a name="supporting-multiple-languages-in-one-knowledge-base"></a>Podpora více jazyků v jedné znalostní bázi

Pokud potřebujete podporovat systém znalostní báze, který obsahuje několik jazyků, můžete:

* Před odesláním otázky do znalostní báze použijte [službu Translator](../../translator/translator-info-overview.md) k překladu otázky do jednoho jazyka. To vám umožní zaměřit se na kvalitu jednoho jazyka a kvality alternativních otázek a odpovědí.
* Vytvořte prostředek QnA Maker a v rámci tohoto prostředku znalostní bázi pro každý jazyk. Díky tomu můžete spravovat samostatné alternativní otázky a text odpovědi, který je pro jednotlivé jazyky odlišitější. Díky tomu získáte mnohem větší flexibilitu, ale při změně otázek a odpovědí napříč všemi jazyky potřebujete mnohem vyšší náklady na údržbu.


## <a name="languages-supported"></a>Podporované jazyky

Následující seznam obsahuje jazyky podporované QnA Makerm prostředkem. 

| Jazyk |
|--|
| Arabština |
| Arménština |
| Bengálština |
| Baskičtina |
| Bulharština |
| Katalánština |
| Chinese_Simplified |
| Chinese_Traditional |
| Chorvatština |
| Čeština |
| Dánština |
| Nizozemština |
| Angličtina |
| Estonština |
| Finština |
| Francouzština |
| Galicijština |
| Němčina |
| Řečtina |
| Gudžarátština |
| Hebrejština |
| Hindština |
| Maďarština |
| Islandština |
| Indonéština |
| Irština |
| Italština |
| Japonština |
| Kannadština |
| Korejština |
| Lotyština |
| Litevština |
| Malajalámština |
| Malajština |
| Norština |
| Polština |
| Portugalština |
| Paňdžábština |
| Rumunština |
| Ruština |
| Serbian_Cyrillic |
| Serbian_Latin |
| Slovenština |
| Slovinština |
| Španělština |
| Švédština |
| Tamilština |
| Telugština |
| Thajština |
| Turečtina |
| Ukrajinština |
| Urdština |
| Vietnamština |

## <a name="query-matching-and-relevance"></a>Shoda a závažnost dotazu
QnA Maker závisí na [analyzátorech kognitivní hledání jazyka Azure](/rest/api/searchservice/language-support) pro poskytování výsledků.

I když jsou možnosti Azure Kognitivní hledání pro podporované jazyky v nominální hodnotě, QnA Maker má další hodnocení, které je umístěné nad výsledky Azure Search. V tomto modelu hodnocení používáme některé speciální sémantické funkce a funkce založené na slovech v následujících jazycích.

|Jazyky s dalšími přiřazením|
|--|
|Čínština|
|Čeština|
|Nizozemština|
|Angličtina|
|Francouzština|
|Němčina|
|Maďarština|
|Italština|
|Japonština|
|Korejština|
|Polština|
|Portugalština|
|Španělština|
|Švédština|

Toto dodatečné hodnocení představuje interní pracovní QnA Maker hodnocení.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Výběr jazyka](../index.yml)