---
title: Azure Disk Encryption pro Windows a virtuální počítače s Linuxem v režimu IaaS | Dokumentace Microsoftu
description: Tento článek je dodatek pro Microsoft Azure Disk Encryption pro Windows a virtuálních počítačů IaaS s Linuxem.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 09/10/2018
ms.openlocfilehash: 2f932ff39495916c4a9fb55714c73383e06c72e1
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2018
ms.locfileid: "44346839"
---
# <a name="appendix-for-azure-disk-encryption"></a>Dodatek pro Azure Disk Encryption 
Tento článek je dodatek k [Azure Disk Encryption pro virtuální počítače IaaS](azure-security-disk-encryption-overview.md). Nezapomeňte že si přečíst Azure Disk Encryption pro virtuální počítače IaaS články nejprve k pochopení kontextu. Tento článek popisuje, jak připravit předem šifrované virtuální pevné disky a další úlohy.

## <a name="connect-to-your-subscription"></a>Připojení k vašemu předplatnému
Než začnete, projděte si [požadavky](azure-security-disk-encryption-prerequisites.md) článku. Jakmile jsou splněné všechny požadavky, připojení k vašemu předplatnému spuštěním následující rutiny:

### <a name="bkmk_ConnectPSH"></a> Připojení k vašemu předplatnému pomocí Powershellu

1. Spusťte relaci Azure Powershellu a přihlaste se ke svému účtu Azure pomocí následujícího příkazu:

     ```powershell
     Connect-AzureRmAccount 
     ```
2. Pokud máte více předplatných a chcete určit nich se má použít, zadejte následující příkaz k zobrazení předplatných pro váš účet:
     
     ```powershell
     Get-AzureRmSubscription
     ```
3. Pokud chcete zadat předplatné, které chcete použít, zadejte:
 
     ```powershell
      Select-AzureRmSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. Pokud chcete ověřit správnost předplatné nakonfigurované, zadejte:
     
     ```powershell
     Get-AzureRmSubscription
     ```
5. V případě potřeby připojení k Azure AD s [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```
6. Pokud chcete potvrdit, že jsou nainstalované rutiny Azure Disk Encryption, zadejte:
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. Kontrola [Začínáme s Azure Powershellem](/powershell/azure/get-started-azureps) a [AzureAD](/powershell/module/azuread), v případě potřeby.

### <a name="bkmk_ConnectCLI"></a> Připojení k vašemu předplatnému pomocí Azure CLI

