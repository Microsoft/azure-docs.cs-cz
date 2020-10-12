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
ms.openlocfilehash: d40a5b64cc8018f45bf08158ce808b2baae27962
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87049081"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Vytvoření prázdného kontejneru cloudové služby pomocí Azure PowerShell příkazu

Tento článek vysvětluje, jak rychle vytvořit kontejner Cloud Services pomocí rutin Azure PowerShell. Postupujte následovně:

1. Na stránce [Azure PowerShell ke stažení](https://aka.ms/webpi-azps) nainstalujte rutinu Microsoft Azure PowerShell.
2. Otevřete příkazový řádek prostředí PowerShell.
3. Přihlaste se pomocí [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount?view=azuresmps-4.0.0) .

   > [!NOTE]
   > Další pokyny k instalaci rutiny Azure PowerShell a připojení k předplatnému Azure najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/).
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

### <a name="next-steps"></a>Další kroky

* Pokud chcete spravovat nasazení cloudové služby, přečtěte si příkazy [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService?view=azuresmps-4.0.0)a [set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice?view=azuresmps-4.0.0) . Můžete si také všimnout, [jak nakonfigurovat cloudové služby](cloud-services-how-to-configure-portal.md) pro další informace.
* Pokud chcete publikovat projekt cloudové služby do Azure, přečtěte si ukázku kódu  **PublishCloudService.ps1** z [archivovaného úložiště Cloud Services](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).