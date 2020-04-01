---
title: Spuštění skriptů Pythonu prostřednictvím datové továrny – Azure Batch Python
description: Kurz – naučte se spouštět skripty Pythonu jako součást kanálu prostřednictvím Azure Data Factory pomocí Azure Batch.
services: batch
author: mammask
manager: jeconnoc
ms.service: batch
ms.devlang: python
ms.topic: tutorial
ms.date: 12/11/2019
ms.author: komammas
ms.custom: mvc
ms.openlocfilehash: 2995c5da4491f14471d9ed03022a144a02beab5a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201833"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Kurz: Spouštění skriptů Pythonu prostřednictvím Azure Data Factory pomocí Azure Batch

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření pomocí účtů Batch a Storage
> * Vývoj a spuštění skriptu v Pythonu
> * Vytvoření fondu výpočetních uzlů pro spouštění aplikace
> * Plánování úloh pythonu
> * Sledování analytického kanálu
> * Přístup k souborům protokolu

V níže uvedeném příkladu je spuštěn skript Pythonu, který přijímá vstup CSV z kontejneru úložiště objektů blob, provádí proces manipulace s daty a zapíše výstup do samostatného kontejneru úložiště objektů blob.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

* Nainstalovaná distribuce [Pythonu](https://www.python.org/downloads/) pro lokální testování.
* Balíček [Azure.](https://pypi.org/project/azure/) `pip`
* Účet Azure Batch a propojený účet Azure Storage. Další informace o vytváření a propojení dávkových účtů s účty úložiště najdete v [tématu Vytvoření dávkového účtu.](quick-create-portal.md#create-a-batch-account)
* Účet Azure Data Factory. Další informace o tom, jak vytvořit datovou továrnu na webu Azure Portal, najdete v tématu [Vytvoření datové továrny.](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)
* [Průzkumník dávek](https://azure.github.io/BatchExplorer/).
* [Průzkumník úložišť Azure](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Vytvoření fondu dávek pomocí Průzkumníka dávek

V této části použijete Batch Explorer k vytvoření fondu dávek, který bude váš kanál Azure Data factory používat. 

1. Přihlaste se k Batch Exploreru pomocí přihlašovacích údajů Azure.
1. Vyberte dávkový účet
1. Vytvořte fond výběrem **bazénů** na levém panelu a potom tlačítkem **Přidat** nad vyhledávacím formulářem. 
    1. Zvolte ID a zobrazovaný název. Použijeme `custom-activity-pool` pro tento příklad.
    1. Nastavte typ měřítka na **pevnou velikost**a nastavte počet vyhrazených uzlů na 2.
    1. V části **Data science**vyberte jako operační systém systém **Dsvm Windows.**
    1. Zvolte `Standard_f2s_v2` jako velikost virtuálního počítače.
    1. Povolte počáteční úlohu `cmd /c "pip install pandas"`a přidejte příkaz . Identita uživatele může zůstat jako výchozí **uživatel fondu**.
    1. Vyberte **OK**.

## <a name="create-blob-containers"></a>Vytvoření kontejnerů objektů blob

Zde vytvoříte kontejnery objektů blob, které budou ukládat vstupní a výstupní soubory pro dávkovou úlohu Rozpoznávání OCR.

1. Přihlaste se k Průzkumníku úložiště pomocí přihlašovacích údajů Azure.
1. Pomocí účtu úložiště propojeného s vaším účtem Batch vytvořte dva kontejnery objektů blob (jeden pro vstupní soubory, jeden pro výstupní soubory) podle kroků v [části Vytvoření kontejneru objektů blob](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * V tomto příkladu budeme volat `input`náš vstupní kontejner `output`a náš výstupní kontejner .
1. Nahrávání `main.py` `iris.csv` a vstup `input` do vstupního kontejneru pomocí Průzkumníka úložiště podle kroků při [správě objektů BLOB v kontejneru objektů blob](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container)


## <a name="develop-a-script-in-python"></a>Vývoj skriptu v Pythonu

Následující skript Pythonu `iris.csv` načte `input` datovou sadu z kontejneru, provede proces manipulace `output` s daty a uloží výsledky zpět do kontejneru.

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
blobService.create_blob_from_text(containerName, "iris_setosa.csv", "iris_setosa.csv")
```

Uložte skript `main.py` jako a nahrajte ho do kontejneru **Úložiště Azure.** Nezapomeňte otestovat a ověřit jeho funkce místně před odesláním do kontejneru objektů blob:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Nastavení kanálu Azure Data Factory

V této části vytvoříte a ověříte kanál pomocí skriptu Pythonu.

1. Postupujte podle pokynů k vytvoření datové továrny v [části](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory)"Vytvořit data továrny" tohoto článku .
1. V poli **Výrobní zdroje** vyberte tlačítko + (plus) a pak vyberte **Pipeline**
1. Na kartě **Obecné** nastavte název kanálu jako "Spustit Python"

    ![](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. V poli **Aktivity** rozbalte **položku Dávková služba**. Přetáhněte vlastní aktivitu z panelu nástrojů **Aktivity** na povrch návrháře kanálu.
1. Na kartě **Obecné** zadejte **testPipeline** pro Name.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task.png)
1. Na kartě **Azure Batch** přidejte **dávkový účet,** který byl vytvořen v předchozích krocích, a **otestovat připojení,** abyste zajistili, že je úspěšný.

    ![](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)

1. Na kartě **Nastavení** zadejte příkaz `python main.py`.
1. Pro **službu propojenou s prostředky**přidejte účet úložiště, který byl vytvořen v předchozích krocích. Otestujte připojení, abyste se ujistili, že je úspěšné.
1. V **cestě ke složce**vyberte název kontejneru **azure blob storage,** který obsahuje skript Pythonu a přidružené vstupy. Tím se vybrané soubory stáhnou z kontejneru do instanů uzlu fondu před spuštěním skriptu Pythonu.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)
1. Kliknutím na **Ověřit** na panelu nástrojů kanálu nad plátnem ověřte nastavení kanálu. Ověřte úspěšné ověření kanálu. Pokud chcete zavřít výstup ověřování, vyberte tlačítko &gt;&gt; (šipky doprava).
1. Klepněte na **tlačítko Ladění** otestovat kanálu a ujistěte se, že funguje přesně.
1. Chcete-li publikovat kanál, klepněte na **tlačítko Publikovat.**
1. Kliknutím na **Aktivovat** spusťte skript Pythonu jako součást dávkového procesu.

    ![](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>Sledování souborů protokolu

V případě, že upozornění nebo chyby jsou vytvářeny `stdout.txt` `stderr.txt` spuštěním skriptu, můžete rezervovat nebo pro více informací o výstupu, který byl zaznamenán.

1. Na levé straně Průzkumníka dávek vyberte **Úlohy.**
1. Vyberte úlohu vytvořenou vaší továrně dat. Za předpokladu, `custom-activity-pool`že `adfv2-custom-activity-pool`jste fond pojmenovali , vyberte .
1. Klikněte na úlohu, která měla kód ukončení selhání.
1. Zobrazit `stdout.txt` `stderr.txt` a prozkoumat a diagnostikovat váš problém.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste prozkoumali příklad, který vás naučil spouštět skripty Pythonu jako součást kanálu prostřednictvím Azure Data Factory pomocí Azure Batch.

Další informace o Azure Data Factory najdete v tématu:

> [!div class="nextstepaction"]
> [Azure Data Factory](../data-factory/introduction.md)
> [kanály a aktivity](../data-factory/concepts-pipelines-activities.md)
> [Vlastní aktivity](../data-factory/transform-data-using-dotnet-custom-activity.md)
