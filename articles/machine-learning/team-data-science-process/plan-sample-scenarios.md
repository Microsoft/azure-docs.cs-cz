---
title: Určit pokročilou analýzu scénáře pro Azure Machine Learning | Microsoft Docs
description: Vyberte odpovídající scénáře pro to advanced prediktivní analýzy v procesu Team Data vědecké účely.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 53aecc1e-5089-42cf-8d44-77678653f92d
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: 7b1b8f00e109776a3a5d3cc8c2224e8160b85704
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="scenarios-for-advanced-analytics-in-azure-machine-learning"></a>Scénáře pro pokročilé analýzy ve službě Azure Machine Learning
Tento článek popisuje řadu ukázkových zdrojů dat a cílové scénáře, které mohou být zpracována [tým datové vědy procesu (TDSP)](overview.md). TDSP poskytuje systematicky pro týmy spolupracovat na vytváření inteligentní aplikace. Scénáře, které jsou uvedeny v tomto tématu ilustrují možnosti dostupné v pracovním postupu zpracování dat, které jsou závislé na vlastností dat, umístění zdrojového a cílového úložiště v Azure.

**Rozhodovací strom** pro výběr ukázkových scénářů, které jsou vhodné pro vaše data a cíl se zobrazí v poslední části.

> [!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]
> 
> 

Každý z těchto částí uvede vzorový scénář. Pro každý scénář, vědecké zpracování možných dat nebo pokročilou analýzu jsou uvedeny toku a podpůrné prostředků Azure.

> [!NOTE]
> **Pro všechny z následujících scénářů budete muset:**
> <br/>
> 
> * [vytvořit účet úložiště](../../storage/common/storage-create-storage-account.md)
>   <br/>
> * [Vytvořit pracovní prostor služby Azure Machine Learning](../studio/create-workspace.md)
> 
> 

## <a name="smalllocal"></a>Scénář \#1: malé a střední tabulkové datovou sadu v místních souborů
![Malé a střední místních souborů][1]

#### <a name="additional-azure-resources-none"></a>Další prostředky Azure: žádné
1. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
2. Nahrání datové sady.
3. Vytvoření experimentu tok Azure Machine Learning, počínaje nahrané datových sad.

## <a name="smalllocalprocess"></a>Scénář \#2: malé a střední datové sady místních souborů, které vyžadují zpracování
![Malé a střední místních souborů s zpracování][2]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Další prostředky Azure: virtuální počítač Azure (server IPython Poznámkový blok)
1. Vytvořte virtuální počítač Azure s IPython Poznámkový blok.
2. Nahrání dat do kontejner úložiště Azure.
3. Předběžně zpracovat a vyčistit data v poznámkovém bloku IPython, přístup k datům z kontejneru úložiště Azure.
4. Transformace dat do domény, vyčistit formě tabulky.
5. Uložte Transformovaná data do objektů BLOB Azure.
6. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
7. Čtení dat z Azure BLOB pomocí [importovat Data] [ import-data] modulu.
8. Vytvoření experimentu tok Azure Machine Learning, počínaje ingestovaný datových sad.

## <a name="largelocal"></a>Scénář \#3: velké datové sady místních souborů, cílení objektů BLOB služby Azure
![Velké místních souborů][3]

#### <a name="additional-azure-resources-azure-virtual-machine-ipython-notebook-server"></a>Další prostředky Azure: virtuální počítač Azure (server IPython Poznámkový blok)
1. Vytvořte virtuální počítač Azure s IPython Poznámkový blok.
2. Nahrání dat do kontejner úložiště Azure.
3. Předběžně zpracovat a vyčistit data v poznámkovém bloku IPython, přístup k datům z Azure BLOB.
4. Transformace dat do domény, vyčistit formě tabulky, v případě potřeby.
5. Prozkoumejte data a podle potřeby vytvářet funkce.
6. Extrahujte vzorku dat. malé a střední.
7. Uložte jen Vzorkovaná data do objektů BLOB Azure.
8. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
9. Čtení dat z Azure BLOB pomocí [importovat Data] [ import-data] modulu.
10. Sestavení Azure Machine Learning experimentu toku počínaje ingestovaný datových sad.

## <a name="smalllocaltodb"></a>Scénář \#4: malé a střední datové sady místních souborů, cílení na SQL Server virtuálním počítači Azure
![Malé a střední místních souborů k databázi SQL v Azure][4]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: virtuální počítač Azure (SQL Server nebo server IPython Poznámkový blok)
1. Vytvořte virtuální počítač Azure systémem SQL Server + IPython Poznámkový blok.
2. Nahrání dat do kontejner úložiště Azure.
3. Předběžně zpracovat a vyčistit data v kontejneru úložiště Azure pomocí IPython Poznámkový blok.
4. Transformace dat do domény, vyčistit formě tabulky, v případě potřeby.
5. Uložení dat do souborů virtuálního počítače – místní (IPython poznámkového bloku běží na virtuálním počítači, místní jednotky odkazovat na disky virtuálních počítačů).
6. Načíst data do databáze systému SQL Server spuštěna na virtuálním počítači Azure.
   
   Možnost \#1: pomocí nástroje SQL Server Management Studio.
   
   * Přihlášení k systému SQL Server virtuálního počítače
   * Spusťte SQL Server Management Studio.
   * Vytváření tabulek databáze a cíle.
   * Používají jeden z hromadného importu metody k načítání dat z virtuálního počítače – místní soubory.
   
   Možnost \#2: pomocí IPython poznámkového bloku – není vhodné pro střední a větší datové sady
   
   <!-- -->    
   * Použijte připojovací řetězec ODBC pro přístup k systému SQL Server na virtuálním počítači.
   * Vytváření tabulek databáze a cíle.
   * Používají jeden z hromadného importu metody k načítání dat z virtuálního počítače – místní soubory.
7. Prozkoumejte data, podle potřeby vytvářet funkce. Všimněte si, že funkce nemusí být materializována v tabulkách databáze. Mějte na paměti pouze nezbytné dotaz k jejich vytvoření.
8. Vyberte velikost vzorku dat, pokud potřebné a/nebo potřeby.
9. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
10. Číst data přímo ze systému SQL Server pomocí [importovat Data] [ import-data] modulu. Vložte nezbytné dotaz, který extrahuje pole, vytvoří funkce a ukázky data v případě potřeby přímo v [importovat Data] [ import-data] dotazu.
11. Sestavení Azure Machine Learning experimentu toku počínaje ingestovaný datových sad.

## <a name="largelocaltodb"></a>Scénář \#5: velké datové sady v místních souborů cíle systému SQL Server ve virtuálním počítači Azure
![Velké místních souborů k databázi SQL v Azure][5]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: virtuální počítač Azure (SQL Server nebo server IPython Poznámkový blok)
1. Vytvořte virtuální počítač Azure systémem SQL Server a server IPython Poznámkový blok.
2. Nahrání dat do kontejner úložiště Azure.
3. (Volitelné) Předběžně zpracovat a vyčistit data.
   
   a.  Předběžně zpracovat a vyčistit data v poznámkovém bloku IPython, přístup k datům z Azure
   
       blobs.
   
   b.  Transformace dat do domény, vyčistit formě tabulky, v případě potřeby.
   
   c.  Uložení dat do souborů virtuálního počítače – místní (IPython poznámkového bloku běží na virtuálním počítači, místní jednotky odkazovat na disky virtuálních počítačů).
4. Načíst data do databáze systému SQL Server spuštěna na virtuálním počítači Azure.
   
   a.  Přihlášení k systému SQL Server virtuálního počítače.
   
   b.  Pokud data nebyla uložena již, stahování datových souborů z Azure
   
       storage container to local-VM folder.
   
   c.  Spusťte SQL Server Management Studio.
   
   d.  Vytváření tabulek databáze a cíle.
   
   e.  Některý z hromadného importu metody chcete načíst data.
   
   f.  Pokud je potřeba spoje tabulky platná, vytvořte indexy urychlit spojení.
   
   > [!NOTE]
   > Rychlejší načítání velikostí velkých objemů dat, se doporučuje, můžete vytvořit dělené tabulky a hromadně importovat data paralelně. Další informace najdete v tématu [paralelní Import dat do tabulek SQL rozdělena na oddíly](parallel-load-sql-partitioned-tables.md).
   > 
   > 
5. Prozkoumejte data, podle potřeby vytvářet funkce. Všimněte si, že funkce nemusí být materializována v tabulkách databáze. Mějte na paměti pouze nezbytné dotaz k jejich vytvoření.
6. Vyberte velikost vzorku dat, pokud potřebné a/nebo potřeby.
7. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
8. Číst data přímo ze systému SQL Server pomocí [importovat Data] [ import-data] modulu. Vložte nezbytné dotaz, který extrahuje pole, vytvoří funkce a ukázky data v případě potřeby přímo v [importovat Data] [ import-data] dotazu.
9. Jednoduchý experiment toku Azure Machine Learning počínaje nahrané datové sady