1. Přihlaste se k Azure s využitím [az login](/cli/azure/authenticate-azure-cli#interactive-log-in). 
     
     ```azurecli
     az login
     ```

2. Pokud chcete vybrat tenanta se přihlásit pod, použijte:
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. Pokud máte více předplatných a chcete určit jedno konkrétní, získejte seznam vašich předplatných s [seznam účtů az](/cli/azure/account#az-account-list) a zadat [az účet sady](/cli/azure/account#az-account-set).
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Zkontrolujte nainstalovanou verzi.
     
     ```azurecli
        az --version
     ``` 

5. Kontrola [Začínáme s Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) v případě potřeby. 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Ukázkové skripty Powershellu pro Azure Disk Encryption 

- **Seznam všech šifrovaných virtuálních počítačů v rámci vašeho předplatného**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzureRmVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzureRmVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Seznam všech tajných kódů šifrování disku používaný k šifrování virtuálních počítačů ve službě key vault** 

     ```azurepowershell-interactive
     Get-AzureKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="bkmk_prereq-script"></a> Pomocí skriptu Powershellu požadavky Azure Disk Encryption
Pokud jste již obeznámeni s požadavky pro Azure Disk Encryption, můžete použít [skript prostředí PowerShell pro Azure Disk Encryption požadované součásti](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Příklad použití tento skript Powershellu, najdete v článku [šifrování virtuálního počítače Quickstart](quick-encrypt-vm-powershell.md). Komentáře můžete odebrat z část skriptu, počínaje řádkem 211, k šifrování všech disků pro stávající virtuální počítače v existující skupinu prostředků. 

V následující tabulce jsou uvedeny parametry, které lze použít ve skriptu prostředí PowerShell: 


|Parametr|Popis|Je povinné.|
|------|------|------|
|$resourceGroupName| Název skupiny prostředků, ke kterému patří trezoru klíčů.  Novou skupinu prostředků s tímto názvem bude vytvořen, pokud neexistuje.| True|
|$keyVaultName|Název trezoru klíčů, ve které šifrovací klíče jsou umístit. Nový trezor s tímto názvem bude vytvořen, pokud neexistuje.| True|
|$location|Umístění trezoru klíčů. Ujistěte se, že trezor klíčů a virtuální počítače k šifrování jsou ve stejném umístění. Seznam umístění získáte pomocí rutiny `Get-AzureRMLocation`.|True|
|$subscriptionId|Identifikátor předplatného Azure, který se má použít.  Své ID předplatného můžete získat pomocí rutiny `Get-AzureRMSubscription`.|True|
|$aadAppName|Název aplikace Azure AD, která se použije k zápisu tajných klíčů do trezoru klíčů. Pokud aplikace se zadaným názvem neexistuje, vytvoří se nová. Pokud tato aplikace už existuje, předejte parametr aadClientSecret skriptu.|False|
|$aadClientSecret|Tajný kód klienta aplikace Azure AD, který jste vytvořili dříve.|False|
|$keyEncryptionKeyName|Název volitelné šifrovací klíč klíče v trezoru klíčů. Nový klíč s tímto názvem bude vytvořen, pokud neexistuje.|False|


## <a name="resource-manager-templates"></a>Šablony Resource Manageru

<!--   - [Create a key vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) -->

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Šifrování nebo dešifrování virtuálních počítačů bez aplikace Azure AD


- [Povolit šifrování disku v existující nebo spouštění virtuálních počítačů IaaS s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)
- [Zakázat šifrování disku v existující nebo spouštění virtuálních počítačů IaaS s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
- [Povolit šifrování disku v existující nebo spuštěné IaaS virtuálního počítače s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
 -  [Zakažte šifrování u spuštěného virtuálního počítače s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Zakázáním šifrování je povolena pouze u svazků s daty pro virtuální počítače s Linuxem.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Šifrování nebo dešifrování virtuálních počítačů pomocí aplikace Azure AD (předchozí verze) 
 
- [Povolit šifrování disku v existující nebo spouštění virtuálních počítačů IaaS s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [Povolit šifrování disku v existující nebo spuštěné IaaS virtuálního počítače s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    

- [Zakázat šifrování disku ve spuštění Windows IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [Zakažte šifrování u spuštěného virtuálního počítače s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Zakázáním šifrování je povolena pouze u svazků s daty pro virtuální počítače s Linuxem. 

- [Povolit šifrování disku na nový virtuální počítač IaaS Windows z Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - Tato šablona vytvoří nový virtuální počítač šifrovaný Windows s, která používá image z galerie systému Windows Server 2012.

- [Vytvoření nové šifrované Windows IaaS spravovaného disku virtuálního počítače z image z Galerie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - Tato šablona vytvoří nový virtuální počítač šifrovaný Windows se spravovanými disky, které používají image z galerie systému Windows Server 2012.

- [Nasazení RHEL 7.2 šifrování celého disku se spravovanými disky](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel)
    - Tato šablona vytvoří plně zašifrované virtuální počítač s RHEL 7.2 v Azure s použitím spravovaných disků. Obsahuje zašifrované jednotky operačního systému 30 GB a šifrované pole 200 GB (RAID-0) připojil /mnt/raidencrypted. Zobrazit [nejčastější dotazy k](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) článku podporované distribuce systému Linux server. 

- [Nasazení RHEL 7.2 šifrování celého disku s nespravovanými disky](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-full-disk-encrypted-rhel-unmanaged)
    - Tato šablona vytvoří plně zašifrované virtuální počítač s RHEL 7.2 v Azure pomocí zašifrované jednotky operačního systému 30 GB a šifrované pole 200 GB (RAID-0) připojil /mnt/raidencrypted. Zobrazit [nejčastější dotazy k](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) článku podporované distribuce systému Linux server. 

- [Povolit šifrování disku v předem šifrované virtuální pevný disk pro Windows nebo Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-pre-encrypted-vm)

- [Vytvoření nové šifrované spravovaného disku z předem šifrované virtuální pevný disk nebo úložiště objektů blob](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Vytvoří nové šifrované spravovaného disku k dispozici předem šifrované virtuální pevný disk a odpovídajících nastavení šifrování

- [Povolit šifrování disku ve spuštěném virtuálním počítači Windows pomocí kryptografický otisk certifikátu klienta Azure AD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-aad-client-cert)
    
- [Povolit šifrování disku ve škálovací sadě virtuálních počítačů spuštěné systému Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Povolit šifrování disku ve škálovací sady virtuálních počítačů spuštěných Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

 - [Nasadit počítače VM Scale Sets ze systému Linux s jumpbox a povoluje šifrování na VMSS v Linuxu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

 - [Nasadit počítače VM Scale Sets z Windows pomocí jumpbox a povoluje šifrování na Windows VMSS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Zakázat šifrování disku ve škálovací sadě virtuálních počítačů spuštěné systému Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Zakázat šifrování disku ve škálovací sady virtuálních počítačů spuštěných Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

## <a name="bkmk_preWin"></a> Příprava virtuálního pevného disku předem šifrované Windows
Následující části jsou nezbytné pro přípravu předem šifrované virtuální pevný disk Windows pro nasazení jako šifrovaný virtuální pevný disk v Azure IaaS. Použijte informace k přípravě a spuštění virtuálního počítače čerstvé Windows (VHD) na Azure Site Recovery a Azure. Další informace o tom, jak připravit a nahrání virtuálního pevného disku najdete v tématu [nahrání generalizovaného virtuálního pevného disku a použít ho k vytvoření nové virtuální počítače v Azure](../virtual-machines/windows/upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualizace zásad skupiny umožňující bez TPM pro ochranu operačního systému
Konfigurace nastavení zásad skupiny bitlockeru určují **nástroj BitLocker Drive Encryption**, které najdete v části **zásady místního počítače** > **konfigurace počítače**  >  **Šablony pro správu** > **součásti Windows**. Změna tohoto nastavení můžete **jednotky operačního systému** > **vyžadovat další ověření při spuštění** > **povolit nástroj BitLocker bez kompatibilního čipu TPM**, jak je znázorněno na následujícím obrázku:

![Microsoft Antimalware v Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Instalace součásti funkcí nástroje BitLocker
Pro Windows Server 2012 a novější použijte následující příkaz:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Windows Server 2008 R2 použijte následující příkaz:

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Příprava svazek s operačním systémem pomocí Bitlockeru `bdehdcfg`
Pokud chcete komprimovat oddílu operačního systému a příprava nástroje BitLocker na počítači, spusťte [bdehdcfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment#using-bitlocker-to-encrypt-volumescommand) v případě potřeby:

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>Ochrana svazku operačního systému pomocí nástroje BitLocker
Použití [ `manage-bde` ](https://technet.microsoft.com/library/ff829849.aspx) příkaz, který povoluje šifrování na spouštěcím svazku pomocí externí ochranné zařízení klíče. Externí klíče (soubor .bek) můžete také umístíte na externím disku nebo svazku. Po dalším restartování je povolené šifrování v systému a spouštěcí svazek.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Příprava virtuálního počítače pomocí samostatných/prostředku datového virtuálního pevného disku pro získání externího klíče pomocí nástroje BitLocker.

## <a name="bkmk_LinuxRunning"></a> Šifrování disku s operačním systémem na spuštěný virtuální počítač s Linuxem

### <a name="prerequisites-for-os-disk-encryption"></a>Požadavky na šifrování disku operačního systému

* Virtuální počítač musí používat kompatibilní se šifrováním disku operačního systému distribučního jak je uvedeno v [Azure Disk Encryption – nejčastější dotazy](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport) 
* Virtuální počítač musí být vytvořené z Marketplace image v Azure Resource Manageru.
* Virtuální počítač Azure s minimálně 4 GB paměti RAM (doporučená velikost je 7 GB).
* (Pro RHEL a CentOS) Zakážete SELinux. Pokud chcete zakázat SELinux, naleznete v tématu "4.4.2. Zakázání SELinux"v [Průvodce SELinux uživatele a správce](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na virtuálním počítači.
* Když zakážete SELinux, restartujte virtuální počítač alespoň jednou.

### <a name="steps"></a>Kroky
1. Vytvoření virtuálního počítače pomocí jedné z distribuce zadali dřív.

 Pro 7.2 CentOS je podporováno šifrování disku operačního systému přes speciální image. Pokud chcete použít tuto bitovou kopii, zadejte "7.2n" jako SKU při vytváření virtuálního počítače:

 ```powershell
    Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
 ```
2. Konfigurace virtuálního počítače podle vašich potřeb. Pokud budete k šifrování všech (operační systém + data) jednotkami, musí být zadané a možnost připojit z /etc/fstab datové jednotky.

 > [!NOTE]
 > Použijte UUID =... k určení datové jednotky v/etc/fstab místo zadávání názvu zařízení blok (například/dev/sdb1). Během šifrování se změní pořadí jednotky na virtuálním počítači. Pokud váš virtuální počítač závisí na konkrétní pořadí blokovat zařízení, dojde k selhání připojení po šifrování.

3. Odhlaste se z relace SSH.

4. K šifrování operačního systému, určete volumeType jako **všechny** nebo **OS** když povolíte šifrování.

 > [!NOTE]
 > Všechny procesy uživatelskou, které nejsou spuštěné jako `systemd` služby by měl ukončen s `SIGKILL`. Restartujte virtuální počítač. Když povolíte šifrování disku operačního systému spuštěného virtuálního počítače, naplánujte výpadky virtuálního počítače.

5. Pravidelně sledovat průběh šifrování pomocí pokynů [další části](#monitoring-os-encryption-progress).

6. Po Get-AzureRmVmDiskEncryptionStatus ukazuje "VMRestartPending", restartujte virtuální počítač po přihlášení k němu nebo pomocí portálu, Powershellu nebo rozhraní příkazového řádku.
    ```powershell
    C:\> Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
Předtím, než je restartovat, doporučujeme uložit [Diagnostika spouštění](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) virtuálního počítače.

## <a name="monitoring-os-encryption-progress"></a>Sledování průběhu šifrování operačního systému
Můžete sledovat průběh šifrování operačního systému třemi způsoby:

* Použití `Get-AzureRmVmDiskEncryptionStatus` rutiny a zkontrolujte pole zpráva o průběhu:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
 Po virtuálním počítači dosáhne "Zahájeno šifrování disku operačního systému", trvá přibližně 40 až 50 minut na Premium storage zálohy virtuálního počítače.

 Z důvodu [vydat #388](https://github.com/Azure/WALinuxAgent/issues/388) v WALinuxAgent, `OsVolumeEncrypted` a `DataVolumesEncrypted` jako `Unknown` v některých distribucích. S WALinuxAgent verze 2.1.5 a později je tento problém automaticky opraven. Pokud se zobrazí `Unknown` ve výstupu, můžete ověřit stav šifrování disku pomocí Průzkumníka prostředků Azure.

 Přejděte na [Azure Resource Exploreru](https://resources.azure.com/)a potom rozbalte tuto hierarchii na levém panelu výběr:

 ~~~~
 |-- subscriptions
     |-- [Your subscription]
          |-- resourceGroups
               |-- [Your resource group]
                    |-- providers
                         |-- Microsoft.Compute
                              |-- virtualMachines
                                   |-- [Your virtual machine]
                                        |-- InstanceView
~~~~                

 V InstanceView přejděte dolů a zobrazit stav šifrování jednotky.

 ![Zobrazení Instance virtuálního počítače](./media/azure-security-disk-encryption/vm-instanceview.png)

* Podívejte se na [Diagnostika spouštění](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Zprávy z rozšíření ADE musí předcházet `[AzureDiskEncryption]`.

* Přihlaste se k virtuálnímu počítači pomocí SSH a získejte rozšíření protokolu:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

 Doporučujeme vám, že není přihlásíte do virtuálního počítače probíhá šifrování operačního systému. Kopírovat protokoly jenom v případě, že tyto dvě metody se nezdařilo.

## <a name="bkmk_preLinux"></a> Připravit předem šifrované linuxového virtuálního pevného disku
Příprava předem šifrované virtuální pevné disky se může lišit v závislosti na distribuci. Příklady o přípravě [Ubuntu 16](#bkmk_Ubuntu), [openSUSE 13.2](#bkmk_openSUSE), a [CentOS 7](#bkmk_CentOS) jsou k dispozici. 

### <a name="bkmk_Ubuntu"></a> Ubuntu 16
Konfigurace šifrování během instalace distribučního provedením následujících kroků:

1. Vyberte **konfigurace šifrovaných svazcích** při rozdělit disky.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Vytvoření samostatné spouštěcí jednotka, která nesmí být zašifrovaná. Šifrování kořenové jednotce.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Zadejte heslo. Toto je heslo, které jste nahráli do služby key vault.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Dokončení vytváření oddílů.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Při spuštění virtuálního počítače a vyzváni k zadání přístupového hesla použijte heslo, které jste zadali v kroku 3.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Příprava virtuálního počítače pro jeho odeslání do Azure s využitím [tyto pokyny](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Při spuštění poslední krok (zrušení zřízení virtuálního počítače) ještě.

Konfigurace šifrování pro práci s Azure provedením následujících kroků:

1. Vytvoření souboru v rámci /usr/local/sbin/azure_crypt_key.sh, s obsahem v následujícím skriptu. Věnujte pozornost KeyFileName, protože je název souboru přístupové heslo používané Azure.

    ```
    #!/bin/sh
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint
    modprobe vfat >/dev/null 2>&1
    modprobe ntfs >/dev/null 2>&1
    sleep 2
    OPENED=0
    cd /sys/block
    for DEV in sd*; do

        echo "> Trying device: $DEV ..." >&2
        mount -t vfat -r /dev/${DEV}1 $MountPoint >/dev/null||
        mount -t ntfs -r /dev/${DEV}1 $MountPoint >/dev/null
        if [ -f $MountPoint/$KeyFileName ]; then
                cat $MountPoint/$KeyFileName
                umount $MountPoint 2>/dev/null
                OPENED=1
                break
        fi
        umount $MountPoint 2>/dev/null
    done

      if [ $OPENED -eq 0 ]; then
        echo "FAILED to find suitable passphrase file ..." >&2
        echo -n "Try to enter your password: " >&2
        read -s -r A </dev/console
        echo -n "$A"
     else
        echo "Success loading keyfile!" >&2
    fi
```

2. Změna konfigurace kryptografie v */etc/crypttab*. Mělo by to vypadat takto:
 ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Pokud upravujete *azure_crypt_key.sh* ve Windows a zkopírován do Linuxu spusťte `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Přidáte spustitelný soubor oprávnění ke skriptu:
 ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
 ```
5. Upravit */etc/initramfs-tools/modules* přidáním řádků:
 ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
```
6. Spustit `update-initramfs -u -k all` aktualizovat initramfs provést `keyscript` projeví.

7. Nyní můžete zrušit zřízení virtuálního počítače.

 ![Instalační program Ubuntu 16.04](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Pokračovat k dalšímu kroku a nahrajte virtuální pevný disk do Azure.

### <a name="bkmk_openSUSE"></a>  openSUSE 13.2
Pokud chcete nakonfigurovat šifrování během instalace distribučního, proveďte následující kroky:
1. Když je rozdělit disky, vyberte **šifrování svazku skupiny**a pak zadejte heslo. Jedná se o heslo, který nahrajete do trezoru klíčů.

 ![Instalace v openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Spuštění virtuálního počítače pomocí hesla.

 ![Instalace v openSUSE 13.2](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Příprava virtuálního počítače pro jeho odeslání do Azure podle pokynů v [Příprava virtuálního počítače se SLES nebo openSUSE pro Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Při spuštění poslední krok (zrušení zřízení virtuálního počítače) ještě.

Ke konfiguraci šifrování pro práci s Azure, proveďte následující kroky:
1. Upravit /etc/dracut.conf a přidejte následující řádek:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Okomentujte řádky na konci souboru /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
 ```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
 ```

3. Připojte následující řádek na začátek souboru /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
 ```
    DRACUT_SYSTEMD=0
 ```
A změňte všechny výskyty položky:
 ```
    if [ -z "$DRACUT_SYSTEMD" ]; then
 ```
na:
```
    if [ 1 ]; then
```
4. Upravit /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh a přidejte je do "# Otevřít LUKS zařízení":

    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```
5. Spustit `/usr/sbin/dracut -f -v` aktualizovat initrd.

6. Nyní můžete zrušení zřízení virtuálního počítače a nahrajte virtuální pevný disk do Azure.

### <a name="bkmk_CentOS"></a> CentOS 7
Pokud chcete nakonfigurovat šifrování během instalace distribučního, proveďte následující kroky:
1. Vyberte **šifrovat data** při rozdělit disky.

 ![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Ujistěte se, že **šifrovat** je vybrán pro kořenový oddíl.

 ![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Zadejte heslo. Toto je heslo, které nahrajete do trezoru klíčů.

 ![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Při spuštění virtuálního počítače a vyzváni k zadání přístupového hesla použijte heslo, které jste zadali v kroku 3.

 ![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Příprava virtuálního počítače pro jeho odeslání do Azure s využitím pokynů "CentOS 7.0 +" [Příprava virtuálního počítače založeného na CentOS pro Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Při spuštění poslední krok (zrušení zřízení virtuálního počítače) ještě.

6. Nyní můžete zrušení zřízení virtuálního počítače a nahrajte virtuální pevný disk do Azure.

Ke konfiguraci šifrování pro práci s Azure, proveďte následující kroky:

1. Upravit /etc/dracut.conf a přidejte následující řádek:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Okomentujte řádky na konci souboru /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
```
    #        inst_multiple -o \
    #        $systemdutildir/system-generators/systemd-cryptsetup-generator \
    #        $systemdutildir/systemd-cryptsetup \
    #        $systemdsystemunitdir/systemd-ask-password-console.path \
    #        $systemdsystemunitdir/systemd-ask-password-console.service \
    #        $systemdsystemunitdir/cryptsetup.target \
    #        $systemdsystemunitdir/sysinit.target.wants/cryptsetup.target \
    #        systemd-ask-password systemd-tty-ask-password-agent
    #        inst_script "$moddir"/crypt-run-generator.sh /sbin/crypt-run-generator
```

3. Připojte následující řádek na začátek souboru /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
```
    DRACUT_SYSTEMD=0
```
A změňte všechny výskyty položky:
```
    if [ -z "$DRACUT_SYSTEMD" ]; then
```
na
```
    if [ 1 ]; then
```
4. Upravit /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh a přidat následující za "otevřené LUKS zařízení #":
    ```
    MountPoint=/tmp-keydisk-mount
    KeyFileName=LinuxPassPhraseFileName
    echo "Trying to get the key from disks ..." >&2
    mkdir -p $MountPoint >&2
    modprobe vfat >/dev/null >&2
    modprobe ntfs >/dev/null >&2
    for SFS in /dev/sd*; do
    echo "> Trying device:$SFS..." >&2
    mount ${SFS}1 $MountPoint -t vfat -r >&2 ||
    mount ${SFS}1 $MountPoint -t ntfs -r >&2
    if [ -f $MountPoint/$KeyFileName ]; then
        echo "> keyfile got..." >&2
        cp $MountPoint/$KeyFileName /tmp-keyfile >&2
        luksfile=/tmp-keyfile
        umount $MountPoint >&2
        break
    fi
    done
    ```    
5. Spustit "/ usr/sbin/dracut - f - v" aktualizovat initrd.

![Instalační program centOS 7](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a> Nahrání šifrovaného virtuálního pevného disku do účtu služby Azure storage
Po povolení šifrování nástrojem BitLocker nebo šifrování DM-Crypt místní šifrované virtuální pevný disk je potřeba nahrát do účtu úložiště.
```powershell
    Add-AzureRmVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a> Odešlete tajný kód k předem šifrovaných virtuálních počítačů do trezoru klíčů
Při šifrování pomocí aplikace Azure AD (předchozí verzi), musí být tajný klíč šifrování disku, který jste získali dříve odeslán jako tajný klíč v trezoru klíčů. Key vault musí mít šifrování disku a povolenými oprávněními pro vašeho klienta Azure AD.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a> Tajný kód disk encryption není šifrován KEK
Chcete-li nastavit tajný klíč v trezoru klíčů, použijte [Set-AzureKeyVaultSecret](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret). Pokud máte virtuální počítače s Windows, je zakódován jako řetězec ve formátu base64 a pak nahrají do vašeho trezoru klíčů pomocí souboru klíče bek `Set-AzureKeyVaultSecret` rutiny. Heslo pro Linux, jsou zakódovány jako řetězec ve formátu base64 a pak nahrají do služby key vault. Kromě toho Ujistěte se, že následující značky jsou nastaveny při vytvoření tajného klíče v trezoru klíčů.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


Použití `$secretUrl` v dalším kroku pro [připojení disku s operačním systémem bez použití KEK](#bkmk_URLnoKEK).

### <a name="bkmk_SecretKEK"></a> Tajný kód disk encryption šifrován KEK
Před odesláním do služby key vault tajný klíč, které můžete volitelně šifrovat pomocí šifrovací klíč klíče. Použít obtékání [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) nejprve šifrování tajného klíče pomocí klíče šifrovacího klíče. Výstupem této operace zalamování řádků je řetězec kódování URL ve formátu base64, který pak můžete nahrát jako tajný kód pomocí [ `Set-AzureKeyVaultSecret` ](/powershell/module/azurerm.keyvault/set-azurekeyvaultsecret) rutiny.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzureKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

Použití `$KeyEncryptionKey` a `$secretUrl` v dalším kroku pro [připojení disku s operačním systémem pomocí KEK](#BKMK_URLKEK).

##  <a name="bkmk_SecretURL"></a> Po připojení disku s operačním systémem zadejte adresa URL tajného kódu

###  <a name="bkmk_URLnoKEK"></a>Bez použití KEK
Když jste připojení disku s operačním systémem, je nutné předat `$secretUrl`. Adresa URL vytvořená v části "šifrování disku tajný klíč není šifrován KEK".
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="bkmk_URLKEK"></a>Použití KEK
Po připojení disku s operačním systémem, předejte `$KeyEncryptionKey` a `$secretUrl`. Adresa URL vytvořená v části "Tajný kód Disk encryption šifrován KEK".
```powershell
    Set-AzureRmVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Linux `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
