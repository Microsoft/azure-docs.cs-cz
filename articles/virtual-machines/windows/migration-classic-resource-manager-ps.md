---
title: Migrace na Správce prostředků s využitím PowerShellu | Microsoft Docs
description: Tento článek vás provede migrací prostředků IaaS podporovaných platformou, jako jsou virtuální počítače, virtuální sítě (virtuální sítě) a účty úložiště z modelu Classic na Azure Resource Manager (ARM) pomocí příkazů Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: bf964f23b6c38444fb15b61161cb7ed5a2b15e00
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102653"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí Azure PowerShell
Tyto kroky ukazují, jak používat Azure PowerShell příkazy k migraci prostředků infrastruktury jako služby (IaaS) z modelu nasazení Classic do modelu nasazení Azure Resource Manager.

Pokud chcete, můžete také migrovat prostředky pomocí [rozhraní příkazového řádku Azure (Azure CLI)](../linux/migration-classic-resource-manager-cli.md).

* Základní informace o podporovaných scénářích migrace najdete v tématu [migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Podrobné pokyny a návod k migraci najdete v článku [o migraci podporované platformou z klasického na Azure Resource Manager s technickými podrobně](migration-classic-resource-manager-deep-dive.md).
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md)

<br>
Tady je vývojový diagram pro identifikaci pořadí, ve kterém je potřeba provést kroky během procesu migrace.

![Snímek obrazovky, který ukazuje kroky migrace](media/migration-classic-resource-manager/migration-flow.png)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="step-1-plan-for-migration"></a>Krok 1: Plánování migrace
Tady je několik doporučených postupů, které doporučujeme při vyhodnocování migrace prostředků IaaS z modelu Classic na Správce prostředků:

* Přečtěte si [podporované a nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md). Pokud máte virtuální počítače, které používají nepodporované konfigurace nebo funkce, doporučujeme, abyste počkali, až bude podpora konfigurace/funkcí oznámena. Případně, pokud to vyhovuje vašim potřebám, odeberte tuto funkci nebo ji z této konfigurace přeinstalujte, aby bylo možné migraci povolit.
* Pokud máte automatizované skripty, které nasazují svou infrastrukturu a aplikace dnes, zkuste vytvořit podobné nastavení testu pomocí těchto skriptů pro migraci. Alternativně můžete nastavit ukázková prostředí pomocí Azure Portal.

