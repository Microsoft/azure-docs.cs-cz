---
title: Vytvořte kontejner cloudové služby pomocí prostředí PowerShell | Microsoft Docs
description: Tento článek vysvětluje, jak vytvořit kontejner cloudové služby pomocí prostředí PowerShell. Kontejner hostuje webové a pracovní role.
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: ''
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: b55a0dd7800448c50897af784092b4a60fa7a25e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29376129"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Použít příkaz prostředí Azure PowerShell k vytvoření kontejner prázdný cloudové služby
Tento článek vysvětluje, jak rychle vytvořit kontejner cloudové služby pomocí rutin prostředí Azure PowerShell. Postupujte podle následujících kroků:

1. Nainstalovat rutiny Powershellu pro Microsoft Azure z [prostředí Azure PowerShell stáhne](http://aka.ms/webpi-azps) stránky.
2. Otevřete příkazový řádek prostředí PowerShell.
3. Použití [Add-AzureAccount](/powershell/module/Azure/Add-AzureAccount?view=azuresmps-4.0.0) k přihlášení.

   > [!NOTE]
   > Další pokyny k instalaci rutiny Azure Powershellu a připojení k předplatnému Azure, najdete v části [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Použití **New-AzureService** vytvořte kontejner prázdný Azure cloud service.

   ```powershell
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Použijte tento příklad k vyvolání rutiny:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Další informace o vytváření Azure cloudové služby spusťte příkaz:

```
Get-help New-AzureService
```

### <a name="next-steps"></a>Další postup
* Ke správě nasazení cloudové služby, naleznete [Get-AzureService](/powershell/module/Azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/Azure/Remove-AzureService?view=azuresmps-4.0.0), a [Set-AzureService](/powershell/module/azure/set-azureservice?view=azuresmps-4.0.0) příkazy. Můžete také použít odkaz na [postup konfigurace cloudové služby](cloud-services-how-to-configure-portal.md) Další informace.
* Publikovat projekt cloudové služby Azure, najdete v tématu **PublishCloudService.ps1** ukázka kódu z [archivovaný cloudové služby úložiště](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).
