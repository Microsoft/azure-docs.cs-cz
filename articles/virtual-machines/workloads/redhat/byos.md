---
title: Red Hat Enterprise Linux vlastní předplatné Azure images | Microsoft Docs
description: Přečtěte si o imagích s vlastními předplatnými pro Red Hat Enterprise Linux v Azure
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
ms.openlocfilehash: 712626345e10ab0e4290ac91b0f121ff6960303e
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920565"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux ve službě Azure na základě vlastních předplatných zlatých imagí

V Azure jsou k dispozici image Red Hat Enterprise Linux (RHEL), a to prostřednictvím modelu PAYG (průběžné platby s průběžnými platbami) nebo vlastního předplatného (Red Hat Gold Image). Tento dokument poskytuje přehled imagí Red Hat Gold v Azure.

>[!NOTE]
> Image RHEL BYOS Gold jsou k dispozici v Azure Public (komerční) a v cloudech Azure Government. Nejsou k dispozici v Azure Čína ani v cloudech Azure blackforest.

## <a name="important-points-to-consider"></a>Důležité body, které je třeba zvážit

- Image Red Hat Gold dodávané v tomto programu jsou RHEL obrázky připravené pro produkční prostředí podobné RHEL PAYG obrázků v galerii Azure nebo na webu Marketplace.

- Obrázky následují naše aktuální zásady popsané v tématu [Red Hat Enterprise Linux images v Azure](./redhat-images.md) .

- Standardní zásady podpory se vztahují na virtuální počítače vytvořené z těchto imagí.

- Virtuální počítače zřízené z imagí Red Hat Gold nenesou RHEL poplatky spojené s imagemi PAYG RHEL.

- Image jsou neoprávněné, takže musíte použít Správce předplatného k registraci a přihlášení k odběru virtuálních počítačů za účelem získání aktualizací ze Red Hat přímo.

- V současné době není možné dynamicky přepínat mezi BYOS a PAYG modely fakturace pro Image Linux. K přepnutí modelu fakturace se vyžaduje opětovné nasazení virtuálního počítače z příslušné image.

