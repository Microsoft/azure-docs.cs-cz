---
title: Vyvolání programů Spark z Azure Data Factory
description: Zjistěte, jak vyvolat programy Spark z datové továrny Azure pomocí aktivity MapReduce.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: ce5fb014c7d954b3e8430a86430c6a666adff204
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75969234"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Vyvolání programů Spark z kanálů Azure Data Factory

> [!div class="op_single_selector" title1="Transformační aktivity"]
> * [Aktivita úlu](data-factory-hive-activity.md)
> * [Aktivita prasat](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Aktivita streamování hadoopu](data-factory-hadoop-streaming-activity.md)
> * [Aktivita jiskry](data-factory-spark.md)
> * [Aktivita dávkového spuštění strojového učení](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita zdrojů aktualizace strojového učení](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita uložené procedury](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Azure Data Factory, která je všeobecně dostupná. Pokud používáte aktuální verzi služby Data Factory, [přečtěte si téma Transformace dat pomocí aktivity Apache Spark v Datové továrně](../transform-data-using-spark.md).

## <a name="introduction"></a>Úvod
Aktivita Spark je jednou z [aktivit transformace dat](data-factory-data-transformation-activities.md) podporovaných data factory. Tato aktivita spustí zadaný program Spark ve vašem clusteru Spark v Azure HDInsight.

> [!IMPORTANT]
> - Aktivita Spark nepodporuje clustery HDInsight Spark, které používají Azure Data Lake Store jako primární úložiště.
> - Aktivita Spark podporuje pouze existující (vaše vlastní) clustery HDInsight Spark. Nepodporuje službu propojenou s HDInsight na vyžádání.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Návod: Vytvoření kanálu s aktivitou Spark
Tady jsou typické kroky k vytvoření kanálu datové továrny s aktivitou Spark:

* Vytvoření datové továrny
* Vytvořte propojenou službu Azure Storage, která propojí vaše úložiště, které je přidružené k vašemu clusteru HDInsight Spark, s toutovou továrně dat.
* Vytvořte propojenou službu HDInsight, která propojí váš cluster Spark v HDInsightu s továrně dat.
* Vytvořte datovou sadu, která odkazuje na propojenou službu úložiště. V současné době je nutné zadat výstupní datovou sadu pro aktivitu i v případě, že není vytvářen žádný výstup.
* Vytvořte kanál s aktivitou Spark, která odkazuje na propojenou službu HDInsight, kterou jste vytvořili. Aktivita je konfigurována s datovou sadou, kterou jste vytvořili v předchozím kroku jako výstupní datová sada. Výstupní datová sada je to, co řídí plán (každou hodinu, denně). Proto je nutné zadat výstupní datovou sadu, i když aktivita ve skutečnosti nevytváří výstup.

### <a name="prerequisites"></a>Požadavky
1. Vytvořte účet úložiště pro obecné účely podle pokynů v části [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md).

1. Vytvořte cluster Spark ve službě HDInsight podle pokynů v kurzu [Vytvořit cluster Spark ve službě HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Přidružte účet úložiště, který jste vytvořili v kroku 1, k tomuto clusteru.

1. Stáhněte si a zkontrolujte [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py)soubor skriptu Pythonu, **test.py** umístěný na adrese .

1. Nahrajte **test.py** do složky **pyFiles** v kontejneru **adfspark** v úložišti objektů blob. Vytvořte kontejner a složku, pokud neexistují.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Pokud chcete vytvořit datovou továrnu, postupujte následovně:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Vyberte **Nová** > **data + Analytics** > Data**Factory**.

1. V okně **Nová data factory** v části **Name**zadejte **SparkDF**.

   > [!IMPORTANT]
   > Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba "Název datové továrny SparkDF není k dispozici", změňte název datové továrny. Například použijte yournameSparkDFdate a znovu vytvořte továrnu dat. Další informace o pravidlech pojmenování najdete v tématu [Data Factory: Pravidla pojmenování](data-factory-naming-rules.md).

1. V části **Předplatné** vyberte předplatné Azure, ve kterém chcete datovou továrnu vytvořit.

1. Vyberte existující skupinu prostředků nebo vytvořte skupinu prostředků Azure.

1. Zaškrtněte políčko **Připnout na řídicí panel**.

1. Vyberte **Vytvořit**.

   > [!IMPORTANT]
   > Pokud chcete vytvářet instance služby Data Factory, musíte být členem role [Přispěvatel Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na úrovni předplatného nebo skupiny prostředků.

1. Zobrazí se data factory, jak se vytvoří v řídicím panelu portálu Azure.

1. Po vytvoření datové továrny se zobrazí stránka **Datová továrna** s obsahem datové továrny. Pokud stránku Data **factory** nevidíte, vyberte dlaždici pro vaši datovou továrnu na řídicím panelu.

    ![Okno Objekt pro vytváření dat](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Vytvoření propojených služeb
V tomto kroku vytvoříte dvě propojené služby. Jedna služba propojuje váš cluster Spark s vaší továrně dat a druhá služba propojuje vaše úložiště s vaší továrně dat.

#### <a name="create-a-storage-linked-service"></a>Vytvoření propojené služby Storage
V tomto kroku s datovou továrnou propojíte svůj účet úložiště. Datová sada, kterou vytvoříte v kroku později v tomto návodu odkazuje na tuto propojenou službu. Propojená služba HDInsight, kterou definujete v dalším kroku, odkazuje také na tuto propojenou službu.

1. V okně **Data factory** vyberte Author **and deploy**. Zobrazí se Editor datové továrny.

1. Vyberte **Nové úložiště dat** a zvolte **Azure Storage**.

   ![Nové úložiště dat](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. V editoru se zobrazí skript JSON, který použijete k vytvoření propojené služby úložiště.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. **Nahraďte název účtu** a klíč **účtu** názvem a přístupovým klíčem svého účtu úložiště. Informace o tom, jak získat přístupový klíč úložiště, najdete v [tématu Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).

1. Chcete-li nasadit propojenou službu, vyberte na panelu příkazů možnost **Nasadit.** Po úspěšném nasazení propojené služby okno Koncept-1 zmizí. Ve stromovém zobrazení na levé straně se zobrazí **AzureStorageLinkedService**.

#### <a name="create-an-hdinsight-linked-service"></a>Vytvoření propojené služby HDInsight
V tomto kroku vytvoříte propojenou službu HDInsight, která propojí váš cluster HDInsight Spark s továrnou na data. Cluster HDInsight se používá ke spuštění programu Spark určeného v aktivitě Spark kanálu v této ukázce.

1. V Editoru datových tovev vyberte **Další** > **nový výpočetní** > **cluster HDInsight**.

    ![Vytvoření propojené služby HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Následující fragment kódu zkopírujte a vložte ho do okna Koncept-1. V editoru JSON postupujte takto:

    a. Zadejte identifikátor URI pro cluster HDInsight Spark. Například: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Zadejte jméno uživatele, který má přístup ke clusteru Spark.

    c. Zadejte heslo tohoto uživatele.

    d. Zadejte službu propojenou úložištěm, která je přidružená ke clusteru HDInsight Spark. V tomto příkladu je AzureStorageLinkedService.

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
    > - Aktivita Spark podporuje pouze existující (vaše vlastní) clustery HDInsight Spark. Nepodporuje službu propojenou s HDInsight na vyžádání.

    Další informace o propojené službě HDInsight najdete v tématu [propojená služba HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Chcete-li nasadit propojenou službu, vyberte na panelu příkazů možnost **Nasadit.**

### <a name="create-the-output-dataset"></a>Vytvoření výstupní datové sady
Výstupní datová sada je to, co řídí plán (každou hodinu, denně). Proto je nutné zadat výstupní datovou sadu pro aktivitu Spark v kanálu, i když aktivita nevytváří žádný výstup. Určení vstupní datové sady pro aktivitu je volitelné.

1. V Editoru datových toky vyberte **Další** > **nové úložiště objektů** > **blob Azure**.

1. Následující fragment kódu zkopírujte a vložte ho do okna Koncept-1. Úryvek JSON definuje datovou sadu nazvanou **OutputDataset**. Kromě toho určíte, že výsledky jsou uloženy v kontejneru objektů blob s názvem **adfspark** a složky s názvem **pyFiles/output**. Jak již bylo zmíněno dříve, tato datová sada je fiktivní datová sada. Program Spark v tomto příkladu nevytváří žádný výstup. Část **dostupnosti** určuje, že výstupní datová sada je vytvářena denně.

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
1. Pokud chcete nasadit datovou sadu, vyberte Na panelu příkazů možnost **Nasadit.**


### <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte kanál s aktivitou HDInsightSpark. V současnosti určuje plán výstupní datová sada, takže musíte výstupní datovou sadu vytvořit i v případě, že aktivita negeneruje žádný výstup. Pokud aktivita nemá žádný vstup, vstupní datovou sadu vytvářet nemusíte. Proto není v tomto příkladu zadána žádná vstupní datová sada.

1. V Editoru datových tocích vyberte **Další** > **nový kanál**.

1. Nahraďte skript v okně Koncept-1 následujícím skriptem:

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

    a. Vlastnost **type** je nastavena na **HDInsightSpark**.

    b. Vlastnost **rootPath** je **nastavena\\na adfspark pyFiles,** kde adfspark je kontejner objektů blob a pyFiles je složka souborů v tomto kontejneru. V tomto příkladu je úložiště objektů blob ten, který je přidružený ke clusteru Spark. Soubor můžete nahrát do jiného účtu úložiště. Pokud tak učiníte, vytvořte službu propojenou úložištěm, která propojí tento účet úložiště s toto datové toky. Potom zadejte název propojené služby jako hodnotu pro **vlastnost sparkJobLinkedService.** Další informace o této vlastnosti a dalších vlastnostech podporovaných aktivitou Spark naleznete v tématu [Vlastnosti aktivity Spark](#spark-activity-properties).

    c. Vlastnost **entryFilePath** je nastavena na **test.py**, což je soubor Pythonu.

    d. Vlastnost **getDebugInfo** je nastavena na **always**, což znamená, že soubory protokolu jsou vždy generovány (úspěch nebo neúspěch).

    > [!IMPORTANT]
    > Doporučujeme nenastavovat `Always` tuto vlastnost v produkčním prostředí, pokud neřešíte problém.

    e. Výstupní **outputs** sekce má jednu výstupní datovou sadu. Je nutné zadat výstupní datovou sadu i v případě, že program Spark nevytváří žádný výstup. Výstupní datová sada řídí plán pro kanál (každou hodinu, denně).

    Další informace o vlastnostech podporovaných aktivitou Spark naleznete v části [Vlastnosti aktivity Spark](#spark-activity-properties).

1. Pokud chcete nasadit kanál, vyberte Na panelu příkazů možnost **Nasadit.**

### <a name="monitor-a-pipeline"></a>Monitorování kanálu
1. V okně **Data factory** vyberte Sledování **& Spravovat** a spusťte monitorovací aplikaci na jiné kartě.

    ![Dlaždice Monitorování a správa](media/data-factory-spark/monitor-and-manage-tile.png)

1. Změňte filtr **Čas zahájení** v horní části na **2/1/2017**a vyberte **Použít**.

1. Zobrazí se pouze jedno okno aktivity, protože mezi zahájením (2017-02-01) a koncovým časem (2017-02-02) kanálu existuje pouze jeden den. Zkontrolujte, zda je řez dat ve stavu **Připraveno.**

    ![Monitorování kanálu](media/data-factory-spark/monitor-and-manage-app.png)

1. V seznamu **Okna aktivity** vyberte spustit aktivitu, abyste o ní viděli podrobnosti. Pokud dojde k chybě, zobrazí se podrobnosti o něm v pravém podokně.

### <a name="verify-the-results"></a>Ověření výsledků

1. Spusťte jupyterový poznámkový blok pro cluster HDInsight Spark tak, že přejdete na [tento web](https://CLUSTERNAME.azurehdinsight.net/jupyter). Můžete také otevřít řídicí panel clusteru pro cluster HDInsight Spark a potom spustit poznámkový blok Jupyter.

1. Výběrem **možnosti Nový** > **PySpark** spusťte nový poznámkový blok.

    ![Jupyter nový notebook](media/data-factory-spark/jupyter-new-book.png)

1. Spusťte následující příkaz zkopírováním a vložením textu a stisknutím shift+enter na konci druhého příkazu:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Potvrďte, že se zobrazují data z hvac tabulky.

    ![Výsledky dotazu Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Podrobné pokyny naleznete v části [Spuštění dotazu Spark SQL](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

### <a name="troubleshooting"></a>Řešení potíží
Vzhledem k tomu, že nastavíte getDebugInfo na **vždy**, zobrazí se podsložka protokolu ve složce pyFiles v kontejneru objektů blob. Soubor protokolu ve složce protokolu poskytuje další informace. Tento soubor protokolu je zvláště užitečné, pokud dojde k chybě. V provozním prostředí můžete chtít nastavit na **selhání**.

Další řešení potíží naleznete v následujících krocích:


1. Přejděte do části `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster` (Soubor > Nový > Jiné).

    ![Aplikace UI YARN](media/data-factory-spark/yarnui-application.png)

1. Vyberte **protokoly** pro jeden z pokusů o spuštění.

    ![Stránka aplikace](media/data-factory-spark/yarn-applications.png)

1. Na stránce protokolu se zobrazí následující další informace o chybě:

    ![Chyba protokolu](media/data-factory-spark/yarnui-application-error.png)

Následující části obsahují informace o entitách datové továrny pro použití clusteru Spark a aktivity Spark v datové továrně.

## <a name="spark-activity-properties"></a>Vlastnosti aktivity jiskry
Tady je ukázková definice JSON kanálu s aktivitou Spark:

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
                    },
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

Následující tabulka popisuje vlastnosti JSON použité v definici JSON.

| Vlastnost | Popis | Požaduje se |
| -------- | ----------- | -------- |
| jméno | Název aktivity v kanálu. | Ano |
| description | Text popisující, co aktivita dělá. | Ne |
| type | Tato vlastnost musí být nastavena na HDInsightSpark. | Ano |
| linkedServiceName | Název propojené služby HDInsight, na které běží program Spark. | Ano |
| rootPath | Kontejner objektů blob a složka, která obsahuje soubor Spark. Název souboru rozlišuje malá a velká písmena. | Ano |
| entryFilePath | Relativní cesta ke kořenové složce kódu Spark/balíčku. | Ano |
| Classname | Aplikace Java / Spark hlavní třídy. | Ne |
| Argumenty | Seznam argumentů příkazového řádku do programu Spark. | Ne |
| proxyUživatel | Uživatelský účet, který se má zosobnit, aby se spouštěl program Spark. | Ne |
| sparkConfig | Zadejte hodnoty pro vlastnosti konfigurace Spark uvedené v [konfiguraci Spark: Vlastnosti aplikace](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Ne |
| getDebugInfo | Určuje, kdy jsou soubory protokolu Spark zkopírovány do úložiště používaného clusterem HDInsight (nebo) určeného službou sparkJobLinkedService. Povolené hodnoty jsou Žádné, Vždy nebo Selhání. Výchozí hodnota je žádné. | Ne |
| sparkJobLinkedService | Propojená služba Úložiště, která obsahuje soubor úlohy Spark, závislosti a protokoly. Pokud nezadáte hodnotu pro tuto vlastnost, použije se úložiště přidružené ke clusteru HDInsight. | Ne |

## <a name="folder-structure"></a>Struktura složek
Aktivita Spark nepodporuje válčné skripty jako aktivity Pig a Hive. Spark pracovních míst jsou také rozšiřitelné než Pig / Hive pracovních míst. Pro úlohy Spark můžete poskytnout více závislostí, jako jsou balíčky jar (umístěné v java CLASSPATH), soubory Pythonu (umístěné na PYTHONPATH) a všechny ostatní soubory.

Vytvořte následující strukturu složek v úložišti objektů blob, na kterou odkazuje propojená služba HDInsight. Potom nahrajte závislé soubory do příslušných podsložek v kořenové složce reprezentované **entryFilePath**. Například nahrajte soubory Pythonu do podsložky pyFiles a jar do podsložky sklenic kořenové složky. Za běhu služba Data Factory očekává následující strukturu složek v úložišti objektů blob:

| Cesta | Popis | Požaduje se | Typ |
| ---- | ----------- | -------- | ---- |
| . | Kořenová cesta úlohy Spark v propojené službě úložiště. | Ano | Složka |
| &lt;definováno uživatelem&gt; | Cesta, která odkazuje na vstupní soubor úlohy Spark. | Ano | File |
| ./sklenice | Všechny soubory v této složce jsou odeslány a umístěny na cestě třídy Java clusteru. | Ne | Složka |
| ./pyFiles | Všechny soubory v této složce jsou odeslány a umístěny na PYTHONPATH clusteru. | Ne | Složka |
| ./soubory | Všechny soubory v této složce jsou odeslány a umístěny do pracovního adresáře prováděcího modulu. | Ne | Složka |
| ./archivy | Všechny soubory v této složce jsou nekomprimované. | Ne | Složka |
| ./protokoly | Složka, ve které jsou uloženy protokoly z clusteru Spark.| Ne | Složka |

Tady je příklad úložiště, které obsahuje dva soubory úloh Spark v úložišti objektů blob, na které odkazuje propojená služba HDInsight:

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
