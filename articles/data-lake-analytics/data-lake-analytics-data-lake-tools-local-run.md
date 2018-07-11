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
ms.openlocfilehash: a7f43c7e17f36d9b4e0767744eee9604c2628ea8
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/06/2018
ms.locfileid: "37888962"
---
# <a name="run-u-sql-script-on-your-local-machine"></a>Spusťte skript U-SQL na místním počítači

Při vývoji skript U-SQL, je běžné spouštět skript U-SQL místně, jak šetří náklady a čas. Azure Data Lake Tools pro Visual Studio podporuje spouštění skriptů U-SQL na místním počítači. 

## <a name="basic-concepts-for-local-run"></a>Základní koncepty pro místní spuštění

Následující graf zobrazuje součásti pro místní spuštění a jak tyto komponenty se mapují na cloud spustit.

|Komponenta|Místní spuštění|Spustit cloud|
|---------|---------|---------|
|Úložiště|Místní Data kořenové složky|Účet výchozí Azure Data Lake Store|
|Compute|Modul pro místní spuštění U-SQL|Služba Azure Data Lake Analytics|
|Spuštění prostředí|Pracovní adresář na místním počítači|Azure Data Lake Analytics clusteru|

Podrobnější vysvětlení pro místní běh součásti:

### <a name="local-data-root-folder"></a>Místní Data kořenové složky

Pro místní výpočetní účet je místní Data kořenová složka "místní úložiště". Všechny složky v místním systému souborů na místním počítači může být místní Data kořenovou složku. Je rovno výchozího účtu Azure Data Lake Store účtu Data Lake Analytics. Přepnutí na jinou složku kořen dat je stejně jako přepnutí na jiný výchozí účet úložiště. 

Data kořenové složky se používá pro:
- Store metadata, jako jsou databáze, tabulky, funkce vracející tabulku nebo sestavení.
- Vyhledání vstupní a výstupní cesty, které jsou definované jako relativní cesty ve skriptu U-SQL. Pomocí relativní cesty usnadňuje nasazení skriptů U-SQL do Azure.

### <a name="u-sql-local-run-engine"></a>Modul pro místní spuštění U-SQL

Modul pro místní spuštění U-SQL je "místní výpočetní účet" pro úloh U-SQL. Uživatelé mohou spouštět úloh U-SQL místně prostřednictvím nástrojů Azure Data Lake pro Visual Studio. Místní spuštění také podporu prostřednictvím rozhraní příkazového řádku a programovacích Azure Data Lake U-SQL SDK. [Další informace o Azure Data Lake U-SQL SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/).

### <a name="working-directory"></a>Pracovní adresář

Při spouštění skriptu U-SQL, je potřeba pracovní adresář složku pro ukládání do mezipaměti výsledky kompilace, protokoly spuštění a tak dále. V Azure Data Lake Tools pro Visual Studio, pracovní adresář je projekt U-SQL pracovní adresář (obvykle je najdete v části `<U-SQL project root path>/bin/debug>`). V pracovním adresáři se začnou pokaždé, když se nové spuštění.

## <a name="local-run-in-visual-studio"></a>Místní spuštění v sadě Visual Studio

Azure Data Lake Tools pro Visual Studio má integrované místní spuštění modulu a zobrazí ho jako místní výpočetní účet. Ke spuštění skriptu U-SQL místně, vyberte (místní počítač) nebo účet (Local projektu) v okraj editoru skriptu rozevíracího seznamu a klikněte na tlačítko **odeslat**.

![Data Lake Tools pro Visual Studio odeslat skript do místního účtu](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-submit-script-to-local-account.png) 
 
## <a name="local-run-with-local-machine-account"></a>Místní spuštění s účtem (místní počítač)

Účet (místní počítač) je sdílený místní výpočetní účet s jednu místní složku kořen dat jako účet místního úložiště. Data kořenová složka je ve výchozím nastavení nachází v "C:\Users\<uživatelské jméno > \AppData\Local\USQLDataRoot", je také prostřednictvím **nástroje > Data Lake > Možnosti a nastavení**.

