---
title: Příloha – Azure Disk Encryption pro virtuální počítače s IaaS | Microsoft Docs
description: Tento článek je dodatek pro Microsoft Azure šifrování disku pro virtuální počítače s Windows a Linux IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 4c065e1970a01f7e3737f8bd99672c84f2019bfe
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71822325"
---
# <a name="appendix-for-azure-disk-encryption"></a>Příloha pro Azure Disk Encryption 

Tento článek je dodatkem k [Azure Disk Encryption pro virtuální počítače s IaaS](azure-security-disk-encryption-overview.md). Nezapomeňte si přečíst článek Azure Disk Encryption pro virtuální počítače s IaaS nejdříve, abyste pochopili kontext. Tento článek popisuje, jak připravit předem zašifrované virtuální pevné disky a další úlohy.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="connect-to-your-subscription"></a>Připojení k vašemu předplatnému
Než začnete, přečtěte si článek o [požadovaných součástech](azure-security-disk-encryption-prerequisites.md) . Po splnění všech požadavků se připojte ke svému předplatnému spuštěním následujících rutin:

### <a name="bkmk_ConnectPSH"></a>Připojení k předplatnému pomocí PowerShellu

1. Spusťte relaci Azure PowerShell a přihlaste se ke svému účtu Azure pomocí následujícího příkazu:

     ```powershell
     Connect-AzAccount 
     ```
2. Pokud máte více předplatných a chcete určit, jestli se má použít, zadejte následující příkaz pro zobrazení předplatných pro váš účet:
     
     ```powershell
     Get-AzSubscription
     ```
3. Chcete-li určit předplatné, které chcete použít, zadejte:
 
     ```powershell
      Select-AzSubscription -SubscriptionName <Yoursubscriptionname>
     ```
4. Pokud chcete ověřit, jestli je předplatné nakonfigurované správně, zadejte:
     
     ```powershell
     Get-AzSubscription
     ```
