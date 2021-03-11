---
title: Vytvoření kontejneru cloudové služby (Classic) pomocí PowerShellu | Microsoft Docs
description: Tento článek vysvětluje, jak vytvořit kontejner cloudové služby pomocí PowerShellu. Kontejner hostuje webové a pracovní role.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 39fe439e37b1af4e833396ef83205729af8c7ad3
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102610411"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>Vytvoření prázdného kontejneru cloudové služby (Classic) pomocí Azure PowerShell příkazu

> [!IMPORTANT]
> [Azure Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md) je nový model nasazení založený na Azure Resource Manager pro produkt Azure Cloud Services.V důsledku této změny se Azure Cloud Services běžící na modelu nasazení založeném na Azure Service Manager přejmenovala jako Cloud Services (Classic) a všechna nová nasazení by měla používat [Cloud Services (Rozšířená podpora)](../cloud-services-extended-support/overview.md).

Tento článek vysvětluje, jak rychle vytvořit kontejner Cloud Services pomocí rutin Azure PowerShell. Postupujte následovně:

1. Na stránce [Azure PowerShell ke stažení](https://aka.ms/webpi-azps) nainstalujte rutinu Microsoft Azure PowerShell.
2. Otevřete příkazový řádek prostředí PowerShell.
3. Přihlaste se pomocí [Add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount) .

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

* Pokud chcete spravovat nasazení cloudové služby, přečtěte si příkazy [Get-AzureService](/powershell/module/servicemanagement/azure.service/Get-AzureService), [Remove-AzureService](/powershell/module/servicemanagement/azure.service/Remove-AzureService)a [set-AzureService](/powershell/module/servicemanagement/azure.service/set-azureservice) . Můžete si také všimnout, [jak nakonfigurovat cloudové služby](cloud-services-how-to-configure-portal.md) pro další informace.
* Pokud chcete publikovat projekt cloudové služby do Azure, přečtěte si ukázku kódu  **PublishCloudService.ps1** z [archivovaného úložiště Cloud Services](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).