![Data Lake Tools pro Visual Studio nakonfigurovat kořen místních dat](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-local-data-root.png)
  
Projekt v U-SQL je vyžadováno pro místní spuštění. Projekt U-SQL pracovní adresář se používá pro pracovní adresář místního spuštění U-SQL. Výsledky kompilace, protokoly spuštění a další úlohy související s spuštění soubory jsou generovány a uložené ve složce pracovní adresář při místním spuštění. Všimněte si, že pokaždé, když se skript spustit znovu, tyto soubory v pracovním adresáři se vyčistí a znovu vygeneroval.

## <a name="local-run-with-local-project-account"></a>Místní spuštění s účet (Local projektu)

Účet (local-project) je samostatný projekt místní výpočetní účet pro každý projekt s izolované místní Data kořenové složky. Každý aktivní projekt U-SQL otevřít v Průzkumníkovi řešení má odpovídající `(Local-project: <project name>)` účet uvedený v Průzkumníku serveru a jazykem U-SQL okraj editoru skriptů. 

Účet (Local-project) poskytuje přehledné a izolované vývojové prostředí pro vývojáře. Na rozdíl od účtu (místní počítač), který má sdílené místní složky kořen dat ukládání metadat a vstupní a výstupní data pro všechny místní úlohy účet (Local-project) vytvoří dočasnou složku kořen dat místní v rámci pracovního adresáře projektu U-SQL pokaždé, když se skript U-SQL se bude spouštět. Tuto dočasnou složku kořen dat získá vyčištěna, pokud dojde znovu sestavit nebo znovu spustit. 

Projekt v U-SQL poskytuje dobré prostředí pro správu této izolované místní spuštění prostředí prostřednictvím odkazu na projekt a vlastnosti. Jak nakonfigurujete zdroje vstupní data pro skripty U-SQL v projektu, stejně jako odkazovaná databáze prostředí.

### <a name="manage-input-data-source-for-local-project-account"></a>Správa zdroje vstupní data pro účet (Local projektu)

Projekt U-SQL se postará o vytvoření složky kořen dat místního a data nastavení pro účet (Local projektu). Dočasné složky kořen dat je Vyčištěná a znovu vytvořit v rámci pracovního adresáře projektu U-SQL, pokaždé, když se stane opětovné sestavení a místní spuštění. Všechny zdroje dat nakonfiguroval projekt U-SQL se zkopírují na tento dočasný místní složku kořen dat před spuštěním místní úlohy. 

Můžete nakonfigurovat v kořenové složce zdrojů dat prostřednictvím **klikněte pravým tlačítkem na projekt v U-SQL > Vlastnosti > zdroj testovacích dat.**. Při spouštění skriptu U-SQL na účet (Local projektu), všechny soubory a podsložky (včetně soubory v podsložkách) **zdroj testovacích dat.** složce se zkopírují do dočasné složky kořen dat místního. Po dokončení provádění místní úlohy, výstup také najdete v části dočasné složky místního kořen dat v pracovním adresáři projektu. Všimněte si, že všechny tyto výstupy se odstraní a vyčistit při získá projekt znovu sestavit a vyčistit. 

![Data Lake Tools pro Visual Studio konfigurace zdroje dat projektu testu](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-configure-project-test-data-source.png)

### <a name="manage-referenced-database-environment-for-local-project-account"></a>Správa prostředí odkazovaná databáze pro účet (Local projektu) 

Pokud U-SQL dotazu používá či dotazy s objekty databáze U-SQL, musíte udělat prostředí databáze připravená místně před spuštěním tohoto skriptu U-SQL místně. Pro účet (Local projektu) může být spravován závislosti databáze U-SQL odkazy na projekt U-SQL. Je možné přidat odkazy projektu databáze U-SQL pro váš projekt v U-SQL. Před spuštěním skriptů U-SQL na účet (Local projektu), jsou všechny odkazované databáze nasazené do dočasné složky kořen dat místního. A pro každé spuštění probíhá čištění dočasné složky kořen dat jako nového izolovaného prostředí.

