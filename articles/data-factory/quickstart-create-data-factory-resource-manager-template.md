---
title: Vytvoření Azure Data Factory pomocí šablony Azure Resource Manager (šablona ARM)
description: Vytvoření ukázkového Azure Data Factory kanálu pomocí šablony Azure Resource Manager (šablona ARM).
ms.service: data-factory
tags: azure-resource-manager
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: c579b6d723533e751e08a80a578195c03e945607
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783332"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>Rychlý Start: vytvoření Azure Data Factory pomocí šablony ARM

> [!div class="op_single_selector" title1="Vyberte verzi Data Factory služby, kterou používáte:"]
> * [Verze 1](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Aktuální verze](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto rychlém startu se dozvíte, jak pomocí šablony Azure Resource Manager (šablony ARM) vytvořit datovou továrnu Azure. Kanál, který vytvoříte v této datové továrně, **kopíruje** data z jedné složky do jiné složky v úložišti objektů BLOB v Azure. Kurz o tom, jak **transformovat** data pomocí Azure Data Factory, najdete v tématu [kurz: transformace dat pomocí Sparku](transform-data-using-spark.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> Tento článek neposkytuje podrobný úvod do služby Data Factory. Úvod do služby Azure Data Factory najdete v tématu [Úvod do Azure Data Factory](introduction.md).

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

### <a name="azure-subscription"></a>Předplatné Azure

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

### <a name="create-a-file"></a>Vytvoření souboru

Otevřete textový editor, například **Poznámkový blok**, a vytvořte soubor s názvem **emp.txt** s následujícím obsahem:

```emp.txt
John, Doe
Jane, Doe
```

Uložte soubor do složky **C:\ADFv2QuickStartPSH** . (Pokud složka ještě neexistuje, vytvořte ji.)

## <a name="review-template"></a>Zkontrolovat šablonu

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/).

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

V šabloně jsou definované prostředky Azure:

- [Microsoft. Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts): definuje účet úložiště.
- [Microsoft. DataFactory/factorys](/azure/templates/microsoft.datafactory/factories): Vytvořte Azure Data Factory.
- [Microsoft. DataFactory/Factory/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices): Vytvořte propojenou službu Azure Data Factory.
- [Microsoft. DataFactory/Factory/datové sady](/azure/templates/microsoft.datafactory/factories/datasets): vytvoří Azure Data Factory datovou sadu.
- [Microsoft. DataFactory/továrny/kanály](/azure/templates/microsoft.datafactory/factories/pipelines): vytvoření kanálu Azure Data Factory.

Další příklady šablon Azure Data Factory najdete v [galerii šablon rychlý Start](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří účet Azure Data Factory, účet úložiště a kontejner objektů BLOB.

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="Nasadit šablonu ADF ARM":::

    Pokud není zadaný, použijte k vytvoření prostředků Azure Data Factory výchozí hodnoty:

    - **Předplatné**: vyberte předplatné Azure.
    - **Skupina prostředků**: vyberte **vytvořit novou**, zadejte jedinečný název pro skupinu prostředků a pak vyberte **OK**.
    - **Oblast**: vyberte umístění.  Například *východní USA*.
    - **Data Factory název**: použijte výchozí hodnotu.
    - **Umístění**: použijte výchozí hodnotu.
    - **Název účtu úložiště**: použijte výchozí hodnotu.
    - **Kontejner objektů BLOB**: použijte výchozí hodnotu.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

1. Vyberte **Přejít do skupiny prostředků**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="Skupina prostředků":::

2.  Ověřte, že je vytvořený Azure Data Factory.
    1. Vaše Azure Data Factory název je ve formátu-DataFactory \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="Ukázka Data Factory":::

2. Ověřte, že je účet úložiště vytvořený.
    1. Název účtu úložiště je ve formátu úložiště \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="Účet úložiště":::

3. Vyberte účet úložiště, který jste vytvořili, a pak vyberte **kontejnery**.
    1. Na stránce **kontejnery** vyberte kontejner objektů blob, který jste vytvořili.
        1. Název kontejneru objektů BLOB je v objektu Format-BLOB \<uniqueid\> .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="Kontejner objektů BLOB":::

### <a name="upload-a-file"></a>Nahrání souboru

1. Na stránce **kontejnery** vyberte **Odeslat**.

2. V pravém podokně v pravém podokně vyberte **soubory** a potom vyhledejte a vyberte soubor **emp.txt** , který jste vytvořili dříve.

3. Rozbalte nadpis **Upřesnit** .

4. Do pole **Odeslat do složky** zadejte Input ( *vstup*).

5. Vyberte tlačítko **Nahrát**. Měli byste vidět soubor **emp.txt** a stav nahrávání v seznamu.

6. Vyberte ikonu **Zavřít** ( **X**) pro zavření stránky **nahrát objekt BLOB** .

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="Odeslat soubor do vstupní složky":::

Nechejte stránku kontejneru otevřenou, protože ji můžete použít k ověření výstupu na konci tohoto rychlého startu.

### <a name="start-trigger"></a>Spustit aktivační událost

1. Přejděte na stránku **Datové továrny** a vyberte datovou továrnu, kterou jste vytvořili. 

2. Vyberte dlaždici **Author & monitor** . 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="& monitorování autora":::

2. Vyberte kartu **Autor** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false"::: .

3. Vyberte kanál vytvořený-ArmtemplateSampleCopyPipeline.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="Kanál šablony ARM":::

4. Vyberte možnost **Přidat**  >  **aktivační událost nyní**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="Trigger":::

5. V pravém podokně v části **spuštění kanálu** vyberte **OK**.

### <a name="monitor-the-pipeline"></a>Monitorování kanálu

1. Vyberte kartu **monitorování** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false"::: .

2. Zobrazí se spuštění aktivit související s příslušným spuštěním kanálu. V tomto rychlém startu má kanál pouze jednu aktivitu typu Kopírování. V takovém případě se zobrazí spuštění této aktivity.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="Úspěšné spuštění":::

### <a name="verify-the-output-file"></a>Ověření výstupního souboru

Kanál automaticky vytvoří výstupní složku v kontejneru objektů BLOB. Potom do výstupní složky zkopíruje soubor emp.txt ze vstupní složky. 

1. V Azure Portal klikněte na stránce **kontejnery** na **aktualizovat** , aby se zobrazila výstupní složka. 

2. V seznamu složek vyberte **výstup** .

3. Potvrďte, že je do výstupní složky zkopírovaný soubor **emp.txt**. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="Výstup":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky, které jste vytvořili v rámci tohoto rychlého startu, můžete vyčistit dvěma způsoby. Můžete [Odstranit skupinu prostředků Azure](../azure-resource-manager/management/delete-resource-group.md), která zahrnuje všechny prostředky ve skupině prostředků. Pokud chcete ostatní prostředky zachovat beze změny, odstraňte pouze datovou továrnu, kterou jste vytvořili v tomto kurzu.

Odstranění skupiny prostředků odstraní všechny prostředky, které v ní jsou, včetně datových továren. Spuštěním následujícího příkazu odstraníte celou skupinu prostředků: 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Pokud chcete odstranit jenom datovou továrnu, ne celou skupinu prostředků, spusťte následující příkaz: 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili Azure Data Factory pomocí šablony ARM a ověřili nasazení. Další informace o Azure Data Factory a Azure Resource Manager najdete dál v článcích níže.

- [Dokumentace k Azure Data Factory](index.yml)
- Další informace o [Azure Resource Manageru](../azure-resource-manager/management/overview.md)
- Získat další [šablony Azure Data Factory ARM](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular)