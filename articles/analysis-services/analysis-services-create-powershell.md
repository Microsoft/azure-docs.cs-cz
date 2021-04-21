---
title: Rychlý Start – vytvoření Azure Analysis Services pomocí prostředí PowerShell Azure Analysis Services | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak vytvořit Azure Analysis Services Server pomocí PowerShellu.
author: minewiskan
ms.author: owend
ms.reviewer: minewiskan
ms.date: 08/31/2020
ms.topic: quickstart
ms.service: azure-analysis-services
ms.custom: devx-track-azurepowershell - references_regions - devx-track-azurepowershell - mode-api
ms.openlocfilehash: f1902a7192fc2153725e2b1d1e76f23cbd1426f3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769314"
---
# <a name="quickstart-create-a-server---powershell"></a>Rychlý start: Vytvoření serveru – PowerShell

Tento rychlý start popisuje použití PowerShellu z příkazového řádku k vytvoření serveru služby Azure Analysis Services ve vašem předplatném Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**: Pokud chcete vytvořit účet, přejděte na [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) .
- **Azure Active Directory:** Vaše předplatné musí být přidružené k tenantovi Azure Active Directory a musíte mít účet v tomto adresáři. Další informace najdete v článku o [ověřování a uživatelských oprávněních](analysis-services-manage-users.md).
- **Azure PowerShell.** Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud chcete provést instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="import-azanalysisservices-module"></a>Import AZ. AnalysisServices Module

Pokud chcete v předplatném vytvořit server, použijte modul [AZ. AnalysisServices](/powershell/module/az.analysisservices) . Načtěte modul AZ. AnalysisServices do relace PowerShellu.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[Skupina prostředků Azure](../azure-resource-manager/management/overview.md) je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina. Při vytváření serveru je potřeba zadat skupinu prostředků ve vašem předplatném. Pokud ještě nemáte skupinu prostředků, můžete vytvořit novou pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Následující příklad vytvoří skupinu prostředků `myResourceGroup` v oblasti USA – západ.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Vytvoření serveru

Pomocí příkazu [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) vytvořte nový server. Následující příklad vytvoří server myServer ve skupině prostředků myResourceGroup v oblasti USA – západ na úrovni D1 (bezplatná úroveň) a určí philipc@adventureworks.com jako správce serveru.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Server můžete z předplatného odebrat pomocí příkazu [Remove-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) . Pokud budete pokračovat dalšími rychlými starty a kurzy v této kolekci, server neodebírejte. Následující příklad odebere server vytvořený v předchozím kroku.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak pomocí PowerShellu vytvořit server v předplatném Azure. Když teď máte server, můžete ho zabezpečit nakonfigurováním (volitelné) brány firewall serveru. Na server také můžete přímo z portálu přidat základní ukázkový datový model. Na ukázkovém modelu se naučíte konfigurovat role modelové databáze a testovat připojení klientů. Ve výuce pokračujte kurzem, ve kterém přidáte ukázkový model.

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Konfigurace brány firewall serveru – portál](analysis-services-qs-firewall.md)      
