---
title: Vyvolání programů Spark ze služby Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak pomocí aktivity MapReduce vyvolání programů Spark ze služby Azure data factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: ''
editor: ''
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 95c49eec6964984894f75ecd0a9e50c9c947683b
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54015810"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Vyvolání programů Spark ze služby Azure Data Factory kanálů

> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Aktivita hivu](data-factory-hive-activity.md)
> * [Aktivita pig](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Aktivita streamování Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktivita Spark](data-factory-spark.md)
> * [Aktivita provedení dávky služby Learning počítače](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning aktualizace prostředku aktivity](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [Vlastní aktivita .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Azure Data Factory, která je všeobecně dostupná. Pokud používáte aktuální verzi služby Data Factory, přečtěte si [transformovat data pomocí aktivity Apache Spark ve službě Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Úvod
Aktivita Spark je jedním z [aktivity transformace dat](data-factory-data-transformation-activities.md) podporovaných službou Data Factory. Tato aktivita spouští zadaný program Sparku na svém clusteru Spark v Azure HDInsight. 

> [!IMPORTANT]
> - Aktivita Spark nepodporuje clustery HDInsight Spark, používající Azure Data Lake Store jako primární úložiště.
> - Aktivity Spark se podporuje jenom současní (vlastní) clustery HDInsight Spark. HDInsight propojené služby na vyžádání nepodporuje.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Průvodce: Vytvoření kanálu s aktivitou Sparku
Tady jsou obvyklé kroky k vytvoření kanálu datové továrny pomocí aktivity Sparku: 

* Vytvoření datové továrny
* Vytvoření služby Azure Storage, propojené k propojení vašeho úložiště, který je spojen s vaším clusterem HDInsight Spark s datovou továrnou.
* Vytvoření HDInsight propojenou službu, která propojí váš cluster Spark v HDInsight s datovou továrnou.
* Vytvoření datové sady, který odkazuje na propojenou službu Storage. V současné době je nutné zadat výstupní datovou sadu pro aktivitu i v případě, že neexistuje žádný výstup vytvořených. 
* Vytvoření kanálu s aktivitou Sparku, která odkazuje na HDInsight propojenou službu, kterou jste vytvořili. Aktivita je nakonfigurovaný s datovou sadou, kterou jste vytvořili v předchozím kroku jako výstupní datovou sadu. Výstupní datová sada určuje plánu (každou hodinu, každý den). Proto je nutné zadat výstupní datovou sadu, i v případě, že aktivita negeneruje skutečně výstup.

### <a name="prerequisites"></a>Požadavky
1. Vytvoření účtu úložiště pro obecné účely podle pokynů v [vytvořit účet úložiště](../../storage/common/storage-quickstart-create-account.md).

1. Vytvoření clusteru Spark v HDInsight, postupujte podle pokynů v tomto kurzu [vytvoření clusteru Spark v HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Přidružíte k účtu úložiště, který jste vytvořili v kroku 1 tohoto clusteru.

1. Stáhnout a projít na soubor skriptu Pythonu **test.py** umístění [ https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py ](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py).

1. Nahrát **test.py** k **pyFiles** složky **adfspark** kontejneru v úložišti objektů blob. Pokud ještě neexistují, vytvořte kontejner a složku.

### <a name="create-a-data-factory"></a>Vytvoření datové továrny
Pokud chcete vytvořit datovou továrnu, postupujte následovně:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Vyberte **Nový** > **Data a analýzy** > **Datová továrna**.

1. Na **nová datová továrna** okně v části **název**, zadejte **SparkDF**.

   > [!IMPORTANT]
   > Název objektu pro vytváření dat Azure musí být globálně jedinečný. Pokud se zobrazí chyba "název objektu pro vytváření dat SparkDF není k dispozici", změňte název datové továrny. Například použijte yournameSparkDFdate a znovu vytvořte datovou továrnu. Další informace o pravidlech pojmenování najdete v tématu [služby Data Factory: Pravidla pojmenování](data-factory-naming-rules.md).

1. V části **Předplatné** vyberte předplatné Azure, ve kterém chcete datovou továrnu vytvořit.

1. Vyberte existující skupinu prostředků nebo vytvořte skupinu prostředků Azure.

1. Zaškrtněte políčko **Připnout na řídicí panel**.

1. Vyberte **Vytvořit**.

   > [!IMPORTANT]
   > Pokud chcete vytvářet instance služby Data Factory, musíte být členem role [Přispěvatel Data Factory](../../role-based-access-control/built-in-roles.md#data-factory-contributor) na úrovni předplatného nebo skupiny prostředků.

1. Objekt pro vytváření dat se zobrazí, jakmile je vytvořena na řídicím panelu na webu Azure portal.

1. Po vytvoření datové továrny se zobrazí stránka **Datová továrna** s obsahem datové továrny. Pokud se nezobrazí **služby Data factory** stránky, vyberte dlaždici pro svou datovou továrnu na řídicím panelu.

    ![Okno Objekt pro vytváření dat](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Vytvoření propojených služeb
V tomto kroku vytvoříte dvě propojené služby. Jedna služba odkazuje váš cluster Spark na svou datovou továrnu a další služba propojuje úložiště do služby data factory. 

#### <a name="create-a-storage-linked-service"></a>Vytvoření propojené služby Storage
V tomto kroku s datovou továrnou propojíte svůj účet úložiště. Datovou sadu, kterou jste vytvořili v kroku později v tomto názorném postupu odkazuje na tuto propojenou službu. HDInsight propojené služby, které definujete v dalším kroku odkazuje na tuto propojenou službu příliš. 

1. Na **služby Data factory** okno, vyberte **vytvořit a nasadit**. Zobrazí se Editor služby Data Factory.

1. Vyberte **Nové úložiště dat** a zvolte **Azure Storage**.

   ![Nové datové úložiště](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. Skript JSON, který použijete k vytvoření úložiště, propojené služby se zobrazí v editoru.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Nahraďte **název účtu** a **klíč účtu** s názvem a přístupový klíč účtu úložiště. Pokud chcete zjistit, jak získat přístupový klíč k úložišti, přečtěte si, jak zobrazit, kopírovat a znovu vygenerovat přístupové klíče k úložišti, v tématu [Správa účtu úložiště](../../storage/common/storage-account-manage.md#access-keys).

1. Pokud chcete nasadit tuto propojenou službu, vyberte **nasadit** na panelu příkazů. Po úspěšném nasazení propojené služby okno Koncept-1 zmizí. Ve stromovém zobrazení na levé straně se zobrazí **AzureStorageLinkedService**.

#### <a name="create-an-hdinsight-linked-service"></a>Vytvoření propojené služby HDInsight
V tomto kroku vytvoříte HDInsight propojenou službu, která propojí váš cluster HDInsight Spark s datovou továrnou. HDInsight cluster se používá k spustí program Sparku určeného v aktivitě Spark kanál v této ukázce. 

1. V editoru služby Data Factory, vyberte **Další** > **nový výpočet** > **clusteru HDInsight**.

    ![Vytvoření propojené služby HDInsight](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Následující fragment kódu zkopírujte a vložte ho do okna Koncept-1. V editoru JSON proveďte následující kroky:

    a. Zadejte identifikátor URI pro cluster HDInsight Spark. Například: `https://<sparkclustername>.azurehdinsight.net/`.

    b. Zadejte jméno uživatele, který má přístup ke clusteru Spark.

    c. Zadejte heslo pro daného uživatele.

    d. Zadejte na propojenou službu Storage, který je spojen s clusterem HDInsight Spark. V tomto příkladu je AzureStorageLinkedService.

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
    > - Aktivita Spark nepodporuje clustery HDInsight Spark, používající Azure Data Lake Store jako primární úložiště.
    > - Aktivity Spark se podporuje jenom současní (vlastní) clustery HDInsight Spark. HDInsight propojené služby na vyžádání nepodporuje.

    Další informace o HDInsight propojené služby, najdete v části [propojená služba HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Pokud chcete nasadit tuto propojenou službu, vyberte **nasadit** na panelu příkazů. 

### <a name="create-the-output-dataset"></a>Vytvoření výstupní datové sady
Výstupní datová sada určuje plánu (každou hodinu, každý den). Proto je nutné zadat výstupní datovou sadu pro aktivitu Spark v kanálu, i v případě, že aktivita negeneruje žádný výstup. Určení vstupní datovou sadu pro aktivitu je volitelné.

1. V editoru služby Data Factory vyberte **Další** > **Nová datová sada** > **Úložiště objektů blob v Azure**.

1. Následující fragment kódu zkopírujte a vložte ho do okna Koncept-1. Fragment kódu JSON definuje datovou sadu s názvem **OutputDataset**. Kromě toho můžete zadat, že mají výsledky ukládat do kontejneru objektů blob s názvem **adfspark** a ve složce s názvem **pyFiles/output**. Jak už bylo zmíněno dříve, tato datová sada je fiktivní datovou sadu. Program Sparku v tomto příkladu negeneruje žádný výstup. **Dostupnosti** část určuje, že výstupní datová sada vytváří každý den. 

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
1. Pokud chcete nasadit datovou sadu, vyberte **nasadit** na panelu příkazů.


### <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte kanál s aktivitou HDInsightSpark. V současnosti určuje plán výstupní datová sada, takže musíte výstupní datovou sadu vytvořit i v případě, že aktivita negeneruje žádný výstup. Pokud aktivita nemá žádný vstup, vstupní datovou sadu vytvářet nemusíte. Proto žádná vstupní datová sada je zadat v tomto příkladu.

1. V editoru služby Data Factory vyberte **Další** > **Nový kanál**.

1. Skript do okna koncept-1 nahraďte následující skript:

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

    b. **RootPath** je nastavena na **adfspark\\pyFiles** kde adfspark je kontejner objektů blob a pyFiles je složka, soubor v tomto kontejneru. Úložiště objektů blob v tomto příkladu je ten, který je spojen s clusterem Spark. Nahrajte soubor do jiného účtu úložiště. Pokud tak učiníte, vytvoření propojené služby Storage k propojení účtu úložiště do služby data factory. Zadejte název propojené služby, jako hodnotu **sparkJobLinkedService** vlastnost. Další informace o této vlastnosti a dalších vlastností podporovaných aktivitou Sparku najdete v tématu [vlastnosti aktivity Spark](#spark-activity-properties).

    c. **EntryFilePath** je nastavena na **test.py**, což je soubor Pythonu.

    d. **GetDebugInfo** je nastavena na **vždy**, což znamená, že soubory protokolu budou vždy generována (úspěch nebo neúspěch).

    > [!IMPORTANT]
    > Doporučujeme, abyste nenastavujte tuto vlastnost na `Always` v produkčním prostředí Pokud řešíte problém.

    e. **Výstupy** oddíl má jednu výstupní datovou sadu. Výstupní datové sady je nutné zadat i v případě, že program Sparku negeneruje žádný výstup. Výstupní datovou sadu řídí plán kanálu (každou hodinu, každý den). 

    Další informace o vlastnostech podporovaných aktivitou Sparku, najdete v části [vlastnosti aktivity Spark](#spark-activity-properties).

1. Pokud chcete nasadit kanálu, vyberte **nasadit** na panelu příkazů.

### <a name="monitor-a-pipeline"></a>Monitorování kanálu
1. Na **služby Data factory** okně vyberte **monitorování a správa** spustit monitorování aplikací na jiné kartě.

    ![Dlaždice Monitorování a správa](media/data-factory-spark/monitor-and-manage-tile.png)

1. Změnit **počáteční čas** filtru v horní části **2/1. června 2017**a vyberte **použít**.

1. Vzhledem k tomu, že existuje pouze jeden den mezi počátečním (2017-02-01) a koncovým časem (2017-02-02) kanálu, zobrazí se okno pouze jednu aktivitu. Potvrďte, že datový řez je v **připravené** stavu.

    ![Monitorování kanálu](media/data-factory-spark/monitor-and-manage-app.png)

1. V **okna aktivit** vyberte spuštění aktivit a podrobnostmi o řezu. Pokud dojde k chybě, zobrazí podrobnosti o něm v pravém podokně.

### <a name="verify-the-results"></a>Zkontrolujte výsledky

1. Začneme přejitím do poznámkového bloku Jupyter pro váš cluster HDInsight Spark [tento web](https://CLUSTERNAME.azurehdinsight.net/jupyter). Také můžete otevřít řídicí panel clusteru HDInsight Spark clusteru, a pak spusťte Poznámkový blok Jupyter.

1. Vyberte **nový** > **PySpark** spustit nový poznámkový blok.

    ![Nový poznámkový blok Jupyter](media/data-factory-spark/jupyter-new-book.png)

1. Kopírování a vkládání text a stisknutím klávesy Shift + Enter na konci druhý příkaz spusťte následující příkaz:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Ověřte, že se data z tabulky hvac. 

    ![Výsledky dotazu v Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article --> Podrobné pokyny najdete v části [spuštění dotazu Spark SQL](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). 

### <a name="troubleshooting"></a>Řešení potíží
Vzhledem k tomu, že nastavíte getDebugInfo **vždy**, najdete v protokolu podsložky ve složce pyFiles v kontejnerech objektů blob. Další informace najdete v souboru protokolu ve složce protokolů. Tento soubor protokolu je zvlášť užitečné, když dojde k chybě. V produkčním prostředí, můžete ji nastavit na hodnotu **selhání**.

Řešení potíží, proveďte následující kroky:


1. Přejděte do části `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster` (Soubor > Nový > Jiné).

    ![Aplikace v uživatelském rozhraní YARN](media/data-factory-spark/yarnui-application.png)

1. Vyberte **protokoly** pro jedno spuštění pokusí.

    ![Stránka aplikace](media/data-factory-spark/yarn-applications.png)

1. Uvidíte následující další chybové informace na stránce protokol:

    ![Chyba protokolu](media/data-factory-spark/yarnui-application-error.png)

Následující části obsahují informace o entit datové továrny použití clusteru Spark a aktivity Sparku ve službě data factory.

## <a name="spark-activity-properties"></a>Vlastnosti aktivity Spark
Tady je ukázková definice JSON kanálu s aktivitou Sparku: 

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
| linkedServiceName | Název HDInsight propojené služby, na kterém se spustí program Sparku. | Ano |
| rootPath | Kontejner objektů blob a složku obsahující soubor Spark. Název souboru je velká a malá písmena. | Ano |
| entryFilePath | Relativní cesta ke kořenové složce kódu nebo balíčku, Spark. | Ano |
| Název třídy | Hlavní třída Java/Spark vaší aplikace. | Ne |
| argumenty | Seznam argumentů příkazového řádku pro program Sparku. | Ne |
| proxyUser | Uživatelský účet zosobnění spuštění programu Sparku. | Ne |
| sparkConfig | Zadejte hodnoty pro vlastnosti konfigurace Spark uvedené v [Spark konfigurace: Vlastnosti aplikace](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Ne |
| getDebugInfo | Určuje, kdy se soubory protokolu Spark zkopírují do úložiště používá HDInsight cluster (nebo) je uvedli v sparkJobLinkedService. Povolené hodnoty jsou None, vždy nebo selhání. Výchozí hodnota je None. | Ne |
| sparkJobLinkedService | Úložiště propojenou službu, která obsahuje Spark soubor úlohy, závislosti a protokoly. Pokud hodnotu pro tuto vlastnost nezadáte, použije se úložiště přidružené ke clusteru HDInsight. | Ne |

## <a name="folder-structure"></a>struktura složek
Aktivita Spark nepodporuje vložený skript jako Pig a proveďte aktivity Hive. Rozšiřitelné více než Pigu a Hivu úlohy jsou také Sparkových úloh. Pro úlohy Spark, můžete zadat více závislostí, jako jar balíčky (umístěné v jazyce java cesta k TŘÍDĚ), soubory Pythonu (umístěné PYTHONPATH) a další soubory.

Vytvořte následující strukturu složky v úložišti objektů blob odkazuje HDInsight propojené služby. Nahrajte závislé soubory odpovídající podsložek v kořenové složce reprezentována **entryFilePath**. Například nahrát soubory Pythonu do podsložky pyFiles a jar soubory do požadované podsložky kromě souborů JAR kořenové složky. V době běhu služba Data Factory očekává, že následující strukturu složek ve službě blob storage: 

| Cesta | Popis | Požaduje se | Typ |
| ---- | ----------- | -------- | ---- |
| . | Kořenová cesta úlohy Sparku na propojenou službu storage. | Ano | Složka |
| &lt;definováno uživatelem &gt; | Cesta, která odkazuje na vstupní soubor úlohy Spark. | Ano | File |
| . / jars | Všechny soubory v této složce jsou odeslat a umístí na cesta třídy Java clusteru. | Ne | Složka |
| . / pyFiles | Všechny soubory v této složce jsou nahraje a umístí na PYTHONPATH clusteru. | Ne | Složka |
| . / soubory | Všechny soubory v této složce jsou odeslány a umístit do pracovního adresáře prováděcího modulu. | Ne | Složka |
| . / archivů | Všechny soubory v této složce nekomprimované. | Ne | Složka |
| . / protokoly | Složka, ve kterém jsou uložené protokoly z clusteru Spark.| Ne | Složka |

Tady je příklad pro úložiště, který obsahuje dva soubory úlohy Spark ve službě blob storage odkazuje HDInsight propojené služby:

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
