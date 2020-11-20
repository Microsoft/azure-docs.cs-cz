---
title: Nasazení Azure Monitor pro řešení SAP pomocí Azure PowerShell
description: Nasazení Azure Monitor pro řešení SAP pomocí Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.topic: quickstart
ms.service: virtual-machines
ms.subservice: workloads
ms.devlang: azurepowershell
ms.date: 09/08/2020
ms.reviewer: cynthn
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 28b26b8d4d6acdffb4083d182c6a0ce0539080c9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957363"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Rychlý Start: nasazení Azure Monitor pro řešení SAP pomocí Azure PowerShell

Tento článek popisuje, jak můžete vytvořit Azure Monitor pro prostředky řešení SAP pomocí modulu [AZ. HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure) PowerShell.

> [!CAUTION]
> Azure Monitor pro řešení SAP jsou momentálně ve verzi Public Preview. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb. Nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="requirements"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud se rozhodnete použít prostředí PowerShell místně, Tento článek vyžaduje, abyste nainstalovali modul AZ PowerShell a připojili se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell](/powershell/azure/install-az-ps). Pokud se rozhodnete použít Cloud Shell, přečtěte si téma [přehled Azure Cloud Shell](../../../cloud-shell/overview.md) , kde najdete další informace.

> [!IMPORTANT]
> I když je modul PowerShell **AZ. HanaOnAzure** ve verzi Preview, musíte ho nainstalovat samostatně pomocí `Install-Module` rutiny. Až bude tento modul PowerShellu všeobecně dostupný, bude součástí budoucna k tomu, že vydává verze modulu PowerShell a jsou dostupné nativně z Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Vyberte konkrétní předplatné pomocí rutiny [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](../../../azure-resource-manager/management/overview.md) pomocí rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina.

Následující příklad vytvoří skupinu prostředků se zadaným názvem a v zadaném umístění.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>Sledování SAP

Chcete-li vytvořit monitorování SAP, použijte rutinu [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor) . Následující příklad vytvoří monitor SAP pro zadané předplatné, skupinu prostředků a název prostředku.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

Pokud chcete načíst vlastnosti monitorování SAP, použijte rutinu [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor) . Následující příklad načte vlastnosti monitorování SAP pro zadané předplatné, skupinu prostředků a název prostředku.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Instance poskytovatele

Chcete-li vytvořit instanci poskytovatele, použijte rutinu [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance) . Následující příklad vytvoří instanci poskytovatele pro zadané předplatné, skupinu prostředků a název prostředku.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

Chcete-li načíst vlastnosti instance poskytovatele, použijte rutinu [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance) . Následující příklad načte vlastnosti instance poskytovatele pro zadané předplatné, skupinu prostředků, název SapMonitor a název prostředku.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud prostředky vytvořené v tomto článku nepotřebujete, můžete je odstranit spuštěním následujících příkladů.

### <a name="delete-the-provider-instance"></a>Odstraní instanci poskytovatele.

Pokud chcete odebrat instanci poskytovatele, použijte rutinu [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance) . Následující příklad odstraní instanci poskytovatele pro zadané předplatné, skupinu prostředků, název SapMonitor a název prostředku.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>Odstranění sledování SAP

Chcete-li odebrat monitor SAP, použijte rutinu [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor) . Následující příklad odstraní monitorování SAP pro zadané předplatné, skupinu prostředků a název monitorování.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>Odstranění skupiny prostředků

> [!CAUTION]
> Následující příklad odstraní zadanou skupinu prostředků a všechny prostředky, které jsou v ní obsažené.
> Pokud v zadané skupině prostředků existují prostředky mimo rozsah tohoto článku, budou také odstraněny.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [Azure monitor pro řešení SAP](azure-monitor-overview.md).