---
title: Hledání vlastního zobrazení – vlastní hledání Bingu
titleSuffix: Azure Cognitive Services
description: Po konfiguraci vlastního prostředí vyhledávání ho můžete otestovat z portálu vlastního vyhledávání Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: f00ffee47e3eb6366d632d8b6ee9beb01f048442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "76983108"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Volání vlastní instance vyhledávání Bingu z portálu

Po konfiguraci vlastního vyhledávání jej můžete otestovat z portálu vlastního vyhledávání [Bingu](https://customsearch.ai). 

![snímek obrazovky vlastního vyhledávacího portálu Bing](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Vytvoření vyhledávacího dotazu 

Po přihlášení k [portálu](https://customsearch.ai)vlastního vyhledávání Bingu vyberte instanci vyhledávání a klikněte na kartu **Produkční.** V části **Koncové body**vyberte koncový bod rozhraní API (například webové rozhraní API). Vaše předplatné určuje, jaké koncové body se zobrazí.

Chcete-li vytvořit vyhledávací dotaz, zadejte hodnoty parametrů pro koncový bod. Všimněte si, že parametry zobrazené na portálu se mohou měnit v závislosti na zvoleném koncovém bodu. Další informace najdete v [odkazu na rozhraní API pro vlastní vyhledávání.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) Chcete-li změnit předplatné, které používá instance vyhledávání, přidejte příslušný klíč předplatného a aktualizujte příslušné parametry trhu nebo jazyka.

Některé důležité parametry jsou uvedeny níže:


|Parametr  |Popis  |
|---------|---------|
|Dotaz     | Hledaný výraz, který chcete vyhledat. K dispozici pouze pro koncové body webu, obrázku, videa a automatického návrhu |
|ID vlastní konfigurace | ID konfigurace vybrané instance vlastního hledání. Toto pole je jen pro čtení. |
|Trhu     | Trh, který výsledky budou pocházet z. K dispozici pouze pro koncové body webového, obrazového, videa a hostovaného uživatelského rozhraní.        |
|Klíč předplatného | Klíč předplatného k testování. Můžete vybrat klíč z rozevíracího seznamu nebo zadat ručně.          |

Kliknutím na **další parametry** zobrazíte následující parametry:  

|Parametr  |Popis  |
|---------|---------|
|Bezpečné vyhledávání     | Filtr používaný k filtrování webových stránek pro obsah v dospělém systému. K dispozici pouze pro koncové body webového, obrazového, videa a hostovaného uživatelského rozhraní. Všimněte si, že hledání vlastního `moderate` `strict`videa Bing podporuje pouze dvě hodnoty: a .        |
|Jazyk uživatelského rozhraní    | Jazyk používaný pro řetězce uživatelského rozhraní. Pokud například povolíte obrázky a videa v hostovaném uzdu, budou karty **Obrázky** a **Video** používat zadaný jazyk.        |
|Počet     | Počet výsledků hledání vrátit v odpovědi. K dispozici pouze pro koncové body webu, obrázku a videa.         |
|Posun    | Počet výsledků hledání přeskočit před vrácením výsledků. K dispozici pouze pro koncové body webu, obrázku a videa.        |
    
Po zadání všech požadovaných možností klikněte na **Volat** a zobrazte odpověď JSON v pravém podokně. Pokud vyberete koncový bod hostovaného uživatelského prostředí, můžete otestovat prostředí hledání v dolním podokně.

## <a name="change-your-bing-custom-search-subscription"></a>Změna předplatného vlastního vyhledávání Bingu

Předplatné přidružené k instanci vlastního vyhledávání Bingu můžete změnit bez vytvoření nové instance. Pokud chcete, aby byla volání rozhraní API odeslána a účtována do nového předplatného, vytvořte na webu Azure Portal nový prostředek vlastního vyhledávání Bingu. Použijte nový klíč předplatného v požadavcích rozhraní API spolu s ID vlastní konfigurace instance.

## <a name="next-steps"></a>Další kroky

- [Volání vlastního zobrazení pomocí c #](./call-endpoint-csharp.md)
- [Volání vlastního zobrazení pomocí Javy](./call-endpoint-java.md)
- [Volání vlastního zobrazení pomocí nodeJs](./call-endpoint-nodejs.md)
- [Volání vlastního zobrazení v Pythonu](./call-endpoint-python.md)

- [Volání vlastního zobrazení pomocí sady C# SDK](./sdk-csharp-quick-start.md)
