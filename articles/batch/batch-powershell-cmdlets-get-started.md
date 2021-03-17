---
title: Začínáme s PowerShellem
description: Rychlý úvod do rutin prostředí Azure PowerShell, jejichž pomocí lze spravovat prostředky služby Batch
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 2b51a2a7852df82625fb342bbbbc4a3a1cbf72a3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685506"
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Správa prostředků služby Batch pomocí rutin PowerShellu

Pomocí rutin Azure Batch PowerShellu můžete provádět a skriptovat mnoho běžných úloh Batch. Tento článek obsahuje rychlý úvod do rutin, s jejichž pomocí můžete spravovat účty Batch a pracovat s prostředky služby Batch, jako jsou fondy, úlohy a úkoly.

Úplný seznam rutin prostředí Batch a podrobný popis syntaxe rutin najdete v článku [Rutiny služby Azure Batch – reference](/powershell/module/az.batch).

Moduly Azure PowerShellu doporučujeme pravidelně aktualizovat, abyste mohli využívat výhody, které vám přinášejí aktualizace a vylepšení služby.

## <a name="prerequisites"></a>Požadavky

- [Nainstalujte a nakonfigurujte modul Azure PowerShellu](/powershell/azure/). Pokud chcete nainstalovat konkrétní modul Azure Batch, například předběžnou verzi modulu, přejděte do [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Az.Batch/).

- Spusťte rutinu **Connect-AzAccount** pro připojení k vašemu předplatnému (rutiny Azure Batch dodávané v modulu Azure Resource Manager):

  ```powershell
  Connect-AzAccount
  ```

- **Zaregistrujte se u poskytovatele oboru názvů služby Batch**. Tuto operaci stačí provést jen **jednou pro každé předplatné**.
  
  ```powershell
  Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
  ```

## <a name="manage-batch-accounts-and-keys"></a>Správa účtů a klíčů služby Batch

### <a name="create-a-batch-account"></a>Vytvoření účtu Batch

