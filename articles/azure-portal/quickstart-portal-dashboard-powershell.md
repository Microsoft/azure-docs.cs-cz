---
title: Vytvoření řídicího panelu Azure Portal pomocí prostředí PowerShell
description: Naučte se, jak vytvořit řídicí panel v Azure Portal pomocí Azure PowerShell.
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.author: mblythe
ms.date: 07/24/2020
ms.openlocfilehash: 6b7a4f6d4ad7f5e94d19b9d531992f54ff13fec0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "87440768"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>Rychlý Start: Vytvoření řídicího panelu Azure Portal pomocí prostředí PowerShell

Řídicí panel v Azure Portal je cílené a organizované zobrazení vašich cloudových prostředků. Tento článek se zaměřuje na proces použití modulu AZ. Portal PowerShell k vytvoření řídicího panelu.
Řídicí panel zobrazuje výkon virtuálního počítače a také některé statické informace a odkazy.

## <a name="requirements"></a>Požadavky

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

Pokud se rozhodnete použít prostředí PowerShell místně, Tento článek vyžaduje, abyste nainstalovali modul AZ PowerShell a připojili se k účtu Azure pomocí rutiny [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

> [!IMPORTANT]
> I když je modul PowerShellu **AZ. Portal** ve verzi Preview, musíte ho z rutiny nainstalovat samostatně z nástroje AZ PowerShell Module `Install-Module` . Až bude tento modul PowerShellu všeobecně dostupný, bude součástí budoucna k tomu, že vydává verze modulu PowerShell a jsou dostupné nativně z Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>Zvolit konkrétní předplatné Azure

Pokud máte více předplatných Azure, vyberte příslušné předplatné, ve kterém se prostředky mají fakturovat. Vyberte konkrétní předplatné pomocí rutiny [set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Definování proměnných

Opakovaně budete používat několik informací. Vytvořte proměnné pro uložení informací.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte [skupinu prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) pomocí rutiny [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina.

Následující příklad vytvoří skupinu prostředků na základě názvu v `$resourceGroupName` proměnné v oblasti určené v `$location` proměnné.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

Řídicí panel, který vytvoříte v další části tohoto rychlého startu, vyžaduje existující virtuální počítač. Pomocí následujících kroků vytvořte virtuální počítač.

Přihlašovací údaje pro virtuální počítač ukládejte do proměnné. Heslo musí být složité. Toto je nové uživatelské jméno a heslo. Nejedná se například o účet, který používáte k přihlášení do Azure. Další informace najdete v tématu [požadavky na uživatelské jméno](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) a [požadavky na heslo](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

```azurepowershell-interactive
$Cred = Get-Credential
```

Vytvořte virtuální počítač.

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

Nasazení virtuálního počítače se teď spustí a obvykle trvá několik minut, než se dokončí. Po dokončení nasazení přejděte k další části.

## <a name="download-the-dashboard-template"></a>Stažení šablony řídicího panelu

Vzhledem k tomu, že řídicí panely Azure jsou prostředky, mohou být reprezentovány jako JSON. Následující kód stáhne reprezentace ukázkového řídicího panelu ve formátu JSON. Další informace najdete v tématu [Struktura řídicích panelů Azure](/azure/azure-portal/azure-portal-dashboards-structure).

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$env:TEMP\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>Přizpůsobení šablony

Staženou šablonu si přizpůsobte spuštěním následujícího kódu.

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

Další informace najdete v referenčních informacích k [šabloně řídicích panelů portálu Microsoft Portal](/azure/templates/microsoft.portal/dashboards).

## <a name="deploy-the-dashboard-template"></a>Nasazení šablony řídicího panelu

`New-AzPortalDashboard`K nasazení šablony přímo z PowerShellu můžete použít rutinu, která je součástí modulu AZ. Portal.

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>Kontrola nasazených prostředků

Ověřte, že se řídicí panel úspěšně vytvořil.

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

Ověřte, že se v Azure Portal zobrazí data o virtuálním počítači.

1. Na webu Azure Portal vyberte **Řídicí panel**.

   ![Azure Portal navigace na řídicí panel](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. Na stránce řídicí panel vyberte **jednoduchý řídicí panel virtuálních počítačů**.

   ![Přejít na panel jednoduchého virtuálního počítače](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. Prohlédněte si řídicí panel. Můžete vidět, že část obsahu je statická, ale existují i grafy, které ukazují výkon virtuálního počítače.

   ![Kontrola jednoduchého řídicího panelu virtuálních počítačů](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odebrat virtuální počítač a přidružený řídicí panel, odstraňte skupinu prostředků, která je obsahuje.

> [!CAUTION]
> Následující příklad odstraní zadanou skupinu prostředků a všechny prostředky, které jsou v ní obsažené.
> Pokud v zadané skupině prostředků existují prostředky mimo rozsah tohoto článku, budou také odstraněny.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Další kroky

Další informace o rutinách obsažených v modulu příkazového portálu AZ. Portal PowerShell najdete v těchto tématech:

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell: rutiny řídicího panelu portálu](https://docs.microsoft.com/powershell/module/Az.Portal/)
