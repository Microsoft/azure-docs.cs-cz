## <a name="repeatability-during-copy"></a>Opakovatelnosti během kopírování
Při kopírování dat do Azure SQL nebo SQL Server z jiných dat ukládá třeba opakovatelnosti mějte na paměti, aby se zabránilo neúmyslnému výsledků. 

Při kopírování dat do služby Azure SQL nebo SQL Server Database, aktivitu kopírování, která bude ve výchozím nastavení připojení sady dat do tabulky jímky ve výchozím nastavení. Například při kopírování dat ze zdroje souboru CSV (data hodnoty oddělené čárkami) obsahující dva záznamy do služby Azure SQL nebo SQL Server Database, to vypadá v tabulce:

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Předpokládejme, že byly nalezeny chyby ve zdrojovém souboru a aktualizovat množství trubky snížit z 2 na 4 ve zdrojovém souboru. Pokud znovu spustíte datový řez za toto období, zjistíte dva nové záznamy připojenou k databázi Azure SQL nebo SQL Server. Níže předpokládá žádný sloupců v tabulce nemá omezení primárního klíče.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Abyste tomu předešli, budete muset zadat UPSERT sémantika s využitím jeden z pod 2 mechanismy, které jsou uvedeny níže.

> [!NOTE]
> Řez opětovně lze spustit automaticky ve službě Azure Data Factory podle určené zásady opakování.
> 
> 

### <a name="mechanism-1"></a>Mechanismus 1
Můžete využít **sqlWriterCleanupScript** vlastnost nejprve provést akce vyčištění při spuštění řezu. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Skript pro vyčištění by byl proveden první během kopírování pro danou řez, který by odstranit data z tabulky SQL odpovídající této řez. Aktivity následně vložte data do tabulky SQL. 

Pokud je řez teď znovu spustit, budete se najít že množství se aktualizuje jako požadovaný.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Předpokládejme, že záznam pro plochý podložku Odebereme z původní sdílený svazek clusteru. Opětovné spuštění řezu byste mohli vytvořit následující výsledek: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```
Není nic nového museli provést. Aktivita kopírování spustili skript pro vyčištění odstranit odpovídajících dat pro tento řez. Potom jej číst vstup ze souboru csv (což je obsaženy pouze 1 záznam) a vloženy do tabulky. 

### <a name="mechanism-2"></a>Mechanismus 2
> [!IMPORTANT]
> sliceIdentifierColumnName není v tuto chvíli podporováno pro službu Azure SQL Data Warehouse. 

Jiný mechanismus opakovatelnosti dosáhnout spočívá v použití vyhrazené sloupec (**sliceIdentifierColumnName**) v cílové tabulce. V tomto sloupci se použije službou Azure Data Factory k zajištění, že zdroj a cíl zůstat synchronizováni. Tento přístup funguje, když je flexibilitu při změně nebo definování schématu cílové tabulky SQL. 

V tomto sloupci se použije službou Azure Data Factory pro účely opakovatelnost a v procesu služby Azure Data Factory neprovede žádné změny schématu tabulky. Způsob, jak pomocí tohoto postupu:

1. Definujte sloupce binárního typu (32) v cílové tabulce SQL. Měla by existovat bez omezení na tomto sloupci. V tomto příkladu pojmenujeme v tomto sloupci jako "ColumnForADFuseOnly".
2. Použije v aktivitě kopírování následujícím způsobem:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "ColumnForADFuseOnly"
    }
    ```

Azure Data Factory se vyplní v tomto sloupci podle jeho potřeba zajistit, že zdroj a cíl zůstat synchronizováni. Hodnoty daného sloupce by neměly používat mimo tento kontext uživatelem. 

Podobně jako mechanismus 1 aktivitu kopírování, která bude automaticky nejprve vyčištění dat pro danou řezu cílové tabulky SQL a pak spusťte aktivity kopírování obvykle k vložení dat ze zdroje do cíle pro tuto řez. 

