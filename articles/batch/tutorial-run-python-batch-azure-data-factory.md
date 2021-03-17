---
title: Kurz – spouštění skriptů Pythonu pomocí Data Factory
description: Naučte se spouštět skripty Pythonu jako součást kanálu prostřednictvím Azure Data Factory pomocí Azure Batch.
author: pkshultz
ms.devlang: python
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: peshultz
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 241a47ccf9021c6065fea907b4d9914744a64972
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2021
ms.locfileid: "103461687"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Kurz: spouštění skriptů Pythonu pomocí Azure Data Factory pomocí Azure Batch

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření pomocí účtů Batch a Storage
> * Vývoj a spouštění skriptu v Pythonu
> * Vytvoření fondu výpočetních uzlů pro spouštění aplikace
> * Plánování úloh v Pythonu
> * Monitorování kanálu analýz
> * Přístup k protokolům protokolů

Následující příklad spustí skript Pythonu, který přijímá vstup sdíleného svazku clusteru z kontejneru úložiště objektů blob, provádí proces manipulace s daty a zapisuje výstup do samostatného kontejneru úložiště objektů BLOB.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

* Nainstalovaná distribuce [Pythonu](https://www.python.org/downloads/) pro místní testování.
* Balíček [Azure-Storage-BLOB](https://pypi.org/project/azure-storage-blob/) `pip` .
* [Datová sadairis.csv](https://github.com/Azure-Samples/batch-adf-pipeline-tutorial/blob/master/iris.csv)
* Účet Azure Batch a propojený účet Azure Storage. Další informace o tom, jak vytvořit a propojit účty Batch s účty úložiště, najdete v tématu [Vytvoření účtu Batch](quick-create-portal.md#create-a-batch-account) .
* Účet Azure Data Factory. Další informace o tom, jak vytvořit datovou továrnu pomocí Azure Portal, najdete v tématu [Vytvoření datové továrny](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) .
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Vytvoření fondu Batch pomocí Batch Explorer

V této části použijete Batch Explorer k vytvoření fondu služby Batch, který bude používat váš kanál Azure Data Factory. 

1. Přihlaste se k Batch Explorer pomocí svých přihlašovacích údajů Azure.
1. Vyberte účet Batch.
1. Vytvořte fond tak, že na levé straně vyberete **fondy** a pak tlačítko **Přidat** nad formulář pro hledání. 
    1. Vyberte ID a zobrazované jméno. `custom-activity-pool`V tomto příkladu budeme používat.
    1. Nastavte typ škálování na **pevnou velikost** a nastavte počet vyhrazených uzlů na 2.
    1. V oblasti **datové vědy** vyberte jako operační systém možnost **Dsvm Windows** .
    1. Vyberte `Standard_f2s_v2` Velikost virtuálního počítače.
    1. Povolte spouštěcí úkol a přidejte příkaz `cmd /c "pip install azure-storage-blob pandas"` . Identita uživatele může zůstat stejná jako uživatel s výchozím **fondem**.
    1. Vyberte **OK**.

## <a name="create-blob-containers"></a>Vytváření kontejnerů objektů BLOB

Tady vytvoříte kontejnery objektů blob, které budou ukládat vstupní a výstupní soubory pro dávkovou úlohu OCR.

1. Přihlaste se k Průzkumník služby Storage pomocí svých přihlašovacích údajů Azure.
1. Pomocí účtu úložiště propojeného s účtem Batch vytvořte dva kontejnery objektů BLOB (jeden pro vstupní soubory, jeden pro výstupní soubory) podle kroků v části [vytvoření kontejneru objektů BLOB](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * V tomto příkladu budeme volat náš vstupní kontejner `input` a náš výstupní kontejner `output` .
1. Nahrajte [`iris.csv`](https://github.com/Azure-Samples/batch-adf-pipeline-tutorial/blob/master/iris.csv) do vstupního kontejneru `input` pomocí Průzkumník služby Storage podle kroků v části [Správa objektů BLOB v kontejneru objektů BLOB](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) .

## <a name="develop-a-script-in-python"></a>Vývoj skriptu v Pythonu

Následující skript Pythonu načte `iris.csv` datovou sadu z vašeho `input` kontejneru, provede proces manipulace s daty a výsledky uloží zpátky do `output` kontejneru.

``` python
# Load libraries
from azure.storage.blob import BlobClient
import pandas as pd

# Define parameters
connectionString = "<storage-account-connection-string>"
containerName = "output"
outputBlobName  = "iris_setosa.csv"

# Establish connection with the blob storage account
blob = BlobClient.from_connection_string(conn_str=connectionString, container_name=containerName, blob_name=outputBlobName)

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Take a subset of the records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv(outputBlobName, index = False)

with open(outputBlobName, "rb") as data:
    blob.upload_blob(data)
```

Uložte skript jako `main.py` a nahrajte ho do kontejneru **Azure Storage** `input` . Před nahráním do kontejneru objektů BLOB Nezapomeňte otestovat a ověřit jeho funkčnost místně:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Nastavení kanálu Azure Data Factory

V této části vytvoříte a ověříte kanál pomocí skriptu Pythonu.

1. Podle pokynů v části Vytvoření datové továrny v [tomto článku](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)Vytvořte datovou továrnu.
1. V poli **prostředky továrny** vyberte tlačítko + (plus) a pak vyberte **kanál** .
1. Na kartě **Obecné** nastavte název kanálu jako "Run Python".

    ![Na kartě Obecné nastavte název kanálu jako "Run Python".](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. V poli **aktivity** rozbalte položku **Služba Batch**. Přetáhněte vlastní aktivitu z panelu nástrojů **aktivity** na plochu návrháře kanálu. Pro vlastní aktivitu vyplňte následující karty:
    1. Na kartě **Obecné** zadejte **testPipeline** pro název ![ na kartě Obecné zadejte testPipeline pro název.](./media/run-python-batch-azure-data-factory/create-custom-task.png)
    1. Na kartě **Azure Batch** přidejte **účet Batch** , který byl vytvořen v předchozích krocích, a **otestujte připojení** , aby bylo zajištěno, že je úspěšné.
    ![Na kartě Azure Batch přidejte účet Batch, který jste vytvořili v předchozích krocích, a pak otestujte připojení.](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)
    1. Na kartě **Nastavení** :
        1. Nastavte **příkaz** jako `python main.py` .
        1. Pro **propojenou službu prostředku** přidejte účet úložiště, který jste vytvořili v předchozích krocích. Otestujte připojení, abyste zajistili jeho úspěšnost.
        1. V **cestě ke složce** vyberte název kontejneru **Azure Blob Storage** , který obsahuje skript Pythonu a související vstupy. Tím se stáhnou vybrané soubory z kontejneru do instancí uzlů fondu před spuštěním skriptu Pythonu.

        ![V cestě ke složce vyberte název kontejneru Azure Blob Storage.](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)

1. Kliknutím na **Ověřit** na panelu nástrojů kanálu nad plátnem ověřte nastavení kanálu. Ověřte úspěšné ověření kanálu. Pokud chcete zavřít výstup ověřování, vyberte tlačítko &gt;&gt; (šipky doprava).
1. Kliknutím na **ladit** otestujete kanál a ujistěte se, že funguje správně.
1. Kliknutím na **publikovat** publikujte kanál.
1. Kliknutím na **Trigger (aktivovat** ) spustíte skript Pythonu jako součást dávkového procesu.

    ![Klikněte na Trigger a spusťte skript Pythonu jako součást dávkového procesu.](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>Monitorování souborů protokolu

V případě, že se při spuštění skriptu vygenerovala upozornění nebo chyby, můžete se podívat `stdout.txt` `stderr.txt` na Další informace o zaznamenání výstupu.

1. Vyberte **úlohy** na levé straně Batch Explorer.
1. Vyberte úlohu vytvořenou datovou továrnou. Za předpokladu, že jste svůj fond najmenovali `custom-activity-pool` , vyberte `adfv2-custom-activity-pool` .
1. Klikněte na úlohu, u které došlo k chybě ukončovacího kódu.
1. Prohlédněte si `stdout.txt` a `stderr.txt` Prozkoumejte a Diagnostikujte svůj problém.

## <a name="clean-up-resources"></a>Vyčištění prostředků

I když se vám neúčtují poplatky za úlohy a úkoly jako takové, účtují se vám poplatky za výpočetní uzly. Proto doporučujeme, abyste fondy přidělovali, jen když je to potřeba. Při odstranění fondu se odstraní veškeré výstupy úkolů v uzlech. Vstupní a výstupní soubory však zůstanou v účtu úložiště. Pokud už je nepotřebujete, můžete také odstranit účet Batch a účet úložiště.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Ověření pomocí účtů Batch a Storage
> * Vývoj a spouštění skriptu v Pythonu
> * Vytvoření fondu výpočetních uzlů pro spouštění aplikace
> * Plánování úloh v Pythonu
> * Monitorování kanálu analýz
> * Přístup k protokolům protokolů

Další informace o Azure Data Factory najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Přehled Azure Data Factory](../data-factory/introduction.md)
