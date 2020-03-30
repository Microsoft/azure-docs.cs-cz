---
title: Určení skupiny prostředků pro virtuální počítače v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak určit skupinu prostředků pro virtuální počítače v testovacím prostředí v Azure DevTest Labs.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/03/2019
ms.author: spelluru
ms.openlocfilehash: 29816d158cf1428727b7ff17bcc2c347f402dedf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77134535"
---
# <a name="specify-a-resource-group-for-lab-virtual-machines-in-azure-devtest-labs"></a>Určení skupiny prostředků pro testovací virtuální počítače v laboratořích Azure DevTest Labs

Jako vlastník testovacího prostředí můžete nakonfigurovat virtuální počítače testovacího prostředí, které mají být vytvořeny v určité skupině prostředků. Tato funkce vám pomůže v následujících scénářích:

- Mít méně skupin prostředků vytvořených testovacích prostředí v rámci předplatného.
- Nechte vaše testovací prostředí pracovat v rámci pevné sady skupin prostředků, které nakonfigurujete.
- Obejděte omezení a schválení požadovaná pro vytváření skupin prostředků v rámci předplatného Azure.
- Konsolidujte všechny prostředky testovacího prostředí v rámci jedné skupiny prostředků, abyste zjednodušili sledování těchto prostředků a [použili zásady](../governance/policy/overview.md) pro správu prostředků na úrovni skupiny prostředků.

Pomocí této funkce můžete pomocí skriptu určit novou nebo existující skupinu prostředků v rámci předplatného Azure pro všechny virtuální počítače testovacího prostředí. V současné době Azure DevTest Labs podporuje tuto funkci prostřednictvím rozhraní API.

> [!NOTE]
> Všechna omezení předplatného platí při vytváření testovacích prostředí v devtest labs. Představte si testovací prostředí jako jakýkoli jiný prostředek ve vašem předplatném. V případě skupin prostředků je limit [980 skupin prostředků na odběr](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). 

## <a name="use-azure-portal"></a>Použití webu Azure Portal
Podle těchto kroků určete skupinu prostředků pro všechny virtuální hody vytvořené v testovacím prostředí. 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. V levé navigační nabídce vyberte **Všechny služby.** 
3. Ze seznamu vyberte **DevTest Labs**.
4. Ze seznamu testovacích prostředí vyberte **testovací prostředí**.  
5. V **levé** nabídce vyberte Konfigurace **a zásady.** 
6. V levé nabídce vyberte **Nastavení testovacího prostředí.** 
7. Vyberte **Všechny virtuální počítače v jedné skupině prostředků**. 
8. V rozevíracím seznamu vyberte existující skupinu prostředků (nebo) vyberte **Vytvořit nový**, zadejte **název** skupiny prostředků a vyberte **OK**. 

    ![Vyberte skupinu prostředků pro všechny virtuální laboratoře.](./media/resource-group-control/select-resource-group.png)

## <a name="use-powershell"></a>Použití prostředí PowerShell 
Následující příklad ukazuje, jak pomocí skriptu Prostředí PowerShell vytvořit všechny testovací virtuální počítače v nové skupině prostředků.

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

Vyvolá skript pomocí následujícího příkazu. ResourceGroup.ps1 je soubor, který obsahuje předchozí skript:

```powershell
.\ResourceGroup.ps1 -subId <subscriptionID> -labRg <labRGNAme> -labName <LanName> -vmRg <RGName> 
```

## <a name="use-an-azure-resource-manager-template"></a>Použití šablony Azure Resource Manageru
Pokud k vytvoření testovacího prostředí používáte šablonu Azure Resource Manageru, použijte vlastnost **vmCreationResourceGroupId** v části vlastností testovacího prostředí šablony, jak je znázorněno v následujícím příkladu:

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


## <a name="api-to-configure-a-resource-group-for-lab-vms"></a>Rozhraní API pro konfiguraci skupiny prostředků pro virtuální počítače testovacího prostředí
Při použití tohoto rozhraní API máte jako vlastník testovacího prostředí následující možnosti:

- Zvolte **skupinu prostředků testovacího prostředí** pro všechny virtuální počítače.
- Zvolte **existující skupinu prostředků** než skupinu prostředků testovacího prostředí pro všechny virtuální počítače.
- Zadejte nový název **skupiny prostředků** pro všechny virtuální počítače.
- Pokračujte v používání existujícího chování, ve kterém je vytvořena skupina prostředků pro každý virtuální počítače v testovacím prostředí.
 
Toto nastavení platí pro nové virtuální počítače vytvořené v testovacím prostředí. Starší virtuální počítače ve vašem testovacím prostředí, které byly vytvořeny ve vlastních skupinách prostředků zůstávají nedotčeny. Prostředí, která jsou vytvořena v testovacím prostředí, zůstávají ve vlastních skupinách prostředků.

Jak používat toto rozhraní API:
- Použití verze rozhraní API **2018_10_15_preview**.
- Pokud zadáte novou skupinu prostředků, ujistěte se, že máte **oprávnění k zápisu do skupin prostředků** ve vašem předplatném. Pokud nemáte oprávnění k zápisu, vytvoření nových virtuálních počítačů v zadané skupině prostředků se nezdaří.
- Při použití rozhraní API předejte **id celé skupiny prostředků**. Například: `/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>`. Ujistěte se, že skupina prostředků je ve stejném předplatném jako testovací hod. 


## <a name="next-steps"></a>Další kroky
Viz následující články: 

- [Nastavení zásad pro testovací prostředí](devtest-lab-get-started-with-lab-policies.md)
- [Nejčastější dotazy](devtest-lab-faq.md)
