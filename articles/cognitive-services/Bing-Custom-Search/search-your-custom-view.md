---
title: Hledání vlastních zobrazení – vlastní vyhledávání Bingu
titlesuffix: Azure Cognitive Services
description: Popisuje, jak hledat vlastní zobrazení webu.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/12/2019
ms.author: maheshb
ms.openlocfilehash: e0c1ec55a7f21ad223629d6d478cbd97c7974abb
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237973"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Volání vaší instance vlastního vyhledávání Bingu z portálu

Po dokončení konfigurace prostředí pro vlastní vyhledávání, takže ji můžete otestovat z v rámci vlastní vyhledávání Bingu [portál](https://customsearch.ai). 

![snímek obrazovky portálu pro vlastní vyhledávání Bingu](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Vytvoření vyhledávací dotaz 

Po přihlášení do vlastní vyhledávání Bingu [portál](https://customsearch.ai), vyberte instanci hledání a klikněte na tlačítko **produkční** kartu. V části **koncové body**, vyberte koncový bod rozhraní API (například webové rozhraní API). Předplatné Určuje, jaké koncové body jsou uvedeny.

Chcete-li vytvořit vyhledávací dotaz, zadejte hodnoty parametrů pro koncový bod služby. Všimněte si, že parametry, zobrazí na portálu může měnit v závislosti na koncový bod, kterou zvolíte. Zobrazit[referenční dokumentace rozhraní API pro vlastní vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) Další informace. 

Některé důležité parametry jsou následující:


|Parametr  |Popis  |
|---------|---------|
|Dotaz     | Hledaný termín, který chcete vyhledat. K dispozici jenom pro Web, Image, Video a pro automatické návrhy koncových bodů |
|ID vlastní konfigurace | ID konfigurace vybrané instance vlastního vyhledávání. Toto pole je jen pro čtení. |
|Trh     | Na trhu, která způsobí, že budou pocházet z. Dostupné jenom pro koncové body Web, Image, Video a hostované uživatelského rozhraní.        |
|Klíč předplatného | Klíč předplatného pro testování. Můžete vybrat klíče z rozevíracího seznamu nebo zadat ručně.          |

Kliknutím na **další parametry** odhalí následující parametry:  

|Parametr  |Popis  |
|---------|---------|
|Bezpečné vyhledávání     | Filtr, který se používá k filtrování webových stránek pro obsah pro dospělé. Dostupné jenom pro koncové body Web, Image, Video a hostované uživatelského rozhraní.        |
|Jazyk uživatelského rozhraní    | Jazyk používaný pro uživatelské rozhraní řetězce. Pokud povolíte obrázky a videa v uživatelském rozhraní hostovaná, například **Image** a **Video** karet použít zadaný jazyk.        |
|Počet     | Počet výsledků hledání vrátit v odpovědi. K dispozici pouze pro koncové body Web, Image a videa.         |
|Posun    | Počet výsledků hledání pro přeskočení před vrácením výsledky. K dispozici pouze pro koncové body Web, Image a videa.        |
    
Po zadání všechny nezbytné možnosti, klikněte na tlačítko **volání** zobrazíte odpověď JSON v pravém podokně. Pokud vyberete hostované uživatelského rozhraní koncového bodu, můžete otestovat vyhledávání v dolním podokně.

## <a name="next-steps"></a>Další postup

- [Volání vlastního zobrazení pomocí jazyka C#](./call-endpoint-csharp.md)
- [Volání vlastního zobrazení pomocí Javy](./call-endpoint-java.md)
- [Volání vlastního zobrazení s NodeJs](./call-endpoint-nodejs.md)
- [Volání vlastního zobrazení pomocí Pythonu](./call-endpoint-python.md)

- [Volání vlastního zobrazení pomocí sady SDK jazyka C#](./sdk-csharp-quick-start.md)
