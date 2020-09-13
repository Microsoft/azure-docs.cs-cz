---
title: Jazyková podpora – QnA Maker
titleSuffix: Azure Cognitive Services
description: Seznam jazykové verze, přirozené jazyky, které QnA Maker podporuje ve znalostní bázi. Nemíchejte jazyky ve stejné znalostní bázi.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/24/2019
ms.openlocfilehash: c990b6980dea871679b0b301e293e4fb94748db7
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650904"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>Jazyková podpora pro QnA Maker prostředky a základy znalostní báze

Při vytváření první znalostní báze v prostředku je vybraný jazyk pro službu. Všechny další databáze znalostí v prostředku musí být ve stejném jazyce. 

Jazyk určuje relevanci výsledků QnA Maker poskytuje odpověď na dotazy uživatelů. Prostředek QnA Maker a všechny znalostní báze uvnitř tohoto prostředku podporují jeden jazyk. Jeden jazyk je nutný k tomu, aby poskytoval nejlepší výsledky odpovědi pro dotaz.

## <a name="single-language-per-resource"></a>Jeden jazyk na prostředek

Zvažte použití těchto zdrojů:

* Služba QnA Maker a všechny její základy znalostní báze podporují jenom jeden jazyk.
* Jazyk se explicitně nastaví, když se vytvoří první znalostní báze služby.
* Jazyk se určí ze souborů a adres URL přidaných při vytváření znalostní báze.
* Jazyk není možné změnit pro žádné jiné znalostní báze ve službě.
* Jazyk používá služba Kognitivní hledání (#1 Rank) a služba QnA Maker (#2 Rank) k vygenerování nejlepší odpovědi na dotaz.

## <a name="supporting-multiple-languages"></a>Podpora více jazyků

Pokud potřebujete podporovat systém znalostní báze, který obsahuje několik jazyků, můžete:

* Před odesláním otázky do znalostní báze použijte [službu Translator](../../translator/translator-info-overview.md) k překladu otázky do jednoho jazyka. To vám umožní zaměřit se na kvalitu jednoho jazyka a kvality alternativních otázek a odpovědí.
* Vytvořte prostředek QnA Maker a v rámci tohoto prostředku znalostní bázi pro každý jazyk. Díky tomu můžete spravovat samostatné alternativní otázky a text odpovědi, který je pro jednotlivé jazyky odlišitější. Díky tomu získáte mnohem větší flexibilitu, ale při změně otázek a odpovědí napříč všemi jazyky potřebujete mnohem vyšší náklady na údržbu.

Přečtěte si [jazyky podporované](../overview/language-support.md) pro QnA maker.

### <a name="support-each-language-with-a-qna-maker-resource"></a>Podpora jednotlivých jazyků pomocí QnA Makerho prostředku

* Vytvořit prostředek QnA Maker pro všechny jazyky
* Přidat jenom soubory a adresy URL pro daný jazyk
* K identifikaci jazyka použijte zásady vytváření názvů prostředků. Příklad je `qna-maker-fr` pro všechny znalostní báze pro francouzské dokumenty.


## <a name="languages-supported"></a>Podporované jazyky

Následující seznam obsahuje jazyky podporované QnA Makerm prostředkem. 

|Jazyk|
|--|
|Arabština|
|Arménština|
|Bengálština|
|Baskičtina|
|Bulharština|
|Katalánština|
|Chinese_Simplified|
|Chinese_Traditional|
|Chorvatština|
|Čeština|
|Dánština|
|Nizozemština|
|Angličtina|
|Estonština|
|Finština|
|Francouzština|
|Galicijština|
|Němčina|
|Řečtina|
|Gudžarátština|
|Hebrejština|
|Hindština|
|Maďarština|
|Islandština|
|Indonéština|
|Irština|
|Italština|
|Japonština|
|Kannadština|
|Korejština|
|Lotyština|
|Litevština|
|Malajalámština|
|Malajština|
|Norština|
|Polština|
|Portugalština|
|Paňdžábština|
|Rumunština|
|Ruština|
|Serbian_Cyrillic|
|Serbian_Latin|
|Slovenština|
|Slovinština|
|Španělština|
|Švédština|
|Tamilština|
|Telugština|
|Thajština|
|Turečtina|
|Ukrajinština|
|Urdština|
|Vietnamština|

## <a name="query-matching-and-relevance"></a>Shoda a závažnost dotazu
QnA Maker závisí na [analyzátorech kognitivní hledání jazyka Azure](https://docs.microsoft.com/rest/api/searchservice/language-support) pro poskytování výsledků.

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
> [Výběr jazyka](../how-to/language-knowledge-base.md)