Související články:
* [Další informace o správě definice databáze U-SQL pomocí databázový projekt U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)
* [Další informace o správě odkaz na databázi U-SQL v projekt v U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)

## <a name="difference-between-local-machine-and-local-project-account"></a>Rozdíl mezi (místní počítač) a účet (Local projektu)

Účet (místní počítač), zaměřuje pro simulaci na místním počítači uživatelova účtu Azure Data Lake Analytics. Pomocí účtu Azure Data Lake Analytics, která sdílí stejné prostředí. (Místní a project) cíle uživatelsky přívětivé místní vývojové prostředí, která uživatelům nasadit odkazy databáze a vstupní data před spuštěním skriptu místně. Účet (místní počítač) poskytuje sdílené trvalé prostředí, které budou přístupné prostřednictvím všech projektů. Účet (local-project) poskytuje prostředí izolované vývoje pro každý projekt a aktualizaci pro každé spuštění. Na základě výše, účet (Local-project) nabízí rychlejší prostředí pro vývoj s použitím nových změn rychle.

Můžete najít další rozdíl mezi (místní počítač) a účet (Local projekt) v grafu následujícím způsobem:

|Úhel rozdíl|(Místní počítač)|(Místní projekt)|
|----------------|---------------|---------------|
|Místní přístup|Je možný přes všechny projekty|Pouze odpovídající project můžete přistupovat k tomuto účtu|
|Kořenové složky místní Data|Trvalé místní složky. Konfigurované prostřednictvím **nástroje > Data Lake > Možnosti a nastavení**|Vytvoří pro každý místní spuštění v rámci pracovního adresáře projektu U-SQL do dočasné složky. Složka získá vyčištěna, pokud dojde znovu sestavit nebo znovu spustit|
|Vstupní data pro skript U-SQL|Relativní cesta trvalý místní Data kořenové složce|Nastavit prostřednictvím **vlastnost projektu U-SQL > zdroj testovacích dat.**. Všechny soubory a podsložky jsou zkopírovány do dočasné složky kořen dat před místním spuštění|
|Výstupní data pro skript U-SQL|Relativní cesta trvalý místní Data kořenové složce|Výstupem dočasných dat kořenovou složku. Výsledky jsou vyčištěna, pokud dojde znovu sestavit nebo znovu spustit.|
|Nasazení odkazované databáze|Odkazované databáze nejsou nasazeni automaticky při spuštění proti účtu (místní počítač). Stejný pro odeslání do účtu Azure Data Lake Analytics.|Odkazované databáze jsou nasazené na účet (Local projektu) automaticky před místní spuštění. Všechna prostředí databáze jsou Vyčištěná a znovu nasadit, když dojde znovu sestavit nebo znovu spustit.|

## <a name="local-run-with-u-sql-sdk"></a>Místní spuštění s U-SQL SDK

Kromě spouštění skriptů U-SQL místně v sadě Visual Studio, můžete také pomocí Azure Data Lake U-SQL SDK místně spouštět skripty U-SQL pomocí rozhraní příkazového řádku a programovacích. Pomocí těchto rozhraní můžete automatizovat místní spuštění U-SQL a testování.

[Další informace o Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md).

## <a name="next-steps"></a>Další kroky

- [Azure Data Lake U-SQL SDK](data-lake-analytics-u-sql-sdk.md)
- [Jak vytvořit kanál CI/CD pro Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [Vývoj databáze U-SQL pomocí databázový projekt U-SQL](data-lake-analytics-data-lake-tools-develop-usql-database.md)
- [Tom, jak testovat kód Azure Data Lake Analytics](data-lake-analytics-cicd-test.md)