> [!IMPORTANT]
> Aplikační brány se v současné době nepodporují pro migraci z modelu Classic na Správce prostředků. Chcete-li migrovat klasickou virtuální síť s aplikační bránou, odeberte bránu před spuštěním operace přípravy k přesunutí sítě. Po dokončení migrace znovu připojte bránu v Azure Resource Manager.
>
>Brány ExpressRoute, které se připojují k okruhům ExpressRoute v jiném předplatném, se nedají migrovat automaticky. V takových případech odeberte bránu ExpressRoute, migrujte virtuální síť a znovu vytvořte bránu. Další informace najdete v tématu [migrace okruhů ExpressRoute a přidružených virtuálních sítí z modelu nasazení Classic do nasazení Správce prostředků](../../expressroute/expressroute-migration-classic-resource-manager.md) .

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Krok 2: Nainstalovat nejnovější verzi Azure PowerShell
Existují dvě hlavní možnosti, jak nainstalovat Azure PowerShell: [Galerie prostředí PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) nebo [Instalace webové platformy (WebPI)](https://aka.ms/webpi-azps). WebPI přijímá měsíční aktualizace. Galerie prostředí PowerShell průběžně průběžně přijímá aktualizace. Tento článek je založený na Azure PowerShell verze 2.1.0.

Pokyny k instalaci najdete v tématu [instalace a konfigurace Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Krok 3: Ujistěte se, že jste správce předplatného v Azure Portal
K provedení této migrace musíte být přidáni jako spolusprávce předplatného v [Azure Portal](https://portal.azure.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **předplatné**. Pokud ho nevidíte, vyberte **všechny služby**.
3. Vyhledejte příslušnou položku předplatného a potom se podívejte do pole **Moje role** . Pro spolusprávce by tato hodnota měla být _správce účtu_.

Pokud nemůžete přidat spolusprávce, kontaktujte správce služby nebo spolusprávce předplatného, aby se mohli přidat.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Krok 4: Nastavte si předplatné a zaregistrujte se do migrace.
Nejdřív spusťte příkazový řádek PowerShellu. Pro migraci je potřeba nastavit prostředí pro klasický i Správce prostředků.

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
> Registrace je jednorázový krok, ale před pokusem o migraci ji musíte provést sami. Bez registrace se zobrazí následující chybová zpráva:
>
> *Důvodu chybného požadavku Předplatné není zaregistrované pro migraci.*

Zaregistrujte se zprostředkovatelem prostředků migrace pomocí následujícího příkazu:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Počkejte prosím pět minut, než se registrace dokončí. Stav schválení můžete zjistit pomocí následujícího příkazu:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Před pokračováním se ujistěte `Registered` , že je RegistrationState.

Teď se přihlaste ke svému účtu pro klasický model.

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

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 5: Ujistěte se, že máte dostatek Azure Resource Manager vCPU virtuálních počítačů v oblasti Azure vašeho aktuálního nasazení nebo virtuální sítě.
K zkontrolování aktuálního počtu vCPU, která máte v Azure Resource Manager, můžete použít následující příkaz prostředí PowerShell. Další informace o kvótách vCPU najdete v tématu [omezení a Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-azure-resource-manager).

Tento příklad zkontroluje dostupnost v oblasti **západní USA** . Nahraďte název ukázkové oblasti vlastním.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Krok 6: Spuštění příkazů pro migraci prostředků IaaS
* [Migrace virtuálních počítačů v cloudové službě (ne ve virtuální síti)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrace virtuálních počítačů ve virtuální síti](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrace účtu úložiště](#step-62-migrate-a-storage-account)

> [!NOTE]
> Všechny popsané operace jsou idempotentní. Pokud máte jiný problém než Nepodporovaná funkce nebo Chyba konfigurace, Doporučujeme zopakovat operaci příprava, přerušení nebo potvrzení. Platforma se pak pokusí akci zopakovat.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Krok 6,1: Možnost 1 – migrace virtuálních počítačů v cloudové službě (mimo virtuální síť)
Seznam cloudových služeb získáte pomocí následujícího příkazu a pak vyberte cloudovou službu, kterou chcete migrovat. Pokud jsou virtuální počítače v cloudové službě ve virtuální síti nebo pokud mají webové role nebo role pracovního procesu, příkaz vrátí chybovou zprávu.

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

* **Možnost 1. Migrace virtuálních počítačů do virtuální sítě vytvořené platformou**

    Nejdřív ověřte, jestli můžete migrovat cloudovou službu pomocí následujících příkazů:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Následující příkaz zobrazí všechna upozornění a chyby, které blokují migraci. Pokud je ověření úspěšné, můžete přejít na krok **Příprava** :

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Možnost 2. Migrace do existující virtuální sítě v modelu nasazení Správce prostředků**

    V tomto příkladu se nastaví název skupiny prostředků na **myResourceGroup**, název virtuální sítě na **myVirtualNetwork** a název podsítě na **mySubNet**. Názvy v příkladu nahraďte názvy vašich vlastních prostředků.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Nejdřív ověřte, jestli můžete migrovat virtuální síť pomocí následujícího příkazu:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Následující příkaz zobrazí všechna upozornění a chyby, které blokují migraci. Pokud je ověření úspěšné, můžete pokračovat následujícím krokem přípravy:

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

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Krok 6,1: Možnost 2 – migrace virtuálních počítačů ve virtuální síti

K migraci virtuálních počítačů ve virtuální síti migrujete virtuální síť. Virtuální počítače se automaticky migrují s virtuální sítí. Vyberte virtuální síť, kterou chcete migrovat.
> [!NOTE]
> [Migrujte jeden klasický virtuální počítač](migrate-single-classic-to-resource-manager.md) tím, že vytvoříte nový správce prostředků virtuální počítač s Managed disks pomocí souborů VHD (OS a data) virtuálního počítače.
<br>

> [!NOTE]
> Název virtuální sítě se může lišit od toho, co se zobrazuje na novém portálu. Nový portál Azure Portal zobrazí název, `[vnet-name]` ale skutečný název virtuální sítě je typu. `Group [resource-group-name] [vnet-name]` Před migrací si vyhledáte skutečný název virtuální sítě pomocí příkazu `Get-AzureVnetSite | Select -Property Name` nebo ho zobrazte na původním portálu Azure Portal. 

V tomto příkladu se nastaví název virtuální sítě na **myVnet**. Nahraďte ukázkový název virtuální sítě vlastním.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Pokud virtuální síť obsahuje webové nebo pracovní role nebo virtuální počítače s nepodporovanými konfiguracemi, zobrazí se chybová zpráva ověřování.

Nejdřív ověřte, jestli můžete migrovat virtuální síť pomocí následujícího příkazu:

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

### <a name="step-62-migrate-a-storage-account"></a>Krok 6,2 Migrace účtu úložiště
Po dokončení migrace virtuálních počítačů doporučujeme před migrací účtů úložiště provést následující kontroly požadovaných součástí.

> [!NOTE]
> Pokud váš účet úložiště nemá žádné přidružené disky ani data virtuálních počítačů, můžete přejít přímo k části **ověření účtu úložiště a spuštění migrace** .

* **Kontroly předpokladů při migraci všech virtuálních počítačů nebo účtu úložiště mají diskové prostředky**
    * **Migrace klasických virtuálních počítačů, jejichž disky se ukládají v účtu úložiště**

        Následující příkaz vrátí RoleName a vlastnosti disku všech klasických disků virtuálních počítačů v účtu úložiště. RoleName je název virtuálního počítače, ke kterému je disk připojen. Pokud tento příkaz vrátí disky, zajistěte, aby virtuální počítače, ke kterým jsou připojené tyto disky, byly migrovány před migrací účtu úložiště.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **Odstraňte nepřipojené disky s klasickým virtuálním počítačem uloženou v účtu úložiště.**

        V účtu úložiště Najděte nepřipojené disky klasických virtuálních počítačů pomocí následujícího příkazu:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Pokud příkaz výše vrátí disky, odstraňte tyto disky pomocí následujícího příkazu:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **Odstranění imagí virtuálních počítačů uložených v účtu úložiště**

        Následující příkaz vrátí všechny image virtuálních počítačů s diskem s operačním systémem uloženým v účtu úložiště.
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
        Pomocí tohoto příkazu odstraňte všechny image virtuálních počítačů vrácené výše uvedenými příkazy:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **Ověření účtu úložiště a spuštění migrace**

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
* [Přehled migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z modelu Classic na Azure Resource Manager pomocí rozhraní příkazového řádku](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Přečtěte si nejčastější dotazy týkající se migrace prostředků IaaS z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
