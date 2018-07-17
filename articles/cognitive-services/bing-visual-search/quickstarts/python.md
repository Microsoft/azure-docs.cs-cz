---
title: Rychlý start Python pro vizuální vyhledávání Bingu rozhraní API | Dokumentace Microsoftu
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ukazuje, jak k nahrání obrázku do API pro vizuální vyhledávání Bingu a získat přehledy o imagi.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 96bd94e37c75d10726245fbcea7044d4ae2ed07e
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39070371"
---
# <a name="your-first-bing-visual-search-query-in-python"></a>Svůj první dotaz v jazyce Python pro vizuální vyhledávání Bingu

API pro vizuální vyhledávání Bingu vrátí informace o bitovou kopii, která zadáte. Image můžete zadat pomocí adresy URL obrázku, insights, token, nebo nahráním image. Informace o těchto možnostech najdete v tématu [co je API pro vizuální vyhledávání Bingu?](../overview.md) Tento článek ukazuje nahráváním obrázku. Nahrání image může být užitečné v mobilních situacích, kde pořídit snímek dobře známé památek a získat informace o něm. Triviální prvek o památek může obsahovat třeba přehledy. 

Pokud odešlete místní image, následuje data formuláře musí obsahovat v textu příspěvku. Data formuláře musí zahrnovat hlavičku Content-Disposition. Jeho `name` parametr musí být nastaven na "image" a `filename` parametr může být nastaven na libovolný řetězec. Obsah formuláře je binární soubor obrázku. Maximální velikost, kterou můžete k nahrání je 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Tento článek obsahuje jednoduchou konzolovou aplikaci, která odešle žádost o API pro vizuální vyhledávání Bingu a zobrazí výsledky hledání JSON. Zatímco tato aplikace je napsaný v Pythonu, je rozhraní API RESTful webová služba, která je kompatibilní s programovací jazyk, který může vytvářet požadavky HTTP a parsování formátu JSON. 

## <a name="prerequisites"></a>Požadavky

Potřebujete [Python 3](https://www.python.org/) tento kód spustit.

Pro účely tohoto rychlého startu, můžete zadat [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) klíč předplatného, nebo klíč do placené předplatné.

## <a name="running-the-walkthrough"></a>Spuštění Průvodce

Ke spuštění této aplikace, postupujte podle těchto kroků:

1. Vytvoření nového projektu Pythonu v Oblíbené prostředí IDE nebo editoru.
2. Vytvořte soubor s názvem visualsearch.py a přidejte kódu zobrazeného v rámci tohoto rychlého startu.
3. Nahraďte `SUBSCRIPTION_KEY` hodnotu s klíči předplatného.
3. Nahraďte `imagePath` hodnotu s cestou k nahrání obrázku.
4. Spuštění programu.



Následující ukazuje, jak odesílat zprávy pomocí dat vícedílného formuláře v jazyce Python.

```python
"""Bing Visual Search upload image example"""

# Download and install Python at https://www.python.org/
# Run the following in a command console window
# pip3 install requests

import requests, json


BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'

SUBSCRIPTION_KEY = '<yoursubscriptionkeygoeshere>'

HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}

imagePath = '<pathtoyourimagetouploadgoeshere>'

file = {'image' : ('myfile', open(imagePath, 'rb'))}

def main():
    
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())

    except Exception as ex:
        raise ex


def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))



# Main execution
if __name__ == '__main__':
    main()
```


## <a name="next-steps"></a>Další postup

[Získejte přehled o bitovou kopii pomocí tokenu insights](../use-insights-token.md)  
[Kurz nahrávání obrázků pro vizuální vyhledávání Bingu](../tutorial-visual-search-image-upload.md)
[kurzu jednostránkovou aplikaci pro vizuální vyhledávání Bingu](../tutorial-bing-visual-search-single-page-app.md)  
[Přehled pro vizuální vyhledávání Bingu](../overview.md)  
[Vyzkoušejte si to](https://aka.ms/bingvisualsearchtryforfree)  
[Získat bezplatnou zkušební verzi přístupový klíč](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referenční dokumentace API pro vizuální vyhledávání Bingu](https://aka.ms/bingvisualsearchreferencedoc)
