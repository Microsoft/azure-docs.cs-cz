---
title: Vyvolání programů Spark z Azure Data Factory
description: Naučte se volat programy Spark z Azure Data Factory pomocí aktivity MapReduce.
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
ms.openlocfilehash: 97e2be64818888040b7e6ac3bc8861da24ebdbbd
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92359947"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Vyvolání programů Spark z Azure Data Factory kanálů

> [!div class="op_single_selector" title1="Aktivity transformace"]
> * [Aktivita v podregistru](data-factory-hive-activity.md)
> * [Aktivita prasete](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Aktivita streamování Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktivita Sparku](data-factory-spark.md)
> * [Aktivita spuštění dávky Azure Machine Learning Studio (Classic)](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita aktualizace prostředku Azure Machine Learning Studio (Classic)](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita uložené procedury](data-factory-stored-proc-activity.md)
> * [Aktivita Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [Vlastní aktivita .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Azure Data Factory, která je všeobecně dostupná. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformace dat pomocí Apache Spark aktivity v Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Úvod
Aktivita Spark je jednou z [aktivit transformace dat](data-factory-data-transformation-activities.md) , které podporuje Data Factory. Tato aktivita spustí zadaný program Spark v clusteru Spark ve službě Azure HDInsight.

> [!IMPORTANT]
> - Aktivita Spark nepodporuje clustery HDInsight Spark, které používají Azure Data Lake Store jako primární úložiště.
> - Aktivita Spark podporuje pouze existující clustery HDInsight Spark. Nepodporuje propojenou službu HDInsight na vyžádání.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Návod: vytvoření kanálu s aktivitou Spark
Tady jsou typické kroky k vytvoření kanálu datové továrny s aktivitou Sparku:

* Vytvoření datové továrny
* Vytvořte propojenou službu Azure Storage k propojení úložiště, které je přidruženo k vašemu clusteru HDInsight Spark, k objektu pro vytváření dat.
* Vytvořte propojenou službu HDInsight, která propojí váš cluster Spark v HDInsight s datovou továrnou.
* Vytvořte datovou sadu, která odkazuje na propojenou službu úložiště. V současné době je nutné zadat výstupní datovou sadu pro aktivitu, i když není vytvořen výstup.
* Vytvořte kanál s aktivitou Spark, který odkazuje na vytvořenou propojenou službu HDInsight. Aktivita je nakonfigurována s datovou sadou, kterou jste vytvořili v předchozím kroku jako výstupní datovou sadu. Výstupní datová sada je to, co určuje plán (každou hodinu, každý den). Proto je nutné zadat výstupní datovou sadu, i když aktivita ve skutečnosti neprodukuje výstup.

### <a name="prerequisites"></a>Předpoklady
1. Podle pokynů v části [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md)vytvořte účet úložiště pro obecné účely.

1. Vytvořte cluster Spark v HDInsight podle pokynů v kurzu [Vytvoření clusteru Spark ve službě HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Přidružte k tomuto clusteru účet úložiště, který jste vytvořili v kroku 1.

1. Stáhněte si a zkontrolujte soubor skriptu Pythonu **test.py** umístěný na adrese [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py) .

1. Nahrajte **test.py** do složky **pyFiles** v kontejneru **adfspark** v úložišti objektů BLOB. Vytvořte kontejner a složku, pokud neexistují.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Pokud chcete vytvořit datovou továrnu, postupujte následovně:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).

1. Vyberte **Nový**  >  **data a analýzy**  >  **Data Factory**.

1. V okně **Nová datová továrna** v části **název**zadejte **SparkDF**.

   > [!IMPORTANT]
   > Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba název objektu pro vytváření dat SparkDF není k dispozici, změňte název datové továrny. Použijte například yournameSparkDFdate a vytvořte datovou továrnu znovu. Další informace o pravidlech pojmenování najdete v tématu [Data Factory: Pravidla pojmenování](data-factory-naming-rules.md).

1. V části **Předplatné** vyberte předplatné Azure, ve kterém chcete datovou továrnu vytvořit.

1. Vyberte existující skupinu prostředků nebo vytvořte skupinu prostředků Azure.

1. Zaškrtněte políčko **Připnout na řídicí panel**.

1. Vyberte **Vytvořit**.

   > [!IMPORTANT]
   > Pokud chcete vytvářet instance služby Data Factory, musíte být členem role [Přispěvatel Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na úrovni předplatného nebo skupiny prostředků.

1. Datová továrna se zobrazí při vytvoření na řídicím panelu Azure Portal.

1. Po vytvoření datové továrny se zobrazí stránka **Datová továrna** s obsahem datové továrny. Pokud nevidíte stránku **Data Factory** , vyberte na řídicím panelu dlaždici pro vaši datovou továrnu.

    ![Okno Objekt pro vytváření dat](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Vytvoření propojených služeb
V tomto kroku vytvoříte dvě propojené služby. Jedna služba propojuje váš cluster Spark s datovou továrnou a druhá služba propojuje vaše úložiště s datovou továrnou.

#### <a name="create-a-storage-linked-service"></a>Vytvoření propojené služby Storage
V tomto kroku s datovou továrnou propojíte svůj účet úložiště. Datová sada, kterou vytvoříte v kroku dále v tomto návodu, odkazuje na tuto propojenou službu. Propojená služba HDInsight, kterou definujete v dalším kroku, odkazuje i na tuto propojenou službu.

1. V okně **objekt pro vytváření dat** vyberte **vytvořit a nasadit**. Zobrazí se Editor Data Factory.

1. Vyberte **Nové úložiště dat** a zvolte **Azure Storage**.

   ![Nové úložiště dat](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. V editoru se zobrazí skript JSON, který použijete k vytvoření propojené služby úložiště.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Nahraďte **název účtu** a **klíč účtu** názvem a přístupovým klíčem účtu úložiště. Informace o tom, jak získat přístupový klíč k úložišti, najdete v tématu [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).

1. Chcete-li nasadit propojenou službu, vyberte možnost **nasadit** na panelu příkazů. Po úspěšném nasazení propojené služby okno Koncept-1 zmizí. Ve stromovém zobrazení na levé straně se zobrazí **AzureStorageLinkedService**.

#### <a name="create-an-hdinsight-linked-service"></a>Vytvoření propojené služby HDInsight
V tomto kroku vytvoříte propojenou službu HDInsight, která propojí cluster HDInsight Spark s datovou továrnou. Cluster HDInsight se používá ke spuštění programu Spark určeného v aktivitě Spark kanálu v této ukázce.

1. V editoru Data Factory vyberte **Další**  >  **nový výpočetní**  >  **cluster HDInsight**.

    ![Vytvoření propojené služby HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Následující fragment kódu zkopírujte a vložte ho do okna Koncept-1. V editoru JSON proveďte následující kroky:

    a. Zadejte identifikátor URI pro cluster HDInsight Spark. Například: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Zadejte jméno uživatele, který má přístup ke clusteru Spark.

    c. Zadejte heslo tohoto uživatele.

    d. Zadejte propojenou službu úložiště, která je přidružená ke clusteru HDInsight Spark. V tomto příkladu je to AzureStorageLinkedService.

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
    > - Aktivita Spark podporuje pouze existující clustery HDInsight Spark. Nepodporuje propojenou službu HDInsight na vyžádání.

    Další informace o propojené službě HDInsight najdete v tématu [propojená služba HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Chcete-li nasadit propojenou službu, vyberte možnost **nasadit** na panelu příkazů.

### <a name="create-the-output-dataset"></a>Vytvoření výstupní datové sady
Výstupní datová sada je to, co určuje plán (každou hodinu, každý den). Proto je nutné zadat výstupní datovou sadu pro aktivitu Spark v kanálu, i když aktivita nevytváří žádný výstup. Zadání vstupní datové sady pro aktivitu je volitelné.

1. V editoru Data Factory vyberte **Další**  >  **Nová datová sada**  >  **úložiště objektů BLOB v Azure**.

1. Následující fragment kódu zkopírujte a vložte ho do okna Koncept-1. Fragment kódu JSON definuje datovou sadu s názvem **OutputDataset**. Kromě toho určujete, že se výsledky ukládají do kontejneru objektů BLOB s názvem **adfspark** a do složky s názvem **pyFiles/Output**. Jak bylo zmíněno dříve, tato datová sada je fiktivní datová sada. Program Spark v tomto příkladu nevytváří žádný výstup. Oddíl **Availability** určuje, že výstupní datová sada je vyprodukována každý den.

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
1. Chcete-li nasadit datovou sadu, vyberte možnost **nasadit** na panelu příkazů.


### <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte kanál s aktivitou HDInsightSpark. V současnosti určuje plán výstupní datová sada, takže musíte výstupní datovou sadu vytvořit i v případě, že aktivita negeneruje žádný výstup. Pokud aktivita nemá žádný vstup, vstupní datovou sadu vytvářet nemusíte. Proto v tomto příkladu není zadána vstupní datová sada.

1. V editoru Data Factory vyberte **Další**  >  **Nový kanál**.

1. Nahraďte skript v okně koncept-1 následujícím skriptem:

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

    a. Vlastnost **Type** je nastavená na **HDInsightSpark**.

    b. Vlastnost **rootPath** je nastavena na hodnotu **adfspark \\ pyFiles** , kde adfspark je kontejner objektů BLOB a pyFiles je složka souboru v tomto kontejneru. V tomto příkladu je BLOB Storage ten, který je přidružený ke clusteru Spark. Soubor můžete nahrát na jiný účet úložiště. Pokud to uděláte, vytvořte propojenou službu úložiště, která tento účet úložiště propojí s datovou továrnou. Pak zadejte název propojené služby jako hodnotu vlastnosti **sparkJobLinkedService** . Další informace o této vlastnosti a dalších vlastnostech podporovaných aktivitou Spark najdete v tématu [vlastnosti aktivity Spark](#spark-activity-properties).

    c. Vlastnost **entryFilePath** je nastavena na **test.py**, což je soubor Python.

    d. Vlastnost **GetDebugInfo –** je nastavena na hodnotu **vždy**, což znamená, že soubory protokolu jsou vždy generovány (úspěch nebo neúspěch).

    > [!IMPORTANT]
    > Pokud neřešíte problém, doporučujeme, abyste tuto vlastnost nestavili `Always` v produkčním prostředí.

    e. Oddíl **výstupy** obsahuje jednu výstupní datovou sadu. Výstupní datovou sadu musíte zadat i v případě, že program Spark nevyprodukuje žádný výstup. Výstupní datová sada nařídí plán pro kanál (každou hodinu, každý den).

    Další informace o vlastnostech podporovaných aktivitou Spark najdete v části [vlastnosti aktivity Spark](#spark-activity-properties).

1. Pokud chcete kanál nasadit, vyberte **nasadit** na panelu příkazů.

### <a name="monitor-a-pipeline"></a>Monitorování kanálu
1. V okně **objekt pro vytváření dat** vyberte **monitor & spravovat** a spusťte tak monitorovací aplikaci na jiné kartě.

    ![Dlaždice Monitorování a správa](media/data-factory-spark/monitor-and-manage-tile.png)

1. Změňte filtr **čas spuštění** v horní části na **2/1/2017**a vyberte **použít**.

1. Zobrazí se pouze jedno okno aktivity, protože mezi začátkem (2017-02-01) a koncovým časem (2017-02-02) kanálu je pouze jeden den. Ověřte, že je datový řez ve stavu **připraveno** .

    ![Monitorování kanálu](media/data-factory-spark/monitor-and-manage-app.png)

1. V seznamu **okna aktivit** vyberte spuštění aktivity, ve kterém se zobrazí podrobnosti. Pokud dojde k chybě, zobrazí se v pravém podokně Podrobnosti.

### <a name="verify-the-results"></a>Ověřit výsledky

1. Spusťte Jupyter Notebook pro cluster HDInsight Spark tak, že na `https://CLUSTERNAME.azurehdinsight.net/jupyter` . Můžete také otevřít řídicí panel clusteru pro cluster HDInsight Spark a pak spustit Jupyter Notebook.

1. Vyberte **Nový**  >  **PySpark** a spusťte nový Poznámkový blok.

    ![Nový Poznámkový blok Jupyter](media/data-factory-spark/jupyter-new-book.png)

1. Spusťte následující příkaz zkopírováním a vložením textu a stisknutím klávesy SHIFT + ENTER na konci druhého příkazu:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Potvrďte, že se zobrazí data z tabulky TVK.

    ![Výsledky dotazu Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Podrobné pokyny najdete v části [spuštění dotazu Spark SQL](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

### <a name="troubleshooting"></a>Řešení potíží
Vzhledem k tomu, že GetDebugInfo – nastavíte na **vždycky**, ve složce pyFiles v kontejneru objektů BLOB se zobrazí podsložka protokolu. Další informace najdete v souboru protokolu ve složce protokolu. Tento soubor protokolu je zvláště užitečný, když dojde k chybě. V produkčním prostředí můžete nastavit, aby **nedošlo k chybě**.

Pro další řešení potíží proveďte následující kroky:


1. Přejděte na `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![Aplikace uživatelského rozhraní PŘÍZ](media/data-factory-spark/yarnui-application.png)

1. Pro jeden z pokusů o spuštění vyberte **protokoly** .

    ![Stránka aplikace](media/data-factory-spark/yarn-applications.png)

1. Na stránce protokolu se zobrazí následující další informace o chybě:

    ![Chyba protokolu](media/data-factory-spark/yarnui-application-error.png)

Následující části obsahují informace o entitách služby Data Factory pro použití clusteru Spark a aktivity Spark ve vaší datové továrně.

## <a name="spark-activity-properties"></a>Vlastnosti aktivity Spark
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

Následující tabulka obsahuje popis vlastností JSON použitých v definici JSON.

| Vlastnost | Popis | Povinné |
| -------- | ----------- | -------- |
| name | Název aktivity v kanálu. | Ano |
| Popis | Text, který popisuje, co aktivita dělá. | Ne |
| typ | Tato vlastnost musí být nastavená na HDInsightSpark. | Ano |
| linkedServiceName | Název propojené služby HDInsight, na které běží program Spark | Ano |
| rootPath | Kontejner objektů BLOB a složka obsahující soubor Spark. V názvu souboru se rozlišují malá a velká písmena. | Ano |
| entryFilePath | Relativní cesta ke kořenové složce kódu nebo balíčku Spark | Ano |
| NázevTřídy | Hlavní třída Java/Spark aplikace | Ne |
| náhodné | Seznam argumentů příkazového řádku pro program Spark. | Ne |
| proxyUser | Uživatelský účet, který se má zosobnit pro spuštění programu Spark. | Ne |
| sparkConfig | Zadejte hodnoty vlastností konfigurace Sparku, které jsou uvedené v části [Konfigurace Sparku: vlastnosti aplikace](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Ne |
| GetDebugInfo – | Určuje, kdy se soubory protokolu Spark zkopírují do úložiště používaného clusterem HDInsight (nebo) určeného pomocí sparkJobLinkedService. Povolené hodnoty jsou None, Always nebo Failure. Výchozí hodnota je žádné. | Ne |
| sparkJobLinkedService | Propojená služba úložiště, která obsahuje soubor úlohy Spark, závislosti a protokoly. Pokud nezadáte hodnotu pro tuto vlastnost, použije se úložiště přidružené ke clusteru HDInsight. | Ne |

## <a name="folder-structure"></a>Struktura složek
Aktivita Spark nepodporuje vložený skript jako aktivity vepřového a podregistru. Úlohy Sparku jsou také více rozšiřitelnější než při úlohách vepřového a podregistru. Pro úlohy Spark můžete zadat několik závislostí, jako jsou například balíčky jar (umístěné v cestě třídy Java), soubory Pythonu (umístěné na PYTHONPATH) a všechny další soubory.

V úložišti objektů blob, na který odkazuje propojená služba HDInsight, vytvořte následující strukturu složek. Pak nahrajte závislé soubory do příslušných podsložek v kořenové složce reprezentované **entryFilePath**. Například nahrajte soubory Pythonu do podsložky pyFiles a soubory jar do podsložky jar kořenové složky. V době běhu Služba Data Factory očekává v úložišti objektů BLOB následující strukturu složek:

| Cesta | Popis | Povinné | Typ |
| ---- | ----------- | -------- | ---- |
| . | Kořenová cesta úlohy Spark v propojené službě úložiště | Ano | Složka |
| &lt;definováno uživatelem &gt; | Cesta, která odkazuje na vstupní soubor úlohy Spark. | Ano | Soubor |
| ./jars | Všechny soubory v této složce se nahrají a umístí do cesty tříd Java clusteru. | Ne | Složka |
| ./pyFiles | Všechny soubory v této složce se nahrají a umístí do PYTHONPATH clusteru. | Ne | Složka |
| ./files | Všechny soubory v této složce se nahrají a umístí do pracovního adresáře prováděcího modulu. | Ne | Složka |
| ./archives | Všechny soubory v této složce jsou nekomprimovány. | Ne | Složka |
| ./logs | Složka, ve které jsou uloženy protokoly z clusteru Spark.| Ne | Složka |

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
