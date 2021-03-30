---
title: Vytvoření funkce v Azure aktivované službou BLOB Storage
description: Pomocí Azure Functions můžete vytvořit funkci bez serveru, která je vyvolána položkami přidanými do kontejneru úložiště objektů BLOB.
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: bf6865d2756579f457dded90b247326d2eec137c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "83122962"
---
# <a name="create-a-function-in-azure-thats-triggered-by-blob-storage"></a>Vytvoření funkce v Azure aktivované službou BLOB Storage

Naučte se vytvářet funkce aktivované při nahrávání nebo aktualizaci souborů v kontejneru úložiště objektů BLOB.

## <a name="prerequisites"></a>Požadavky

+ Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-function-app"></a>Vytvoření aplikace Azure Function App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Úspěšně jste vytvořili novou aplikaci Function App.

:::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-create-success.png" alt-text="Aplikace Function App byla úspěšně vytvořena." border="true":::

Dál vytvoříte v nové aplikaci Function App funkci.

<a name="create-function"></a>

## <a name="create-an-azure-blob-storage-triggered-function"></a>Vytvoření funkce aktivované službou Azure Blob Storage

1. Vyberte **funkce** a pak vyberte **+ Přidat** a přidejte novou funkci.

   :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-template.png" alt-text="Vyberte šablonu funkce v Azure Portal." border="true":::

1. Vyberte šablonu **aktivační události Azure Blob Storage** .

1. Použijte nastavení uvedená v tabulce pod obrázkem.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png" alt-text="Název a nakonfigurujte funkci aktivované úložištěm objektů BLOB." border="true":::

    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Nová funkce** | Jedinečný název v rámci aplikace Function App | Název této funkce aktivované objektem blob. |
    | **Cesta**   | samples-workitems/{název}    | Monitorované umístění ve službě Blob Storage. Název souboru objektu blob se předává v rámci vazby jako parametr _název_.  |
    | **Připojení k účtu úložiště** | AzureWebJobsStorage | Můžete použít připojení k účtu úložiště, které už používá vaše aplikace Function App, nebo můžete vytvořit nové.  |

1. Pro vytvoření funkce vyberte **vytvořit funkci** .

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-3.png" alt-text="Vytvoření funkce aktivované službou Blob Storage" border="true":::

Dále vytvořte kontejner **Samples-pracovní položky** .

## <a name="create-the-container"></a>Vytvoření kontejneru

1. Ve své funkci na stránce **Přehled** vyberte skupinu prostředků.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-resource-group.png" alt-text="Vyberte svou Azure Portal skupinu prostředků." border="true":::

1. Vyhledejte a vyberte účet úložiště vaší skupiny prostředků.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-account-access.png" alt-text="Přístup k účtu úložiště" border="true":::

1. Zvolte **kontejnery** a pak zvolte **+ kontejner**. 

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-add-container.png" alt-text="Přidejte kontejner do svého účtu úložiště v Azure Portal." border="true":::

1. Do pole **název** zadejte `samples-workitems` a pak vyberte **vytvořit**.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-name-blob-container.png" alt-text="Pojmenujte kontejner úložiště." border="true":::

Teď když máte kontejner objektů blob a můžete funkci otestovat tím, že do kontejneru odešlete soubor.

## <a name="test-the-function"></a>Testování funkce

1. Zpátky na webu Azure Portal přejděte na svoji funkci, ve spodní části stránky rozbalte **Protokoly** a ujistěte se, že není pozastavené streamování protokolů.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-log-expander.png" alt-text="Rozbalte protokol Azure Portal." border="true":::

1. V samostatném okně prohlížeče v Azure Portal otevřete skupinu prostředků a vyberte účet úložiště.

1. Vyberte **kontejnery** a pak vyberte kontejner **Samples-pracovní položky** .

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-container.png" alt-text="V Azure Portal přejdete do kontejneru Samples-pracovní položky." border="true":::

1. Vyberte **nahrát** a potom vyberte ikonu složky a zvolte soubor, který se má nahrát.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png" alt-text="Nahrání souboru do kontejneru objektů blob." border="true":::

1. Přejděte k souboru na místním počítači, jako je například soubor obrázku, a vyberte soubor. Vyberte **otevřít** a pak **Odeslat**.

1. Vraťte se k protokolům funkce a zkontrolujte, jestli proběhlo čtení objektu blob.

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png" alt-text="Zobrazte si zprávy v protokolech." border="true":::

    >[!NOTE]
    > Pokud vaše Function App běží ve výchozím plánu Consumption, může mezi přidáním nebo aktualizací objektu blob a aktivací funkce dojít až k několikaminutové prodlevě. Pokud u funkcí aktivovaných objekty blob potřebujete nízkou latenci, zvažte spuštění aplikace Function App v rámci plánu služby App Service.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Vytvořili jste funkci, která se spustí při přidání nebo aktualizaci objektu blob ve službě Blob Storage. Další informace o aktivačních událostech služby Blob Storage najdete v tématu [Vazby služby Azure Functions Blob Storage](functions-bindings-storage-blob.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
