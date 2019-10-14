---
title: Jazykové koncepty – QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker podporuje obsah znalostní báze v mnoha jazycích. Každá QnA Makerová služba by však měla být vyhrazena pro jeden jazyk. První vytvořená znalostní báze, která cílí na konkrétní službu QnA Maker, nastaví jazyk této služby.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 849c919950c57a1df3b0fb76021de6e10254c7b4
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286413"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Jazyková podpora obsahu znalostní báze pro QnA Maker

Při vytváření první znalostní báze v prostředku je vybraný jazyk pro službu. Všechny další databáze znalostí v prostředku musí být ve stejném jazyce. 

Jazyk určuje relevanci výsledků QnA Maker poskytuje odpověď na dotazy uživatelů.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Jeden jazyk pro všechny databáze znalostí v prostředku

QnA Maker umožňuje vybrat jazyk pro službu QnA při vytváření první znalostní báze. Všechna znalostní báze v prostředku QnA Maker musí být ve stejném jazyce. Tento jazyk se nedá změnit.

Vytváření znalostní báze v různých jazycích v jednom zdroji negativně ovlivňuje relevanci výsledků QnA Maker poskytuje odpověď na dotazy uživatelů.

Projděte si seznam [podporovaných jazyků](../overview/language-support.md#languages-supported) a to, jak jazyky ovlivňují [a relevance](#query-matching-and-relevance). 

## <a name="select-language-when-creating-first-knowledge-base"></a>Při vytváření prvního znalostní báze vybrat jazyk

Výběr jazyka je součástí kroků k vytvoření první znalostní báze v prostředku. 

![Snímek obrazovky QnA Makerového portálu pro výběr jazyka pro první znalostní bázi](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Shoda a závažnost dotazu
QnA Maker závisí na [analyzátorech Azure Search jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support) pro poskytování výsledků. 

I když jsou funkce Azure Search pro podporované jazyky v nominální hodnotě, QnA Maker má další řazení, které se nachází nad výsledky Azure Search. V tomto modelu hodnocení používáme některé speciální sémantické funkce a funkce založené na slovech v následujících jazycích. 

|Jazyky s dalšími přiřazením|
|--|
|Čínština|
|Čeština|
|Holandština|
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
> [Vytvoření QnA robota pomocí Azure Bot Service](../Tutorials/create-qna-bot.md)
