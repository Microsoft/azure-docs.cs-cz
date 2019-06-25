---
title: Hlavní rozdíly pro Azure SQL Database Machine Learning Services (preview)
description: Toto téma popisuje hlavní rozdíly mezi Azure SQL Database Machine Learning Services (s jazykem R) a SQL Server Machine Learning Services.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 92785015a1ce122b8301b56fa62d122c8d95180c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64725050"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Hlavní rozdíly mezi služby Machine Learning v Azure SQL Database (preview) a SQL Server

Funkce Azure SQL Database Machine Learning Services (s jazykem R) ve verzi (preview) je podobný [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Níže jsou uvedeny některé hlavní rozdíly.

> [!IMPORTANT]
> Služby Machine Learning Azure SQL Database je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Podpora jazyků

Podpora jazyků R a Python prostřednictvím má systém SQL Server [extensibility framework](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). SQL Database nepodporuje oba jazyky. Hlavní rozdíly jsou:

- R je jediný podporovaný jazyk ve službě SQL Database. Python se momentálně nepodporuje.
- Verze jazyka R je 3.4.4.
- Není nutné nakonfigurovat `external scripts enabled` prostřednictvím `sp_configure`. Jakmile budete [zaregistrovali](sql-database-machine-learning-services-overview.md#signup), služby machine learning je povolená pro vaši databázi SQL.

## <a name="package-management"></a>Správa balíčků

Správa balíčků R a instalace fungovat rozdíly mezi SQL Database a SQL Server. Tyto rozdíly jsou:

- Balíčky R instalují přes [sqlmlutils](https://github.com/Microsoft/sqlmlutils) nebo [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Balíčky nelze provést volání odchozí sítě. Toto omezení je podobný [výchozí pravidla brány firewall pro Machine Learning Services](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) v systému SQL Server, ale nelze změnit ve službě SQL Database.
- Není dostupná podpora pro balíčky, které jsou závislé na externích modulů runtime (jako je Java) nebo potřebují přístup k rozhraní API pro operační systém pro instalaci nebo používání.

## <a name="resource-governance"></a>Zásady správného řízení prostředků

Není možné omezit prostředky R prostřednictvím [správce zdrojů](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) a fondy externích zdrojů.

Ve verzi public preview, R prostředky jsou nastaveny na maximálně 20 % prostředků SQL Database a závisí na jaká úroveň služby zvolíte. Další informace najdete v tématu [modely nákupu Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Chyba nedostatku paměti

Pokud je pro R k dispozici dostatek paměti, zobrazí se chybová zpráva. Běžné chybové zprávy jsou:

- Nelze navázat komunikaci s modulem runtime pro skript "R" pro id požadavku: ***. Zkontrolujte prosím požadavky modulu runtime "R"
- Během provádění "sp_execute_external_script' s hodnotou HRESULT 0x80004004 došlo k chybě skriptu"R". ... došlo k chybě externího skriptu: ".. Nelze přidělit paměť (0 Mb) funkce jazyka C "R_AllocStringBuffer."
- Došlo k chybě externího skriptu: Chyba: Nelze přidělit vektor velikost.

Využití závisí na tom, kolik paměti se používá v svoje skripty jazyka R a počet paralelních dotazů, které se spouští. Pokud se zobrazí výše uvedené chyby, je možné škálovat databáze na vyšší úroveň služby na tento problém vyřešit.

## <a name="next-steps"></a>Další postup

- Naleznete v přehledu, [SQL databáze služeb Azure Machine Learning s jazykem R (preview)](sql-database-machine-learning-services-overview.md).
- Další informace o použití jazyka R k dotazování Azure SQL Database Machine Learning Services (preview), najdete v článku [příručky rychlý Start](sql-database-connect-query-r.md).
- Začínáme s některé jednoduché skripty jazyka R, najdete v článku [vytvořit a spustit jednoduché skripty jazyka R v Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md).
