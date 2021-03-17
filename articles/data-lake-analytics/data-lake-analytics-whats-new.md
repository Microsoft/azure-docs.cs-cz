---
title: Data Lake Analytics poslední změny
description: Tento článek poskytuje průběžný seznam nedávných změn, které se provedou Data Lake Analytics.
author: xujiang1
ms.service: data-lake-analytics
ms.topic: overview
ms.author: xujiang1
ms.date: 07/31/2020
ms.openlocfilehash: 6f4885594e4f67d91c595d8afdddc15e8ed4ae75
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "88263176"
---
# <a name="whats-new-in-data-lake-analytics"></a>Co je nového v Data Lake Analytics?

Azure Data Lake Analytics se aktualizuje na základě aperiodic pro určité komponenty. V tomto článku najdete informace o tom, jak se má aktualizovat s nejnovější aktualizací.

- Oznámení o klíčové součásti beta verze Preview
- Důležité informace o verzi součásti, například: seznam dostupných verzí komponenty, aktuální výchozí verze atd.


## <a name="notification-of-key-component-beta-preview"></a>Oznámení o klíčové součásti beta verze Preview

Pro verzi Preview není k dispozici žádná klíčová součást beta verze. 

## <a name="u-sql-runtime"></a>Běhový modul U-SQL

Modul runtime Azure Data Lake U-SQL, včetně kompilátoru, Optimalizátoru a Správce úloh, je, který zpracovává váš kód U-SQL.

Když odešlete úlohu Azure Data Lake Analytics ze všech nástrojů, bude vaše úloha používat aktuálně dostupný výchozí modul runtime v produkčním prostředí. 

Verze modulu runtime bude aktualizována aperiodically. A předchozí modul runtime bude v některých časových intervalech stále k dispozici. Když je nová beta verze připravená na verzi Preview, bude k dispozici také zde.

> [!CAUTION]
> - Výběr modulu runtime, který je jiný než výchozí, má potenciál rozdělit úlohy U-SQL. Důrazně doporučujeme, abyste nepoužívali tyto jiné než výchozí verze pro produkční prostředí, ale jenom pro testování.
> - Nevýchozí verze modulu runtime má pevný životní cyklus. Dojde k automatickému vypršení platnosti.

Následující verze je aktuální výchozí verze modulu runtime.

- verze 20200124live_adl_16283022_2

Chcete-li získat informace o řešení potíží s modulem runtime U-SQL, přečtěte si téma [řešení potíží s modulem runtime u-SQL](runtime-troubleshoot.md).

## <a name="net-framework"></a>.NET Framework

Azure Data Lake Analytics nyní používá **.NET Framework v 4.7.2**. 

Pokud váš Azure Data Lake Analytics kód skriptu U-SQL používá vlastní sestavení a tato vlastní sestavení používají knihovny .NET, ověřte kód a zkontrolujte, zda existují žádná přerušení.

Chcete-li získat informace o řešení potíží s upgradem rozhraní .NET pomocí [řešení potíží s upgradem rozhraní .NET](runtime-troubleshoot.md).

## <a name="release-note"></a>Poznámka k verzi

Informace o posledních aktualizacích najdete v [poznámkách k verzi Azure Data Lake Analytics](https://github.com/Azure/AzureDataLake/tree/master/docs/Release_Notes).


## <a name="next-steps"></a>Další kroky

* Začínáme s Data Lake Analytics pomocí rozhraní příkazového řádku [Azure Portal](data-lake-analytics-get-started-portal.md)  |  [Azure PowerShell](data-lake-analytics-get-started-powershell.md)  |  [CLI](data-lake-analytics-get-started-cli.md)

