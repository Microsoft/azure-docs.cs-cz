---
title: 'Rychlý start: Vytvoření dotazu pro vizuální vyhledávání, Python – Vizuální vyhledávání Bingu'
titleSuffix: Azure Cognitive Services
description: Ukazuje, jak nahrát obrázek do rozhraní API pro vizuální vyhledávání Bingu a získat zpět přehledy o obrázku.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
ms.openlocfilehash: 16d3d0ddf77e37e32cc50961a3870b820ac2748e
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2018
ms.locfileid: "48884237"
---
# <a name="quickstart-your-first-bing-visual-search-query-in-python"></a>Rychlý start: Váš první dotaz Vizuálního vyhledávání Bingu v Pythonu

Rozhraní API pro Vizuální vyhledávání Bingu vrátí informace o obrázku, který poskytnete. Obrázek můžete zadat pomocí adresy URL obrázku, tokenu insights nebo nahráním obrázku. Informace o těchto možnostech najdete v článku [Co je rozhraní API pro vizuální vyhledávání Bingu?](../overview.md) Tento článek ukazuje nahrávání obrázku. Nahrání obrázku může být užitečné v situacích s mobilním zařízením, když pořídíte snímek dobře známé pamětihodnosti a získáte o ní informace. Přehledy můžou například obsahovat základní informace o pamětihodnosti. 

Pokud nahrajete místní obrázek, ukazuje následující kód data formuláře, která musíte zahrnout do textu žádosti POST. Data formuláře musí obsahovat hlavičku Content-Disposition. Jeho parametr `name` musí být nastavený na "image" a parametr `filename` může být nastavený na libovolný řetězec. Obsah formuláře je binární soubor obrázku. Maximální velikost obrázku, kterou můžete nahrát, je 1 MB. 

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Tento článek obsahuje jednoduchou konzolovou aplikaci, která pošle žádost do rozhraní API pro vizuální vyhledávání Bingu a zobrazí výsledky hledání JSON. Aplikace je sice napsaná v Pythonu, ale rozhraní API je webová služba RESTful kompatibilní s každým programovacím jazykem, který dokáže provádět požadavky HTTP a parsovat JSON. 

## <a name="prerequisites"></a>Požadavky

Abyste mohli tento kód spustit, potřebujete [Python 3](https://www.python.org/).

Pro tento rychlý start můžete použít klíč [bezplatné zkušební verze](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) předplatného, nebo klíč placeného předplatného.

## <a name="running-the-walkthrough"></a>Spuštění průvodce

Pokud chcete tuto aplikaci spustit, postupujte následovně:

1. Vytvořte nový projekt v jazyce Python v oblíbeném integrovaném vývojovém prostředí nebo editoru.
2. Vytvořte soubor s názvem visualsearch.py a přidejte do něj kód zobrazený v tomto rychlém startu.
3. Hodnotu `SUBSCRIPTION_KEY` nahraďte klíčem předplatného.
3. Hodnotu `imagePath` nahraďte cestou nahrávaného obrázku.
4. Spusťte program.



Dále je ukázáno, jak poslat zprávu pomocí dat vícedílného formuláře v Pythonu.

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


## <a name="next-steps"></a>Další kroky

[Získání přehledu o obrázku pomocí tokenu insights](../use-insights-token.md)  
[Kurz nahrávání obrázků Vizuálního vyhledávání Bingu](../tutorial-visual-search-image-upload.md)
[Kurz k jednostránkové aplikaci Vizuálního vyhledávání Bingu](../tutorial-bing-visual-search-single-page-app.md)  
[Přehled Vizuálního vyhledávání Bingu](../overview.md)  
[Vyzkoušejte si to](https://aka.ms/bingvisualsearchtryforfree)  
[Získání bezplatné zkušební verze přístupového klíče](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Reference k rozhraní API pro vizuální vyhledávání Bingu](https://aka.ms/bingvisualsearchreferencedoc)
