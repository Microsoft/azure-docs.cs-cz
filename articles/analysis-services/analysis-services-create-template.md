---
title: Úvodní příručka – vytvoření prostředku serveru Služby Azure Analysis Services pomocí šablony Azure Resource Manager
description: Úvodní příručka ukazuje, jak prostředek serveru Azure Analysis Services pomocí šablony Azure Resource Manager.
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: analysis-services
ms.topic: quickstart
ms.date: 04/14/2020
ms.custom: subject-armqs
ms.openlocfilehash: d292500c5e26d3c07ff2402964166b3928cc7e44
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384254"
---
# <a name="quickstart-create-a-server---azure-resource-manager-template"></a>Úvodní příručka: Vytvoření serveru – šablona Azure Resource Manager

Tento rychlý start popisuje, jak vytvořit prostředek serveru Analysis Services ve vašem předplatném Azure pomocí šablony Správce prostředků.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud si chcete vytvořit účet, přejděte na stránku [Bezplatný zkušební verze Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).
* **Azure Active Directory:** Vaše předplatné musí být přidružené k tenantovi Azure Active Directory. Zároveň musíte být přihlášeni k Azure pod účtem v této službě Azure Active Directory. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md).

## <a name="create-a-server"></a>Vytvoření serveru

### <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je ze [šablon Azure QuickStart](https://azure.microsoft.com/resources/templates/101-analysis-services-create/).

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

V šabloně je definován jeden prostředek [Microsoft.AnalysisServices/servery](https://docs.microsoft.com/azure/templates/microsoft.analysisservices/2017-08-01/servers) s pravidlem brány firewall. 

### <a name="deploy-the-template"></a>Nasazení šablony

1. Vyberte následující odkaz Nasazení do Azure, abyste se přihlásili do Azure a otevřeli šablonu. Šablona se používá k vytvoření prostředku serveru Služby analysis services a k určení povinných a volitelných vlastností.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json"><img src="./media/analysis-services-create-template/deploy-azure.png" alt="deploy to azure"/></a>

2. Vyberte nebo zadejte následující hodnoty.

    Pokud není uvedeno jinak, použijte výchozí hodnoty.

    * **Předplatné**: Vyberte předplatné Azure.
    * **Skupina prostředků**: Klikněte na **Vytvořit nový**a zadejte jedinečný název nové skupiny prostředků.
    * **Umístění**: Vyberte výchozí umístění pro prostředky vytvořené ve skupině prostředků.
    * **Název serveru**: Zadejte název prostředku serveru. 
    * **Umístění:** Ignorovat pro analysis services. Umístění je určeno v umístění serveru.
    * **Umístění serveru**: Zadejte umístění serveru Analysis Services. Toto je často stejná oblast jako výchozí umístění určené pro skupinu prostředků, ale není vyžadováno. Například **severní střední USA**. Podporované oblasti najdete v [tématu Dostupnost analytických služeb podle oblastí](analysis-services-overview.md#availability-by-region).
    * **Název sku**: Zadejte název sku pro server Analysis Services, který má být vytvořit. Vyberte si z: B1, B2, D1, S0, S1, S2, S3, S4, S8v2, S9v2. Dostupnost sku závisí na regionu. Pro hodnocení a testování se doporučuje S0 nebo D1.
    * **Kapacita:** Zadejte celkový počet instancí škálování na horizontální navýšení kapacity repliky dotazu. Horizontální navýšení kapacity pro více než jednu instanci je podporována pouze ve vybraných oblastech.
    * **Nastavení brány firewall**: Zadejte příchozí pravidla brány firewall, která chcete definovat pro server. Pokud není zadán, brána firewall je zakázána.
    * **Záložní objekt blob Kontejner Uri:** Zadejte Identifikátor URI SAS do soukromého kontejneru úložiště objektů blob Azure s oprávněními pro čtení, zápis a seznam. Povinné pouze v případě, že máte v úmyslu použít [program Backup/restore](analysis-services-backup.md).
    * **Souhlasím s podmínkami a ujednáními uvedenými nahoře:** Toto políčko zaškrtněte.

3. Vyberte **Koupit**. Po úspěšném nasazení serveru se zobrazí oznámení:

   ![Šablona Správce prostředků, oznámení o nasazení portálu](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>Ověření nasazení

Pomocí portálu Azure nebo Azure PowerShellu ověřte, že byla vytvořena skupina prostředků a prostředek serveru.

#### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete, použijte portál Azure, Azure CLI nebo Azure PowerShell k odstranění skupiny prostředků a prostředků serveru.

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

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste použili šablonu Azure Resource Manager k vytvoření nové skupiny prostředků a prostředku serveru Azure Analysis Services. Po vytvoření serverového prostředku pomocí šablony zvažte následující:
- [Rychlý start: Vytvoření serveru – PowerShell](analysis-services-create-powershell.md)
- [Přidání ukázkového modelu z portálu](analysis-services-create-sample-model.md)
- [Konfigurace správce serveru a uživatelských rolí](tutorials/analysis-services-tutorial-roles.md)