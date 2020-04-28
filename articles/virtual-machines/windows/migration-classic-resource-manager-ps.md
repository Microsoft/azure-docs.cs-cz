---
title: Migrace do Správce prostředků pomocí PowerShellu
description: Tento článek vás provede migrací prostředků IaaS podporovanou platformou, jako jsou virtuální počítače, virtuální sítě a účty úložiště z klasických na Azure Resource Manager pomocí příkazů Azure PowerShellu
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 314d7a4725709f00ba5cdbf54595857502bc5805
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865941"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Migrace prostředků IaaS z klasického do Správce prostředků Azure pomocí PowerShellu

> [!IMPORTANT]
> Dnes asi 90 % virtuálních počítačích IaaS používá [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). února 2020, klasické virtuální počítačky byly zastaralé a budou plně vyřazeny v březnu 1, 2023. [Další informace]( https://aka.ms/classicvmretirement) o tomto vyřazení a [o tom, jak vás ovlivňuje](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me).

Tyto kroky ukazují, jak pomocí příkazů Azure PowerShellu migrovat prostředky infrastruktury jako služby (IaaS) z klasického modelu nasazení do modelu nasazení Azure Resource Manageru.

Pokud chcete, můžete také migrovat prostředky pomocí [azure cli](../linux/migration-classic-resource-manager-cli.md).

* Informace o podporovaných scénářích migrace najdete v [tématu Migrace prostředků IaaS podporovaná platformou z klasického na Správce prostředků Azure](migration-classic-resource-manager-overview.md).
* Podrobné pokyny a návod k migraci najdete v [tématu Technické podrobné informace o migraci podporované platformou z klasické na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Zkontrolujte nejčastější chyby migrace](migration-classic-resource-manager-errors.md).

<br>
Zde je vývojový diagram k identifikaci pořadí, ve kterém je třeba provést kroky během procesu migrace.

![Snímek obrazovky, který ukazuje kroky migrace](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Krok 1: Plán migrace
Zde je několik doporučených postupů, které doporučujeme při vyhodnocování, zda migrovat prostředky IaaS z klasického správce prostředků:

* Přečtěte si [podporované a nepodporované funkce a konfigurace](migration-classic-resource-manager-overview.md). Pokud máte virtuální počítače, které používají nepodporované konfigurace nebo funkce, počkejte na oznámení podpory konfigurace nebo funkcí. Případně, pokud to vyhovuje vašim potřebám, odeberte tuto funkci nebo se z této konfigurace přesuňte, abyste povolili migraci.
* Pokud máte automatizované skripty, které nasazují infrastrukturu a aplikace dnes, zkuste vytvořit podobné nastavení testu pomocí těchto skriptů pro migraci. Případně můžete nastavit ukázková prostředí pomocí portálu Azure.

> [!IMPORTANT]
> Aplikační brány nejsou aktuálně podporovány pro migraci z klasického správce prostředků. Chcete-li migrovat virtuální síť s aplikační bránou, odeberte ji před spuštěním operace Příprava pro přesunutí sítě. Po dokončení migrace znovu připojte bránu ve Správci prostředků Azure.
>
> Brány Azure ExpressRoute, které se připojují k okruhům ExpressRoute v jiném předplatném, nelze migrovat automaticky. V takových případech odeberte bránu ExpressRoute, migrujte virtuální síť a znovu vytvořte bránu. Další informace naleznete [v tématu Migrace okruhů ExpressRoute a přidružených virtuálních sítí z klasického modelu nasazení Resource Manageru](../../expressroute/expressroute-migration-classic-resource-manager.md).

## <a name="step-2-install-the-latest-version-of-powershell"></a>Krok 2: Instalace nejnovější verze PowerShellu
Azure PowerShell můžete nainstalovat dvěma hlavními možnostmi: [PowerShell Gallery](https://www.powershellgallery.com/profiles/azure-sdk/) nebo [Web Platform Installer (WebPI).](https://aka.ms/webpi-azps) WebPI přijímá měsíční aktualizace. Galerie prostředí PowerShell přijímá aktualizace průběžně. Tento článek je založený na Azure PowerShell verze 2.1.0.

Pokyny k instalaci najdete v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Krok 3: Ujistěte se, že jste správcem předplatného
Chcete-li provést tuto migraci, musíte být přidáni jako spolusprávce předplatného na [webu Azure Portal](https://portal.azure.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. V nabídce **Hub** vyberte **Předplatné**. Pokud ji nevidíte, vyberte **Všechny služby**.
3. Najděte příslušnou položku předplatného a pak se podívejte do pole **MOJE ROLE.** Pro spolusprávce by měla být hodnota _Správce účtu_.

Pokud nemůžete přidat spolusprávce, obraťte se na správce služby nebo spolusprávce předplatného a požádejte o přidání.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Krok 4: Nastavení předplatného a registrace k migraci
Nejprve spusťte výzvu prostředí PowerShell. Pro migraci nastavte prostředí pro klasické i Resource Manager.

Přihlaste se ke svému účtu pro model Správce prostředků.

```powershell
    Connect-AzAccount
```

Získejte dostupná předplatná pomocí následujícího příkazu:

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Nastavte předplatné Azure pro aktuální relaci. Tento příklad nastaví výchozí název předplatného na **moje předplatné Azure**. Nahraďte název ukázkového předplatného svým vlastním.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Registrace je jednorázový krok, ale musíte to udělat jednou před pokusem o migraci. Bez registrace se zobrazí následující chybová zpráva:
>
> *BadRequest : Odběr není registrován pro migraci.*

Zaregistrujte se u poskytovatele prostředků migrace pomocí následujícího příkazu:

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Počkejte pět minut, než se registrace dokončí. Zkontrolujte stav schválení pomocí následujícího příkazu:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Ujistěte se, `Registered` že RegistrationState je před pokračováním.

Než přepnete na klasický model nasazení, ujistěte se, že máte dostatek virtuálních procesorů virtuálních procesorů Azure Resource Manager v oblasti Azure aktuálního nasazení nebo virtuální sítě. Pomocí následujícího příkazu PowerShellu můžete zkontrolovat aktuální počet virtuálních procesorů, které máte ve Správci prostředků Azure. Další informace o kvótách virtuálních procesorů najdete v [tématu Limity a Správce prostředků Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

Tento příklad kontroluje dostupnost v oblasti **ZÁPADNÍ USA.** Nahraďte název oblasti příkladu vlastním.

```powershell
    Get-AzVMUsage -Location "West US"
```

Nyní se přihlaste ke svému účtu pro klasický model nasazení.

```powershell
    Add-AzureAccount
```

Získejte dostupná předplatná pomocí následujícího příkazu:

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Nastavte předplatné Azure pro aktuální relaci. Tento příklad nastaví výchozí předplatné **předplatného My Azure Subscription**. Nahraďte název ukázkového předplatného svým vlastním.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Krok 5: Spuštění příkazů pro migraci prostředků IaaS
* [Migrace virtuálních počítačů v cloudové službě (ne ve virtuální síti)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Migrace virtuálních počítačů ve virtuální síti](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network)
* [Migrace účtu úložiště](#step-52-migrate-a-storage-account)

> [!NOTE]
> Všechny zde popsané operace jsou idempotentní. Pokud máte jiný problém než nepodporovanou funkci nebo chybu konfigurace, doporučujeme opakovat operaci přípravy, přerušení nebo potvrzení. Platforma pak zkusí akci znovu.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Krok 5.1: Možnost 1 – Migrace virtuálních počítačů v cloudové službě (ne ve virtuální síti)
Seznam cloudových služeb získáte pomocí následujícího příkazu. Pak vyberte cloudovou službu, kterou chcete migrovat. Pokud virtuální počítače v cloudové službě jsou ve virtuální síti nebo pokud mají webové nebo pracovní role, příkaz vrátí chybovou zprávu.

```powershell
    Get-AzureService | ft Servicename
```

Získejte název nasazení pro cloudovou službu. V tomto příkladu je název služby **Moje služba**. Nahraďte název ukázkové služby vlastním názvem služby.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Připravte virtuální počítače v cloudové službě pro migraci. Máte na výběr ze dvou možností.

* **Možnost 1: Migrace virtuálních počítačů do virtuální sítě vytvořené platformou.**

    Nejprve ověřte, zda můžete mířit cloudovou službu pomocí následujících příkazů:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Následující příkaz zobrazí všechna upozornění a chyby, které blokují migraci. Pokud je ověření úspěšné, můžete přejít na krok Příprava.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Možnost 2: Migrace do existující virtuální sítě v modelu nasazení Správce prostředků.**

    Tento příklad nastaví název skupiny prostředků na **myResourceGroup**, název virtuální sítě na **myVirtualNetwork**a název podsítě na **mySubNet**. Nahraďte názvy v příkladu názvy vlastních prostředků.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Nejprve ověřte, zda můžete migrovat virtuální síť pomocí následujícího příkazu:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Následující příkaz zobrazí všechna upozornění a chyby, které blokují migraci. Pokud je ověření úspěšné, můžete pokračovat v následujícím kroku příprava:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

Po dokončení operace Příprava s některou z předchozích možností, dotaz na stav migrace virtuálních počítačů. Ujistěte se, že `Prepared` jsou ve státě.

Tento příklad nastaví název virtuálního_ **virtuálního_** Nahraďte název příkladu vlastním názvem virtuálního počítače.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Zkontrolujte konfiguraci připravených prostředků pomocí PowerShellu nebo portálu Azure. Pokud nejste připraveni na migraci a chcete se vrátit do starého stavu, použijte následující příkaz:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Pokud připravená konfigurace vypadá dobře, můžete se posunout vpřed a potvrdit prostředky pomocí následujícího příkazu:

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Krok 5.1: Možnost 2 – Migrace virtuálních počítačů ve virtuální síti

Chcete-li migrovat virtuální počítače ve virtuální síti, migrujte virtuální síť. Virtuální počítače se automaticky migrují s virtuální sítí. Vyberte virtuální síť, kterou chcete migrovat.
> [!NOTE]
> [Migrujte jeden virtuální počítač](migrate-single-classic-to-resource-manager.md) vytvořený pomocí klasického modelu nasazení vytvořením nového virtuálního počítače Správce prostředků se spravovanými disky pomocí souborů VHD (OS a data) virtuálního počítače.
<br>

> [!NOTE]
> Název virtuální sítě se může lišit od toho, co je zobrazeno na novém portálu. Nový portál Azure zobrazí `[vnet-name]`název jako , ale skutečný `Group [resource-group-name] [vnet-name]`název virtuální sítě je typu . Než migraci spustíte, vyhledejte název skutečné virtuální `Get-AzureVnetSite | Select -Property Name` sítě pomocí příkazu nebo ho zobrazte na starém portálu Azure. 

Tento příklad nastaví název virtuální sítě na **myVnet**. Nahraďte název ukázkové virtuální sítě svým vlastním.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Pokud virtuální síť obsahuje webové nebo pracovní role nebo virtuální počítače s nepodporovanými konfiguracemi, zobrazí se chybová zpráva ověření.

Nejprve ověřte, zda můžete migrovat virtuální síť pomocí následujícího příkazu:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Následující příkaz zobrazí všechna upozornění a chyby, které blokují migraci. Pokud je ověření úspěšné, můžete pokračovat v následujícím kroku příprava:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Zkontrolujte konfiguraci připravených virtuálních počítačů pomocí Azure PowerShellu nebo portálu Azure. Pokud nejste připraveni na migraci a chcete se vrátit do starého stavu, použijte následující příkaz:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Pokud připravená konfigurace vypadá dobře, můžete se posunout vpřed a potvrdit prostředky pomocí následujícího příkazu:

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Krok 5.2: Migrace účtu úložiště
Po dokončení migrace virtuálních počítačů proveďte před migrací účtů úložiště následující požadované kontroly.

> [!NOTE]
> Pokud váš účet úložiště nemá žádné přidružené disky nebo data virtuálního počítače, můžete přeskočit přímo do části Ověřit účty úložiště a spustit migraci.

* Předpokladzkontroluje, jestli jste migrovali všechny virtuální počítače nebo účet úložiště má prostředky na disku:
    * Migrujte virtuální počítače, jejichž disky jsou uloženy v účtu úložiště.

        Následující příkaz vrátí vlastnosti RoleName a DiskName všech disků virtuálního počítače v účtu úložiště. RoleName je název virtuálního počítače, ke kterému je připojen disk. Pokud tento příkaz vrátí disky, ujistěte se, že virtuální počítače, ke kterým jsou tyto disky připojeny, jsou migrovány před migrací účtu úložiště.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Odstraňte nepřipojené disky virtuálních počítačů uložené v účtu úložiště.

        Najděte nepřipojené disky virtuálních počítačů v účtu úložiště pomocí následujícího příkazu:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Pokud předchozí příkaz vrací disky, odstraňte tyto disky pomocí následujícího příkazu:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Odstraňte image virtuálních účtů uložené v účtu úložiště.

        Následující příkaz vrátí všechny image virtuálního počítače s disky operačního systému uloženými v účtu úložiště.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Následující příkaz vrátí všechny image virtuálního počítače s datovými disky uloženými v účtu úložiště.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Odstraňte všechny image virtuálního soudu vrácené předchozími příkazy pomocí tohoto příkazu:
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

    Zkontrolujte konfiguraci připraveného účtu úložiště pomocí Azure PowerShellu nebo portálu Azure. Pokud nejste připraveni na migraci a chcete se vrátit do starého stavu, použijte následující příkaz:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Pokud připravená konfigurace vypadá dobře, můžete se posunout vpřed a potvrdit prostředky pomocí následujícího příkazu:

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Další kroky
* [Přehled migrace prostředků IaaS podporovaných platformou z klasického do Správce prostředků Azure](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Plánování migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Použití příkazového příkazového příkazu k migraci prostředků IaaS z klasických do Správce prostředků Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Nástroje komunity pro usnadnění migrace prostředků IaaS z klasických na Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Běžné chyby při migraci](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Projděte si nejčastější dotazy týkající se migrace prostředků IaaS z klasického na Azure Resource Manager.](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
