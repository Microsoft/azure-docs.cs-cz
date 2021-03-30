---
title: Zadejte skupinu prostředků pro virtuální počítače v Azure DevTest Labs | Microsoft Docs
description: Naučte se určit skupinu prostředků pro virtuální počítače v testovacím prostředí v Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7b72048405d3025ca21b324b6ad3168dd0c9ac95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85483359"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Zadejte skupinu prostředků pro virtuální počítače v testovacím prostředí v Azure DevTest Labs

Jako vlastník testovacího prostředí můžete nakonfigurovat virtuální počítače testovacího prostředí tak, aby se vytvořily v konkrétní skupině prostředků. Tato funkce vám pomůže v následujících scénářích:

- Ve vašem předplatném je víc skupin prostředků vytvořených Labs.
- Vaše laboratoře budou fungovat v rámci pevně stanovené sady skupin prostředků, které nakonfigurujete.
- V rámci předplatného Azure můžete obejít omezení a schválení požadovaná při vytváření skupin prostředků.
- Konsolidujte všechny prostředky testovacího prostředí v rámci jedné skupiny prostředků, abyste zjednodušili sledování těchto prostředků a uplatňovali [zásady](../governance/policy/overview.md) pro správu prostředků na úrovni skupiny prostředků.

Pomocí této funkce můžete použít skript k zadání nové nebo existující skupiny prostředků v rámci předplatného Azure pro všechny vaše virtuální počítače v testovacím prostředí. V současné době Azure DevTest Labs tuto funkci podporuje prostřednictvím rozhraní API.

> [!NOTE]
> Všechny limity předplatného platí při vytváření cvičení v DevTest Labs. Představte si testovací prostředí jako jakýkoliv jiný prostředek v rámci vašeho předplatného. V případě skupin prostředků je limit [980 skupin prostředků v rámci předplatného](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Použití webu Azure Portal
Pomocí těchto kroků určete skupinu prostředků pro všechny virtuální počítače vytvořené v testovacím prostředí. 

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V levé navigační nabídce vyberte **všechny služby** . 
3. Ze seznamu vyberte **DevTest Labs**.
4. V seznamu cvičení vyberte **testovací prostředí**.  
5. V části **Nastavení** v levé nabídce vyberte **Konfigurace a zásady** . 
6. V nabídce vlevo vyberte **Nastavení testovacího prostředí** . 
7. Vyberte **všechny virtuální počítače v jedné skupině prostředků**. 
8. V rozevíracím seznamu vyberte existující skupinu prostředků (nebo) vyberte **vytvořit novou**, zadejte **název** skupiny prostředků a vyberte **OK**. 

    ![Vyberte skupinu prostředků pro všechny testovací virtuální počítače.](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Použití prostředí PowerShell 
Následující příklad ukazuje, jak pomocí skriptu prostředí PowerShell vytvořit všechny virtuální počítače v testovacím prostředí v nové skupině prostředků.

```powershell
[CmdletBinding()]
Param(
    $subId,
    $labRg,
    $labName,
    $vmRg
)

az login | out-null

az account set --subscription $subId | out-null

$rgId = "/subscriptions/"+$subId+"/resourceGroups/"+$vmRg

"Updating lab '$labName' with vm rg '$rgId'..."

az resource update -g $labRg -n $labName --resource-type "Microsoft.DevTestLab/labs" --api-version 2018-10-15-preview --set properties.vmCreationResourceGroupId=$rgId

"Done. New virtual machines will now be created in the resource group '$vmRg'."
```

Vyvolejte skript pomocí následujícího příkazu. ResourceGroup.ps1 je soubor, který obsahuje předchozí skript:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager
Pokud k vytvoření testovacího prostředí používáte šablonu Azure Resource Manager, použijte vlastnost **vmCreationResourceGroupId** v části vlastnosti testovacího prostředí vaší šablony, jak je znázorněno v následujícím příkladu:

```json
        {
            "type": "microsoft.devtestlab/labs",
            "name": "[parameters('lab_name')]",
            "apiVersion": "2018_10_15_preview",
            "location": "eastus",
            "tags": {},
            "scale": null,
            "properties": {
                "vmCreationResourceGroupId": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>",
                "labStorageType": "Premium",
                "premiumDataDisks": "Disabled",
                "provisioningState": "Succeeded",
                "uniqueIdentifier": "000000000f-0000-0000-0000-00000000000000"
            },
            "dependsOn": []
        },
```


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>Rozhraní API pro konfiguraci skupiny prostředků pro virtuální počítače v testovacím prostředí
K dispozici jsou následující možnosti jako vlastník testovacího prostředí při používání tohoto rozhraní API:

- Vyberte **skupinu prostředků testovacího prostředí** pro všechny virtuální počítače.
- Vyberte **existující skupinu prostředků** kromě skupiny prostředků testovacího prostředí pro všechny virtuální počítače.
- Zadejte **nový název skupiny prostředků** pro všechny virtuální počítače.
- Pokračujte v používání existujícího chování, ve kterém se vytvoří skupina prostředků pro každý virtuální počítač v testovacím prostředí.
 
Toto nastavení platí pro nové virtuální počítače vytvořené v testovacím prostředí. Starší virtuální počítače v testovacím prostředí, které byly vytvořeny ve vlastních skupinách prostředků, zůstávají neovlivněné. Prostředí, která jsou vytvořená v testovacím prostředí, zůstávají pořád ve vlastních skupinách prostředků.

Jak používat toto rozhraní API:
- Použijte **2018_10_15_preview** verze rozhraní API.
- Pokud zadáte novou skupinu prostředků, ujistěte se, že máte **oprávnění k zápisu do skupin prostředků** v rámci vašeho předplatného. Pokud nemáte oprávnění k zápisu, nepodaří se vytvořit nové virtuální počítače v zadané skupině prostředků.
- Při používání rozhraní API předejte **úplné ID skupiny prostředků**. Příklad: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Ujistěte se, že je skupina prostředků ve stejném předplatném jako testovací prostředí. 


## <a name="next-steps"></a>Další kroky
Viz následující články: 

- [Nastavení zásad pro testovací prostředí](devtest-lab-set-lab-policy.md)
- [Nejčastější dotazy](devtest-lab-faq.md)
