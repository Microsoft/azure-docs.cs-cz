---
title: Spuštění Azure Data Lake U-SQL skriptů v místním počítači | Dokumentace Microsoftu
description: Zjistěte, jak používat Azure Data Lake Tools pro Visual Studio ke spouštění úloh U-SQL na místním počítači.
services: data-lake-analytics
documentationcenter: ''
author: yanancai
manager: ''
editor: ''
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/03/2018
ms.author: yanacai
ms.openlocfilehash: 892147f3668ee811e3c43044478c650d2f37587a
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630311"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Spouštění skriptů U-SQL na místním počítači

Při vývoji skriptů U-SQL můžete uložit museli vynakládat čas a spuštěním skriptů místně. Azure Data Lake Tools pro Visual Studio podporuje spouštění skriptů U-SQL na místním počítači. 

## <a name="basic-concepts-for-local-runs"></a>Základní koncepty pro místní běhy

Následující graf ukazuje komponenty pro místní spuštění a jak tyto komponenty se mapují na cloud spustit.

|Komponenta|Místní spuštění|Spustit cloud|
|---------|---------|---------|
|Úložiště|Kořenové složky místní data|Účet výchozí Azure Data Lake Store|
|Compute|Modul pro místní spuštění U-SQL|Služba Azure Data Lake Analytics|
|Prostředí pro spuštění|Pracovní adresář na místním počítači|Azure Data Lake Analytics clusteru|

Následující části poskytují další informace o místní spuštění součásti.

### <a name="local-data-root-folders"></a>Kořenové složky místní data

Je kořenová složka místních dat **místní úložiště** místní výpočetní účet. Všechny složky v místním systému souborů na místním počítači může být kořenové složky místní data. Je stejný jako výchozí účet Azure Data Lake Store účtu Data Lake Analytics. Přepnutí na kořenovou složku s různými daty je stejné jako přepnutí na jiný výchozí účet úložiště. 

Kořenové složce dat se používá takto:
- Store metadat. Příklady jsou databáze, tabulky, funkce vracející tabulku a sestavení.
- Vyhledání vstupní a výstupní cesty, které jsou definovány jako relativními cestami na skripty U-SQL. Pomocí relativní cesty je snazší nasazování skripty U-SQL do Azure.

### <a name="u-sql-local-run-engines"></a>U-SQL místní spuštění moduly

