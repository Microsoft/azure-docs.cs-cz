---
title: Jazykové koncepty - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker podporuje obsah znalostní báze v mnoha jazycích. Každá služba QnA Maker by však měla být vyhrazena pro jeden jazyk. První vytvořená znalostní báze zaměřená na konkrétní službu QnA Maker nastavuje jazyk této služby.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220630"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Jazyková podpora obsahu znalostní báze pro QnA Maker

Jazyk služby je vybrán při vytváření první znalostní báze v prostředku. Všechny další znalostní báze v prostředku musí být ve stejném jazyce.

Jazyk určuje relevanci výsledků QnA Maker poskytuje v reakci na dotazy uživatelů.

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>Jeden jazyk pro všechny znalostní báze v oblasti zdrojů

QnA Maker umožňuje vybrat jazyk pro službu QnA při vytváření první znalostní báze. Pro všechny znalostní báze v prostředku QnA Maker musí být všechny ve stejném jazyce. Tento jazyk nelze změnit.

Vytváření znalostních bází v různých jazycích v jednom prostředku negativně ovlivňuje relevanci výsledků, které qnA Maker poskytuje v reakci na dotazy uživatelů.

Prohlédněte si seznam [podporovaných jazyků](../overview/language-support.md#languages-supported) a způsob, jakým jazyky ovlivňují [shodu a relevanci](#query-matching-and-relevance).

## <a name="select-language-when-creating-first-knowledge-base"></a>Výběr jazyka při vytváření první znalostní báze

Výběr jazyka je součástí kroků k vytvoření první znalostní báze v prostředku.

![Snímek obrazovky portálu QnA Maker s výběrem jazyka pro první znalostní bázi](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>Párování dotazů a relevance
QnA Maker závisí na [Azure Cognitive Search analyzátory jazyka](https://docs.microsoft.com/rest/api/searchservice/language-support) pro poskytování výsledků.

Zatímco azure kognitivní vyhledávání možnosti jsou na stejné úrovni pro podporované jazyky, QnA Maker má další ranker, který sedí nad výsledky hledání Azure. V tomto ranker modelu používáme některé speciální sémantické a slovní funkce v následujících jazycích.

|Jazyky s přídavným rankerem|
|--|
|Chinese|
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

Toto dodatečné hodnocení je interním fungováním hodnostáře QnA Makeru.

## <a name="verify-language"></a>Ověřit jazyk

Jazyk prostředku qnA makeru můžete ověřit na stránce nastavení služby v programu QnA Maker.

![Snímek obrazovky portálu QnA Maker na stránce Nastavení služby](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Migrace znalostní báze](../Tutorials/migrate-knowledge-base.md)
