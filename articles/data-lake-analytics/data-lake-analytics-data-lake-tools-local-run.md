---
title: Spuštění Azure Data Lake skriptů U-SQL na místním počítači
description: Naučte se používat Nástroje Azure Data Lake pro Visual Studio ke spouštění úloh U-SQL na místním počítači.
author: liudan66
ms.author: liud
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 07/03/2018
ms.openlocfilehash: 5f9410cc91174420662bb5efc67c8904b5d5e647
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018983"
---
# <a name="run-u-sql-scripts-on-your-local-machine"></a>Spouštění skriptů U-SQL na místním počítači

Když vyvíjíte skripty U-SQL, můžete ušetřit čas a výdaje spuštěním skriptů místně. Nástroje Azure Data Lake pro Visual Studio podporuje spouštění skriptů U-SQL na místním počítači. 

## <a name="basic-concepts-for-local-runs"></a>Základní pojmy pro místní běhy

Následující graf znázorňuje komponenty pro místní spuštění a způsob mapování těchto součástí do cloudového spuštění.

|Součást|Místní spuštění|Spuštění cloudu|
|---------|---------|---------|
|Storage|Kořenová složka místních dat|Výchozí účet Azure Data Lake Store|
|Compute|Místní modul runtime U-SQL|Služba Azure Data Lake Analytics|
|Spustit prostředí|Pracovní adresář v místním počítači|Cluster Azure Data Lake Analytics|

Níže uvedené části poskytují další informace o místních součástech pro spuštění.

### <a name="local-data-root-folders"></a>Kořenové složky místních dat

Kořenová složka místních dat je **místní úložiště** pro místní výpočetní účet. Každá složka v místním systému souborů na místním počítači může být kořenová složka místních dat. Je stejný jako výchozí účet Azure Data Lake Store účtu Data Lake Analytics. Přepnutí na jinou kořenovou složku dat je stejně jako přepnutí na jiný výchozí účet úložiště. 

Složka data root se používá následujícím způsobem:
- Ukládat metadata Příklady jsou databáze, tabulky, funkce vracející tabulku a sestavení.
- Vyhledejte vstupní a výstupní cesty, které jsou definovány jako relativní cesty ve skriptech U-SQL. Pomocí relativních cest je snazší nasadit skripty U-SQL do Azure.

### <a name="u-sql-local-run-engines"></a>Moduly místního spuštění U-SQL

