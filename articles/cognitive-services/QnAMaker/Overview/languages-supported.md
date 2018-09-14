---
title: Podporované jazyky – QnA Maker
titlesuffix: Azure Cognitive Services
description: Jazyk znalostní báze ovlivňuje QnA Maker umožňuje automaticky extrahovat otázky a odpovědi ze zdrojů, jakož i relevance výsledků, které poskytuje nástroj QnA Maker v odpovědi na dotazy uživatelů.
services: cognitive-services
author: nstulasi
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: saneppal
ms.openlocfilehash: ee04733064ec4e3d131b800fe1f18b27e5127fe8
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45572936"
---
# <a name="supported-languages"></a>Podporované jazyky

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

## <a name="query-matching-and-relevance"></a>Odpovídající dotaz a relevance
Nástroj QnA Maker závisí na [jazykové analyzátory](https://docs.microsoft.com/rest/api/searchservice/language-support) ve službě Azure search pro poskytování výsledků. Speciální znovu hodnocení funkce jsou k dispozici En-* jazyky, které umožňují lepší podle relevance.

Nástroj QnA Maker automaticky zjistí jazyk znalostní báze během vytváření a příslušným způsobem nastaví analyzátor. Můžete vytvořit znalostních bází v těchto jazycích. Čtení [to](../How-To/language-knowledge-base.md) podrobné informace o tom, jak QnA Maker zpracovává jazyky.


> [!Tip]
> Analyzátory jazyka, po nastavení, nedá se změnit. Analyzátor jazyka platí také, pro všechny znalostních bází v [služba QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Pokud budete chtít mít znalostních bází v jiném jazyce, je musí vytvořit v rámci samostatné služby QnA Maker.

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
