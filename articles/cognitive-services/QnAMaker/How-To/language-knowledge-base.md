---
title: Jazykové koncepty – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker podporuje obsah znalostní báze v mnoha jazycích. Každá QnA Makerová služba by však měla být vyhrazena pro jeden jazyk. První vytvořená znalostní báze, která cílí na konkrétní službu QnA Maker, nastaví jazyk této služby.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: b0d4250a6659996187923905955a9825a44cea42
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132615"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Jazyková podpora obsahu znalostní báze pro QnA Maker

Při vytváření první znalostní báze v prostředku je vybraný jazyk pro službu. Všechny další databáze znalostí v prostředku musí být ve stejném jazyce.

Jazyk určuje relevanci výsledků QnA Maker poskytuje odpověď na dotazy uživatelů.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Jeden jazyk pro všechny databáze znalostí v prostředku

Služba QnA Maker umožňuje vybrat jazyk služby otázek a odpovědí při vytváření první znalostní báze. Všechny znalostní báze v jednom prostředku služby QnA Maker musí být ve stejném jazyce. Tento jazyk není možné změnit.

Vytváření znalostní báze v různých jazycích v jednom zdroji negativně ovlivňuje relevanci výsledků QnA Maker poskytuje odpověď na dotazy uživatelů.

Projděte si seznam [podporovaných jazyků](../overview/language-support.md#languages-supported) a to, jak jazyky ovlivňují [a relevance](#query-matching-and-relevance).

## <a name="select-language-when-creating-first-knowledge-base"></a>Při vytváření prvního znalostní báze vybrat jazyk

Výběr jazyka je součástí kroků k vytvoření první znalostní báze v prostředku.

![Snímek obrazovky QnA Makerového portálu pro výběr jazyka pro první znalostní bázi](../media/language-support/select-language-when-creating-knowledge-base.png)

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

## <a name="verify-language"></a>Ověřit jazyk

Jazyk prostředku QnA Maker můžete ověřit na stránce nastavení služby v QnA Maker.

![Snímek obrazovky QnA Makerového portálu stránky nastavení služby](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace znalostní báze](../Tutorials/migrate-knowledge-base.md)
