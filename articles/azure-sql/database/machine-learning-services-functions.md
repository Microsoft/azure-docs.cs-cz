---
title: Psaní pokročilých funkcí R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Přečtěte si, jak napsat funkci R pro pokročilý statistickou výpočet v Azure SQL Database pomocí Machine Learning Services (Preview).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a7d06b08ce5047c24dd09278a16b9646dcb4e1f7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048214"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Zápis pokročilých funkcí R v Azure SQL Database pomocí Machine Learning Services (Preview)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Tento článek popisuje, jak do uložené procedury SQL vložit matematické a obslužné funkce jazyka R. Pokročilé statistické funkce, které jsou komplikované k implementaci v T-SQL, se dají provádět v R s pouze jedním řádkem kódu.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [účet](https://azure.microsoft.com/free/) před tím, než začnete.

- Chcete-li spustit vzorový kód v těchto cvičeních, musíte nejprve mít [Azure SQL Database s povolenou Machine Learning Services (s R)](machine-learning-services-overview.md) .

- Ujistěte se, že jste nainstalovali nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Můžete spouštět skripty R pomocí jiných nástrojů pro správu databáze nebo dotazů, ale v tomto rychlém startu použijete SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Vytvořit uloženou proceduru pro generování náhodných čísel

Pro jednoduchost použijte `stats` balíček R, který je nainstalovaný a načtený ve výchozím nastavení, s Azure SQL Database pomocí Machine Learning Services (Preview). Balíček obsahuje stovky funkcí pro běžné statistické úkoly, které jsou mimo ně `rnorm` funkce. Tato funkce generuje zadaný počet náhodných čísel pomocí normálního rozdělení s ohledem na směrodatnou odchylku a způsob.

Například následující kód R vrátí 100 čísel ve střední části 50 s ohledem na směrodatnou odchylku 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Pro volání tohoto řádku R z T-SQL spusťte `sp_execute_external_script` a přidejte funkci R do parametru skriptu jazyka R, například takto:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Co když byste chtěli usnadnit generování jiné sady náhodných čísel?

Je to jednoduché v kombinaci s SQL. Definujete uloženou proceduru, která získá argumenty od uživatele, a pak tyto argumenty předejte do skriptu jazyka R jako proměnné.

```sql
CREATE PROCEDURE MyRNorm (
    @param1 INT
    , @param2 INT
    , @param3 INT
    )
AS
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(mynumbers, mymean, mysd));
'
    , @input_data_1 = N'   ;'
    , @params = N' @mynumbers int, @mymean int, @mysd int'
    , @mynumbers = @param1
    , @mymean = @param2
    , @mysd = @param3
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

- První řádek definuje všechny vstupní parametry SQL, které jsou požadovány při spuštění uložené procedury.

- Řádek začínající v `@params` definuje všechny proměnné používané kódem R a odpovídající datové typy SQL.

- Řádky, které bezprostředně následují, mapují názvy parametrů SQL na odpovídající názvy proměnných R.

Teď, když jste zabalily funkci R v uložené proceduře, můžete snadno volat funkci a předat do nich různé hodnoty, jako je:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Použití funkcí nástroje R pro řešení potíží

`utils`Balíček nainstalovaný ve výchozím nastavení poskytuje celou řadu funkcí nástrojů pro zkoumání aktuálního prostředí jazyka R. Tyto funkce mohou být užitečné, pokud hledáte rozdíly ve způsobu, jakým váš kód R provádí v SQL a mimo prostředí. Například můžete použít `memory.limit()` funkci R k získání paměti pro aktuální prostředí jazyka R.

Vzhledem k tomu `utils` , že balíček je nainstalovaný, ale není ve výchozím nastavení načtený, musíte `library()` ho nejdřív načíst pomocí funkce.

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
library(utils);
mymemory <- memory.limit();
OutputDataSet <- as.data.frame(mymemory);
'
    , @input_data_1 = N' ;'
WITH RESULT SETS(([Col1] INT NOT NULL));
```

> [!TIP]
> Mnoho uživatelů chce používat funkce časování systému v jazyce R, například `system.time` a `proc.time` , k zachycení času používaného procesy jazyka r a k analýze problémů s výkonem.
