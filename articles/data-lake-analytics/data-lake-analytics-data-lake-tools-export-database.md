---
title: Export databáze U-SQL pomocí nástrojů Azure Data Lake pro Visual Studio
description: Další informace o použití nástroje Azure Data Lake pro Visual Studio pro export U-SQL databázi a automaticky ho importovat do místní účet.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
manager: kfile
editor: jasonwhowell
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 11/27/2017
ms.openlocfilehash: 1dbcf9b88af7777a1103876a429ccc26450e2f58
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623406"
---
# <a name="export-a-u-sql-database"></a>Export U-SQL databáze.

V tomto článku, Naučte se používat [nástrojů Azure Data Lake pro Visual Studio](http://aka.ms/adltoolsvs) se exportovat databázi U-SQL jako jednoho skriptu U-SQL a stažené prostředky. Vyexportované databáze můžete importovat do místní účet v rámci jednoho procesu.

Zákazníci obvykle udržovat více prostředí pro vývoj, testování a provozním. Těchto prostředí jsou hostované na obou místní účet, na místním počítači pro vývojáře a v účtu Azure Data Lake Analytics v Azure. 

Při vývoji a optimalizovat dotazy U-SQL v vývojová a testovací prostředí, vývojáři často potřebují k práci v produkční databázi znovu vytvořit. Průvodce exportem databáze pomáhá zrychlit tohoto procesu. Pomocí Průvodce může klonovat vývojáři existující databáze prostředí a ukázkových dat na jiné účty Data Lake Analytics.

## <a name="export-steps"></a>Postup exportu

### <a name="step-1-export-the-database-in-server-explorer"></a>Krok 1: Exportujte databázi v Průzkumníku serveru

Všechny účty Data Lake Analytics, které máte oprávnění pro jsou uvedeny v Průzkumníku serveru. Export databáze:

1. V Průzkumníku serveru rozbalte účet, který obsahuje databázi, která chcete exportovat.
2. Klikněte pravým tlačítkem na databázi a potom vyberte **exportovat**. 
   
    ![Průzkumník serveru – Export a databáze](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Pokud **exportovat** nabídky možnost není dostupná, budete muset [aktualizaci nástroj na lasted](http://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Krok 2: Konfigurace objekty, které chcete provést export

Pokud budete potřebovat pouze malou část velké databáze, můžete nakonfigurovat podmnožinu objektů, které chcete exportovat v průvodci export. 

Dokončení akce exportu spuštěním úlohy U-SQL. Export z účtu Azure proto způsobuje náklady.

![Průvodce exportem databáze – vyberte exportovat objekty](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Krok 3: Kontrola seznamu objektů a další konfigurace

V tomto kroku ověříte vybrané objekty v **seznam objektů Export** pole. Pokud nejsou žádné chyby, vyberte **předchozí** přejít zpět a správně nakonfigurujte objekty, které chcete provést export.

Můžete také nakonfigurovat další nastavení pro export cíl. V následující tabulce jsou uvedeny popisy konfigurace:

|Konfigurace|Popis|
|-------------|-----------|
|Název cílového|Tento název označuje, kam chcete uložit exportovaný databázových prostředků. Příklady jsou sestavení, další soubory a ukázková data. Složka s tímto názvem se vytvoří v kořenové složce vaše místní data.|
|Adresáři projektu|Tato cesta definuje, kam chcete uložit exportovaný skript U-SQL. V tomto umístění se ukládají všechny definice objektu databáze.|
|Pouze schéma|Pokud vyberete tuto možnost, exportují se pouze definice databáze a prostředky (jako je sestavení a další soubory).|
|Schéma a data|Pokud vyberete tuto možnost, exportují se definice databáze, prostředky a data. Exportují se horních N řádků tabulky.|
|Importovat do místní databáze automaticky|Pokud vyberete tuto možnost, vyexportované databáze je automaticky importován do místní databáze při exportu po dokončení.|

![Průvodce exportem databáze - Export seznamu objektů a další konfigurace](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Krok 4: Kontrola výsledků exportu

Po dokončení exportu, můžete zobrazit vyexportovaných výsledků v okně protokolu v průvodci. Následující příklad ukazuje, jak najít exportovaných U-SQL skriptu a databáze prostředků, včetně sestavení a další soubory, ukázková data:

![Průvodce exportem databáze - Export výsledků](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>Import vyexportované databáze místní účet

Nejvhodnější způsob, jak importovat vyexportované databáze je výběr **importovat do místní databáze automaticky** políčko během procesu exportu v kroku 3. Pokud nebylo zaškrtněte toto políčko, nejdříve vyhledejte exportovaný skript U-SQL v protokolu exportu. Spusťte skript U-SQL místně pro importování databáze do místní účet.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>Import vyexportované databáze do účtu Data Lake Analytics

K importování databáze do různých účet Data Lake Analytics:

1. Nahrajte exportovaných prostředků, včetně sestavení a další soubory, ukázková data do výchozího účtu Azure Data Lake Store účtu Data Lake Analytics, který chcete importovat do. Můžete najít složku exportovaný prostředků v kořenové složce místní data. Nahrajte celou složku do kořenového adresáře výchozího účtu Data Lake Store.
2. Po dokončení nahrávání odešlete exportovaný skript U-SQL pro účet Data Lake Analytics, který chcete importovat do databáze.

## <a name="known-limitations"></a>Známá omezení

V současné době Pokud vyberete **schéma a Data** možnost v kroku 3, tento nástroj spustí úlohu U-SQL pro export dat uložených v tabulkách. Z toho důvodu data export proces může být pomalé a může způsobit náklady. 

## <a name="next-steps"></a>Další postup

* [Další informace o U-SQL databáze](https://msdn.microsoft.com/library/azure/mt621299.aspx) 
* [Testování a ladění úloh U-SQL pomocí místního spuštění a sady Azure Data Lake U-SQL SDK](data-lake-analytics-data-lake-tools-local-run.md)


