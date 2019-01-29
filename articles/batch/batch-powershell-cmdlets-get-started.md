---
title: Začínáme s prostředím PowerShell – Azure Batch | Dokumentace Microsoftu
description: Rychlý úvod do rutin prostředí Azure PowerShell, jejichž pomocí lze spravovat prostředky služby Batch
services: batch
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 01/15/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 3ceb7585a26290985cd5a2c523ad8094b1d1a40a
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55194232"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Správa prostředků služby Batch pomocí rutin PowerShellu

Pomocí rutin PowerShellu pro službu Azure Batch můžete provádět a převádět na skripty řadu úloh, které se provádějí pomocí rozhraní API služby Batch, webu Azure Portal a rozhraní příkazového řádku (CLI) Azure. Tento článek obsahuje rychlý úvod do rutin, s jejichž pomocí můžete spravovat účty Batch a pracovat s prostředky služby Batch, jako jsou fondy, úlohy a úkoly.

Úplný seznam rutin prostředí Batch a podrobný popis syntaxe rutin najdete v článku [Rutiny služby Azure Batch – reference](/powershell/module/az.batch).

Tento článek je založen na rutiny v modulu Az Batch 1.0.0. Moduly Azure PowerShellu doporučujeme pravidelně aktualizovat, abyste mohli využívat výhody, které vám přinášejí aktualizace a vylepšení služby.

## <a name="prerequisites"></a>Požadavky

* [Nainstalujte a nakonfigurujte modul Azure PowerShellu](/powershell/azure/overview). Pokud chcete nainstalovat konkrétní modul Azure Batch, například předběžnou verzi modulu, přejděte do [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az.Batch/1.0.0).

* Spustit **připojit AzAccount** pro připojení k vašemu předplatnému (rutiny dodávané služby Azure Batch v modulu Azure Resource Manageru):

  ```PowerShell
  Connect-AzAccount
  ```

* **Zaregistrujte se u poskytovatele oboru názvů služby Batch**. Tuto operaci stačí provést jen **jednou pro každé předplatné**.
  
  ```PowerShell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Správa účtů a klíčů služby Batch

### <a name="create-a-batch-account"></a>Vytvoření účtu Batch

**Nové AzBatchAccount** vytvoří účet Batch v určené skupině prostředků. Pokud ještě nemáte skupinu prostředků, vytvořte ji spuštěním [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) rutiny. Do parametru **Location** zadejte některou oblast Azure, třeba „Střed USA“. Příklad:

```PowerShell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

Potom ve skupině prostředků vytvořte účet Batch. Zadejte název účtu v <*account_name*>, umístění a název vaší skupiny prostředků. Vytváření účtu Batch může nějakou dobu trvat. Příklad:

```PowerShell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> Název účtu Batch musí být v dané oblasti Azure skupiny prostředků jedinečný, musí být tvořen 3 až 24 znaky a obsahovat pouze malá písmena a číslice.

### <a name="get-account-access-keys"></a>Získání přístupových klíčů k účtu

**Get-AzBatchAccountKeys** Zobrazí přístupové klíče přidružené k účtu Azure Batch. Pokud například chcete získat primární a sekundární klíče vytvořeného účtu, spusťte následující rutinu:

 ```PowerShell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Vygenerování nového přístupového klíče

**Nové AzBatchAccountKey** vygeneruje nový primární nebo sekundární klíč pro účet Azure Batch. Pokud například chcete vygenerovat nový primární klíč pro účet Batch, zadejte:

```PowerShell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Pokud chcete vygenerovat nový sekundární klíč, zadejte jako parametr **KeyType** Secondary. Primární a sekundární klíče je nutné vygenerovat odděleně.

### <a name="delete-a-batch-account"></a>Odstranění účtu Batch

**Odebrat AzBatchAccount** odstraní účet Batch. Příklad:

```PowerShell
Remove-AzBatchAccount -AccountName <account_name>
```

Až k tomu budete vyzváni, potvrďte, že chcete odebrat účet. Odebrání účtu může trvat nějakou dobu.

## <a name="create-a-batchaccountcontext-object"></a>Vytvoření objektu BatchAccountContext

Pokud chcete spravovat prostředky služby Batch, můžete provést ověření pomocí sdíleného klíče nebo služby Azure Active Directory. Pokud chcete provést ověření pomocí rutin PowerShellu pro službu Batch, vytvořte nejprve objekt BatchAccountContext, do kterého se uloží identita nebo přihlašovací údaje vašeho účtu. Objekt BatchAccountContext budete předávat rutinám, které pracují s parametrem **BatchContext**.

### <a name="shared-key-authentication"></a>Ověřování pomocí sdíleného klíče

```PowerShell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Ve výchozím nastavení se k ověřování používá primární klíč účtu, ale změnou hodnoty vlastnosti **KeyInUse** objektu BatchAccountContext lze klíč vybrat explicitně: `$context.KeyInUse = "Secondary"`.

