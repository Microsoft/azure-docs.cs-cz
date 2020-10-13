---
title: Vytvoření funkce v Azure aktivované zprávami ve frontě
description: Pomocí Azure Functions můžete vytvořit funkci bez serveru, která je vyvolána zprávami odeslanými do fronty v Azure.
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: d722d420597bb459d3e7b6d2ca33fdc49bfe6f09
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90981571"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Vytvoření funkce aktivované službou Azure Queue Storage

Zjistěte, jak vytvořit funkci, která se aktivuje při odeslání zpráv do fronty služby Azure Storage.

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-an-azure-function-app"></a>Vytvoření aplikace Azure Function App

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-create-success.png" alt-text="Aplikace Function App se úspěšně vytvořila..." border="true":::

Dál vytvoříte v nové aplikaci Function App funkci.

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>Vytvoření funkce aktivované frontou

1. Vyberte **funkce**a pak vyberte **+ Přidat** a přidejte novou funkci.

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-app-quickstart-choose-template.png" alt-text="Aplikace Function App se úspěšně vytvořila..." border="true":::

1. Vyberte šablonu **aktivační události Azure Queue Storage** .

1. Použijte nastavení uvedená v tabulce pod obrázkem.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png" alt-text="Aplikace Function App se úspěšně vytvořila..." border="true":::


    | Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|
    | **Název** | Jedinečný název v rámci aplikace Function App | Název této funkce aktivované frontou. |
    | **Název fronty**   | myqueue-items    | Název fronty, ke které se připojíte ve svém účtu úložiště. |
    | **Připojení k účtu úložiště** | AzureWebJobsStorage | Můžete použít připojení k účtu úložiště, které už používá vaše aplikace Function App, nebo můžete vytvořit nové.  |    

1. Pro vytvoření funkce vyberte **vytvořit funkci** .

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-3.png" alt-text="Aplikace Function App se úspěšně vytvořila..." border="true":::

Pak se připojíte k účtu úložiště Azure a vytvoříte frontu úložiště **MyQueue-Items** .

## <a name="create-the-queue"></a>Vytvoření fronty

1. Ve své funkci na stránce **Přehled** vyberte skupinu prostředků.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-resource-group.png" alt-text="Aplikace Function App se úspěšně vytvořila..." border="true":::

1. Vyhledejte a vyberte účet úložiště vaší skupiny prostředků.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-account-access.png" alt-text="Aplikace Function App se úspěšně vytvořila..." border="true":::

1. Zvolte **fronty**a pak zvolte **+ fronta**. 

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-add-queue.png" alt-text="Aplikace Function App se úspěšně vytvořila..." border="true":::

1. Do pole **název** zadejte `myqueue-items` a pak vyberte **vytvořit**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-name-queue.png" alt-text="Aplikace Function App se úspěšně vytvořila..." border="true":::

Teď máte frontu úložiště a můžete funkci otestovat přidáním zprávy do fronty.

## <a name="test-the-function"></a>Testování funkce

1. Zpátky na webu Azure Portal přejděte na svoji funkci, ve spodní části stránky rozbalte **Protokoly** a ujistěte se, že není pozastavené streamování protokolů.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-queue-storage-log-expander.png" alt-text="Aplikace Function App se úspěšně vytvořila..." border="true":::

1. V samostatném okně prohlížeče v Azure Portal otevřete skupinu prostředků a vyberte účet úložiště.

1. Vyberte **fronty**a pak vyberte kontejner **MyQueue-Items** .

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue.png" alt-text="Aplikace Function App se úspěšně vytvořila..." border="true":::

1. Vyberte **přidat zprávu**a zadejte "Hello World!". v **textu zprávy**. Vyberte **OK**.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue-test.png" alt-text="Aplikace Function App se úspěšně vytvořila..." border="true":::

1. Několik sekund počkejte, potom se vraťte k protokolům funkce a zkontrolujte, jestli se nová zpráva z fronty přečetla.

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png" alt-text="Aplikace Function App se úspěšně vytvořila..." border="true":::

1. Vraťte se do fronty úložiště, vyberte **aktualizovat** a ověřte, že zpráva byla zpracována a že již není ve frontě.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

Vytvořili jste funkci, která se spustí při přidání zprávy do fronty úložiště. Další informace o aktivačních událostech fronty úložiště najdete v tématu [Vazby front úložiště služby Azure Functions](functions-bindings-storage-queue.md).

Teď, když máte vytvořenou první funkci, přidáme výstupní vazbu do funkce, která zapíše zprávu zpět do jiné fronty.

> [!div class="nextstepaction"]
> [Přidání zpráv do fronty Azure Storage pomocí funkcí](functions-integrate-storage-queue-output-binding.md)
