---
title: Rychlý Start – vytvoření prostředku Azure Analysis Services serveru pomocí šablony Azure Resource Manager
description: Rychlý Start ukazuje, jak Azure Analysis Services prostředek serveru pomocí Azure Resource Manager šablony.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/31/2020
ms.custom: subject-armqs, references_regions
ms.openlocfilehash: 3e776bf41420d38a1b208ce11a6a34e97fa92a15
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89230768"
---
# <a name="quickstart-create-a-server---arm-template"></a>Rychlý Start: Vytvoření šablony serveru – ARM

V tomto rychlém startu se dozvíte, jak vytvořit prostředek serveru Analysis Services v předplatném Azure pomocí šablony Azure Resource Manager (šablona ARM).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**: Pokud chcete vytvořit účet, přejděte na [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) .
* **Azure Active Directory:** Vaše předplatné musí být přidružené k tenantovi Azure Active Directory. Zároveň musíte být přihlášeni k Azure pod účtem v této službě Azure Active Directory. Další informace najdete v článku o [ověřování a uživatelských oprávněních](analysis-services-manage-users.md).

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-analysis-services-create/).

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

V šabloně je definován jeden prostředek [Microsoft. AnalysisServices/Servers](/azure/templates/microsoft.analysisservices/servers) s pravidlem brány firewall.

## <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující odkaz nasazení do Azure, abyste se přihlásili do Azure a otevřeli šablonu. Šablona se používá k vytvoření prostředku serveru Analysis Services a určení požadovaných a volitelných vlastností.

   [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

2. Vyberte nebo zadejte následující hodnoty.

    Pokud není uvedeno jinak, použijte výchozí hodnoty.

    * **Předplatné**: vyberte předplatné Azure.
    * **Skupina prostředků**: klikněte na **vytvořit nový** a zadejte jedinečný název nové skupiny prostředků.
    * **Umístění**: Vyberte výchozí umístění pro prostředky vytvořené ve skupině prostředků.
    * **Název serveru**: zadejte název prostředku serveru. 
    * **Location**: Ignore pro Analysis Services. Umístění je zadáno v umístění serveru.
    * **Umístění serveru**: zadejte umístění serveru Analysis Services. Často se jedná o stejnou oblast jako výchozí umístění zadané pro skupinu prostředků, ale není nutné. Například **střed USA – sever**. Podporované oblasti najdete v tématu [dostupnost Analysis Services v jednotlivých oblastech](analysis-services-overview.md#availability-by-region).
    * **Název SKU**: zadejte název sku pro Analysis Services Server, který chcete vytvořit. Vyberte jednu z těchto: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. Dostupnost skladové položky závisí na oblasti. Pro účely vyhodnocení a testování se doporučuje S0 nebo D1.
    * **Kapacita**: zadejte celkový počet instancí škálování na více instancí repliky dotazů. Horizontální navýšení kapacity z více než jedné instance se podporuje jenom ve vybraných oblastech.
    * **Nastavení brány firewall**: zadejte příchozí pravidla brány firewall, která chcete pro server definovat. Pokud není zadaný, brána firewall je zakázaná.
    * **Záložní identifikátor URI kontejneru objektů BLOB**: zadejte identifikátor URI SAS k privátnímu kontejneru Azure Blob Storage s oprávněním ke čtení, zápisu a výpisu. Vyžadováno pouze v případě, že máte v úmyslu použít [zálohování a obnovení](analysis-services-backup.md).
    * **Souhlasím s podmínkami a ujednáními uvedenými nahoře:** Toto políčko zaškrtněte.

3. Vyberte **Koupit**. Po úspěšném nasazení serveru obdržíte oznámení:

   ![Šablona ARM, nasazení oznámení na portálu](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Ověření nasazení

Pomocí Azure Portal nebo Azure PowerShell ověřte, že se vytvořila skupina prostředků a prostředek serveru.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků a prostředek serveru pomocí Azure Portal, Azure CLI nebo Azure PowerShell.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili šablonu ARM k vytvoření nové skupiny prostředků a prostředku serveru Azure Analysis Services. Po vytvoření prostředku serveru pomocí šablony Vezměte v úvahu následující skutečnosti:

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Konfigurace brány firewall serveru – portál](analysis-services-qs-firewall.md)   
