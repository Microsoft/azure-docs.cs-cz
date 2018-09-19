---
title: Stream dat ze služby Stream Analytics do Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Pomocí Azure Stream Analytics streamování dat do Azure Data Lake Storage Gen1
services: data-lake-store,stream-analytics
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: nitinme
ms.openlocfilehash: 0d9ddbeae3a666d3b3cf56f80ae633a7ecaa650a
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294029"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Stream data z Azure Storage Blob do služby Azure Data Lake Storage Gen1 pomocí Azure Stream Analytics
V tomto článku se dozvíte, jak pomocí Azure Data Lake Storage Gen1 jako výstup pro úlohu Azure Stream Analytics. Tento článek ukazuje jednoduchý scénář, který čte data z Azure Storage blob (vstup) a zapisuje data do Data Lake Storage Gen1 (výstup).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet služby Azure Storage**. Kontejner objektů blob z tohoto účtu budete používat pro vstupní data pro úlohu Stream Analytics. Pro účely tohoto kurzu předpokládá, že jste účet úložiště s názvem **storageforasa** kontejner v rámci účtu s názvem **storageforasacontainer**. Po vytvoření kontejneru do něj nahrajte ukázkový datový soubor. 
  
* **Účet Data Lake Storage Gen1**. Postupujte podle pokynů na adrese [Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure Portal](data-lake-store-get-started-portal.md). Předpokládejme, že máte účet Data Lake Storage Gen1 volá **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics
Začněte vytvořením úlohy Stream Analytics, která obsahuje vstupní zdroj a cíl výstupu. Pro účely tohoto kurzu zdroj je kontejner objektů blob Azure a cíl je Data Lake Storage Gen1.