### <a name="azure-active-directory-authentication"></a>Ověřování pomocí Azure Active Directory

```PowerShell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Vytváření a úpravy prostředků služby Batch

Pomocí rutin, jako **New-AzBatchPool**, **New-AzBatchJob**, a **New-AzBatchTask** vytvářet prostředky v účtu Batch. Pomocí rutin **Get-** a **Set-** lze aktualizovat vlastnosti existujících prostředků a pomocí rutin **Remove-** lze prostředky v účtu Batch odebírat.

Při použití řady těchto rutin musíte kromě předání objektu BatchContext navíc taky vytvořit nebo předat objekty, které obsahují podrobné nastavení prostředků, jak ukazuje následující příklad. Další příklady najdete v podrobné nápovědě k jednotlivým rutinám.

### <a name="create-a-batch-pool"></a>Vytvoření fondu služby Batch

Při vytváření nebo aktualizaci fondu Batch vyberete buď konfiguraci cloudových služeb, nebo konfiguraci virtuálního počítače pro operační systém výpočetních uzlů (viz [Přehled funkcí služby Batch](batch-api-basics.md#pool)). Pokud zadáte konfiguraci cloudových služeb, vaše výpočetní uzly obdrží image některé z [vydaných verzí hostovaného operačního systému Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Pokud zadáte konfiguraci virtuálního počítače, můžete zadat některou z imagí podporovaných virtuálních počítačů s Linuxem nebo Windows uvedených na webu [Azure Virtual Machines Marketplace][vm_marketplace] nebo vlastní image, kterou jste si připravili.

Při spuštění **New-AzBatchPool**, předejte nastavení operačního systému v objektu PSCloudServiceConfiguration nebo PSVirtualMachineConfiguration. Například následující fragment kódu vytvoří dávky s velikostí Standard_A1 fondu výpočetních uzlů v konfiguraci virtuálního počítače, obdrží Image Ubuntu Server 18.04-LTS. Parametr **VirtualMachineConfiguration** tady určuje proměnnou *$configuration* jako objekt PSVirtualMachineConfiguration. Parametr **BatchContext** určuje jako objekt BatchAccountContext dříve definovanou proměnnou *$context*.

```PowerShell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04.0-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

Cílový počet výpočetních uzlů v novém fondu se vypočte tak vzorec automatického škálování. V tomto případě se používá jednoduchý vzorec **$TargetDedicated=4**, který značí, že maximální počet výpočetních uzlů ve fondu je 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Dotazy na fondy, úlohy, úkoly a další podrobnosti

Pomocí rutin, jako **Get-AzBatchPool**, **Get-AzBatchJob**, a **Get-AzBatchTask** dotazy na entity vytvořené v účtu Batch.

### <a name="query-for-data"></a>Dotazy na data

Jako příklad použijte **Get-AzBatchPools** k vyhledání fondů. Tato rutina se ve výchozím nastavení dotazuje na všechny fondy v účtu, za předpokladu, že jste už uložili objekt BatchAccountContext do hodnoty *$context*:

```PowerShell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>Použití filtru OData

Pomocí parametru **Filter** lze použít filtr OData a vyhledat pouze objekty, které vás zajímají. Například najdete všechny fondy s ID začíná "myPool":

```PowerShell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

Tato metoda není tak účinná jako použití klauzule Where-Object v místním kanálu. Dotaz se však odešle do služby Batch přímo, takže se veškeré filtrování provede na straně serveru, což šetří šířku pásma připojení k internetu.

### <a name="use-the-id-parameter"></a>Použití parametru Id

Alternativou k použití filtru OData je použití parametru **Id**. Postup zadání dotazu na konkrétní fond s parametrem Id myPool

