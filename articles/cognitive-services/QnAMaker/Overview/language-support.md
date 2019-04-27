---
title: Podpora jazyků – QnA Maker
titleSuffix: Azure Cognitive Services
description: Jazyk znalostní báze ovlivňuje QnA Maker umožňuje automaticky extrahovat otázky a odpovědi ze zdrojů, jakož i relevance výsledků, které poskytuje nástroj QnA Maker v odpovědi na dotazy uživatelů. Seznam jazykovou verzi, podporuje QnA Maker znalostní báze přirozeného jazyka. Nekombinujte jazyky ve stejném znalostní báze.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: a6304a93b1409cff871ed1c4c1d7e66d6c8c6f53
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736706"
---
# <a name="language-support-for-qna-maker"></a>Jazyková podpora v nástroji QnA Maker

Jazyk znalostní báze ovlivňuje QnA Maker umožňuje automaticky extrahovat otázky a odpovědi z [zdroje](../Concepts/data-sources-supported.md), a také podle relevance výsledků poskytuje nástroj QnA Maker v odpovědi na dotazy uživatelů.

## <a name="auto-extraction"></a>Automatická extrakce
Nástroj QnA Maker podporuje extrakce otázek a odpovědí na libovolné stránce jazyka, ale účinnost extrakce je mnohem větší pro následující jazyky, jak QnA Maker používá k identifikaci dotazy klíčová slova.

|Podporované jazyky| Národní prostředí|
|-----|----|
|Angličtina|cs-*|
|Francouzština|FR-*|
|italština|IT-*|
|Němčina|de-*|
|Španělština|ES-*|

## <a name="primary-language-detection"></a>Primární rozpoznávání jazyka

Primární jazyk používaný pro zjišťování je nastavena pro nástroj QnA Maker prostředek a všechny znalostních bází, vytvoří při prvním dokumentu nebo adrese URL se přidá do první znalostní báze na tento prostředek. Jazyk nelze změnit. 

Pokud uživatel plánuje zajistit podporu více jazyků, potřebují mít nový prostředek nástroje QnA Maker pro jednotlivé jazyky. Zjistěte, jak [vytvoření znalostní báze QnA Maker založený na jazyce](../how-to/language-knowledge-base.md).  

Ověřte primárního jazyka pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](http://portal.azure.com).  
1. Vyhledejte a vyberte prostředek Azure Search vytvořených jako součást vašeho prostředku QnA Maker. Název prostředku Azure Search, začne se stejným názvem jako prostředek QnA Maker a bude mít typ **služba Search**. 
1. Z **přehled** stránky vyhledávání prostředků, vyberte **indexy**. 
1. Vyberte **testkb** indexu.
1. Vyberte **pole** kartu. 
1. Zobrazení **analyzátor** sloupce **dotazy** a **odpovědí** pole. 


## <a name="query-matching-and-relevance"></a>Odpovídající dotaz a relevance
Nástroj QnA Maker závisí na [jazykové analyzátory](https://docs.microsoft.com/rest/api/searchservice/language-support) ve službě Azure search pro poskytování výsledků. Speciální znovu hodnocení funkce jsou k dispozici En-* jazyky, které umožňují lepší podle relevance.

Možnosti Azure Search jsou na pamětích pro podporované jazyky, nástroje QnA Maker je další klasifikátor, který je umístěný nad výsledky Azure search. V tomto modelu klasifikátor používáme některé speciální funkce sémantického a Wordu en-*, které ještě nejsou k dispozici pro jiné jazyky. Jsme zpřístupnění těchto funkcí, jako jsou součástí vnitřní fungování nástroje QnA Maker klasifikátor. 

Nástroj QnA Maker [automaticky zjistí jazyk znalostní báze](#primary-language-detection) během vytváření a příslušným způsobem nastaví analyzátor. Můžete vytvořit znalostních bází v těchto jazycích. 

|Podporované jazyky|
|-----|
|arabština|
|Arménština|
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
