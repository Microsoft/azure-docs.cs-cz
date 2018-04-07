---
title: Migrace do Správce prostředků v prostředí PowerShell | Microsoft Docs
description: Tento článek vás provede migraci podporované platformy IaaS prostředků, jako jsou virtuální počítače (VM), virtuální sítě (virtuální sítě) a účty úložiště z klasického do Azure Resource Manager (ARM) pomocí příkazů prostředí Azure PowerShell
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
ms.openlocfilehash: c184fa2aa3e8b09c798f3c1f007cad57891c413e
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-azure-powershell"></a>Migrovat prostředky infrastruktury z klasického do Azure Resource Manageru pomocí prostředí Azure PowerShell
Tyto kroky ukazují, jak používat příkazy Azure PowerShell k migraci infrastruktury jako služby (IaaS) prostředky z modelu nasazení classic do modelu nasazení Azure Resource Manager.

Pokud chcete, můžete také migrovat prostředky pomocí [rozhraní příkazového řádku Azure (Azure CLI)](../linux/migration-classic-resource-manager-cli.md).

* Pro informace o podporovaných scénářů migrace, viz [platformy podporované migrace z klasického do Azure Resource Manageru prostředků IaaS](migration-classic-resource-manager-overview.md).
* Podrobné pokyny a migrace návod najdete v tématu [technické podrobné informace o platformy podporované migrace z klasického do Azure Resource Manageru](migration-classic-resource-manager-deep-dive.md).
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md)

<br>
Tady je Vývojový diagram k identifikaci pořadí, ve kterém kroky se musí provést během procesu migrace

![Snímek obrazovky, který ukazuje kroky migrace](media/migration-classic-resource-manager/migration-flow.png)

## <a name="step-1-plan-for-migration"></a>Krok 1: Plánování migrace
Tady je několik osvědčených postupů, které doporučujeme jak vyhodnotit migrace IaaS prostředky z classic do Resource Manager:

* Pročtěte [podporované a nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md). Pokud máte virtuální počítače, které používají nepodporované konfigurace nebo funkce, doporučujeme, abyste počkali podpory konfigurace nebo funkce, která má být oznámeno. Případně pokud ji vyhovuje vašim potřebám, odeberte tuto funkci nebo přesunout mimo tuto konfiguraci, chcete-li migrovat.
* Pokud máte automatizované skripty, které jsou dnes nasazení infrastruktury a aplikace, pokuste se vytvořit podobné nastavení testu pomocí těchto skriptů pro migraci. Alternativně můžete nastavit ukázkové prostředí pomocí portálu Azure.

> [!IMPORTANT]
> Application Gateway nejsou aktuálně podporovány pro migraci z classic do Resource Manager. Pokud chcete migrovat klasickou virtuální síť s aplikační brány, odstranění brány před spuštěním operace Příprava přesunout sítě. Po dokončení migrace znovu připojte bránu ve službě Správce prostředků Azure.
>
>Připojování k okruhy ExpressRoute v jiné předplatné brány ExpressRoute se nedají automaticky migrovat. V takových případech odebrat bránu ExpressRoute, migrujte virtuální sítě a znovu vytvořit bránu. Najdete v tématu [okruhy ExpressRoute migrovat a přidružené virtuální sítě z klasického modelu nasazení Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md) Další informace.
>
>

