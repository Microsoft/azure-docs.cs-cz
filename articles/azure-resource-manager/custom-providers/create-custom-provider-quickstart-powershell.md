---
title: Vytvoření vlastního poskytovatele prostředků Azure pomocí Azure PowerShell
description: Popisuje, jak vytvořit vlastního poskytovatele prostředků Azure pomocí Azure PowerShell
author: MSEvanhi
ms.author: evanhi
ms.topic: quickstart
ms.devlang: azurepowershell
ms.date: 09/22/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9c921541cc1f27eb7a9af186c25346f101ba65d2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91348900"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Rychlý Start: Vytvoření vlastního poskytovatele prostředků Azure pomocí Azure PowerShell

V tomto rychlém startu se dozvíte, jak vytvořit vlastního poskytovatele prostředků Azure pomocí modulu [AZ. CustomProviders](/powershell/module/az.customproviders) PowerShell.

> [!CAUTION]
> Vlastní zprostředkovatelé Azure jsou momentálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb. Nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud se rozhodnete použít prostředí PowerShell místně, Tento článek vyžaduje, abyste nainstalovali modul AZ PowerShell a připojili se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell](/powershell/azure/install-az-ps). Pokud se rozhodnete použít Cloud Shell, přečtěte si téma [přehled Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) , kde najdete další informace.

> [!IMPORTANT]
> I když je modul PowerShell **AZ. CustomProviders** ve verzi Preview, musíte ho nainstalovat samostatně pomocí `Install-Module` rutiny. Až bude tento modul PowerShellu všeobecně dostupný, bude součástí budoucna k tomu, že vydává verze modulu PowerShell a jsou dostupné nativně z Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Vyberte konkrétní předplatné pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../../azure-resource-manager/management/overview.md) pomocí rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina.

Následující příklad vytvoří skupinu prostředků se zadaným názvem a v zadaném umístění.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Vytvoření vlastního poskytovatele prostředků

Pokud chcete vytvořit nebo aktualizovat vlastního poskytovatele prostředků, použijte rutinu [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider) , jak je znázorněno v následujícím příkladu.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Získání vlastního manifestu poskytovatele prostředků

Chcete-li načíst informace o vlastním manifestu poskytovatele prostředků, použijte rutinu [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider) , jak je znázorněno v následujícím příkladu.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>Vytvořit přidružení

Pokud chcete vytvořit nebo aktualizovat přidružení, použijte rutinu [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation) , jak je znázorněno v následujícím příkladu.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>Získat přidružení

Chcete-li načíst informace o přidružení, použijte rutinu [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation) , jak je znázorněno v následujícím příkladu.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud prostředky vytvořené v tomto článku nepotřebujete, můžete je odstranit spuštěním následujících příkladů.

### <a name="delete-an-association"></a>Odstraní přidružení.

Pokud chcete přidružení odebrat, použijte rutinu [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation) . Následující příklad odstraní přidružení.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Odstranění vlastního poskytovatele prostředků

Pokud chcete odebrat vlastního poskytovatele prostředků, použijte rutinu [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider) . Následující příklad odstraní vlastního poskytovatele prostředků.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

> [!CAUTION]
> Následující příklad odstraní zadanou skupinu prostředků a všechny prostředky, které jsou v ní obsažené.
> Pokud v zadané skupině prostředků existují prostředky mimo rozsah tohoto článku, budou také odstraněny.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [vlastních poskytovatelích prostředků Azure](overview.md).