5. V případě potřeby se připojte k Azure AD pomocí [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```
6. Pokud chcete potvrdit, že se nainstalují rutiny Azure Disk Encryption, zadejte:
     
     ```powershell
     Get-command *diskencryption*
     ```
                       
7. V případě potřeby se podívejte na [téma Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps) a [AzureAD](/powershell/module/azuread).

### <a name="bkmk_ConnectCLI"></a>Připojení k předplatnému pomocí Azure CLI

1. Přihlaste se k Azure pomocí [AZ Login](/cli/azure/authenticate-azure-cli#sign-in-interactively). 
     
     ```azurecli
     az login
     ```

2. Pokud chcete vybrat tenanta pro přihlášení, použijte:
    
     ```azurecli
     az login --tenant <tenant>
     ```

3. Pokud máte více předplatných a chcete zadat konkrétní konkrétní, získejte seznam předplatných pomocí [AZ Account list](/cli/azure/account#az-account-list) a určete pomocí [AZ Account set](/cli/azure/account#az-account-set).
     
     ```azurecli
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Ověřte nainstalovanou verzi.
     
     ```azurecli
        az --version
     ``` 

5. V případě potřeby si přečtěte téma [Začínáme s Azure CLI 2,0](/cli/azure/get-started-with-azure-cli) . 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Ukázkové skripty PowerShellu pro Azure Disk Encryption 

- **Zobrazit seznam všech šifrovaných virtuálních počítačů ve vašem předplatném**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Vypíše všechny tajné klíče pro šifrování disků používané k šifrování virtuálních počítačů v trezoru klíčů.** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="bkmk_prereq-script"></a>Použití skriptu PowerShellu pro Azure Disk Encryption předpoklady
Pokud jste již obeznámeni s požadavky pro Azure Disk Encryption, můžete použít [skript PowerShellu pro Azure Disk Encryption požadavků](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Příklad použití tohoto skriptu PowerShellu najdete v tématu [rychlý Start k šifrování virtuálního počítače](../virtual-machines/linux/disk-encryption-powershell-quickstart.md). Komentáře můžete z části skriptu odebrat, od řádku 211 až po zašifrování všech disků pro existující virtuální počítače v existující skupině prostředků. 

Následující tabulka ukazuje, které parametry lze použít ve skriptu prostředí PowerShell: 


|Parametr|Popis|Je povinné|
|------|------|------|
|$resourceGroupName| Název skupiny prostředků, do které patří Trezor klíčů.  Pokud neexistuje, vytvoří se nová skupina prostředků s tímto názvem.| True|
|$keyVaultName|Název trezoru klíčů, do kterého se mají umístit šifrovací klíče Pokud jeden z nich neexistuje, vytvoří se nový trezor s tímto názvem.| True|
|$location|Umístění trezoru klíčů. Zajistěte, aby byl Trezor klíčů a virtuální počítače zašifrované ve stejném umístění. Seznam umístění získáte pomocí rutiny `Get-AzLocation`.|True|
|$subscriptionId|Identifikátor předplatného Azure, který se má použít  Své ID předplatného můžete získat pomocí rutiny `Get-AzSubscription`.|True|
|$aadAppName|Název aplikace služby Azure AD, která bude použita k zápisu tajných kódů do trezoru klíčů. Pokud aplikace se zadaným názvem neexistuje, vytvoří se nová. Pokud tato aplikace již existuje, předejte do skriptu parametr aadClientSecret.|False|
|$aadClientSecret|Tajný kód klienta aplikace Azure AD, který byl vytvořen dříve.|False|
|$keyEncryptionKeyName|Název volitelného šifrovacího klíče klíče v trezoru klíčů. Pokud neexistuje, vytvoří se nový klíč s tímto názvem.|False|


## <a name="resource-manager-templates"></a>Šablony Správce prostředků

<!--   - [Create a key vault](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) -->

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Šifrování nebo dešifrování virtuálních počítačů bez aplikace Azure AD


- [Povolení šifrování disku u stávajících nebo spuštěných virtuálních počítačů s IaaS s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)
- [Zakázání šifrování disku u stávajících nebo spuštěných virtuálních počítačů s IaaS s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)
- [Povolení šifrování disku na existujícím nebo běžícím virtuálním počítači s IaaS Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
  - [Zakázat šifrování na běžícím virtuálním počítači se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Zakázání šifrování je povolené jenom u datových svazků pro virtuální počítače se systémem Linux.  

### <a name="encrypt-or-decrypt-virtual-machine-scale-sets"></a>Šifrování nebo dešifrování virtuálních počítačů Scale Sets

- [Povolit šifrování disku na běžící sadě virtuálních počítačů se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Povolení šifrování disku ve spuštěné sadě škálování virtuálních počítačů s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Nasazení sady virtuálních počítačů se systémem Linux s využitím JumpBox a povolení šifrování na platformě Linux VMSS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Nasazení škály virtuálních počítačů s Windows pomocí JumpBox a povolení šifrování ve Windows VMSS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Zakázat šifrování disku na běžící sadě virtuálních počítačů se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Zakázat šifrování disku ve spuštěné sadě škálování virtuálních počítačů s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Šifrování nebo dešifrování virtuálních počítačů pomocí aplikace Azure AD (předchozí verze) 
 
- [Povolení šifrování disku u stávajících nebo spuštěných virtuálních počítačů s IaaS s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)

- [Povolení šifrování disku na existujícím nebo běžícím virtuálním počítači s IaaS Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    

- [Zakázat šifrování disku při spuštění Windows IaaS](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm) 

-  [Zakázat šifrování na běžícím virtuálním počítači se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Zakázání šifrování je povolené jenom u datových svazků pro virtuální počítače se systémem Linux. 

- [Povolení šifrování disku na novém virtuálním počítači s IaaS s Windows z Marketplace](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)
    - Tato šablona vytvoří nový zašifrovaný virtuální počítač s Windows, který používá Image Galerie Windows Serveru 2012.

- [Vytvoření nového šifrovaného virtuálního počítače s Windows IaaS spravovaného disku z Galerie](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image-managed-disks)
    - Tato šablona vytvoří nový zašifrovaný virtuální počítač s Windows se spravovanými disky pomocí Image Galerie Windows Serveru 2012.

- [Vytvoření nového šifrovaného spravovaného disku z předem šifrovaného objektu VHD nebo úložiště objektů BLOB](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Vytvoří nový zašifrovaný spravovaný disk, který poskytl předem zašifrovaný virtuální pevný disk a jeho odpovídající nastavení šifrování.

- [Povolení šifrování disku na běžícím virtuálním počítači s Windows pomocí kryptografického otisku certifikátu klienta Azure AD](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-aad-client-cert)
    


## <a name="bkmk_preWin"></a>Příprava předem zašifrovaného virtuálního pevného disku s Windows
Níže uvedené části jsou nezbytné k přípravě předem zašifrovaného virtuálního pevného disku s Windows pro nasazení jako šifrovaného virtuálního pevného disku v Azure IaaS. Tyto informace slouží k přípravě a spuštění nového virtuálního počítače s Windows (VHD) v systému Azure Site Recovery nebo Azure. Další informace o tom, jak připravit a nahrát VHD, najdete v tématu [nahrání zobecněného virtuálního pevného disku a jeho použití k vytvoření nových virtuálních počítačů v Azure](../virtual-machines/windows/upload-generalized-managed.md).

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>Aktualizace zásad skupiny pro povolení ochrany operačního systému bez čipu TPM
Nakonfigurujte nastavení Zásady skupiny BitLockeru **Nástroj BitLocker Drive Encryption**, které najdete v části **zásady místního počítače** > **konfigurace počítače** > **šablony pro správu** > **Windows. Součásti**. Toto nastavení změňte na **jednotky operačního systému** > **vyžadovat při spuštění další ověřování** > **Povolte BitLocker bez kompatibilního čipu TPM**, jak je znázorněno na následujícím obrázku:

![Microsoft Antimalware v Azure](./media/azure-security-disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>Nainstalovat součásti funkcí nástroje BitLocker
U systému Windows Server 2012 a novějších verzí použijte následující příkaz:

    dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart

Pro Windows Server 2008 R2 použijte následující příkaz:

    ServerManagerCmd -install BitLockers
### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>Příprava svazku operačního systému pro nástroj BitLocker pomocí `bdehdcfg`
Pokud chcete zkomprimovat oddíl s operačním systémem a připravit počítač na BitLocker, spusťte v případě potřeby [BdeHdCfg](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-basic-deployment) :

    bdehdcfg -target c: shrink -quiet 


### <a name="protect-the-os-volume-by-using-bitlocker"></a>Ochrana svazku operačního systému pomocí nástroje BitLocker
Pomocí příkazu [`manage-bde`](https://technet.microsoft.com/library/ff829849.aspx) Povolte šifrování na spouštěcím svazku pomocí ochrany externím klíčem. Také externí klíč (soubor. klíče bek) umístěte na externí disk nebo svazek. Po příštím restartování se na systém/spouštěcí svazek povolí šifrování.

    manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
    reboot

> [!NOTE]
> Připravte virtuální počítač se samostatným virtuálním pevným diskem dat nebo prostředků, abyste mohli získat externí klíč pomocí BitLockeru.

## <a name="bkmk_LinuxRunning"></a>Šifrování jednotky operačního systému na běžícím virtuálním počítači se systémem Linux

### <a name="prerequisites-for-os-disk-encryption"></a>Předpoklady pro šifrování disků s operačním systémem

* Virtuální počítač musí používat distribuci kompatibilní s nástrojem pro šifrování disků s operačním systémem, jak je uvedeno v [Azure Disk Encryption podporované operační systémy: Linux](azure-security-disk-encryption-prerequisites.md#linux) 
* Virtuální počítač musí být vytvořený z image Marketplace v Azure Resource Manager.
* Virtuální počítač Azure s minimálně 4 GB paměti RAM (doporučená velikost je 7 GB).
* (Pro RHEL a CentOS) Zakáže SELinux. Pokud chcete zakázat SELinux, přečtěte si téma "4.4.2". Zakázání SELinux "v [Průvodci uživatele SELinux](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) na virtuálním počítači.
* Po zakázání SELinux restartujte virtuální počítač aspoň jednou.

### <a name="steps"></a>Kroky
1. Vytvořte virtuální počítač pomocí jedné z výše uvedených distribucí.

   Pro CentOS 7,2 se šifrování disku s operačním systémem podporuje přes speciální image. Pokud chcete použít tento obrázek, jako SKU při vytváření virtuálního počítače zadejte "7.2 n":

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Nakonfigurujte virtuální počítač podle svých potřeb. Pokud chcete zašifrovat všechny jednotky (OS + data), musíte zadat a připojit datové jednotky z adresáři/etc/fstab.

   > [!NOTE]
   > Použít UUID =... Chcete-li zadat datové jednotky v/etc/fstab namísto zadání názvu blokového zařízení (například adresář/dev/sdb1). Během šifrování se pořadí jednotek změní na virtuálním počítači. Pokud váš virtuální počítač spoléhá na konkrétní pořadí blokovaných zařízení, připojení se po jeho šifrování nepodaří připojit.

3. Odhlaste se z relací SSH.

4. Chcete-li zašifrovat operační systém, zadejte volumeType jako **všechny** nebo **operační systém** , pokud povolíte šifrování.

   > [!NOTE]
   > Všechny procesy uživatelského prostoru, které nejsou spuštěny jako služby `systemd`, by měly být ukončeny pomocí `SIGKILL`. Restartujte virtuální počítač. Když na běžícím virtuálním počítači povolíte šifrování disků s operačním systémem, naplánujte výpadky virtuálního počítače.

5. Pravidelně Sledujte průběh šifrování pomocí pokynů v [následující části](#monitoring-os-encryption-progress).

6. Po Get-AzVmDiskEncryptionStatus se zobrazí "VMRestartPending", restartujte virtuální počítač, a to tak, že se přihlásíte k tomuto počítači nebo pomocí portálu, PowerShellu nebo rozhraní příkazového řádku.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Před restartováním doporučujeme uložit [diagnostiku spouštění](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) virtuálního počítače.

## <a name="monitoring-os-encryption-progress"></a>Sledování průběhu šifrování operačního systému
Průběh šifrování operačního systému můžete monitorovat třemi způsoby:

* Použijte rutinu `Get-AzVmDiskEncryptionStatus` a zkontrolujte pole ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Jakmile virtuální počítač dosáhne "spuštěného" šifrování disku s operačním systémem, bude trvat přibližně 40 až 50 minut na virtuálním počítači s podporou Premium Storage.

  Kvůli [potížím #388](https://github.com/Azure/WALinuxAgent/issues/388) v WALinuxAgent se v některých distribucích `OsVolumeEncrypted` a `DataVolumesEncrypted` ukázaly jako `Unknown`. U WALinuxAgent verze 2.1.5 a novější je tento problém vyřešen automaticky. Pokud se ve výstupu zobrazí `Unknown`, můžete ověřit stav šifrování disku pomocí Azure Resource Explorer.

  Přejít na [Azure Resource Explorer](https://resources.azure.com/)a potom rozbalte tuto hierarchii na panelu výběru vlevo:

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

  V InstanceView se posuňte dolů a zobrazte stav šifrování vašich jednotek.

  ![Zobrazení instance virtuálního počítače](./media/azure-security-disk-encryption/vm-instanceview.png)

* Podívejte se na [diagnostiku spouštění](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Zprávy z rozšíření ADE by měly obsahovat předponu `[AzureDiskEncryption]`.

* Přihlaste se k virtuálnímu počítači přes SSH a získejte protokol rozšíření z:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Doporučujeme, abyste se k virtuálnímu počítači přihlásili, zatímco probíhá šifrování operačního systému. Protokoly zkopírujte pouze v případě, že se nezdařily jiné dvě metody.

## <a name="bkmk_preLinux"></a>Příprava předem zašifrovaného virtuálního pevného disku se systémem Linux
Příprava pro předem zašifrované virtuální pevné disky se může lišit v závislosti na distribuci. Příklady pro přípravu [Ubuntu 16](#bkmk_Ubuntu), [openSUSE 13,2](#bkmk_openSUSE)a [CentOS 7](#bkmk_CentOS) jsou k dispozici. 

### <a name="bkmk_Ubuntu"></a>Ubuntu 16
Pomocí následujících kroků nakonfigurujte šifrování při instalaci distribuce:

1. Při vytváření oddílů disků vyberte **Konfigurovat šifrované svazky** .

   ![Instalace Ubuntu 16,04 – konfigurace šifrovaných svazků](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Vytvořte samostatnou spouštěcí jednotku, která nesmí být zašifrovaná. Zašifrujte kořenovou jednotku.

   ![Nastavení Ubuntu 16,04 – vyberte zařízení, která chcete zašifrovat.](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Zadejte heslo. Toto je přístupové heslo, které jste nahráli do trezoru klíčů.

   ![Nastavení Ubuntu 16,04 – poskytnutí hesla](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Dokončete dělení.

   ![Nastavení Ubuntu 16,04 – dokončení dělení](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Když spustíte virtuální počítač a budete požádáni o zadání hesla, použijte heslo, které jste zadali v kroku 3.

   ![Nastavení Ubuntu 16,04 – poskytnutí hesla při spuštění](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Pomocí [těchto pokynů](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/)Připravte virtuální počítač pro nahrání do Azure. Nespouštějte poslední krok (zrušení zřízení virtuálního počítače).

Nakonfigurujte šifrování pro práci s Azure pomocí následujících kroků:

1. V části/usr/local/sbin/azure_crypt_key.sh vytvořte soubor s obsahem v následujícím skriptu. Věnujte pozornost na název souboru, protože se jedná o název souboru hesla, který používá Azure.

    ```bash
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

2. Změňte konfiguraci nešifrovaných oznámení v */etc/crypttab*. Mělo by to vypadat takto:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

3. Pokud upravujete *azure_crypt_key. sh* ve Windows a zkopírovali jste ji do systému Linux, spusťte `dos2unix /usr/local/sbin/azure_crypt_key.sh`.

4. Přidejte do skriptu oprávnění ke spustitelnému souboru:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Upravte */etc/initramfs-tools/modules* přidáním řádků:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Spuštěním `update-initramfs -u -k all` aktualizujte initramfs tak, aby se projevila `keyscript`.

7. Nyní můžete zrušit zřízení virtuálního počítače.

   ![Instalace Ubuntu 16,04 – aktualizace – initramfs](./media/azure-security-disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Pokračujte dalším krokem a nahrajte virtuální pevný disk do Azure.

### <a name="bkmk_openSUSE"></a>openSUSE 13,2
Chcete-li konfigurovat šifrování při instalaci distribuce, proveďte následující kroky:
1. Při vytváření oddílů disku vyberte možnost **Šifrovat skupinu svazků**a pak zadejte heslo. Toto je heslo, které nahrajete do svého trezoru klíčů.

   ![Nastavení openSUSE 13,2-šifrovat skupinu svazků](./media/azure-security-disk-encryption/opensuse-encrypt-fig1.png)

2. Pomocí hesla spusťte virtuální počítač.

   ![Nastavení openSUSE 13,2 – poskytnutí hesla při spuštění](./media/azure-security-disk-encryption/opensuse-encrypt-fig2.png)

3. Připravte virtuální počítač na nahrávání do Azure podle pokynů v tématu [Příprava virtuálního počítače s SLES nebo openSUSE pro Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Nespouštějte poslední krok (zrušení zřízení virtuálního počítače).

Pokud chcete nakonfigurovat šifrování pro práci s Azure, proveďte následující kroky:
1. Upravte/etc/Dracut.conf a přidejte následující řádek:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Odkomentujte tyto řádky na konci souboru/usr/lib/Dracut/Modules.d/90crypt/Module-Setup.sh:
   ```bash
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

3. Na začátek souboru/usr/lib/Dracut/Modules.d/90crypt/Parse-crypt.sh přidejte následující řádek:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   A změňte všechny výskyty:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na:
   ```bash
    if [ 1 ]; then
   ```
4. Upravit/usr/lib/Dracut/Modules.d/90crypt/cryptroot-Ask.sh a připojit ho k "# Open LUKS zařízení":

    ```bash
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
5. Image initrd můžete aktualizovat spuštěním `/usr/sbin/dracut -f -v`.

6. Teď můžete zrušit zřízení virtuálního počítače a nahrát virtuální pevný disk do Azure.

### <a name="bkmk_CentOS"></a>CentOS 7
Chcete-li konfigurovat šifrování při instalaci distribuce, proveďte následující kroky:
1. Pokud chcete rozdělit disky na oddíly, vyberte možnost **Šifrovat moje data** .

   ![Instalace CentOS 7 – cíl instalace](./media/azure-security-disk-encryption/centos-encrypt-fig1.png)

2. Ujistěte se, že je pro kořenový oddíl vybráno **šifrování** .

   ![Instalace CentOS 7 – vyberte možnost šifrovat pro kořenový oddíl](./media/azure-security-disk-encryption/centos-encrypt-fig2.png)

3. Zadejte heslo. Toto je přístupové heslo, které nahrajete do svého trezoru klíčů.

   ![Nastavení pro CentOS 7 – poskytnutí hesla](./media/azure-security-disk-encryption/centos-encrypt-fig3.png)

4. Když spustíte virtuální počítač a budete požádáni o zadání hesla, použijte heslo, které jste zadali v kroku 3.

   ![Instalace CentOS 7 – zadejte přístupové heslo na spouštění](./media/azure-security-disk-encryption/centos-encrypt-fig4.png)

5. Připravte virtuální počítač pro nahrání do Azure pomocí instrukcí "CentOS 7.0 +" v tématu [Příprava virtuálního počítače založeného na CentOS pro Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Nespouštějte poslední krok (zrušení zřízení virtuálního počítače).

6. Teď můžete zrušit zřízení virtuálního počítače a nahrát virtuální pevný disk do Azure.

Pokud chcete nakonfigurovat šifrování pro práci s Azure, proveďte následující kroky:

1. Upravte/etc/Dracut.conf a přidejte následující řádek:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Odkomentujte tyto řádky na konci souboru/usr/lib/Dracut/Modules.d/90crypt/Module-Setup.sh:
   ```bash
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

3. Na začátek souboru/usr/lib/Dracut/Modules.d/90crypt/Parse-crypt.sh přidejte následující řádek:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   A změňte všechny výskyty:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na
   ```bash
    if [ 1 ]; then
   ```
4. Po "# Open LUKS zařízení" upravte/usr/lib/Dracut/Modules.d/90crypt/cryptroot-Ask.sh a přidejte následující:
    ```bash
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
5. Spusťte rutinu "/usr/sbin/Dracut-f-v" a aktualizujte image initrd.

![Instalace CentOS 7 – Spuštění/usr/sbin/Dracut-f-v](./media/azure-security-disk-encryption/centos-encrypt-fig5.png)

## <a name="bkmk_UploadVHD"></a>Nahrání šifrovaného virtuálního pevného disku do účtu služby Azure Storage
Po povolení šifrování BitLockeru nebo šifrování DM-crypt je potřeba nahrát místní zašifrovaný virtuální pevný disk do svého účtu úložiště.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="bkmk_UploadSecret"></a>Nahrajte tajný klíč pro předem zašifrovaný virtuální počítač do trezoru klíčů.
Když šifrujete pomocí aplikace Azure AD (předchozí verze), musí se šifrovací klíč šifrování disku, který jste dříve získali, nahrát jako tajný klíč do trezoru klíčů. Trezor klíčů musí mít pro klienta Azure AD povolený šifrování disku a oprávnění.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="bkmk_SecretnoKEK"></a>Tajný klíč šifrování disku není zašifrovaný pomocí KEK.
K nastavení tajného klíče v trezoru klíčů použijte [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Pokud máte virtuální počítač s Windows, soubor klíče bek se zakóduje jako řetězec Base64 a pak se nahraje do trezoru klíčů pomocí rutiny `Set-AzKeyVaultSecret`. Pro Linux se přístupové heslo zakóduje jako řetězec Base64 a pak se nahraje do trezoru klíčů. Kromě toho se ujistěte, že jsou při vytváření tajného klíče v trezoru klíčů nastavené následující značky.

#### <a name="windows-bek-file"></a>Soubor klíče bek Windows
```powershell
# Change the VM Name, key vault name, and specify the path to the BEK file.
$VMName ="MySecureVM"
$BEKFilepath = "C:\test\BEK\12345678-90AB-CDEF-A1B2-C3D4E5F67890A.BEK"
$VeyVaultName ="MySecureVault"

# Get the name of the BEK file from the BEK file path. This will be a tag for the key vault secret.
$BEKFileName =  Split-Path $BEKFilepath -Leaf

# These tags will be added to the key vault secret so you can easily see which BEK file belongs to which VM.
$tags = @{“MachineName” = “$VMName”;"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "$BEKFileName"}

# Convert the BEK file to a Base64 string.
$FileContentEncoded = [System.convert]::ToBase64String((Get-Content -Path $BEKFilepath -Encoding Byte))

# Create a new secret in the vault from the converted BEK file. 
# The file is converted to a secure string before import into the key vault

$SecretName = [guid]::NewGuid().ToString()
$SecureSecretValue = ConvertTo-SecureString $FileContentEncoded -AsPlainText -Force
$Secret = Set-AzKeyVaultSecret -VaultName $VeyVaultName -Name $SecretName -SecretValue $SecureSecretValue -tags $tags

# Show the secret's URL and store it as a variable. This is used as -DiskEncryptionKeyUrl in Set-AzVMOSDisk when you attach your OS disk. 
$SecretUrl=$secret.Id
$SecretUrl
```

#### <a name="linux"></a>Linux
```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


V dalším kroku použijte `$secretUrl` pro [připojení disku s operačním systémem bez použití KEK](#bkmk_URLnoKEK).

### <a name="bkmk_SecretKEK"></a>Tajný kód šifrování disku zašifrovaný pomocí KEK
Než nahrajete tajný klíč do trezoru klíčů, můžete ho případně zašifrovat pomocí klíčového šifrovacího klíče. Použijte [rozhraní API](https://msdn.microsoft.com/library/azure/dn878066.aspx) pro zabalení k prvnímu šifrování tajného klíče pomocí klíčového šifrovacího klíče. Výstupem této operace zalamování je řetězec kódovaný v adrese URL Base64, který lze poté odeslat jako tajný klíč pomocí rutiny [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

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

V dalším kroku použijte `$KeyEncryptionKey` a `$secretUrl` pro [připojení disku s operačním systémem pomocí KEK](#bkmk_URLKEK).

##  <a name="bkmk_SecretURL"></a>Zadejte adresu URL tajného kódu při připojení disku s operačním systémem.

###  <a name="bkmk_URLnoKEK"></a>Bez použití KEK
Když připojujete disk s operačním systémem, musíte předat `$secretUrl`. Adresa URL byla vygenerována v části "tajný klíč šifrování disku není zašifrovaný pomocí KEK".
```powershell
    Set-AzVMOSDisk `
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
Když připojíte disk s operačním systémem, předejte `$KeyEncryptionKey` a `$secretUrl`. Adresa URL se vygenerovala v části tajný klíč šifrování disku zašifrovaný pomocí KEK.
```powershell
    Set-AzVMOSDisk `
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
