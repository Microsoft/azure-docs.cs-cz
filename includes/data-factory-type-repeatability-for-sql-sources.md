---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 9447cec55c53861ca57d5416a91ffefd35fdd20b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91571956"
---
## <a name="repeatability-during-copy"></a>Opakovatelnost během kopírování
Při kopírování dat do Azure SQL/SQL Server z jiných úložišť dat je nutné mít na paměti, aby se zabránilo nezamýšleným výsledkům. 

Když kopírujete data do databáze Azure SQL nebo SQL Server, aktivita kopírování bude ve výchozím nastavení automaticky připojit datovou sadu k tabulce jímky. Když například kopírujete data ze zdroje souborů CSV (data oddělený čárkami), který obsahuje dva záznamy do služby Azure SQL/SQL Server Database, vypadá to, že tabulka vypadá takto:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Předpokládejme, že ve zdrojovém souboru byly zjištěny chyby a v zdrojovém souboru bylo aktualizováno množství z trubky z 2 na 4. Pokud znovu spustíte datový řez pro tuto dobu, najdete dva nové záznamy připojené k databázi Azure SQL/SQL Server. Níže uvedený postup předpokládá, že žádný ze sloupců v tabulce nemá omezení PRIMARY KEY.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Abyste tomu předešli, budete muset určit sémantiku UPSERT pomocí jednoho z následujících dvou mechanismů uvedených níže.

> [!NOTE]
> Řez se dá v Azure Data Factory znovu spustit automaticky podle zadaných zásad opakování.
> 
> 

### <a name="mechanism-1"></a>Mechanismus 1
Vlastnost **sqlWriterCleanupScript** můžete využít k první provedení akce čištění při spuštění řezu. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Skript vyčištění se spustí jako první během kopírování pro daný řez, který by odstranil data z tabulky SQL odpovídající danému řezu. Tato aktivita následně vloží data do tabulky SQL. 

Pokud je řez teď znovu spuštěný, zjistíte, že se množství aktualizuje podle potřeby.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Předpokládejme, že se záznam plochého mycího pračky odebírá z původního sdíleného svazku clusteru. Po opětovném spuštění řezu by se vytvořil následující výsledek: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Nic nového se nemuselo dělat. Aktivita kopírování spustila skript vyčištění, který odstraní odpovídající data pro daný řez. Pak Přečtěte vstup ze sdíleného svazku clusteru (který pak obsahuje jenom 1 záznam) a vloží ho do tabulky. 

### <a name="mechanism-2"></a>Mechanismus 2
> [!IMPORTANT]
> sliceIdentifierColumnName se v tuto chvíli nepodporuje pro Azure synapse Analytics. 

Dalším mechanismem, který se má dosáhnout opakovatelnosti, je vytvoření vyhrazeného sloupce (**sliceIdentifierColumnName**) v cílové tabulce. Tento sloupec Azure Data Factory použít k tomu, aby bylo zajištěno, že zůstane synchronizován zdroj a cíl. Tento přístup funguje v případě, že dojde k flexibilitě při změně nebo definování schématu cílové tabulky SQL. 

Tento sloupec bude použit Azure Data Factory pro účely opakovatelnosti a v Azure Data Factory procesu neprovede v tabulce žádné změny schématu. Způsob použití tohoto přístupu:

1. Definujte sloupec typu Binary (32) v cílové tabulce SQL. Pro tento sloupec by neměla existovat žádná omezení. Pojmenujte tento sloupec jako ColumnForADFuseOnly v tomto příkladu.
2. Použijte ji v aktivitě kopírování následujícím způsobem:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory tento sloupec naplní podle potřeby, aby se zajistila synchronizace zdroje a cíle. Hodnoty tohoto sloupce by se neměly používat mimo tento kontext uživatelem. 

Podobně jako u mechanismu 1, aktivita kopírování automaticky vyčistí data pro daný řez z cílové tabulky SQL a pak spustí aktivitu kopírování normálně za účelem vložení dat ze zdroje do cílového umístění pro daný řez. 

