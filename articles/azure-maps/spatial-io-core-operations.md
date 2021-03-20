---
title: Základní vstupně-výstupní operace | Mapy Microsoft Azure
description: Naučte se efektivně číst a zapisovat XML a data s oddělovači pomocí základních knihoven z modulu prostorového vstupu/výstupu.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 08966549d2c4d2bf701ee0dbfc5ed24f202396fc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891372"
---
# <a name="core-io-operations"></a>Základní vstupně-výstupní operace

Kromě poskytování nástrojů pro čtení prostorových datových souborů zpřístupňuje modul pro prostorové vstupně-výstupní operace základní knihovny pro čtení a zápis XML a data s oddělovači rychleji a efektivně.

`atlas.io.core`Obor názvů obsahuje dvě třídy nízké úrovně, které můžou rychle číst a zapisovat data CSV a XML. Tyto základní třídy elektricky doplní čtečky prostorových dat a zapisovače v modulu prostorového vstupu/výstupu. Můžete je používat k přidání další podpory pro čtení a zápis souborů CSV nebo XML.
 
## <a name="read-delimited-files"></a>Čtení souborů s oddělovači

`atlas.io.core.CsvReader`Třída čte řetězce, které obsahují s oddělenými datovými sadami. Tato třída poskytuje dvě metody pro čtení dat:

- `read`Funkce načte celou datovou sadu a vrátí dvojrozměrné pole řetězců reprezentující všechny buňky sady dat s oddělovači.
- `getNextRow`Funkce přečte jednotlivé řádky textu v sadě dat s oddělovači a vrátí pole řetězců reprezentující všechny buňky v daném řádku sady dat. Uživatel může řádek zpracovat a uvolnit z tohoto řádku veškerou nepotřebnou paměť před zpracováním dalšího řádku. Funkce je tedy mnohem efektivnější.

Ve výchozím nastavení čtečka použije jako oddělovač znak čárky. Oddělovač je však možné změnit na libovolný jeden znak nebo nastavit na `'auto'` . Při nastavení na `'auto'` je čtenář analyzovat první řádek textu v řetězci. Pak se v následující tabulce vybere nejběžnější znak, který použijete jako oddělovač.

| Oddělovač | Znak |
| :-- | :-- |
| Čárka | `,` |
| Karta | `\t` |
| Příkazem | `|` |

Tento čtenář také podporuje kvalifikátory textu, které se používají ke zpracování buněk obsahujících znak oddělovače. Znak uvozovky ( `'"'` ) je výchozí kvalifikátor textu, ale může být změněn na libovolný jeden znak.

## <a name="write-delimited-files"></a>Zapisovat soubory s oddělovači

`atlas.io.core.CsvWriter`Zapisuje pole objektů jako oddělený řetězec. Libovolný jeden znak lze použít jako oddělovač nebo textový kvalifikátor. Výchozí oddělovač je čárka ( `','` ) a výchozí kvalifikátor textu je znak uvozovky ( `'"'` ).

Chcete-li použít tuto třídu, postupujte podle následujících kroků:

- Vytvořte instanci třídy a volitelně nastavte vlastní oddělovač nebo kvalifikátor textu.
- Zapište do třídy data pomocí `write` funkce nebo `writeRow` funkce. Pro `write` funkci předejte dvojrozměrné pole objektů reprezentujících více řádků a buněk. Chcete-li použít `writeRow` funkci, předejte pole objektů reprezentující řádek dat s více sloupci.
- Zavolejte `toString` funkci pro načtení řetězce s oddělovači. 
- Volitelně volejte metodu, `clear` aby modul pro zápis znovu natlačil a snížil jeho přidělení prostředků, nebo zavolejte `delete` metodu pro uvolnění instance zapisovače.

> [!Note]
> Počet zapsaných sloupců bude omezen na počet buněk v prvním řádku dat předaných do zapisovače.

## <a name="read-xml-files"></a>Čtení souborů XML

`atlas.io.core.SimpleXmlReader`Třída je rychlejší při analýze souborů XML než `DOMParser` . `atlas.io.core.SimpleXmlReader`Třída ale vyžaduje, aby soubory XML byly ve správném formátu. Soubory XML, které nejsou ve správném formátu, například chybějící ukončovací značky, budou nejspíš způsobit chybu.

Následující kód ukazuje, jak použít `SimpleXmlReader` třídu k analýze řetězce XML do objektu JSON a jeho serializaci do požadovaného formátu.

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

`atlas.io.core.SimpleXmlWriter`Třída zapisuje ve správném formátu XML do paměti efektivní způsob.

Následující kód ukazuje, jak použít `SimpleXmlWriter` třídu k vygenerování dobře formátovaného řetězce XML.

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

Vygenerovaný kód XML z výše uvedeného kódu by vypadal jako následující.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Další kroky

Další informace o třídách a metodách, které se používají v tomto článku:

[CsvReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

[CsvWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

[SimpleXmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

[SimpleXmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Další ukázky kódu pro přidání do vašich map najdete v následujících článcích:

[Podrobnosti o podporovaných formátech dat](spatial-io-supported-data-format-details.md)