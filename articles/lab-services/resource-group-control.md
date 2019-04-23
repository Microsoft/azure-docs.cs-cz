---
title: Zadejte skupinu prostředků pro virtuální počítače ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak zadat skupinu prostředků pro virtuální počítače v testovacím prostředí ve službě Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: spelluru
ms.openlocfilehash: 1001e6aec7ba2f6ce62eb267d218149296048bb9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60394336"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Zadejte skupinu prostředků pro virtuální počítače testovacího prostředí ve službě Azure DevTest Labs

Jako vlastník testovacího prostředí můžete konfigurovat virtuální počítače testovacího prostředí vytvořit v konkrétní skupině prostředků. Tato funkce vám pomůže v následujících scénářích:

- Můžete mít méně skupiny prostředků vytvořené testovací prostředí v rámci vašeho předplatného.
- Máte cvičeních fungují fixní sadu skupin prostředků, které nakonfigurujete.
- Obejít omezení a jejich schvalování, vyžadovaný pro vytvoření skupiny prostředků v rámci vašeho předplatného Azure.
- Konsolidovat všechny prostředky testovacího prostředí v rámci jedné skupiny prostředků pro zjednodušení sledování těchto prostředků a použití [zásady](../governance/policy/overview.md) ke správě prostředků na úrovni skupiny prostředků.

Díky této funkci můžete použít skript zadat nový nebo existující skupiny prostředků v rámci vašeho předplatného Azure pro všechny vaše testovací prostředí virtuálních počítačů. Azure DevTest Labs v současné době podporuje tuto funkci prostřednictvím rozhraní API.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
Následujícím postupem určete skupinu prostředků pro všechny virtuální počítače vytvořené v testovacím prostředí. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **všechny služby** v navigační nabídce vlevo. 
3. Ze seznamu vyberte **DevTest Labs**.
4. V seznamu testovacích prostředí, vyberte vaše **lab**.  
5. Vyberte **konfigurace a zásad** v **nastavení** části v nabídce vlevo. 
6. Vyberte **nastavení testovacího prostředí** v nabídce vlevo. 
7. Vyberte **všechny virtuální počítače v jedné skupině prostředků**. 
8. Vyberte existující skupinu prostředků v rozevíracím seznamu (nebo) vyberte **vytvořit nový**, zadejte **název** pro skupinu prostředků a vyberte **OK**. 

    ![Vyberte skupinu prostředků pro všechny virtuální počítače testovacího prostředí](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Použití prostředí PowerShell 
Následující příklad ukazuje, jak použít skript prostředí PowerShell k vytvoření všech virtuálních počítačů testovací prostředí v nové skupině prostředků.

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

Vyvolání skriptu pomocí následujícího příkazu. ResourceGroup.ps1 je soubor, který obsahuje předchozí skript:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru
Pokud používáte šablonu Azure Resource Manageru k vytvoření testovacího prostředí, použijte **vmCreationResourceGroupId** vlastnosti v části Vlastnosti testovacího prostředí šablony, jak je znázorněno v následujícím příkladu:

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>Rozhraní API konfigurace skupiny prostředků pro testovací prostředí virtuálních počítačů
Máte následující možnosti jako vlastník testovacího prostředí při použití tohoto rozhraní API:

- Zvolte **skupiny prostředků testovacího prostředí** pro všechny virtuální počítače.
- Zvolte **existující skupinu prostředků** než skupina prostředků testovacího prostředí pro všechny virtuální počítače.
- Zadejte **novou skupinu prostředků** název pro všechny virtuální počítače.
- Pokračujte v používání existujícího chování, ve kterém se vytvoří skupinu prostředků pro každý virtuální počítač v testovacím prostředí.
 
Toto nastavení platí pro nové virtuální počítače vytvořené v testovacím prostředí. Starší virtuální počítače ve vaší laboratoři, které byly vytvořeny ve své vlastní skupiny prostředků zůstanou beze změny. Prostředí, které jsou vytvořeny ve vaší laboratoři dál zůstanou ve své vlastní skupiny prostředků.

Jak používat toto rozhraní API:
- Použití rozhraní API verze **2018_10_15_preview**.
- Pokud chcete zadat novou skupinu prostředků, ujistěte se, že máte **oprávnění k zápisu na skupiny prostředků** ve vašem předplatném. Pokud nemáte oprávnění k zápisu, vytváření nových virtuálních počítačů v zadané skupině prostředků se nezdaří.
- Při použití rozhraní API, předejte **úplné ID skupiny prostředků**. Například: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Ujistěte se, že skupina prostředků je ve stejném předplatném jako testovacího prostředí. 


## <a name="next-steps"></a>Další postup
Viz následující články: 

- [Nastavení zásad pro testovací prostředí](devtest-lab-get-started-with-lab-policies.md)
- [Nejčastější dotazy](devtest-lab-faq.md)