U-SQL je modul pro místní spuštění **místní výpočetní účet** úloh U-SQL. Uživatelé mohou spouštět úloh U-SQL místně prostřednictvím nástrojů Azure Data Lake pro Visual Studio. Místní spuštění jsou podporovány také prostřednictvím rozhraní příkazového řádku a programovacích Azure Data Lake U-SQL SDK. Další informace o [Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Pracovní adresáře

Když spustíte skript U-SQL, pracovní adresář složku je potřeba výsledky kompilace do mezipaměti, spuštění protokoly a provádění dalších funkcí. V Azure Data Lake Tools pro Visual Studio pracovní adresář je projekt U-SQL pracovní adresář. Je umístěna ve složce `<U-SQL project root path>/bin/debug>`. Pracovní adresář je vyčistit pokaždé, když se aktivuje nové spuštění.

## <a name="local-runs-in-microsoft-visual-studio"></a>Místní spuštění v sadě Microsoft Visual Studio

Azure Data Lake Tools pro Visual Studio mají integrovaný modul místního spuštění. Nástroje pro surface jako účet místní výpočetní modul. Chcete-li spustit skript U-SQL místně, vyberte **místní počítač** nebo **místní projekt** účtu v tento skript editoru okraje rozevírací nabídky. Potom vyberte **odeslat**.

![Odeslat skript U-SQL do místního účtu](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Místní spuštění s účtem služby Local machine

A **místní počítač** účtu je sdílený místní výpočetní účet s kořenovou složku jeden místní data jako účet místního úložiště. Ve výchozím nastavení, se nachází v kořenové složce dat **C:\Users\<uživatelské jméno > \AppData\Local\USQLDataRoot**. Je také prostřednictvím **nástroje** > **Data Lake** > **možnosti a nastavení**.

![Nakonfigurujte kořenové složky místní data](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Projekt v U-SQL je vyžadováno pro místní spuštění. Projekt U-SQL pracovní adresář se používá pro U-SQL místního spuštění pracovního adresáře. Výsledky kompilace, spusťte protokoly a další úlohy související s spustit soubory jsou generovány a uložené ve složce pracovní adresář při místním spuštění. Pokaždé, když se skript spustit znovu, jsou všechny soubory v pracovním adresáři Vyčištěná a znovu vygeneroval.

## <a name="local-runs-with-a-local-project-account"></a>Místní spuštění s účtem místní projekt

A **místní projekt** účtu je samostatný projekt místní výpočetní účet pro každý projekt s kořenovou složku izolované místní data. Každý aktivní projekt U-SQL, které se otevře v okně Průzkumník řešení v sadě Visual Studio má odpovídající `(Local-project: <project name>)` účtu. Účty jsou uvedeny v Průzkumníku serveru v sadě Visual Studio a na okraj editoru skriptů U-SQL.  

**Místní projekt** účet poskytuje přehledné a izolované vývojové prostředí. A **místní počítač** účet má kořenová složka sdílených dat místní, která ukládá metadata a vstupní a výstupní data pro všechny místní úlohy. Ale **místní projekt** účet vytváří kořenovou složku Dočasné místních dat v rámci pracovního adresáře projektu U-SQL při každém spuštění skriptu U-SQL. Tato kořenová složka dočasných dat probíhá čištění po novém sestavení nebo se stane, spusťte znovu. 

Projekt v U-SQL spravuje izolované místní prostředí pro spuštění prostřednictvím odkazu na projekt a vlastnosti. Vstupní data zdroje pro skripty U-SQL můžete nakonfigurovat v projektu a prostředí odkazovaná databáze.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Správa zdroje vstupní data pro projekt místní účet 

Projekt v U-SQL vytvoří kořenové složky místní data a nastaví data pro **místní projekt** účtu. Kořenová složka dočasných dat je Vyčištěná a znovu vytvořit v rámci pracovního adresáře projektu U-SQL, pokaždé, když se stane opětovné sestavení a místní spuštění. Všechny zdroje dat, které jsou nakonfigurované v projektu U-SQL se zkopírují do kořenové složky Tento dočasný místní data předtím, než spustí místní úlohy. 

Můžete nakonfigurovat kořenové složce vašich datových zdrojů. Klikněte pravým tlačítkem na **projekt v U-SQL** > **vlastnost** > **zdroj testovacích dat**. Pokud spouštíte skript U-SQL na **místní projekt** účtu, všechny soubory a podsložky v **zdroj testovacích dat.** složce se zkopírují do kořenové složky místní dočasná data. Soubory v podsložkách jsou zahrnuty. Po spuštění místní úlohy, výstup také najdete v kořenové složce dočasných dat místní v pracovním adresáři projektu. Tímto výstupem je odstranit a vyčistit při získá projekt znovu sestavit a vyčistit. 

![Konfigurace zdroje dat projektu testu](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Správa prostředí odkazovaná databáze pro **místní projekt** účtu 

Pokud U-SQL dotazu používá či dotazy s objekty databáze U-SQL, musíte udělat prostředí databáze připravená místně před spuštěním skriptu U-SQL místně. Pro **místní projekt** účtu, databáze U-SQL je možné spravovat závislosti pomocí odkazů na projekt U-SQL. Je možné přidat odkazy projektu databáze U-SQL pro váš projekt v U-SQL. Před spuštěním skriptů U-SQL **místní projekt** účtu, všechny odkazované databáze jsou nasazeny do kořenové složky místní dočasná data. A pro každé spuštění kořenové složce dočasných dat vymazaných jako nové izolované prostředí.

Najdete v článku související:
* Další informace o správě definic databáze U-SQL a odkazy v [projektů U-SQL database](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Rozdíl mezi **místní počítač** a **místní projekt** účty

A **místní počítač** účet simuluje účtu Azure Data Lake Analytics v místních počítačích uživatelů. Pomocí účtu Azure Data Lake Analytics, která sdílí stejné prostředí. A **místní projekt** účet poskytuje uživatelsky přívětivé místní vývojové prostředí. Toto prostředí pomáhá uživatelům nasadit odkazy na databázi a vstupní data před jejich místně spouštět skripty. A **místní počítač** účet poskytuje trvalé sdíleném prostředí, který je přístupný prostřednictvím všech projektů. A **místní projekt** účet poskytuje prostředí izolované vývoje pro každý projekt. Aktualizaci pro každé spuštění. A **místní projekt** účet poskytuje rychlejší vývojové prostředí rychle použitím nových změn.

Další rozdíly mezi **místní počítač** a **místní projekt** účty jsou uvedeny v následující tabulce:

|Úhel rozdíl|Místní počítač|Místní projekt|
|----------------|---------------|---------------|
|Místní přístup|Je přístupný všechny projekty.|K tomuto účtu můžete přistupovat pouze odpovídajícího projektu.|
|Kořenové složky místní data|Trvalé místní složky. Konfigurované prostřednictvím **nástroje** > **Data Lake** > **možnosti a nastavení**.|Dočasnou složku pro každý místní spuštění v rámci projektu U-SQL, pracovní adresář vytvořit. Získá složce čištění po novém sestavení nebo se stane, spusťte znovu.|
|Vstupní data pro skript U-SQL|Relativní cesta v kořenové složce trvalé místní data.|Nastavit prostřednictvím **vlastnost projektu U-SQL** > **zdroj testovacích dat**. Ke kořenové složce dočasných dat před místním spuštění se zkopírují všechny soubory a podsložky.|
|Výstupní data pro skript U-SQL|Relativní cesta v kořenové složce trvalé místní data.|Výstup do kořenové složky dočasná data. Výsledky jsou čištění po novém sestavení nebo se stane, spusťte znovu.|
|Nasazení odkazované databáze|Odkazované databáze se nenasazují automaticky při spouštění **místní počítač** účtu. Je stejný pro odeslání do účtu Azure Data Lake Analytics.|Odkazované databáze jsou nasazeny **místní projekt** účtu automaticky před místní spuštění. Všechna prostředí databáze jsou Vyčištěná a znovu nasadil při opětovné sestavení nebo se stane, spusťte znovu.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Místní spuštění s U-SQL SDK

Můžete spouštět skripty U-SQL místně v sadě Visual Studio a také pomocí sady SDK Azure Data Lake U-SQL místně spouštět skripty U-SQL pomocí rozhraní příkazového řádku a programovacích. Pomocí těchto rozhraní můžete automatizovat místní spuštění U-SQL a testy.

Další informace o [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Další postup

- [Jak vytvořit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Tom, jak testovat kód Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
