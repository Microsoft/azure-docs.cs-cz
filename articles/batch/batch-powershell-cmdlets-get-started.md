---
title: Začínáme se službou Azure Batch pomocí PowerShellu | Dokumentace Microsoftu
description: Rychlý úvod do rutin prostředí Azure PowerShell, jejichž pomocí lze spravovat prostředky služby Batch
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: f9ad62c5-27bf-4e6b-a5bf-c5f5914e6199
ms.service: batch
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: powershell
ms.workload: big-compute
ms.date: 02/27/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6767257a540e4f29bb5445a718ad65a31e1f373e
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="manage-batch-resources-with-powershell-cmdlets"></a>Správa prostředků služby Batch pomocí rutin PowerShellu

Pomocí rutin prostředí PowerShell služby Batch můžete provádět a převádět na skripty řadu stejných úkolů, které se provádějí pomocí rozhraní API služby Batch, webu Azure Portal a rozhraní příkazového řádku (CLI) Azure. Tento článek obsahuje rychlý úvod do rutin, s jejichž pomocí můžete spravovat účty Batch a pracovat s prostředky služby Batch, jako jsou fondy, úlohy a úkoly.

Úplný seznam rutin prostředí Batch a podrobný popis syntaxe rutin najdete v článku [Rutiny služby Azure Batch – reference](/powershell/module/azurerm.batch/#batch).

Tento článek vychází z rutin prostředí Azure PowerShell verze 3.0.0. Prostředí Azure PowerShell doporučujeme často aktualizovat, abyste mohli využívat výhody, které vám přinášejí aktualizace a vylepšení služby.

## <a name="prerequisites"></a>Požadavky
Pokud chcete používat Azure PowerShell ke správě prostředků služby Batch, proveďte následující operace.

* [Nainstalujte a nakonfigurujte Azure PowerShell.](/powershell/azure/overview)
* Spusťte rutinu **Login-AzureRmAccount**, abyste připojili své předplatné (rutiny služby Azure Batch jsou dodávané v modulu Azure Resource Manager):
  
    `Login-AzureRmAccount`
* **Zaregistrujte se u poskytovatele oboru názvů služby Batch**. Tuto operaci stačí provést jen **jednou za předplatné**.
  
    `Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch`

## <a name="manage-batch-accounts-and-keys"></a>Správa účtů a klíčů služby Batch
### <a name="create-a-batch-account"></a>Vytvoření účtu Batch
Rutina **New-AzureRmBatchAccount** vytvoří v zadané skupině prostředků účet služby Batch. Pokud skupinu prostředků ještě nemáte, použijte k jejímu vytvoření rutinu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Do parametru **Location** zadejte některou oblast Azure, třeba „Střed USA“. Příklad:

    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"

Potom ve skupině prostředků vytvořte účet Batch. Do parametru <*account_name*> zadejte název účtu a zadejte také umístění a název skupiny prostředků. Vytváření účtu Batch může nějakou dobu trvat. Příklad:

    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName <res_group_name>

> [!NOTE]
> Název účtu Batch musí být v dané oblasti Azure skupiny prostředků jedinečný, musí být tvořen 3 až 24 znaky a obsahovat pouze malá písmena a číslice.
> 
> 

### <a name="get-account-access-keys"></a>Získání přístupových klíčů k účtu
**Get-AzureRmBatchAccountKeys** zobrazí přístupové klíče asociované s účtem Azure Batch. Pokud například chcete získat primární a sekundární klíče vytvořeného účtu, spusťte následující rutinu:

    $Account = Get-AzureRmBatchAccountKeys –AccountName <account_name>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey

### <a name="generate-a-new-access-key"></a>Vygenerování nového přístupového klíče
**New-AzureRmBatchAccountKey** vygeneruje nový primární nebo sekundární přístupový klíč pro účet Azure Batch. Pokud například chcete vygenerovat nový primární klíč pro účet Batch, zadejte:

    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary

> [!NOTE]
> Pokud chcete vygenerovat nový sekundární klíč, zadejte jako parametr **KeyType** Secondary. Primární a sekundární klíče je nutné vygenerovat odděleně.
> 
> 

### <a name="delete-a-batch-account"></a>Odstranění účtu Batch
**Remove-AzureRmBatchAccount** odstraní účet Batch. Příklad:

    Remove-AzureRmBatchAccount -AccountName <account_name>

Až k tomu budete vyzváni, potvrďte, že chcete odebrat účet. Odebrání účtu může trvat nějakou dobu.

## <a name="create-a-batchaccountcontext-object"></a>Vytvoření objektu BatchAccountContext
Pokud chcete ve službě Batch při vytváření a správě fondů, úloh, úkolů a dalších prostředků k ověřování používat rutiny prostředí PowerShell, je nutné nejprve vytvořit objekt BatchAccountContext, který bude uchovávat název vašeho účtu a klíče:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Objekt BatchAccountContext budete předávat rutinám, které pracují s parametrem **BatchContext**.

> [!NOTE]
> Ve výchozím nastavení se k ověřování používá primární klíč účtu, ale změnou hodnoty vlastnosti **KeyInUse** objektu BatchAccountContext lze klíč vybrat explicitně: `$context.KeyInUse = "Secondary"`.
> 
> 

## <a name="create-and-modify-batch-resources"></a>Vytváření a úpravy prostředků služby Batch
Pomocí rutin, jako třeba **New-AzureBatchPool**, **New-AzureBatchJob** a **New-AzureBatchTask**, můžete v účtu Batch vytvářet prostředky. Pomocí rutin **Get-** a **Set-** lze aktualizovat vlastnosti existujících prostředků a pomocí rutin **Remove-** lze prostředky v účtu Batch odebírat.

Při použití řady těchto rutin musíte kromě předání objektu BatchContext navíc taky vytvořit nebo předat objekty, které obsahují podrobné nastavení prostředků, jak ukazuje následující příklad. Další příklady najdete v podrobné nápovědě k jednotlivým rutinám.

### <a name="create-a-batch-pool"></a>Vytvoření fondu služby Batch
Při vytváření nebo aktualizaci fondu Batch vyberete buď konfiguraci cloudové služby, nebo konfiguraci virtuálního počítače pro operační systém výpočetních uzlů (viz [Přehled funkcí služby Batch](batch-api-basics.md#pool)). Pokud zadáte konfiguraci cloudové služby, vaše výpočetní uzly obdrží image některé z [vydaných verzí hostovaného operačního systému Azure](../cloud-services/cloud-services-guestos-update-matrix.md#releases). Pokud zadáte konfiguraci virtuálního počítače, můžete zadat některou z imagí podporovaných virtuálních počítačů s Linuxem nebo Windows uvedených na webu [Azure Virtual Machines Marketplace][vm_marketplace] nebo vlastní image, kterou jste si připravili.

Když spouštíte rutinu **New-AzureBatchPool**, předejte nastavení operačního systému v objektu PSCloudServiceConfiguration nebo PSVirtualMachineConfiguration. Následující rutina třeba vytvoří nový fond Batch s výpočetními uzly malé velikosti v konfiguraci cloudové služby, které obdrží image nejnovější verze operačního systému z řady 3 (Windows Server 2012). Parametr **CloudServiceConfiguration** tady určuje proměnnou *$configuration* jako objekt PSCloudServiceConfiguration. Parametr **BatchContext** určuje jako objekt BatchAccountContext dříve definovanou proměnnou *$context*.

    $configuration = New-Object -TypeName "Microsoft.Azure.Commands.Batch.Models.PSCloudServiceConfiguration" -ArgumentList @(4,"*")

    New-AzureBatchPool -Id "AutoScalePool" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -AutoScaleFormula '$TargetDedicated=4;' -BatchContext $context

Cílový počet výpočetních uzlů v novém fondu určuje vzorec automatického škálování. V tomto případě se používá jednoduchý vzorec **$TargetDedicated=4**, který značí, že maximální počet výpočetních uzlů ve fondu je 4.

## <a name="query-for-pools-jobs-tasks-and-other-details"></a>Dotazy na fondy, úlohy, úkoly a další podrobnosti
Pomocí rutin, jako například **Get-AzureBatchPool**, **Get-AzureBatchJob** a **Get-AzureBatchTask**, lze zadávat dotazy na entity vytvořené v účtu Batch.

### <a name="query-for-data"></a>Dotazy na data
Jako příklad použijte rutinu **Get-AzureBatchPools** k vyhledání fondů. Tato rutina se ve výchozím nastavení dotazuje na všechny fondy v účtu, za předpokladu, že jste už uložili objekt BatchAccountContext do hodnoty *$context*:

    Get-AzureBatchPool -BatchContext $context

### <a name="use-an-odata-filter"></a>Použití filtru OData
Pomocí parametru **Filter** lze použít filtr OData a vyhledat pouze objekty, které vás zajímají. Například můžete vyhledat všechny fondy s ID začínajícími řetězcem myPool.

    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context

Tato metoda není tak účinná jako použití klauzule Where-Object v místním kanálu. Dotaz se však odešle do služby Batch přímo, takže se veškeré filtrování provede na straně serveru, což šetří šířku pásma připojení k internetu.

### <a name="use-the-id-parameter"></a>Použití parametru Id
Alternativou k použití filtru OData je použití parametru **Id**. Postup zadání dotazu na konkrétní fond s parametrem Id myPool

    Get-AzureBatchPool -Id "myPool" -BatchContext $context

Parametr **Id** podporuje pouze vyhledávání úplných ID, nepodporuje zástupné znaky ani filtry stylu typu OData.

### <a name="use-the-maxcount-parameter"></a>Použití parametru MaxCount
Ve výchozím nastavení každá rutina vrací maximálně 1 000 objektů. Pokud tento limit překročíte, můžete buď upřesnit filtr, aby vracel méně objektů, nebo explicitně nastavit maximální hodnotu pomocí parametru **MaxCount**. Příklad:

    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Pokud chcete horní omezení počtu zcela odebrat, nastavte parametr **MaxCount** na hodnotu 0 nebo menší.

### <a name="use-the-powershell-pipeline"></a>Použití kanálu prostředí PowerShell
Rutiny služby Batch mohou využívat kanál prostředí PowerShell k odesílání dat mezi rutinami. Výsledek je stejný, jako když zadáte parametr, ale zjednoduší se práce s více entitami.

Když chcete například najít a zobrazit všechny úlohy ve svém účtu:

    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context

Restartování všech výpočetních uzlů ve fondu:

    Get-AzureBatchComputeNode -PoolId "myPool" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

## <a name="application-package-management"></a>Správa balíčků aplikací
Balíčky aplikací umožňují zjednodušené nasazování aplikací ve výpočetních uzlech ve vašich fondech. Rutinami PowerShellu ve službě Batch můžete odesílat a spravovat balíčky aplikací v účtu Batch a nasazovat verze balíčků do výpočetních uzlů.

**Vytvoření** aplikace:

    New-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

**Přidání** balíčku aplikace:

    New-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0" -Format zip -FilePath package001.zip

Nastavte pro aplikaci **výchozí verzi**:

    Set-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -DefaultVersion "1.0"

**Výčet** balíčků aplikací:

    $application = Get-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

    $application.ApplicationPackages

**Odstranění** balíčku aplikace:

    Remove-AzureRmBatchApplicationPackage -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication" -ApplicationVersion "1.0"

**Odstranění** aplikace:

    Remove-AzureRmBatchApplication -AccountName <account_name> -ResourceGroupName <res_group_name> -ApplicationId "MyBatchApplication"

> [!NOTE]
> Před odstraněním aplikace musíte odstranit všechny verze balíčků aplikace. Pokud se pokusíte odstranit aplikaci, která ještě má balíčky, zobrazí se chyba s informací o konfliktu.
> 
> 

### <a name="deploy-an-application-package"></a>Nasazení balíčku aplikace
Při vytváření fondu můžete zadat jeden nebo více balíčků aplikací, které budete nasazovat. Když určíte balíček při vytváření fondu, bude nasazen v každém uzlu při jeho přidání do fondu. Balíčky se také nasazují při restartování uzlu nebo jeho obnovení z image.

Pokud vytváříte fond pro nasazení balíčku aplikace na uzly fondu při jejich přidávání do fondu, zadejte parametr `-ApplicationPackageReference`. Nejprve vytvořte objekt **PSApplicationPackageReference** a nakonfigurujte ho pomocí ID aplikace a verze balíčku, který chcete nasadit do výpočetních uzlů fondu:

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "1.0"

Teď vytvořte fond a zadejte referenční objekt balíčku jako argument možnosti `ApplicationPackageReferences`:

    New-AzureBatchPool -Id "PoolWithAppPackage" -VirtualMachineSize "Small" -CloudServiceConfiguration $configuration -BatchContext $context -ApplicationPackageReferences $appPackageReference

Další informace o balíčcích aplikací najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací Batch](batch-application-packages.md).

> [!IMPORTANT]
> Chcete-li používat balíčky aplikací, je třeba [propojit účet Azure Storage](#linked-storage-account-autostorage) s vaším účtem Batch.
> 
> 

### <a name="update-a-pools-application-packages"></a>Aktualizace balíčků aplikací fondu
Pokud chcete aktualizovat aplikace přiřazené do stávajícího fondu, nejprve vytvořte objekt PSApplicationPackageReference s požadovanými vlastnostmi (ID aplikace a verze balíčku):

    $appPackageReference = New-Object Microsoft.Azure.Commands.Batch.Models.PSApplicationPackageReference

    $appPackageReference.ApplicationId = "MyBatchApplication"

    $appPackageReference.Version = "2.0"

Potom získejte fond ze služby Batch, smažte všechny stávající balíčky, přidejte odkaz na nový balíček a aktualizujte ve službě Batch nastavení nového balíčku:

    $pool = Get-AzureBatchPool -BatchContext $context -Id "PoolWithAppPackage"

    $pool.ApplicationPackageReferences.Clear()

    $pool.ApplicationPackageReferences.Add($appPackageReference)

    Set-AzureBatchPool -BatchContext $context -Pool $pool

Aktualizovali jste vlastnosti fondu ve službě Batch. Pokud chcete nový balíček aplikace skutečně nasadit do výpočetních uzlů ve fondu, musíte příslušné uzly restartovat nebo je obnovit z image. K restartování všech uzlů ve fondu můžete použít tento příkaz:

    Get-AzureBatchComputeNode -PoolId "PoolWithAppPackage" -BatchContext $context | Restart-AzureBatchComputeNode -BatchContext $context

> [!TIP]
> Do výpočetních uzlů ve fondu můžete nasadit několik balíčků aplikací. Pokud chcete balíček aplikace *přidat*, místo abyste jím nahrazovali aktuálně nasazené balíčky, vynechte řádek `$pool.ApplicationPackageReferences.Clear()`, (viz výše).
> 
> 

## <a name="next-steps"></a>Další kroky
* Podrobný popis syntaxe rutin najdete v článku [Rutiny služby Azure Batch – reference](/powershell/module/azurerm.batch/#batch).
* Další informace o aplikacích a balíčcích aplikací ve službě Batch najdete v tématu [Nasazení aplikací do výpočetních uzlů pomocí balíčků aplikací Batch](batch-application-packages.md).

[vm_marketplace]: https://azure.microsoft.com/marketplace/virtual-machines/