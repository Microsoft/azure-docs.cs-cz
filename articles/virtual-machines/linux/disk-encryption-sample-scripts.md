---
title: Ukázkové skripty pro službu Azure Disk Encryption
description: Tento článek je přílohou pro šifrování disku Microsoft Azure pro virtuální počítače s Linuxem.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: b54f9f3466fe5f7e2da622077f53575d6f43f72d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585964"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Ukázkové skripty pro službu Azure Disk Encryption 

Tento článek obsahuje ukázkové skripty pro přípravu předem šifrované virtuální chodů a další chod.

 

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Ukázkové skripty prostředí PowerShell pro šifrování disku Azure 

- **Seznam všech šifrovaných virtuálních počítačů ve vašem předplatném**

     ```azurepowershell-interactive
     $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
     $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
     Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
     ```

- **Seznam všech tajných klíčů šifrování disku používaných k šifrování virtuálních počítačů v trezoru klíčů** 

     ```azurepowershell-interactive
     Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
     ```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a>Použití skriptu PowerShellu s požadavky azure diskového šifrování
Pokud už jste obeznámeni s požadavky pro šifrování disku Azure, můžete použít [skript PowerShell u azure diskového šifrování](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Příklad použití tohoto skriptu prostředí PowerShell najdete v tématu [Šifrování rychlého startu virtuálního virtuálního soudu](disk-encryption-powershell-quickstart.md). Komentáře můžete odebrat z části skriptu, počínaje řádkem 211, abyste zašifrovat všechny disky pro existující virtuální počítače v existující skupině prostředků. 

V následující tabulce jsou uvedeny parametry, které lze použít ve skriptu prostředí PowerShell: 


|Parametr|Popis|Povinné?|
|------|------|------|
|$resourceGroupName| Název skupiny prostředků, do které keyvault patří.  Pokud neexistuje, bude vytvořena nová skupina prostředků s tímto názvem.| True|
|$keyVaultName|Název keyvault, ve kterém mají být umístěny šifrovací klíče. Pokud trezor s tímto názvem neexistuje, vytvoří se nový trezor s tímto názvem.| True|
|$location|Umístění KeyVault. Ujistěte se, že KeyVault a virtuální chod, které mají být šifrovány jsou ve stejném umístění. Seznam umístění získáte pomocí rutiny `Get-AzLocation`.|True|
|$subscriptionId|Identifikátor předplatného Azure, které má být použito.  Své ID předplatného můžete získat pomocí rutiny `Get-AzSubscription`.|True|
|$aadAppName|Název aplikace Azure AD, která se bude používat k zápisu tajných kódů do KeyVault. Pokud aplikace se zadaným názvem neexistuje, vytvoří se nová. Pokud tato aplikace již existuje, předajte skriptu parametr aadClientSecret.|False|
|$aadClientSecret|Tajný klíč klienta aplikace Azure AD, která byla vytvořena dříve.|False|
|$keyEncryptionKeyName|Název volitelného šifrovacího klíče v úložišti KeyVault. Nový klíč s tímto názvem bude vytvořen, pokud neexistuje.|False|


### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Šifrování nebo dešifrování virtuálních počítačů bez aplikace Azure AD

- [Povolení šifrování disku na existujícím nebo spuštěném virtuálním počítači s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)  
- [Zakázání šifrování na spuštěném virtuálním počítači s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) 
    - Zakázání šifrování je povoleno jenom na datových svazcích pro virtuální počítače s Linuxem.  

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Šifrování nebo dešifrování virtuálních počítačů pomocí aplikace Azure AD (předchozí verze) 
 
- [Povolení šifrování disku na existujícím nebo spuštěném virtuálním počítači s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)    


-  [Zakázání šifrování na spuštěném virtuálním počítači s Linuxem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm) 
    - Zakázání šifrování je povoleno jenom na datových svazcích pro virtuální počítače s Linuxem. 


- [Vytvoření nového šifrovaného spravovaného disku z předšifrovaného objektu blob virtuálního pevného disku nebo úložiště](https://github.com/Azure/azure-quickstart-templates/tree/master/201-create-encrypted-managed-disk)
    - Vytvoří nový šifrovaný spravovaný disk za předpokladu, že předem zašifrovaný virtuální disk a odpovídající nastavení šifrování





## <a name="encrypting-an-os-drive-on-a-running-linux-vm"></a>Šifrování jednotky operačního systému na spuštěném virtuálním počítači s Linuxem

### <a name="prerequisites-for-os-disk-encryption"></a>Požadavky pro šifrování disku operačního systému

* Virtuální počítač musí používat distribuci kompatibilní s šifrováním disku operačního systému, jak je uvedeno v [podporovaných operačních systémech Azure Disk Encryption](disk-encryption-overview.md#supported-vms) 
* Virtuální počítač musí být vytvořený z image Marketplace ve Správci prostředků Azure.
* Virtuální počítač Azure s alespoň 4 GB paměti RAM (doporučená velikost je 7 GB).
* (Pro RHEL a CentOS) Zakažte SELinux. Chcete-li zakázat SELinux, viz "4.4.2. Zakázání SELinuxu" v [uživatelské příručce a administrátorské příručce SELinuxu](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/SELinux_Users_and_Administrators_Guide/sect-Security-Enhanced_Linux-Working_with_SELinux-Changing_SELinux_Modes.html#sect-Security-Enhanced_Linux-Enabling_and_Disabling_SELinux-Disabling_SELinux) o virtuálním počítači.
* Po zakázání SELinuxu restartujte virtuální počítač alespoň jednou.

### <a name="steps"></a>Kroky
1. Vytvořte virtuální virtuální hopomocí pomocí jedné z dříve určených distribucí.

   Pro CentOS 7.2 je šifrování disku operačního systému podporováno pomocí speciálního obrazu. Chcete-li použít tento obrázek, zadejte "7.2n" jako skladovou položku při vytváření virtuálního virtuálního mísy:

   ```powershell
    Set-AzVMSourceImage -VM $VirtualMachine -PublisherName "OpenLogic" -Offer "CentOS" -Skus "7.2n" -Version "latest"
   ```
2. Nakonfigurujte virtuální počítač podle svých potřeb. Pokud se chystáte šifrovat všechny jednotky (OS + data), musí být datové jednotky zadány a připojitelné z /etc/fstab.

   > [!NOTE]
   > Použít UUID=... chcete-li místo určení názvu blokového zařízení (například /dev/sdb1) zadat datové jednotky v parametru /etc/fstab. Během šifrování se změní pořadí jednotek na virtuálním počítači. Pokud váš virtuální počítač závisí na konkrétní pořadí blokových zařízení, se nezdaří připojit po šifrování.

3. Odhlaste se z relace SSH.

4. Chcete-li zašifrovat operační systém, zadejte volumeType jako **Všechny** nebo **OS,** když povolíte šifrování.

   > [!NOTE]
   > Všechny procesy uživatelského prostoru, `systemd` které nejsou spuštěny jako služby by měly být usmrceny `SIGKILL`s . Restartujte virtuální počítač. Když povolíte šifrování disku operačního systému na spuštěném virtuálním počítači, plánujte na prostoje virtuálních počítačů.

5. Pravidelně sledujte průběh šifrování pomocí pokynů v [další části](#monitoring-os-encryption-progress).

6. Po Get-AzVmDiskEncryptionStatus zobrazí "VMRestartPending", restartujte virtuální počítač buď přihlášením nebo pomocí portálu, PowerShell nebo CLI.
    ```powershell
    C:\> Get-AzVmDiskEncryptionStatus  -ResourceGroupName $ResourceGroupName -VMName $VMName
    -ExtensionName $ExtensionName

    OsVolumeEncrypted          : VMRestartPending
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk successfully encrypted, reboot the VM
    ```
   Před restartováním doporučujeme uložit [diagnostiku spuštění](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/) virtuálního počítače.

## <a name="monitoring-os-encryption-progress"></a>Sledování průběhu šifrování operačního systému
Průběh šifrování operačního režimu můžete sledovat třemi způsoby:

* Použijte `Get-AzVmDiskEncryptionStatus` rutinu a zkontrolujte pole ProgressMessage:
    ```powershell
    OsVolumeEncrypted          : EncryptionInProgress
    DataVolumesEncrypted       : NotMounted
    OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
    ProgressMessage            : OS disk encryption started
    ```
  Poté, co virtuální počítač dosáhne "šifrování disku operačního systému spuštěno", trvá asi 40 až 50 minut na virtuálním počítači podporovaném úložištěm Premium.

  Z důvodu [problému #388](https://github.com/Azure/WALinuxAgent/issues/388) walinuxagent, `OsVolumeEncrypted` a `DataVolumesEncrypted` ukázat se jako `Unknown` v některých distribucích. S WALinuxAgent verze 2.1.5 a novější, tento problém je vyřešen automaticky. Pokud se `Unknown` ve výstupu zobrazí, můžete ověřit stav šifrování disku pomocí Průzkumníka prostředků Azure.

  Přejděte do [Průzkumníka prostředků Azure](https://resources.azure.com/)a rozbalte tuto hierarchii v panelu pro výběr vlevo:

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

  V zobrazení InstanceView přejděte dolů a zobrazte stav šifrování jednotek.

  ![Zobrazení instance virtuálního vana](./media/disk-encryption/vm-instanceview.png)

* Podívejte se na [boot diagnostiku](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/). Zprávy z rozšíření ADE by `[AzureDiskEncryption]`měly být předponou .

* Přihlaste se k virtuálnímu virtuálnímu virtuálnímu mísu přes SSH a získejte protokol rozšíření od:

    /var/log/azure/Microsoft.Azure.Security.AzureDiskEncryptionForLinux

  Doporučujeme, abyste se nepřihlašovali k virtuálnímu virtuálnímu virtuálnímu ms, zatímco probíhá šifrování operačního. Zkopírujte protokoly pouze v případě, že ostatní dvě metody selhaly.

## <a name="prepare-a-pre-encrypted-linux-vhd"></a>Příprava předem šifrovaného virtuálního pevného disku systému Linux
Příprava na předem šifrované vhánění se může lišit v závislosti na distribuci. Příklady přípravy Ubuntu 16, openSUSE 13.2 a CentOS 7 jsou k dispozici. 

### <a name="ubuntu-16"></a>Ubuntu 16
Nakonfigurujte šifrování během instalace distribuce následujícím postupem:

1. Při rozdělení disků vyberte **Konfigurovat šifrované svazky.**

   ![Nastavení Ubuntu 16.04 - Konfigurace šifrovaných svazků](./media/disk-encryption/ubuntu-1604-preencrypted-fig1.png)

2. Vytvořte samostatnou spouštěcí jednotku, která nesmí být šifrována. Zašifrujte kořenovou jednotku.

   ![Nastavení Ubuntu 16.04 - Vyberte zařízení k šifrování](./media/disk-encryption/ubuntu-1604-preencrypted-fig2.png)

3. Zadejte přístupové heslo. Toto je přístupové heslo, které jste nahráli do trezoru klíčů.

   ![Nastavení Ubuntu 16.04 - Poskytnutí přístupové fráze](./media/disk-encryption/ubuntu-1604-preencrypted-fig3.png)

4. Dokončete dělení.

   ![Nastavení Ubuntu 16.04 - Dokončete dělení](./media/disk-encryption/ubuntu-1604-preencrypted-fig4.png)

5. Když spustíte virtuální hod a budete požádáni o přístupové heslo, použijte přístupové heslo, které jste zadali v kroku 3.

   ![Nastavení Ubuntu 16.04 - Poskytnutí přístupové fráze při startu](./media/disk-encryption/ubuntu-1604-preencrypted-fig5.png)

6. Připravte virtuální počítač pro nahrávání do Azure podle [těchto pokynů](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-ubuntu/). Ještě nespouštějte poslední krok (zrušení zřízení virtuálního soudu).

Nakonfigurujte šifrování tak, aby fungovalo s Azure, a to takto:

1. Vytvořte soubor pod /usr/local/sbin/azure_crypt_key.sh s obsahem v následujícím skriptu. Věnujte pozornost KeyFileName, protože se jedná o název souboru hesel používaný Azure.

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

2. Změňte kryptografický konfigurátor v */etc/crypttab*. Mělo by to vypadat takto:
   ```
    xxx_crypt uuid=xxxxxxxxxxxxxxxxxxxxx none luks,discard,keyscript=/usr/local/sbin/azure_crypt_key.sh
    ```

4. Přidejte ke skriptu spustitelná oprávnění:
   ```
    chmod +x /usr/local/sbin/azure_crypt_key.sh
   ```
5. Upravit */etc/initramfs-tools/modules* připojením řádků:
   ```
    vfat
    ntfs
    nls_cp437
    nls_utf8
    nls_iso8859-1
   ```
6. Spusťte `update-initramfs -u -k all` aktualizaci initramfs, aby se projevily. `keyscript`

7. Teď můžete zrušení zřízení virtuálního soudu.

   ![Ubuntu 16.04 Nastavení - update-initramfs](./media/disk-encryption/ubuntu-1604-preencrypted-fig6.png)

8. Pokračujte dalším krokem a nahrajte virtuální pevný disk do Azure.

### <a name="opensuse-132"></a>openSUSE 13.2
Chcete-li během instalace distribuce nakonfigurovat šifrování, postupujte takto:
1. Když disky rozdělíte, vyberte **Šifrovat skupinu svazků**a zadejte heslo. Toto je heslo, které nahrajete do trezoru klíčů.

   ![openSUSE 13.2 Setup - Šifrovat skupinu svazků](./media/disk-encryption/opensuse-encrypt-fig1.png)

2. Spusťte virtuální počítač pomocí hesla.

   ![openSUSE 13.2 Setup - Zadejte přístupové heslo při startu](./media/disk-encryption/opensuse-encrypt-fig2.png)

3. Připravte virtuální počítač na nahrání do Azure podle pokynů v [části Příprava sles nebo openSUSE virtuální počítač pro Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-suse-create-upload-vhd/#prepare-opensuse-131). Ještě nespouštějte poslední krok (zrušení zřízení virtuálního soudu).

Chcete-li nakonfigurovat šifrování tak, aby fungovalo s Azure, postupujte takto:
1. Upravte /etc/dracut.conf a přidejte následující řádek:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```
2. Zakomentujte tyto řádky do konce souboru /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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

3. Připojte následující řádek na začátku souboru /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   A změnit všechny výskyty:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na:
   ```bash
    if [ 1 ]; then
   ```
4. Upravte /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh a připojte jej k "# Open LUKS device":

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
5. Spuštěním `/usr/sbin/dracut -f -v` aktualizujte initrd.

6. Teď můžete zřídit virtuální počítač a nahrát virtuální pevný disk do Azure.

### <a name="centos-7-and-rhel-81"></a>CentOS 7 a RHEL 8.1

Chcete-li během instalace distribuce nakonfigurovat šifrování, postupujte takto:
1. Při rozdělení disků vyberte **Možnost Zašifrovat moje data.**

   ![Nastavení CentOS 7 - cíl instalace](./media/disk-encryption/centos-encrypt-fig1.png)

2. Ujistěte **se,** že je pro kořenový oddíl vybrána možnost Šifrovat.

   ![Nastavení CentOS 7 -Select encrypt for root partition](./media/disk-encryption/centos-encrypt-fig2.png)

3. Zadejte přístupové heslo. Toto je přístupové heslo, které nahrajete do trezoru klíčů.

   ![Nastavení CentOS 7 - poskytnutí přístupové fráze](./media/disk-encryption/centos-encrypt-fig3.png)

4. Když spustíte virtuální hod a budete požádáni o přístupové heslo, použijte přístupové heslo, které jste zadali v kroku 3.

   ![Nastavení CentOS 7 - Při spuštění zadejte přístupové heslo](./media/disk-encryption/centos-encrypt-fig4.png)

5. Připravte virtuální počítač pro nahrávání do Azure pomocí pokynů "CentOS 7.0+" v [části Příprava virtuálního počítače založeného na CentOS pro Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-linux-create-upload-centos/#centos-70). Ještě nespouštějte poslední krok (zrušení zřízení virtuálního soudu).

6. Teď můžete zřídit virtuální počítač a nahrát virtuální pevný disk do Azure.

Chcete-li nakonfigurovat šifrování tak, aby fungovalo s Azure, postupujte takto:

1. Upravte /etc/dracut.conf a přidejte následující řádek:
    ```
    add_drivers+=" vfat ntfs nls_cp437 nls_iso8859-1"
    ```

2. Zakomentujte tyto řádky do konce souboru /usr/lib/dracut/modules.d/90crypt/module-setup.sh:
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

3. Připojte následující řádek na začátku souboru /usr/lib/dracut/modules.d/90crypt/parse-crypt.sh:
   ```bash
    DRACUT_SYSTEMD=0
   ```
   A změnit všechny výskyty:
   ```bash
    if [ -z "$DRACUT_SYSTEMD" ]; then
   ```
   na
   ```bash
    if [ 1 ]; then
   ```
4. Upravte /usr/lib/dracut/modules.d/90crypt/cryptroot-ask.sh a přiložte následující za "# Otevřít zařízení LUKS":
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
5. Spusťte "/usr/sbin/dracut -f -v" a aktualizujte initrd.

    ![Nastavení CentOS 7 - běh /usr/sbin/dracut -f -v](./media/disk-encryption/centos-encrypt-fig5.png)

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Nahrání šifrovaného virtuálního pevného disku do účtu úložiště Azure
Po povolení šifrování DM-Crypt je třeba místní šifrovaný virtuální pevný disk nahrát do vašeho účtu úložiště.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```
## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a>Nahrání tajného klíče pro předem zašifrovaný virtuální počítač do trezoru klíčů
Při šifrování pomocí aplikace Azure AD (předchozí verze) musí být tajný klíč šifrování disku, který jste získali dříve, odeslán jako tajný klíč do trezoru klíčů. Trezor klíčů musí mít pro klienta Azure AD povolena šifrování disku a oprávnění.

```powershell 
 $AadClientId = "My-AAD-Client-Id"
 $AadClientSecret = "My-AAD-Client-Secret"

 $key vault = New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -Location $Location

 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -ServicePrincipalName $AadClientId -PermissionsToKeys all -PermissionsToSecrets all
 Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $ResourceGroupName -EnabledForDiskEncryption
``` 

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>Tajný klíč šifrování disku není šifrován pomocí KEK
Chcete-li nastavit tajný klíč v trezoru klíčů, použijte [set-azKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret). Přístupové heslo je kódováno jako řetězec base64 a poté odesláno do trezoru klíčů. Kromě toho se ujistěte, že jsou při vytváření tajného klíče v trezoru klíčů nastaveny následující značky.

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


Použijte `$secretUrl` v dalším kroku pro [připojení disku operačního systému bez použití KEK](#without-using-a-kek).

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>Tajný klíč šifrování disku zašifrovaný pomocí kek
Před nahráním tajného klíče do trezoru klíčů jej můžete volitelně zašifrovat pomocí šifrovacího klíče klíče. Pomocí rozhraní [API](https://msdn.microsoft.com/library/azure/dn878066.aspx) pro zalamování nejprve zašifrujte tajný klíč pomocí šifrovacího klíče klíče. Výstupem této operace obtékání je řetězec kódovaný adresou URL base64, [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) který pak můžete nahrát jako tajný klíč pomocí rutiny.

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

Použití `$KeyEncryptionKey` `$secretUrl` a v dalším kroku pro [připojení disku operačního systému pomocí KEK](#using-a-kek).

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>Zadání tajné adresy URL při připojení disku operačního systému

###  <a name="without-using-a-kek"></a>Bez použití KEK
Při připojování disku operačního systému `$secretUrl`je třeba předat . Adresa URL byla vygenerována v části "Tajný klíč šifrování disku není šifrován pomocí KEK".
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
### <a name="using-a-kek"></a>Použití KEK
Když připojíte disk operačního `$KeyEncryptionKey` `$secretUrl`systému, předaj a . Adresa URL byla vygenerována v části "Tajný klíč šifrování disku zašifrovaný pomocí KEK".
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
