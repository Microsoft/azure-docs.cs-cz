---
title: Identifikace scénářů pro Azure Machine Learning a týmovou vědu pro zpracování dat
description: Vyberte vhodné scénáře pro pokročilou prediktivní analýzu v rámci vědeckého zpracování týmových dat.
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
ms.openlocfilehash: 2d589d6c3394556499daf033c4c1d528a214b0e3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93319291"
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scénáře pro pokročilé analýzy ve službě Azure Machine Learning
Tento článek popisuje řadu ukázkových zdrojů dat a cílových scénářů, které lze zpracovat v rámci [vědeckého zpracování týmových dat (TDSP)](overview.md). TDSP poskytuje systematický přístup pro týmy, které umožňují spolupráci při vytváření inteligentních aplikací. Zde uvedené scénáře znázorňují možnosti dostupné v pracovním postupu zpracování dat, které závisí na charakteristikách dat, zdrojových umístěních a cílových úložištích v Azure.

V poslední části se zobrazí **rozhodovací strom** pro výběr vzorových scénářů, které jsou vhodné pro vaše data a cíl.

V následujících částech najdete ukázkový scénář. V každém scénáři jsou uvedeny možné toky pro datové vědy a pokročilé analýzy a podpůrné prostředky Azure.

> [!NOTE]
> **U všech následujících scénářů potřebujete:**
> <br/>
> 
> * [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md)
>   <br/>
> * [Vytvoření pracovního prostoru Azure Machine Learning](../classic/create-workspace.md)
> 
> 

## <a name="scenario-1-small-to-medium-tabular-dataset-in-local-files"></a><a name="smalllocal"></a>Scénář \# 1: malá a střední datová sada tabulkových dat v místních souborech
![Malé až střední místní soubory][1]

#### <a name="additional-azure-resources-none"></a>Další prostředky Azure: žádné
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Nahrajte datovou sadu.
1. Sestavte Azure Machine Learningho toku experimentu počínaje nahranými datovými prvky.

