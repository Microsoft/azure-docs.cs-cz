---
title: Export databáze U-SQL – Nástroje Azure Data Lake pro Visual Studio
description: Naučte se, jak pomocí Nástroje Azure Data Lake pro Visual Studio exportovat databázi U-SQL a automaticky ji naimportovat do místního účtu.
services: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 11/27/2017
ms.openlocfilehash: 05e55d1608323b8e746a1c82ccb915a2db58ce97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132088"
---
# <a name="export-a-u-sql-database"></a>Export databáze U-SQL

V tomto článku se naučíte, jak pomocí [nástroje Azure Data Lake pro Visual Studio](https://aka.ms/adltoolsvs) exportovat databázi u-SQL jako jeden skript u-SQL a stažené prostředky. Můžete importovat exportovanou databázi do místního účtu v rámci stejného procesu.

Zákazníci obvykle uchovávají více prostředí pro vývoj, testování a produkci. Tato prostředí jsou hostovaná na místním účtu, v místním počítači vývojáře a v účtu Azure Data Lake Analytics v Azure. 

Když vyvíjíte a ladíte dotazy U-SQL ve vývojovém a testovacím prostředí, vývojáři často potřebují znovu vytvořit svou práci v provozní databázi. Průvodce exportem databáze pomáhá zrychlit tento proces. Vývojáři mohou pomocí Průvodce klonovat existující databázové prostředí a ukázková data na jiné účty Data Lake Analytics.

## <a name="export-steps"></a>Kroky exportu

### <a name="step-1-export-the-database-in-server-explorer"></a>Krok 1: Export databáze do Průzkumník serveru

Všechny účty Data Lake Analytics, pro které máte oprávnění, jsou uvedeny v Průzkumník serveru. Export databáze:

1. V Průzkumník serveru rozbalte účet obsahující databázi, kterou chcete exportovat.
2. Klikněte pravým tlačítkem na databázi a pak vyberte **exportovat**. 
   
    ![Průzkumník serveru – export databáze](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Pokud možnost nabídky **exportovat** není k dispozici, je nutné [aktualizovat nástroj na poslední verzi](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Krok 2: Konfigurace objektů, které chcete exportovat

Pokud potřebujete jenom malou část velké databáze, můžete v Průvodci exportem nakonfigurovat podmnožinu objektů, které chcete exportovat. 

Akce export se dokončila spuštěním úlohy U-SQL. Z tohoto důvodu se export z účtu Azure účtuje za nějaké náklady.

![Průvodce exportem databáze – výběr objektů exportu](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Krok 3: zkontrolování seznamu objektů a dalších konfigurací

V tomto kroku můžete ověřit vybrané objekty v poli se **seznamem exportovat objekt** . Pokud dojde k chybám, vyberte **Předchozí** a vraťte se zpátky a správně nakonfigurujte objekty, které chcete exportovat.

Můžete také nakonfigurovat další nastavení pro cíl exportu. Popisy konfigurace jsou uvedeny v následující tabulce:

|Konfigurace|Description|
|-------------|-----------|
|Název cíle|Tento název označuje, kam chcete uložit exportované prostředky databáze. Příklady jsou sestavení, další soubory a ukázková data. Složka s tímto názvem se vytvoří v kořenové složce vašich místních dat.|
|Adresář projektu|Tato cesta definuje, kam chcete Uložit exportovaný skript U-SQL. Všechny definice databázových objektů jsou uloženy v tomto umístění.|
|Pouze schéma|Vyberete-li tuto možnost, budou exportovány pouze definice databáze a prostředky (například sestavení a další soubory).|
|Schéma a data|Pokud vyberete tuto možnost, budou exportovány definice databáze, prostředky a data. Exportují se horních N řádků tabulek.|
|Automaticky importovat do místní databáze|Pokud vyberete tuto možnost, exportovaná databáze se automaticky naimportuje do místní databáze při dokončení exportu.|

![Průvodce exportem databáze – export seznamu objektů a dalších konfigurací](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Krok 4: ověření výsledků exportu

Po dokončení exportu můžete zobrazit exportované výsledky v okně protokolu v průvodci. Následující příklad ukazuje, jak najít exportovaný skript U-SQL a databázové prostředky, včetně sestavení, dalších souborů a ukázkových dat:

![Průvodce exportem databáze – Export výsledků](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Importovat exportovanou databázi do místního účtu

Nejpohodlnější způsob, jak importovat exportovanou databázi, je zaškrtnout políčko **importovat do místní databáze automaticky** během procesu exportu v kroku 3. Pokud jste toto políčko nezaškrtli, napřed vyhledejte v protokolu exportu exportovaný skript U-SQL. Potom spusťte skript U-SQL místně a importujte databázi do místního účtu.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Import exportované databáze na účet Data Lake Analytics

Import databáze do jiného účtu Data Lake Analytics:

1. Nahrajte exportované prostředky, včetně sestavení, dalších souborů a ukázkových dat, na výchozí účet Azure Data Lake Store účtu Data Lake Analytics, který chcete importovat do. Složku exportovaných prostředků najdete v kořenové složce místní data. Nahrajte celou složku do kořenového adresáře výchozího účtu Data Lake Store.
2. Po dokončení odesílání odešlete exportovaný skript U-SQL na účet Data Lake Analytics, do kterého chcete databázi importovat.

## <a name="known-limitations"></a>Známá omezení

Pokud v současné době vyberete možnost **schéma a data** v kroku 3, nástroj spustí úlohu U-SQL pro export dat uložených v tabulkách. Z tohoto důvodu může být proces exportu dat pomalý a Vy se vám můžou účtovat náklady. 

## <a name="next-steps"></a>Další kroky

* [Další informace o databázích U-SQL](/u-sql/data-definition-language-ddl-statements) 
* [Testování a ladění úloh U-SQL pomocí místního spuštění a sady Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)


