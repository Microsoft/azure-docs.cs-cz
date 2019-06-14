---
title: Psaní pokročilých funkcí R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Další informace o zápisu funkce jazyka R pro pokročilé statistické výpočty v Azure SQL Database pomocí služby Machine Learning (preview).
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
ms.openlocfilehash: 939798d5d9eb2843d7bbbbe74680342e4ce6ce95
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60702448"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Zápis pokročilé funkce jazyka R ve službě Azure SQL Database pomocí služby Machine Learning (preview)

Tento článek popisuje, jak vložit R matematické a uložené procedury, funkce nástrojů v SQL. Pokročilé statistické funkce, které jsou složité implementovat v T-SQL můžete udělat v R s pouze jediný řádek kódu.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, [vytvořit účet](https://azure.microsoft.com/free/) předtím, než začnete.

- Chcete-li spustit ukázkový kód v těchto cvičeních, musíte nejprve mít databázi Azure SQL Machine Learning Services (R) povolena. Ve verzi public preview, společnost Microsoft bude připojit je a povolit strojového učení pro existující nebo nové databáze. Postupujte podle kroků v [zaregistrovat verzi preview](sql-database-machine-learning-services-overview.md#signup).

- Ujistěte se, že jste nainstalovali nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Můžete spustit skripty R používat správu jiných databází nebo nástrojů pro dotazování, ale v tomto rychlém startu budete používat SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Vytvořit uloženou proceduru pro generování náhodných čísel

Pro jednoduchost, použijeme R `stats` balíček, který má nainstalovaný a načíst ve výchozím nastavení s Azure SQL Database pomocí služby Machine Learning (preview). Balíček obsahuje funkce pro běžné statistické úkoly, mezi nimi `rnorm` funkce. Tato funkce generuje náhodná čísla pomocí normální rozdělení daného směrodatná odchylka a znamená, že zadaný počet.

Například následující kód R vrací 100 čísla na střední hodnotu 50, daný směrodatnou odchylku 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Chcete-li tento řádek R volat z jazyka T-SQL, spusťte `sp_execute_external_script` a přidat funkci jazyka R v parametru skript R, následujícím způsobem:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Co když chcete usnadnit Generovat jinou sadu náhodných čísel?

Je to jednoduché, v kombinaci s SQL. Můžete definovat uloženou proceduru, která získá argumenty od uživatele, a pak předejte tyto argumenty do skriptu R jako proměnné.

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

- První řádek definuje všech vstupních parametrů SQL, které jsou potřeba při spuštění uložené procedury.

- Řádek začínající příkazem `@params` definuje všechny proměnné používané kód R a odpovídající datové typy SQL.

- Řádky, které bezprostředně následují namapovat na odpovídající názvy proměnných R názvy parametrů SQL.

Teď, když jste zabalené funkce R v uložené proceduře, můžete snadno volání funkce a předávat různé hodnoty, například takto:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Funkce nástroje R pro řešení potíží

`utils` Nainstalovaný ve výchozím nastavení, balíček poskytuje celou řadu funkcí nástroje pro zkoumání aktuálního prostředí R. Tyto funkce může být užitečné, pokud nedostatky ve způsobu, jak váš kód R provádí v SQL a mimo prostředí. Například můžete použít R `memory.limit()` funkce získat paměť pro aktuální prostředí R.

Protože `utils` balíček nainstalován, ale nejsou načtené ve výchozím nastavení, je nutné použít `library()` funkce načíst první.

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
> Mnoho uživatelů, jako je mohli využívat funkce časování systému v jazyce R, jako například `system.time` a `proc.time`, k zaznamenání času používané procesy R a analyzovat problémy s výkonem.
