---
title: Streamovat data ze služby Stream Analytics do Data Lake Store | Microsoft Docs
description: Pomocí Azure Stream Analytics datový proud dat do Azure Data Lake Store
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
ms.openlocfilehash: 8c045bed943427b5cb74291d70ee2eebcb90b2ef
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625229"
---
# <a name="stream-data-from-azure-storage-blob-into-data-lake-store-using-azure-stream-analytics"></a>Streamování dat z Azure Storage Blob do služby Data Lake Store pomocí Azure Stream Analytics
V tomto článku se dozvíte, jak používat Azure Data Lake Store jako výstup pro úlohu služby Azure Stream Analytics. Tento článek ukazuje jednoduchého scénáře, který čte data z objektu blob Azure Storage (vstup) a zapisuje data do Data Lake Store (výstup).

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Účet služby Azure Storage**. Kontejner objektů blob z tohoto účtu se použít jako vstup data úlohy Stream Analytics. Pro tento kurz předpokládá, že máte účet úložiště s názvem **storageforasa** kontejner v rámci účtu s názvem **storageforasacontainer**. Po vytvoření kontejneru nahrajte do ní ukázkový datový soubor. 
  
* **Účet Azure Data Lake Store**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Store s použitím webu Azure Portal](data-lake-store-get-started-portal.md). Předpokládejme, máte účet Data Lake Store s názvem **asadatalakestore**. 

## <a name="create-a-stream-analytics-job"></a>Vytvoření úlohy Stream Analytics
Začněte vytvořením úlohy služby Stream Analytics, který obsahuje vstupní zdroj a cíl výstupu. V tomto kurzu je zdroj kontejner objektů blob v Azure a jako cíl byla Data Lake Store.

