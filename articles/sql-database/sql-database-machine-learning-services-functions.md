---
title: Psaní pokročilých funkcí R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Naučte se, jak napsat funkci R pro pokročilé statistické výpočty v Azure SQL Database pomocí Machine Learning Services (preview).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60702448"
---
# <a name="write-advanced-r-functions-in-azure-sql-database-using-machine-learning-services-preview"></a>Psaní pokročilých funkcí R v Azure SQL Database pomocí služby Machine Learning Services (preview)

Tento článek popisuje, jak vložit R matematické a užitkové funkce v sql uložené procedury. Pokročilé statistické funkce, které jsou složité implementovat v T-SQL lze provést v R pouze s jedním řádkem kódu.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, [vytvořte si účet,](https://azure.microsoft.com/free/) než začnete.

- Chcete-li spustit ukázkový kód v těchto cvičeních, musíte mít nejprve databázi Azure SQL se službami Machine Learning Services (s R) povolenou. Během veřejné verze Preview vás Microsoft připojí k vaší službě a umožní strojové učení pro vaši stávající nebo novou databázi. Postupujte podle kroků v [části Zaregistrujte se pro náhled](sql-database-machine-learning-services-overview.md#signup).

- Ujistěte se, že jste nainstalovali nejnovější [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). Skripty R můžete spustit pomocí jiných nástrojů pro správu databáze nebo dotazů, ale v tomto rychlém startu budete používat SSMS.

## <a name="create-a-stored-procedure-to-generate-random-numbers"></a>Vytvoření uložené procedury pro generování náhodných čísel

Pro jednoduchost použijeme balíček R, `stats` který je nainstalovaný a načtený ve výchozím nastavení s Azure SQL Database pomocí machine learningových služeb (preview). Balíček obsahuje stovky funkcí pro běžné statistické `rnorm` úkoly, mezi nimi funkce. Tato funkce generuje zadaný počet náhodných čísel pomocí normálního rozdělení, vzhledem k směrodatné odchylce a prostředkům.

Například následující kód R vrátí 100 čísel na střední hodnotu 50, vzhledem k směrodatné odchylce 3.

```R
as.data.frame(rnorm(100, mean = 50, sd = 3));
```

Chcete-li volat tento řádek R `sp_execute_external_script` z T-SQL, spusťte a přidejte funkci R do parametru skriptu R takto:

```sql
EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
OutputDataSet <- as.data.frame(rnorm(100, mean = 50, sd =3));
'
    , @input_data_1 = N'   ;'
WITH RESULT SETS(([Density] FLOAT NOT NULL));
```

Co když chcete usnadnit generování jiné sady náhodných čísel?

To je snadné v kombinaci s SQL. Definujete uloženou proceduru, která získá argumenty od uživatele a pak je předá temeno do skriptu R jako proměnné.

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

- První řádek definuje každý vstupní parametry SQL, které jsou požadovány při spuštění uložené procedury.

- Řádek začínající `@params` definuje všechny proměnné používané kódem R a odpovídající datové typy SQL.

- Řádky, které bezprostředně následují, mapují názvy parametrů SQL na odpovídající názvy proměnných R.

Nyní, když jste zabalili funkci R do uložené procedury, můžete snadno volat funkci a předat různé hodnoty, například takto:

```sql
EXECUTE MyRNorm @param1 = 100
    , @param2 = 50
    , @param3 = 3
```

## <a name="use-r-utility-functions-for-troubleshooting"></a>Použití funkcí nástroje R pro řešení potíží

Balíček, `utils` který je nainstalován ve výchozím nastavení, poskytuje řadu funkcí nástroje pro zkoumání aktuálního prostředí R. Tyto funkce mohou být užitečné, pokud jste zjištění nesrovnalostí ve způsobu, jakým r kód provádí v SQL a ve vnějších prostředích. Můžete například použít funkci `memory.limit()` R k získání paměti pro aktuální prostředí R.

Vzhledem `utils` k tomu, že balíček je `library()` nainstalován, ale není načten ve výchozím nastavení, je nutné použít funkci k jeho načtení jako první.

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
> Mnoho uživatelů jako použití funkce časování systému v R, jako je například `system.time` a `proc.time`, zachytit čas používaný procesy R a analyzovat problémy s výkonem.
