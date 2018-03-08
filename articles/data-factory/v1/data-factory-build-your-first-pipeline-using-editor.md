---
title: "Sestavení prvního objektu pro vytváření dat (Azure Portal) | Dokumentace Microsoftu"
description: "V tomto kurzu vytvoříte pomocí editoru služby Data Factory na webu Azure Portal ukázkový kanál služby Azure Data Factory."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: 
editor: 
ms.assetid: d5b14e9e-e358-45be-943c-5297435d402d
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: c4fe0e01936ebc131b10f011b98e9d0c1782179b
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
---
# <a name="tutorial-build-your-first-data-factory-by-using-the-azure-portal"></a>Kurz: Vytvoření první datové továrny pomocí webu Azure Portal
> [!div class="op_single_selector"]
> * [Přehled a požadavky](data-factory-build-your-first-pipeline.md)
> * [Azure Portal](data-factory-build-your-first-pipeline-using-editor.md)
> * [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
> * [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
> * [Šablona Azure Resource Manageru](data-factory-build-your-first-pipeline-using-arm.md)
> * [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)


> [!NOTE]
> Tento článek se týká verze 1 služby Azure Data Factory, která je všeobecně dostupná. Pokud používáte verzi 2 služby Data Factory, která je ve verzi Preview, přečtěte si [Rychlý start: Vytvoření datové továrny pomocí služby Data Factory verze 2](../quickstart-create-data-factory-dot-net.md).

V tomto článku se dozvíte, jak pomocí webu [Azure Portal](https://portal.azure.com/) vytvořit první datovou továrnu. Pokud chcete kurz absolvovat s použitím jiných nástrojů nebo sad SDK, vyberte některou z možností z rozevíracího seznamu. 

Kanál v tomto kurzu obsahuje jednu aktivitu: aktivitu Azure HDInsight Hive. Tato aktivita spouští v clusteru Azure HDInsight skript Hive, který transformuje vstupní data a generuje výstupní data. Spuštění kanálu je naplánované jednou za měsíc mezi zadaným počátečním a koncovým časem. 

> [!NOTE]
> Datový kanál v tomto kurzu transformuje vstupní data, aby vytvořil výstupní data. Kurz předvádějící způsoby kopírování dat pomocí služby Data Factory najdete v tématu [Kurz: Kopírování dat z úložiště objektů blob v Azure do služby Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Kanál může obsahovat víc než jednu aktivitu. A dvě aktivity můžete zřetězit (spustit jednu aktivitu po druhé) nastavením výstupní datové sady jedné aktivity jako vstupní datové sady druhé aktivity. Další informace najdete v tématu [Plánování a spouštění ve službě Data Factory](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

## <a name="prerequisites"></a>Požadavky
Přečtěte si [Přehled kurzu](data-factory-build-your-first-pipeline.md) a proveďte kroky v části Požadavky.

Tento článek neposkytuje koncepční přehled služby Data Factory. Další informace o této službě najdete v tématu [Úvod do služby Azure Data Factory](data-factory-introduction.md).  

## <a name="create-a-data-factory"></a>Vytvoření datové továrny
Objekt pro vytváření dat může mít jeden nebo víc kanálů. Kanál může obsahovat jednu nebo víc aktivit. Příkladem je aktivita kopírování, která kopíruje data ze zdrojového do cílového úložiště dat. Dalším příkladem je aktivita HDInsight Hive spouštějící skript Hive, který transformuje vstupní data a generuje výstupní data. 

Pokud chcete vytvořit datovou továrnu, postupujte následovně:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

2. Vyberte **Nový** > **Data a analýzy** > **Datová továrna**.

   ![Okno Vytvořit](./media/data-factory-build-your-first-pipeline-using-editor/create-blade.png)

3. V okně **Nová datová továrna** v části **Název** zadejte **GetStartedDF**.

   ![Okno Nový objekt pro vytváření dat](./media/data-factory-build-your-first-pipeline-using-editor/new-data-factory-blade.png)

   > [!IMPORTANT]
   > Název datové továrny musí být globálně jedinečný. Pokud se zobrazí chyba Název datové továrny GetStartedDF není k dispozici, změňte název datové továrny. Použijte například váš_název_GetStartedDF a vytvořte datovou továrnu znovu. Další informace o pravidlech pojmenování najdete v tématu [Data Factory: Pravidla pojmenování](data-factory-naming-rules.md).
   >
   > Název datové továrny se může v budoucnu zaregistrovat jako název DNS a tak se stát veřejně viditelným.
   >
   >
4. V části **Předplatné** vyberte předplatné Azure, ve kterém chcete datovou továrnu vytvořit.

5. Vyberte existující skupinu prostředků nebo vytvořte novou. Pro účely tohoto kurzu vytvořte skupinu prostředků **ADFGetStartedRG**.

6. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu jsou uvedené pouze oblasti podporované službou Data Factory.

7. Zaškrtněte políčko **Připnout na řídicí panel**.

8. Vyberte **Vytvořit**.

   > [!IMPORTANT]
   > Pokud chcete vytvářet instance služby Data Factory, musíte být členem role [Přispěvatel Data Factory](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) na úrovni předplatného nebo skupiny prostředků.
   >
   >
9. Na řídicím panelu se zobrazí následující dlaždice se stavem **Nasazování datové továrny**:    

   ![Stav nasazování datové továrny](./media/data-factory-build-your-first-pipeline-using-editor/creating-data-factory-image.png)

10. Po vytvoření datové továrny se zobrazí stránka **Datová továrna** s obsahem datové továrny.     

    ![Okno Datová továrna](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-blade.png)

Před vytvořením kanálu v datové továrně je nejprve potřeba vytvořit několik entit datové továrny. Nejprve vytvoříte propojené služby, které propojí úložiště dat a výpočetní služby s úložištěm dat. Pak nadefinujte vstupní a výstupní datovou sadu, které budou reprezentovat vstupní a výstupní data v propojených úložištích dat. Nakonec vytvořte kanál s aktivitou, která používá tyto datové sady.

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V tomto kroku s datovou továrnou propojíte svůj účet služby Azure Storage a cluster HDInsight na vyžádání. Účet úložiště v této ukázce obsahuje vstupní a výstupní data pro kanál. Propojená služba HDInsight slouží v této ukázce ke spuštění skriptu Hive určeného v aktivitě kanálu. Určete, jaké [úložiště dat](data-factory-data-movement-activities.md) / [výpočetní služby](data-factory-compute-linked-services.md) se používají ve vašem scénáři. Pak tyto služby propojte s datovou továrnou vytvořením propojených služeb.  

### <a name="create-a-storage-linked-service"></a>Vytvoření propojené služby Storage
V tomto kroku s datovou továrnou propojíte svůj účet úložiště. V tomto kurzu použijete k uložení vstupních a výstupních dat a souboru skriptu HQL stejný účet úložiště.

1. V okně **Datová továrna** pro **GetStartedDF** vyberte **Vytvořit a nasadit**. Zobrazí se editor služby Data Factory.

   ![Dlaždice Vytvořit a nasadit](./media/data-factory-build-your-first-pipeline-using-editor/data-factory-author-deploy.png)

2. Vyberte **Nové úložiště dat** a zvolte **Azure Storage**.

   ![Okno Nové úložiště dat](./media/data-factory-build-your-first-pipeline-using-editor/new-data-store-azure-storage-menu.png)

3. V editoru se zobrazí skript JSON pro vytvoření propojené služby Storage.

   ![Propojená služba Storage](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

4. Nahraďte **název účtu** názvem vašeho účtu úložiště. Nahraďte **klíč účtu** přístupovým klíčem účtu úložiště. Pokud chcete zjistit, jak získat přístupový klíč k úložišti, přečtěte si, jak zobrazit, kopírovat a znovu vygenerovat přístupové klíče k úložišti, v tématu [Správa účtu úložiště](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

5. Vyberte **Nasadit** na panelu příkazů a nasaďte propojenou službu.

    ![Tlačítko Nasadit](./media/data-factory-build-your-first-pipeline-using-editor/deploy-button.png)

   Po úspěšném nasazení propojené služby okno Koncept-1 zmizí. Ve stromovém zobrazení na levé straně se zobrazí **AzureStorageLinkedService**.

    ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/StorageLinkedServiceInTree.png)    

### <a name="create-an-hdinsight-linked-service"></a>Vytvoření propojené služby HDInsight
V tomto kroku propojíte se svým objektem pro vytváření dat cluster HDInsight na vyžádání. Cluster HDInsight se vytváří automaticky za běhu. Až dokončí zpracování, po určité zadané době nečinnosti se odstraní.

1. V editoru služby Data Factory vyberte **Další** > **Nové výpočetní prostředí** > **Cluster HDInsight na vyžádání**.

    ![Nový výpočet](./media/data-factory-build-your-first-pipeline-using-editor/new-compute-menu.png)

2. Následující fragment kódu zkopírujte a vložte ho do okna Koncept-1. Tento fragment kódu JSON popisuje vlastnosti, které se použijí při vytváření clusteru HDInsight na vyžádání.

    ```JSON
    {
        "name": "HDInsightOnDemandLinkedService",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    Následující tabulka obsahuje popis vlastností použitých v tomto fragmentu kódu JSON.

   | Vlastnost | Popis |
   |:--- |:--- |
   | clusterSize |Určuje velikost clusteru HDInsight. |
   | timeToLive | Určuje dobu nečinnosti před odstraněním clusteru HDInsight. |
   | linkedServiceName | Určuje účet úložiště, který se má použít k ukládání protokolů generovaných clusterem HDInsight. |

    Je třeba počítat s následujícím:

     a. Pomocí těchto vlastností JSON vytvoří datová továrna cluster HDInsight s Linuxem za vás. Další informace najdete v tématu [Propojená služba HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

     b. Místo clusteru HDInsight na vyžádání můžete použít také vlastní cluster HDInsight. Další informace najdete v tématu [Propojená služba HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

     c. Cluster HDInsight vytvoří výchozí kontejner v úložišti objektů blob, které jste zadali ve vlastnosti JSON (**linkedServiceName**). Při odstranění clusteru HDInsight neprovede odstranění tohoto kontejneru. Toto chování je záměrné. Díky propojené službě HDInsight na vyžádání se cluster HDInsight vytvoří pokaždé, když se zpracuje určitý řez, pokud neexistuje aktivní cluster (**timeToLive**). Po dokončení zpracování se cluster automaticky odstraní.

     Po zpracování dalších řezů se v úložišti objektů blob zobrazí spousta kontejnerů. Pokud je nepotřebujete k řešení potíží s úlohami, můžete je odstranit, abyste snížili náklady na úložiště. Názvy těchto kontejnerů používají následující formát: adf**název_vaší_datové_továrny**-**název_propojené_služby**-razítko_data_a_času. K odstranění kontejnerů z úložiště objektů blob můžete použít nástroje, jako je například [Průzkumník služby Azure Storage](http://storageexplorer.com/).

     Další informace najdete v tématu [Propojená služba HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

3. Vyberte **Nasadit** na panelu příkazů a nasaďte propojenou službu.

    ![Možnost Nasadit](./media/data-factory-build-your-first-pipeline-using-editor/ondemand-hdinsight-deploy.png)

4. Zkontrolujte, jestli se v zobrazení stromu vlevo zobrazuje služba **AzureStorageLinkedService** i služba **HDInsightOnDemandLinkedService**.

    ![Zobrazení stromu s propojenými službami](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-linked-services.png)

## <a name="create-datasets"></a>Vytvoření datových sad
V tomto kroku vytvoříte datové sady, které představují vstupní a výstupní data pro zpracování Hive. Tyto datové sady odkazují na službu AzureStorageLinkedService, kterou jste vytvořili v dřívější části tohoto kurzu. Propojená služba odkazuje na účet úložiště. Datové sady určují kontejner, složku a název souboru v úložišti, který obsahuje vstupní a výstupní data.   

### <a name="create-the-input-dataset"></a>Vytvoření vstupní datové sady
1. V editoru služby Data Factory vyberte **Další** > **Nová datová sada** > **Úložiště objektů blob v Azure**.

    ![Nová datová sada](./media/data-factory-build-your-first-pipeline-using-editor/new-data-set.png)

2. Následující fragment kódu zkopírujte a vložte ho do okna Koncept-1. V tomto fragmentu kódu JSON vytvoříte datovou sadu **AzureBlobInput**, která představuje vstupní data pro aktivitu v kanálu. Kromě toho určíte, že se vstupní data nacházejí v kontejneru objektů blob **adfgetstarted** ve složce **inputdata**.

    ```JSON
    {
        "name": "AzureBlobInput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "input.log",
                "folderPath": "adfgetstarted/inputdata",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Month",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }
    ```
    Následující tabulka obsahuje popis vlastností použitých v tomto fragmentu kódu JSON.

   | Vlastnost | Popis |
   |:--- |:--- |
   | type |Vlastnost type je nastavená na hodnotu **AzureBlob**, protože se data nacházejí v úložišti objektů blob. |
   | linkedServiceName |Odkazuje na službu AzureStorageLinkedService, kterou jste vytvořili dříve. |
   | folderPath | Určuje kontejner objektů blob a složku obsahující vstupní objekty blob. | 
   | fileName |Tato vlastnost je nepovinná. Pokud tuto vlastnost vynecháte, vyberou se všechny soubory v cestě folderPath. V tomto kurzu se zpracovává jenom soubor input.log. |
   | type |Soubory protokolů jsou v textovém formátu, proto použijte hodnotu **TextFormat**. |
   | columnDelimiter |Sloupce v souborech protokolů jsou oddělené znakem čárky (`,`). |
   | frequency/interval |Frekvence je nastavená na hodnotu **Month** (Měsíc) a interval je **1**, takže vstupní řezy jsou dostupné jednou za měsíc. |
   | external | Pokud vstupní data negeneruje tento kanál, je tato vlastnost nastavená na hodnotu **true**. V tomto kurzu se soubor input.log pomocí tohoto kanálu negeneruje, takže je tato vlastnost nastavená na hodnotu **true**. |

    Další informace o těchto vlastnostech JSON najdete v tématu [Konektor Azure Blob](data-factory-azure-blob-connector.md#dataset-properties).

3. Vyberte **Nasadit** na panelu příkazů a nasaďte nově vytvořenou datovou sadu. Datová sada se zobrazí ve stromovém zobrazení na levé straně.

### <a name="create-the-output-dataset"></a>Vytvoření výstupní datové sady
Nyní vytvoříte výstupní datovou sadu, která bude představovat výstupní data uložená v úložišti objektů blob.

1. V editoru služby Data Factory vyberte **Další** > **Nová datová sada** > **Úložiště objektů blob v Azure**.

2. Následující fragment kódu zkopírujte a vložte ho do okna Koncept-1. V tomto fragmentu kódu JSON vytvoříte datovou sadu **AzureBlobOutput**, která určí strukturu dat generovaných skriptem Hive. Určíte také, že se výsledky budou ukládat do kontejneru objektů blob **adfgetstarted** do složky **partitioneddata**. Část **availability** určuje, že se výstupní sada generuje jednou měsíčně.

    ```JSON
    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adfgetstarted/partitioneddata",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Month",
          "interval": 1
        }
      }
    }
    ```
    Popisy těchto vlastností najdete v části Vytvoření vstupní datové sady. U výstupní datové sady nenajdete vlastnost external, protože tuto datovou sadu generuje služba Data Factory.

3. Vyberte **Nasadit** na panelu příkazů a nasaďte nově vytvořenou datovou sadu.

4. Zkontrolujte, jestli se datová sada úspěšně vytvořila.

    ![Zobrazení stromu s propojenými službami](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-data-set.png)

## <a name="create-a-pipeline"></a>Vytvoření kanálu
V tomto kroku vytvoříte svůj první kanál s aktivitou HDInsight Hive. Vstupní řez je k dispozici jednou měsíčně (frekvence je měsíc a interval je 1). Výstupní řez se generuje jednou měsíčně. Vlastnost scheduler pro aktivitu je také nastavená na monthly (měsíčně). Nastavení výstupní datové sady a vlastnosti scheduler se musí shodovat. V současnosti určuje plán výstupní datová sada, takže musíte výstupní datovou sadu vytvořit i v případě, že aktivita negeneruje žádný výstup. Pokud aktivita nemá žádný vstup, vstupní datovou sadu vytvářet nemusíte. Vysvětlení vlastností použitých v následujícím fragmentu kódu JSON najdete na konci této části.

1. V editoru služby Data Factory vyberte **Další** > **Nový kanál**.

    ![Možnost Nový kanál](./media/data-factory-build-your-first-pipeline-using-editor/new-pipeline-button.png)

2. Následující fragment kódu zkopírujte a vložte ho do okna Koncept-1.

   > [!IMPORTANT]
   > Ve fragmentu kódu JSON nahraďte **storageaccountname** názvem vašeho účtu úložiště.
   >
   >

    ```JSON
    {
        "name": "MyFirstPipeline",
        "properties": {
            "description": "My first Azure Data Factory pipeline",
            "activities": [
                {
                    "type": "HDInsightHive",
                    "typeProperties": {
                        "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                        "scriptLinkedService": "AzureStorageLinkedService",
                        "defines": {
                            "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                            "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AzureBlobInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "policy": {
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "name": "RunSampleHiveActivity",
                    "linkedServiceName": "HDInsightOnDemandLinkedService"
                }
            ],
            "start": "2017-07-01T00:00:00Z",
            "end": "2017-07-02T00:00:00Z",
            "isPaused": false
        }
    }
    ```

    V tomto fragmentu kódu JSON vytvoříte kanál sestávající z jediné aktivity, která pomocí Hive zpracovává data v clusteru HDInsight.

    Soubor skriptu Hive **partitionweblogs.hql** je uložený v účtu úložiště, který určuje služba scriptLinkedService s názvem **AzureStorageLinkedService1**. Najdete ho ve složce **script** v kontejneru **adfgetstarted**.

    Část **defines** slouží k určení nastavení modulu runtime, která se předají skriptu Hive jako hodnoty konfigurace Hive. Příkladem jsou ${hiveconf:inputtable} a ${hiveconf:partitionedtable}.

    Vlastnosti kanálu **start** a **end** určují období aktivity kanálu.

    V kódu JSON aktivity určujete, že má skript Hive běžet ve výpočetní službě určené vlastností **linkedServiceName**: **HDInsightOnDemandLinkedService**.

   > [!NOTE]
   > Další informace o vlastnostech JSON použitých v příkladu najdete v části JSON kanálu v tématu [Kanály a aktivity ve službě Data Factory](data-factory-create-pipelines.md).
   >
   >
3. Zkontrolujte:

   a. Jestli ve složce **inputdata** v kontejneru **adfgetstarted** v úložišti objektů blob existuje soubor **input.log**.

   b. Jestli ve složce **script** v kontejneru **adfgetstarted** v úložišti objektů blob existuje soubor **partitionweblogs.hql**. Pokud tyto soubory nevidíte, proveďte kroky v části Požadavky v tématu [Přehled kurzu](data-factory-build-your-first-pipeline.md).

   c. Jestli jste položku **storageaccountname** v kódu JSON kanálu nahradili názvem svého účtu úložiště.

4. Vyberte **Nasadit** na panelu příkazů a nasaďte kanál. Vzhledem k tomu, že časy **start** a **end** jsou nastavené na minulost a vlastnost **isPaused** je nastavená na **false**, kanál (aktivita v kanálu) se spustí ihned po nasazení.

5. Zkontrolujte, jestli se kanál objevil v zobrazení stromu.

    ![Zobrazení stromu s kanálem](./media/data-factory-build-your-first-pipeline-using-editor/tree-view-pipeline.png)



## <a name="monitor-a-pipeline"></a>Monitorování kanálu
### <a name="monitor-a-pipeline-by-using-the-diagram-view"></a>Monitorování kanálu s využitím zobrazení diagramu
1. V okně **Datová továrna** vyberte **Diagram**.

    ![Dlaždice Diagram](./media/data-factory-build-your-first-pipeline-using-editor/diagram-tile.png)

2. V zobrazení **Diagram** uvidíte přehled kanálů a datových sad použitých v tomto kurzu.

    ![Zobrazení diagramu](./media/data-factory-build-your-first-pipeline-using-editor/diagram-view-2.png)

3. Pokud chcete zobrazit všechny aktivity v kanálu, klikněte na kanál v diagramu pravým tlačítkem a vyberte **Otevřít kanál**.

    ![Nabídka Otevřít kanál](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-menu.png)

4. Zkontrolujte, jestli se v kanálu zobrazí **Aktivita Hive**.

    ![Zobrazení Otevřít kanál](./media/data-factory-build-your-first-pipeline-using-editor/open-pipeline-view.png)

    Pokud se chcete vrátit do předchozího zobrazení, v nabídce v horní části vyberte **Datová továrna**.

5. V zobrazení **Diagram** dvakrát klikněte na datovou sadu **AzureBlobInput**. Zkontrolujte, jestli je řez ve stavu **Připraveno**. Než se řez zobrazí ve stavu **Připraveno**, může to několik minut trvat. Pokud se to do nějaké doby nestane, zkontrolujte, jestli je vstupní soubor (**input.log**) umístěný ve správném kontejneru (**adfgetstarted**) a složce (**inputdata**).

   ![Vstupní řez ve stavu Připraveno](./media/data-factory-build-your-first-pipeline-using-editor/input-slice-ready.png)

6. Zavřete okno **AzureBlobInput**.

7. V zobrazení **Diagram** dvakrát klikněte na datovou sadu **AzureBlobOutput**. Uvidíte, že se řez právě zpracovává.

   ![Probíhající zpracování datové sady](./media/data-factory-build-your-first-pipeline-using-editor/dataset-blade.png)

8. Po dokončení zpracování se řez zobrazí ve stavu **Připraveno**.

   ![Datová sada ve stavu Připraveno](./media/data-factory-build-your-first-pipeline-using-editor/dataset-slice-ready.png)  

   > [!IMPORTANT]
   > Vytváření clusteru HDInsight na vyžádání obvykle trvá přibližně 20 minut. Počítejte s tím, že zpracování řezu kanálem bude trvat přibližně 30 minut.
   >
   >

9. Až bude řez ve stavu **Připraveno**, zkontrolujte, jestli se ve složce **partitioneddata** v kontejneru **adfgetstarted** ve vašem úložišti objektů blob nachází výstupní data.  

   ![Výstupní data](./media/data-factory-build-your-first-pipeline-using-editor/three-ouptut-files.png)

10. Výběrem řezu otevřete okno **Datový řez** s dalšími informacemi o řezu.

    ![Informace o datovém řezu](./media/data-factory-build-your-first-pipeline-using-editor/data-slice-details.png)

11. V seznamu **Spuštění aktivit** vyberte spuštění aktivit a zobrazte o něm další informace. (V tomto scénáři je to aktivita Hive.) Informace se zobrazí v okně **Podrobnosti o spuštění aktivit**.   

    ![Okno Podrobnosti o spuštění aktivit](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-blade.png)    

   V souborech protokolů můžete zobrazit provedený dotaz Hive s informacemi o jeho stavu. Tyto protokoly jsou užitečné při řešení potíží.
   Další informace najdete v tématu [Monitorování a správa kanálů pomocí oken na webu Azure Portal](data-factory-monitor-manage-pipelines.md).

> [!IMPORTANT]
> Po úspěšném zpracování řezu se vstupní soubor odstraní. Pokud tedy chcete spustit řez znovu nebo si znovu projít tento kurz, nahrajte vstupní soubor (**input.log**) do složky **inputdata** v kontejneru **adfgetstarted**.
>
>

### <a name="monitor-a-pipeline-by-using-the-monitor--manage-app"></a>Monitorování kanálu pomocí aplikace pro monitorování a správu
K monitorování kanálů můžete použít také aplikaci pro monitorování a správu. Další informace o použití této aplikace najdete v tématu [Monitorování a správa kanálů Data Factory pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md).

1. Na domovské stránce vaší datové továrny vyberte dlaždici **Monitorování a správa**.

    ![Dlaždice Monitorování a správa](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-tile.png)

2. V aplikaci pro monitorování a správu změňte hodnoty **Čas spuštění** a **Čas ukončení** tak, aby odpovídaly času spuštění a ukončení vašeho kanálu. Vyberte **Použít**.

    ![Aplikace pro monitorování a správu](./media/data-factory-build-your-first-pipeline-using-editor/monitor-and-manage-app.png)

3. Výběrem okna aktivity v seznamu **Okna aktivit** zobrazíte informace o příslušné aktivitě.

    ![Seznam Okna aktivit](./media/data-factory-build-your-first-pipeline-using-editor/activity-window-details.png)

## <a name="summary"></a>Souhrn
V tomto kurzu jste vytvořili datovou továrnu, která zpracovává data pomocí skriptu Hive v clusteru HDInsight Hadoop. Pomocí editoru služby Data Factory na webu Azure Portal jste provedli následující:  

* Vytvoření datové továrny
* Vytvoření dvou propojených služeb:
   * Propojená služba Storage, která propojuje úložiště objektů blob obsahující vstupní a výstupní soubory s datovou továrnou.
   * Propojená služba Azure HDInsight na vyžádání, která propojuje cluster HDInsight Hadoop na vyžádání s datovou továrnou. Data Factory vytvoří cluster HDInsight Hadoop ve chvíli, kdy je potřeba zpracovat vstupní data a vygenerovat výstupní data.
* Vytvoření dvou datových sad, které popisují vstupní a výstupní data aktivity HDInsight Hive v kanálu.
* Vytvoření kanálu s aktivitou HDInsight Hive.

## <a name="next-steps"></a>Další kroky
V tomto článku jste vytvořili kanál s aktivitou transformace (aktivita HDInsight), která v clusteru HDInsight na vyžádání spouští skript Hive. Pokud chcete zjistit, jak pomocí aktivity kopírování kopírovat data z úložiště objektů blob do databáze SQL, přečtěte si [Kurz: Kopírování dat úložiště objektů blob do služby SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

## <a name="see-also"></a>Viz také
| Téma | Popis |
|:--- |:--- |
| [Kanály](data-factory-create-pipelines.md) |Tento článek vám pomůže pochopit kanály a aktivity ve službě Data Factory a porozumět tomu, jak se dají ve vaší situaci nebo firmě použít k sestavení kompletních pracovních postupů založených na datech. |
| [Datové sady](data-factory-create-datasets.md) |Tento článek vám pomůže pochopit datové sady ve službě Data Factory. |
| [Plánování a provádění](data-factory-scheduling-and-execution.md) |Tento článek vysvětluje aspekty plánování a spouštění aplikačního modelu služby Data Factory. |
| [Monitorování a správa kanálů pomocí aplikace pro monitorování a správu](data-factory-monitor-manage-app.md) |Tento článek popisuje, jak monitorovat, spravovat a ladit kanály pomocí aplikace pro monitorování a správu. |