## <a name="largedbtodb"></a>Scénář \#6: velké datové sady v SQL Server databáze místní, cílení na SQL Server virtuálním počítači Azure
![Velké SQL DB místní k databázi SQL v Azure][6]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: virtuální počítač Azure (SQL Server nebo server IPython Poznámkový blok)
1. Vytvořte virtuální počítač Azure systémem SQL Server a server IPython Poznámkový blok.
2. Použijte jeden z data exportovat metody pro export dat z SQL serveru do souborů výpisu paměti.
   
   > [!NOTE]
   > Pokud se rozhodnete přesouvání všech dat z databáze místní, alternativní metodu (rychlejší) přesunout databázi úplné instanci systému SQL Server v Azure. Přeskočte kroky pro export dat, vytvoření databáze a zatížení nebo importovat data na cílovou databázi a postupujte podle alternativní metodu.
   > 
   > 
3. Nahrání souborů výpisu paměti do kontejneru úložiště Azure.
4. Načtěte data do databáze systému SQL Server spuštěna na virtuální počítač Azure.
   
   a.  Přihlášení k systému SQL Server virtuálního počítače.
   
   b.  Stahování datových souborů z kontejner úložiště Azure do složky, místní počítač.
   
   c.  Spusťte SQL Server Management Studio.
   
   d.  Vytváření tabulek databáze a cíle.
   
   e.  Některý z hromadného importu metody chcete načíst data.
   
   f.  Pokud je potřeba spoje tabulky platná, vytvořte indexy urychlit spojení.
   
   > [!NOTE]
   > Pro rychlejší načítání velikostí velkých objemů dat, vytváření oddílů tabulek a k hromadnému importu dat paralelně. Další informace najdete v tématu [paralelní Import dat do tabulek SQL rozdělena na oddíly](parallel-load-sql-partitioned-tables.md).
   > 
   > 
5. Prozkoumejte data, podle potřeby vytvářet funkce. Všimněte si, že funkce nemusí být materializována v tabulkách databáze. Mějte na paměti pouze nezbytné dotaz k jejich vytvoření.
6. Vyberte velikost vzorku dat, pokud potřebné a/nebo potřeby.
7. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
8. Číst data přímo ze systému SQL Server pomocí [importovat Data] [ import-data] modulu. Vložte nezbytné dotaz, který extrahuje pole, vytvoří funkce a ukázky data v případě potřeby přímo v [importovat Data] [ import-data] dotazu.
9. Jednoduché Azure Machine Learning experimentu toku počínaje nahrané datovou sadu.

### <a name="alternate-method-to-copy-a-full-database-from-an-on-premises--sql-server-to-azure-sql-database"></a>Alternativní metoda pro kopírování úplné databáze z místního serveru SQL do Azure SQL Database
![Místní databáze odpojit a připojit k databázi SQL v Azure][7]

#### <a name="additional-azure-resources-azure-virtual-machine-sql-server--ipython-notebook-server"></a>Další prostředky Azure: virtuální počítač Azure (SQL Server nebo server IPython Poznámkový blok)
Replikovat celou databázi SQL serveru ve vaší virtuální počítač SQL Server, měli byste zkopírovat databázi z jednoho umístění nebo serveru na jiný, za předpokladu, že databáze můžete provedeny dočasně offline. Můžete to udělat v Průzkumník objektů systému SQL Server Management Studio nebo pomocí ekvivalentní příkazy jazyka Transact-SQL.

