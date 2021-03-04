---
title: Migrace na Správce prostředků s využitím PowerShellu
description: Tento článek vás provede migrací prostředků IaaS podporovaných platformou, jako jsou virtuální počítače (VM), virtuální sítě a účty úložiště z klasického na Azure Resource Manager pomocí příkazů Azure PowerShell
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.subservice: classic-to-arm-migration
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c750e34e8081cf5a8b3d41cc8c52584a4353a336
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695154"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí prostředí PowerShell

> [!IMPORTANT]
> V dnešní době se o 90% virtuálních počítačů IaaS používají [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Od 28. února 2020 se klasické virtuální počítače zastaraly a budou plně vyřazeny od 1. března 2023. [Přečtěte si další informace]( https://aka.ms/classicvmretirement) o této zastaralosti a [o tom, jak vás to ovlivní](classic-vm-deprecation.md#how-does-this-affect-me).

Tyto kroky ukazují, jak používat Azure PowerShell příkazy k migraci prostředků infrastruktury jako služby (IaaS) z modelu nasazení Classic do modelu nasazení Azure Resource Manager.

Pokud chcete, můžete také migrovat prostředky pomocí [Azure CLI](migration-classic-resource-manager-cli.md).

* Základní informace o podporovaných scénářích migrace najdete v tématu [migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Podrobné pokyny a návod k migraci najdete v článku [o migraci podporované platformou z klasického na Azure Resource Manager s technickými podrobně](migration-classic-resource-manager-deep-dive.md).
* [Projděte si nejčastější chyby migrace](migration-classic-resource-manager-errors.md).

<br>
Tady je vývojový diagram pro identifikaci pořadí, ve kterém je potřeba provést kroky během procesu migrace.

![Snímek obrazovky, který ukazuje kroky migrace](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Krok 1: plánování migrace
Tady je několik doporučených postupů, které doporučujeme při vyhodnocení toho, jestli se mají migrovat IaaS prostředky z modelu Classic na Správce prostředků:

* Přečtěte si [podporované a nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md). Pokud máte virtuální počítače, které používají nepodporované konfigurace nebo funkce, počkejte, než bude oznámena podpora konfigurace nebo funkce. Případně, pokud to vyhovuje vašim potřebám, odeberte tuto funkci nebo ji z této konfigurace přeinstalujte, aby bylo možné migraci povolit.
* Pokud máte automatizované skripty, které nasazují svou infrastrukturu a aplikace dnes, zkuste vytvořit podobné nastavení testu pomocí těchto skriptů pro migraci. Alternativně můžete nastavit ukázková prostředí pomocí Azure Portal.

> [!IMPORTANT]
> Aplikační brány se v současné době nepodporují pro migraci z modelu Classic na Správce prostředků. Chcete-li migrovat virtuální síť s aplikační bránou, odeberte bránu před spuštěním operace přípravy k přesunutí sítě. Po dokončení migrace znovu připojte bránu v Azure Resource Manager.
>
> Brány Azure ExpressRoute, které se připojují k okruhům ExpressRoute v jiném předplatném, se nedají migrovat automaticky. V takových případech odeberte bránu ExpressRoute, migrujte virtuální síť a znovu vytvořte bránu. Další informace najdete v tématu [migrace okruhů ExpressRoute a přidružených virtuálních sítí z modelu nasazení Classic do modelu nasazení Správce prostředků](../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Krok 2: Instalace nejnovější verze prostředí PowerShell
Existují dvě hlavní možnosti instalace Azure PowerShell: [Galerie prostředí PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) nebo [instalační program webové platformy (WebPI)](https://aka.ms/webpi-azps). WebPI přijímá měsíční aktualizace. Galerie prostředí PowerShell průběžně průběžně přijímá aktualizace. Tento článek je založený na Azure PowerShell verze 2.1.0.

Pokyny k instalaci najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Krok 3: Ujistěte se, že jste správcem předplatného.
K provedení této migrace musíte být přidáni jako spolusprávce předplatného v [Azure Portal](https://portal.azure.com).

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. V nabídce **centra** vyberte **předplatné**. Pokud ho nevidíte, vyberte **všechny služby**.
3. Vyhledejte odpovídající položku pro odběr a potom se podívejte do pole **Moje role** . Pro spolusprávce by měla být hodnota _správce účtu_.

Pokud nemůžete přidat spolusprávce, obraťte se na správce služby nebo spolupracujícího správce, aby vás přidal.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Krok 4: nastavte předplatné a zaregistrujte se do migrace.
Nejdřív spusťte příkazový řádek PowerShellu. Pro migraci nastavte své prostředí pro klasický i Správce prostředků.

Přihlaste se ke svému účtu pro model Správce prostředků.

```powershell
    Connect-AzAccount
```

K získání dostupných předplatných použijte následující příkaz:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Nastavte si předplatné Azure pro aktuální relaci. V tomto příkladu se nastaví výchozí název předplatného na **Moje předplatné Azure**. Nahraďte název ukázkového předplatného svým vlastním.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Registrace je jednorázový krok, ale musíte to udělat dřív, než se pokusíte o migraci. Bez registrace se zobrazí následující chybová zpráva:
>
> *Důvodu chybného požadavku: předplatné není zaregistrované pro migraci.*

Zaregistrujte se zprostředkovatelem prostředků migrace pomocí následujícího příkazu:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Počkejte pět minut, než se registrace dokončí. Stav schválení zkontrolujete pomocí následujícího příkazu:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Před pokračováním se ujistěte, že je RegistrationState `Registered` .

Než přepnete do modelu nasazení Classic, ujistěte se, že máte dostatek Azure Resource Manager vCPU virtuálních počítačů v oblasti Azure vašeho aktuálního nasazení nebo virtuální sítě. K zkontrolování aktuálního počtu vCPU, která máte v Azure Resource Manager, můžete použít následující příkaz prostředí PowerShell. Další informace o kvótách vCPU najdete v tématu [omezení a Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Tento příklad zkontroluje dostupnost v oblasti **západní USA** . Nahraďte název ukázkové oblasti vlastním.

```powershell
    Get-AzVMUsage -Location "West US"
```

Teď se přihlaste ke svému účtu pro model nasazení Classic.

```powershell
    Add-AzureAccount
```

K získání dostupných předplatných použijte následující příkaz:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Nastavte si předplatné Azure pro aktuální relaci. V tomto příkladu se nastaví výchozí předplatné na **Moje předplatné Azure**. Nahraďte název ukázkového předplatného svým vlastním.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Krok 5: Spusťte příkazy pro migraci prostředků IaaS
* [Migrace virtuálních počítačů v cloudové službě (ne ve virtuální síti)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrace virtuálních počítačů ve virtuální síti](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrace účtu úložiště](#step-52-migrate-a-storage-account)

> [!NOTE]
> Všechny popsané operace jsou idempotentní. Pokud máte jiný problém než Nepodporovaná funkce nebo Chyba konfigurace, Doporučujeme zopakovat operaci příprava, přerušení nebo potvrzení. Platforma se pak pokusí akci zopakovat.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Krok 5,1: možnost 1 – migrace virtuálních počítačů v cloudové službě (mimo virtuální síť)
Seznam služeb Cloud Services získáte pomocí následujícího příkazu. Pak vyberte cloudovou službu, kterou chcete migrovat. Pokud jsou virtuální počítače v cloudové službě ve virtuální síti nebo pokud mají webové role nebo role pracovního procesu, příkaz vrátí chybovou zprávu.

```powershell
    Get-AzureService | ft Servicename
```

Získá název nasazení pro cloudovou službu. V tomto příkladu je název služby **Moje služba**. Nahraďte ukázkový název služby vlastním názvem služby.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Připravte virtuální počítače v cloudové službě pro migraci. Máte dvě možnosti, ze kterých si můžete vybrat.

* **Možnost 1: migrujte virtuální počítače do virtuální sítě vytvořené platformou.**

    Nejdřív ověřte, že můžete migrovat cloudovou službu pomocí následujících příkazů:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Následující příkaz zobrazí všechna upozornění a chyby, které blokují migraci. Pokud ověřovací zprávy neobsahují zprávu typu Chyba, můžete přejít na krok příprava.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Možnost 2: migrujte do existující virtuální sítě v modelu nasazení Správce prostředků.**

    V tomto příkladu se nastaví název skupiny prostředků na **myResourceGroup**, název virtuální sítě na **myVirtualNetwork** a název podsítě na **mySubNet**. Názvy v příkladu nahraďte názvy vašich vlastních prostředků.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Nejdřív ověřte, že můžete migrovat virtuální síť pomocí následujícího příkazu:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Následující příkaz zobrazí všechna upozornění a chyby, které blokují migraci. Pokud ověřovací zprávy neobsahují chyby, můžete pokračovat následujícím krokem přípravy:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Po úspěšném dokončení operace přípravy s některou z předchozích možností se dotazuje na stav migrace virtuálních počítačů. Ujistěte se, že jsou ve `Prepared` stavu.

V tomto příkladu se nastaví název virtuálního počítače na **myVM**. Nahraďte název příkladu vlastním názvem virtuálního počítače.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Projděte si konfiguraci připravených prostředků pomocí PowerShellu nebo Azure Portal. Pokud nejste připraveni na migraci a chcete se vrátit k původnímu stavu, použijte následující příkaz:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Pokud je připravená konfigurace dobrá, můžete přesunout prostředky vpřed a potvrdit je pomocí následujícího příkazu:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Krok 5,1: možnost 2 – migrace virtuálních počítačů ve virtuální síti

K migraci virtuálních počítačů ve virtuální síti migrujete virtuální síť. Virtuální počítače se automaticky migrují s virtuální sítí. Vyberte virtuální síť, kterou chcete migrovat.
> [!NOTE]
> [Migrace jednoho virtuálního počítače](./windows/create-vm-specialized-portal.md) vytvořeného pomocí modelu nasazení Classic vytvořením nového virtuálního počítače s Správce prostředků pomocí Managed disks pomocí souborů VHD (OS and data) virtuálního počítače.
<br>

> [!NOTE]
> Název virtuální sítě se může lišit od toho, co se zobrazuje na novém portálu. Nový Azure Portal zobrazí název jako `[vnet-name]` , ale skutečný název virtuální sítě je typu `Group [resource-group-name] [vnet-name]` . Než začnete s migrací, vyhledejte skutečný název virtuální sítě pomocí příkazu `Get-AzureVnetSite | Select -Property Name` nebo ho Zobrazte ve starém Azure Portal. 

V tomto příkladu se nastaví název virtuální sítě na **myVnet**. Nahraďte ukázkový název virtuální sítě vlastním.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Pokud virtuální síť obsahuje webové nebo pracovní role nebo virtuální počítače s nepodporovanými konfiguracemi, zobrazí se chybová zpráva ověřování.

Nejdřív ověřte, že můžete migrovat virtuální síť pomocí následujícího příkazu:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Následující příkaz zobrazí všechna upozornění a chyby, které blokují migraci. Pokud je ověření úspěšné, můžete pokračovat následujícím krokem přípravy:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Pomocí Azure PowerShell nebo Azure Portal Ověřte konfiguraci připravených virtuálních počítačů. Pokud nejste připraveni na migraci a chcete se vrátit k původnímu stavu, použijte následující příkaz:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Pokud je připravená konfigurace dobrá, můžete přesunout prostředky vpřed a potvrdit je pomocí následujícího příkazu:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Krok 5,2: Migrace účtu úložiště
Po dokončení migrace virtuálních počítačů proveďte před migrací účtů úložiště následující kontroly požadovaných součástí.

> [!NOTE]
> Pokud váš účet úložiště nemá žádné přidružené disky ani data virtuálních počítačů, můžete přejít přímo na oddíl ověřit účty úložiště a zahájit migraci. Všimněte si také, že odstranění klasických disků, image virtuálních počítačů nebo image operačních systémů neodstraní zdrojové soubory VHD v účtu úložiště. Nicméně přeruší zapůjčení těchto souborů VHD, aby se po migraci mohli znovu použít k vytváření disků ARM nebo imagí.

* Kontroly předpokladů při migraci všech virtuálních počítačů nebo účtu úložiště mají diskové prostředky:
    * Migrujte virtuální počítače, jejichž disky jsou uložené v účtu úložiště.

        Následující příkaz vrátí RoleName a vlastnosti disku všech disků virtuálního počítače v účtu úložiště. RoleName je název virtuálního počítače, ke kterému je disk připojen. Pokud tento příkaz vrátí disky, zajistěte, aby virtuální počítače, ke kterým jsou připojené tyto disky, byly migrovány před migrací účtu úložiště.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Odstraňte nepřipojené disky virtuálních počítačů uložené v účtu úložiště.

        V účtu úložiště Najděte nepřipojené disky virtuálních počítačů pomocí následujícího příkazu:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Pokud předchozí příkaz vrátí disky, odstraňte tyto disky pomocí následujícího příkazu:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Odstraňte image virtuálních počítačů uložené v účtu úložiště.

        Následující příkaz vrátí všechny image virtuálních počítačů s disky s operačním systémem uloženými v účtu úložiště.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Následující příkaz vrátí všechny image virtuálních počítačů s datovými disky uloženými v účtu úložiště.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Pomocí tohoto příkazu odstraňte všechny image virtuálních počítačů vrácené předchozími příkazy:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Ověřte účty úložiště a spusťte migraci.

    Ověřte každý účet úložiště pro migraci pomocí následujícího příkazu. V tomto příkladu je název účtu úložiště **myStorageAccount**. Nahraďte název příkladu názvem vlastního účtu úložiště.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Dalším krokem je příprava účtu úložiště pro migraci.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Ověřte konfiguraci připraveného účtu úložiště pomocí Azure PowerShell nebo Azure Portal. Pokud nejste připraveni na migraci a chcete se vrátit k původnímu stavu, použijte následující příkaz:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Pokud je připravená konfigurace dobrá, můžete přesunout prostředky vpřed a potvrdit je pomocí následujícího příkazu:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Další kroky
* [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md)
* [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku](migration-classic-resource-manager-cli.md)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md)
* [Přečtěte si nejčastější dotazy týkající se migrace prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-faq.md)
