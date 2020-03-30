---
title: Identifikujte scénáře pro Azure Machine Learning – proces týmové datové vědy
description: Vyberte příslušné scénáře pro provádění pokročilých prediktivních analýz pomocí procesu vědecké analýzy týmových dat.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 48b51c40e5de8f10d9d1d16b02e2c70b045816b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251619"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scénáře pro pokročilé analýzy ve službě Azure Machine Learning
Tento článek popisuje různé ukázkové zdroje dat a cílové scénáře, které mohou být zpracovány [procesem vědecké vědy o týmových datech (TDSP).](overview.md) TDSP poskytuje systematický přístup pro týmy ke spolupráci na vytváření inteligentních aplikací. Zde uvedené scénáře ilustrují možnosti dostupné v pracovním postupu zpracování dat, které závisí na charakteristikách dat, zdrojových umístěních a cílových úložištích v Azure.

Rozhodovací **strom** pro výběr ukázkových scénářů, které jsou vhodné pro vaše data a cíl, je uveden v poslední části.

Každá z následujících částí představuje ukázkový scénář. Pro každý scénář jsou uvedeny možné datové vědy nebo pokročilé analytické toky a podporu prostředků Azure jsou uvedeny.

> [!NOTE]
> **Pro všechny následující scénáře je třeba:**
> <br/>
> 
> * [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md)
>   <br/>
> * [Vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>Scénář \#1: Malá až střední tabulková datová sada v místních souborech
![Malé až střední místní soubory][1]

#### <a name="additional-azure-resources-none"></a>Další prostředky Azure: Žádné
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Nahrajte datovou sadu.
1. Vytvořte tok experimentu Azure Machine Learning počínaje nahrané datové sady.

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>Scénář \#2: Malá až střední datová sada místních souborů, které vyžadují zpracování
![Malé až střední místní soubory se zpracováním][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Další prostředky Azure: Virtuální počítač Azure (notebookový server IPython)
1. Vytvořte virtuální počítač Azure se systémem IPython Notebook.
1. Nahrajte data do kontejneru úložiště Azure.
1. Předzpracování a čištění dat v Poznámkovém bloku IPython, přístup k datům z kontejneru úložiště Azure.
1. Transformace dat do vyčištěné tabulkové formy.
1. Ukládejte transformovaná data do objektů BLOB Azure.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Přečtěte si data z objektů BLOB Azure pomocí modulu [Import dat.][import-data]
1. Vytvořte tok experimentu Azure Machine Learning počínaje ingestovanými datovými sadami.

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>Scénář \#3: Velká datová sada místních souborů, cílení na objekty Blob Azure
![Velké místní soubory][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Další prostředky Azure: Virtuální počítač Azure (notebookový server IPython)
1. Vytvořte virtuální počítač Azure se systémem IPython Notebook.
1. Nahrajte data do kontejneru úložiště Azure.
1. Předzpracování a čištění dat v Poznámkovém bloku IPython, přístup k datům z objektů BLOB Azure.
1. V případě potřeby transformujte data do vyčištěné tabulkové formy.
1. Prozkoumejte data a podle potřeby vytvářejte funkce.
1. Extrahujte ukázku dat s malými a středními daty.
1. Uložte ukázková data v objektech BLOB Azure.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Přečtěte si data z objektů BLOB Azure pomocí modulu [Import dat.][import-data]
1. Sestavte tok experimentu Azure Machine Learning počínaje ingestovanými datovými sadami.

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>Scénář \#4: Malá až střední datová sada místních souborů, cílení na SQL Server ve virtuálním počítači Azure
![Malé až střední místní soubory do SQL DB v Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: Virtuální počítač Azure (SQL Server / Notebook ový server IPython)
1. Vytvořte virtuální počítač Azure se systémem SQL Server + Poznámkový blok IPython.
1. Nahrajte data do kontejneru úložiště Azure.
1. Předběžně zpracujte a vyčistěte data v kontejneru úložiště Azure pomocí poznámkového bloku IPython.
1. V případě potřeby transformujte data do vyčištěné tabulkové formy.
1. Ukládat data do místních souborů virtuálních zařízení (Poznámkový blok IPython běží na virtuálním počítači, místní jednotky odkazují na jednotky virtuálních počítačů).
1. Načtěte data do databáze SQL Serveru spuštěné na virtuálním počítači Azure.
   
   Možnost \#1: Použití sql server management studio.
   
   * Přihlášení k virtuálnímu virtuálnímu ms serveru SQL Server
   * Spusťte sql server management studio.
   * Vytvořte databázové a cílové tabulky.
   * Pomocí jedné z metod hromadného importu načtěte data z místních souborů virtuálního připojení.
   
   Možnost \#2: Použití notebooku IPython – není vhodné pro střední a větší datové sady
   
   <!-- -->    
   * Pomocí připojovacího řetězce ODBC pro přístup k serveru SQL Server na virtuálním počítači.
   * Vytvořte databázové a cílové tabulky.
   * Pomocí jedné z metod hromadného importu načtěte data z místních souborů virtuálního připojení.
1. Prozkoumejte data, vytvářejte funkce podle potřeby. Funkce není nutné zhmotnit v databázových tabulkách. Všimněte si pouze potřebného dotazu k jejich vytvoření.
1. V případě potřeby a/nebo v případě potřeby rozhodněte o velikosti vzorku dat.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Čtení dat přímo ze serveru SQL Server pomocí modulu [Import dat.][import-data] Vložte potřebný dotaz, který extrahuje pole, vytvoří funkce a v případě potřeby ukažuje data přímo v dotazu [Importovat data.][import-data]
1. Sestavte tok experimentu Azure Machine Learning počínaje ingestovanými datovými sadami.

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>Scénář \#5: Velká datová sada v místních souborech, cílový SQL Server ve virtuálním počítači Azure
![Velké místní soubory do SQL DB v Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: Virtuální počítač Azure (SQL Server / Notebook ový server IPython)
1. Vytvořte virtuální počítač Azure se systémem SQL Server a notebookový server IPython.
1. Nahrajte data do kontejneru úložiště Azure.
1. (Nepovinné) Předzpracování min. a čištění dat.
   
    a.  Předzpracování a čištění dat v Poznámkovém bloku IPython, přístup k datům z objektů BLOB Azure.
   
    b.  V případě potřeby transformujte data do vyčištěné tabulkové formy.
   
    c.  Ukládat data do místních souborů virtuálních zařízení (Poznámkový blok IPython běží na virtuálním počítači, místní jednotky odkazují na jednotky virtuálních počítačů).
1. Načtěte data do databáze SQL Serveru spuštěné na virtuálním počítači Azure.
   
    a.  Přihlaste se k virtuálnímu virtuálnímu ms serveru SQL Server.
   
    b.  Pokud data ještě nejsou uložená, stáhněte datové soubory z kontejneru úložiště Azure do složky místního virtuálního počítače.
   
    c.  Spusťte sql server management studio.
   
    d.  Vytvořte databázové a cílové tabulky.
   
    e.  K načtení dat použijte jednu z metod hromadného importu.
   
    f.  Pokud jsou požadována spojení tabulky, vytvořte indexy pro urychlení spojení.
   
   > [!NOTE]
   > Pro rychlejší načítání velkých velikostí dat se doporučuje vytvořit rozdělené tabulky a hromadně importovat data paralelně. Další informace naleznete [v tématu Paralelní import dat do tabulek oddílů SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Prozkoumejte data, vytvářejte funkce podle potřeby. Funkce není nutné zhmotnit v databázových tabulkách. Všimněte si pouze potřebného dotazu k jejich vytvoření.
1. V případě potřeby a/nebo v případě potřeby rozhodněte o velikosti vzorku dat.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Čtení dat přímo ze serveru SQL Server pomocí modulu [Import dat.][import-data] Vložte potřebný dotaz, který extrahuje pole, vytvoří funkce a v případě potřeby ukažuje data přímo v dotazu [Importovat data.][import-data]
1. Jednoduchý tok experimentu Azure Machine Learning počínaje nahranou datovou sadou

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>Scénář \#6: Velká datová sada v místní databázi serveru SQL Server, cílení na SQL Server ve virtuálním počítači Azure
![Velké SQL DB on-prem na SQL DB v Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: Virtuální počítač Azure (SQL Server / Notebook ový server IPython)
1. Vytvořte virtuální počítač Azure se systémem SQL Server a notebookový server IPython.
1. Pomocí jedné z metod exportu dat exportujte data ze serveru SQL Server do souborů s výpisem stavu dat.
   
   > [!NOTE]
   > Pokud se rozhodnete přesunout všechna data z místní databáze, alternativní (rychlejší) metoda přesunout úplnou databázi do instance SQL Server v Azure. Přeskočte kroky k exportu dat, vytvoření databáze a načtení/importdat do cílové databáze a postupujte podle alternativní metody.
   > 
   > 
1. Nahrajte soubory výpisu do kontejneru úložiště Azure.
1. Načtěte data do databáze serveru SQL Server spuštěné ve virtuálním počítači Azure.
   
   a.  Přihlaste se k virtuálnímu virtuálnímu serveru SQL Server.
   
   b.  Stáhněte datové soubory z kontejneru úložiště Azure do složky místního virtuálního počítače.
   
   c.  Spusťte sql server management studio.
   
   d.  Vytvořte databázové a cílové tabulky.
   
   e.  K načtení dat použijte jednu z metod hromadného importu.
   
   f.  Pokud jsou požadována spojení tabulky, vytvořte indexy pro urychlení spojení.
   
   > [!NOTE]
   > Pro rychlejší načítání velkých velikostí dat vytvořte rozdělené tabulky a hromadně importujte data paralelně. Další informace naleznete [v tématu Paralelní import dat do tabulek oddílů SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Prozkoumejte data, vytvářejte funkce podle potřeby. Funkce není nutné zhmotnit v databázových tabulkách. Všimněte si pouze potřebného dotazu k jejich vytvoření.
1. V případě potřeby a/nebo v případě potřeby rozhodněte o velikosti vzorku dat.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Čtení dat přímo ze serveru SQL Server pomocí modulu [Import dat.][import-data] Vložte potřebný dotaz, který extrahuje pole, vytvoří funkce a v případě potřeby ukažuje data přímo v dotazu [Importovat data.][import-data]
1. Tok jednoduchého experimentu Azure Machine Learning počínaje nahranou datovou sadou.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternativní metoda kopírování úplné databáze z místního sql serveru do databáze Azure SQL Database
![Odpojit místní databázi a připojit se k SQL DB v Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: Virtuální počítač Azure (SQL Server / Notebook ový server IPython)
Chcete-li replikovat celou databázi serveru SQL Server ve vašem virtuálním počítači sql serveru, měli byste zkopírovat databázi z jednoho umístění nebo serveru do jiného, za předpokladu, že databáze může být dočasně offline. Můžete použít SQL Server Management Studio Object Explorer nebo pomocí ekvivalentních příkazů Transact-SQL.

1. Odpojte databázi ve zdrojovém umístění. Další informace naleznete v [tématu Detach a database](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. V okně Průzkumník Windows nebo Příkazový řádek systému Windows zkopírujte odpojený soubor databáze nebo soubory a soubory protokolu nebo soubory protokolu do cílového umístění na virtuálním počítači SQL Server v Azure.
1. Připojte zkopírované soubory k cílové instanci serveru SQL Server. Další informace naleznete [v tématu Attach a Database](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Přesunutí databáze pomocí odpojení a připojení (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>Scénář \#7: Velká data v místních souborech, cílová databáze Hive v clusterech Azure HDInsight Hadoop
![Velké objemy dat v místním cílovém úlu][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Další prostředky Azure: Cluster Azure HDInsight Hadoop a Virtuální počítač Azure (notebookový server IPython)
1. Vytvořte virtuální počítač Azure se systémem IPython Notebook server.
1. Vytvořte cluster Azure HDInsight Hadoop.
1. (Nepovinné) Předzpracování min. a čištění dat.
   
   a.  Předběžné zpracování a čištění dat v poznámkovém bloku IPython, přístup k datům z Azure
   
       blobs.
   
   b.  V případě potřeby transformujte data do vyčištěné tabulkové formy.
   
   c.  Ukládat data do místních souborů virtuálních zařízení (Poznámkový blok IPython běží na virtuálním počítači, místní jednotky odkazují na jednotky virtuálních počítačů).
1. Nahrajte data do výchozího kontejneru clusteru Hadoop vybraného v kroku 2.
1. Načtěte data do databáze Hive v clusteru Azure HDInsight Hadoop.
   
   a.  Přihlášení k hlavnímu uzlu clusteru Hadoop
   
   b.  Otevřete příkazový řádek Hadoopu.
   
   c.  Zadejte kořenový adresář `cd %hive_home%\bin` Hive příkazem Hadoop Command Line.
   
   d.  Spusťte dotazy Hive k vytvoření databáze a tabulek a načtěte data z úložiště objektů blob do tabulek Hive.
   
   > [!NOTE]
   > Pokud jsou data velká, uživatelé mohou vytvořit tabulku Hive s oddíly. Potom mohou uživatelé `for` použít smyčku v příkazovém řádku Hadoop na hlavním uzlu k načtení dat do tabulky Hive rozdělené oddílem.
   > 
   > 
1. Prozkoumejte data a vytvářejte funkce podle potřeby v příkazovém řádku Hadoop. Funkce není nutné zhmotnit v databázových tabulkách. Všimněte si pouze potřebného dotazu k jejich vytvoření.
   
   a.  Přihlášení k hlavnímu uzlu clusteru Hadoop
   
   b.  Otevřete příkazový řádek Hadoopu.
   
   c.  Zadejte kořenový adresář `cd %hive_home%\bin` Hive příkazem Hadoop Command Line.
   
   d.  Spusťte dotazy Hive v příkazovém řádku Hadoop na hlavním uzlu clusteru Hadoop, prozkoumejte data a vytvořte funkce podle potřeby.
1. V případě potřeby nebo požadované, ukázka dat, aby se vešly do Azure Machine Learning Studio.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Načtěte data `Hive Queries` přímo z modulu [Using Import Data.][import-data] Vložte potřebný dotaz, který extrahuje pole, vytvoří funkce a v případě potřeby ukažuje data přímo v dotazu [Importovat data.][import-data]
1. Tok jednoduchého experimentu Azure Machine Learning počínaje nahranou datovou sadou.

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Rozhodovací strom pro výběr scénáře
---
Následující diagram shrnuje výše popsané scénáře a provedené volby Rozšířeného procesu a technologií, které vás přenese do jednotlivých rozepsaných scénářů. Zpracování dat, zkoumání, konstrukce funkcí a odběr vzorků mohou probíhat v jedné nebo více metodách/prostředích – ve zdrojovém, mezilehlém nebo cílovém prostředí – a podle potřeby mohou pokračovat iterativně. Diagram slouží pouze jako ilustrace některých možných toků a neposkytuje vyčerpávající výčet.

![Ukázkové scénáře zpracování ds][8]

### <a name="advanced-analytics-in-action-examples"></a>Příklady pokročilé analýzy v akci
Pro komplexní Azure Machine Learning návody, které využívají pokročilý analytický proces a technologie pomocí veřejných datových sad, najdete v tématu:

* [Proces vědecké ho služky týmu v akci: pomocí serveru SQL Server](sql-walkthrough.md).
* [Proces vědecké ho služky týmu v akci: pomocí clusterů HDInsight Hadoop](hive-walkthrough.md).

[1]: ./media/plan-sample-scenarios/dsp-plan-small-in-aml.png
[2]: ./media/plan-sample-scenarios/dsp-plan-local-with-processing.png
[3]: ./media/plan-sample-scenarios/dsp-plan-local-large.png
[4]: ./media/plan-sample-scenarios/dsp-plan-local-to-db.png
[5]: ./media/plan-sample-scenarios/dsp-plan-large-to-db.png
[6]: ./media/plan-sample-scenarios/dsp-plan-db-to-db.png
[7]: ./media/plan-sample-scenarios/dsp-plan-attach-db.png
[8]: ./media/plan-sample-scenarios/dsp-plan-sample-scenarios.png
[9]: ./media/plan-sample-scenarios/dsp-plan-local-to-hive.png


<!-- Module References -->
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
