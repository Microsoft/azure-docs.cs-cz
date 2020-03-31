---
title: Vývoj uživatelem definovaných operátorů U-SQL – Azure Data Lake Analytics
description: Zjistěte, jak vyvinout uživatelem definované operátory, které se mají používat a znovu používat v azure data lake analytics úlohy.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: b2d1293b06b4d8791138ed666bc3cb4abe3adf40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71316544"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Vývoj uživatelem definovaných operátorů U-SQL (UDO)
Tento článek popisuje, jak vyvíjet uživatelem definované operátory pro zpracování dat v úloze U SQL.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definování a použití uživatelem definovaného operátoru v U-SQL
**Vytvoření a odeslání úlohy U-SQL**

1. Ve Visual Studiu vyberte **Soubor > Nový > projekt > U-SQL Project**.
2. Klikněte na tlačítko **OK**. Visual Studio vytvoří řešení se souborem Script.usql.
3. V **Průzkumníku řešení**rozbalte soubor Script.usql a poklepejte na **Script.usql.cs**.
4. Vložte do souboru následující kód:

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
6. Otevřete **soubor Script.usql**a vložte následující skript U-SQL:

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
10. Pokud jste se k předplatnému Azure nepřipojili, budete vyzváni k zadání přihlašovacích údajů k účtu Azure.
11. Klepněte na **tlačítko Odeslat**. Výsledky odeslání a odkaz na úlohu jsou k dispozici v okně Výsledky po dokončení odeslání.
12. Kliknutím na tlačítko **Aktualizovat** zobrazíte nejnovější stav úlohy a aktualizujte obrazovku.

**Chcete-li zobrazit výstup**

1. V **Průzkumníkovi serveru**rozbalte **Azure**, rozbalte Data **Lake Analytics**, rozbalte účet Data Lake Analytics, **rozbalte účty úložiště**, klikněte pravým tlačítkem myši na výchozí úložiště a potom klikněte na **Explorer**.
2. Rozbalte ukázky, rozbalte výstupy a poklepejte na **položku Drivers.csv**.

## <a name="see-also"></a>Viz také
* [Rozšíření výrazů U-SQL pomocí uživatelského kódu](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Použití nástrojů data lake pro Visual Studio pro vývoj aplikací U-SQL](data-lake-analytics-data-lake-tools-get-started.md)