**New-AzBatchAccount** vytvoří účet Batch v zadané skupině prostředků. Pokud ještě nemáte skupinu prostředků, vytvořte ji spuštěním rutiny [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Do parametru **Location** zadejte některou oblast Azure, třeba „Střední USA“. Například:

```powershell
New-AzResourceGroup –Name MyBatchResourceGroup –Location "Central US"
```

Potom ve skupině prostředků vytvořte účet Batch. Zadejte název účtu v <*account_name*> a umístění a název vaší skupiny prostředků. Vytváření účtu Batch může nějakou dobu trvat. Například:

```powershell
New-AzBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>
```

> [!NOTE]
> Název účtu Batch musí být v dané oblasti Azure skupiny prostředků jedinečný, musí být tvořen 3 až 24 znaky a obsahovat pouze malá písmena a číslice.

### <a name="get-account-access-keys"></a>Získání přístupových klíčů k účtu

**Get-AzBatchAccountKeys** zobrazí přístupové klíče spojené s účtem Azure Batch. Pokud například chcete získat primární a sekundární klíče vytvořeného účtu, spusťte následující rutinu:

 ```powershell
$Account = Get-AzBatchAccountKeys –AccountName <account_name>

$Account.PrimaryAccountKey

$Account.SecondaryAccountKey
```

### <a name="generate-a-new-access-key"></a>Vygenerování nového přístupového klíče

**New-AzBatchAccountKey** vygeneruje nový primární nebo sekundární klíč účtu pro účet Azure Batch. Pokud například chcete vygenerovat nový primární klíč pro účet Batch, zadejte:

```powershell
New-AzBatchAccountKey -AccountName <account_name> -KeyType Primary
```

> [!NOTE]
> Pokud chcete vygenerovat nový sekundární klíč, zadejte jako parametr **KeyType** Secondary. Primární a sekundární klíče je nutné vygenerovat odděleně.

### <a name="delete-a-batch-account"></a>Odstranění účtu Batch

**Remove-AzBatchAccount** odstraní účet Batch. Například:

```powershell
Remove-AzBatchAccount -AccountName <account_name>
```

Až k tomu budete vyzváni, potvrďte, že chcete odebrat účet. Odebrání účtu může trvat nějakou dobu.

## <a name="create-a-batchaccountcontext-object"></a>Vytvoření objektu BatchAccountContext

Pokud chcete spravovat prostředky služby Batch, můžete provést ověření pomocí sdíleného klíče nebo služby Azure Active Directory. Pokud chcete provést ověření pomocí rutin PowerShellu pro službu Batch, vytvořte nejprve objekt BatchAccountContext, do kterého se uloží identita nebo přihlašovací údaje vašeho účtu. Objekt BatchAccountContext budete předávat rutinám, které pracují s parametrem **BatchContext**.

### <a name="shared-key-authentication"></a>Ověřování pomocí sdíleného klíče

```powershell
$context = Get-AzBatchAccountKeys -AccountName <account_name>
```

> [!NOTE]
> Ve výchozím nastavení se k ověřování používá primární klíč účtu, ale změnou hodnoty vlastnosti **KeyInUse** objektu BatchAccountContext lze klíč vybrat explicitně: `$context.KeyInUse = "Secondary"`.

### <a name="azure-active-directory-authentication"></a>Ověřování služby Azure Active Directory

```powershell
$context = Get-AzBatchAccount -AccountName <account_name>
```

## <a name="create-and-modify-batch-resources"></a>Vytváření a úpravy prostředků služby Batch

Pomocí rutin, jako je **New-AzBatchPool**, **New-AzBatchJob** a **New-AzBatchTask** , můžete vytvářet prostředky v rámci účtu Batch. Pomocí rutin **Get-** a **Set-** lze aktualizovat vlastnosti existujících prostředků a pomocí rutin **Remove-** lze prostředky v účtu Batch odebírat.

Při použití řady těchto rutin musíte kromě předání objektu BatchContext navíc taky vytvořit nebo předat objekty, které obsahují podrobné nastavení prostředků, jak ukazuje následující příklad. Další příklady najdete v podrobné nápovědě k jednotlivým rutinám.

### <a name="create-a-batch-pool"></a>Vytvoření fondu služby Batch

Při vytváření nebo aktualizaci fondu Batch zadáte [konfiguraci](nodes-and-pools.md#configurations). Fondy by měly být obecně nakonfigurované s konfigurací virtuálního počítače, což vám umožní určit jednu z podporovaných imagí pro Linux nebo Windows VM, které jsou uvedené na [webu Azure Virtual Machines Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1), nebo zadat vlastní image, kterou jste připravili. Fondy konfigurací Cloud Services poskytují jenom výpočetní uzly Windows a nepodporují všechny funkce dávky.

Když spustíte rutinu **New-AzBatchPool**, předejte nastavení operačního systému v objektu PSVirtualMachineConfiguration nebo PSCloudServiceConfiguration. Například následující fragment kódu vytvoří fond služby Batch s velikostí Standard_A1 výpočetních uzlů v konfiguraci virtuálního počítače s imagí s Ubuntu serverem 18,04-LTS. Parametr **VirtualMachineConfiguration** tady určuje proměnnou *$configuration* jako objekt PSVirtualMachineConfiguration. Parametr **BatchContext** určuje jako objekt BatchAccountContext dříve definovanou proměnnou *$context*.

```powershell
$imageRef = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSImageReference" -ArgumentList @("UbuntuServer","Canonical","18.04-LTS")

$configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSVirtualMachineConfiguration" -ArgumentList @($imageRef, "batch.node.ubuntu 18.04")

New-AzBatchPool -Id "mypspool" -VirtualMachineSize "Standard_a1" -VirtualMachineConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context
```

Cílový počet výpočetních uzlů v novém fondu je vypočítán vzorcem automatického škálování. V tomto případě se používá jednoduchý vzorec **$TargetDedicated=4**, který značí, že maximální počet výpočetních uzlů ve fondu je 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Dotazy na fondy, úlohy, úkoly a další podrobnosti

Pomocí rutin, jako **Get-AzBatchPool**, **Get-AzBatchJob** a **Get-AzBatchTask** , můžete zadávat dotazy na entity vytvořené v účtu Batch.

### <a name="query-for-data"></a>Dotazy na data

Jako příklad použijte **Get-AzBatchPools** k vyhledání vašich fondů. Tato rutina se ve výchozím nastavení dotazuje na všechny fondy v účtu, za předpokladu, že jste už uložili objekt BatchAccountContext do hodnoty *$context*:

```powershell
Get-AzBatchPool -BatchContext $context
```

### <a name="use-an-odata-filter"></a>Použití filtru OData

Pomocí parametru **Filter** lze použít filtr OData a vyhledat pouze objekty, které vás zajímají. Můžete například najít všechny fondy s ID začínajícími řetězcem "myPool":

```powershell
$filter = "startswith(id,'myPool')"

Get-AzBatchPool -Filter $filter -BatchContext $context
```

Tato metoda není tak účinná jako použití klauzule Where-Object v místním kanálu. Dotaz se však odešle do služby Batch přímo, takže se veškeré filtrování provede na straně serveru, což šetří šířku pásma připojení k internetu.

### <a name="use-the-id-parameter"></a>Použití parametru Id

Alternativou k použití filtru OData je použití parametru **Id**. Postup zadání dotazu na konkrétní fond s parametrem Id myPool

```powershell
Get-AzBatchPool -Id "myPool" -BatchContext $context
```

Parametr **ID** podporuje pouze vyhledávání s úplným ID; Nejedná se o zástupné znaky nebo filtry ve stylu OData.

### <a name="use-the-maxcount-parameter"></a>Použití parametru MaxCount

Ve výchozím nastavení každá rutina vrací maximálně 1 000 objektů. Pokud tento limit překročíte, můžete buď upřesnit filtr, aby vracel méně objektů, nebo explicitně nastavit maximální hodnotu pomocí parametru **MaxCount**. Například:

```powershell
Get-AzBatchTask -MaxCount 2500 -BatchContext $context
```

Pokud chcete horní omezení počtu zcela odebrat, nastavte parametr **MaxCount** na hodnotu 0 nebo menší.

### <a name="use-the-powershell-pipeline"></a>Použití kanálu prostředí PowerShell

Rutiny Batch používají kanál PowerShellu k posílání dat mezi rutinami. Výsledek je stejný, jako když zadáte parametr, ale zjednoduší se práce s více entitami.

Když chcete například najít a zobrazit všechny úlohy ve svém účtu:

```powershell
Get-AzBatchJob -BatchContext $context | Get-AzBatchTask -BatchContext $context
```

Restartování všech výpočetních uzlů ve fondu:

```powershell
Get-AzBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

## <a name="application-package-management"></a>Správa balíčků aplikací

[Balíčky aplikací](batch-application-packages.md) poskytují zjednodušený způsob nasazení aplikací do výpočetních uzlů ve fondech. Rutinami PowerShellu ve službě Batch můžete odesílat a spravovat balíčky aplikací v účtu Batch a nasazovat verze balíčků do výpočetních uzlů.

> [!IMPORTANT]
> Chcete-li používat balíčky aplikací, je třeba propojit účet Azure Storage s vaším účtem Batch.

**Vytvoření** aplikace:

```powershell
New-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

**Přidání** balíčku aplikace:

```powershell
New-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip
```

Nastavte pro aplikaci **výchozí verzi**:

```powershell
Set-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"
```

**Výčet** balíčků aplikací:

```powershell
$application = Get-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

$application.ApplicationPackages
```

**Odstranění** balíčku aplikace:

```powershell
Remove-AzBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"
```

**Odstranění** aplikace:

```powershell
Remove-AzBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"
```

> [!NOTE]
> Před odstraněním aplikace musíte odstranit všechny verze balíčků aplikace. Pokud se pokusíte odstranit aplikaci, která ještě má balíčky, zobrazí se chyba s informací o konfliktu.

### <a name="deploy-an-application-package"></a>Nasazení balíčku aplikace

Při vytváření fondu můžete zadat jeden nebo více balíčků aplikací, které budete nasazovat. Když určíte balíček při vytváření fondu, bude nasazen v každém uzlu při jeho přidání do fondu. Balíčky se také nasazují při restartování uzlu nebo jeho obnovení z image.

Pokud vytváříte fond pro nasazení balíčku aplikace na uzly fondu při jejich přidávání do fondu, zadejte parametr `-ApplicationPackageReference`. Nejprve vytvořte objekt **PSApplicationPackageReference** a nakonfigurujte ho pomocí ID aplikace a verze balíčku, který chcete nasadit do výpočetních uzlů fondu:

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "1.0"
```

Teď vytvořte fond a zadejte referenční objekt balíčku jako argument možnosti `ApplicationPackageReferences`:

```powershell
New-AzBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -VirtualMachineConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference
```

Další informace o balíčcích aplikací najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací Batch](batch-application-packages.md).

### <a name="update-a-pools-application-packages"></a>Aktualizace balíčků aplikací fondu

Chcete-li aktualizovat aplikace přiřazené k existujícímu fondu, nejprve vytvořte objekt PSApplicationPackageReference s požadovanými vlastnostmi (ID aplikace a verze balíčku):

```powershell
$appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

$appPackageReference.ApplicationId = "MyBatchApplication"

$appPackageReference.Version = "2.0"

```

Potom Získejte fond ze služby Batch, zrušte všechny existující balíčky, přidejte odkaz na nový balíček a aktualizujte službu Batch pomocí nového nastavení fondu:

```powershell
$pool = Get-AzBatchPool -BatchContext $context -Id "PoolWithAppPackage"

$pool.ApplicationPackageReferences.Clear()

$pool.ApplicationPackageReferences.Add($appPackageReference)

Set-AzBatchPool -BatchContext $context -Pool $pool
```

Aktualizovali jste vlastnosti fondu ve službě Batch. Pokud chcete nový balíček aplikace skutečně nasadit do výpočetních uzlů ve fondu, musíte příslušné uzly restartovat nebo je obnovit z image. K restartování všech uzlů ve fondu můžete použít tento příkaz:

```powershell
Get-AzBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzBatchComputeNode -BatchContext $context
```

> [!TIP]
> Do výpočetních uzlů ve fondu můžete nasadit několik balíčků aplikací. Pokud chcete balíček aplikace přidat, místo abyste jím nahrazovali aktuálně nasazené balíčky, vynechte řádek `$pool.ApplicationPackageReferences.Clear()`, (viz výše).

## <a name="next-steps"></a>Další kroky

- Podrobné informace o syntaxi a příkladech rutin najdete v [referenčních informacích k rutině Azure Batch](/powershell/module/az.batch) .
- Naučte se, jak [nasadit aplikace do výpočetních uzlů pomocí balíčků aplikací Batch](batch-application-packages.md).
