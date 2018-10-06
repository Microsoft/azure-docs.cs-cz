---
title: Hledání vlastních zobrazení – vlastní vyhledávání Bingu
titlesuffix: Azure Cognitive Services
description: Popisuje, jak hledat vlastní zobrazení webu.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 09/28/2017
ms.author: v-brapel
ms.openlocfilehash: 235062c1b3e54843b5e64f4ef16091ae5d630894
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48814351"
---
# <a name="call-your-custom-search"></a>Volání vlastního vyhledávání

Před vytvořením první volání do rozhraní API pro vlastní vyhledávání zobrazíte výsledky hledání pro vaši instanci, potřebujete získat klíč předplatného služeb Cognitive Services. Chcete-li získat klíč pro vlastní rozhraní API pro vyhledávání, naleznete v tématu [zkuste služby Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search).


## <a name="try-it-out"></a>Vyzkoušet

Po dokončení konfigurace prostředí pro vlastní vyhledávání, můžete otestovat konfiguraci z portálu pro vlastní vyhledávání. 

1. Přihlaste se do [vlastní vyhledávání](https://customsearch.ai).
2. Klikněte na instanci vlastního vyhledávání ze svého seznamu instancí.
3. Klikněte na tlačítko **produkční** kartu. 
4. V části **koncové body** kartu, vyberte koncový bod (například webové rozhraní API). Předplatné Určuje, jaké koncové body jsou (naleznete v tématu [ceny](https://azure.microsoft.com/pricing/details/cognitive-services/bing-custom-search/) možnosti předplatného). 
5. Zadejte hodnoty parametrů. 

    Toto jsou možné můžete nastavit parametry (skutečný seznam závisí na vybraný koncový bod). Další informace o těchto parametrech naleznete v tématu [rozhraní API pro vlastní vyhledávání](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#query-parameters) odkaz.

    - **Dotaz**: hledaný termín, který chcete vyhledat. Dostupné jenom pro Web, Image, Video a pro automatické návrhy koncových bodů.
    - **Vlastní ID konfigurace**: ID konfigurace vybrané instance vlastního vyhledávání. Toto pole je jen pro čtení.
    - **Na trhu**: na trhu, odkud pochází výsledky. Dostupné jenom pro koncové body Web, Image, Video a hostované uživatelského rozhraní.
    - **Klíč předplatného**: klíč předplatného pro testování. Může z rozevíracího seznamu vyberte klíč, nebo ji zadat ručně.  
      
    Kliknutím na **další parametry** odhalí následující parametry:  
      
    - **Bezpečné vyhledávání**: Filtr slouží k filtrování webových stránek pro obsah pro dospělé. K dispozici pouze pro koncové body Web, Image, Video a hostované uživatelského rozhraní.
    - **Jazyk uživatelského rozhraní**: jazyk používaný pro uživatelské rozhraní řetězce. Pokud povolíte obrázky a videa v uživatelském rozhraní hostovaná, například **Image** a **Video** karet použít zadaný jazyk.
    - **Počet**: počet výsledků hledání vrátit v odpovědi. K dispozici pouze pro koncové body Web, Image a videa.
    - **Posun**: počet výsledků hledání pro přeskočení před vrácením výsledky. K dispozici pouze pro koncové body Web, Image a videa.

6. Po zadání všechny nezbytné možnosti, klikněte na tlačítko **volání** zobrazíte odpověď JSON v pravém podokně. 

Pokud vyberete hostované uživatelského rozhraní koncového bodu, můžete otestovat vyhledávání v dolním podokně.

## <a name="next-steps"></a>Další postup

- [Volání vlastního zobrazení pomocí jazyka C#](./call-endpoint-csharp.md)
- [Volání vlastního zobrazení pomocí Javy](./call-endpoint-java.md)
- [Volání vlastního zobrazení s NodeJs](./call-endpoint-nodejs.md)
- [Volání vlastního zobrazení pomocí Pythonu](./call-endpoint-python.md)

- [Volání vlastního zobrazení pomocí sady SDK jazyka C#](./sdk-csharp-quick-start.md)