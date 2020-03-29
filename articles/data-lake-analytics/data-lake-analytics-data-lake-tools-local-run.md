---
title: Spouštění skriptů Azure Data Lake U-SQL v místním počítači
description: Zjistěte, jak pomocí nástrojů Azure Data Lake Tools pro Visual Studio spouštět úlohy U-SQL v místním počítači.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 07/03/2018
ms.openlocfilehash: 42e58125fcbc3ab411c0d7503c42c14c28178428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "62113931"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Spuštění skriptů U-SQL v místním počítači

Při vývoji skriptů U-SQL můžete ušetřit čas a náklady spuštěním skriptů místně. Azure Data Lake Tools pro Visual Studio podporuje spouštění skriptů U-SQL v místním počítači. 

## <a name="basic-concepts-for-local-runs"></a>Základní koncepty pro místní běhy

Následující graf ukazuje součásti pro místní spuštění a jak tyto součásti mapovat na cloud spustit.

|Komponenta|Místní spuštění|Cloud běh|
|---------|---------|---------|
|Úložiště|Kořenová složka místních dat|Výchozí účet Azure Data Lake Store|
|Služba Compute|Modul místního spuštění U-SQL|Služba Azure Data Lake Analytics|
|Spustit prostředí|Pracovní adresář v místním počítači|Cluster Azure Data Lake Analytics|

Následující části poskytují další informace o součástech místního spuštění.

### <a name="local-data-root-folders"></a>Kořenové složky místních dat

Kořenová složka místních dat je **místní úložiště** pro místní výpočetní účet. Každá složka v místním systému souborů v místním počítači může být místní kořenovou složkou dat. Je stejný jako výchozí účet Azure Data Lake Store účtu Data Lake Analytics. Přepnutí do jiné kořenové složky dat je stejné jako přepnutí na jiný výchozí účet úložiště. 

Kořenová složka dat se používá následujícím způsobem:
- Uložit metadata. Příklady jsou databáze, tabulky, funkce s hodnotou tabulky a sestavení.
- Vyhledejte vstupní a výstupní cesty, které jsou definovány jako relativní cesty ve skriptech U-SQL. Pomocí relativní cesty, je jednodušší nasadit u-SQL skripty do Azure.

### <a name="u-sql-local-run-engines"></a>Moduly místního spuštění U-SQL

