---
title: Streamování dat z Stream Analytics do Azure Data Lake Storage Gen1 | Dokumenty společnosti Microsoft
description: Streamování dat do Azure Data Lake Storage Storage Gen1 pomocí Azure Stream Analytics
services: data-lake-store,stream-analytics
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: edb58e0b-311f-44b0-a499-04d7e6c07a90
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: d3dbacd58b3bda3fbf8ee8ad5f175eccc2cb2a24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60194851"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Streamování dat z objektu blob úložiště Azure do Azure Data Lake Storage Gen1 pomocí Azure Stream Analytics
V tomto článku se dozvíte, jak používat Azure Data Lake Storage Gen1 jako výstup pro úlohu Azure Stream Analytics. Tento článek ukazuje jednoduchý scénář, který čte data z objektu blob (vstup) úložiště Azure a zapíše data do Data Lake Storage Gen1 (výstup).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet Azure Storage**. Kontejner objektů blob z tohoto účtu použijete k zadávání dat pro úlohu Stream Analytics. V tomto kurzu předpokládejme, že máte účet úložiště s názvem **storageforasa** a kontejner v rámci účtu s názvem **storageforasacontainer**. Po vytvoření kontejneru do něj nahrajte ukázkový datový soubor. 
  
* **Účet Gen1 úložiště datového jezera**. Postupujte podle pokynů na [webu Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure Portal](data-lake-store-get-started-portal.md). Předpokládejme, že máte účet Data Lake Storage Gen1 s názvem **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics
Můžete začít vytvořením úlohy Stream Analytics, která obsahuje vstupní zdroj a výstupní cíl. Pro účely tohoto kurzu je zdrojem kontejner objektů blob Azure a cílem je Data Lake Storage Gen1.

