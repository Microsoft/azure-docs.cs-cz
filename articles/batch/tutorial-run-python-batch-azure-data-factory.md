---
title: Spouštění skriptů Pythonu pomocí Data Factory
description: Kurz – Naučte se spouštět skripty Pythonu jako součást kanálu prostřednictvím Azure Data Factory pomocí Azure Batch.
author: mammask
ms.devlang: python
ms.topic: tutorial
ms.date: 08/12/2020
ms.author: komammas
ms.custom: mvc, devx-track-python
ms.openlocfilehash: f4c71cffe00faa6dd8cc440c59f94b8c2d60f712
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88185107"
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
* Balíček [Azure](https://pypi.org/project/azure/) `pip` .
* [Datová sadairis.csv](https://www.kaggle.com/uciml/iris/version/2#Iris.csv)
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
    1. Nastavte typ škálování na **pevnou velikost**a nastavte počet vyhrazených uzlů na 2.
    1. V oblasti **datové vědy**vyberte jako operační systém možnost **Dsvm Windows** .
    1. Vyberte `Standard_f2s_v2` Velikost virtuálního počítače.
    1. Povolte spouštěcí úkol a přidejte příkaz `cmd /c "pip install pandas"` . Identita uživatele může zůstat stejná jako uživatel s výchozím **fondem**.
    1. Vyberte **OK**.

## <a name="create-blob-containers"></a>Vytváření kontejnerů objektů BLOB

Tady vytvoříte kontejnery objektů blob, které budou ukládat vstupní a výstupní soubory pro dávkovou úlohu OCR.

1. Přihlaste se k Průzkumník služby Storage pomocí svých přihlašovacích údajů Azure.
1. Pomocí účtu úložiště propojeného s účtem Batch vytvořte dva kontejnery objektů BLOB (jeden pro vstupní soubory, jeden pro výstupní soubory) podle kroků v části [vytvoření kontejneru objektů BLOB](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * V tomto příkladu budeme volat náš vstupní kontejner `input` a náš výstupní kontejner `output` .
1. Nahrajte [`iris.csv`](https://www.kaggle.com/uciml/iris/version/2#Iris.csv) do vstupního kontejneru `input` pomocí Průzkumník služby Storage podle kroků v části [Správa objektů BLOB v kontejneru objektů BLOB](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container) .

## <a name="develop-a-script-in-python"></a>Vývoj skriptu v Pythonu

Následující skript Pythonu načte `iris.csv` datovou sadu z vašeho `input` kontejneru, provede proces manipulace s daty a výsledky uloží zpátky do `output` kontejneru.

``` python
# Load libraries
from azure.storage.blob import BlockBlobService
import pandas as pd

# Define parameters
storageAccountName = "<storage-account-name>"
storageKey         = "<storage-account-key>"
containerName      = "output"

# Establish connection with the blob storage account
blobService = BlockBlobService(account_name=storageAccountName,
                               account_key=storageKey
                               )

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Subset records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv("iris_setosa.csv", index = False)

# Upload iris dataset
blobService.create_blob_from_path(containerName, "iris_setosa.csv", "iris_setosa.csv")
```

Uložte skript jako `main.py` a nahrajte ho do kontejneru **Azure Storage** . Před nahráním do kontejneru objektů BLOB Nezapomeňte otestovat a ověřit jeho funkčnost místně:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Nastavení kanálu Azure Data Factory

V této části vytvoříte a ověříte kanál pomocí skriptu Pythonu.

1. Podle pokynů v části Vytvoření datové továrny v [tomto článku](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)Vytvořte datovou továrnu.
1. V poli **prostředky továrny** vyberte tlačítko + (plus) a pak vyberte **kanál** .
1. Na kartě **Obecné** nastavte název kanálu jako "Run Python".

    ![Na kartě Obecné nastavte název kanálu jako "Run Python".](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. V poli **aktivity** rozbalte položku **Služba Batch**. Přetáhněte vlastní aktivitu z panelu nástrojů **aktivity** na plochu návrháře kanálu.
1. Na kartě **Obecné** zadejte **testPipeline** pro název.

    ![Na kartě Obecné zadejte testPipeline pro název.](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. Na kartě **Azure Batch** přidejte **účet Batch** , který byl vytvořen v předchozích krocích, a **otestujte připojení** , aby bylo zajištěno, že je úspěšné.

    ![Na kartě Azure Batch přidejte účet Batch, který jste vytvořili v předchozích krocích, a pak otestujte připojení.](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. Na kartě **Nastavení** zadejte příkaz `python main.py` .
1. Pro **propojenou službu prostředku**přidejte účet úložiště, který jste vytvořili v předchozích krocích. Otestujte připojení, abyste zajistili jeho úspěšnost.
1. V **cestě ke složce**vyberte název kontejneru **Azure Blob Storage** , který obsahuje skript Pythonu a související vstupy. Tím se stáhnou vybrané soubory z kontejneru do instancí uzlů fondu před spuštěním skriptu Pythonu.

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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste prozkoumali příklad, který vás seznámí s tím, jak spustit skripty Pythonu jako součást kanálu prostřednictvím Azure Data Factory pomocí Azure Batch.

Další informace o Azure Data Factory najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Azure Data Factory](../data-factory/introduction.md) 
>  [Kanály a aktivity](../data-factory/concepts-pipelines-activities.md) 
>  [Vlastní aktivity](../data-factory/transform-data-using-dotnet-custom-activity.md)