Místní modul runtime U-SQL je **místní výpočetní účet** pro úlohy u-SQL. Uživatelé můžou spouštět úlohy U-SQL místně prostřednictvím Nástroje Azure Data Lake pro Visual Studio. Místní běhy jsou také podporovány prostřednictvím rozhraní příkazového řádku sady Azure Data Lake U-SQL SDK a programovacích rozhraní. Přečtěte si další informace o [Azure Data Lake SDK U-SQL](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directories"></a>Pracovní adresáře

Když spustíte skript U-SQL, je pracovní složka pracovního adresáře nutná k ukládání výsledků kompilace do mezipaměti, spuštění protokolů a provádění dalších funkcí. V Nástroje Azure Data Lake pro Visual Studio pracovní adresář je pracovní adresář v projektu U-SQL. Je umístěný v `<U-SQL project root path>/bin/debug>` . Pracovní adresář se vyčistí při každém spuštění nového spuštění.

## <a name="local-runs-in-microsoft-visual-studio"></a>Místní spuštění v Microsoft Visual Studio

Nástroje Azure Data Lake pro Visual Studio mít integrovaný místní modul spuštění. Nástroje surfují modul jako místní výpočetní účet. Chcete-li spustit skript U-SQL místně, vyberte v rozevírací nabídce okraje editoru skriptu účet **místní počítač** nebo **místní projekt** . Pak vyberte **Odeslat**.

![Odeslání skriptu U-SQL do místního účtu](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-runs-with-a-local-machine-account"></a>Místní spuštění s účtem místního počítače

Účet **místního počítače** je sdílený místní výpočetní účet s jednou adresářovou kořenovou složkou místních dat jako s účtem místního úložiště. Ve výchozím nastavení se složka data root nachází v adresáři **C:\Users \<username> \AppData\Local\USQLDataRoot**. Dá se taky nakonfigurovat prostřednictvím **nástrojů**  >  **Data Lake**  >  **možností a nastavení**.

![Konfigurace kořenové složky místních dat](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Pro místní spuštění je vyžadován projekt U-SQL. Pracovní adresář v projektu U-SQL se používá pro místní běhový pracovní adresář U-SQL. Výsledky kompilace, spuštění protokolů a další soubory související s spuštěním úlohy se generují a ukládají do složky pracovního adresáře během místního spuštění. Pokaždé, když znovu spustíte skript, všechny soubory v pracovním adresáři se vyčistí a znovu vygenerují.

## <a name="local-runs-with-a-local-project-account"></a>Místní spuštění s účtem místního projektu

Účet **místní projekt** je místní výpočetní účet izolovaný na projektu pro každý projekt s kořenovou složkou izolované místní data. Každý aktivní projekt U-SQL, který se otevírá v Průzkumník řešení v aplikaci Visual Studio má odpovídající `(Local-project: <project name>)` účet. Účty jsou uvedeny v Průzkumník serveru v aplikaci Visual Studio a na okraji editoru skriptu U-SQL.  

Účet **místní projekt** poskytuje čisté a izolované vývojové prostředí. Účet **místního počítače** má sdílenou kořenovou složku místních dat, která ukládá metadata a vstupní a výstupní data pro všechny místní úlohy. Účet **místního projektu** ale při každém spuštění skriptu u-SQL vytvoří dočasnou kořenovou složku místních dat v pracovním adresáři u-SQL projektu. Tato dočasná kořenová složka dat je vyčištěna, když dojde k opakovanému sestavení nebo opětovnému spuštění. 

Projekt U-SQL spravuje izolované místní běhové prostředí prostřednictvím odkazu a vlastnosti projektu. Vstupní zdroje dat pro skripty U-SQL můžete nakonfigurovat v rámci projektu i v odkazovaných databázových prostředích.

### <a name="manage-the-input-data-source-for-a-local-project-account"></a>Správa vstupního zdroje dat pro účet místní projekt 

Projekt U-SQL vytvoří kořenovou složku místních dat a nastaví data pro účet **místního projektu** . Dočasná kořenová složka dat se vyčistí a znovu vytvoří v pracovním adresáři U-SQL projektu pokaždé, když dojde k opětovnému sestavení a místnímu spuštění. Všechny zdroje dat, které jsou konfigurovány projektem U-SQL, se zkopírují do této dočasné kořenové složky místních dat, než se spustí místní úloha. 

Můžete nakonfigurovat kořenovou složku zdrojů dat. Klikněte pravým tlačítkem na  >    >  **zdroj dat testu** vlastnosti projektu U-SQL. Když spustíte skript U-SQL na účtu **místní projekt** , všechny soubory a podsložky ve složce **zdroje testovacích dat** se zkopírují do dočasné místní kořenové složky dat. Jsou zahrnuty soubory podsložek. Po spuštění místní úlohy je možné výsledky výstupů najít také v dočasné místní složce pro kořen dat v pracovním adresáři projektu. Veškerý tento výstup se odstraní a vyčistí, když se projekt znovu sestaví a vyčistí. 

![Konfigurace zdroje testovacích dat projektu](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-a-referenced-database-environment-for-a-local-project-account"></a>Správa odkazovaného databázového prostředí pro účet **místní projekt** 

Pokud dotaz U-SQL používá nebo se dotazuje s objekty databáze U-SQL, je nutné, aby byla prostředí databáze připravena místně před spuštěním skriptu U-SQL místně. Pro účet **místního projektu** lze závislosti u-SQL Database spravovat pomocí odkazů na projekt u-SQL. Do projektu U-SQL můžete přidat odkazy na projekt databáze U-SQL. Před spuštěním skriptů U-SQL na účtu **místní projekt** budou všechny odkazované databáze nasazeny do dočasné místní kořenové složky dat. U každého spuštění se složka dočasných dat vyčistí jako čerstvé izolované prostředí.

Další informace najdete v tomto souvisejícím článku:
* Naučte se spravovat definice a odkazy databáze U-SQL v [projektech databáze u-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="the-difference-between-local-machine-and-local-project-accounts"></a>Rozdíl mezi účty **místní počítač** a **místní projekt**

Účet **místního počítače** simuluje účet Azure Data Lake Analytics v místních počítačích uživatelů. Sdílí stejné prostředí s účtem Azure Data Lake Analytics. Účet **místní projekt** nabízí uživatelsky přívětivé místní vývojové prostředí. Toto prostředí pomáhá uživatelům nasazovat databázové odkazy a vstupní data předtím, než budou spouštět skripty místně. Účet **místního počítače** poskytuje sdílené trvalé prostředí, ke kterému lze přihlédnout prostřednictvím všech projektů. Účet **místního projektu** poskytuje izolované vývojové prostředí pro každý projekt. Aktualizuje se pro každé spuštění. Účet **místního projektu** nabízí rychlejší vývojové prostředí, které rychle aplikuje nové změny.

V následující tabulce jsou uvedeny další rozdíly mezi účty **místní počítač** a **místní projekt** :

|Úhel rozdílů|Místní počítač|Místní projekt|
|----------------|---------------|---------------|
|Místní přístup|Lze k němu přistupovat všechny projekty.|K tomuto účtu má přístup jenom odpovídající projekt.|
|Kořenová složka místních dat|Trvalá místní složka. Nakonfigurováno prostřednictvím **nástrojů**  >  **Data Lake**  >  **možností a nastavení**.|Dočasná složka vytvořená pro každý místní běh v pracovním adresáři U-SQL projektu. Složka se vyčistí, když dojde k opakovanému sestavení nebo opětovnému spuštění.|
|Vstupní data pro skript U-SQL|Relativní cesta v rámci trvalé místní kořenové složky dat|Nastaví se ve zdroji dat testu **Vlastnosti projektu U-SQL**  >  . Všechny soubory a podsložky se zkopírují do kořenové složky dočasných dat před místním spuštěním.|
|Výstupní data pro skript U-SQL|Relativní cesta pod kořenovou složkou trvalého místního data|Výstup do dočasné kořenové složky dat Výsledky jsou vyčištěny, když dojde k opakovanému sestavení nebo opětovnému spuštění.|
|Nasazení odkazované databáze|Odkazované databáze nejsou nasazeny automaticky, pokud jsou spuštěny v účtu **místního počítače** . Je totéž pro odeslání do účtu Azure Data Lake Analytics.|Odkazované databáze jsou do účtu **místního projektu** nasazeny automaticky před místním spuštěním. Všechna databázová prostředí se vyčistí a znovu nasazují, když dojde k opakovanému sestavení nebo opětovnému spuštění.|

## <a name="a-local-run-with-the-u-sql-sdk"></a>Místní spuštění pomocí sady U-SQL SDK

Můžete spouštět skripty U-SQL lokálně v sadě Visual Studio a také pomocí Azure Data Lake sady U-SQL SDK spouštět skripty U-SQL místně pomocí příkazového řádku a programovacích rozhraní. Prostřednictvím těchto rozhraní můžete automatizovat místní běhy U-SQL a testy.

Přečtěte si další informace o [Azure Data Lake SDK U-SQL](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Další kroky

- [Jak nastavit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md).
- [Postup testování kódu Azure Data Lake Analytics](data-lake-analytics-cicd-test.md).
