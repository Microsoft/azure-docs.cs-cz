---
title: Rychlý start Python pro Visual vyhledávání Bing rozhraní API | Microsoft Docs
titleSuffix: Bing Web Search APIs - Cognitive Services
description: Ukazuje, jak nahrát bitovou kopii do rozhraní API vyhledávání Visual Bing a získat zpět přehledy o bitové kopii.
services: cognitive-services
author: swhite-msft
manager: rosh
ms.service: cognitive-services
ms.technology: bing-visual-search
ms.topic: article
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: a520466825eb429e45e0500b52bd7af502c0a38c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343595"
---
# <a name="your-first-bing-visual-search-query-in-python"></a>Svůj první dotaz Bing Visual vyhledávání v Pythonu

Rozhraní API vyhledávání Visual Bing vrací informace o bitovou kopii, kterou zadáte. Můžete zadat bitovou kopii pomocí adresy URL bitové kopie, insights tokenu, nebo tím, že nahrajete image. Informace o těchto možnostech najdete v tématu [co je rozhraní API vyhledávání Visual Bing?](../overview.md) Tento článek ukazuje, odešlou obrázek. Nahrávání obrázku může být užitečné v mobilních situacích, kde pořídit snímek dobře známé významné a vrátit informace o něm. K přehledům může obsahovat třeba trivia o významné. 

Odešlete obrázek místní následující zobrazí formulář data, že je nutné zahrnout text příspěvku. Data formuláře musí obsahovat hlavičku Content-Disposition. Jeho `name` parametr musí být nastaven na "image" a `filename` parametr lze nastavit na libovolný řetězec. Obsah formuláře je binární soubor bitové kopie. Maximální obrázek, který může odeslat je 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Tento článek obsahuje jednoduché konzolové aplikace, která se odešle požadavek Visual Bing rozhraní API služby Search a zobrazí výsledky hledání JSON. Při této aplikace je napsána v Python, rozhraní API je kompatibilní s žádný programovací jazyk, který můžete nastavit požadavků HTTP a analyzovat JSON RESTful webová služba. 

## <a name="prerequisites"></a>Požadavky

Je třeba [Python 3](https://www.python.org/) pro spuštění tohoto kódu.

Pro tento rychlý start, můžete použít [bezplatnou zkušební verzi](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) předplatné nebo placené předplatné klíče.

## <a name="running-the-walkthrough"></a>Spuštění Průvodce

Ke spuštění této aplikace, postupujte takto:

1. Vytvořte nový projekt Python vaše oblíbené IDE nebo editoru.
2. Vytvořte soubor s názvem visualsearch.py a přidejte kód ukazuje tento rychlý start.
3. Nahraďte `SUBSCRIPTION_KEY` hodnotu s svůj klíč předplatného.
3. Nahraďte `imagePath` hodnotu s cestou bitové kopie a nahrajte.
4. Spusťte program.



Následující ukazuje, jak odeslat zprávu pomocí dat vícedílného formuláře v Pythonu.

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

[Získejte přehledy o bitovou kopii pomocí tokenu statistiky](../use-insights-token.md)  
[Kurz jednostránkové aplikace Bing Visual Search](../tutorial-bing-visual-search-single-page-app.md)  
[Přehled Visual vyhledávání v Bingu](../overview.md)  
[Vyzkoušet](https://aka.ms/bingvisualsearchtryforfree)  
[Získat bezplatnou zkušební verzi přístupový klíč](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Referenční dokumentace rozhraní API Visual pro hledání Bing](https://aka.ms/bingvisualsearchreferencedoc)