1. Přihlaste se k [webu Azure Portal](https://portal.azure.com).

2. V levém podokně klikněte na tlačítko **úlohy Stream Analytics**a potom klikněte na **přidat**.

    ![Vytvoření úlohy Stream Analytics](./media/data-lake-store-stream-analytics/create.job.png "vytvořit úlohu služby Stream Analytics")

    > [!NOTE]
    > Zajistěte, aby ve stejné oblasti jako účet úložiště vytvoříte úlohu nebo je zpoplatněná další náklady na přesun dat mezi oblastmi.
    >

## <a name="create-a-blob-input-for-the-job"></a>Vytvoření vstupní objekt Blob pro úlohu

1. Otevřete stránku pro úlohu služby Stream Analytics v levém podokně klikněte **vstupy** a pak klikněte **přidat**.

    ![Přidat vstup do úlohy](./media/data-lake-store-stream-analytics/create.input.1.png "přidat vstup do úlohy")

2. Na **nový vstupní** okno, zadejte následující hodnoty.

    ![Přidat vstup do úlohy](./media/data-lake-store-stream-analytics/create.input.2.png "přidat vstup do úlohy")

    * Pro **vstupní alias**, zadejte jedinečný název pro vstupní úlohu.
    * Pro **typ zdroje**, vyberte **datový proud**.
    * Pro **zdroj**, vyberte **úložiště objektů Blob**.
    * Pro **předplatné**, vyberte **pomocí úložiště objektů blob z aktuálního předplatného**.
    * Pro **účet úložiště**, vyberte účet úložiště, který jste vytvořili v rámci požadované součásti. 
    * Pro **kontejneru**, vyberte kontejner, který jste vytvořili v na vybraný účet úložiště.
    * Pro **formát serializace událostí**, vyberte **CSV**.
    * Pro **oddělovač**, vyberte **kartě**.
    * Pro **kódování**, vyberte **UTF-8**.

    Klikněte na možnost **Vytvořit**. Na portálu teď přidá vstupu a otestuje připojení k němu.


## <a name="create-a-data-lake-store-output-for-the-job"></a>Vytvoření výstupní Data Lake Store pro úlohu

1. Klikněte na Otevřít stránku pro úlohu služby Stream Analytics **výstupy** a pak klikněte **přidat**.

    ![Přidat výstup do vaší úlohy](./media/data-lake-store-stream-analytics/create.output.1.png "přidat výstup do úlohy")

2. Na **nový výstupní** okno, zadejte následující hodnoty.

    ![Přidat výstup do vaší úlohy](./media/data-lake-store-stream-analytics/create.output.2.png "přidat výstup do úlohy")

    * Pro **alias pro výstup**, zadejte jedinečný název pro výstup úlohy. Toto je popisný název používaný v dotazech na přesměrujte výstup dotazu do tohoto Data Lake Store.
    * Pro **jímky**, vyberte **Data Lake Store**.
    * Zobrazí se výzva k autorizaci přístupu k účtu Data Lake Store. Klikněte na tlačítko **Autorizovat**.

3. Na **nový výstupní** okně pokračovat v poskytování následující hodnoty.

    ![Přidat výstup do vaší úlohy](./media/data-lake-store-stream-analytics/create.output.3.png "přidat výstup do úlohy")

    * Pro **název účtu**, vyberte účet Data Lake Store, jste již vytvořili, kam chcete úlohy, výstup k odeslání.
    * Pro **vzorek cesty předponu**, zadejte cestu k souboru umožňuje zapisovat vaše soubory v rámci zadaného účtu Data Lake Store.
    * Pro **formát data**, pokud jste použili token kalendářního data v cestě předponu, můžete vybrat formát data, ve kterém jsou uspořádány soubory.
    * Pro **formát času**, pokud jste použili token čas v cestě předponu, zadejte formát času, ve kterém jsou uspořádány soubory.
    * Pro **formát serializace událostí**, vyberte **CSV**.
    * Pro **oddělovač**, vyberte **kartě**.
    * Pro **kódování**, vyberte **UTF-8**.
    
    Klikněte na možnost **Vytvořit**. Na portálu se teď přidá výstup a otestuje připojení k němu.
    
## <a name="run-the-stream-analytics-job"></a>Spuštění úlohy Stream Analytics

1. Chcete-li spustit úlohu služby Stream Analytics, je nutné spustit dotaz z **dotazu** kartě. V tomto kurzu můžete spustit ukázkový dotaz nahraďte zástupné symboly úlohu vstupní a výstupní aliasy, jak je znázorněno v následujícím snímku obrazovky.

    ![Spuštění dotazu](./media/data-lake-store-stream-analytics/run.query.png "spuštění dotazu")

2. Klikněte na tlačítko **Uložit** z horní části obrazovky a pak z **přehled** , klikněte na **spustit**. V dialogovém okně vyberte **vlastní čas**a poté nastavte aktuální datum a čas.

    ![Nastavit čas úlohy](./media/data-lake-store-stream-analytics/run.query.2.png "nastavit čas úlohy")

    Klikněte na tlačítko **spustit** spustíte úlohu. Ho může trvat až několik minut spustíte úlohu.

3. Aktivuje úlohu a vyberte data z objektu blob, zkopírujte ukázkový datový soubor do kontejneru objektů blob. Můžete získat ukázkový soubor dat z [úložiště Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). V tomto kurzu budeme zkopírujte soubor **vehicle1_09142014.csv**. Můžete používat různé klienty, jako třeba [Azure Storage Explorer](http://storageexplorer.com/), odesílat data do kontejneru objektů blob.

4. Z **přehled** v části **monitorování**, najdete v části zpracovávání data.

    ![Úloha monitoru](./media/data-lake-store-stream-analytics/run.query.3.png "úlohy monitorování")

5. Nakonec můžete ověřit, že data výstup úlohy je k dispozici v účtu Data Lake Store. 

    ![Zkontrolujte výstup](./media/data-lake-store-stream-analytics/run.query.4.png "ověřte výstup")

    V podokně Průzkumník dat, Všimněte si, že je výstup zapsán do cesty ke složce uvedeného v Data Lake Store nastavení výstupu (`streamanalytics/job/output/{date}/{time}`).  

## <a name="see-also"></a>Další informace najdete v tématech
* [Vytvoření clusteru HDInsight používat Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
