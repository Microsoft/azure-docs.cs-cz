---
title: 'Rychlý start: Volání koncového bodu pomocí Pythonu – Vlastní vyhledávání Bingu'
titlesuffix: Azure Cognitive Services
description: Tento rychlý start ukazuje, jak si z instance vlastního vyhledávání vyžádat výsledky hledání za použití Pythonu k volání koncového bodu vlastního vyhledávání Bingu.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: quickstart
ms.date: 05/07/2018
ms.author: aahi
ms.openlocfilehash: e3e4256d3654f532f16d33c77f4c7e8cb7e93dd4
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2018
ms.locfileid: "52162632"
---
# <a name="quickstart-call-bing-custom-search-endpoint-python"></a>Rychlý start: Volání koncového bodu vlastního vyhledávání Bingu (Python)

Tento rychlý start ukazuje, jak si z instance vlastního vyhledávání vyžádat výsledky hledání za použití Pythonu k volání koncového bodu vlastního vyhledávání Bingu. 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Instance vlastního vyhledávání připravená k použití. Přečtěte si téma [Vytvoření první instance vlastního vyhledávání Bingu](quick-start.md).
- Nainstalovaný [Python](https://www.python.org/).
- Klíč předplatného. Klíč předplatného můžete získat aktivací [bezplatné zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) nebo můžete použít klíč placeného předplatného z řídicího panelu Azure (informace najdete v tématu [Účet rozhraní API služby Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>Spuštění kódu

Pokud chcete tuto ukázku spustit, postupujte takto:

1. Vytvořte pro svůj kód složku.  
  
2. Z příkazového řádku nebo terminálu správce přejděte do složky, kterou jste právě vytvořili.  
  
3. Nainstalujte modul Python **requests**:  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. Ve vytvořené složce vytvořte soubor s názvem BingCustomSearch.py a zkopírujte do něj následující kód. Položky **YOUR-SUBSCRIPTION-KEY** a **YOUR-CUSTOM-CONFIG-ID** nahraďte klíčem předplatného a ID konfigurace.  
  
    ``` Python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?q=' + searchTerm + '&customconfig=' + customConfigId
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```  
  
7. Spusťte kód pomocí následujícího příkazu.  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Další postup
- [Konfigurace prostředí pro hostované uživatelské rozhraní](./hosted-ui.md)
- [Zvýraznění textu pomocí dekoračních značek](./hit-highlighting.md)
- [Stránkování webových stránek](./page-webpages.md)
