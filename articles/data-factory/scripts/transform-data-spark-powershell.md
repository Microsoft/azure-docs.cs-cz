---
title: Prostředí PowerShell skriptu a transformace dat v cloudu pomocí služby Data Factory | Dokumentace Microsoftu
description: Tento skript Powershellu transformuje data v cloudu spuštěním programu Spark v clusteru Azure HDInsight Spark.
services: data-factory
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
ms.author: shlo
ms.openlocfilehash: bfec4ffa4d8a9f41b9c9c55ab0d84f4133bd2445
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2019
ms.locfileid: "57451702"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>Skript prostředí PowerShell – transformace dat v cloudu pomocí Azure Data Factory

Tento ukázkový skript Powershellu vytvoří kanál, který transformuje data v cloudu spuštěním programu Spark v clusteru Azure HDInsight Spark. 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Požadavky
* **Účet služby Azure Storage**. Vytvoření python skriptu a vstupní soubor a nahrát do služby Azure storage. V tomto účtu úložiště se ukládá výstup z programu Sparku. Cluster Spark na vyžádání používá stejný účet úložiště jako primární úložiště.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Uložení skriptu Pythonu do účtu služby Blob Storage
1. Vytvořte soubor Pythonu **WordCount_Spark.py** s následujícím obsahem: 

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
2. Nahraďte **&lt;storageAccountName&gt;** názvem vašeho účtu služby Azure Storage. Pak soubor uložte. 
3. Ve službě Azure Blob Storage, vytvořte kontejner **adftutorial**, pokud ještě neexistuje. 
4. Vytvořte složku **spark**.
5. Ve složce **spark** vytvořte podsložku **script**. 
6. Do podsložky **script** uložte soubor **WordCount_Spark.py**. 


### <a name="upload-the-input-file"></a>Nahrání vstupního souboru
1. Vytvořte soubor **minecraftstory.txt** a nějakým textem. Program Sparku spočítá slova v tomto textu. 
2. Vytvořte podsložku s názvem `inputfiles` v `spark` složce kontejneru objektů blob. 
3. Do podsložky `inputfiles` uložte soubor `minecraftstory.txt`. 

## <a name="sample-script"></a>Ukázkový skript
> [!IMPORTANT]
> Tento skript vytvoří soubory JSON, které definují entity služby Data Factory (propojené služby, datové sady a kanál) na váš pevný disk do složky c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete k odebrání skupiny prostředků a všechny prostředky, které s ním spojená následující příkaz:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Chcete-li odebrat objekt pro vytváření dat ze skupiny prostředků, spusťte následující příkaz: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Vytvoření datové továrny |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | Vytvoření propojené služby ve službě data factory. Propojená služba propojuje úložiště dat nebo výpočetní výkon s datovou továrnou. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | Ve službě data factory vytvoří kanál. Kanál obsahuje jednu nebo víc aktivit, které provádí určité operace. V tomto kanálu aktivitu spark transformuje data spuštěním programu v clusteru Azure HDInsight Spark. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | Vytvoří spuštění kanálu. Jinými slovy spouští kanál. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Načte podrobnosti o spuštění aktivit (spuštění aktivit) v kanálu. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty Azure Powershellu objekt pro vytváření dat najdete v [ukázky Azure Powershellu objekt pro vytváření dat](../samples-powershell.md).