## <a name="scenario-2-small-to-medium-dataset-of-local-files-that-require-processing"></a><a name="smalllocalprocess"></a>Scénář \# 2: malá a střední datová sada místních souborů, které vyžadují zpracování
![Malé až střední místní soubory se zpracováním][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Další prostředky Azure: virtuální počítač Azure (Server poznámkového bloku IPython)
1. Vytvořte virtuální počítač Azure s poznámkovým blokem IPython.
1. Nahrajte data do kontejneru Azure Storage.
1. Předzpracování a vyčištění dat v IPython poznámkovém bloku s přístupem k datům z kontejneru Azure Storage.
1. Umožňuje transformovat data do vyčištěné tabulky tabulkového formátu.
1. Ukládat transformovaná data v objektech blob Azure
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Pomocí modulu [Import dat][import-data] si přečtěte data z objektů blob Azure.
1. Sestavte Azure Machine Learning tok experimentu počínaje příjmovou datovou sadou (y).

## <a name="scenario-3-large-dataset-of-local-files-targeting-azure-blobs"></a><a name="largelocal"></a>Scénář \# 3: velká datová sada místních souborů, která cílí na objekty blob Azure
![Velké místní soubory][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Další prostředky Azure: virtuální počítač Azure (Server poznámkového bloku IPython)
1. Vytvořte virtuální počítač Azure s poznámkovým blokem IPython.
1. Nahrajte data do kontejneru Azure Storage.
1. Předběžné zpracování a vyčištění dat v IPython poznámkovém bloku s přístupem k datům z objektů blob Azure.
1. V případě potřeby Transformujte data na vyčištěný tabulkový formulář.
1. Prozkoumejte data a podle potřeby Vytvářejte funkce.
1. Extrahujte ukázku dat z malých do střední.
1. Uložte ukázková data do objektů blob Azure.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Pomocí modulu [Import dat][import-data] si přečtěte data z objektů blob Azure.
1. Sestavování Azure Machine Learningho toku experimentů od ingestních datových sad.

## <a name="scenario-4-small-to-medium-dataset-of-local-files-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="smalllocaltodb"></a>Scénář \# 4: malá a střední datová sada místních souborů, která cílí na SQL Server na virtuálním počítači Azure
![Malé až střední místní soubory do databáze SQL v Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: virtuální počítač Azure (SQL Server/Server notebook IPython)
1. Vytvořte virtuální počítač Azure se spuštěným SQL Server a poznámkovým blokem IPython.
1. Nahrajte data do kontejneru Azure Storage.
1. Předběžné zpracování a vyčištění dat v kontejneru Azure Storage pomocí poznámkového bloku IPython
1. V případě potřeby Transformujte data na vyčištěný tabulkový formulář.
1. Uložení dat do místních souborů VM (Poznámkový blok IPython běží na virtuálním počítači, místní jednotky odkazují na jednotky virtuálních počítačů).
1. Načte data do SQL Server databáze běžící na virtuálním počítači Azure.
   
   Možnost \# 1: použití SQL Server Management Studio.
   
   * Přihlášení k SQL Server virtuálního počítače
   * Spusťte SQL Server Management Studio.
   * Vytvořte databázi a cílové tabulky.
   * Pomocí jedné z metod hromadného importu načtěte data z místních souborů virtuálních počítačů.
   
   Možnost \# 2: Použití poznámkového bloku IPython – nejde doporučit pro střední a větší datové sady.
   
   <!-- -->    
   * Pro přístup k SQL Server na virtuálním počítači použijte připojovací řetězec ODBC.
   * Vytvořte databázi a cílové tabulky.
   * Pomocí jedné z metod hromadného importu načtěte data z místních souborů virtuálních počítačů.
1. Prozkoumejte data a podle potřeby vytvořte funkce. Tyto funkce není nutné vymaterializovat v databázových tabulkách. Poznamenejte si pouze nezbytný dotaz pro jejich vytvoření.
1. V případě potřeby určete velikost vzorku dat a/nebo požadovanou velikost.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Pomocí modulu [Import dat][import-data] Přečtěte data přímo z SQL Server. Vložte potřebný dotaz, který extrahuje pole, vytvoří funkce a vzorkuje data v případě potřeby přímo v dotazu [Import dat][import-data] .
1. Sestavování Azure Machine Learningho toku experimentů od ingestních datových sad.

## <a name="scenario-5-large-dataset-in-local-files-target-sql-server-in-azure-vm"></a><a name="largelocaltodb"></a>Scénář \# 5: velká datová sada v místních souborech, cílová SQL Server na virtuálním počítači Azure
![Velké místní soubory do databáze SQL v Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: virtuální počítač Azure (SQL Server/Server notebook IPython)
1. Vytvořte virtuální počítač Azure se spuštěným serverem SQL Server a IPython notebookem.
1. Nahrajte data do kontejneru Azure Storage.
1. Volitelné Předběžná zpracování a čištění dat.
   
    a.  Předběžné zpracování a vyčištění dat v IPython poznámkovém bloku s přístupem k datům z objektů blob Azure.
   
    b.  V případě potřeby Transformujte data na vyčištěný tabulkový formulář.
   
    c.  Uložení dat do místních souborů VM (Poznámkový blok IPython běží na virtuálním počítači, místní jednotky odkazují na jednotky virtuálních počítačů).
1. Načte data do SQL Server databáze běžící na virtuálním počítači Azure.
   
    a.  Přihlaste se k SQL Server virtuálního počítače.
   
    b.  Pokud se data ještě neuložila, Stáhněte si datové soubory z kontejneru Azure Storage do složky Local-VM.
   
    c.  Spusťte SQL Server Management Studio.
   
    d.  Vytvořte databázi a cílové tabulky.
   
    e.  K načtení dat použijte jednu z metod hromadného importu.
   
    f.  Pokud jsou vyžadovány spojení s tabulkami, vytvořte indexy pro urychlení spojení.
   
   > [!NOTE]
   > Pro rychlejší načítání velkých objemů dat doporučujeme vytvořit dělené tabulky a hromadně importovat data. Další informace najdete v tématu [paralelní import dat do dělených tabulek SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Prozkoumejte data a podle potřeby vytvořte funkce. Tyto funkce není nutné vymaterializovat v databázových tabulkách. Poznamenejte si pouze nezbytný dotaz pro jejich vytvoření.
1. V případě potřeby určete velikost vzorku dat a/nebo požadovanou velikost.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Pomocí modulu [Import dat][import-data] Přečtěte data přímo z SQL Server. Vložte potřebný dotaz, který extrahuje pole, vytvoří funkce a vzorkuje data v případě potřeby přímo v dotazu [Import dat][import-data] .
1. Jednoduchý tok experimentu Azure Machine Learning počínaje nahranou datovou sadou

## <a name="scenario-6-large-dataset-in-a-sql-server-database-on-premises-targeting-sql-server-in-an-azure-virtual-machine"></a><a name="largedbtodb"></a>Scénář \# 6: velká datová sada v SQL Server databázi místně, která cílí na SQL Server na virtuálním počítači Azure
![Rozsáhlá databáze SQL DB on-Prem do databáze SQL v Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: virtuální počítač Azure (SQL Server/Server notebook IPython)
1. Vytvořte virtuální počítač Azure se spuštěným serverem SQL Server a IPython notebookem.
1. Pomocí jedné z metod exportu dat exportujte data z SQL Server do souborů s výpisem paměti.
   
   > [!NOTE]
   > Pokud se rozhodnete přesunout všechna data z místní databáze, alternativní (rychlejší) metodu pro přesun úplné databáze do instance SQL Server v Azure. Přeskočte kroky pro export dat, vytvořte databázi a načtěte/importujte data do cílové databáze a použijte alternativní metodu.
   > 
   > 
1. Nahrajte do kontejneru Azure Storage soubory s výpisem paměti.
1. Načtěte data do databáze SQL Server běžící na virtuálním počítači Azure.
   
   a.  Přihlaste se k virtuálnímu počítači s SQL Server.
   
   b.  Stáhněte si datové soubory z kontejneru Azure Storage do složky Local-VM.
   
   c.  Spusťte SQL Server Management Studio.
   
   d.  Vytvořte databázi a cílové tabulky.
   
   e.  K načtení dat použijte jednu z metod hromadného importu.
   
   f.  Pokud jsou vyžadovány spojení s tabulkami, vytvořte indexy pro urychlení spojení.
   
   > [!NOTE]
   > Pro rychlejší načítání velkých objemů dat Vytvářejte dělené tabulky a hromadně importujte data. Další informace najdete v tématu [paralelní import dat do dělených tabulek SQL](parallel-load-sql-partitioned-tables.md).
   > 
   > 
1. Prozkoumejte data a podle potřeby vytvořte funkce. Tyto funkce není nutné vymaterializovat v databázových tabulkách. Poznamenejte si pouze nezbytný dotaz pro jejich vytvoření.
1. V případě potřeby určete velikost vzorku dat a/nebo požadovanou velikost.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Pomocí modulu [Import dat][import-data] Přečtěte data přímo z SQL Server. Vložte potřebný dotaz, který extrahuje pole, vytvoří funkce a vzorkuje data v případě potřeby přímo v dotazu [Import dat][import-data] .
1. Jednoduchý tok experimentu Azure Machine Learning počínaje nahranou datovou sadou.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternativní metoda kopírování úplné databáze z místního SQL Server do Azure SQL Database
![Odpojení místní databáze a připojení k databázi SQL v Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: virtuální počítač Azure (SQL Server/Server notebook IPython)
Pokud chcete replikovat celou SQL Server databázi na svém SQL Serverm VIRTUÁLNÍm počítači, měli byste zkopírovat databázi z jednoho umístění nebo serveru do jiné za předpokladu, že databázi je možné dočasně považovat do offline režimu. Můžete použít SQL Server Management Studio Průzkumník objektů nebo použít ekvivalentní příkazy Transact-SQL.

1. Odpojte databázi ve zdrojovém umístění. Další informace najdete v tématu [odpojení databáze](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
1. V Průzkumníku Windows nebo v okně příkazového řádku Windows zkopírujte odpojený databázový soubor nebo soubory a soubory protokolu nebo soubory do cílového umístění na SQL Serverm VIRTUÁLNÍm počítači v Azure.
1. Připojte zkopírované soubory k cílové instanci SQL Server. Další informace najdete v tématu [připojení databáze](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Přesunutí databáze pomocí příkazu odpojit a připojit (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="scenario-7-big-data-in-local-files-target-hive-database-in-azure-hdinsight-hadoop-clusters"></a><a name="largedbtohive"></a>Scénář \# 7: velké objemy dat v místních souborech, cílová databáze podregistru v Azure HDInsight Hadoopch clusterech
![Velké objemy dat v místním cílovém podregistru][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Další prostředky Azure: Azure HDInsight Hadoop cluster a virtuální počítač Azure (Server poznámkového bloku IPython)
1. Vytvořte virtuální počítač Azure se spuštěným serverem IPython notebook.
1. Vytvořte cluster Azure HDInsight Hadoop.
1. Volitelné Předběžná zpracování a čištění dat.
   
   a.  Předběžné zpracování a vyčištění dat v IPython poznámkovém bloku s přístupem k datům z objektů blob Azure.
   
   b.  V případě potřeby Transformujte data na vyčištěný tabulkový formulář.
   
   c.  Uložení dat do místních souborů VM (Poznámkový blok IPython běží na virtuálním počítači, místní jednotky odkazují na jednotky virtuálních počítačů).
1. Nahrajte data do výchozího kontejneru clusteru Hadoop vybraného v kroku 2.
1. Načte data do databáze podregistru v clusteru Azure HDInsight Hadoop.
   
   a.  Přihlaste se k hlavnímu uzlu clusteru Hadoop.
   
   b.  Otevřete příkazový řádek Hadoop.
   
   c.  Do příkazového řádku Hadoop zadejte kořenový adresář podregistru podle příkazu `cd %hive_home%\bin` .
   
   d.  Spuštěním dotazů na podregistr vytvořte databázi a tabulky a načtěte data z úložiště objektů blob do tabulek podregistru.
   
   > [!NOTE]
   > Pokud jsou data velká, uživatelé mohou vytvořit tabulku podregistru s oddíly. Uživatelé pak mohou použít `for` smyčku v příkazovém řádku Hadoop na hlavním uzlu pro načtení dat do tabulky s oddíly dělené oddílem.
   > 
   > 
1. Prozkoumejte data a podle potřeby Vytvářejte funkce v příkazovém řádku Hadoop. Tyto funkce není nutné vymaterializovat v databázových tabulkách. Poznamenejte si pouze nezbytný dotaz pro jejich vytvoření.
   
   a.  Přihlaste se k hlavnímu uzlu clusteru Hadoop.
   
   b.  Otevřete příkazový řádek Hadoop.
   
   c.  Do příkazového řádku Hadoop zadejte kořenový adresář podregistru podle příkazu `cd %hive_home%\bin` .
   
   d.  Spusťte dotazy na podregistr v hlavním uzlu clusteru Hadoop a prozkoumejte data a podle potřeby vytvořte funkce.
1. V případě potřeby a/nebo podle potřeby vyvzorkujte data tak, aby se vešla do Azure Machine Learning Studio.
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
1. Přečtěte si data přímo z části `Hive Queries` pomocí modulu [Import dat][import-data] . Vložte potřebný dotaz, který extrahuje pole, vytvoří funkce a vzorkuje data v případě potřeby přímo v dotazu [Import dat][import-data] .
1. Jednoduchý tok experimentu Azure Machine Learning počínaje nahranou datovou sadou.

## <a name="decision-tree-for-scenario-selection"></a><a name="decisiontree"></a>Rozhodovací strom pro výběr scénáře
---
Následující diagram shrnuje výše popsané scénáře a procesy pokročilé analýzy a možnosti vývoje, které vás provedou jednotlivými scénáři s vydanými položkami. Zpracování dat, průzkum, strojírenství funkcí a vzorkování může probíhat v jedné nebo více metodách nebo prostředích – ve zdrojovém, zprostředkujícím nebo cílovém prostředí – a podle potřeby může postupovat iterativním způsobem. Diagram slouží pouze jako ilustrace některých možných toků a neposkytuje vyčerpávající výčet.

![Ukázka scénářů pro postupy procesu DS][8]

### <a name="advanced-analytics-in-action-examples"></a>Pokročilé analýzy v příkladech akcí
Ucelené postupy Azure Machine Learning, které využívají proces a technologii pokročilých analýz s využitím veřejných datových sad, najdete v těchto tématech:

* [Vědecké zpracování týmových dat v akci: použití SQL Server](sql-walkthrough.md).
* [Vědecké zpracování týmových dat v akci: používání clusterů HDInsight Hadoop](hive-walkthrough.md).

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
[import-data]: /azure/machine-learning/studio-module-reference/import-data