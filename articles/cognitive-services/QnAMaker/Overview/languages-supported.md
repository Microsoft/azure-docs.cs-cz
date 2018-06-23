---
title: Podporované jazyky - QnA Maker - Azure kognitivní Services | Microsoft Docs
description: Zjistěte, jaké jazyky jsou podporovány pro QnA Maker.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: b210f59129a962046787b27d003c2872a54f6c8e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343550"
---
# <a name="supported-languages"></a>Podporované jazyky

Jazyk znalostní bázi knowledge base má vliv na QnA Maker možnost automatické extrakce otázky a odpovědi z [zdroje](../Concepts/data-sources-supported.md), a také relevance výsledků QnA Maker poskytuje v odpovědi na dotazy na uživatele.

## <a name="auto-extraction"></a>Automatické extrakce
QnA Maker podporuje extrakce otázek a odpovědí na libovolné stránce jazyk, ale efektivitu extrahování je mnohem vyšší pro následující jazyky, jako QnA Maker používá k identifikaci otázky klíčová slova.

|Podporované jazyky| Národní prostředí|
|-----|----|
|Angličtina|en-*|
|Francouzština|FR-*|
|italština|IT-*|
|Němčina|de-*|
|Španělština|ES-*|

## <a name="query-matching-and-relevance"></a>Porovnávání a relevance dotazů
QnA Maker závisí na [analyzátory jazyka](https://docs.microsoft.com/en-us/rest/api/searchservice/language-support) ve službě Azure search pro zajištění výsledky. Speciální znovu hodnotící funkce jsou dostupné pro En-* jazyky, které umožňují lepší relevance.

QnA Maker automaticky zjistí jazyk znalostní báze během vytváření a nastaví nástroje analyzer odpovídajícím způsobem. Můžete vytvořit znalostních bází v těchto jazycích. Čtení [to](../How-To/language-knowledge-base.md) další podrobnosti o způsobu, jakým QnA Maker zpracovává jazyky.


> [!Tip]
> Analyzátory jazyka po nastavení, nelze změnit. Navíc analyzátor jazyka platí pro všechny znalostních bází v [QnA Maker služby](../How-To/set-up-qnamaker-service-azure.md). Pokud chcete mít znalostních bází v jiném jazyce, měli byste je vytvořit v rámci samostatné QnA Maker services.

|Podporované jazyky|
|-----|
|arabština|
|Arménština|,
bengálština|
|Baskičtina|
|Bulharština|
|Katalánština|
|Chinese_Simplified|
|Chinese_Traditional|
|Chorvatština|
|Čeština|
|dánština|
|Holandština|
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
|italština|
|Japonština|
|Kannadština|
|Korejština|
|Lotyština|
|Litevština|
|Malajálamština|
|Malajština|
|norština|
|polština|
|Portugalština|
|Paňdžábština|
|Rumunština|
|ruština|
|Serbian_Cyrillic|
|Serbian_Latin|
|Slovenština|
|Slovinština|
|Španělština|
|švédština|
|Tamilština|
|Telugština|
|Thajština|
|turečtina|
|Ukrajinština|
|Urdština|
|Vietnamština|
