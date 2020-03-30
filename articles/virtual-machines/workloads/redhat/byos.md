---
title: Red Hat Enterprise Linux přináší vlastní předplatné azure image | Dokumenty společnosti Microsoft
description: Přečtěte si o image chyty pro předplatné pro Red Hat Enterprise Linux v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: asinn826
manager: BorisB2015
editor: ''
ms.assetid: f495f1b4-ae24-46b9-8d26-c617ce3daf3a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 825d26307f2b462d51b143b88127e229508f2f25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264593"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux přináší zlaté obrázky s vlastním předplatným v Azure

Image Red Hat Enterprise Linux (RHEL) jsou dostupné v Azure prostřednictvím modelu s průběžnými platbami nebo s vlastním předplatným (BYOS) (Red Hat Gold Image). Tento článek obsahuje přehled image Red Hat Gold v Azure.

>[!NOTE]
> Zlaté image RHEL BYOS jsou dostupné v cloudech Azure Public (commercial) a Azure Government. Nejsou dostupné v cloudech Azure China nebo Azure Blackforest.

## <a name="important-points-to-consider"></a>Důležité body, které je třeba zvážit

- Image Red Hat Gold, které jsou v tomto programu, jsou obrázky RHEL připravené pro produkční prostředí podobné bitovým kopiím rhel s průběžným platbou na Azure Marketplace.
- Obrázky se řídí aktuálními zásadami popsanými v [ibi Red Hat Enterprise Linux v Azure](./redhat-images.md).
- Standardní zásady podpory platí pro virtuální společnosti vytvořené z těchto irek.
- Virtuální společnosti zřízené z obrázků Red Hat Gold nenesou poplatky RHEL spojené s obrázky s průběžnými platbami RHEL.
- Obrázky nejsou oprávněné. Chcete-li získat aktualizace z Red Hatu přímo, musíte použít Správce předplatného Red Hat k registraci a odběru virtuálních aplikací.
- V současné době není možné dynamicky přepínat mezi BYOS a pay-as-you-go fakturační modely pro obrazy Linuxu. Chcete-li přepnout model fakturace, musíte znovu nasadit virtuální ho virtuálního účtu z příslušné bitové kopie.

