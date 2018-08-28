---
title: Vývoj U-SQL operátory definované uživatelem (UDO) ve službě Azure Data Lake Analytics
description: Zjistěte, jak vývoj uživatelem definovaných operátorů používány a znovu použít v úlohách Azure Data Lake Analytics.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 9751801449eebc5d07ca4bbb9b408d9679fef24e
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43040986"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Vývoj uživatelem definované operátory U-SQL (UDO)
Tento článek popisuje, jak vývoj uživatelem definovaných operátorů ke zpracování dat v rámci úlohy U-SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definice a používání uživatelem definovaný operátor v U-SQL
**Postup vytvoření a odeslání úlohy U-SQL**

1. V sadě Visual Studio vyberte **soubor > Nový > Projekt > projekt v U-SQL**.
2. Klikněte na **OK**. Visual Studio vytvoří řešení se souborem Script.usql.
3. Z **Průzkumníka řešení**rozbalte Script.usql a potom dvakrát klikněte na panel **Script.usql.cs**.
4. Vložte následující kód do souboru:

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
6. Otevřít **Script.usql**a vložte následující skript U-SQL:

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
7. Zadejte účet Data Lake Analytics, Databázi a Schéma.
8. V **Průzkumníku řešení** klikněte pravým tlačítkem na položku **Script.usql** a pak klikněte na možnost **Sestavit skript**.
9. V **Průzkumníku řešení** klikněte pravým tlačítkem na položku **Script.usql** a pak klikněte na položku **Odeslat skript**.
10. Pokud jste se nepřipojili k předplatnému Azure, budete vyzváni k zadání přihlašovacích údajů k účtu Azure.
11. Klikněte na tlačítko **odeslat**. Výsledky odeslání a odkaz na úlohu jsou dostupné v okně výsledků při dokončení odesílání.
12. Klikněte na tlačítko **aktualizovat** tlačítko, které chcete zobrazit nejnovější stav úlohy a aktualizovat obrazovku.

**Postup zobrazení výstupu**

1. Z **Průzkumníka serveru**, rozbalte **Azure**, rozbalte **Data Lake Analytics**, rozbalte účet Data Lake Analytics, rozbalte **účty úložiště**, klikněte pravým tlačítkem na výchozí úložiště a pak klikněte na tlačítko **Explorer**.
2. Rozbalte ukázky, rozbalte výstupy a potom dvakrát klikněte na panel **ovladače.csv**.

## <a name="see-also"></a>Další informace najdete v tématech
* [Rozšíření U-SQL výrazy s uživatelský kód](https://msdn.microsoft.com/library/azure/mt621316.aspx)
* [Pomocí nástrojů Data Lake pro Visual Studio pro vývoj aplikací v U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
