---
title: Vyvolání Spark programy z Azure Data Factory | Microsoft Docs
description: Zjistěte, jak má být vyvolán Spark programy ze služby Azure data factory pomocí činnost MapReduce.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: e775798dbaaf93d5a9b497323a3b2fa365820550
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37046458"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Vyvolání Spark programy z kanálů služby Azure Data Factory

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Aktivita Hive](data-factory-hive-activity.md)
> * [Pig aktivity](data-factory-pig-activity.md)
> * [Činnost MapReduce](data-factory-map-reduce.md)
> * [Hadoop streamované aktivitě](data-factory-hadoop-streaming-activity.md)
> * [Spark aktivity](data-factory-spark.md)
> * [Machine Learning dávkového spuštění aktivity](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita prostředku aktualizace Learning počítače](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita uložené procedury](data-factory-stored-proc-activity.md)
> * [Aktivita data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [Vlastní aktivita .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Azure Data Factory, která je všeobecně dostupná. Pokud používáte aktuální verze služby Data Factory, najdete v části [transformace dat pomocí aktivity Apache Spark v datové továrně](../transform-data-using-spark.md).

## <a name="introduction"></a>Úvod
Aktivita Spark je jedním z [aktivit transformace dat](data-factory-data-transformation-activities.md) podporovaných službou Data Factory. Tato aktivita běží zadaný program Spark na váš cluster Spark v Azure HDInsight. 

> [!IMPORTANT]
> - Aktivita Spark nepodporuje clustery HDInsight Spark, které používají Azure Data Lake Store jako primární úložiště.
> - Aktivita Spark podporuje pouze existující (vlastní) clustery HDInsight Spark. HDInsight propojené služby na vyžádání nepodporuje.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Návod: Vytvoření kanálu s aktivitou Spark
Tady jsou obvyklá kroky k vytvoření objektu pro vytváření dat kanál s aktivitou Spark: 

* Vytvoření datové továrny
* Vytvoření služby Azure Storage, propojené propojení vašeho úložiště, který je přidružen k objektu pro vytváření dat cluster HDInsight Spark.
* Vytvoření propojené služby HDInsight propojit k objektu pro vytváření dat cluster Spark v HDInsight.
* Vytvořte datovou sadu, která odkazuje propojenou službu úložiště. V současné době je třeba zadat datovou sadu výstupů pro aktivitu i v případě, že neexistuje žádný výstup se vytváří. 
* Vytvoření kanálu s aktivitou Spark, která odkazuje propojená služba HDInsight, kterou jste vytvořili. Aktivita je nakonfigurován s datovou sadou, kterou jste vytvořili v předchozím kroku jako výstupní datové. Výstupní datové sady je určuje plán (každou hodinu, každý den). Proto je nutné zadat výstupní datovou sadu, i když aktivita neobsahuje skutečně výstup.

### <a name="prerequisites"></a>Požadavky
1. Vytvořit účet úložiště pro obecné účely podle pokynů v [vytvořit účet úložiště](../../storage/common/storage-create-storage-account.md#create-a-storage-account).

2. Vytvořte Spark cluster v HDInsight postupujte podle pokynů v tomto kurzu [vytvořte Spark cluster v HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Přidružte účet úložiště, kterou jste vytvořili v kroku 1 s tímto clusterem.

3. Stáhnout a revidovat souboru skriptu jazyka Python **test.py** nacházející se v [ https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py ](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

4. Nahrát **test.py** k **pyFiles** složku **adfspark** kontejneru ve službě blob storage. Vytvořte kontejner, složku, pokud ještě neexistují.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Pokud chcete vytvořit datovou továrnu, postupujte následovně:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte **Nový** > **Data a analýzy** > **Datová továrna**.

3. Na **nový objekt pro vytváření dat** okno, v části **název**, zadejte **SparkDF**.

   > [!IMPORTANT]
   > Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba "název objektu pro vytváření dat SparkDF není k dispozici", změňte název objektu pro vytváření dat. Například použijte yournameSparkDFdate a objektu pro vytváření dat vytvořit znovu. Další informace o pravidlech pojmenování najdete v tématu [Data Factory: Pravidla pojmenování](data-factory-naming-rules.md).

4. V části **Předplatné** vyberte předplatné Azure, ve kterém chcete datovou továrnu vytvořit.

5. Vyberte existující skupinu prostředků nebo vytvořte skupinu prostředků Azure.

6. Zaškrtněte políčko **Připnout na řídicí panel**.

7. Vyberte **Vytvořit**.

   > [!IMPORTANT]
   > Pokud chcete vytvářet instance služby Data Factory, musíte být členem role [Přispěvatel Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na úrovni předplatného nebo skupiny prostředků.

8. Objekt pro vytváření dat uvidíte, jak je zadáno v řídicím panelu portálu Azure.

9. Po vytvoření datové továrny se zobrazí stránka **Datová továrna** s obsahem datové továrny. Pokud nevidíte **objekt pro vytváření dat** vyberte dlaždici objektu pro vytváření dat na řídicím panelu.

    ![Okno Objekt pro vytváření dat](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Vytvoření propojených služeb
V tomto kroku vytvoříte dvě propojené služby. Jedna služba odkazuje na datovou továrnu váš cluster Spark a jiné služby odkazuje na datovou továrnu úložiště. 

#### <a name="create-a-storage-linked-service"></a>Vytvoření propojené služby Storage
V tomto kroku s datovou továrnou propojíte svůj účet úložiště. Datové sady, které vytvoříte v kroku později v tomto názorném postupu odkazuje na tato propojená služba. Propojená služba HDInsight, kterou definujete v dalším kroku odkazuje na tato propojená služba příliš. 

1. Na **objekt pro vytváření dat** vyberte **vytvořit a nasadit**. Zobrazí se editoru služby Data Factory.

2. Vyberte **Nové úložiště dat** a zvolte **Azure Storage**.

   ![Nové datové úložiště](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

3. Skript JSON, který použijete k vytvoření úložiště propojené služby se zobrazí v editoru.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

4. Nahraďte **název účtu** a **klíč účtu** s názvem a přístupovým klíčem účtu úložiště. Pokud chcete zjistit, jak získat přístupový klíč k úložišti, přečtěte si, jak zobrazit, kopírovat a znovu vygenerovat přístupové klíče k úložišti, v tématu [Správa účtu úložiště](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

5. Chcete-li nasadit propojené služby, vyberte **nasadit** na panelu příkazů. Po úspěšném nasazení propojené služby okno Koncept-1 zmizí. Ve stromovém zobrazení na levé straně se zobrazí **AzureStorageLinkedService**.

#### <a name="create-an-hdinsight-linked-service"></a>Vytvoření propojené služby HDInsight
V tomto kroku vytvoříte propojené služby HDInsight propojit k objektu pro vytváření dat cluster HDInsight Spark. HDInsight cluster se používá ke spuštění programu Spark určeného v aktivitě Spark kanálu v této ukázce. 

1. V editoru služby Data Factory, vyberte **Další** > **nový výpočet** > **clusteru HDInsight**.

    ![Vytvoření propojené služby HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

2. Následující fragment kódu zkopírujte a vložte ho do okna Koncept-1. V editoru JSON proveďte následující kroky:

    a. Zadejte identifikátor URI clusteru HDInsight Spark. Například: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Zadejte jméno uživatele, který má přístup ke clusteru Spark.

    c. Zadejte heslo pro uživatele.

    d. Zadejte službu úložiště propojené, který je přidružen ke clusteru HDInsight Spark. V tomto příkladu je AzureStorageLinkedService.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Aktivita Spark nepodporuje clustery HDInsight Spark, které používají Azure Data Lake Store jako primární úložiště.
    > - Aktivita Spark podporuje pouze existující (vlastní) clustery HDInsight Spark. HDInsight propojené služby na vyžádání nepodporuje.

    Další informace o propojená služba HDInsight najdete v tématu [propojená služba HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

3. Chcete-li nasadit propojené služby, vyberte **nasadit** na panelu příkazů. 

### <a name="create-the-output-dataset"></a>Vytvoření výstupní datové sady
Výstupní datové sady je určuje plán (každou hodinu, každý den). Proto je nutné zadat výstupní datovou sadu aktivity Spark v kanálu, i když aktivita neobsahuje žádný výstup. Určení vstupní datové sady pro aktivitu je volitelné.

1. V editoru služby Data Factory vyberte **Další** > **Nová datová sada** > **Úložiště objektů blob v Azure**.

2. Následující fragment kódu zkopírujte a vložte ho do okna Koncept-1. Fragmentu kódu JSON Určuje datovou sadu s názvem **OutputDataset**. Kromě toho určíte, že se mají výsledky ukládat do kontejneru objektů blob s názvem **adfspark** a ve složce s názvem **pyFiles výstupní**. Jak je uvedeno nahoře, je tato datová sada fiktivní datová sada. Program Spark v tomto příkladu neobsahuje žádný výstup. **Dostupnosti** části určuje, že se výstupní sada vytváří denně. 

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
3. Chcete-li nasadit datovou sadu, vyberte **nasadit** na panelu příkazů.


### <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte kanál s aktivitou HDInsightSpark. V současnosti určuje plán výstupní datová sada, takže musíte výstupní datovou sadu vytvořit i v případě, že aktivita negeneruje žádný výstup. Pokud aktivita nemá žádný vstup, vstupní datovou sadu vytvářet nemusíte. Proto žádné vstupní datové sady je zadána v tomto příkladu.

1. V editoru služby Data Factory vyberte **Další** > **Nový kanál**.

2. Skript v okna koncept-1 nahraďte následující skript:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```
    Je třeba počítat s následujícím:

    a. **Typ** je nastavena na **HDInsightSpark**.

    b. **RootPath** je nastavena na **adfspark\\pyFiles** kde adfspark je kontejner objektů blob a pyFiles je složka, soubor v tomto kontejneru. V tomto příkladu úložiště objektů blob je ten, který je přidružen Spark cluster. Můžete nahrát soubor na jiný účet úložiště. Pokud tak učiníte, vytvořte propojená služba úložiště propojení objektu pro vytváření dat. Tento účet úložiště. Potom zadejte název propojené služby, jako hodnotu **sparkJobLinkedService** vlastnost. Další informace o této vlastnosti a dalších vlastností nepodporuje Spark aktivity najdete v tématu [Spark vlastnosti aktivity](#spark-activity-properties).

    c. **EntryFilePath** je nastavena na **test.py**, což je soubor Python.

    d. **Getdebuginfo –** je nastavena na **vždy**, tzn., soubory protokolů jsou vždy vygeneruje (úspěch nebo neúspěch).

    > [!IMPORTANT]
    > Doporučujeme, abyste tuto vlastnost nenastavujte na `Always` v provozním prostředí není-li se řešení problémů.

    e. **Výstupy** obsahuje jednu výstupní datovou sadu. Je třeba zadat datovou sadu výstupů i v případě, že Spark program neobsahuje žádný výstup. Výstupní datovou sadu jednotky plán pro kanál (každou hodinu, každý den). 

    Další informace o vlastnostech podporovaných zprostředkovatelem aktivity Spark, najdete v části [Spark vlastnosti aktivity](#spark-activity-properties).

3. Chcete-li nasadit kanálu, vyberte **nasadit** na panelu příkazů.

### <a name="monitor-a-pipeline"></a>Monitorování kanálu
1. Na **objekt pro vytváření dat** vyberte **monitorování a správa** spustit monitorování aplikací na jiné kartě.

    ![Dlaždice Monitorování a správa](media/data-factory-spark/monitor-and-manage-tile.png)

2. Změna **počáteční čas** filtru v horní části na **2/1/2017**a vyberte **použít**.

3. Zobrazí se okno jenom jedna aktivita, protože existuje pouze jeden den mezi počátečním (2017-02-01) a koncový čas (2017-02-02) kanálu. Potvrďte, že datový řez je v **připraven** stavu.

    ![Monitorování kanálu](media/data-factory-spark/monitor-and-manage-app.png)

4. V **aktivity windows** vyberte aktivitu spustit zobrazíte její podrobnosti. Pokud dojde k chybě, zobrazí podrobnosti o tom, v pravém podokně.

### <a name="verify-the-results"></a>Ověření výsledků

1. Spustit tak, že přejdete do poznámkového bloku Jupyter pro váš cluster HDInsight Spark [tento web](https://CLUSTERNAME.azurehdinsight.net/jupyter). Můžete také otevřít řídicí panel clusteru pro HDInsight Spark clusteru, a pak spusťte poznámkového bloku Jupyter.

2. Vyberte **nový** > **PySpark** spusťte nový poznámkový blok.

    ![Nový poznámkový blok Jupyter](media/data-factory-spark/jupyter-new-book.png)

3. Kopírování a vkládání textu a stisknutím klávesy Shift + Enter na konci druhý příkaz spusťte následující příkaz:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
4. Zkontrolujte, jestli data z tabulky TVK. 

    ![Výsledky dotazu Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article --> Podrobné pokyny najdete v části [spuštění dotazů Spark SQL](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

### <a name="troubleshooting"></a>Řešení potíží
Vzhledem k tomu, že nastavíte getdebuginfo – na **vždy**, najdete v části protokolu podsložky ve složce pyFiles v kontejnerech objektů blob. Další informace najdete v souboru protokolu ve složce protokolů. Tento soubor protokolu je obzvláště užitečná, když dojde k chybě. V produkčním prostředí, můžete chtít nastavit na **selhání**.

Pro další informace o řešení, proveďte následující kroky:


1. Přejděte do části `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster` (Soubor > Nový > Jiné).

    ![Uživatelské rozhraní YARN aplikace](media/data-factory-spark/yarnui-application.png)

2. Vyberte **protokoly** pro jedno spuštění pokusí.

    ![Stránka aplikace](media/data-factory-spark/yarn-applications.png)

3. Zobrazí následující další chybové informace na stránce protokolu:

    ![Chyba protokolu](media/data-factory-spark/yarnui-application-error.png)

Následující části obsahují informace o entitách objekt pro vytváření dat v datové továrně používat Spark cluster a aktivity Spark.

## <a name="spark-activity-properties"></a>Vlastnosti aktivity Spark
Zde je ukázka definici JSON kanálu s aktivitou Spark: 

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

Následující tabulka popisuje vlastnostech JSON použitých v definici JSON.

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| jméno | Název aktivity v kanálu. | Ano |
| description | Text, který popisuje, jakým způsobem aktivita naloží. | Ne |
| type | Tato vlastnost musí být nastavená na HDInsightSpark. | Ano |
| linkedServiceName | Název propojená služba HDInsight na kterém běží Spark program. | Ano |
| rootPath | Kontejner objektů blob a složky, která obsahuje soubor Spark. Název souboru je malá a velká písmena. | Ano |
| entryFilePath | Relativní cesta ke kořenové složce Spark kódu nebo balíčku. | Ano |
| Název třídy | Hlavní třídy aplikace Java/Spark. | Ne |
| argumenty | Seznam argumentů příkazového řádku pro Spark program. | Ne |
| proxyUser | Uživatelský účet zosobnění spuštění programu Spark. | Ne |
| sparkConfig | Zadejte hodnoty pro vlastnosti konfigurace Spark uvedené v [konfigurace Spark: vlastnosti aplikace](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Ne |
| getdebuginfo – | Určuje, kdy se soubory protokolu Spark zkopírují do úložiště používaný v clusteru HDInsight (nebo) zadaný ve sparkJobLinkedService. Povolené hodnoty jsou None, vždy nebo selhání. Výchozí hodnota je None. | Ne |
| sparkJobLinkedService | Úložiště propojená služba, která obsahuje Spark soubor úlohy, závislosti a protokoly. Pokud hodnotu pro tuto vlastnost nezadáte, použije se úložiště přidružený k clusteru HDInsight. | Ne |

## <a name="folder-structure"></a>Struktura složek
Aktivita Spark nepodporuje vloženého skriptu jako Pig a do aktivity Hive. Spark úlohy jsou také více extensible než úlohy Pig nebo Hive. Pro úlohy Spark, můžete zadat více závislostí, jako jar balíčky (umístěné v jazyce java cesty pro třídy), soubory Python (umístit na PYTHONPATH) a všechny další soubory.

Vytvořte následující strukturu složek ve službě blob storage, na kterou odkazuje propojená služba HDInsight. Potom obsah nahrajete soubory závislé na příslušné podsložky v kořenové složce reprezentována **entryFilePath**. Například nahrání souborů Python do podsložky pyFiles a jar soubory do podsložky JAR kořenové složky. Služba Data Factory v době běhu očekává následující strukturu složek ve službě blob storage: 

| Cesta | Popis | Požaduje se | Typ |
| ---- | ----------- | -------- | ---- |
| . | Kořenová cesta úlohy Spark v úložišti propojená služba. | Ano | Složka |
| &lt;Uživatelem definované &gt; | Cesta, která odkazuje na soubor položka úlohy Spark. | Ano | File |
| . / jars | Všechny soubory v této složce se nahrála a umístit na cestě třídy Java clusteru. | Ne | Složka |
| . / pyFiles | Všechny soubory v této složce se nahrála a umístit do PYTHONPATH clusteru. | Ne | Složka |
| . / soubory | Všechny soubory v této složce se nahrála a umístit na vykonavatele pracovní adresář. | Ne | Složka |
| . / archivy | Všechny soubory v této složce nekomprimované. | Ne | Složka |
| . / protokoly | Složky, kde jsou uložené protokoly z clusteru Spark.| Ne | Složka |

Tady je příklad pro úložiště, který obsahuje dva soubory úlohy Spark v úložišti objektů blob, na kterou odkazuje propojená služba HDInsight:

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
