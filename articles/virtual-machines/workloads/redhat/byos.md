---
title: Red Hat Enterprise Linux vlastní předplatné Azure images | Microsoft Docs
description: Přečtěte si o imagích s vlastními předplatnými pro Red Hat Enterprise Linux v Azure.
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
ms.date: 06/10/2020
ms.author: alsin
ms.openlocfilehash: 54d703b8a493610174f00844cd0736f65f3ee541
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87052169"
---
# <a name="red-hat-enterprise-linux-bring-your-own-subscription-gold-images-in-azure"></a>Red Hat Enterprise Linux ve službě Azure na základě vlastních předplatných zlatých imagí

V Azure jsou k dispozici image Red Hat Enterprise Linux (RHEL) prostřednictvím modelu průběžných plateb (BYOS) (Image Red Hat Gold). Tento článek poskytuje přehled imagí Red Hat Gold v Azure.

>[!NOTE]
> Image RHEL BYOS Gold jsou k dispozici ve veřejných cloudech Azure (komerčních) a Azure Government. Nejsou k dispozici v Azure Čína ani v cloudech Azure blackforest.

## <a name="important-points-to-consider"></a>Důležité body, které je třeba zvážit

- Image Red Hat Gold dodávané v tomto programu jsou RHEL obrázky připravené pro produkční prostředí, podobně jako image RHEL s průběžnými platbami v Azure Marketplace.
- Image se řídí aktuálními zásadami popsanými v [Red Hat Enterprise Linux imagí v Azure](./redhat-images.md).
- Standardní zásady podpory se vztahují na virtuální počítače vytvořené z těchto imagí.
- Virtuální počítače zřízené z imagí Red Hat Gold nenesou RHEL poplatky spojené s imagemi RHEL s průběžnými platbami.
- Obrázky jsou neoprávněné. K registraci a přihlášení k odběru virtuálních počítačů za účelem přímého získání aktualizací ze Red Hat je nutné použít Red Hat Subscription-Manager.
- V současné době není možné dynamicky přepínat mezi BYOS a modelem fakturace s průběžnými platbami pro Image Linux. Chcete-li přepnout model fakturace, je nutné znovu nasadit virtuální počítač z příslušné bitové kopie.

