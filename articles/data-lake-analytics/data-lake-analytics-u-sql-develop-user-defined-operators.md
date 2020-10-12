---
title: Vývoj uživatelem definovaných operátorů U-SQL – Azure Data Lake Analytics
description: Naučte se vyvíjet uživatelsky definované operátory, které se použijí a znovu použijí v úlohách Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: how-to
ms.date: 12/05/2016
ms.openlocfilehash: 71cb6e026c81b00e475ae3f4e7fc4958a8de82a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87131799"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Vývoj uživatelem definovaných operátorů U-SQL (Udo)
Tento článek popisuje, jak vyvíjet uživatelsky definované operátory pro zpracování dat v úloze U-SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definování a použití uživatelem definovaného operátoru v U-SQL

### <a name="to-create-and-submit-a-u-sql-job"></a>Vytvoření a odeslání úlohy U-SQL

1. V aplikaci Visual Studio vyberte **soubor > nový projekt > > U-SQL**.
2. Klikněte na **OK**. Visual Studio vytvoří řešení se souborem Script.usql.
3. Z **Průzkumník řešení**rozbalte položku Script. usql a dvakrát klikněte na položku **script.usql.cs**.
4. Do souboru vložte následující kód:

   ```usql
   using Microsoft.Analytics.Interfaces;
   using System.Collections.Generic;
   namespace USQL_UDO
   {
       public class CountryName : IProcessor
       {
           private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
           {
               {
                   "Deutschland", "Germany"
               },
               {
                   "Suisse", "Switzerland"
               },
               {
                   "UK", "United Kingdom"
               },
               {
                   "USA", "United States of America"
               },
               {
                   "中国", "PR China"
               }
           };
           public override IRow Process(IRow input, IUpdatableRow output)
           {
               string UserID = input.Get<string>("UserID");
               string Name = input.Get<string>("Name");
               string Address = input.Get<string>("Address");
               string City = input.Get<string>("City");
               string State = input.Get<string>("State");
               string PostalCode = input.Get<string>("PostalCode");
               string Country = input.Get<string>("Country");
               string Phone = input.Get<string>("Phone");
               if (CountryTranslation.Keys.Contains(Country))
               {
                   Country = CountryTranslation[Country];
               }
               output.Set<string>(0, UserID);
               output.Set<string>(1, Name);
               output.Set<string>(2, Address);
               output.Set<string>(3, City);
               output.Set<string>(4, State);
               output.Set<string>(5, PostalCode);
               output.Set<string>(6, Country);
               output.Set<string>(7, Phone);
               return output.AsReadOnly();
           }
       }
   }
   ```

5. Otevřete **skript. usql**a vložte následující skript U-SQL:

   ```usql
   @drivers =
       EXTRACT UserID      string,
               Name        string,
               Address     string,
               City        string,
               State       string,
               PostalCode  string,
               Country     string,
               Phone       string
       FROM "/Samples/Data/AmbulanceData/Drivers.txt"
       USING Extractors.Tsv(Encoding.Unicode);

   @drivers_CountryName =
       PROCESS @drivers
       PRODUCE UserID string,
               Name string,
               Address string,
               City string,
               State string,
               PostalCode string,
               Country string,
               Phone string
       USING new USQL_UDO.CountryName();

   OUTPUT @drivers_CountryName
       TO "/Samples/Outputs/Drivers.csv"
       USING Outputters.Csv(Encoding.Unicode);
   ```

6. Zadejte účet Data Lake Analytics, Databázi a Schéma.
7. V **Průzkumníku řešení** klikněte pravým tlačítkem na položku **Script.usql** a pak klikněte na možnost **Sestavit skript**.
8. V **Průzkumníku řešení** klikněte pravým tlačítkem na položku **Script.usql** a pak klikněte na položku **Odeslat skript**.
9. Pokud jste se nepřipojili k předplatnému Azure, budete vyzváni k zadání přihlašovacích údajů k účtu Azure.
10. Klikněte na **Odeslat**. Výsledky odeslání a odkaz na úlohu jsou k dispozici v okně výsledků po dokončení odeslání.
11. Kliknutím na tlačítko **aktualizovat** zobrazíte nejnovější stav úlohy a aktualizujete obrazovku.

### <a name="to-see-the-output"></a>Zobrazení výstupu

1. V **Průzkumník serveru**rozbalte položku **Azure**, rozbalte položku **Data Lake Analytics**, rozbalte účet služby Data Lake Analytics, rozbalte položku **účty úložiště**, klikněte pravým tlačítkem na výchozí úložiště a pak klikněte na tlačítko **Průzkumník**.

2. Rozbalte položku ukázky, rozbalte položku výstupy a dvakrát klikněte na položku **Drivers.csv**.

## <a name="next-steps"></a>Další kroky

* [Rozšíření výrazů U-SQL s uživatelským kódem](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Použití Data Lake nástrojů pro Visual Studio pro vývoj aplikací U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
