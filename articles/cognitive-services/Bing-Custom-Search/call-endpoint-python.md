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
ms.openlocfilehash: 889762ae0b401438f25546738268c584ddd58389
ms.sourcegitcommit: 15bfce02b334b67aedd634fa864efb4849fc5ee2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "35343815"
---
# <a name="call-bing-custom-search-endpoint-python"></a>Volání koncového bodu pro vlastní vyhledávání Bingu (Python)

Tento rychlý start ukazuje, jak si vyžádat výsledky hledání s použitím jazyka Python k volání koncového bodu pro vlastní vyhledávání Bingu z vaší instance vlastního hledání. 

## <a name="prerequisites"></a>Požadavky
K dokončení tohoto rychlého startu je potřeba:

- Instanci vlastního vyhledávání. Zobrazit [vytvořit první instanci vlastního vyhledávání Bingu](quick-start.md).

-  [Python](https://www.python.org/) nainstalované.

- A [účet rozhraní API služeb Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) s **rozhraní API Bingu pro vyhledávání**. [Bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-custom-search) stačí pro účely tohoto rychlého startu. Budete potřebovat přístupový klíč získáte při aktivaci vaší bezplatné zkušební verze, nebo můžete použít klíč placené předplatné z řídicího panelu Azure. 

## <a name="run-the-code"></a>Spuštění kódu

K volání koncového bodu vlastní vyhledávání Bingu, postupujte podle těchto kroků:

1. Vytvořte složku pro váš kód.
2. Z příkazového řádku správce nebo terminálu přejděte do složky, kterou jste právě vytvořili.
3. Nainstalujte **požadavky** modul pythonu:
    <pre>
    pip install pipenv
    pipenv install requests
    </pre>
7. Vytvořte soubor BingCustomSearch.py a zkopírujte do něj následující kód.
8. Nahraďte **YOUR-SUBSCRIPTION-KEY** a **YOUR-vlastní-CONFIG-ID** identifikátorem klíče a konfigurace (viz krok 1).

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
9. Spuštění kódu pomocí následujícího příkazu.
    ```
    python BingCustomSearch.py
    ```

## <a name="next-steps"></a>Další postup
- [Konfigurace prostředí uživatelského rozhraní](./hosted-ui.md)
- [Použití dekorace značek zvýraznění textu](./hit-highlighting.md)
- [Stránka webové stránky](./page-webpages.md)