>[!NOTE]
> RHEL 2. generace BYOS image nejsou aktuálně dostupné prostřednictvím nabídky Marketplace. Pokud potřebujete image BYOS generace 2 RHEL, přejděte na řídicí panel Cloud Access ve správě předplatných Red Hat. Další informace najdete v dokumentaci k [Red Hat](https://access.redhat.com/articles/4847681).

## <a name="requirements-and-conditions-to-access-the-red-hat-gold-images"></a>Požadavky a podmínky pro přístup k obrazům Red Hat Gold

1. Seznamte se s podmínkami [programu Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) . Umožněte svým předplatným Red Hat pro cloudový přístup v [Red Hat Subscription-Manager](https://access.redhat.com/management/cloud). Musíte mít k dispozici předplatná Azure, která se budou registrovat pro cloudový přístup.

1. Pokud je u předplatných Red Hat, které jste povolili pro přístup k cloudu, splněny požadavky na způsobilost, budou mít vaše předplatná Azure automaticky povolený přístup k imagi

### <a name="expected-time-for-image-access"></a>Očekávaný čas pro přístup k obrázku

Po dokončení kroků povolení přístupu do cloudu ověří Red Hat nárok na Image Red Hat Gold. Pokud je ověření úspěšné, dostanete do tří hodin přístup ke zlatým obrázkům.

## <a name="use-the-red-hat-gold-images-from-the-azure-portal"></a>Použití imagí Red Hat Gold z Azure Portal

1. Jakmile vaše předplatné Azure získá přístup k obrázkům Red Hat Gold, můžete je vyhledat v [Azure Portal](https://portal.azure.com). Přejděte na **vytvořit prostředek**  >  **Zobrazit vše**.

1. V horní části stránky uvidíte, že máte soukromé nabídky.

    ![Veřejné nabídky Marketplace](./media/rhel-byos-privateoffers.png)

1. Vyberte fialový odkaz, nebo se posuňte dolů k dolnímu okraji stránky, aby se zobrazily vaše soukromé nabídky.

1. Zbytek zřizování v uživatelském rozhraní se neliší od ostatních stávajících imagí Red Hat. Vyberte verzi RHEL a postupujte podle pokynů pro zřízení virtuálního počítače. Tento proces také umožňuje přijmout podmínky obrázku v posledním kroku.

>[!NOTE]
>Tyto kroky zatím neumožňují instalaci Red Hat Gold pro programové nasazení. Další krok je vyžadován, jak je popsáno v části "Další informace".

Zbytek tohoto dokumentu se zaměřuje na metodu CLI, která umožňuje zřídit a přijmout podmínky pro image. Uživatelské rozhraní a CLI jsou plně zaměnitelné, pokud se jedná o konečný výsledek (zřízený virtuální počítač RHEL Gold Image).

## <a name="use-the-red-hat-gold-images-from-the-azure-cli"></a>Použití imagí Red Hat Gold z Azure CLI

Následující pokyny vás provedou procesem prvotního nasazení pro virtuální počítač s RHEL pomocí Azure CLI. V těchto pokynech se předpokládá, že máte nainstalované rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli).

>[!IMPORTANT]
>Ujistěte se, že používáte všechna malá písmena v odkazech vydavatele, nabídky, plánu a obrázku pro všechny následující příkazy.

1. Ověřte, že jste v požadovaném předplatném.

    ```azurecli
    az account show -o=json
    ```

1. Vytvořte skupinu prostředků pro virtuální počítač Red Hat Gold Image.

    ```azurecli
    az group create --name <name> --location <location>
    ```

1. Přijměte podmínky pro obrázek.

    ```azurecli
    az vm image terms accept --publisher redhat --offer rhel-byos --plan <SKU value here> -o=jsonc

    # Example:
    az vm image terms accept --publisher redhat --offer rhel-byos --plan rhel-lvm75 -o=jsonc

    OR

    az vm image terms accept --urn redhat:rhel-byos:rhel-lvm8:8.0.20190620
    ```

    >[!NOTE]
    >Tyto výrazy je potřeba přijmout *jednou pro každou skladovou jednotku na obrázku za předplatné Azure*.

1. Volitelné Ověřte nasazení virtuálního počítače pomocí následujícího příkazu:

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn> --validate

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest --validate
    ```

1. Zřiďte virtuální počítač spuštěním stejného příkazu, jak je znázorněno v předchozím příkladu bez `--validate` argumentu.

    ```azurecli
    az vm create -n <VM name> -g <resource group name> --image <image urn>

    # Example:
    az vm create -n rhel-byos-vm -g rhel-byos-group --image redhat:rhel-byos:rhel-lvm8:latest
    ```

1. Připojte se k VIRTUÁLNÍmu počítači přes SSH a ověřte, jestli máte neoprávněnou image. Chcete-li provést tento krok, spusťte příkaz `sudo yum repolist` . Pro RHEL 8 použijte `sudo dnf repolist` . Výstup vás vyzve k použití Subscription-Manager k registraci virtuálního počítače pomocí Red Hat.

>[!NOTE]
>V RHEL 8 `dnf` a `yum` jsou zaměnitelné. Další informace najdete v příručce pro [správce RHEL 8](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/packaging_and_distributing_software/index).

## <a name="use-the-red-hat-gold-images-from-powershell"></a>Použití imagí Red Hat Gold z PowerShellu

Následující skript je příkladem. Podle konfigurace podle vlastního výběru nahraďte skupinu prostředků, umístění, název virtuálního počítače, přihlašovací informace a další proměnné. Informace o vydavateli a plánu musí být malé.

```powershell-interactive
    # Variables for common values
    $resourceGroup = "testbyos"
    $location = "canadaeast"
    $vmName = "test01"

    # Define user name and blank password
    $securePassword = ConvertTo-SecureString 'TestPassword1!' -AsPlainText -Force
    $cred = New-Object System.Management.Automation.PSCredential("azureuser",$securePassword)
    Get-AzureRmMarketplaceTerms -Publisher redhat -Product rhel-byos -Name rhel-lvm75 | SetAzureRmMarketplaceTerms -Accept

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

Red Hat Enterprise Linux BYOS zlaté obrázky lze zabezpečit pomocí [Azure Disk Encryption](../../linux/disk-encryption-overview.md). Aby bylo možné povolit šifrování, *musí* být předplatné registrováno. Další informace o tom, jak zaregistrovat RHEL BYOS Gold, najdete v tématu [registrace a přihlášení k odběru systému na portál Red Hat Customer pomocí Správce předplatného Red Hat](https://access.redhat.com/solutions/253273). Pokud máte aktivní předplatné Red Hat, můžete si také přečíst téma [vytváření aktivačních klíčů pro zákazníka Red Hat](https://access.redhat.com/articles/1378093).

U [vlastních imagí Red Hat](../../linux/redhat-create-upload-vhd.md)se Azure Disk Encryption nepodporuje. Další Azure Disk Encryption požadavky a požadavky jsou popsány v [Azure Disk Encryption pro virtuální počítače se systémem Linux](../../linux/disk-encryption-overview.md#additional-vm-requirements).

Postup pro použití Azure Disk Encryption najdete v tématu [scénáře Azure Disk Encryption na virtuálních počítačích se systémem Linux a v](../../linux/disk-encryption-linux.md) souvisejících článcích.

## <a name="additional-information"></a>Další informace

- Pokud se pokusíte zřídit virtuální počítač v předplatném, které není pro tuto nabídku povoleno, zobrazí se následující zpráva:

    ```
    "Offer with PublisherId: redhat, OfferId: rhel-byos, PlanId: rhel-lvm75 is private and can not be purchased by subscriptionId: GUID"
    ```

    V takovém případě kontaktujte Microsoft nebo Red Hat, abyste povolili vaše předplatné.

- Pokud upravíte snímek z image RHEL BYOS a pokusíte se o publikování této vlastní image do [Galerie sdílených imagí](../../linux/shared-image-galleries.md), je nutné zadat informace o plánu, který odpovídá původnímu zdroji snímku. Příkaz může vypadat například takto:

    ```azurecli
    az vm create –image \
    "/subscriptions/GUID/resourceGroups/GroupName/providers/Microsoft.Compute/galleries/GalleryName/images/ImageName/versions/1.0.0" \
    -g AnotherGroupName --location EastUS2 -n VMName \
    --plan-publisher redhat --plan-product rhel-byos --plan-name rhel-lvm75
    ```

    Poznamenejte si parametry plánu v posledním řádku.

    [Azure Disk Encryption](#encrypt-red-hat-enterprise-linux-bring-your-own-subscription-gold-images) se u vlastních imagí nepodporuje.

- Pokud používáte automatizaci k zřizování virtuálních počítačů z imagí RHEL BYOS, je nutné zadat parametry plánu podobné tomu, co bylo zobrazeno v ukázkových příkazech. Například pokud používáte Terraformu, zadáte informace o plánu do [bloku plánu](https://www.terraform.io/docs/providers/azurerm/r/virtual_machine.html#plan).

## <a name="next-steps"></a>Další kroky

- Další podrobnosti o cloudovém přístupu Red Hat jsou k dispozici v [dokumentaci k veřejnému cloudu Red Hat](https://access.redhat.com/public-cloud) .
- Podrobné návody a podrobné informace o programu pro cloudový přístup najdete v dokumentaci k řešení [Red Hat Cloud Access](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index).
- Další informace o infrastruktuře aktualizace Red Hat najdete v tématu [infrastruktura aktualizací Azure Red Hat](./redhat-rhui.md).
- Další informace o všech obrázcích Red Hat v Azure najdete na [stránce s dokumentací](./redhat-images.md).
- Informace o zásadách podpory Red Hat pro všechny verze RHEL najdete na stránce [Red Hat Enterprise Linux životní cyklus](https://access.redhat.com/support/policy/updates/errata) .
- Další dokumentaci k RHEL Gold imagí najdete v [dokumentaci k Red Hat](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/using_red_hat_gold_images#con-gold-image-azure).
