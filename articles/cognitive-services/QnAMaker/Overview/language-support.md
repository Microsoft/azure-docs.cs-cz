---
title: Podpora jazyků – QnA Maker
titleSuffix: Azure Cognitive Services
description: Jazyk znalostní báze má vliv na možnost QnA Maker automaticky extrahovat otázky a odpovědi ze zdrojů a také relevanci výsledků QnA Maker poskytuje reakci na dotazy uživatelů. Seznam jazykovou verzi, podporuje QnA Maker znalostní báze přirozeného jazyka. Nekombinujte jazyky ve stejném znalostní báze.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: bec09e5cd053774902083c6dd042c1ed4b293d20
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066668"
---
# <a name="language-support-for-qna-maker"></a>Jazyková podpora pro QnA Maker

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

## <a name="primary-language-detection"></a>Detekce primárního jazyka

Primární jazyk použitý k detekci je nastaven pro prostředek QnA Maker a všechny znalostní báze vytvořené v tomto prostředku, když se první dokument nebo adresa URL přidá do první znalostní báze. Jazyk nelze změnit. 

Pokud uživatel plánuje podporu více jazyků, musí mít nový QnA Maker prostředek pro každý jazyk. Naučte se, jak [vytvořit jazykovou QnA maker znalostní bázi založené na jazyce](../how-to/language-knowledge-base.md).  

Ověřte primární jazyk pomocí následujících kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).  
1. Vyhledejte a vyberte prostředek Azure Search vytvořený jako součást prostředku QnA Maker. Název prostředku Azure Search bude začínat stejným názvem jako prostředek QnA Maker a bude mít **službu vyhledávání**typu. Mějte na paměti, že k jednomu prostředku Azure Search může být propojený jenom jeden QnA Maker prostředek.
1. Na stránce **Přehled** prostředku vyhledávání vyberte **indexy**. 
1. Vyberte **Testkb** index.
1. Vyberte kartu **pole** . 
1. Zobrazit sloupec **analyzátoru** pro pole **otázek** a **odpovědí** 


## <a name="query-matching-and-relevance"></a>Odpovídající dotaz a relevance
Nástroj QnA Maker závisí na [jazykové analyzátory](https://docs.microsoft.com/rest/api/searchservice/language-support) ve službě Azure search pro poskytování výsledků. Speciální znovu hodnocení funkce jsou k dispozici En-* jazyky, které umožňují lepší podle relevance.

Možnosti Azure Search jsou na pamětích pro podporované jazyky, nástroje QnA Maker je další klasifikátor, který je umístěný nad výsledky Azure search. V tomto modelu hodnocení používáme některé speciální sémantiky a funkce založené na slovech v EN-*, které ještě nejsou k dispozici pro jiné jazyky. Tyto funkce nejsou k dispozici, protože jsou součástí interního fungování rozsahu QnA Maker. 

QnA Maker [automaticky detekuje jazyk znalostní báze](#primary-language-detection) při vytváření a nastaví analyzátor odpovídajícím způsobem. Můžete vytvořit znalostních bází v těchto jazycích. 

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
