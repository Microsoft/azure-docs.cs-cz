---
title: Streamování dat z Stream Analytics do Data Lake Storage Gen1 – Azure
description: Naučte se používat Azure Data Lake Storage Gen1 jako výstup pro Azure Stream Analytics úlohu, s jednoduchým scénářem, který čte data z objektu blob Azure Storage.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: 4c289ecb1d8471a7b99f1d4c85a0163de4d0c593
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91576213"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Streamování dat z Azure Storage Blob do Azure Data Lake Storage Gen1 pomocí Azure Stream Analytics
V tomto článku se naučíte, jak používat Azure Data Lake Storage Gen1 jako výstup pro Azure Stream Analytics úlohu. Tento článek ukazuje jednoduchý scénář, který čte data z Azure Storage objektů BLOB (Input) a zapisuje data do Data Lake Storage Gen1 (výstup).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Storage**. Pomocí kontejneru objektů BLOB z tohoto účtu můžete zadat data pro úlohu Stream Analytics. Pro účely tohoto kurzu Předpokládejme, že máte účet úložiště s názvem **storageforasa** a kontejner v rámci účtu s názvem **storageforasacontainer**. Po vytvoření kontejneru nahrajte do něj Ukázkový datový soubor. 
  
* **Účet Data Lake Storage Gen1**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí Azure Portal](data-lake-store-get-started-portal.md). Předpokládejme, že máte účet Data Lake Storage Gen1 s názvem **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics
Začnete vytvořením úlohy Stream Analytics, která zahrnuje vstupní zdroj a výstupní cíl. Pro tento kurz se zdrojem je kontejner objektů BLOB v Azure a cíl je Data Lake Storage Gen1.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).

2. V levém podokně klikněte na **Stream Analytics úlohy** a pak klikněte na **Přidat**.

    ![Vytvoření úlohy Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "Vytvoření úlohy Stream Analytics")

    > [!NOTE]
    > Ujistěte se, že jste vytvořili úlohu ve stejné oblasti jako účet úložiště, nebo vám budou účtovány další náklady na přesun dat mezi oblastmi.
    >

## <a name="create-a-blob-input-for-the-job"></a>Vytvoření vstupu objektu BLOB pro úlohu

1. Otevřete stránku Stream Analytics úlohy, v levém podokně klikněte na kartu **vstupy** a pak klikněte na **Přidat**.

    ![Snímek obrazovky okna úlohy Stream Analytics s možností vstupy a možností přidat vstup streamu s názvem.](./media/data-lake-store-stream-analytics/create.input.1.png "Přidání vstupu do úlohy")

2. V okně **Nový vstup** zadejte následující hodnoty.

    ![Snímek obrazovky úložiště objektů BLOB – nové vstupní okno](./media/data-lake-store-stream-analytics/create.input.2.png "Přidání vstupu do úlohy")

   * Pro **vstupní alias** zadejte jedinečný název pro vstup úlohy.
   * Jako **typ zdroje** vyberte **datový proud**.
   * Jako **zdroj** vyberte **úložiště objektů BLOB**.
   * U **předplatného** vyberte **použít úložiště objektů BLOB z aktuálního předplatného**.
   * V poli **účet úložiště** vyberte účet úložiště, který jste vytvořili v rámci požadavků. 
   * V části **kontejner** vyberte kontejner, který jste vytvořili ve vybraném účtu úložiště.
   * Jako **formát serializace události** vyberte **CSV**.
   * V případě **oddělovače** vyberte **kartu**.
   * V případě **kódování** vyberte **UTF-8**.

     Klikněte na **Vytvořit**. Portál teď přidá vstup a otestuje připojení k němu.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Vytvořit výstup Data Lake Storage Gen1 pro úlohu

1. Otevřete stránku Stream Analytics úlohy, klikněte na kartu **výstupy** , klikněte na tlačítko **Přidat** a vyberte možnost **Data Lake Storage Gen1**.

    ![Snímek obrazovky okna úlohy Stream Analytics s možností výstupy, přidat možnost a Data Lake Storage Gen 1 s názvem.](./media/data-lake-store-stream-analytics/create.output.1.png "Přidat výstup do úlohy")