>[!NOTE]
> Image RHEL BYOS generace 2 nejsou v současné době k dispozici prostřednictvím nabídky marketplace. Pokud požadujete image GENERACE 2 RHEL BYOS, navštivte řídicí panel Cloud Access ve správě předplatného Red Hat. Další informace naleznete v [dokumentaci k Red Hat](https://access.redhat.com/articles/4847681).

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Požadavky a podmínky pro přístup k obrázkům Red Hat Gold

1. Seznamte se s podmínkami [programu Red Hat Cloud Access.](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) Povolte předplatná Red Hat pro Cloud Access ve Správci [předplatného Red Hat](https://access.redhat.com/management/cloud). Musíte mít po ruce předplatná Azure, která budou registrovaná pro Cloud Access.

1. Pokud předplatná Red Hat, která jste povolili pro Cloud Access, splňují požadavky na způsobilost, vaše předplatná Azure se automaticky povedou pro přístup ke zlaté image.

### <a name="expected-time-for-image-access"></a>Očekávaný čas pro přístup k obrázkům

Po dokončení kroků povolení cloudového přístupu redhat ověří vaši způsobilost pro zlaté obrázky Red Hat. Pokud je ověření úspěšné, získáte přístup ke zlatým obrazům do tří hodin.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Použití zlatých obrázků Red Hat z portálu Azure

1. Po získání přístupu k obrázkům Red Hat Gold získáte přístup k obrázkům Red Hat Gold, které můžete najít na [webu Azure Portal](https://portal.azure.com). Přejděte na **Vytvořit prostředek** > **Zobrazit vše**.

1. V horní části stránky uvidíte, že máte soukromé nabídky.

    ![Soukromé nabídky marketplace](./media/rhel-byos-privateoffers.png)

1. Vyberte fialový odkaz nebo přejděte dolů do dolní části stránky a podívejte se na své soukromé nabídky.

1. Zbytek zřizování v ui se neliší od jakékoli jiné existující image Red Hat. Vyberte si verzi RHEL a postupujte podle pokynů k zřízení virtuálního počítače. Tento proces také umožňuje přijmout podmínky obrázku v posledním kroku.

>[!NOTE]
>Tyto kroky zatím neumožní váš Red Hat Gold Image pro programové nasazení. Další krok je vyžadován, jak je popsáno v části "Další informace".

Zbytek tohoto dokumentu se zaměřuje na metodu zaokreslování k zajištění a přijetí podmínek na obrázku. UI a CLI jsou plně zaměnitelné, pokud jde o konečný výsledek (zřízené RHEL Gold Image VM) se týká.

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Použití zlatých obrázků Red Hat z azure cli

Následující pokyny vás provedou procesem počátečního nasazení pro virtuální počítač RHEL pomocí příkazového příkazu k onomu Azure. Tyto pokyny předpokládají, že máte [nainstalovaný Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Pro všechny následující příkazy používejte všechna malá písmena v odkazech na majitele stránek, nabídky, plány a obrázky.

1. Zkontrolujte, zda jste v požadovaném předplatném.

    ```azurecli
    az account show -o=json
    ```

1. Vytvořte skupinu prostředků pro virtuální počítač S red hat gold image.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Přijměte podmínky obrázku.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Tyto podmínky je třeba přijmout *jednou za předplatné Azure, na image SKU*.

1. (Nepovinné) Ověřte nasazení virtuálního počítače pomocí následujícího příkazu:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Zřízení virtuálního počítače spuštěním stejného příkazu, `--validate` jak je znázorněno v předchozím příkladu bez argumentu.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. SSH do virtuálního počítače a ověřte, že máte neoprávněný obrázek. Chcete-li provést `sudo yum repolist`tento krok, spusťte . Pro RHEL 8 `sudo dnf repolist`použijte . Výstup vás vyzve k použití Správce předplatného k registraci virtuálního zařízení pomocí Red Hatu.

>[!NOTE]
>Na RHEL `dnf` 8, a `yum` jsou zaměnitelné. Další informace naleznete v [průvodci správcem RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Použití zlatých obrázků Red Hat z prostředí PowerShell

Následující skript je příkladem. Nahraďte skupinu prostředků, umístění, název virtuálního počítače, přihlašovací údaje a další proměnné konfigurací podle vašeho výběru. Informace o aplikaci Publisher a plánu musí být malá písmena.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher RedHat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

    # Create a resource group
    New-AzureRmResourceGroup -Name $resourceGroup -Location $location

    # Create a subnet configuration
    $subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

    # Create a virtual network
    $vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location
    $location `-Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

    # Create a public IP address and specify a DNS name
    $pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location
    $location `-Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

    # Create an inbound network security group rule for port 22
    $nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name
    myNetworkSecurityGroupRuleSSH -Protocol Tcp `
    -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -
    DestinationAddressPrefix * `-DestinationPortRange 22 -Access Allow

    # Create a network security group
    $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location
    $location `-Name myNetworkSecurityGroup -SecurityRules $nsgRuleSSH

    # Create a virtual network card and associate with public IP address and NSG
    $nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -
    Location $location `-SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

    # Create a virtual machine configuration
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D3_v2 |
    Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred |
    Set-AzureRmVMSourceImage -PublisherName redhat -Offer rhel-byos -Skus rhel-lvm75 -Version latest | Add-     AzureRmVMNetworkInterface -Id $nic.Id
    Set-AzureRmVMPlan -VM $vmConfig -Publisher redhat -Product rhel-byos -Name "rhel-lvm75"

    # Configure SSH Keys
    $sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"
    Add-AzureRmVMSshPublicKey -VM $vmconfig -KeyData $sshPublicKey -Path "/home/azureuser/.ssh/authorized_keys"

    # Create a virtual machine
    New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Šifrovat Red Hat Enterprise Linux bring-your-vlastní-předplatné Gold Obrázky

Zlaté obrázky Red Hat Enterprise Linux BYOS lze zabezpečit pomocí [azure disk encryptionu](../../linux/disk-encryption-overview.md). Před povolením šifrování *je nutné* předplatné zaregistrovat. Další informace o tom, jak zaregistrovat zlatý obrázek RHEL BYOS, najdete v tématu [Jak se zaregistrovat a přihlásit k odběru systému zákaznického portálu Red Hat pomocí správce předplatného Red Hat](https://access.redhat.com/solutions/253273). Pokud máte aktivní předplatné Red Hat, můžete si také přečíst [vytvoření aktivačních klíčů zákaznického portálu Red Hat](https://access.redhat.com/articles/1378093).

Azure Disk Encryption není podporována na [vlastní image Red Hat](../../linux/redhat-create-upload-vhd.md). Další požadavky a požadavky na šifrování disku Azure jsou popsány v [Azure Disk Encryption pro virtuální počítače s Linuxem](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Postup použití Azure Disk Encryption najdete v článku [Scénáře šifrování disku Azure na virtuálních počítačích s Linuxem](../../linux/disk-encryption-linux.md) a souvisejících článcích.

## <a name="additional-information"></a>Další informace

- Pokud se pokusíte zřídit virtuální počítač na předplatné, které není povoleno pro tuto nabídku, zobrazí se následující zpráva:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    V takovém případě se obraťte na společnost Microsoft nebo Red Hat a povolte předplatné.

- Pokud změníte snímek z bitové kopie RHEL BYOS a pokusíte se publikovat tento vlastní obrázek do [galerie sdílených obrázků](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries), musíte poskytnout informace o plánu, které odpovídají původnímu zdroji snímku. Příkaz může například vypadat takto:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Poznamenejte si parametry plánu v posledním řádku.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) není podporována na vlastní image.

- Pokud používáte automatizaci k zřízení virtuálních mích z bitových kopií RHEL BYOS, musíte zadat parametry plánu podobné tomu, co bylo zobrazeno v ukázkových příkazech. Pokud například používáte Terraform, zadáte informace o plánu v [bloku plánu](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Další kroky

- Podrobné pokyny a podrobnosti o programu pro cloudový přístup najdete v dokumentaci k [redhat cloudaccessu](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Další informace o infrastruktuře aktualizací Red Hat najdete v tématu [Azure Red Hat Update Infrastructure](./redhat-rhui.md).
- Další informace o všech ibi Red Hatv Azure najdete na [stránce dokumentace](./redhat-images.md).
- Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [životního cyklu Red Hat Enterprise Linux.](https://access.redhat.com/support/policy/updates/errata)
- Další dokumentaci k zlatým obrazům RHEL naleznete v [dokumentaci k Red Hatu](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