1. Přihlaste se k [webu Azure Portal](https://portal.azure.com).

2. V levém podokně klikněte na tlačítko **úlohy Stream Analytics**a potom klikněte na tlačítko **přidat**.

    ![Vytvoření úlohy Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "vytvoření úlohy Stream Analytics")

    > [!NOTE]
    > Ujistěte se, že vytvoření úlohy ve stejné oblasti jako účet úložiště, nebo vám budou účtovat další poplatky pro přesun dat mezi oblastmi.
    >

## <a name="create-a-blob-input-for-the-job"></a>Vytvoření objektu Blob vstup pro úlohu

1. Otevřete stránku pro úlohu Stream Analytics, v levém podokně klikněte na **vstupy** kartu a potom klikněte na tlačítko **přidat**.

    ![Přidat vstup pro úlohu](./media/data-lake-store-stream-analytics/create.input.1.png "přidat vstup pro úlohu")

2. Na **nový vstup** okně zadejte následující hodnoty.

    ![Přidat vstup pro úlohu](./media/data-lake-store-stream-analytics/create.input.2.png "přidat vstup pro úlohu")

    * Pro **vstupní alias**, zadejte jedinečný název pro vstup úlohy.
    * Pro **typ zdroje**vyberte **datový proud**.
    * Pro **zdroj**vyberte **úložiště objektů Blob**.
    * Pro **předplatné**vyberte **používání úložiště blob z aktuálního předplatného**.
    * Pro **účtu úložiště**, vyberte účet úložiště, který jste vytvořili jako součást požadavků. 
    * Pro **kontejneru**, vyberte kontejner, který jste vytvořili v účtu úložiště.
    * Pro **formát serializace události**vyberte **sdíleného svazku clusteru**.
    * Pro **oddělovač**vyberte **kartu**.
    * Pro **kódování**vyberte **UTF-8**.

    Klikněte na možnost **Vytvořit**. Na portálu teď přidá vstupu a otestuje připojení k němu.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Vytvoření Data Lake Storage Gen1 výstupu úlohy

1. Otevřete klikněte na stránce pro úlohu Stream Analytics **výstupy** klikněte na tlačítko **přidat**a vyberte **Data Lake Storage Gen1**.

    ![Přidat výstup pro vaši úlohu](./media/data-lake-store-stream-analytics/create.output.1.png "přidat výstup pro vaši úlohu")

2. Na **nový výstup** okně zadejte následující hodnoty.

    ![Přidat výstup pro vaši úlohu](./media/data-lake-store-stream-analytics/create.output.2.png "přidat výstup pro vaši úlohu")

    * Pro **alias pro výstup**, zadejte jedinečný název pro výstup úlohy. Toto je popisný název používaný v dotazech pro výstup dotazu k tomuto účtu Data Lake Storage Gen1 přesměrování.
    * Zobrazí se výzva k autorizaci přístupu k účtu Data Lake Storage Gen1. Klikněte na tlačítko **Autorizovat**.

3. Na **nový výstup** okně zadejte následující hodnoty i nadále.

    ![Přidat výstup pro vaši úlohu](./media/data-lake-store-stream-analytics/create.output.3.png "přidat výstup pro vaši úlohu")

    * Pro **název účtu**, vyberte účet Data Lake Storage Gen1 jste už vytvořili, kde chcete úlohu k odeslání do výstupu.
    * Pro **vzor předpony cesty**, zadejte cestu k souboru použitý k zápisu souborů v rámci zadaného účtu Data Lake Storage Gen1.
    * Pro **formát data**, pokud jste použili token kalendářního data v předponovou cestu, můžete vybrat formát data, ve kterém jsou uspořádány souborů.
    * Pro **formát času**, pokud jste použili čas tokenu v předponovou cestu, zadat formát času, ve kterém jsou uspořádány souborů.
    * Pro **formát serializace události**vyberte **sdíleného svazku clusteru**.
    * Pro **oddělovač**vyberte **kartu**.
    * Pro **kódování**vyberte **UTF-8**.
    
    Klikněte na možnost **Vytvořit**. Na portálu teď přidá výstup a otestuje připojení k němu.
    
## <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

1. Ke spuštění úlohy Stream Analytics, musíte spustit dotaz z **dotazu** kartu. Pro účely tohoto kurzu, můžete spustit ukázkový dotaz pomocí zástupné texty nahraďte vstup úlohy tak za výstupní aliasy, jak je znázorněno v následujícím snímku obrazovky.

    ![Spuštění dotazu](./media/data-lake-store-stream-analytics/run.query.png "spuštění dotazu")

2. Klikněte na tlačítko **Uložit** z horní části obrazovky a pak **přehled** klikněte na tlačítko **Start**. V dialogovém okně vyberte **vlastní čas**a potom nastavte aktuální datum a čas.

    ![Nastavte čas úlohy](./media/data-lake-store-stream-analytics/run.query.2.png "nastavit čas úlohy")

    Klikněte na tlačítko **Start** spustíte úlohu. To může trvat až několik minut na spuštění úlohy.

3. K aktivaci úlohy pro výběr data z objektu blob, zkopírujte ukázkový datový soubor do kontejneru objektů blob. Můžete získat soubor ukázkových dat z [úložiště Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). Pro účely tohoto kurzu, můžeme zkopírovat soubor **vehicle1_09142014.csv**. Můžete použít různé klienty, například [Průzkumníka služby Azure Storage](http://storageexplorer.com/), jak nahrát data do kontejneru objektů blob.

4. Z **přehled** ve skupině **monitorování**, naleznete v tématu Jak se zpracovávají data.

    ![Monitorování úlohy](./media/data-lake-store-stream-analytics/run.query.3.png "úlohy monitorování")

5. Nakonec můžete ověřit, že je k dispozici v účtu Data Lake Storage Gen1 výstupních dat úlohy. 

    ![Ověření výstupu](./media/data-lake-store-stream-analytics/run.query.4.png "ověření výstupu")

    V podokně Průzkumník dat, Všimněte si, že výstup bude zapsán do cesty ke složce, jak je uvedeno v Data Lake Storage Gen1 nastavení výstupu (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Další informace najdete v tématech
* [Vytvoření clusteru HDInsight pro použití Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