>[!NOTE]
> Image generace 2 RHEL BYOS nejsou aktuálně k dispozici prostřednictvím nabídky Marketplace. Pokud potřebujete image BYOS s RHELou Gen 2, přejděte na řídicí panel coud Access ve správě předplatných Red Hat. Další podrobnosti najdete v dokumentaci k [Red Hat](https://access.redhat.com/articles/4847681).

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Požadavky a podmínky pro přístup k obrazům Red Hat Gold

1. Seznamte se s podmínkami [programu Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) a Umožněte svým předplatným Red Hat pro cloudový přístup ve [Správci Red Hat Subscription Manager](https://access.redhat.com/management/cloud). Budete muset mít k dispozici předplatné Azure, které se bude registrovat pro cloudový přístup.

1. Pokud jste povolili předplatné Red Hat pro cloudový přístup, který splňuje správné požadavky na způsobilost, budou mít vaše předplatná Azure automaticky povolený přístup k obrazům Gold.

### <a name="expected-time-for-image-access"></a>Očekávaný čas pro přístup k obrázku

Po dokončení kroků povolení přístupu ke cloudu vám Red Hat ověří nárok na Image Red Hat Gold. Pokud bude ověření úspěšné, dostanete během tří hodin přístup ke zlatým imagím.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Použití imagí Red Hat Gold z Azure Portal

1. Jakmile vaše předplatné Azure získá přístup k obrázkům Red Hat Gold, můžete je vyhledat v [Azure Portal](https://portal.azure.com) tak, že přejdete na **vytvořit prostředek** a pak **zobrazíte vše**.

1. V horní části stránky se zobrazí, že máte soukromé nabídky.

    ![Veřejné nabídky Marketplace](./media/rhel-byos-privateoffers.png)

1. Můžete kliknout na fialový odkaz nebo přejít dolů k dolnímu okraji stránky a zobrazit vaše soukromé nabídky.

1. Zbytek zřizování v uživatelském rozhraní se neliší od žádné jiné existující image Red Hat. Vyberte verzi RHEL a podle pokynů zřiďte svůj virtuální počítač. Tento postup vám také umožní přijmout podmínky image v posledním kroku.

>[!NOTE]
>Tyto kroky zatím neumožňují pro programové nasazení obrázek Red Hat Gold. další krok bude nutný, jak je popsáno v části Další informace.

Zbytek tohoto dokumentu se zaměřuje na metodu CLI, která umožňuje zřídit a přijmout podmínky pro image. Uživatelské rozhraní a CLI jsou plně zaměnitelné, pokud se jedná o konečný výsledek (zřízený virtuální počítač RHEL Gold Image).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Použití imagí Red Hat Gold z Azure CLI
Následující postup vás provede procesem prvotního nasazení pro virtuální počítač s RHEL pomocí rozhraní příkazového řádku Azure CLI. V těchto pokynech se předpokládá, že máte nainstalované rozhraní příkazového [řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Ujistěte se, že používáte všechna malá písmena v odkazech vydavatele, nabídky, plánu a obrázku pro všechny následující příkazy.

1. Ověřte, že jste v požadovaném předplatném:
    ```azurecli
    az account show -o=json
    ```

1. Vytvořte skupinu prostředků pro virtuální počítač s Red Hat Gold s obrázkem:
    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Přijměte podmínky pro Image:
    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn RedHat:rhel-byos:rhel-lvm8:8.0.20190620
    ```
    >[!NOTE]
    >Tyto výrazy je potřeba přijmout *jednou pro každou skladovou jednotku na obrázku za předplatné Azure*.

1. Volitelné Ověřte nasazení virtuálního počítače pomocí následujícího příkazu:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image RedHat:rhel-byos:rhel-lvm75:7.5.20190620
    ```

1. Zřiďte virtuální počítač spuštěním stejného příkazu, který není bez `--validate` argumentu:
    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate
    ```

1. Připojte se k VIRTUÁLNÍmu počítači přes SSH a ověřte, jestli máte neoprávněnou image. Pokud to chcete provést, spusťte `sudo yum repolist` (pro RHEL 8 použijte `sudo dnf repolist`). Výstup vás vyzve k použití Správce předplatného k registraci virtuálního počítače pomocí Red Hat.

>[!NOTE]
>V RHEL 8 `dnf` a `yum` jsou zaměnitelné, další informace najdete v [příručce pro správce RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/installing-software-with-yum_configuring-basic-system-settings).


## <a name="use-the-red-hat-gold-images-from-powershell"></a>Použití imagí Red Hat Gold z PowerShellu
Následuje ukázkový skript. Měli byste nahradit skupinu prostředků, umístění, název virtuálního počítače, přihlašovací informace a další proměnné s konfigurací, kterou zvolíte. Informace o vydavateli a plánu musí být malé.

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

## <a name="encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images"></a>Šifrování zlatých imagí Red Hat Enterprise Linux s využitím vlastních předplatných

Pomocí [Azure Disk Encryption](../../linux/disk-encryption-overview.md)je možné zabezpečit zlaté image, které se dají použít Red Hat Enterprise Linux. Předplatné ale **musí** být zaregistrované předtím, než se povolí šifrování.  Podrobnosti o registraci RHEL BYOS Gold jsou k dispozici na webu Red Hat. Podívejte se [, jak zaregistrovat a přihlásit se k odběru systému na portál Red Hat Customer pomocí programu Red Hat Subscription – Manager](https://access.redhat.com/solutions/253273). Pokud máte aktivní předplatné Red Hat, můžete si také přečíst téma [vytváření aktivačních klíčů pro zákazníka Red Hat](https://access.redhat.com/articles/1378093).

Azure Disk Encryption se u [vlastních imagí Red Hat](../../linux/redhat-create-upload-vhd.md)nepodporuje. Další požadavky a požadavky na ADE jsou popsány v [Azure Disk Encryption pro virtuální počítače se systémem Linux](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Kroky pro použití Azure Disk Encryption jsou k dispozici ve [scénářích Azure Disk Encryption na virtuálních počítačích s Linux a v](../../linux/disk-encryption-linux.md) souvisejících článcích.

## <a name="additional-information"></a>Další informace

- Pokud se pokusíte zřídit virtuální počítač v předplatném, které není pro tuto nabídku povoleno, zobrazí se následující chyba:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    V takovém případě kontaktujte Microsoft nebo Red Hat, abyste povolili vaše předplatné.

- Pokud upravíte snímek z image RHEL BYOS a pokusíte se o publikování této vlastní image do [Galerie sdílených imagí](https://docs.microsoft.com/azure/virtual-machines/linux/shared-image-galleries), je nutné zadat informace o plánu, který odpovídá původnímu zdroji snímku. Příkaz může vypadat například takto:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```
    Poznamenejte si parametry plánu v posledním řádku.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) se nepodporuje u vlastních imagí.

- Pokud používáte automatizaci k zřizování virtuálních počítačů z imagí RHEL BYOS, musíte zadat parametry plánu podobné tomu, co bylo uvedeno výše. Například pokud používáte Terraformu, zadejte informace o plánu do [bloku plánu](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Další kroky
- Podrobné návody a podrobnosti o programu pro cloudový přístup jsou k dispozici v dokumentaci k řešení [Red Hat Cloud Access.](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)
- Přečtěte si další informace o [infrastruktuře aktualizací Red Hat pro Azure](./redhat-rhui.md).
- Pokud se chcete dozvědět víc o všech obrázcích Red Hat v Azure, navštivte [stránku dokumentace](./redhat-images.md).
- Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .
- Další dokumentaci k RHEL Gold imagí najdete v [dokumentaci k Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
