---
title: Machine Learning Services s R (Preview)
description: Tento článek popisuje Azure SQL Database Machine Learning Services (s R) a vysvětluje, jak to funguje.
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
ms.openlocfilehash: ca223de2bc0b26e4968d400ea418761a399dacae
ms.sourcegitcommit: 95931aa19a9a2f208dedc9733b22c4cdff38addc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74462349"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database Machine Learning Services s R (Preview)

Machine Learning Services je funkce Azure SQL Database, která se používá ke spouštění skriptů R v databázi. Tato funkce zahrnuje balíčky Microsoft R pro vysoce výkonné prediktivní analýzy a strojové učení. Relační data lze použít ve skriptech R prostřednictvím uložených procedur, skriptu T-SQL obsahujícího příkazy jazyka R nebo kódu R obsahujícího T-SQL.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

> [!NOTE]
> Verze Preview je k dispozici pro izolované databáze a elastické fondy s využitím nákupního modelu založeného na vCore v úrovních služeb pro **obecné účely** a **důležité pro firmy** . V této počáteční verzi Preview se nepodporuje možnost **nasazení na úrovni služby a** nasazení **spravované instance** . V současné době je R jediným podporovaným jazykem. Python se momentálně nepodporuje.
>
> Verze Preview je aktuálně dostupná v následujících oblastech: Západní Evropa, Severní Evropa, Západní USA 2, Východní USA, Střed USA – jih, Střed USA – sever, Kanada – střed, jihovýchodní Asie, Indie – jih a Austrálie – jihovýchod.

## <a name="what-you-can-do-with-r"></a>Co se dá dělat s R

Využijte sílu jazyka R k doručování pokročilých analýz a strojového učení v databázi. Tato možnost přináší výpočty a zpracování na místě, kde se data nacházejí, což eliminuje nutnost vyčítat data v síti. Navíc můžete využít sílu podnikových balíčků R k doručování pokročilých analýz ve velkém měřítku.

Machine Learning Services zahrnuje základní distribuci R, překrývající se s podnikovými balíčky R od Microsoftu. Funkce a algoritmy společnosti Microsoft jsou zpracovávány pro škálování i nástroj, poskytování prediktivních analýz, statistického modelování, vizualizací dat a špičkových algoritmů strojového učení.

### <a name="r-packages"></a>Balíčky R

Nejběžnější balíčky Open Source R jsou předem nainstalované v Machine Learning Services. K dispozici jsou také následující balíčky R od společnosti Microsoft:

| Balíček R | Popis|
|-|-|
| [Microsoft R – otevřít](https://mran.microsoft.com/rro) | Microsoft R Open je rozšířená distribuce jazyka R od Microsoftu. Je to kompletní Open-Source platforma pro statistickou analýzu a datové vědy. Je založen na a 100% kompatibilním s R a obsahuje další možnosti pro zlepšení výkonu a reprodukovatelnosti. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR je primární knihovna pro škálovatelné funkce R. Functions v této knihovně, které jsou široce používané. V těchto knihovnách jsou nalezeny transformace dat a manipulace, statistická sumarizace, vizualizace a mnoho forem modelování a analýz. Kromě toho funkce v těchto knihovnách automaticky distribuují úlohy napříč dostupnými jádry pro paralelní zpracování, s možností pracovat na blocích dat, která jsou koordinována a spravována výpočetním modulem. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML přidává algoritmy strojového učení, které vytvářejí vlastní modely pro analýzu textu, analýzu obrázků a analýzu mínění. |

Kromě předem nainstalovaných balíčků můžete [nainstalovat další balíčky](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Registrace verze Preview

> [!IMPORTANT]
> Registrace pro Azure SQL Database Machine Learning Services (Preview) je aktuálně zavřená.

Machine Learning Services se R nedoporučuje pro produkční úlohy ve verzi Preview.

## <a name="next-steps"></a>Další kroky

- Podívejte se na [klíčové rozdíly od SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Pokud se chcete dozvědět, jak použít R k dotazování Azure SQL Database Machine Learning Services (Preview), přečtěte si [příručku pro rychlý Start](sql-database-connect-query-r.md).
- Pokud chcete začít s některými jednoduchými skripty jazyka R, přečtěte si téma [Vytvoření a spuštění jednoduchých skriptů r v Azure SQL Database Machine Learning Services (Preview)](sql-database-quickstart-r-create-script.md).
