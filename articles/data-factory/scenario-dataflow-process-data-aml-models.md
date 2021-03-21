---
title: Používání toků dat ke zpracování dat z modelů automatizovaného strojového učení (AutoML)
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
ms.openlocfilehash: 45cd44cc0678b7f3a006a88bf66be2bca091af76
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104595375"
---
# <a name="process-data-from-automated-machine-learning-models-by-using-data-flows"></a>Zpracování dat z automatizovaných modelů strojového učení pomocí toků dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Automatizované Machine Learning (AutoML) přijímá projekty strojového učení, aby je bylo možné automaticky ladit, vyladit a získávat z nich nejlepší modely pomocí cílových metrik, které zadáte pro klasifikaci, regresi a prognózy časových řad.

Jednou z výzev pro AutoML je to, že nezpracovaná data z datového skladu nebo transakční databáze by byla velkou datovou sadou, možná 10 GB. Velká datová sada vyžaduje pro výuku modelů delší dobu, proto doporučujeme před vytvořením Azure Machine Learningch modelů optimalizovat zpracování dat. V tomto kurzu se dozvíte, jak pomocí Azure Data Factory rozdělit datovou sadu do souborů AutoML pro Machine Learning datovou sadu.

Projekt AutoML zahrnuje následující tři scénáře zpracování dat:

* Rozdělte velké objemy dat tak, aby AutoML soubory před výukou modelů.

     [Datový rámec PANDAS](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) se běžně používá ke zpracování dat před výukou modelů. Datový rámec PANDAS funguje dobře pro velikost dat menší než 1 GB, ale pokud jsou data větší než 1 GB, datový rámec PANDAS zpomaluje zpracování dat. Někdy se může stát, že se zobrazí chybová zpráva nedostatek paměti. Pro strojové učení doporučujeme použít formát [souboru Parquet](https://parquet.apache.org/) , protože se jedná o binární sloupcový formát.
    
     Data Factory mapování datových toků je vizuálně navrženo transformace dat, které uvolňují technici dat při psaní kódu. Mapování toků dat představuje účinný způsob zpracování velkých objemů dat, protože kanál využívá Clustery Spark se škálováním na více systémů.

* Rozdělte datovou sadu a testovací datovou sadu.
    
    Datová sada školení bude použita pro školicí model. Testovací datová sada se použije k vyhodnocení modelů v projektu strojového učení. Aktivita podmíněného rozdělení pro toky mapování dat by mohla rozdělit školicí data a testovat data.

* Odeberte nekvalifikovaná data.

    Možná budete chtít odebrat nekvalifikovaná data, jako je například soubor Parquet s nulovými řádky. V tomto kurzu pomocí agregované aktivity získáme počet řádků. Počet řádků bude podmínkou pro odebrání nekvalifikovaných dat.

## <a name="preparation"></a>Příprava

Použijte následující Azure SQL Database tabulku.

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

Následující tok dat převede SQL Database tabulku na formát souboru Parquet:

- **Zdrojová datová sada**: tabulka transakcí SQL Database.
- **Datová sada jímky**: úložiště objektů BLOB ve formátu Parquet

## <a name="remove-unqualified-data-based-on-row-count"></a>Odebrat nekvalifikovaná data na základě počtu řádků

Řekněme, že musíme odebrat počet řádků, který je menší než dva.

1. Pomocí agregované aktivity získáte počet řádků. Použijte **seskupení podle** col2 a **agregace** s `count(1)` počtem řádků.

    ![Snímek obrazovky, který zobrazuje konfiguraci agregované aktivity, aby získal počet řádků](./media/scenario-dataflow-process-data-aml-models/aggregate-activity-addrowcount.png)

1. Pomocí aktivity jímky vyberte na kartě **jímka** **typ jímky** jako **mezipaměť** . Pak v rozevíracím seznamu **sloupce klíče** na kartě **Nastavení** vyberte požadovaný sloupec.

    ![Snímek obrazovky, který ukazuje konfiguraci aktivity CacheSink, aby získala počet řádků v jímky uložené v mezipaměti.](./media/scenario-dataflow-process-data-aml-models/cachesink-activity-addrowcount.png)

1. Pomocí aktivity odvozeného sloupce přidejte do zdrojového streamu sloupec počet řádků. Na kartě **Nastavení odvozeného sloupce** použijte `CacheSink#lookup` výraz pro získání počtu řádků z CacheSink.

    ![Snímek obrazovky, který ukazuje konfiguraci aktivity odvozeného sloupce pro přidání počtu řádků v source1.](./media/scenario-dataflow-process-data-aml-models/derived-column-activity-rowcount-source-1.png)

1. K odebrání neúplných dat použijte aktivitu podmíněného rozdělení. V tomto příkladu je počet řádků založen na sloupci col2. Podmínkou je odebrání počtu řádků menšího než 2, takže budou odebrány dva řádky (ID = 2 a ID = 7). Nekvalifikované údaje byste uložili do úložiště objektů BLOB pro správu dat.

    ![Snímek obrazovky, který ukazuje konfiguraci aktivity podmíněného rozdělení pro získání dat, která jsou větší nebo rovná hodnotě 2.](./media/scenario-dataflow-process-data-aml-models/conditionalsplit-greater-or-equal-than-2.png)

> [!NOTE]
>    * Vytvořte nový zdroj pro získání počtu řádků, které se použijí v původním zdroji v pozdějších krocích.
>    * Využijte CacheSink z hlediska výkonu.

## <a name="split-training-data-and-test-data"></a>Rozdělit data školení a testovat data

Chceme rozdělit školicí data a testovací data pro každý oddíl. V tomto příkladu můžete pro stejnou hodnotu col2 získat horní dva řádky jako testovací data a zbytek řádků jako školicí data.

1. Pomocí aktivity okna můžete pro každý oddíl přidat jedno číslo řádku sloupce. Na kartě **over** vyberte sloupec pro oddíl. V tomto kurzu budeme oddíl pro col2. Poskytněte objednávku na kartě **řazení** , která v tomto kurzu bude založena na ID. Poskytněte objednávku na kartě **sloupce okna** a přidejte jeden sloupec jako číslo řádku pro každý oddíl.

    ![Snímek obrazovky zobrazující konfiguraci aktivity okna pro přidání jednoho nového sloupce s číslem řádku](./media/scenario-dataflow-process-data-aml-models/window-activity-add-row-number.png)

1. Pomocí aktivity podmíněného rozdělení můžete rozdělit horní dva řádky každého oddílu na testovací datovou sadu a zbytek řádků do datové sady školení. Na kartě **nastavení podmíněného rozdělení** použijte výraz `lesserOrEqual(RowNum,2)` jako podmínku.

    ![Snímek obrazovky, který ukazuje konfiguraci aktivity podmíněného rozdělení pro rozdělení aktuální datové sady na školicí datovou sadu a testovací datovou sadu.](./media/scenario-dataflow-process-data-aml-models/split-training-dataset-test-dataset.png)

## <a name="partition-the-training-and-test-datasets-with-parquet-format"></a>Rozdělení datových sad pro školení a testování pomocí formátu Parquet

Pomocí aktivity jímky na kartě **optimalizace** použijte **jedinečnou hodnotu na oddíl** a nastavte sloupec jako klíč sloupce pro oddíl.

![Snímek obrazovky, který ukazuje konfiguraci aktivity jímky pro nastavení oddílu datové sady školení.](./media/scenario-dataflow-process-data-aml-models/partition-training-dataset-sink.png)

Pojďme se podívat zpátky na celou logiku kanálu.

![Snímek obrazovky zobrazující logiku celého kanálu](./media/scenario-dataflow-process-data-aml-models/entire-pipeline.png)

## <a name="next-steps"></a>Další kroky

Sestavte zbytek logiky toku dat pomocí mapování [transformací](concepts-data-flow-overview.md)toku dat.
