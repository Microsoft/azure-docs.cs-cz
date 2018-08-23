---
title: Vytvoření kontejneru cloudové služby pomocí prostředí PowerShell | Dokumentace Microsoftu
description: Tento článek vysvětluje postup vytvoření kontejneru cloudové služby pomocí prostředí PowerShell. Kontejner je hostitelem webových a pracovních rolí.
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
ms.openlocfilehash: 0a05b4fadf2cd7e794680907d39f355882a4330c
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054818"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Použít příkaz prostředí Azure PowerShell k vytvoření prázdného kontejneru cloudové služby
Tento článek vysvětluje, jak rychle vytvořit kontejner cloudové služby pomocí rutin prostředí Azure PowerShell. Postupujte podle následujících kroků:

1. Nainstalujte rutiny prostředí Azure PowerShell z [stáhne prostředí Azure PowerShell](http://aka.ms/webpi-azps) stránky.
2. Otevřete příkazový řádek Powershellu.
3. Použití [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) k přihlášení.

   > [!NOTE]
   > Další pokyny k instalaci rutiny Azure Powershellu a připojení k předplatnému Azure, najdete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).
   >
   >
4. Použití **New-AzureService** rutina pro vytvoření kontejneru služby prázdný cloudu Azure.

   ```powershell
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Postupujte podle tohoto příkladu, který má být vyvolán rutinu:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Další informace o vytváření cloudové služby Azure spusťte:

```
Get-help New-AzureService
```

### <a name="next-steps"></a>Další postup
* Chcete-li spravovat nasazení cloudové služby, přečtěte si [Get-AzureService](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0), a [Set-AzureService](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) příkazy. Můžete také použít k [postup konfigurace služby cloud services](cloud-services-how-to-configure-portal.md) pro další informace.
* Chcete-li publikovat projekt cloudové služby Azure, přečtěte si **PublishCloudService.ps1** vzorového kódu z [úložiště archivované cloud services](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).
