---
title: Export databáze U-SQL – nástroje datového jezera Azure pro Visual Studio
description: Zjistěte, jak pomocí nástrojů Datového jezera Azure pro Visual Studio exportovat databázi U-SQL a automaticky ji importovat do místního účtu.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: e5a52de0342e864cb108d8d590583fe64f72e3b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71315796"
---
# <a name="export-a-u-sql-database"></a>Export databáze U-SQL

V tomto článku se dozvíte, jak pomocí [nástroje Datového jezera Azure pro Visual Studio](https://aka.ms/adltoolsvs) exportovat databázi U-SQL jako jeden skript U-SQL a stažené prostředky. Exportovnou databázi můžete importovat do místního účtu ve stejném procesu.

Zákazníci obvykle udržují více prostředí pro vývoj, testování a výrobu. Tato prostředí jsou hostovaná na místním účtu, v místním počítači vývojáře a v účtu Azure Data Lake Analytics v Azure. 

Při vývoji a ladění U-SQL dotazy ve vývojových a testovacích prostředích, vývojáři často potřebují znovu vytvořit svou práci v produkční databázi. Průvodce exportem databáze pomáhá tento proces urychlit. Pomocí průvodce mohou vývojáři klonovat existující databázové prostředí a ukázková data do jiných účtů Data Lake Analytics.

## <a name="export-steps"></a>Kroky exportu

### <a name="step-1-export-the-database-in-server-explorer"></a>Krok 1: Export databáze v Průzkumníkovi serveru

Všechny účty Data Lake Analytics, ke kterým máte oprávnění, jsou uvedeny v Průzkumníkovi serveru. Export databáze:

1. V Průzkumníkovi serveru rozbalte účet obsahující databázi, kterou chcete exportovat.
2. Klepněte pravým tlačítkem myši na databázi a vyberte příkaz **Exportovat**. 
   
    ![Průzkumník serveru – export databáze](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Pokud není možnost **Nabídky Exportovat** k dispozici, je třeba [aktualizovat nástroj na poslední verzi](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Krok 2: Konfigurace objektů, které chcete exportovat

Pokud potřebujete pouze malou část velké databáze, můžete nakonfigurovat podmnožinu objektů, které chcete exportovat v průvodci exportem. 

Akce exportu je dokončena spuštěním úlohy U-SQL. Export z účtu Azure tedy nese určité náklady.

![Průvodce exportem databáze – výběr exportu objektů](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Krok 3: Kontrola seznamu objektů a dalších konfigurací

V tomto kroku můžete ověřit vybrané objekty v **seznamu Exportovat objekt.** Pokud se našly nějaké chyby, vyberte **Předchozí,** chcete-li se vrátit zpět a správně nakonfigurovat objekty, které chcete exportovat.

Můžete také nakonfigurovat další nastavení pro cíl exportu. Popisy konfigurace jsou uvedeny v následující tabulce:

|Konfigurace|Popis|
|-------------|-----------|
|Název cíle|Tento název označuje, kam chcete exportované databázové prostředky uložit. Příklady jsou sestavení, další soubory a ukázková data. Složka s tímto názvem je vytvořena pod místní kořenovou složkou dat.|
|Adresář projektu|Tato cesta definuje, kam chcete exportovaný skript U-SQL uložit. V tomto umístění jsou uloženy všechny definice databázových objektů.|
|Pouze schéma|Pokud vyberete tuto možnost, budou exportovány pouze definice databáze a prostředky (jako jsou sestavení a další soubory).|
|Schéma a data|Pokud vyberete tuto možnost, budou exportovány definice databáze, prostředky a data. Horní n řádky tabulek jsou exportovány.|
|Automaticky importovat do místní databáze|Pokud vyberete tuto možnost, exportovaná databáze se po dokončení exportu automaticky importuje do místní databáze.|

![Průvodce exportem databáze – export seznamu objektů a dalších konfigurací](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Krok 4: Kontrola výsledků exportu

Po dokončení exportu můžete exportované výsledky zobrazit v okně protokolu v průvodci. Následující příklad ukazuje, jak najít exportovaný skript U-SQL a databázové prostředky, včetně sestavení, dalších souborů a ukázkových dat:

![Průvodce exportem databáze – export výsledků](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Import exportované databáze do místního účtu

Nejpohodlnějším způsobem importu exportované databáze je zaškrtnutí políčka **Automaticky importovat do místní databáze** během procesu exportu v kroku 3. Pokud jste toto políčko nezaškrtli, najděte nejprve exportovaný skript U-SQL v protokolu exportu. Potom spusťte skript U-SQL místně importovat databázi do místního účtu.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Import exportované databáze do účtu Data Lake Analytics

Import databáze do jiného účtu Data Lake Analytics:

1. Nahrajte exportované prostředky, včetně sestavení, dalších souborů a ukázkových dat, do výchozího účtu Azure Data Lake Store účtu Data Lake Analytics, do kterého chcete importovat. Exportovnou složku prostředků najdete pod kořenovou složkou místních dat. Nahrajte celou složku do kořenového adresáře výchozího účtu Data Lake Store.
2. Po dokončení nahrávání odešlete exportovaný skript U-SQL do účtu Data Lake Analytics, do kterého chcete databázi importovat.

## <a name="known-limitations"></a>Známá omezení

V současné době pokud vyberete možnost **Schéma a data** v kroku 3, nástroj spustí úlohu U-SQL pro export dat uložených v tabulkách. Z tohoto důvodu může být proces exportu dat pomalý a mohou vám vzniknout náklady. 

## <a name="next-steps"></a>Další kroky

* [Další informace o databázích U-SQL](/u-sql/data-definition-language-ddl-statements) 
* [Testování a ladění úloh U-SQL pomocí místního spuštění a sady Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)