## <a name="step-2-install-the-latest-version-of-azure-powershell"></a>Krok 2: Nainstalujte nejnovější verzi prostředí Azure PowerShell
Existují dvě hlavní možnosti nainstalujte prostředí Azure PowerShell: [Galerie prostředí PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) nebo [instalačního programu webové platformy (WebPI)](http://aka.ms/webpi-azps). WebPI obdrží měsíčních aktualizací. Galerie prostředí PowerShell získává aktualizace trvale. Tento článek je založená na prostředí Azure PowerShell verze 2.1.0.

Pokyny k instalaci naleznete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

<br>

## <a name="step-3-ensure-that-you-are-an-administrator-for-the-subscription-in-azure-portal"></a>Krok 3: Ujistěte se, že jste správce pro předplatné na portálu Azure
Jak tuto migraci provést, je třeba přidat jako spolusprávce pro předplatné ve [portál Azure](https://portal.azure.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce centra vyberte **předplatné**. Pokud ho nevidíte, vyberte **všechny služby**.
3. Najít položku odpovídající předplatné a podívejte se na **Moje ROLE** pole. Pro správce a společné hodnota musí být _správce účtu_.

Pokud si nejste moct přidávat společné správce, kontaktujte správce služeb nebo spolusprávce pro předplatné získat sami přidat.   

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Krok 4: Nastavte předplatné a zaregistrujte se pro migraci
Nejprve spusťte příkazovém řádku prostředí PowerShell. Pro migraci, budete muset nastavit svoje prostředí pro obě classic a Resource Manager.

Přihlaste se ke svému účtu pro model Resource Manager.

```powershell
    Login-AzureRmAccount
```

Získáte dostupných předplatných pomocí následujícího příkazu:

```powershell
    Get-AzureRMSubscription | Sort Name | Select Name
```

Nastavte předplatné Azure pro aktuální relaci. Tento příklad nastaví výchozí název odběru **Moje předplatné Azure**. Nahraďte název odběru příklad vlastní.

```powershell
    Select-AzureRmSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Registrace je jednorázové krok, ale musíte to provést jednou před pokusem o migraci. Bez registrace, zobrazí se následující chybová zpráva:
>
> *Struktura BadRequest: Předplatné není zaregistrované pro migraci.*
>
>

Zaregistrovat u zprostředkovatele prostředků migrace pomocí následujícího příkazu:

```powershell
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Počkejte 5 minut pro registraci dokončit. Pomocí následujícího příkazu můžete zkontrolovat stav schválení:

```powershell
    Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Ujistěte se, že je RegistrationState `Registered` než budete pokračovat.

Přihlaste se teď ke svému účtu pro klasického modelu.

```powershell
    Add-AzureAccount
```

Získáte dostupných předplatných pomocí následujícího příkazu:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Nastavte předplatné Azure pro aktuální relaci. Tento příklad nastaví výchozí odběr do **Moje předplatné Azure**. Nahraďte název odběru příklad vlastní.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```

<br>

## <a name="step-5-make-sure-you-have-enough-azure-resource-manager-virtual-machine-vcpus-in-the-azure-region-of-your-current-deployment-or-vnet"></a>Krok 5: Zkontrolujte, zda že máte dostatek virtuálního počítače Azure Resource Manager Vcpu v oblasti Azure vaše aktuální nasazení nebo virtuální sítě
Následující příkaz prostředí PowerShell slouží ke kontrole aktuální počet Vcpu, které máte ve službě Správce prostředků Azure. Další informace o virtuálních procesorů kvóty, najdete v části [omezení a Azure Resource Manager](../../azure-subscription-service-limits.md#limits-and-the-azure-resource-manager).

Tento příklad zkontroluje dostupnost **západní USA** oblast. Příklad názvu oblasti nahraďte vlastními.

```powershell
Get-AzureRmVMUsage -Location "West US"
```

## <a name="step-6-run-commands-to-migrate-your-iaas-resources"></a>Krok 6: Spuštění příkazů pro migraci vašich prostředků IaaS
> [!NOTE]
> Všechny operace, které jsou zde popsané jsou idempotent. Pokud došlo k potížím. než nepodporované funkce nebo chyby v konfiguraci, doporučujeme zopakovat Příprava, zrušení nebo potvrzení operace. Platformu a potom se pokusí akci znovu.
>
>

## <a name="step-61-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Krok 6.1: Možnost 1 - migraci virtuálních počítačů v rámci cloudové služby (ne ve virtuální síti)
Získání seznamu cloudových služeb pomocí následujícího příkazu a pak vyberte cloudovou službu, která chcete migrovat. Pokud jsou virtuální počítače v rámci cloudové služby ve virtuální síti nebo mají role web nebo worker, příkaz vrátí chybovou zprávu.

```powershell
    Get-AzureService | ft Servicename
```

Získáte název nasazení pro cloudovou službu. V tomto příkladu je název služby **Moje služba**. Nahraďte název službu příkladu vlastní název služby.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Vypněte virtuální počítače v rámci cloudové služby pro migraci. Máte dvě možnosti, které lze vybírat.

* **Možnost 1. Migrovat virtuální počítače k virtuální síti vytvořené platformy**

    Nejprve ověřte, jestli je možné migrovat cloudové služby, použijte následující příkazy:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Předchozí příkaz zobrazí všechny upozornění a chyb, které blokovat migrace. Pokud je ověření úspěšné, pak můžete přesunout **Příprava** kroku:

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Možnost 2. Migrovat na existující virtuální síť v modelu nasazení Resource Manager**

    Tento příklad nastaví na název skupiny prostředků **myResourceGroup**, název virtuální sítě k **myVirtualNetwork** a název podsítě k **mySubNet**. Názvy v příkladu nahraďte názvy vlastních prostředků.

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

    Předchozí příkaz zobrazí všechny upozornění a chyb, které blokovat migrace. Pokud je ověření úspěšné, pak můžete pokračovat následujícím přípravný krok:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Po úspěšné operace přípravy s některou z předchozích možností dotazování na stav migrace virtuálních počítačů. Ujistěte se, že jsou v `Prepared` stavu.

Tento příklad nastaví název virtuálního počítače na **Můjvp**. Příklad názvu nahraďte váš vlastní název virtuálního počítače.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Zkontrolujte konfiguraci pro připravené prostředky pomocí prostředí PowerShell nebo portálu Azure. Pokud si nejste připravený pro migraci a chcete přejít zpět do původního stavu, použijte následující příkaz:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Pokud připravené konfigurací spokojeni, můžete přejít a potvrdit prostředky pomocí následujícího příkazu:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

## <a name="step-61-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Krok 6.1: Možnost 2 - migrovat virtuální počítače ve virtuální síti

Pokud chcete migrovat virtuální počítače ve virtuální síti, migrujte virtuální sítě. Virtuální počítače migrovat automaticky pomocí virtuální sítě. Vyberte virtuální síť, která chcete migrovat.
> [!NOTE]
> [Migrovat jeden virtuální počítač classic](migrate-single-classic-to-resource-manager.md) po vytvoření nového virtuálního počítače správce prostředků se spravovaná diskům použít soubory virtuálního pevného disku (operačního systému a data) virtuálního počítače.
<br>

> [!NOTE]
> Název virtuální sítě se může lišit od informace zobrazené v nového portálu. Zobrazí název nového portálu Azure `[vnet-name]` , ale skutečný virtuální síť s názvem typu `Group [resource-group-name] [vnet-name]`. Před migrací, vyhledávací název skutečné virtuální sítě pomocí příkazu `Get-AzureVnetSite | Select -Property Name` nebo ji zobrazit v starý portál Azure. 

Tento příklad nastaví název virtuální sítě na **myVnet**. Nahraďte název virtuální sítě příklad vlastní.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Pokud virtuální síť obsahuje webové nebo rolí pracovního procesu nebo virtuálních počítačů s nepodporované konfigurace, zobrazí chybovou zprávu ověření.
>
>

Nejprve ověřte, jestli virtuální sítě můžete migrovat pomocí následujícího příkazu:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Předchozí příkaz zobrazí všechny upozornění a chyb, které blokovat migrace. Pokud je ověření úspěšné, pak můžete pokračovat následujícím přípravný krok:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Zkontrolujte konfiguraci připravené virtuálních počítačů pomocí Azure PowerShell nebo portálu Azure. Pokud si nejste připravený pro migraci a chcete přejít zpět do původního stavu, použijte následující příkaz:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Pokud připravené konfigurací spokojeni, můžete přejít a potvrdit prostředky pomocí následujícího příkazu:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

## <a name="step-62-migrate-a-storage-account"></a>Krok 6.2 migrací účtu úložiště
Po dokončení migrace virtuálních počítačů, doporučujeme, migraci účtů úložiště.

Před migrací účtu úložiště, proveďte předcházející kontrol požadovaných součástí:

* **Migrace klasické virtuální počítače, jejichž disky jsou uložené v účtu úložiště**

    Předcházející příkaz vrátí RoleName a DiskName vlastnosti všechny klasické disky virtuálních počítačů v účtu úložiště. RoleName je název virtuálního počítače, ke kterému je disk připojen. Pokud předcházející příkaz vrátí disky pak se ujistěte, že jsou virtuální počítače, na které tyto disky připojené migrovány před migrací účtu úložiště.
    ```powershell
     $storageAccountName = 'yourStorageAccountName'
      Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
      DiskName | Format-List -Property RoleName, DiskName

    ```
* **Odstranit odpojit classic disky virtuálních počítačů uložených v účtu úložiště**

    Najít odpojit classic disky virtuálních počítačů v úložišti účtu pomocí následující příkaz:

    ```powershell
        $storageAccountName = 'yourStorageAccountName'
        Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

    ```
    Pokud se výše příkaz vrátí disky pak odstraňte tyto disky pomocí následující příkaz:

    ```powershell
       Remove-AzureDisk -DiskName 'yourDiskName'
    ```
* **Odstranit Image virtuálních počítačů uložených v účtu úložiště**

    Předcházející příkaz vrátí všechny bitové kopie virtuálních počítačů s diskem operačního systému uložené v účtu úložiště.
     ```powershell
        Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                } | Select-Object -Property ImageName, ImageLabel
     ```
     Předcházející příkaz vrátí všechny bitové kopie virtuálního počítače s datovými disky uložené v účtu úložiště.
     ```powershell

        Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                         -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                        } | Select-Object -Property ImageName, ImageLabel
     ```
    Odstraňte všechny Image virtuálních počítačů vrácený výše příkazy předchozím příkazem:
    ```powershell
    Remove-AzureVMImage -ImageName 'yourImageName'
    ```

Pomocí následujícího příkazu ověřte každý účet úložiště pro migraci. V tomto příkladu je název účtu úložiště **Můj_účet_úložiště**. Příklad názvu nahraďte názvem účtu úložiště.

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
```

Dalším krokem je účet úložiště přípravy na migraci

```powershell
    $storageAccountName = "myStorageAccount"
    Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
```

Zkontrolujte konfiguraci pro účet připravený úložiště pomocí prostředí Azure PowerShell nebo portálu Azure. Pokud si nejste připravený pro migraci a chcete přejít zpět do původního stavu, použijte následující příkaz:

```powershell
    Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
```

Pokud připravené konfigurací spokojeni, můžete přejít a potvrdit prostředky pomocí následujícího příkazu:

```powershell
    Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
```

## <a name="next-steps"></a>Další postup
* [Přehled platformy podporované migrace z klasického do Azure Resource Manageru prostředků IaaS](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Technické podrobné informace o platformy podporované migrace z klasického do Azure Resource Manageru](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Pomocí rozhraní příkazového řádku můžete migrovat prostředky infrastruktury z classic do Azure Resource Manageru](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Komunita nástroje asistence s migrace z klasického do Azure Resource Manageru prostředků IaaS](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Přečtěte si nejčastější dotazy o migraci prostředky infrastruktury jako služby z klasického do Azure Resource Manageru](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
