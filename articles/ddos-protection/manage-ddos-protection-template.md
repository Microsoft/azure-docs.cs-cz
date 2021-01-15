---
title: Vytvoření a povolení plánu Azure DDoS Protection pomocí šablony Azure Resource Manager (šablona ARM).
description: Naučte se vytvořit a povolit plán Azure DDoS Protection pomocí šablony Azure Resource Manager (šablony ARM).
services: ddos-protection
documentationcenter: na
author: mumian
ms.service: ddos-protection
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.custom: subject-armqs
ms.author: jgao
ms.date: 01/14/2021
ms.openlocfilehash: 3d6f1707ec354cbcceb8c400cfb55f6e143f4cad
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98224562"
---
# <a name="quickstart-create-an-azure-ddos-protection-standard-using-arm-template"></a>Rychlý Start: vytvoření Azure DDoS Protection Standard pomocí šablony ARM

V tomto rychlém startu se dozvíte, jak použít šablonu Azure Resource Manager (šablonu ARM) k vytvoření plánu ochrany distribuovaného odepření služby (DDoS) a virtuální sítě (VNet), který umožňuje plán ochrany virtuální sítě. Plán Azure DDoS Protection Standard definuje sadu virtuálních sítí, které mají povolenou ochranu DDoS napříč předplatnými. Můžete nakonfigurovat jeden plán DDoS Protection pro vaši organizaci a propojit virtuální sítě s několika předplatnými do stejného plánu.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Pokud vaše prostředí splňuje požadavky a jste obeznámeni s používáním šablon ARM, vyberte tlačítko **Nasazení do Azure**. Šablona se otevře v prostředí Azure Portal.

[![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="review-the-template"></a>Kontrola šablony

Šablona použitá v tomto rychlém startu je jednou z [šablon pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-create-and-enable-ddos-protection-plans).

:::code language="json" source="~/quickstart-templates/101-create-and-enable-ddos-protection-plans/azuredeploy.json":::

Šablona definuje dva prostředky:

- [Microsoft. Network/ddosProtectionPlans](/templates/microsoft.network/ddosprotectionplans)
- [Microsoft. Network/virtualNetworks](/templates/microsoft.network/virtualnetworks)

## <a name="deploy-the-template"></a>Nasazení šablony

V tomto příkladu šablona vytvoří novou skupinu prostředků, plán DDoS Protection a virtuální síť.

1. Pokud se chcete přihlásit k Azure a otevřít šablonu, vyberte tlačítko **nasadit do Azure** .

    [![Nasazení do Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-create-and-enable-ddos-protection-plans%2Fazuredeploy.json)

1. Zadejte hodnoty pro vytvoření nové skupiny prostředků, plánu DDoS Protection a názvu virtuální sítě.

    :::image type="content" source="media/manage-ddos-protection-template/ddos-template.png" alt-text="Šablona pro rychlý Start DDoS":::

    - **Předplatné**: název předplatného Azure, kde budou prostředky nasazeny.
    - **Skupina prostředků**: Vyberte existující skupinu prostředků nebo vytvořte novou skupinu prostředků.
    - **Oblast**: oblast, kde je nasazená skupina prostředků, například východní USA.
    - **Název plánu DDoS Protection**: název nového plánu DDoS Protection.
    - **Virtual Network název**: vytvoří název nové virtuální sítě.
    - **Location**: funkce, která používá stejnou oblast jako skupina prostředků pro nasazení prostředků.
    - **Předpona adresy virtuální** sítě: použijte výchozí hodnotu nebo zadejte adresu virtuální sítě.
    - **Předpona podsítě**: použijte výchozí hodnotu nebo zadejte podsíť virtuální sítě.
    - **Plán DDoS Protection Enabled**: ve výchozím nastavení je povolený `true` plán DDoS Protection.

1. Vyberte **Zkontrolovat a vytvořit**.
1. Ověřte, že se úspěšně vybralo ověření šablony, a vyberte **vytvořit** a zahajte nasazení.

## <a name="review-deployed-resources"></a>Kontrola nasazených prostředků

Pokud chcete zkopírovat příkaz Azure CLI nebo Azure PowerShell, vyberte tlačítko **Kopírovat** . Tlačítko **vyzkoušet** se otevře Azure Cloud Shell ke spuštění příkazu.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
az network ddos-protection show \
  --resource-group MyResourceGroup \
  --name MyDdosProtectionPlan
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzDdosProtectionPlan -ResourceGroupName 'MyResourceGroup' -Name 'MyDdosProtectionPlan'
```

---

Výstup zobrazuje nové prostředky.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```Output
{
  "etag": "W/\"abcdefgh-1111-2222-bbbb-987654321098\"",
  "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan",
  "location": "eastus",
  "name": "MyDdosProtectionPlan",
  "provisioningState": "Succeeded",
  "resourceGroup": "MyResourceGroup",
  "resourceGuid": null,
  "tags": null,
  "type": "Microsoft.Network/ddosProtectionPlans",
  "virtualNetworks": [
    {
      "id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet",
      "resourceGroup": "MyResourceGroup"
    }
  ]
}
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```Output
Name              : MyDdosProtectionPlan
Id                : /subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/ddosProtectionPlans/MyDdosProtectionPlan
Etag              : W/"abcdefgh-1111-2222-bbbb-987654321098"
ProvisioningState : Succeeded
VirtualNetworks   : [
                      {
                        "Id": "/subscriptions/b1111111-2222-3333-aaaa-012345678912/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVNet"
                      }
                    ]
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až budete hotovi, můžete prostředky odstranit. Příkaz odstraní skupinu prostředků a všechny prostředky, které obsahuje.

# <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

```azurecli-interactive
az group delete --name MyResourceGroup
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name 'MyResourceGroup'
```

---

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět, jak zobrazit a nakonfigurovat telemetrii pro svůj plán DDoS Protection, přejděte k kurzům.

> [!div class="nextstepaction"]
> [Zobrazení a konfigurace telemetrie ochrany před útoky DDoS](telemetry.md)
