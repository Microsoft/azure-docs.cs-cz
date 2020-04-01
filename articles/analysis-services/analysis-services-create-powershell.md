---
title: Úvodní příručka – vytvoření služby Azure Analysis Services pomocí služby PowerShell Azure Analysis Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvořit server služby Azure Analysis Services pomocí PowerShellu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: e2b772ac060e55d21341626772667d56245542ca
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409939"
---
# <a name="quickstart-create-a-server---powershell"></a>Rychlý start: Vytvoření serveru – PowerShell

Tento rychlý start popisuje použití PowerShellu z příkazového řádku k vytvoření serveru služby Azure Analysis Services ve vašem předplatném Azure.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Předplatné Azure:** Pokud si chcete vytvořit účet, přejděte na stránku [Bezplatný zkušební verze Azure](https://azure.microsoft.com/offers/ms-azr-0044p/).
- **Azure Active Directory:** Vaše předplatné musí být přidružené k tenantovi Azure Active Directory a musíte mít účet v tomto adresáři. Další informace najdete v tématu [Ověřování a uživatelská oprávnění](analysis-services-manage-users.md).
- **Azure PowerShell**. Nainstalovanou verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud chcete provést instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="import-azanalysisservices-module"></a>Importovat modul Az.AnalysisServices

Chcete-li vytvořit server v předplatném, použijte modul [Az.AnalysisServices.](/powershell/module/az.analysisservices) Načtěte modul Az.AnalysisServices do relace prostředí PowerShell.

```powershell
Import-Module Az.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se ke svému předplatnému Azure pomocí příkazu [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

[Skupina prostředků Azure](../azure-resource-manager/management/overview.md) je logický kontejner, ve kterém se nasazují a spravují prostředky Azure jako skupina. Při vytváření serveru je potřeba zadat skupinu prostředků ve vašem předplatném. Pokud ještě nemáte skupinu prostředků, můžete vytvořit novou pomocí příkazu [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Následující příklad vytvoří skupinu prostředků `myResourceGroup` v oblasti USA – západ.

```powershell
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

## <a name="create-a-server"></a>Vytvoření serveru

Vytvořte nový server pomocí příkazu [New-AzAnalysisServicesServer.](/powershell/module/az.analysisservices/new-azanalysisservicesserver) Následující příklad vytvoří server myServer ve skupině prostředků myResourceGroup v oblasti USA – západ na úrovni D1 (bezplatná úroveň) a určí philipc@adventureworks.com jako správce serveru.

```powershell
New-AzAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myserver" -Location WestUS -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Server můžete odebrat z předplatného pomocí příkazu [Remove-AzAnalysisServicesServer.](/powershell/module/az.analysisservices/new-azanalysisservicesserver) Pokud budete pokračovat dalšími rychlými starty a kurzy v této kolekci, server neodebírejte. Následující příklad odebere server vytvořený v předchozím kroku.


```powershell
Remove-AzAnalysisServicesServer -Name "myserver" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak pomocí PowerShellu vytvořit server v předplatném Azure. Když teď máte server, můžete ho zabezpečit nakonfigurováním (volitelné) brány firewall serveru. Na server také můžete přímo z portálu přidat základní ukázkový datový model. Na ukázkovém modelu se naučíte konfigurovat role modelové databáze a testovat připojení klientů. Ve výuce pokračujte kurzem, ve kterém přidáte ukázkový model.

> [!div class="nextstepaction"]
> [Úvodní příručka: Konfigurace brány firewall serveru – portál](analysis-services-qs-firewall.md)      
> [!div class="nextstepaction"]
> [Kurz: Přidání ukázkového modelu na server](analysis-services-create-sample-model.md)
