---
title: Použití toku dat pro zpracování dat z modelů automatizovaného strojového učení (AutoML)
description: Naučte se používat Azure Data Factory toky dat ke zpracování dat z modelů automatizovaného strojového učení (AutoML).
services: data-factory
author: amberz
co-author: ATLArcht
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 1/31/2021
ms.author: amberz
ms.co-author: Donnana
ms.openlocfilehash: 494fc2c227b6fe855e96a780d43cc6702722fa94
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100520927"
---
# <a name="process-data-from-automated-machine-learningautoml-models-using-data-flow"></a>Zpracování dat z modelů automatizovaného strojového učení (AutoML) pomocí toku dat

Projekty strojového učení přijímají automatizované Machine Learning (AutoML), aby se automaticky provedly školením, vyladěním a získali nejlepší model, a to pomocí cílové metriky, kterou zadáte pro klasifikaci, regresi a prognózy časových řad 

Jedna z nich je nezpracovaných dat z datového skladu nebo transakční databáze by představovalo značnou datovou sadu, jako je: 10 GB, Velká datová sada vyžaduje pro výuku modelů delší dobu, takže před školením Azure Machine Learningch modelů se doporučuje optimalizovat zpracování dat. V tomto kurzu se dozvíte, jak pomocí ADF vytvořit oddíly pro datovou sadu k Parquet souborů pro Azure Machine Learning datovou sadu. 

V projektu automatizovaného strojového učení (AutoML) by se použily tyto tři scénáře zpracování dat:

* Rozdělení velkých dat do Parquet souborů před výukou modelů. 

     [Datový rámec PANDAS](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) se běžně používá ke zpracování dat před školicími modely. PANDAS datový rámec funguje dobře pro velikosti dat menší než 1 GB, ale pokud jsou data Velká než 1 GB, PANDAS datový rámec zpomaluje zpracování dat, a to i při nedostatku paměti. Formáty [souborů Parquet](https://parquet.apache.org/) se doporučují pro strojové učení, protože je to binární sloupcový formát.
    
    Toky dat mapování datových továren v Azure jsou vizuálně navržené transformace dat s využitím datových techniků bez kódu. Zpracování velkých objemů dat je výkonné, protože kanál používá Clustery Spark se škálováním na více systémů.

* Rozdělte datovou sadu a datovou sadu testů.
    
    Datová sada pro školení se použije pro model školení. testovací sada se použije pro vyhodnocení modelů v projektu Machine Learning. Aktivita mapování toků dat pro podmíněné rozdělení by mohla rozdělit školicí data a testovat data. 

* Odeberte nekvalifikovaná data.

    Možná budete chtít odebrat nekvalifikovaná data, jako je například soubor Parquet s nulovým řádkem. V tomto kurzu použijeme agregovanou aktivitu k získání počtu řádků, počet řádků bude podmínkou pro odebrání neúplných dat. 


## <a name="preparation"></a>Příprava
Použijte následující tabulku Azure SQL Database. 
```
CREATE TABLE [dbo].[MyProducts](
    [ID] [int] NULL,
    [Col1] [char](124) NULL,
    [Col2] [char](124) NULL,
    [Col3] datetime NULL,
    [Col4] int NULL

) 

```

## <a name="convert-data-format-to-parquet"></a>Převést formát dat na Parquet

Tok dat převede tabulku Azure SQL Database na formát souboru Parquet. 

**Zdrojová datová sada**: tabulka transakcí Azure SQL Database

**Datová sada jímky**: úložiště objektů BLOB ve formátu Parquet


## <a name="remove-unqualified-data-based-on-row-count"></a>Odebrat nekvalifikovaná data na základě počtu řádků

Řekněme, že se má odebrat počet řádků menší než 2. 

1. Pomocí agregované aktivity můžete získat počet řádků: **seskupení podle** col2 a **agregace** s počtem (1) pro počet řádků. 

    ![Konfigurace agregované aktivity pro získání počtu řádků](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Použijte aktivitu jímky, zvolte **typ jímky** jako mezipaměť na kartě **jímka** a pak zvolte požadovaný sloupec ze seznamu **klíčových sloupců** v rozevíracím seznamu na kartě **Nastavení** . 

    ![Konfigurace aktivity CacheSink k získání počtu řádků v jímky uložených v mezipaměti](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Pomocí odvozené aktivity sloupce přidejte do zdrojového streamu sloupec počet řádků. Na kartě **Nastavení odvozeného sloupce** použijte vyhledávací výraz CacheSink # získání počtu řádků z SinkCache.
    ![Konfigurace aktivity odvozeného sloupce pro přidání počtu řádků ve zdroji 1](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. K odebrání neúplných dat použijte aktivitu podmíněného rozdělení. V tomto příkladu je počet řádků založený na sloupci col2 a podmínka má za následek odebrání počtu řádků menšího než 2, takže se odeberou dva řádky (ID = 2 a ID = 7). Měli byste uložit nekvalifikovaná data do úložiště objektů BLOB pro správu dat. 

    ![Konfigurace aktivity podmíněného rozdělení pro získání dat, která jsou větší nebo rovna 2](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    *    Vytvořte nový zdroj pro získání počtu řádků, který se použije v původním zdroji v pozdějších krocích. 
>    *    Využijte CacheSink z hlediska výkonu. 

## <a name="split-training-data-and-test-data"></a>Rozdělit data školení a testovat data 

1. Chceme rozdělit školicí data a testovací data pro každý oddíl. V tomto příkladu získáte pro stejnou hodnotu col2 první 2 řádky jako testovací data a řádky REST jako školicí data. 

    Pomocí aktivity okna můžete pro každý oddíl přidat jedno číslo řádku sloupce. V nabídce **přes** kartu Vybrat sloupec pro oddíl (v tomto kurzu bude oddíl pro col2), přiřadit objednávku na kartě **řazení** (v tomto kurzu bude vycházet z ID a objednat) a na kartě **sloupce oken** můžete pro každý oddíl přidat jeden sloupec jako číslo řádku. 
    ![Konfigurace aktivity okna pro přidání jednoho nového sloupce, který je číslo řádku](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Pomocí aktivity podmíněného rozdělení můžete rozdělit jednotlivé 2 řádky oddílu na testovací datovou sadu a zbývající řádky na školicí datovou sadu. Na kartě **nastavení podmíněného rozdělení** použijte jako podmínku výraz LesserOrEqual (RowNum, 2). 

    ![Konfigurace aktivity podmíněného rozdělení pro rozdělení aktuální datové sady na školicí datovou sadu a testovací datovou sadu](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-training-dataset-and-test-dataset-with-parquet-format"></a>Sada dat školení a testovací datová sada pro oddíly pomocí formátu Parquet

1. Použijte aktivitu jímky, na kartě **optimalizace** pomocí **jedinečné hodnoty na oddíl** nastavte sloupec jako klíč sloupce pro oddíl. 
    ![Konfigurace aktivity jímky pro nastavení oddílu datové sady školení](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

    Pojďme se podívat zpátky na celou logiku kanálu.
    ![Logika celého kanálu](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)


## <a name="next-steps"></a>Další kroky

* Sestavte zbývající část logiky toku dat pomocí [transformací](concepts-data-flow-overview.md)mapování toků dat.
