---
title: Azure SQL Database služby Machine Learning s přehled R (preview)
description: Tento článek popisuje Azure SQL Database Machine Learning Services (s jazykem R) a vysvětluje, jak to funguje.
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
ms.openlocfilehash: fe472b8a19b45d7f7b00a8f858c9179d6ee51999
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951578"
---
# <a name="azure-sql-database-machine-learning-services-with-r-preview"></a>Azure SQL Database služby Machine Learning s jazykem R (preview)

Služby Machine Learning je funkce služby Azure SQL Database, používá se ke spuštění skriptů jazyka R v databázi. Funkce obsahuje balíčky Microsoft R pro machine learning a výkonné prediktivní analýzy. Relační data je možné v skripty jazyka R pomocí uložených procedur, obsahující R příkazy skriptu T-SQL nebo kód R, který obsahuje T-SQL.

> [!IMPORTANT]
> Azure SQL Database Machine Learning Services (s jazykem R) je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> Verze public preview je k dispozici pro izolované databáze a elastických fondů v založený na virtuálních jádrech nákupní model **Obecné** a **pro důležité obchodní informace** úrovně služeb. V tomto počáteční verzi preview pro veřejnost **hyperškálovatelný systém** úroveň služby a **spravovanou instanci** možnost nasazení se nepodporují. V současné době je R jediným podporovaným jazykem. Python se momentálně nepodporuje.
>
> Verzi preview je teď dostupná v těchto oblastech: Západní Evropa, Severní Evropa, západní USA 2, USA – východ, USA (střed) – Jih, USA (střed) – sever, Kanada – střed, jihovýchodní Asie, Indie – jih a Austrálie – jihovýchod.
>
> [Zaregistrujte si verzi preview](#signup) níže.

## <a name="what-you-can-do-with-r"></a>Co můžete dělat s jazykem R

Využijte sílu jazyka R k poskytování pokročilých analýz a strojového učení v databázi. Tato schopnost přináší výpočty a zpracování, kde jsou data uložená, takže odpadá potřeba k získání dat přes síť. Navíc můžete využít sílu balíčky R organizace k poskytování pokročilé analýzy ve velkém měřítku.

Služby Machine Learning zahrnuje základní distribuci R překrývající se balíčky jazyka R enterprise od Microsoftu. Funkce jazyka R a algoritmy Microsoftu jsou navržené pro škálování a nástroj pro prediktivní analýzy, statistické modelování, vizualizace dat a špičkový algoritmů strojového učení.

### <a name="r-packages"></a>Balíčky R

Nejběžnější balíčky R open source jsou předem nainstalované ve službě Machine Learning Services. Následující balíčky R od společnosti Microsoft jsou zahrnuté také:

| Balíček R | Popis|
|-|-|
| [Microsoft R Open](https://mran.microsoft.com/rro) | Microsoft R Open je vylepšená distribuce R od společnosti Microsoft. Je kompletní platforma open source pro statistickou analýzu a datové vědy. Je založený na a 100 % kompatibilní s jazykem R a obsahuje další možnosti pro vylepšení výkonu a reprodukovatelnosti. |
| [RevoScaleR](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-revoscaler) | RevoScaleR je primární knihovna pro škálovatelné R. funkce v této knihovně patří mezi nejčastěji používaná. Transformace dat a manipulaci s statistického shrnutí, vizualizace a mnoho forem modelování a analýzy jsou součástí těchto knihoven. Kromě toho funkce v těchto knihoven automaticky distribuují úlohy napříč dostupná jádra pro paralelní zpracování, umožňuje pracovat na bloky dat, která jsou koordinuje a spravuje výpočetní modul. |
| [MicrosoftML (R)](https://docs.microsoft.com/sql/advanced-analytics/r/ref-r-microsoftml) | MicrosoftML přidá algoritmů strojového učení k vytvoření vlastních modelů pro analýzu textu, Analýza obrázků a analýza mínění. |

Kromě předem nainstalovaných balíčků, můžete [instalace dalších balíčků](sql-database-machine-learning-services-add-r-packages.md).

<a name="signup"></a>

## <a name="sign-up-for-the-preview"></a>Registrace verze Preview

Zaregistrovat verzi public preview, postupujte podle těchto kroků:

1. Pokud nemáte předplatné Azure, [vytvořit účet](https://azure.microsoft.com/free/) předtím, než začnete.

2. Odeslání e-mailu společnosti Microsoft na [ sqldbml@microsoft.com ](mailto:sqldbml@microsoft.com) zaregistrovat verzi public preview. Služba Machine Learning Services (s jazykem R) verze Public Preview ve službě SQL Database není ve výchozím nastavení povolená.

Po registraci v programu Microsoft bude připojení je ve verzi public preview a povolit R pro stávající nebo novou databázi.

Služby Machine Learning s jazykem R se nedoporučuje pro produkční úlohy ve verzi public preview.

## <a name="next-steps"></a>Další postup

- Zobrazit [klíče rozdíl oproti serveru SQL Server Machine Learning Services](sql-database-machine-learning-services-differences.md).
- Další informace o použití jazyka R k dotazování Azure SQL Database Machine Learning Services (preview), najdete v článku [příručky rychlý Start](sql-database-connect-query-r.md).
- Začínáme s některé jednoduché skripty jazyka R, najdete v článku [vytvořit a spustit jednoduché skripty jazyka R v Azure SQL Database Machine Learning Services (preview)](sql-database-quickstart-r-create-script.md).
