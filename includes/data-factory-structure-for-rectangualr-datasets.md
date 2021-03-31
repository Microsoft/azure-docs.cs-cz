---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "67175677"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Určení definice struktury pro pravoúhlé datové sady
Oddíl Structure v datových sadách JSON je **volitelný** oddíl pro obdélníkové tabulky (s řádky & sloupci) a obsahuje kolekci sloupců pro tabulku. Použijete oddíl Structure pro poskytování informací o typu pro převody typů nebo pro mapování sloupců. Následující části podrobně popisují tyto funkce. 

Každý sloupec obsahuje následující vlastnosti:

| Vlastnost | Popis | Vyžadováno |
| --- | --- | --- |
| name |Název sloupce |Yes |
| typ |Datový typ sloupce Další podrobnosti o tom, kdy byste měli zadat informace o typu, najdete v části převody typů níže. |No |
| jazyková verze |Jazyková verze založená na rozhraní .NET, která se má použít při zadání typu a je typu DateTime nebo DateTimeOffset typu .NET. Výchozí hodnota je "en-US". |No |
| formát |Řetězec formátu, který má být použit, pokud je zadán typ a je typu DateTime nebo DateTimeOffset typu .NET. |No |

Následující ukázka ukazuje JSON oddílu struktury pro tabulku, která má tři sloupce UserID, Name a LastLoginDate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Pokud chcete zahrnout informace o struktuře a zahrnout do části **Struktura** , použijte prosím následující pokyny.

* **U strukturovaných zdrojů dat** , které ukládají schéma dat a informace o typech spolu s samotnými daty (zdroje jako SQL Server, Oracle, tabulka Azure atd.), byste měli zadat oddíl Structure (struktura) pouze v případě, že chcete provést mapování sloupce pro konkrétní zdrojové sloupce na konkrétní sloupce v jímky a jejich názvy nejsou stejné (podrobnosti najdete v části mapování sloupců níže). 
  
    Jak je uvedeno výše, informace o typu jsou v části Structure volitelné. V případě strukturovaných zdrojů jsou informace o typech již k dispozici v rámci definice datové sady v úložišti dat, takže byste neměli zahrnovat informace o typu, pokud zahrnete oddíl "Structure".
* **Pro schéma při čtení zdrojů dat (konkrétně blob Azure)**  se můžete rozhodnout ukládat data bez uložení informací o schématu nebo typu s daty. Pro tyto typy zdrojů dat byste měli zahrnout "Structured" v následujících dvou případech:
  * Chcete provést mapování sloupce.
  * Pokud je datová sada zdrojem v aktivitě kopírování, můžete zadat informace o typu v části struktura a Data Factory budou používat tyto informace o typu pro převod na nativní typy jímky. Další informace najdete v článku o [přesunu dat do a z objektu BLOB v Azure](../articles/data-factory/v1/data-factory-azure-blob-connector.md) .

### <a name="supported-net-based-types"></a>Doložen. Typy založené na síti
Data Factory podporuje následující hodnoty typů založené na specifikaci CLS kompatibilní se specifikací CLS pro poskytování informací o typu ve struktuře pro čtení zdrojů dat, jako je Azure Blob.

* Int16
* Int32 
* Int64
* Jednoduché
* dvojité
* Decimal
* Byte []
* Logická hodnota
* Řetězec 
* Identifikátor GUID
* Datum a čas
* DateTimeOffset
* Časový interval 

Pro DateTime & DateTimeOffset můžete také volitelně zadat řetězec "Culture" & "Format", abyste usnadnili analýzu vlastního řetězce DateTime. Viz Ukázka pro převod typu níže.

