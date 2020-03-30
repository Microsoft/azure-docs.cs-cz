---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 24bb7a1fcb1569922fb34034fb3c0d003cdd7061
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175474"
---
## <a name="repeatability-during-copy"></a>Opakovatelnost během kopírování
Při kopírování dat do Azure SQL/SQL Server z jiných úložišť dat je třeba mít na paměti opakovatelnost, aby se zabránilo nezamýšleným výsledkům. 

Při kopírování dat do databáze Azure SQL/SQL Server bude aktivita kopírování ve výchozím nastavení APPEND data nastavena na tabulku jímky ve výchozím nastavení. Například při kopírování dat ze zdroje souboru CSV (data oddělené hodnoty čárky) obsahující dva záznamy do databáze Azure SQL/SQL Server, je to, jak tabulka vypadá:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Předpokládejme, že jste našli chyby ve zdrojovém souboru a aktualizovali množství Down Tube od 2 do 4 ve zdrojovém souboru. Pokud znovu spustíte řez dat pro toto období, najdete dva nové záznamy připojené k Azure SQL/SQL Server Database. Níže uvedené předpokládá, že žádný ze sloupců v tabulce nemá omezení primárního klíče.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Chcete-li tomu zabránit, budete muset zadat sémantiku UPSERT využitím jednoho z níže uvedených mechanismů níže uvedených níže.

> [!NOTE]
> Řez lze spustit automaticky v Azure Data Factory podle zásady opakování zadané.
> 
> 

### <a name="mechanism-1"></a>Mechanismus 1
Vlastnost **sqlWriterCleanupScript** můžete využít k prvnímu provedení akce vyčištění při spuštění řezu. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Skript pro vyčištění by byl proveden nejprve během kopírování pro daný řez, který by odstranil data z tabulky SQL odpovídající tomuto řezu. Aktivita následně vloží data do tabulky SQL. 

Pokud je řez nyní znovu spuštěn, zjistíte, že množství je aktualizováno podle potřeby.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Předpokládejme, že záznam ploché podložky bude odebrán z původního csv. Potom opětovné spuštění řezu by vedlo k následujícímu výsledku: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Nic nového se nemuselo dělat. Aktivita kopírování spustila skript vyčištění, aby odstranila odpovídající data pro tento řez. Pak si přečetl vstup z csv (který pak obsahoval pouze 1 záznam) a vložil jej do tabulky. 

### <a name="mechanism-2"></a>Mechanismus 2
> [!IMPORTANT]
> sliceIdentifierColumnName není v současné době pro datový sklad Azure SQL podporován. 

Dalším mechanismem pro dosažení opakovatelnosti je vyhrazený sloupec **(sliceIdentifierColumnName**) v cílové tabulce. Tento sloupec by byl používán Azure Data Factory k zajištění synchronizace zdrojového a cílového zůstatu. Tento přístup funguje, pokud je flexibilita při změně nebo definování cílového schématu tabulky SQL. 

Tento sloupec by byl používán Azure Data Factory pro účely opakovatelnosti a v procesu Azure Data Factory nebude provádět žádné změny schématu v tabulce. Způsob použití tohoto přístupu:

1. Definujte sloupec typu binární (32) v cílové tabulce SQL. V tomto sloupci by neměla být žádná omezení. Pojmenujme tento sloupec jako "ColumnForADFuseOnly" pro tento příklad.
2. Použijte ji v aktivitě kopírování následujícím způsobem:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory naplní tento sloupec podle potřeby zajistit, aby zdroj a cíl zůstat synchronizovány. Hodnoty tohoto sloupce by neměly být používány mimo tento kontext uživatelem. 

Podobně jako mechanismus 1, aktivita kopírování automaticky nejprve vyčistí data pro daný řez z cílové tabulky SQL a potom spustí aktivitu kopírování normálně vložit data ze zdroje do cíle pro tento řez. 