2. V okně **Nový výstup** zadejte následující hodnoty.

    ![Snímek obrazovky s oknem Data Lake Storage Gen 1 – nové okno výstup s možností autorizace s názvem.](./media/data-lake-store-stream-analytics/create.output.2.png "Přidat výstup do úlohy")

    * Pro **alias Output** zadejte jedinečný název výstupu úlohy. Toto je popisný název, který se používá v dotazech k přesměrování výstupu dotazu na tento Data Lake Storage Gen1 účet.
    * Zobrazí se výzva k autorizaci přístupu k účtu Data Lake Storage Gen1. Klikněte na **autorizovat**.

3. V okně **Nový výstup** Pokračujte zadáním následujících hodnot.

    ![Snímek obrazovky s oknem Data Lake Storage 1 – nové okno výstup.](./media/data-lake-store-stream-analytics/create.output.3.png "Přidat výstup do úlohy")

   * Jako **název účtu** vyberte účet Data Lake Storage Gen1, který jste už vytvořili, do kterého chcete odeslat výstup úlohy.
   * V poli **vzor předpony cesty** zadejte cestu k souboru, která se používá k zápisu souborů v rámci zadaného data Lake Storage Gen1 účtu.
   * Pokud pro **formát data** jste použili v cestě předpony datový token, můžete vybrat formát data, ve kterém jsou vaše soubory uspořádány.
   * Pokud jste v cestě předpony použili časový **token, určete formát času, ve** kterém jsou soubory uspořádány.
   * Jako **formát serializace události** vyberte **CSV**.
   * V případě **oddělovače** vyberte **kartu**.
   * V případě **kódování** vyberte **UTF-8**.
    
     Klikněte na **Vytvořit**. Portál teď přidá výstup a otestuje připojení k němu.
    
## <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

1. Chcete-li spustit úlohu Stream Analytics, je nutné spustit dotaz z karty **dotaz** . V tomto kurzu můžete ukázkový dotaz spustit nahrazením zástupných symbolů vstupními a výstupními aliasy úlohy, jak je znázorněno na snímku obrazovky níže.

    ![Spustit dotaz](./media/data-lake-store-stream-analytics/run.query.png "Spuštění dotazu")

2. V horní části obrazovky klikněte na **Uložit** a na kartě **Přehled** klikněte na **Spustit**. V dialogovém okně vyberte možnost **vlastní čas** a pak nastavte aktuální datum a čas.

    ![Nastavit čas úlohy](./media/data-lake-store-stream-analytics/run.query.2.png "Nastavit čas úlohy")

    Kliknutím na **Spustit** spusťte úlohu. Spuštění úlohy může trvat až několik minut.

3. Chcete-li aktivovat úlohu pro výběr dat z objektu blob, zkopírujte Ukázkový datový soubor do kontejneru objektů BLOB. Ukázkový datový soubor můžete získat z [Azure Data Lake úložiště Git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Pro tento kurz zkopírujte soubor **vehicle1_09142014.csv**. Pro nahrání dat do kontejneru objektů blob můžete použít různé klienty, například [Průzkumník služby Azure Storage](https://storageexplorer.com/).

4. Na kartě **Přehled** v části **monitorování** Zjistěte, jak byla data zpracována.

    ![Monitorování úlohy](./media/data-lake-store-stream-analytics/run.query.3.png "Monitorování úlohy")

5. Nakonec můžete ověřit, že jsou výstupní data úlohy v účtu Data Lake Storage Gen1 k dispozici. 

    ![Ověření výstupu](./media/data-lake-store-stream-analytics/run.query.4.png "Ověření výstupu")

    V podokně Průzkumník dat si všimněte, že výstup je zapsán do cesty ke složce, jak je uvedeno v nastavení Data Lake Storage Gen1 Output ( `streamanalytics/job/output/{date}/{time}` ).  

## <a name="see-also"></a>Viz také
* [Vytvoření clusteru HDInsight pro použití Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
