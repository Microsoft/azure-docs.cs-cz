---
title: Transformace dat v cloudu pomocí PowerShellu
description: Tento skript PowerShellu transformuje data v cloudu spuštěním programu Spark v clusteru Azure HDInsight Spark.
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/12/2017
ms.openlocfilehash: 3af38f4cae53807c7268f00017f02f1325116fb6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008748"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>Skript PowerShellu – transformace dat v cloudu pomocí Azure Data Factory

Tento ukázkový skript PowerShell vytvoří kanál, který transformuje data v cloudu spuštěním programu Spark na Azure HDInsight Sparkovém clusteru. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Požadavky
* **Účet Azure Storage**. Vytvořte skript Pythonu a vstupní soubor a nahrajte je do úložiště Azure. V tomto účtu úložiště se ukládá výstup z programu Sparku. Cluster Spark na vyžádání používá stejný účet úložiště jako primární úložiště.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Uložení skriptu Pythonu do účtu služby Blob Storage
1. Vytvořte soubor Pythonu s názvem **WordCount_Spark. py** s následujícím obsahem: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Nahraďte **&lt; storageAccountName &gt;** názvem vašeho účtu Azure Storage. Pak soubor uložte. 
3. Ve službě Azure Blob Storage, vytvořte kontejner nazvaný **adftutorial**, pokud ještě neexistuje. 
4. Vytvořte složku **spark**.
5. Ve složce **spark** vytvořte podsložku **script**. 
6. Do podsložky **script** uložte soubor **WordCount_Spark.py**. 


### <a name="upload-the-input-file"></a>Nahrání vstupního souboru
1. Vytvořte soubor **minecraftstory.txt** a nějakým textem. Program Sparku spočítá slova v tomto textu. 
2. Vytvořte podsložku s názvem `inputfiles` ve `spark` složce kontejneru objektů BLOB. 
3. Do podsložky `inputfiles` uložte soubor `minecraftstory.txt`. 

## <a name="sample-script"></a>Ukázkový skript
> [!IMPORTANT]
> Tento skript vytvoří soubory JSON, které definují Data Factory entit (propojená služba, datová sada a kanál) na pevném disku v c:\. složky.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Pokud chcete datovou továrnu ze skupiny prostředků odebrat, spusťte následující příkaz: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Vytvoření datové továrny |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Vytvoří propojenou službu v datové továrně. Propojená služba propojuje úložiště dat nebo výpočetní prostředky s datovou továrnou. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Vytvoří v datové továrně kanál. Kanál obsahuje jednu nebo více aktivit, které provádějí určitou operaci. V tomto kanálu aktivita Spark transformuje data spuštěním programu v clusteru Azure HDInsight Spark. |
| [Invoke – AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Vytvoří běh pro kanál. Jinými slovy, spouští kanál. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Získá podrobnosti o spuštění aktivity (spuštění aktivity) v kanálu. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/).

Další Azure Data Factory ukázkových skriptech PowerShellu najdete v [ukázkách Azure Data Factory PowerShellu](../samples-powershell.md).