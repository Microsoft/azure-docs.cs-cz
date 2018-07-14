## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Určení definice struktury pro obdélníkové datové sady
Části struktury v datových sadách JSON je **volitelné** obdélníkové tabulky (s řádky a sloupce) v části a obsahuje kolekci sloupců pro tabulku. Části struktury použije pro buď poskytující informace o typu pro převod na typ nebo provádění mapování sloupců. Následující části popisují tyto funkce podrobně. 

Všechny sloupce obsahují následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Definuje chování kopírování, pokud je zdroj BlobSource nebo systému souborů. |Ano |
| type |PreserveHierarchy: zachová hierarchií souborů v cílové složce. Zobrazit typ části převody pro více podrobností, kdy by měla zadáte informace o typu |Ne |
| Jazyková verze |.NET na základě jazykové verze se použije, když je zadán typ a je typ formátu .NET Datetime nebo Datetimeoffset. Výchozí hodnota je "en-us". |Ne |
| Formát |Formátovací řetězec se použije, když je zadán typ a je typ formátu .NET Datetime nebo Datetimeoffset. |Ne |

Následující příklad ukazuje strukturu část JSON pro tabulku, která má tři sloupce userid, název a lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Použijte následující pokyny, kdy se mají zahrnout informace "struktury" a co mají být zahrnuty **struktura** oddílu.

* **Pro strukturovaná data zdroje** , že informace schématu a typu dat úložiště spolu s daty o samotné (zdrojů, jako jsou Azure SQL Server, Oracle, tabulku atd.), je třeba zadat oddílu "struktura" pouze, pokud chcete provést mapování sloupců z konkrétního zdroje sloupce, které mají určité sloupce jímky a jejich názvy se neshodují (podrobnosti najdete v následující části mapování sloupců). 
  
    Jak je uvedeno výše, je v oddílu "struktura" volitelné informace o typu. U strukturovaných zdrojů, informací o typu je již k dispozici jako součást definice datové sady v úložišti dat, proto by neměl zahrnete informací o typu zahrnují oddílu "struktura".
* **Pro schéma na zdroji čtená data (konkrétně objektů blob v Azure)** můžete k ukládání dat bez ukládání žádné schéma nebo typ informací s daty. Pro tyto typy zdrojů dat byste měli zahrnout "struktura" v těchto případech 2:
  * Chcete provést mapování sloupců.
  * Při tato datová sada je zdroj v aktivitě kopírování, můžete zadat informace o typu "struktury" a objektu pro vytváření dat použije tyto informace typu pro převod na nativních typů pro jímku. Zobrazit [přesun dat do a z Azure Blob](../articles/data-factory/v1/data-factory-azure-blob-connector.md) najdete další informace.

### <a name="supported-net-based-types"></a>Podporované. Typy založené na NET
Data factory podporuje následující kompatibilní se Specifikací kompatibilní s .NET na základě typu hodnoty pro poskytnutí informací o typu "struktury" pro schéma na zdroje čtení dat jako objektů blob v Azure.

* Int16
* Datový typ Int32 
* Int64
* Jednoduchá
* Double
* Decimal
* Byte]
* BOOL
* Řetězec 
* Guid
* Datum a čas
* DateTimeOffset
* Časový interval 

Pro typ Datetime a Datetimeoffset také volitelně můžete zadat řetězce "culture" & "format" usnadnit analýzu svůj vlastní řetězec data a času. Najdete v ukázce pro převod typu níže.

