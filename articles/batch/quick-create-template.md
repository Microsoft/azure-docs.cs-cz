---
title: Rychlý Start Azure – vytvoření účtu Batch – šablona Azure Resource Manager
description: V tomto rychlém startu se dozvíte, jak vytvořit účet Batch pomocí šablony ARM.
ms.topic: quickstart
ms.date: 08/17/2020
ms.custom: subject-armqs
ms.openlocfilehash: fc6f1537b32c8ced2cfc2bf09f16106f791001fc
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97106576"
---
# <a name="quickstart-create-a-batch-account-by-using-arm-template"></a>Rychlý Start: vytvoření účtu Batch pomocí šablony ARM

Účet Batch potřebujete k vytváření výpočetních prostředků (fondy výpočetních uzlů) a dávkových úloh. Můžete propojit účet Azure Storage s účtem Batch, který je užitečný pro nasazení aplikací a ukládání vstupních a výstupních dat pro většinu reálných úloh. V tomto rychlém startu se dozvíte, jak použít šablonu Azure Resource Manager (šablonu ARM) k vytvoření účtu Batch, včetně úložiště. Po dokončení tohoto rychlého startu budete rozumět klíčovým konceptům služby Batch a budete moct službu Batch vyzkoušet ve větším měřítku s úlohami, které víc odpovídají realitě.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Musíte mít aktivní předplatné Azure.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-batchaccount-with-storage/).

:::code language="json" source="~/quickstart-templates/101-batchaccount-with-storage/azuredeploy.json":::

V šabloně jsou definované dva prostředky Azure:

- [Microsoft. Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): vytvoří účet úložiště.
- [Microsoft.Batch/batchAccounts](/azure/templates/microsoft.batch/batchaccounts): vytvoří účet Batch.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující obrázek a přihlaste se k Azure a otevřete šablonu. Šablona vytvoří účet Azure Batch a účet úložiště.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-batchaccount-with-storage%2Fazuredeploy.json)

1. Vyberte nebo zadejte následující hodnoty.

   ![Správce prostředků šablona, vytvoření účtu Batch, portál nasazení](media/quick-create-template/batch-template.png)

   - **Předplatné:** Vyberte předplatné Azure.
   - **Skupina prostředků**: vyberte **vytvořit novou**, zadejte jedinečný název skupiny prostředků a pak klikněte na **OK**.
   - **Umístění:** Vyberte prosím umístění. Například **USA – střed**.
   - **Název účtu Batch**: ponechte výchozí hodnotu.
   - **Accountsku úložiště**: Vyberte typ účtu úložiště. Například **Standard_LRS**.
   - **Umístění**: ponechte výchozí hodnotu, aby se prostředky nacházet ve stejném umístění jako vaše skupina prostředků.
   - Souhlasím s podmínkami a ujednáními uvedenými nahoře: **Vyberte**.

1. Vyberte **Koupit**.

Po několika minutách by se mělo zobrazit oznámení o úspěšném vytvoření účtu Batch.

V tomto příkladu se k nasazení šablony používá Azure Portal. Kromě Azure Portal můžete použít také Azure PowerShell, Azure CLI a REST API. Další informace o dalších metodách nasazení najdete v tématu [Nasazení šablon](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="validate-the-deployment"></a>Ověření nasazení

Nasazení můžete v Azure Portal ověřit tak, že přejdete do skupiny prostředků, kterou jste vytvořili. Na obrazovce **Přehled** potvrďte, že účet Batch a účet úložiště jsou k dispozici.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v práci s dalšími [kurzy](./tutorial-parallel-dotnet.md), možná budete chtít tyto prostředky ponechat na místě. Nebo, pokud je už nepotřebujete, můžete [Odstranit skupinu prostředků](../azure-resource-manager/management/delete-resource-group.md?tabs=azure-portal#delete-resource-group), ve které se taky odstraní účet Batch a účet úložiště, který jste vytvořili.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili účet Batch a účet úložiště. Další informace o službě Azure Batch najdete v dalších kurzech o službě Azure Batch.

> [!div class="nextstepaction"]
> [Kurzy o službě Azure Batch](./tutorial-parallel-dotnet.md)
