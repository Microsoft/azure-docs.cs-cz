---
title: Správa úložiště v Azure nezávisle cloudů pomocí Azure PowerShell | Microsoft Docs
description: Správa úložiště v cloudu Čína, Cloud vlády a německé cloudu pomocí Azure PowerShell
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: rogarana
ms.openlocfilehash: 3eecf8918e6628071e44cc588b1826df68a00f10
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32186906"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Správa úložiště v Azure nezávislé cloudy pomocí prostředí PowerShell

Většina lidí používá veřejném cloudu Azure pro jejich globální nasazení Azure. Existují také některé nezávislé nasazení Microsoft Azure z důvodů suverenity a tak dále. Tato nezávislé nasazení jsou označovány jako "prostředí." V následujícím seznamu jsou aktuálně k dispozici nezávislé cloudy.

* [Cloud vlády Azure](https://azure.microsoft.com/features/gov/)
* [Čína cloudu Azure provozované v Číně společností 21Vianet](http://www.windowsazure.cn/)
* [Němčině cloudu Azure](../../germany/germany-welcome.md)

## <a name="using-an-independent-cloud"></a>Pomocí nezávislé cloudu 

Používání Azure Storage v jednom z nezávislé cloudy, připojit s tímto cloudem místo Azure veřejné. Použít nezávislé cloudy místo veřejný Azure:

* Zadáte *prostředí* pro připojení.
* Můžete určit a používat dostupné oblasti.
* Použijete příponě správný koncový bod, který se liší od Azure veřejné.

Příklady vyžadují prostředí Azure PowerShell verze modulu 4.4.0 nebo novější. V okně prostředí PowerShell, spusťte `Get-Module -ListAvailable AzureRM` najít verzi. Nic uvedeno, zda je nutné upgradovat, najdete v části [modul nainstalovat Azure PowerShell](/powershell/azure/install-azurerm-ps). 

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Spustit [Get-AzureEnvironment](/powershell/module/azure/Get-AzureRmEnvironment) rutiny zobrazíte dostupné prostředí Azure:
   
```powershell
Get-AzureRmEnvironment
```

Přihlaste se ke svému účtu, který má přístup do cloudu, ke kterému chcete připojit a nastavte prostředí. Tento příklad ukazuje, jak se přihlaste účtu, který používá cloudu Azure Government.   

```powershell
Connect-AzureRmAccount –Environment AzureUSGovernment
```

Pro přístup k cloudu Čína, použijte prostředí **AzureChinaCloud**. Chcete-li získat přístup k cloudu němčina, použijte **AzureGermanCloud**.

Nyní, pokud potřebujete seznam umístění k vytvoření účtu úložiště nebo jiný prostředek, můžete dát dotaz na umístění, které jsou k dispozici pro použití vybraný cloud [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation).

```powershell
Get-AzureRmLocation | select Location, DisplayName
```

Následující tabulka uvádí umístění vrátil němčině cloudu.

|Umístění | displayName |
|----|----|
| germanycentral | Německo – střed|
| germanynortheast | Německo – severovýchod | 


## <a name="endpoint-suffix"></a>Koncový bod příponu

Přípona koncový bod pro každou z těchto prostředích se liší od Azure veřejný koncový bod. Je například přípona koncový bod objektu blob Azure veřejné **blob.core.windows.net**. Pro Cloud vlády přípona koncový bod objektu blob je **blob.core.usgovcloudapi.net**. 

### <a name="get-endpoint-using-get-azurermenvironment"></a>Získání koncového bodu pomocí Get-AzureRMEnvironment 

Načíst pomocí příponu koncový bod [Get-AzureRMEnvironment](/powershell/module/azurerm.profile/get-azurermenvironment). Koncový bod je *StorageEndpointSuffix* vlastnost prostředí. Následující fragmenty kódu ukazují, jak to udělat. Všechny tyto příkazy vrátí něco "core.cloudapp.net" nebo "core.cloudapi.de", atd. To umožňuje připojte ke službě úložiště pro přístup k této službě. Například "queue.core.cloudapi.de" se přístup ke službě fronty v cloudu němčina.

Tento fragment kódu načte všechny prostředí a příponou koncový bod pro každé z nich.

```powershell
Get-AzureRmEnvironment | select Name, StorageEndpointSuffix 
```

Tento příkaz vrátí následující výsledky.

| Název| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | Core.usgovcloudapi.NET |

Chcete-li načíst všechny vlastnosti pro zadaného prostředí, volejte **Get-AzureRmEnvironment** a zadejte název cloudu. Tento fragment kódu vrátí seznam hodnot vlastnosti; Vyhledejte **StorageEndpointSuffix** v seznamu. V následujícím příkladu se pro Cloud němčina.

```powershell
Get-AzureRmEnvironment -Name AzureGermanCloud 
```

Výsledky budou vypadat takto:

|Název vlastnosti|Hodnota|
|----|----|
| Název | AzureGermanCloud |
| EnableAdfsAuthentication | False |
| ActiveDirectoryServiceEndpointResourceI | http://management.core.cloudapi.de/ |
| GalleryURL | https://gallery.cloudapi.de/ |
| ManagementPortalUrl | https://portal.microsoftazure.de/ | 
| ServiceManagementUrl | https://manage.core.cloudapi.de/ |
| PublishSettingsFileUrl| https://manage.microsoftazure.de/publishsettings/index |
| ResourceManagerUrl | http://management.microsoftazure.de/ |
| SqlDatabaseDnsSuffix | .database.cloudapi.de |
| **StorageEndpointSuffix** | core.cloudapi.de |
| ... | ... | 

Načtení právě vlastnosti přípony úložiště koncový bod, načíst konkrétní cloudové a požádejte o právě jeden vlastnosti.

```powershell
$environment = Get-AzureRmEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix 
```

Tento příkaz vrátí následující informace.

```
Storage Endpoint Suffix = core.cloudapi.de
```

### <a name="get-endpoint-from-a-storage-account"></a>Získání koncového bodu z účtu úložiště

Můžete také zkontrolovat vlastnosti účtu úložiště načíst koncových bodů. To může být užitečné, pokud už používáte účet úložiště ve vašem skriptu prostředí PowerShell; právě můžete načíst koncový bod, které potřebujete. 

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

Pro účet úložiště v cloudu Government vrátí následující: 

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Po nastavení prostředí

Z zde do budoucna, můžete použít stejné PowerShell použít ke správě účtů úložiště a datové roviny přístup, jak je popsáno v článku [použití Azure Powershellu s Azure Storage](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili novou skupinu prostředků a účet úložiště pro toto cvičení, všechny prostředky můžete odebrat odstraněním skupiny prostředků. Tím se odstraní také všechny prostředky, které skupina obsahuje. V takovém případě odebere účtu úložiště vytvořeném a skupině prostředků, sám sebe.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další postup

* [Zachování přihlášení uživatelů mezi relace prostředí PowerShell](/powershell/azure/context-persistence)
* [Úložiště Azure Government](../../azure-government/documentation-government-services-storage.md)
* [Příručka vývojáře pro službu Microsoft Azure Government](../../azure-government/documentation-government-developer-guide.md)
* [Poznámky pro vývojáře pro Azure Číně aplikace](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Dokumentace Azure Německo](../../germany/germany-welcome.md)