1. Odpojení databáze v umístění zdroje. Další informace najdete v tématu [odpojení databáze](https://technet.microsoft.com/library/ms191491\(v=sql.110\).aspx).
2. V okně Průzkumníka Windows nebo příkazového řádku systému Windows zkopírujte soubor odpojit databáze nebo soubory a soubor protokolu nebo soubory do cílového umístění na virtuální počítač SQL Server v Azure.
3. Připojte zkopírované soubory v cílové instanci systému SQL Server. Další informace najdete v tématu [připojit databázi](https://technet.microsoft.com/library/ms190209\(v=sql.110\).aspx).

[Přesuňte databázi pomocí odpojit a připojit (Transact-SQL)](https://technet.microsoft.com/library/ms187858\(v=sql.110\).aspx)

## <a name="largedbtohive"></a>Scénář \#7: velkých objemů dat v místních souborech cílové databáze Hive v Azure HDInsight Hadoop clusterů
![Velké objemy dat v místní cíl Hive][9]

#### <a name="additional-azure-resources-azure-hdinsight-hadoop-cluster-and-azure-virtual-machine-ipython-notebook-server"></a>Další prostředky Azure: clusteru Azure HDInsight Hadoop a virtuální počítač Azure (server IPython Poznámkový blok)
1. Vytvořte virtuální počítač Azure serverem IPython Poznámkový blok.
2. Vytvoření clusteru Azure HDInsight Hadoop.
3. (Volitelné) Předběžně zpracovat a vyčistit data.
   
   a.  Předběžně zpracovat a vyčistit data v poznámkovém bloku IPython, přístup k datům z Azure
   
       blobs.
   
   b.  Transformace dat do domény, vyčistit formě tabulky, v případě potřeby.
   
   c.  Uložení dat do souborů virtuálního počítače – místní (IPython poznámkového bloku běží na virtuálním počítači, místní jednotky odkazovat na disky virtuálních počítačů).
4. Nahrání dat do výchozí kontejner clusteru Hadoop vybrali v kroku 2.
5. Načíst data do databáze Hive v clusteru Azure HDInsight Hadoop.
   
   a.  Přihlaste se k hlavnímu uzlu clusteru Hadoop
   
   b.  Otevřete příkazový řádek Hadoop.
   
   c.  Zadejte kořenový adresář Hive pomocí příkazu `cd %hive_home%\bin` v Hadoop příkazového řádku.
   
   d.  Spouštění dotazů Hive k vytvoření databáze a tabulky a načtení dat z úložiště objektů blob do tabulek Hive.
   
   > [!NOTE]
   > Pokud je velkých objemů dat, uživatelé mohou vytvářet tabulku Hive s oddíly. Uživatelé pak mohou používat `for` smyčky v Hadoop příkazový řádek z hlavního uzlu pro načtení dat do tabulky Hive rozdělena na oddíly oddílem.
   > 
   > 
6. Prozkoumejte data a vytvářet funkcí podle potřeby v Hadoop příkazového řádku. Všimněte si, že funkce nemusí být materializována v tabulkách databáze. Mějte na paměti pouze nezbytné dotaz k jejich vytvoření.
   
   a.  Přihlaste se k hlavnímu uzlu clusteru Hadoop
   
   b.  Otevřete příkazový řádek Hadoop.
   
   c.  Zadejte kořenový adresář Hive pomocí příkazu `cd %hive_home%\bin` v Hadoop příkazového řádku.
   
   d.  Spouštění dotazů Hive v Hadoop příkazový řádek z hlavního uzlu clusteru Hadoop data prozkoumat a vytvořit funkcí podle potřeby.
7. Pokud potřebné a/nebo potřeby, ukázková data, aby se vešla do Azure Machine Learning Studio.
8. Přihlaste se k [Azure Machine Learning Studio](https://studio.azureml.net/).
9. Číst data přímo z `Hive Queries` pomocí [importovat Data] [ import-data] modulu. Vložte nezbytné dotaz, který extrahuje pole, vytvoří funkce a ukázky data v případě potřeby přímo v [importovat Data] [ import-data] dotazu.
10. Jednoduché Azure Machine Learning experimentu toku počínaje nahrané datovou sadu.

## <a name="decisiontree"></a>Rozhodovací strom pro výběr scénář
- - -
Následující diagram shrnuje výše popsané scénáře a pokročilé analýzy proces a výběr technologie, která vás zavedou na každý z rozepsané scénářů. Všimněte si, že může trvat zpracování dat, průzkum, funkce inženýrství a vzorkování umístit do jednoho nebo více metoda prostředí – ve zdroji zprostředkující, nebo cílové prostředí – a může pokračovat interaktivně, podle potřeby. Diagram pouze slouží jako ilustraci některé možné toků a neposkytuje vyčerpávající výčet.

![Vzorové DS proces návod scénáře][8]

### <a name="advanced-analytics-in-action-examples"></a>Pokročilé analýzy v akci příklady
Návody začátku do konce Azure Machine Learning, která využívají pokročilé analýzy proces a technologie pomocí veřejné datové sady najdete v části:

* [Tým proces vědecké účely dat v akci: pomocí SQL serveru](sql-walkthrough.md).
* [Tým proces vědecké účely dat v akci: pomocí clusterů systému HDInsight Hadoop](hive-walkthrough.md).

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
