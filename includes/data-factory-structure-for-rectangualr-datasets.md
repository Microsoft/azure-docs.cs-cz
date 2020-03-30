---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 1ab404b838af65dcb75395dfeee1ca0553e497a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175677"
---
## <a name="specifying-structure-definition-for-rectangular-datasets"></a>Určení definice struktury pro obdélníkové datové sady
Část struktury v datových sadách JSON je **volitelná** část pro obdélníkové tabulky (s řádky & sloupců) a obsahuje kolekci sloupců pro tabulku. Oddíl Struktura použijete k poskytnutí informací o typu pro převody typů nebo k mapování sloupců. Následující části popisují tyto funkce podrobně. 

Každý sloupec obsahuje následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název sloupce. |Ano |
| type |Datový typ sloupce. Další podrobnosti o tom, kdy byste měli zadat informace o typu, naleznete v části převody typů níže. |Ne |
| jazyková verze |Jazyková verze založená na rozhraní .NET, která má být použita při zadání typu typu .NET, je typu Datetime nebo Datetimeoffset. Výchozí hodnota je "en-us". |Ne |
| formát |Formátovací řetězec, který se má použít při zadání typu a je typu .NET Datetime nebo Datetimeoffset. |Ne |

Následující ukázka ukazuje část struktury JSON pro tabulku, která má tři sloupce userid, name a lastlogindate.

```json
"structure": 
[
    { "name": "userid"},
    { "name": "name"},
    { "name": "lastlogindate"}
],
```

Použijte následující pokyny, kdy zahrnout informace o "struktuře" a co zahrnout do části **struktury.**

* **Pro strukturované zdroje dat,** které ukládají schéma dat a informace o typu spolu se samotnými daty (zdroje jako SQL Server, Oracle, tabulka Azure atd.), byste měli zadat oddíl "struktura" pouze v případě, že chcete mapování sloupců konkrétních zdrojových sloupců na konkrétní sloupce v jímce a jejich názvy nejsou stejné (viz podrobnosti v části mapování sloupců níže). 
  
    Jak bylo uvedeno výše, informace o typu jsou volitelné v části "struktura". U strukturovaných zdrojů jsou informace o typu již k dispozici jako součást definice datové sady v úložišti dat, takže byste neměli uvádět informace o typu, pokud zahrnete oddíl "struktura".
* **Pro schéma na zdroje dat pro čtení (konkrétně objekt blob Azure)** můžete ukládat data bez ukládání jakéhokoli schématu nebo zadejte informace s daty. Pro tyto typy zdrojů dat byste měli zahrnout "strukturu" v následujících 2 případech:
  * Chcete provést mapování sloupců.
  * Pokud je datová sada zdrojem v aktivitě kopírování, můžete zadat informace o typu v "structure" a data factory použije tyto informace o typu pro převod na nativní typy pro jímku. Další informace najdete v článku Přesunutí dat do a z článku [azure blob.](../articles/data-factory/v1/data-factory-azure-blob-connector.md)

### <a name="supported-net-based-types"></a>Podporovány. Typy založené na NET
Factory podporuje následující cls kompatibilní .NET založené hodnoty typu pro poskytování informací o typu v "struktura" pro schéma na čtení zdrojů dat, jako je objekt blob Azure.

* Int16
* Int32 
* Int64
* Single
* Double
* Desetinné číslo
* Bajt[]
* Logická hodnota
* Řetězec 
* Identifikátor GUID
* Datum a čas
* Datetimeoffset
* Časový interval 

Pro Datetime & Datetimeoffset můžete také volitelně zadat řetězec "jazyková verze" & "formát", abyste usnadnili analýzu vlastního řetězce Datetime. Viz ukázka převodu typu níže.

