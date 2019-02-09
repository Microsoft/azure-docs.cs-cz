---
title: Migrace na Resource Manager pomocí Powershellu | Dokumentace Microsoftu
description: Tento článek vás provede platformou podporované migraci prostředků IaaS, jako jsou virtuální počítače (VM), virtuální sítě (Vnet) a účty úložiště z modelu classic na Azure Resource Manageru (ARM) pomocí příkazů Azure Powershellu
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/30/2017
ms.author: kasing
ms.openlocfilehash: 5e905168ab2c2f10bcfadfc605fdcaa800e70332
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55982003"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrovat prostředky IaaS z modelu classic na Azure Resource Manager pomocí Azure Powershellu
Tyto kroky ukazují, jak používat příkazy Azure Powershellu k migraci infrastruktury jako služby (IaaS) prostředky z modelu nasazení classic do modelu nasazení Azure Resource Manageru.

Pokud chcete, můžete také migrovat prostředky pomocí [rozhraní příkazového řádku Azure (Azure CLI)](../linux/migration-classic-resource-manager-cli.md).

* Další informace o podporovaných scénářů migrace najdete v článku [platformou podporované migraci prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Názorný postup migrace a podrobné pokyny najdete v tématu [podrobné technické informace o platformou podporované migraci z modelu classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md)

<br>
Tady je Vývojový diagram k identifikaci pořadí, ve kterém kroky je nutné provést během procesu migrace.

![Snímek obrazovky, který ukazuje kroky migrace](media/migration-classic-resource-manager/migration-flow.png)

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="step-1-plan-for-migration"></a>Krok 1: Plánování migrace
Tady je několik osvědčených postupů, které doporučujeme při hodnocení migrace prostředků IaaS z classic do Resource Manageru:

* Přečtěte si [podporované a nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md). Pokud máte virtuální počítače, které používají nepodporované konfigurace nebo funkce, doporučujeme počkejte podpora konfigurace/funkce oznámí se. Případně pokud vyhovuje vašim potřebám, odeberte tuto funkci nebo přesunout mimo tuto konfiguraci pro povolení migrace.
* Pokud máte automatizované skripty, které nasadit infrastrukturu a aplikace ještě dnes, pokuste se vytvořit podobné nastavení testu pomocí těchto skriptů pro migraci. Ukázková prostředí můžete také nastavit pomocí webu Azure portal.

> [!IMPORTANT]
> Application Gateway nejsou aktuálně podporovány pro migraci z modelu nasazení classic do Resource Manageru. Pokud chcete migrovat klasické virtuální sítě pomocí služby Application gateway, odebrání brány před spuštěním operace přípravy přesunout sítě. Po dokončení migrace znovu připojte bránu v Azure Resource Manageru.
>
>Brány ExpressRoute se připojuje k okruhů ExpressRoute v jiném předplatném není možné migrovat automaticky. V takovém případě odeberte bránu ExpressRoute, migraci virtuální sítě a znovu vytvořte bránu. Podrobnosti najdete na [migrace okruhů ExpressRoute a přidružených virtuálních sítí z klasického modelu nasazení Resource Manageru](../../expressroute/expressroute-migration-classic-resource-manager.md) Další informace.

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Krok 2: Nainstalujte nejnovější verzi Azure Powershellu
Existují dvě hlavní možnosti, jak nainstalovat Azure PowerShell: [Galerie prostředí PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) nebo [webové platformy (Webpi)](https://aka.ms/webpi-azps). Instalace webové platformy obdrží měsíčních aktualizací. Galerie prostředí PowerShell získává aktualizace průběžně. Tento článek je založen na prostředí Azure PowerShell verze 2.1.0.

Pokyny k instalaci, naleznete v tématu [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Krok 3: Ujistěte se, že jste správce pro předplatné na webu Azure portal
Jak tuto migraci provést, musíte přidat jako spolusprávce k předplatnému [webu Azure portal](https://portal.azure.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **předplatné**. Pokud ho nevidíte, vyberte **všechny služby**.
3. Najít položku odpovídající předplatné, pak si prohlédněte **Moje ROLE** pole. Pro spolusprávce, je třeba zadat hodnotu _správce účtu_.

Pokud nejste schopni přidat jako spolusprávce, požádejte správce služeb nebo spolupracující správce pro předplatné zobrazíte nechejte se přidat.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Krok 4: Nastavte předplatné a zaregistrujte si migration
Nejprve spusťte příkazový řádek Powershellu. Pro migraci, je nutné nastavit prostředí pro obě classic a Resource Manageru.

Přihlaste se ke svému účtu pro model Resource Manageru.

```powershell
    Connect-AzAccount
```

Získáte dostupná předplatná pomocí následujícího příkazu:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Nastavte své předplatné Azure pro aktuální relaci. Tento příklad nastaví výchozí název předplatného **Moje předplatné Azure**. Nahraďte název předplatného příkladu vlastní.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Registrace je jednorázový krok, ale je nutné provést jednou před pokusem o migraci. Bez registrace, zobrazí se následující chybová zpráva:
>
> *Chybného požadavku: Předplatné není zaregistrované pro migraci.*

Zaregistrovat poskytovatele prostředků migrace s použitím následujícího příkazu:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Počkejte prosím 5 minut pro registraci dokončit. Stav schválení můžete zkontrolovat pomocí následujícího příkazu:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Ujistěte se, že je RegistrationState `Registered` předtím, než budete pokračovat.

Přihlaste se k účtu pro klasický model.

```powershell
    Add-AzureAccount
```

Získáte dostupná předplatná pomocí následujícího příkazu:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Nastavte své předplatné Azure pro aktuální relaci. Tento příklad nastaví výchozí předplatné na **Moje předplatné Azure**. Nahraďte název předplatného příkladu vlastní.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 5: Ujistěte se, že máte dostatek virtuálních procesorů na virtuální počítač Azure Resource Manageru v oblasti Azure pro vaše aktuální nasazení nebo virtuální sítě
Následující příkaz prostředí PowerShell můžete použít ke kontrole aktuální počet virtuálních procesorů, které máte v Azure Resource Manageru. Další informace o kvóty virtuálních procesorů, naleznete v tématu [limity a Azure Resource Manageru](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

Tento příklad kontroluje dostupnost **USA – západ** oblasti. Ukázkový název oblasti nahraďte vlastními.

```powershell
Get-AzVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Krok 6: Spouštění příkazů migrace prostředků IaaS
* [Migrace virtuálních počítačů v cloudové službě (ne ve virtuální síti)](#step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrace virtuálních počítačů ve virtuální síti](#step-61-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrace účtu úložiště](#step-62-migrate-a-storage-account)

> [!NOTE]
> Všechny operace, je zde popsáno, jsou idempotentní. Pokud máte jiný problém, než je nepodporovaná funkce nebo Chyba konfigurace, doporučujeme, abyste se znovu pokusíte přípravy, přerušení nebo potvrzení operace. Platformu se pak pokusí akci provést znovu.


### <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Krok 6.1: Možnost 1 - migrovat virtuální počítače v cloudové službě (ne ve virtuální síti)
Pomocí následujícího příkazu Získejte seznam cloudových služeb a pak vyberte cloudovou službu, kterou chcete migrovat. Pokud jsou virtuální počítače v rámci cloudové služby ve virtuální síti nebo mají webové nebo pracovní role, příkaz vrátí chybovou zprávu.

```powershell
    Get-AzureService | ft Servicename
```

Získání názvu nasazení pro cloudovou službu. V tomto příkladu je název služby **služba**. Název služby příkladu nahraďte název služby.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Příprava virtuálních počítačů v rámci cloudové služby pro migraci. Máte dvě možnosti, jak vybírat.

* **Možnost 1. Migrovat virtuální počítače k virtuální síti vytvořené platformy**

    Nejprve ověřte, jestli je možné migrovat cloudové službě pomocí následujících příkazů:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Následující příkaz zobrazí všechny upozornění a chyby, které blokují migrace. Pokud je ověření úspěšné, pak můžete přesunout **připravit** kroku:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Možnost 2. Migrace do existující virtuální síť v modelu nasazení Resource Manager**

    V tomto příkladu nastaví na název skupiny prostředků **myResourceGroup**, název virtuální sítě k **myVirtualNetwork** a název podsítě do **mySubNet**. Názvy v příkladu nahraďte názvy vlastních prostředků.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Nejprve ověřte, jestli je možné migrovat virtuální sítě pomocí následujícího příkazu:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Následující příkaz zobrazí všechny upozornění a chyby, které blokují migrace. Pokud je ověření úspěšné, pak budete pokračovat následujícím krokem přípravy:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Po úspěšné operace přípravy některým z předchozích možností dotazování na stav migrace virtuálních počítačů. Ujistěte se, že jsou v `Prepared` stavu.

V tomto příkladu nastaví na název virtuálního počítače **myVM**. Název příkladu nahraďte vlastním názvem virtuálního počítače.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Zkontrolujte konfiguraci připravené prostředky pomocí Powershellu nebo na webu Azure portal. Pokud ještě nejste připraveni na migraci a chcete přejít zpět do původní stavu, použijte následující příkaz:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Pokud připravené konfigurací spokojeni, můžete posunout vpřed a potvrdit prostředky pomocí následujícího příkazu:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Krok 6.1: Možnost 2 – migrace virtuálních počítačů ve virtuální síti

Pokud chcete migrovat virtuální počítače ve virtuální síti, můžete migraci virtuální sítě. Virtuální počítače migrovat automaticky s virtuální sítí. Vyberte virtuální síť, která chcete migrovat.
> [!NOTE]
> [Jeden klasický virtuální počítač migrovat](migrate-single-classic-to-resource-manager.md) tak, že vytvoříte nový virtuální počítač Resource Manageru se službou Managed Disks pomocí virtuálního pevného disku (operačního systému a datových) souborů virtuálního počítače.
<br>

> [!NOTE]
> Název virtuální sítě se může lišit od zobrazeného na novém portálu. Na novém portálu Azure zobrazuje název jako `[vnet-name]` název skutečné virtuální sítě je typu, ale `Group [resource-group-name] [vnet-name]`. Před migrací, vyhledávací název skutečné virtuální sítě pomocí příkazu `Get-AzureVnetSite | Select -Property Name` nebo se podívejte na starém portálu Azure. 

V tomto příkladu nastaví na název virtuální sítě **myVnet**. Nahraďte název virtuální sítě příkladu vlastní.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Pokud virtuální síť obsahuje webové nebo pracovní role nebo virtuálních počítačů pomocí nepodporované konfigurace, obdržíte chybovou zprávu ověření.

Nejprve ověřte, jestli migrujete virtuální sítě s použitím následujícího příkazu:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Následující příkaz zobrazí všechny upozornění a chyby, které blokují migrace. Pokud je ověření úspěšné, pak budete pokračovat následujícím krokem přípravy:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Zkontrolujte konfiguraci pro připravené virtuální počítače pomocí Azure Powershellu nebo na webu Azure portal. Pokud ještě nejste připraveni na migraci a chcete přejít zpět do původní stavu, použijte následující příkaz:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Pokud připravené konfigurací spokojeni, můžete posunout vpřed a potvrdit prostředky pomocí následujícího příkazu:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-62-migrate-a-storage-account"></a>Krok 6.2 migrace účtu úložiště
Po dokončení migrace virtuálních počítačů, doporučujeme, abyste proveďte následující kontroly požadovaných součástí před zahájením migrace účtů úložiště.

> [!NOTE]
> Pokud váš účet úložiště má žádné přidružené disky nebo data virtuálního počítače, můžete přeskočit přímo **ověření účtu úložiště a spuštění migrace** oddílu.

* **Kontrola požadovaných součástí je-li migrovat všechny virtuální počítače nebo prostředky disku má váš účet úložiště**
    * **Migrace klasických virtuálních počítačů, jejichž disky jsou uložené v účtu úložiště**

        Následující příkaz vrátí vlastnosti RoleName a DiskName všechny klasické disky virtuálních počítačů v účtu úložiště. RoleName je název virtuálního počítače, ke kterému je připojený disk. Pokud tento příkaz vrátí disky pak Ujistěte se, že virtuální počítače, na které jsou připojeny tyto disky jsou migrovány před migrací účtu úložiště.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * **Odstranit nepřipojené klasické disky virtuálních počítačů uložených v účtu úložiště**

        Najdete nepřipojené klasické disky virtuálních počítačů ve službě storage account používá následující příkaz:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Pokud výše příkaz vrátí disky pak odstraňte tyto disky pomocí následujícího příkazu:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * **Odstranit Image virtuálních počítačů uložených v účtu úložiště**

        Následující příkaz vrátí všechny Image virtuálních počítačů s diskem operačního systému uložené v účtu úložiště.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Následující příkaz vrátí všechny Image virtuálních počítačů s datovými disky uložené v účtu úložiště.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Odstraňte všechny Image virtuálních počítačů vrátil podle výše uvedených příkazů pomocí tohoto příkazu:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* **Ověření úložiště účtu a spustit migraci**

    Pomocí následujícího příkazu ověřte každý účet úložiště pro migraci. V tomto příkladu je název účtu úložiště **myStorageAccount**. Příklad názvu nahraďte názvem účtu úložiště.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Dalším krokem je příprava migrace účtu úložiště

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Zkontrolujte konfiguraci pro účet připravený úložiště pomocí Azure Powershellu nebo na webu Azure portal. Pokud ještě nejste připraveni na migraci a chcete přejít zpět do původní stavu, použijte následující příkaz:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Pokud připravené konfigurací spokojeni, můžete posunout vpřed a potvrdit prostředky pomocí následujícího příkazu:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Další postup
* [Přehled o platformou podporované migraci prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Migrace prostředků IaaS z modelu classic na Azure Resource Manageru pomocí rozhraní příkazového řádku](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Komunitní nástroje pro pomoc s migrací prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Projděte si nejčastější dotazy o migraci prostředků IaaS z modelu classic na Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