```PowerShell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

**Id** parametr podporuje pouze vyhledávání úplných ID, nepodporuje zástupné znaky nebo filtry stylu typu OData.

### <a name="use-the-maxcount-parameter"></a>Použití parametru MaxCount

Ve výchozím nastavení každá rutina vrací maximálně 1 000 objektů. Pokud tento limit překročíte, můžete buď upřesnit filtr, aby vracel méně objektů, nebo explicitně nastavit maximální hodnotu pomocí parametru **MaxCount**. Příklad:

```PowerShell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

Pokud chcete horní omezení počtu zcela odebrat, nastavte parametr **MaxCount** na hodnotu 0 nebo menší.

### <a name="use-the-powershell-pipeline"></a>Použití kanálu prostředí PowerShell

Rutiny služby batch použití kanálu prostředí PowerShell k odesílání dat mezi rutinami. Výsledek je stejný, jako když zadáte parametr, ale zjednoduší se práce s více entitami.

Když chcete například najít a zobrazit všechny úlohy ve svém účtu:

```PowerShell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

Restartování všech výpočetních uzlů ve fondu:

```PowerShell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Správa balíčků aplikací

Balíčky aplikací umožňují zjednodušené nasazování aplikací ve výpočetních uzlech ve vašich fondech. Rutinami PowerShellu ve službě Batch můžete odesílat a spravovat balíčky aplikací v účtu Batch a nasazovat verze balíčků do výpočetních uzlů.

**Vytvoření** aplikace:

```PowerShell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**Přidání** balíčku aplikace:

```PowerShell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Nastavte pro aplikaci **výchozí verzi**:

```PowerShell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

**Výčet** balíčků aplikací:

```PowerShell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

**Odstranění** balíčku aplikace:

```PowerShell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

**Odstranění** aplikace:

```PowerShell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Před odstraněním aplikace musíte odstranit všechny verze balíčků aplikace. Pokud se pokusíte odstranit aplikaci, která ještě má balíčky, zobrazí se chyba s informací o konfliktu.

### <a name="deploy-an-application-package"></a>Nasazení balíčku aplikace

Při vytváření fondu můžete zadat jeden nebo více balíčků aplikací, které budete nasazovat. Když určíte balíček při vytváření fondu, bude nasazen v každém uzlu při jeho přidání do fondu. Balíčky se také nasazují při restartování uzlu nebo jeho obnovení z image.

Pokud vytváříte fond pro nasazení balíčku aplikace na uzly fondu při jejich přidávání do fondu, zadejte parametr `-ApplicationPackageReference`. Nejprve vytvořte **PSApplicationPackageReference** objektu a nakonfigurujte ho pomocí ID a balíček verze aplikace chcete nasadit do výpočetních uzlů fondu:

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Teď vytvořte fond a zadejte referenční objekt balíčku jako argument možnosti `ApplicationPackageReferences`:

```PowerShell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Další informace o balíčcích aplikací najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací Batch](batch-application-packages.md).

> [!IMPORTANT]
> Chcete-li používat balíčky aplikací, je třeba [propojit účet Azure Storage](#linked-storage-account-autostorage) s vaším účtem Batch.

### <a name="update-a-pools-application-packages"></a>Aktualizace balíčků aplikací fondu

Pokud chcete aktualizovat aplikace přiřazené do existujícího fondu, nejprve vytvořte objekt PSApplicationPackageReference s požadovanými vlastnostmi (ID aplikace a balíček verze):

```PowerShell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Potom získejte fond ze služby Batch, smažte všechny stávající balíčky, přidejte odkaz na nový balíček a aktualizujte ve službě Batch nastavení nového balíčku:

```PowerShell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

Aktualizovali jste vlastnosti fondu ve službě Batch. Pokud chcete nový balíček aplikace skutečně nasadit do výpočetních uzlů ve fondu, musíte příslušné uzly restartovat nebo je obnovit z image. K restartování všech uzlů ve fondu můžete použít tento příkaz:

```PowerShell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> Do výpočetních uzlů ve fondu můžete nasadit několik balíčků aplikací. Pokud chcete balíček aplikace *přidat*, místo abyste jím nahrazovali aktuálně nasazené balíčky, vynechte řádek `$pool.ApplicationPackageReferences.Clear()`, (viz výše).

## <a name="next-steps"></a>Další postup

* Podrobný popis syntaxe rutin najdete v článku [Rutiny služby Azure Batch – reference](/powershell/module/az.batch).
* Další informace o aplikacích a balíčcích aplikací ve službě Batch najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací Batch](batch-application-packages.md).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/