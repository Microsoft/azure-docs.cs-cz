---
title: Glosář – QnA Maker
titleSuffix: Azure Cognitive Services
description: Služba QnA Maker má mnoho nové podmínky ze služby machine learning a zpracování i specifickou pro službu podmínky přirozeného jazyka. Tento seznam vám pomůže pochopit tyto podmínky.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: e86ea22208ccd08c0268fda0cc47d5127b4568a1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226464"
---
# <a name="glossary-for-qna-maker-knowledge-base-and-service"></a>Glosář pro nástroj QnA Maker znalostní báze a služby

## <a name="qna-maker-service"></a>Služba QnA Maker
Služba QnA Maker je jako nezbytné komponenty k použití nástroje QnA Maker. Zakoupení vrstvu QnA Maker nastaví prostředky ve vašem předplatném Azure pro vytváření a správu znalostní báze. Všechny uživatelské účty nástroje QnA Maker můžete vytvořit víc služeb nástroje QnA Maker v rámci svého předplatného Azure.

## <a name="knowledge-base"></a>Znalostní báze
Znalostní báze je úložiště otázky a odpovědi vytvořen, udržovat a použít nástroj QnA Maker. Každá úroveň QnA Maker lze použít pro více znalostní báze.

## <a name="endpoint"></a>Koncový bod
Koncový bod HTTP založené na protokolu REST pro obsluhu obsah znalostní báze, který je možné integrovat do své aplikace, často je chatovací robot. 

## <a name="test-knowledge-base"></a>Test znalostní báze
Znalostní báze má dva stavy - testů a publikovat. Znalostní báze testu je verze, která je právě upravený, uložených a testují přesnosti a úplnosti odpovědi. Změny provedené ve znalostní bázi test neovlivňují koncový uživatel aplikace/chatovací robot.

## <a name="published-knowledge-base"></a>Publikované znalostní báze
Znalostní báze má dva stavy - Test a publikovaný.  Znalostní báze publikované je verze, která se používá v vašeho botu nebo aplikace chat. Akce publikování znalostní báze převádí obsah znalostní báze Test na publikovanou verzi znalostní báze. Protože znalostní báze publikované verzi, která aplikace používá prostřednictvím koncového bodu, by měl zajistit, že obsah je správná a dobře otestovaný věnovat pozornost.

## <a name="query"></a>Dotaz
Dotaz, který uživatel je dotazu, koncovým uživatelům nebo tester znalostní báze. Dotaz je často ve formátu přirozeného jazyka nebo několik klíčových slov, které představují dotaz.

## <a name="response"></a>Odpověď
Odpověď je načíst ze znalostní báze podle je nejlepší shodou pro daný uživatelský dotaz odpověď.

## <a name="confidence-score"></a>Skóre spolehlivosti
Skóre spolehlivosti odpovědi je číselná hodnota mezi 0 a 100, 100 se přesný dotaz v případě shody mezi uživatele dotazu a dotaz ve znalostní báze, který obsluhuje odpovědi je odpověď správná, odpovídající dotazu daného uživatele. Odpovědi jsou obvykle seřazené podle skóre spolehlivosti a s větší jistoty slouží jako výchozí odpověď.