1. Přihlaste se na [portál Azure Portal](https://portal.azure.com).

2. V levém podokně klikněte na **Položky Streamování analýzy a**potom klikněte na **Přidat**.

    ![Vytvoření úlohy analýzy datového proudu](./media/data-lake-store-stream-analytics/create.job.png "Vytvoření úlohy Stream Analytics")

    > [!NOTE]
    > Ujistěte se, že vytvoříte úlohu ve stejné oblasti jako účet úložiště, nebo vám vzniknou další náklady na přesun dat mezi oblastmi.
    >

## <a name="create-a-blob-input-for-the-job"></a>Vytvoření vstupu objektu blob pro úlohu

1. Otevřete stránku úlohy Stream Analytics, v levém podokně klikněte na kartu **Vstupy** a potom klikněte na **Přidat**.

    ![Přidání příspěvku do úlohy](./media/data-lake-store-stream-analytics/create.input.1.png "Přidání příspěvku do úlohy")

2. Na nové **vstupní** okno, zadejte následující hodnoty.

    ![Přidání příspěvku do úlohy](./media/data-lake-store-stream-analytics/create.input.2.png "Přidání příspěvku do úlohy")

   * Do **pole Input alias**zadejte jedinečný název pro vstup úlohy.
   * V **popřípadě Typ zdroj**vyberte Datový **proud**.
   * V **pouzdře**vyberte **úložiště objektů Blob**.
   * V **případě předplatného**vyberte použít úložiště objektů **blob z aktuálního předplatného**.
   * Pro **účet úložiště**vyberte účet úložiště, který jste vytvořili jako součást předpokladů. 
   * V **části Kontejner**vyberte kontejner, který jste vytvořili ve vybraném účtu úložiště.
   * Ve **formátu serializace událostí**vyberte **položku CSV**.
   * V **případě oddělovače**vyberte **kartu**.
   * V **části Kódování**vyberte možnost **UTF-8**.

     Klikněte na **Vytvořit**. Portál nyní přidá vstup a otestuje připojení k němu.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Vytvoření výstupu Data Lake Storage Gen1 pro úlohu

1. Otevřete stránku úlohy Stream Analytics, klikněte na kartu **Výstupy,** klikněte na **Přidat**a vyberte **Data Lake Storage Gen1**.

    ![Přidání výstupu do úlohy](./media/data-lake-store-stream-analytics/create.output.1.png "Přidání výstupu do úlohy")

2. Na **nové výstupní** okno, zadejte následující hodnoty.

    ![Přidání výstupu do úlohy](./media/data-lake-store-stream-analytics/create.output.2.png "Přidání výstupu do úlohy")

    * Do **aliasu Výstup**zadejte jedinečný název výstupu úlohy. Toto je popisný název používaný v dotazech k přesměrování výstupu dotazu na tento účet Data Lake Storage Gen1.
    * Budete vyzváni k autorizaci přístupu k účtu Data Lake Storage Gen1. Klepněte na **tlačítko Autorizovat**.

3. Na **nové výstupní** okno, nadále poskytovat následující hodnoty.

    ![Přidání výstupu do úlohy](./media/data-lake-store-stream-analytics/create.output.3.png "Přidání výstupu do úlohy")

   * V **části Název účtu**vyberte účet Data Lake Storage Gen1, který jste již vytvořili, kam chcete odeslat výstup úlohy.
   * Do **pole Cesta předpona vzor**, zadejte cestu k souboru slouží k zápisu souborů v rámci zadaného data lake storage gen1 účet.
   * Pokud **Date format**jste v cestě předpony použili token data , můžete vybrat formát data, ve kterém jsou soubory uspořádány.
   * Pokud jste pro **formát Čas**použili časový token v cestě předpony, určete formát času, ve kterém jsou soubory uspořádány.
   * Ve **formátu serializace událostí**vyberte **položku CSV**.
   * V **případě oddělovače**vyberte **kartu**.
   * V **části Kódování**vyberte možnost **UTF-8**.
    
     Klikněte na **Vytvořit**. Portál nyní přidá výstup a otestuje připojení k němu.
    
## <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

1. Chcete-li spustit úlohu Stream Analytics, musíte spustit dotaz z karty **Dotaz.** V tomto kurzu můžete spustit ukázkový dotaz nahrazením zástupných symbolů vstupními a výstupními aliasy úlohy, jak je znázorněno na obrazovce capture níže.

    ![Spuštění dotazu](./media/data-lake-store-stream-analytics/run.query.png "Spuštění dotazu")

2. V horní části obrazovky klikněte na **Uložit** a potom na kartě **Přehled** klikněte na **Start**. V dialogovém okně vyberte **Vlastní čas**a nastavte aktuální datum a čas.

    ![Nastavit čas úlohy](./media/data-lake-store-stream-analytics/run.query.2.png "Nastavit čas úlohy")

    Chcete-li zahájit úlohu, klepněte na tlačítko **Zahájit.** Zahájení práce může trvat až několik minut.

3. Chcete-li spustit úlohu k výběru dat z objektu blob, zkopírujte ukázkový datový soubor do kontejneru objektů blob. Ukázkový datový soubor můžete získat z [úložiště Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). V tomto kurzu zkopírujeme soubor **vehicle1_09142014.csv**. K nahrání dat do kontejneru objektů blob můžete použít různé klienty, například [Průzkumník a údržbu zařízení Azure](https://storageexplorer.com/)Storage Explorer .

4. Na kartě **Přehled** v části **Sledování**se podívejte, jak byla data zpracována.

    ![Monitorování úlohy](./media/data-lake-store-stream-analytics/run.query.3.png "Monitorování úlohy")

5. Nakonec můžete ověřit, zda jsou výstupní data úlohy k dispozici v účtu Data Lake Storage Gen1. 

    ![Ověření výstupu](./media/data-lake-store-stream-analytics/run.query.4.png "Ověření výstupu")

    V podokně Průzkumník dat si všimněte, že výstup je zapsán do cesty ke`streamanalytics/job/output/{date}/{time}`složce, jak je určeno v nastavení výstupu Data Lake Storage Gen1 ( ).  

## <a name="see-also"></a>Viz také
* [Vytvoření clusteru HDInsight pro použití data lake storage gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
