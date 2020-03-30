---
title: Operace v provozu jádra | Mapy Microsoft Azure
description: Naučte se efektivně číst a zapisovat data XML a oddělování dat pomocí základních knihoven z prostorového vomodulu.
author: philmea
ms.author: philmea
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 0b8fe1b319dc480879944d28f10645025a8cb38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371439"
---
# <a name="core-io-operations"></a>Operace core IO

Kromě poskytování nástrojů pro čtení souborů prostorových dat poskytuje prostorový vozítko modul zpřístupňuje základní základní knihovny pro rychlé a efektivní čtení a zápis dat XML a oddělených dat.

Obor `atlas.io.core` názvů obsahuje dvě třídy nižší úrovně, které mohou rychle číst a zapisovat data CSV a XML. Tyto základní třídy pohánějí čtečky prostorových dat a zapisovače v modulu Prostorové vi. Neváhejte je použít k přidání další podpory čtení a zápisu pro soubory CSV nebo XML.
 
## <a name="read-delimited-files"></a>Čtení oddělených souborů

Třída `atlas.io.core.CsvReader` čte řetězce, které obsahují oddělené datové sady. Tato třída poskytuje dvě metody pro čtení dat:

- Funkce `read` přečte úplnou sadu dat a vrátí dvojrozměrné pole řetězců představující všechny buňky oddělené datové sady.
- Funkce `getNextRow` přečte každý řádek textu v oddělené datové sadě a vrátí pole řetězce představující všechny buňky v této řadě datových sad. Uživatel může zpracovat řádek a zlikvidovat všechny nepotřebné paměti z tohoto řádku před zpracováním další řádek. Takže funkce je efektivnější paměti.

Ve výchozím nastavení bude čtenář používat znak čárky jako oddělovač. Oddělovač však lze změnit na libovolný jednotlivý `'auto'`znak nebo nastavit na . Pokud je `'auto'`nastavena na , bude čtenář analyzovat první řádek textu v řetězci. Poté vybere nejběžnější znak z níže uvedené tabulky, který se použije jako oddělovač.

| | |
| :-- | :-- |
| Čárka | `,` |
| Karta | `\t` |
| Potrubí | `|` |

Tato čtečka také podporuje kvalifikátory textu, které se používají ke zpracování buněk, které obsahují znak oddělovače. Znak citace`'"'`( ) je výchozí textový kvalifikátor, ale lze jej změnit na libovolný jednotlivý znak.

## <a name="write-delimited-files"></a>Zápis souborů s oddělovači

Zapíše `atlas.io.core.CsvWriter` pole objektů jako řetězec s oddělovači. Libovolný jeden znak lze použít jako oddělovač nebo textový kvalifikátor. Výchozí oddělovač je čárka (`','`) a výchozí`'"'`textový kvalifikátor je znak citace ( ).

Chcete-li použít tuto třídu, postupujte podle následujících kroků:

- Vytvořte instanci třídy a volitelně nastavte vlastní oddělovač nebo textový kvalifikátor.
- Zápis dat do třídy `write` pomocí `writeRow` funkce nebo funkce. Pro `write` funkci předavte dvojrozměrné pole objektů představujících více řádků a buněk. Chcete-li `writeRow` funkci použít, předaj pole objektů představujících řádek dat s více sloupci.
- Volání `toString` funkce načíst řetězec s oddělovače. 
- Volitelně volání `clear` metody, aby se zapisovač opakovaně použitelné a `delete` snížit jeho přidělení prostředků nebo volání metody k vyřazení instance writer.

> [!Note]
> Počet zapsaných sloupců bude omezen na počet buněk v prvním řádku dat předaných zapisovateli.

## <a name="read-xml-files"></a>Čtení souborů XML

Třída `atlas.io.core.SimpleXmlReader` je rychlejší při analýzě `DOMParser`souborů XML než . `atlas.io.core.SimpleXmlReader` Třída však vyžaduje, aby byly soubory XML dobře formátovány. Soubory XML, které nejsou dobře formátovány, například chybějící uzavírací značky, pravděpodobně způsobí chybu.

Následující kód ukazuje, jak `SimpleXmlReader` použít třídu k analýzě řetězce XML do objektu JSON a serializovat jej do požadovaného formátu.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>Zápis souborů XML

Třída `atlas.io.core.SimpleXmlWriter` zapisuje dobře formátovaný kód XML způsobem, který je efektivní v paměti.

Následující kód ukazuje, jak `SimpleXmlWriter` použít třídu ke generování dobře formátovaný řetězec XML.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

Vygenerovaný XML z výše uvedeného kódu bude vypadat takto.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách použitých v tomto článku:

> [!div class="nextstepaction"]
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Další ukázky kódu, které chcete přidat do map, naleznete v následujících článcích:

> [!div class="nextstepaction"]
> [Podrobnosti o podporovaném formátu dat](spatial-io-supported-data-format-details.md)