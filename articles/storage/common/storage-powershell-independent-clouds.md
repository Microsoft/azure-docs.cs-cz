---
title: Správa dat v cloudech nezávislých na Azure pomocí PowerShellu
titleSuffix: Azure Storage
description: Správa úložiště v Čínském cloudu, government cloudu a německém cloudu pomocí Azure PowerShellu.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5fa515515c06466e121a5c0ee925fd4d14245363
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74895239"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Správa úložiště v nezávislých cloudech Azure pomocí PowerShellu

Většina lidí používá Azure Public Cloud pro své globální nasazení Azure. Existují také některé nezávislé nasazení Microsoft Azure z důvodu suverenity a tak dále. Tato nezávislá nasazení se označují jako "prostředí". Následující seznam podrobně popisuje nezávislé mraky, které jsou v současné době k dispozici.

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure China 21Vianet Cloud provozovaný společností 21Vianet v Číně](http://www.windowsazure.cn/)
* [Německý cloud Azure](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Používání nezávislého cloudu

Pokud chcete Azure Storage používat v jednom z nezávislých cloudů, připojte se k tomuto cloudu místo Azure Public. Použití jednoho z nezávislých cloudů místo Azure Public:

* Můžete zadat *prostředí,* ke kterému se má připojit.
* Můžete určit a použít dostupné oblasti.
* Použijete správnou příponu koncového bodu, která se liší od Azure Public.

Příklady vyžadují modul Azure PowerShell Az verze 0.7 nebo novější. V okně Prostředí PowerShell spusťte `Get-Module -ListAvailable Az` a vyhledejte verzi. Pokud nic není v seznamu nebo potřebujete upgradovat, přečtěte si informace [o instalaci modulu Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Spusťte rutinu [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) a podívejte se na dostupná prostředí Azure:

```powershell
Get-AzEnvironment
```

Přihlaste se ke svému účtu, který má přístup ke cloudu, ke kterému se chcete připojit, a nastavte prostředí. Tento příklad ukazuje, jak se přihlásit k účtu, který používá Azure Government Cloud.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Chcete-li získat přístup k Čínskému cloudu, použijte prostředí **AzureChinaCloud**. Chcete-li získat přístup k německému cloudu, použijte **AzureGermanCloud**.

V tomto okamžiku, pokud potřebujete seznam umístění k vytvoření účtu úložiště nebo jiného prostředku, můžete dotaz umístění k dispozici pro vybraný cloud pomocí [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

V následující tabulce jsou uvedena umístění vrácená pro německý cloud.

|Umístění | Zobrazovaný název |
|----|----|
| `germanycentral` | Německo – střed|
| `germanynortheast` | Německo – severovýchod |


## <a name="endpoint-suffix"></a>Přípona koncového bodu

Přípona koncového bodu pro každé z těchto prostředí se liší od koncového bodu Azure Public. Například přípona koncového bodu objektu blob pro Azure Public je **blob.core.windows.net**. Pro government Cloud je přípona koncového bodu objektu **blob blob.core.usgovcloudapi.net**.

### <a name="get-endpoint-using-get-azenvironment"></a>Získání koncového bodu pomocí prostředí Get-AzEnvironment

Načíst příponu koncového bodu pomocí [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). Koncový bod je *StorageEndpointSuffix* vlastnost prostředí.

Následující fragmenty kódu ukazují, jak načíst příponu koncového bodu. Všechny tyto příkazy vrátí něco jako "core.cloudapp.net" nebo "core.cloudapi.de", atd. Připojte příponu ke službě úložiště pro přístup k této službě. Například "queue.core.cloudapi.de" bude přistupovat ke službě fronty v německém cloudu.

Tento fragment kódu načte všechna prostředí a příponu koncového bodu pro každou z nich.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Tento příkaz vrátí následující výsledky.

| Name (Název)| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Chcete-li načíst všechny vlastnosti pro zadané prostředí, volání **Get-AzEnvironment** a zadejte název cloudu. Tento fragment kódu vrátí seznam vlastností; vyhledejte **StorageEndpointSuffix** v seznamu. Následující příklad je pro německý cloud.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Výsledky jsou podobné následujícím hodnotám:

|Název vlastnosti|Hodnota|
|----|----|
| Name (Název) | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| Rozhraní ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| Adresa URL galerie | `https://gallery.cloudapi.de/` |
| Adresa ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| Adresa ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| Adresa PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| Adresa ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |
Chcete-li načíst jenom vlastnost koncového bodu úložiště, načtěte konkrétní cloud a požádejte o jenom jednu vlastnost.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Tento příkaz vrátí následující informace:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Získání koncového bodu z účtu úložiště

Můžete také zkontrolovat vlastnosti účtu úložiště k načtení koncových bodů:

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

Pro účet úložiště v cloudu vlády tento příkaz vrátí následující výstup:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Po nastavení prostředí

Odtud do budoucna můžete použít stejný PowerShell používaný ke správě účtů úložiště a přístup k rovině dat, jak je popsáno v článku [Použití Azure PowerShellu s Azure Storage](storage-powershell-guide-full.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste pro toto cvičení vytvořili novou skupinu prostředků a účet úložiště, můžete oba prostředky odebrat odstraněním skupiny prostředků. Odstraněním skupiny prostředků se odstraní všechny prostředky, které tato skupina obsahuje.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

* [Zachování přihlášení uživatele napříč relacemi PowerShellu](/powershell/azure/context-persistence)
* [Úložiště Azure Government](../../azure-government/documentation-government-services-storage.md)
* [Příručka pro vývojáře pro zásady pro vládní instituce Microsoft Azure](../../azure-government/documentation-government-developer-guide.md)
* [Poznámky pro vývojáře pro aplikace Azure China 21Vianet](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Dokumentace k Azure Germany](../../germany/germany-welcome.md)
