---
title: Volání koncového bodu s využitím Pythonu – vlastní vyhledávání Bingu – Microsoft Cognitive Services
description: Tento rychlý start ukazuje, jak si vyžádat výsledky hledání s použitím jazyka Python k volání koncového bodu pro vlastní vyhledávání Bingu z vaší instance vlastního hledání.
services: cognitive-services
author: brapel
manager: ehansen
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: article
ms.date: 05/07/2018
ms.author: v-brapel
ms.openlocfilehash: 3666e92372e9bed80e5c0c7991dcac730cebb588
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46967587"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Volání koncového bodu pro vlastní vyhledávání Bingu (Python)

Tento rychlý start ukazuje, jak si vyžádat výsledky hledání z vaší instance vlastního hledání použití Pythonu k volání koncového bodu pro vlastní vyhledávání Bingu. 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu je potřeba:

- Instanci vlastního vyhledávání připravené k použití. Zobrazit [vytvořit první instanci vlastního vyhledávání Bingu](quick-start.md).
- [Python](https://www.python.org/) nainstalované.
- Klíč předplatného. Klíč předplatného můžete získat, když aktivujete vaše [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search), nebo můžete použít klíč placené předplatné z řídicího panelu Azure (naleznete v tématu [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)).    


## <a name="run-the-code"></a>Spuštění kódu

Chcete-li spustit tento příklad, postupujte podle těchto kroků:

1. Vytvořte složku pro váš kód.  
  
2. Z příkazového řádku správce nebo terminálu přejděte do složky, kterou jste právě vytvořili.  
  
3. Nainstalujte **požadavky** modul pythonu:  
  
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>  
      
4. Vytvořte soubor s názvem BingCustomSearch.py ve složce, kterou jste vytvořili a zkopírujte do něj následující kód. Nahraďte **YOUR-SUBSCRIPTION-KEY** a **YOUR-vlastní-CONFIG-ID** s subscriptioin klíč a konfigurací ID.  
  
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
  
7. Spuštění kódu pomocí následujícího příkazu.  
  
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Další postup
- [Konfigurace prostředí uživatelského rozhraní](./hosted-ui.md)
- [Použití dekorace značek zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)
