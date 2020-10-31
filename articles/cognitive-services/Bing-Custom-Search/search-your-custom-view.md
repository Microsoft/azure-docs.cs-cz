---
title: Hledání vlastního zobrazení – Vlastní vyhledávání Bingu
titleSuffix: Azure Cognitive Services
description: Po nakonfigurování vlastního vyhledávacího prostředí ho můžete otestovat na portálu Vlastní vyhledávání Bingu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: 38191c55afb87151b238069a75a6c86acd8904df
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100537"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Volání instance Vlastní vyhledávání Bingu z portálu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Po nakonfigurování vlastního vyhledávacího prostředí ho můžete otestovat na [portálu](https://customsearch.ai)vlastní vyhledávání Bingu. 

![snímek obrazovky s portálem pro vlastní vyhledávání Bingu](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Vytvoření vyhledávacího dotazu 

Po přihlášení k [portálu](https://customsearch.ai)vlastní vyhledávání Bingu vyberte svou instanci hledání a klikněte na kartu **Výroba** . V části **koncové body** vyberte koncový bod rozhraní API (například webové rozhraní API). Vaše předplatné určuje, jaké koncové body se zobrazí.

Vyhledávací dotaz vytvoříte zadáním hodnot parametrů pro koncový bod. Všimněte si, že parametry zobrazené na portálu se můžou měnit v závislosti na koncovém bodu, který zvolíte. Další informace najdete v [referenčních](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) informacích k rozhraní API pro vlastní vyhledávání. Chcete-li změnit předplatné, které vaše instance vyhledávání používá, přidejte příslušný klíč předplatného a aktualizujte příslušné parametry trhu nebo jazyka.

Níže jsou uvedeny některé důležité parametry:


|Parametr  |Popis  |
|---------|---------|
|Dotaz     | Hledaný termín. K dispozici pouze pro koncové body webu, obrázku, videa a Automatický návrh |
|ID vlastní konfigurace | ID konfigurace vybrané instance vlastního vyhledávání. Toto pole je jen pro čtení. |
|Uvádět     | Trh, ze kterého budou výsledky pocházet. K dispozici pouze pro koncové body webu, obrázku, videa a hostovaného uživatelského rozhraní.        |
|Klíč předplatného | Klíč předplatného, pomocí kterého se má testovat. Můžete vybrat klíč z rozevíracího seznamu nebo ho zadat ručně.          |

Kliknutím na **Další parametry** se odhalí následující parametry:  

|Parametr  |Popis  |
|---------|---------|
|Bezpečné vyhledávání     | Filtr použitý k filtrování webových stránek pro obsah určený pro dospělé K dispozici pouze pro koncové body webu, obrázku, videa a hostovaného uživatelského rozhraní. Všimněte si, že vlastní Vyhledávání videí Bingu podporuje jenom dvě hodnoty: `moderate` a `strict` .        |
|Jazyk uživatelského rozhraní    | Jazyk používaný pro řetězce uživatelského rozhraní. Pokud například povolíte image a videa v hostovaném uživatelském rozhraní, na kartách **Obrázek** a **video** se použije zadaný jazyk.        |
|Počet     | Počet výsledků hledání, které se mají vrátit v odpovědi K dispozici pouze pro koncové body webu, obrázku a videa.         |
|Posun    | Počet výsledků hledání, které se mají přeskočit před vrácením výsledků K dispozici pouze pro koncové body webu, obrázku a videa.        |
    
Po zadání všech požadovaných možností klikněte na **zavolat** , aby se zobrazila odpověď JSON v pravém podokně. Pokud vyberete koncový bod hostovaného uživatelského rozhraní, můžete otestovat možnosti vyhledávání v dolním podokně.

## <a name="change-your-bing-custom-search-subscription"></a>Změna předplatného Vlastní vyhledávání Bingu

Předplatné přidružené k vaší instanci Vlastní vyhledávání Bingu můžete změnit bez vytvoření nové instance. Pokud chcete, aby se volání rozhraní API poslala a vyrovnala novému předplatnému, vytvořte v Azure Portal nový prostředek Vlastní vyhledávání Bingu. Použijte nový klíč předplatného v požadavcích rozhraní API spolu s ID vlastní konfigurace vaší instance.

## <a name="next-steps"></a>Další kroky

- [Volání vlastního zobrazení pomocí jazyka C #](./call-endpoint-csharp.md)
- [Volání vlastního zobrazení pomocí Java](./call-endpoint-java.md)
- [Volání vlastního zobrazení pomocí NodeJs](./call-endpoint-nodejs.md)
- [Volání vlastního zobrazení pomocí Pythonu](./call-endpoint-python.md)

- [Volání vlastního zobrazení pomocí sady C# SDK](./sdk-csharp-quick-start.md)
