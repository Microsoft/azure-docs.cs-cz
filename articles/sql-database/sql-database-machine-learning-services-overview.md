---
title: Služby strojového učení s R (náhled)
description: Tento článek popisuje Služby strojového učení Azure SQL Database (s R) a vysvětluje, jak to funguje.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 46ca4661d06b52c861431a680a69297575ac99b0
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461408"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Služby strojového učení azure SQL database s r (preview)

Machine Learning Services je funkce Azure SQL Database, která se používá pro provádění skriptů R v databázi. Tato funkce obsahuje balíčky Microsoft R pro vysoce výkonnou prediktivní analýzu a strojové učení. Relační data lze použít ve skriptech R prostřednictvím uložených procedur, skriptu T-SQL obsahujícího příkazy R nebo r kódu obsahujícího T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="what-you-can-do-with-r"></a>Co můžete dělat s R

Využijte sílu jazyka R k poskytování pokročilých analýz a strojového učení v databázi. Tato schopnost přináší výpočty a zpracování na místo, kde se data nacházejí, což eliminuje potřebu vytahovat data v síti. Můžete také využít sílu podnikových balíčků R k poskytování pokročilých analýz ve velkém měřítku.

Machine Learning Services zahrnuje základní distribuci R, překryté podnikové balíčky R od společnosti Microsoft. Funkce a algoritmy R společnosti Microsoft jsou navrženy pro škálování i nástroje a poskytují prediktivní analýzy, statistické modelování, vizualizace dat a špičkové algoritmy strojového učení.

### <a name="r-packages"></a>Balíčky R

Nejběžnější balíčky R s otevřeným zdrojovým kódem jsou předinstalovány ve službách Machine Learning Services. Zahrnuty jsou také následující balíčky R od společnosti Microsoft:

| Balíček R | Popis|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open je vylepšená distribuce R od společnosti Microsoft. Jedná se o kompletní open-source platformu pro statistickou analýzu a datovou vědu. Je založen na a 100% kompatibilní s R, a zahrnuje další funkce pro zlepšení výkonu a reprodukovatelnosti. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR je primární knihovna pro škálovatelné R. Funkce v této knihovně patří mezi nejpoužívanější. V těchto knihovnách se nacházejí transformace a manipulace s daty, statistické sumarizování, vizualizace a mnoho forem modelování a analýz. Kromě toho funkce v těchto knihovnách automaticky distribuují úlohy mezi dostupnými jádry pro paralelní zpracování, se schopností pracovat na blocích dat, které jsou koordinovány a spravovány výpočtovým strojem. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML přidává algoritmy strojového učení k vytváření vlastních modelů pro analýzu textu, analýzu obrázků a analýzu mínění. |

Kromě předinstalovaných balíčků můžete [nainstalovat další balíčky](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview-closed"></a>Zaregistrujte se k náhledu (zavřeno)

> [!IMPORTANT]
> Zaregistrujte se do služby Azure SQL Database Machine Learning Services (preview) je aktuálně **uzavřená**.

## <a name="next-steps"></a>Další kroky

- Podívejte se na [klíčové rozdíly ze služby SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Informace o použití jazyka R k dotazování služby Azure SQL Database Machine Learning Services (preview) najdete v [příručce K Rychlému startu](sql-database-connect-query-r.md).
- Pokud chcete začít s některými jednoduchými skripty R, přečtěte si část [Vytvoření a spuštění jednoduchých skriptů Jazyka R ve službě Azure SQL Database Machine Learning Services (preview).](sql-database-quickstart-r-create-script.md)
