---
title: Vytvoření serveru služby Azure Analysis Services pomocí PowerShellu | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit server služby Azure Analysis Services pomocí PowerShellu.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3f0d3ae6786e9f63f0e4eb025118d0d217eced64
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="create-an-azure-analysis-services-server-by-using-powershell"></a>Vytvoření serveru služby Azure Analysis Services pomocí PowerShellu

Tento rychlý start popisuje použití PowerShellu z příkazového řádku k vytvoření serveru Azure Analysis Services ve [skupině prostředků Azure](../azure-resource-manager/resource-group-overview.md) ve vašem předplatném Azure.

Tato úloha vyžaduje modul Azure PowerShell verze 4.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud chcete provést instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

> [!NOTE]
> Vytvoření serveru může znamenat, že se vám začne fakturovat nová služba. Další informace najdete v tématu [Ceny služby Azure Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="before-you-begin"></a>Než začnete
K dokončení tohoto rychlého startu je potřeba:

* **Předplatné Azure:** Pokud si chcete vytvořit účet, přejděte na stránku [Bezplatný zkušební verze Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).
* **Azure Active Directory:** Vaše předplatné musí být přidružené k tenantovi Azure Active Directory a musíte mít účet v tomto adresáři. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md).

## <a name="import-azurermanalysisservices-module"></a>Import modulu AzureRm.AnalysisServices
K vytvoření serveru ve vašem předplatném můžete použít modul komponenty [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Načtěte modul AzureRm.AnalysisServices do relace PowerShellu.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) příkaz. Postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[Skupina prostředků Azure](../azure-resource-manager/resource-group-overview.md) je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina. Při vytváření serveru je potřeba zadat skupinu prostředků ve vašem předplatném. Pokud skupinu prostředků ještě nemáte, můžete vytvořit novou pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Následující příklad vytvoří skupinu prostředků `myResourceGroup` v oblasti USA – západ.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
```

## <a name="create-a-server"></a>Vytvoření serveru

Vytvořte nový server pomocí příkazu [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Následující příklad vytvoří server myServer ve skupině prostředků myResourceGroup v oblasti USA – západ na úrovni D1 a určí philipc@adventureworks.com jako správce serveru.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myServer" -Location West US -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Server můžete z předplatného odebrat pomocí příkazu [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Pokud budete pokračovat dalšími rychlými starty a kurzy v této kolekci, server neodebírejte. Následující příklad odebere server vytvořený v předchozím kroku.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myServer" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Další postup
[Správa Azure Analysis Services pomocí prostředí PowerShell](analysis-services-powershell.md)
[nasadit model z rozšíření SSDT](analysis-services-deploy.md)
[vytvoření modelu na portálu Azure](analysis-services-create-model-portal.md)
