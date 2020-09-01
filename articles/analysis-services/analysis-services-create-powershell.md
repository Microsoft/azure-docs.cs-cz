---
title: Rychlý Start – vytvoření Azure Analysis Services pomocí prostředí PowerShell Azure Analysis Services | Microsoft Docs
description: Zjistěte, jak vytvořit server služby Azure Analysis Services pomocí PowerShellu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions , devx-track-azurepowershell
ms.openlocfilehash: a57222346a69d3d92c108da9e57a1d656974b561
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2020
ms.locfileid: "89074810"
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
> [Rychlý Start: Konfigurace brány firewall serveru – portál](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Kurz: Přidání ukázkového modelu na server](analysis-services-create-sample-model.md)
