---
title: Vytvoření kontejneru cloudových služeb pomocí PowerShellu | Dokumenty společnosti Microsoft
description: Tento článek vysvětluje, jak vytvořit kontejner cloudové služby s PowerShellem. Kontejner hostuje webové a pracovní role.
services: cloud-services
documentationcenter: .net
author: cawaMS
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: ef9d3c7c479df9c71a855f0a243b5b9d0da947d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359499"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Vytvoření prázdného kontejneru cloudové služby pomocí příkazu Azure PowerShell

Tento článek vysvětluje, jak rychle vytvořit kontejner cloudových služeb pomocí rutin Azure PowerShell. Postupujte následovně:

1. Nainstalujte rutinu Microsoft Azure PowerShell na stránce [ke stažení Azure PowerShellu.](https://aka.ms/webpi-azps)
2. Otevřete příkazový řádek Prostředí PowerShell.
3. Přihlaste se pomocí [účtu Add-AzureAccount.](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0)

   > [!NOTE]
   > Další informace o instalaci rutiny Azure PowerShell a připojení k předplatnému Azure najdete v části [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Pomocí rutiny **New-AzureService** vytvořte prázdný kontejner cloudových služeb Azure.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Postupujte podle tohoto příkladu pro vyvolání rutiny:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Další informace o vytváření cloudové služby Azure nastoňte takto:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Další kroky

* Chcete-li spravovat nasazení cloudové služby, podívejte se na příkazy [Get-AzureService](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0)a [Set-AzureService.](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) Další informace naleznete také v návodu [ke konfiguraci cloudových služeb.](cloud-services-how-to-configure-portal.md)
* Pokud chcete publikovat projekt cloudových služeb do Azure, podívejte se na ukázku kódu **PublishCloudService.ps1** z [archivovaného úložiště cloudových služeb](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).