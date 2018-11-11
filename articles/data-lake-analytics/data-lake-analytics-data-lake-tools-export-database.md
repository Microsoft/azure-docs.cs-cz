---
title: Exportovat databázi U-SQL pomocí nástrojů Azure Data Lake pro Visual Studio
description: Další informace o použití Azure Data Lake Tools pro Visual Studio exportovat databázi U-SQL a automaticky importovat ho do místního účtu.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 7a4aae87cd704cf94c89c308c0c056ae5b9a8b4d
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250998"
---
# <a name="export-a-u-sql-database"></a>Exportovat databázi U-SQL

V tomto článku najdete další informace o použití [Azure Data Lake Tools pro Visual Studio](https://aka.ms/adltoolsvs) exportovat databázi U-SQL jako jeden skript U-SQL a stažené zdroje. Vyexportované databáze můžete importovat do místní účet v rámci stejného procesu.

Zákazníci obvykle udržovat několik prostředí pro vývoj, testování a produkce. Tato prostředí jsou hostované na obou místní účet, na místním počítači vývojáře a v rámci účtu Azure Data Lake Analytics v Azure. 

Při vývoji a ladění dotazů U-SQL v vývojová a testovací prostředí, vývojáři často potřeba znovu vytvořit svou práci v provozní databázi. Průvodce exportem databáze pomáhá zrychlit tento proces. Pomocí Průvodce vývojáře naklonovat existující databázi prostředí a ukázková data do jiných účtů Data Lake Analytics.

## <a name="export-steps"></a>Postup exportu

### <a name="step-1-export-the-database-in-server-explorer"></a>Krok 1: Exportujte databáze v Průzkumníku serveru

Všechny účty Data Lake Analytics, ke kterým máte oprávnění pro jsou uvedeny v Průzkumníku serveru. Export databáze:

1. V Průzkumníku serveru rozbalte účet, který obsahuje databázi, kterou chcete exportovat.
2. Klikněte pravým tlačítkem na databázi a pak vyberte **exportovat**. 
   
    ![Průzkumník serveru – Export databáze](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Pokud **exportovat** možnost nabídky nebude k dispozici, budete muset [aktualizaci nástroj lasted verzi](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Krok 2: Konfigurace, které chcete exportovat objekty

Pokud potřebujete pouze malou část velké databázi, můžete nakonfigurovat podmnožinu objektů, které chcete exportovat v průvodci export. 

Export akce provedete spuštění úlohy U-SQL. Export z účtu Azure, proto způsobuje náklady.

![Průvodce exportem databáze - vyberte exportovat objekty](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Krok 3: Kontrola seznamu objektů a další konfigurace

V tomto kroku ověříte vybrané objekty v **exportovat seznam objektu** pole. Pokud nejsou žádné chyby, vyberte **předchozí** vrátit zpět a správně nakonfigurovat objekty, které chcete exportovat.

Můžete také nakonfigurovat další nastavení pro cíl exportu. V následující tabulce jsou uvedeny popisy konfigurace:

|Konfigurace|Popis|
|-------------|-----------|
|Název cíle|Tento název označuje, ve které chcete uložit exportovaný databázových prostředků. Příklady jsou sestavení, další soubory a ukázkovými daty. Složka s tímto názvem se vytvoří v kořenové složce místní data.|
|Adresář projektu|Tato cesta definuje, ve které chcete uložit exportovaný skript U-SQL. V tomto umístění jsou uloženy všech definic objektů databáze.|
|Pouze schéma|Pokud vyberete tuto možnost, jsou exportovány pouze definice databáze a prostředky (jako jsou sestavení a další soubory).|
|Schéma a data|Pokud vyberete tuto možnost, jsou exportovány definic databáze, prostředky a data. Horní N řádků z tabulky jsou exportovány.|
|Importovat do místní databáze automaticky|Pokud vyberete tuto možnost, vyexportované databáze je automaticky importován do místní databáze při exportu bylo dokončeno.|

![Průvodce exportem databáze - Export seznamu objektů a další konfigurace](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Krok 4: Kontrola výsledků exportu

Po dokončení exportu můžete zobrazit vyexportovaných výsledků v okně protokolu v průvodci. Následující příklad ukazuje, jak najít exportovaný U-SQL skriptů a databáze prostředky, včetně sestavení, další soubory a ukázkovými daty:

![Průvodce exportem databáze - Export výsledků](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Import vyexportované databáze pro místní účet

Nejpohodlnější způsob importu vyexportované databáze je výběr **importovat do místní databáze automaticky** zaškrtávací políčko během procesu exportu v kroku 3. Pokud je toto políčko nezaškrtli, nejdříve vyhledejte exportovaný skript U-SQL v protokolu exportu. Potom spusťte skript U-SQL místně k importování databáze do místní účet.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Importovat exportovaný databáze do účtu Data Lake Analytics

K importování databáze do jiného účtu Data Lake Analytics:

1. Nahrání exportované prostředky, včetně sestavení, další soubory a ukázková data do výchozího účtu Azure Data Lake Store, který chcete importovat do účtu Data Lake Analytics. Můžete najít složku exportovaných prostředků v kořenové složce místní data. Kořen výchozí účet Data Lake Store nahrajte celou složku.
2. Po dokončení nahrávání odešlete exportovaný skript U-SQL k účtu Data Lake Analytics, který chcete importovat databázi do.

## <a name="known-limitations"></a>Známá omezení

V současné době po vybrání **schéma a Data** možnosti v kroku 3, nástroj spouští úlohu U-SQL k exportu dat ukládají do tabulek. Z tohoto důvodu data exportu procesu může být pomalé a můžou se vám účtovat náklady. 

## <a name="next-steps"></a>Další postup

* [Další informace o databáze U-SQL](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [Testování a ladění úloh U-SQL pomocí místního spuštění a sady Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)


