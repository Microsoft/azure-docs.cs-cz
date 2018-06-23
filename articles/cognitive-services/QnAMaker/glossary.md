---
title: Glosář - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Glosář
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: e28cddec005cb6ba99b9f60d8b03a11f1bc97062
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343273"
---
# <a name="glossary"></a>Glosář

## <a name="qna-maker-service"></a>Služba QnA Maker
Služba QnA Maker je nezbytné k použití QnA Maker. Zakoupení vrstvu QnA Maker nastaví prostředky ve vašem předplatném Azure k vytváření a správě vašeho znalostní báze knowledge base. Všechny uživatelské účty, QnA Maker můžete vytvořit více služeb QnA Maker v rámci svého předplatného Azure.

## <a name="knowledge-base"></a>Znalostní báze Knowledge Base
Znalostní báze slouží jako úložiště otázky a odpovědi vytvořen, udržuje a použít QnA Maker. Každá úroveň QnA Maker lze použít pro více znalostních bází.

## <a name="endpoint"></a>Koncový bod
Koncový bod HTTP na základě REST obsluhy obsahu znalostní báze knowledge base, které lze integrovat do vaší aplikace, obvykle robotu konverzace. 

## <a name="test-knowledge-base"></a>Test znalostní báze
Znalostní báze má dva stavy - Test a publikovat. Znalostní báze test je verze, která se úpravy, uložený a testované pro přesnosti a úplnosti odpovědi. Změny provedené ve znalostní bázi knowledge testovací neovlivňují koncového uživatele vaší aplikace nebo chat robota.

## <a name="published-knowledge-base"></a>Publikované znalostní báze
Znalostní báze má dva stavy - Test a publikovaný.  Znalostní báze publikované je verze, která se používá v robota chatovací nebo aplikace. Akce publikování znalostní bázi převádí obsah znalostní báze Test na publikovanou verzi znalostní bázi knowledge base. Vzhledem k tomu, že publikované znalostní bázi knowledge base na verzi, která používá aplikace prostřednictvím koncového bodu se mělo dbát na to, k zajištění, že obsah je správná a dobře otestované.

## <a name="query"></a>Dotaz
Dotaz uživatele je otázka s dotazem, koncovým uživatelům nebo tester znalostní bázi knowledge base. Dotaz je často ve formátu přirozeného jazyka nebo několik klíčových slov, které představují na otázku.

## <a name="response"></a>Odpověď
Odpověď je načíst ze znalostní báze podle nejlepší shodu pro daného uživatele dotaz odpověď.

## <a name="confidence-score"></a>Skóre spolehlivosti
Skóre spolehlivosti odpovědi je numerická hodnota mezi 0 a 100, 100 se přesný dotazu nalezena shoda mezi dotaz uživatele a dotaz ve znalostní bázi knowledge base, který zpracoval odpověď je správná, příslušné odpovědi na dotaz daného uživatele. Odpovědi, které jsou obvykle seřazeny podle skóre spolehlivosti a jednu s vyšší jistotou skóre spuštěn jako výchozí odpověď.
