---
title: Služby Machine Learning (s jazykem R) v Přehled služby Azure SQL Database (Preview)
description: Toto téma popisuje Azure SQL Database Machine Learning Services (s jazykem R) a vysvětluje, jak to funguje.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: e2159e7cc59830c3d0d10f1c5b9697ab5b45b666
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55824021"
---
# <a name="machine-learning-services-with-r-in-azure-sql-database-preview"></a>Machine Learning Services (s jazykem R) ve službě Azure SQL Database (preview)

Služby Machine Learning je funkce služby Azure SQL Database, používá se ke spuštění skriptů jazyka R v databázi. Funkce obsahuje balíčky Microsoft R pro machine learning a výkonné prediktivní analýzy. Relační data je možné v skripty jazyka R pomocí uložených procedur, obsahující R příkazy skriptu T-SQL nebo kód R, který obsahuje T-SQL.

> [!NOTE]
> Machine Learning Services (s jazykem R) ve službě Azure SQL Database je aktuálně ve verzi public preview. [Zaregistrujte si verzi preview](#signup) níže.

## <a name="what-you-can-do-with-r"></a>Co můžete dělat s jazykem R

Využijte sílu jazyka R k poskytování pokročilých analýz a strojového učení v databázi. Tato schopnost přináší výpočty a zpracování, kde jsou data uložená, takže odpadá potřeba k získání dat přes síť. Také využijte možnosti balíčky R organizace k poskytování pokročilé analýzy ve velkém měřítku.

Služby Machine Learning zahrnuje základní distribuci R překrývající se balíčky jazyka R enterprise od Microsoftu. Funkce jazyka R a algoritmy Microsoftu jsou navržené pro škálování a nástroj pro prediktivní analýzy, statistické modelování, vizualizace dat a špičkový algoritmů strojového učení.

### <a name="r-packages"></a>Balíčky R

Nejběžnější balíčky opensourcového jazyka R jsou předem nainstalované ve službě Machine Learning Services. Následující balíčky R od společnosti Microsoft jsou zahrnuté také:

| Balíček R | Popis|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open je vylepšená distribuce R od společnosti Microsoft. Jde kompletní platforma open source pro statistickou analýzu a datové vědy. Je založený na a 100 % kompatibilní s jazykem R a obsahuje další možnosti pro vylepšení výkonu a reprodukovatelnosti. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR je primární knihovna pro škálovatelné R. funkce v této knihovně patří mezi nejčastěji používaná. Transformace dat a manipulaci s statistického shrnutí, vizualizace a mnoho forem modelování a analýzy jsou součástí těchto knihoven. Kromě toho funkce v těchto knihoven automaticky distribuují úlohy napříč dostupná jádra pro paralelní zpracování, umožňuje pracovat na bloky dat, která jsou koordinuje a spravuje výpočetní modul. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML přidá algoritmů strojového učení k vytvoření vlastních modelů pro analýzu textu, Analýza obrázků a analýza mínění. |

Kromě předem nainstalovaných balíčků, můžete [nainstalovat další balíčky](sql-database-connect-query-r.md#add-package).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Registrace verze Preview

Zaregistrovat verzi public preview, postupujte podle těchto kroků:

1. Pokud nemáte předplatné Azure, [vytvořit účet](https://azure.microsoft.com/free/) předtím, než začnete.

2. Odeslání e-mailu společnosti Microsoft na [ sqldbml@microsoft.com ](mailto:sqldbml@microsoft.com) zaregistrovat verzi public preview. Služba Machine Learning Services (s jazykem R) verze Public Preview ve službě SQL Database není ve výchozím nastavení povolená.

Po registraci v programu Microsoft bude připojení je ve verzi public preview a povolit R pro stávající nebo novou databázi.

Machine Learning Services (s jazykem R) ve službě SQL Database je momentálně dostupný jenom v nákupní model v založený na virtuálních jádrech **obecné účely** a **pro důležité obchodní informace** úrovně u samostatných služeb a elastický fond možností nasazení. V tomto počáteční verzi preview pro veřejnost **Hyperškálovatelného** úroveň služby a **Managed Instance** výběrem možností nasazení se nepodporují.

V současné době je R jediným podporovaným jazykem. Python se momentálně nepodporuje. Verzi preview je zpočátku k dispozici v následujících oblastech: západní Evropa, Severní Evropa, západní USA 2, východní USA, střed USA – Jih, střed USA – sever, Kanada – střed, jihovýchodní Asie, Indie – jih a Austrálie – jihovýchod. Další oblasti bude přidána později.

Nepoužívejte služby Machine Learning s jazykem R pro produkční úlohy ve verzi public preview.

## <a name="next-steps"></a>Další postup

- Zobrazit [klíče rozdíl oproti serveru SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md)
- Zjistěte, jak pomocí služby Machine Learning (s jazykem R) ve službě Azure SQL Database, najdete v článku [příručky rychlý Start](sql-database-connect-query-r.md).
- Další informace najdete [kurzy jazyka SQL Server R](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)