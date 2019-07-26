---
title: Vytvoření kontejneru cloudové služby pomocí PowerShellu | Microsoft Docs
description: Tento článek vysvětluje, jak vytvořit kontejner cloudové služby pomocí PowerShellu. Kontejner hostuje webové a pracovní role.
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
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359499"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Vytvoření prázdného kontejneru cloudové služby pomocí Azure PowerShell příkazu

Tento článek vysvětluje, jak rychle vytvořit kontejner Cloud Services pomocí rutin Azure PowerShell. Postupujte prosím podle následujících kroků:

1. Na stránce [Azure PowerShell ke stažení](https://aka.ms/webpi-azps) nainstalujte rutinu Microsoft Azure PowerShell.
2. Otevřete příkazový řádek prostředí PowerShell.
3. Přihlaste se pomocí [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) .

   > [!NOTE]
   > Další pokyny k instalaci rutiny Azure PowerShell a připojení k předplatnému Azure najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).
   >
   >
4. K vytvoření prázdného kontejneru cloudové služby Azure použijte rutinu **New-AzureService** .

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Použijte tento příklad k vyvolání rutiny:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Další informace o vytvoření cloudové služby Azure získáte spuštěním:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Další postup

* Pokud chcete spravovat nasazení cloudové služby, přečtěte si příkazy [Get-AzureService](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0)a [set-AzureService](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) . Můžete si také všimnout, [jak nakonfigurovat cloudové služby](cloud-services-how-to-configure-portal.md) pro další informace.
* Pokud chcete projekt cloudové služby publikovat do Azure, přečtěte si ukázku kódu **PublishCloudService. ps1** z [archivovaného úložiště Cloud Services](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).