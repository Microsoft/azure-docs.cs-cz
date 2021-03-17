---
title: Použití PowerShellu ke správě dat v nezávislých cloudech Azure
titleSuffix: Azure Storage
description: Správa úložiště v čínském cloudu, v cloudu pro státní správu a v německém cloudu pomocí Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e924a5f6c765b5b964fe3b1492393b063d9d23b4
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92783568"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Správa úložiště v nezávislých cloudech Azure pomocí PowerShellu

Většina lidí používá ke globálnímu nasazení Azure veřejný cloud Azure. Existují také některá nezávislá nasazení Microsoft Azure z důvodů svrchovanosti a tak dále. Tato nezávislá nasazení se označují jako "prostředí". Následující seznam podrobně popisuje aktuálně dostupné nezávislé cloudy.

* [Cloud Azure Government](https://azure.microsoft.com/features/gov/)
* [Azure Čína 21Vianet Cloud provozovaný společností 21Vianet v Číně](http://www.windowsazure.cn/)
* [Cloud pro Azure němčina](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Používání nezávislého cloudu

Pokud chcete použít Azure Storage v jednom z nezávislých cloudů, připojíte se k tomuto cloudu místo veřejného Azure. Použití jednoho z nezávislých cloudů, nikoli Azure Public:

* Určíte *prostředí* , ke kterému se chcete připojit.
* Určíte a použijete dostupné oblasti.
* Použijete správnou příponu koncového bodu, která se liší od veřejné služby Azure.

Příklady vyžadují Azure PowerShell modul AZ verze 0,7 nebo novější. V okně PowerShellu spusťte příkaz `Get-Module -ListAvailable Az` a vyhledejte verzi. Pokud není nic uvedeno nebo potřebujete provést upgrade, přečtěte si téma [Install Azure PowerShell Module](/powershell/azure/install-Az-ps).

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Spuštěním rutiny [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) zobrazte dostupná prostředí Azure:

```powershell
Get-AzEnvironment
```

Přihlaste se ke svému účtu, který má přístup ke cloudu, ke kterému se chcete připojit, a nastavte prostředí. Tento příklad ukazuje, jak se přihlásit k účtu, který používá Azure Government Cloud.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Pro přístup k čínskému cloudu použijte prostředí **AzureChinaCloud** . Pokud chcete získat přístup k německému cloudu, použijte **AzureGermanCloud** .

Pokud v tomto okamžiku potřebujete seznam umístění pro vytvoření účtu úložiště nebo jiného prostředku, můžete zadat dotaz na umístění dostupná pro vybraný Cloud pomocí příkazu [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

V následující tabulce jsou uvedena umístění, která jsou vrácena pro německý Cloud.

|Umístění | Zobrazovaný název |
|----|----|
| `germanycentral` | Německo – střed|
| `germanynortheast` | Německo – severovýchod |


## <a name="endpoint-suffix"></a>Přípona koncového bodu

Přípona koncového bodu pro každé z těchto prostředí se liší od veřejného koncového bodu Azure. Například přípona koncového bodu objektu BLOB pro Azure Public je **BLOB.Core.Windows.NET** . V případě cloudu pro státní správu je přípona koncového bodu objektu BLOB **BLOB.Core.usgovcloudapi.NET** .

### <a name="get-endpoint-using-get-azenvironment"></a>Získat koncový bod pomocí Get-AzEnvironment

Načtěte příponu koncového bodu pomocí [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). Koncový bod je vlastnost *StorageEndpointSuffix* prostředí.

Následující fragmenty kódu ukazují, jak načíst příponu koncového bodu. Všechny tyto příkazy vrátí něco jako "core.cloudapp.net" nebo "core.cloudapi.de" atd. Pro přístup k této službě přidejte příponu ke službě úložiště. Například "queue.core.cloudapi.de" bude přistupovat ke službě Queue v německém cloudu.

Tento fragment kódu načte všechna prostředí a příponu koncového bodu pro každé z nich.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Tento příkaz vrátí následující výsledky.

| Název| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Chcete-li načíst všechny vlastnosti pro zadané prostředí, zavolejte **Get-AzEnvironment** a zadejte název cloudu. Tento fragment kódu vrátí seznam vlastností; v seznamu vyhledejte **StorageEndpointSuffix** . Následující příklad je pro německý Cloud.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Výsledky jsou podobné následujícím hodnotám:

|Název vlastnosti|Hodnota|
|----|----|
| Název | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalleryURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |

Pokud chcete načíst jenom vlastnost přípona koncového bodu úložiště, načtěte konkrétní Cloud a vyžádejte si jenom jednu vlastnost.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Tento příkaz vrátí následující informace:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Získat koncový bod z účtu úložiště

Můžete také prostudovat vlastnosti účtu úložiště a načíst koncové body:

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

V případě účtu úložiště v cloudu pro státní správu vrátí tento příkaz následující výstup:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Po nastavení prostředí

Pomocí PowerShellu teď můžete spravovat účty úložiště a přistupovat k datům objektů blob, front, souborů a tabulek. Další informace najdete v tématu [AZ. Storage](/powershell/module/az.storage).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste pro toto cvičení vytvořili novou skupinu prostředků a účet úložiště, můžete odebrat oba prostředky odstraněním skupiny prostředků. Odstraněním skupiny prostředků se odstraní všechny prostředky, které tato skupina obsahuje.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

* [Zachování přihlášení uživatele napříč relacemi PowerShellu](/powershell/azure/context-persistence)
* [Azure Government úložiště](../../azure-government/compare-azure-government-global-azure.md)
* [Microsoft Azure Government příručka pro vývojáře](../../azure-government/documentation-government-developer-guide.md)
* [Poznámky pro vývojáře pro aplikace Azure Čína 21Vianet](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Dokumentace ke službě Azure Německo](../../germany/germany-welcome.md)