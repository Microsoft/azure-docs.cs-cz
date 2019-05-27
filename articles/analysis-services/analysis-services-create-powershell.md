---
title: Rychlý start – Vytvoření serveru služby Azure Analysis Services pomocí PowerShellu | Microsoft Docs
description: Zjistěte, jak vytvořit server služby Azure Analysis Services pomocí PowerShellu.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 01/09/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b3b23509197dd1d91dbd74c57b3c732151c51a97
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "66142984"
---
# <a name="quickstart-create-a-server---powershell"></a>Rychlý start: Vytvoření serveru – PowerShell

Tento rychlý start popisuje použití PowerShellu z příkazového řádku k vytvoření serveru služby Azure Analysis Services ve vašem předplatném Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure**: Navštivte [bezplatnou zkušební verzi Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) vytvoření účtu služby.
- **Azure Active Directory**: Předplatné musí být přidružen k tenantovi Azure Active Directory a účet musí mít v tomto adresáři. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md).
- **Azure PowerShell**. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud chcete provést instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="import-azanalysisservices-module"></a>Import Az.AnalysisServices module

K vytvoření serveru v rámci vašeho předplatného, můžete použít [Az.AnalysisServices](/powershell/module/az.analysisservices) modulu. Načtení modulu Az.AnalysisServices do relace prostředí PowerShell.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se ke svému předplatnému Azure pomocí [připojit AzAccount](/powershell/module/az.accounts/connect-azaccount) příkazu. Postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Vytvořit skupinu prostředků

[Skupina prostředků Azure](../azure-resource-manager/resource-group-overview.md) je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina. Při vytváření serveru je potřeba zadat skupinu prostředků ve vašem předplatném. Pokud ještě nemáte skupinu prostředků, můžete vytvořit novou pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) příkazu. Následující příklad vytvoří skupinu prostředků `myResourceGroup` v oblasti Západní USA.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Vytvoření serveru

Vytvořit nový server s použitím [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) příkazu. Následující příklad vytvoří server myServer ve skupině prostředků myResourceGroup v oblasti USA – západ na úrovni D1 (bezplatná úroveň) a určí philipc@adventureworks.com jako správce serveru.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Server můžete z předplatného odebrat pomocí [odebrat AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) příkazu. Pokud budete pokračovat dalšími rychlými starty a kurzy v této kolekci, server neodebírejte. Následující příklad odebere server vytvořený v předchozím kroku.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak pomocí PowerShellu vytvořit server v předplatném Azure. Když teď máte server, můžete ho zabezpečit nakonfigurováním (volitelné) brány firewall serveru. Na server také můžete přímo z portálu přidat základní ukázkový datový model. Na ukázkovém modelu se naučíte konfigurovat role modelové databáze a testovat připojení klientů. Ve výuce pokračujte kurzem, ve kterém přidáte ukázkový model.

> [!div class="nextstepaction"]
> [Rychlé zprovoznění: Konfigurace brány firewall serveru – portál](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Kurz: Přidání ukázkový model na server](analysis-services-create-sample-model.md)