Modul místního spuštění U-SQL je **místní výpočetní účet** pro úlohy U-SQL. Uživatelé můžou spouštět úlohy U-SQL místně prostřednictvím nástrojů Azure Data Lake Tools pro Visual Studio. Místní spuštění jsou také podporovány prostřednictvím příkazového řádku Azure Data Lake U-SQL SDK a programovací rozhraní. Další informace o [Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Pracovní adresáře

Při spuštění skriptu U-SQL je potřeba pracovní složka adresáře k ukládání výsledků kompilace do mezipaměti, spuštění protokolů a provádění dalších funkcí. V nástroji Azure Data Lake Tools pro Visual Studio je pracovní adresář pracovním adresářem projektu U-SQL. Nachází se pod. `<U-SQL project root path>/bin/debug>` Pracovní adresář je vyčištěn při každém spuštění nového spuštění.

## <a name="local-runs-in-microsoft-visual-studio"></a>Místní spuštění v sadě Microsoft Visual Studio

Nástroje datového jezera Azure pro Visual Studio mají integrovaný modul místního spuštění. Nástroje povrch motoru jako místní výpočetní účet. Chcete-li skript U-SQL spustit místně, vyberte v rozevírací nabídce okraje editoru skriptu účet **Local-machine** nebo **Local-project.** Pak vyberte **Odeslat**.

![Odeslání skriptu U-SQL k místnímu účtu](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Místní spuštění s účtem místního počítače

Účet **místního počítače** je sdílený místní výpočetní účet s jednou kořenovou složkou místních dat jako účet místního úložiště. Ve výchozím nastavení je kořenová složka dat umístěna na **adrese C:\Users\<username>\AppData\Local\USQLDataRoot**. Je také konfigurovatelný prostřednictvím **nástrojů** > **Data Lake** > **Možnosti a nastavení**.

![Konfigurace kořenové složky místních dat](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Projekt U-SQL je vyžadován pro místní spuštění. Pracovní adresář projektu U-SQL se používá pro místní pracovní adresář u-SQL. Výsledky kompilace, protokoly spuštění a další soubory související se spuštěním úlohy jsou generovány a uloženy ve složce pracovního adresáře během místního spuštění. Pokaždé, když znovu spustíte skript, všechny soubory v pracovním adresáři jsou vyčištěny a znovu generovány.

## <a name="local-runs-with-a-local-project-account"></a>Místní spuštění s účtem místního projektu

Účet **místního projektu** je projektově izolovaný místní výpočetní účet pro každý projekt s izolovanou kořenovou složkou místních dat. Každý aktivní projekt U-SQL, který se otevře `(Local-project: <project name>)` v Průzkumníkovi řešení v sadě Visual Studio, má odpovídající účet. Účty jsou uvedeny v Průzkumníkovi serveru v sadě Visual Studio i v okraji editoru skriptů U-SQL.  

Účet **místního projektu** poskytuje čisté a izolované vývojové prostředí. Účet **místního počítače** má sdílenou kořenovou složku místních dat, která ukládá metadata a vstupní a výstupní data pro všechny místní úlohy. Ale účet **místního projektu** vytvoří dočasnou kořenovou složku místních dat v pracovním adresáři projektu U-SQL při každém spuštění skriptu U-SQL. Tato kořenová složka dočasných dat je vyčištěna, když dojde k opětovnému sestavení nebo opětovnému spuštění. 

Projekt U-SQL spravuje izolované prostředí místního spuštění prostřednictvím odkazu projektu a vlastnosti. Můžete nakonfigurovat vstupní zdroje dat pro skripty U-SQL v projektovém i odkazovaném databázovém prostředí.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Správa vstupního zdroje dat pro účet místního projektu 

Projekt U-SQL vytvoří kořenovou složku místních dat a nastaví data pro účet **místního projektu.** Dočasná kořenová složka dat je vyčištěna a znovu vytvořena v pracovním adresáři projektu U-SQL pokaždé, když dojde k opětovnému sestavení a místnímu spuštění. Všechny zdroje dat, které jsou konfigurovány v projektu U-SQL, jsou před spuštěním místní úlohy zkopírovány do této dočasné kořenové složky místních dat. 

Kořenovou složku zdrojů dat můžete nakonfigurovat. Klepněte pravým tlačítkem myši na**položku** **U-SQL project** > **Property** > Test Data Source . Při spuštění skriptu U-SQL na účtu **místního projektu** jsou všechny soubory a podsložky ve složce **Testovací zdroj dat** zkopírovány do dočasné kořenové složky místních dat. Soubory pod podsložkami jsou zahrnuty. Po spuštění místní úlohy lze výstupní výsledky nalézt také pod dočasnou kořenovou složkou místních dat v pracovním adresáři projektu. Všechny tento výstup je odstraněn a vyčištěn, když projekt získá znovu sestavit a vyčistit. 

![Konfigurace testovacího zdroje dat projektu](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Správa referenčního databázového prostředí pro účet **místního projektu** 

Pokud dotaz U-SQL používá nebo se dotazuje s databázovými objekty U-SQL, je nutné před místním spuštěním skriptu U-SQL provést místní nastavení databázových prostředí. Pro účet **místního projektu** lze závislosti databáze U-SQL spravovat pomocí odkazů na projekt U-SQL. Do projektu U-SQL můžete přidat odkazy na databázový projekt U-SQL. Před spuštěním skriptů U-SQL na účtu **místního projektu** jsou všechny odkazované databáze nasazeny do dočasné kořenové složky místních dat. A pro každé spuštění je kořenová složka dočasných dat vyčištěna jako čerstvé izolované prostředí.

Viz tento související článek:
* Zjistěte, jak spravovat definice a odkazy na databázi U-SQL v [databázových projektech U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Rozdíl mezi účty **místního počítače** a **místního projektu**

Účet **místního počítače** simuluje účet Azure Data Lake Analytics na místních počítačích uživatelů. Stejné prostředí sdílí s účtem Azure Data Lake Analytics. Účet **místního projektu** poskytuje uživatelsky přívětivé prostředí místního vývoje. Toto prostředí pomáhá uživatelům nasadit odkazy na databázi a vstupní data před místním spuštěním skriptů. Účet **místního počítače** poskytuje sdílené trvalé prostředí, které lze přistupovat prostřednictvím všech projektů. Účet **místního projektu** poskytuje izolované vývojové prostředí pro každý projekt. Je to svěží pro každý běh. Účet **místního projektu** nabízí rychlejší vývoj díky rychlému použití nových změn.

Další rozdíly mezi účty **místního počítače** a **místního projektu** jsou uvedeny v následující tabulce:

|Rozdílový úhel|Místní počítač|Místní projekt|
|----------------|---------------|---------------|
|Místní přístup|Je přístupný všem projektům.|K tomuto účtu má přístup pouze odpovídající projekt.|
|Kořenová složka místních dat|Trvalá místní složka. Nakonfigurováno prostřednictvím **nástrojů** > **Možnosti a nastavení****datového jezera** > .|Dočasná složka vytvořená pro každé místní spuštění v pracovním adresáři projektu U-SQL. Složka se vyčistí, když dojde k opětovnému sestavení nebo opětovnému spuštění.|
|Vstupní data pro skript U-SQL|Relativní cesta pod kořenovou složkou trvalých místních dat.|Nastavit prostřednictvím >  **vlastnosti projektu U-SQL****Test Data Source**. Všechny soubory a podsložky jsou před místním spuštěním zkopírovány do kořenové složky dočasných dat.|
|Výstupní data pro skript U-SQL|Relativní cesta pod kořenovou složkou trvalých místních dat.|Výstup do kořenové složky dočasných dat. Výsledky jsou vyčištěny při opětovném sestavení nebo opětovné spuštění.|
|Nasazení odkazované databáze|Odkazované databáze nejsou nasazeny automaticky při spuštění proti účtu **místního počítače.** Je to stejné pro odeslání účtu Azure Data Lake Analytics.|Odkazované databáze jsou nasazeny na účet **místního projektu** automaticky před místním spuštěním. Všechna databázová prostředí jsou vyčištěna a znovu nasazena, když dojde k opětovnému sestavení nebo opětovnému spuštění.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Místní spuštění s u-SQL SDK

Skripty U-SQL můžete spouštět místně v sadě Visual Studio a také použít Azure Data Lake U-SQL SDK ke spuštění skriptů U-SQL místně s příkazovým řádkem a programovacími rozhraními. Prostřednictvím těchto rozhraní můžete automatizovat u-SQL místní spuštění a testy.

Další informace o [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Další kroky

- [Jak nastavit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Jak otestovat kód Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
