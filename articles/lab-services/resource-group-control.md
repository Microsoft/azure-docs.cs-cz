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
ms.date: 02/05/2019
ms.author: spelluru
ms.openlocfilehash: ddda9ef2b9bb716f7cdd33aa8fe9233f6c7d8e82
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55748996"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Zadejte skupinu prostředků pro virtuální počítače testovacího prostředí ve službě Azure DevTest Labs
Jako vlastník testovacího prostředí můžete konfigurovat virtuální počítače testovacího prostředí vytvořit v konkrétní skupině prostředků. Pomocí této funkce tak, aby vám nedostanou prostředků omezení skupiny v rámci předplatného Azure. Tato funkce také umožňuje konsolidovat prostředky testovacího prostředí v rámci jedné skupiny prostředků. Taky usnadňují sledování těchto prostředků a použití [zásady](../governance/policy/overview.md) pro správu na úrovni skupiny prostředků.

Pomocí této funkce můžete použít skript, který zadat nový nebo existující skupinu prostředků v rámci vašeho předplatného Azure pro všechny vaše testovací prostředí virtuálních počítačů. DevTest Labs v současné době podporuje tuto funkci prostřednictvím rozhraní API. 

## <a name="api-to-configure-a-resource-group-for-labs-vms"></a>Rozhraní API konfigurace skupiny prostředků pro laboratorní cvičení virtuálních počítačů
Nyní pojďme si projít možnosti máte jako vlastník testovacího prostředí při použití tohoto rozhraní API: 

- Můžete použít **skupiny prostředků testovacího prostředí** pro všechny virtuální počítače.
- Můžete použít **existující skupinu prostředků** než skupina prostředků testovacího prostředí pro všechny virtuální počítače.
- Můžete zadat **novou skupinu prostředků** název pro všechny virtuální počítače.
- Můžete pokračovat se stávající chování, to znamená, skupina prostředků se vytvoří pro každý virtuální počítač v testovacím prostředí.
 
Toto nastavení platí pro nové virtuální počítače vytvořené v testovacím prostředí. Starší virtuální počítače ve vaší laboratoři, které byly vytvořeny ve své vlastní skupiny prostředků i nadále nijak ovlivněn. Však můžete migrovat tyto virtuální počítače z jejich skupin pro jednotlivé prostředky do společného skupiny prostředků tak, aby všechny virtuální počítače testovacího prostředí v jedné skupině prostředků běžné. Další informace najdete v tématu [přesunutí prostředků do nové skupiny prostředků](../azure-resource-manager/resource-group-move-resources.md). Prostředí vytvořená ve vaší laboratoři dál zůstanou ve své vlastní skupiny prostředků.

### <a name="how-to-use-this-api"></a>Jak používat toto rozhraní API:
- Použijte verzi rozhraní API **2018_10_15_preview** při používání tohoto rozhraní API. 
- Pokud chcete zadat novou skupinu prostředků, ujistěte se, že máte **oprávnění k zápisu na skupiny prostředků** v rámci vašeho předplatného. Bez oprávnění k zápisu, vytváření nových virtuálních počítačů ve výsledku zadaný prostředek skupiny neúspěšně. 
- Při použití rozhraní API, předejte **úplné ID skupiny prostředků**. Například: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Ujistěte se, že skupina prostředků je ve stejném předplatném jako testovacího prostředí. 

## <a name="use-powershell"></a>Použití prostředí PowerShell 
Následující příklad popisuje, jak vytvořit všechny virtuální počítače testovacího prostředí do nové skupiny prostředků pomocí Powershellového skriptu.

```PowerShell
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

Vyvolání skriptu pomocí následujícího příkazu (ResourceGroup.ps1 je soubor, který obsahuje předchozí skript): 

```PowerShell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-azure-resource-manager-template"></a>Použití šablon Azure Resource Manageru
Pokud použijete šablony Azure Resource Manageru k vytvoření testovacího prostředí, použijte **vmCreationResourceGroupId** vlastnosti v části Vlastnosti testovacího prostředí šablony Resource Manageru, jak je znázorněno v následujícím příkladu:

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
                "uniqueIdentifier": "6e6f668f-992b-435c-bac3-d328b745cd25"
            },
            "dependsOn": []
        },
```


## <a name="next-steps"></a>Další postup
Viz následující články: 

- [Nastavení zásad pro testovací prostředí](devtest-lab-get-started-with-lab-policies.md)
- [Nejčastější dotazy](devtest-lab-faq.md)
