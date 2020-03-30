---
title: Jak nakonfigurovat LVM a RAID on-crypt na virtuálním počítači SIP
description: Tento článek obsahuje pokyny ke konfiguraci LVM a RAID na crypt na virtuálních počítačích S IP.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 78ba47ba887cf7c90adf70d9d444fbd8a3c721cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284904"
---
# <a name="how-to-configure-lvm-and-raid-on-crypt"></a>Jak nakonfigurovat LVM a RAID on-crypt

Tento dokument je podrobný proces o tom, jak provádět LVM na kryptě a Raid na kryptě konfigurace.

### <a name="environment"></a>Prostředí

- Distribuce Linuxu
    - RHEL 7,6+
    - Ubuntu 18.04+
    - SUSE 12+
- ADE jeden průchod
- Duální průchod ADE


## <a name="scenarios"></a>Scénáře

**Tento scénář je použitelný pro ade dual pass a jednoprůchodové rozšíření.**  

- Konfigurace LVM na šifrovaných zařízeních (LVM-on-Crypt)
- Konfigurace raidu na šifrovaných zařízeních (RAID-on-Crypt)

Jakmile jsou podkladové zařízení zašifrovány, můžete vytvořit struktury LVM nebo RAID nad tuto šifrovanou vrstvou. Fyzické svazky (PV) jsou vytvořeny v horní části šifrované vrstvy.
Fyzické svazky se používají k vytvoření skupiny svazků.
Vytvoříte svazky a přidáte požadované položky na /etc/fstab. 

![Kontrola disků připojených powershellů](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Podobným způsobem je zařízení RAID vytvořeno nad šifrovanou vrstvou na discích. Souborový systém je vytvořen na vrcholu zařízení RAID a přidán do /etc/fstab jako běžné zařízení.

### <a name="considerations"></a>Požadavky

Doporučená metoda pro použití je LVM-on-Crypt.

RAID je považován za když LVM nelze použít z důvodu omezení konkrétní aplikace/prostředí.

Budete používat EncryptFormatAll možnost, informace o této funkci https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vmsse nachází zde: .

Zatímco tato metoda může být provedena také při šifrování operačního systému, jsme jen šifrování datových jednotek.

Tento postup předpokládá, že jste již přezkoumali zde https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux uvedené https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstartpředpoklady: a zde .

ADE dual pass verze je na deprecation cestu a by již neměly být používány na nové šifrování ADE.

### <a name="procedure"></a>Postup

Při použití konfigurací "na kryptě" budete sledovat níže uvedený proces:

>[!NOTE] 
>V celém dokumentu používáme proměnné, podle toho nahraďte hodnoty.
## <a name="general-steps"></a>Obecné kroky
### <a name="deploy-a-vm"></a>Nasazení virtuálního počítače 
>[!NOTE] 
>I když je to volitelné doporučujeme použít na nově nasazený virtuální počítač.

PowerShell
```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Cli:
```bash
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>Připojení disků k virtuálnímu počítače:
Opakujte $N počet nových disků, které chcete připojit k prostředí PowerShell virtuálního počítače.
```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```
Cli:
```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```
### <a name="verify-the-disks-are-attached-to-the-vm"></a>Ověřte, zda jsou disky připojené k virtuálnímu počítače:
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Zkontrolujte disky připojené](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png) cli prostředí PowerShell:
```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Zkontrolujte disky připojené](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png) CLI portál: ![Zkontrolujte](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png) disky připojené CLI OS:
```bash
lsblk 
```
![Kontrola disků připojeného portálu](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)
### <a name="configure-the-disks-to-be-encrypted"></a>Konfigurace zašifrovaných disků
Tato konfigurace se provádí, že úroveň operačního systému, odpovídající disky jsou konfigurovány pro tradiční šifrování ADE:

Souborové systémy jsou vytvořeny na horní části disků.

Pro připojení souborových systémů jsou vytvořeny dočasné přípojné body.

Souborové systémy jsou konfigurovány na /etc/fstab, aby byly namontovány při startu.

Zkontrolujte písmeno zařízení přiřazené k novým diskům, v tomto příkladu používáme čtyři datové disky

```bash
lsblk 
```
![Zkontrolujte disky připojené operační systém](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-filesystem-on-top-of-each-disk"></a>Vytvořte souborový systém nad každým diskem.
Tento příkaz itetuje vytvoření souborového systému ext4 na každém disku definovaném v části "pro" cyklu.
```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Zkontrolujte disky připojené](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png) os Najít UUID souborových systémů nedávno vytvořených, vytvořit dočasnou složku pro připojení, přidejte odpovídající položky na /etc/fstab a připojit všechny souborové systémy.

Tento příkaz také iterates na každém disku definované na "in" část "pro" cyklu:
```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 
### <a name="verify-the-disks-are-mounted-properly"></a>Ověřte, zda jsou disky správně připojeny:
```bash
lsblk
```
![Zkontrolujte, zda](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png) temp souborové systémy namontovány a nakonfigurovány:
```bash
cat /etc/fstab
```
![Zkontrolujte fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)
### <a name="encrypt-the-data-disks"></a>Šifrujte datové disky:
Prostředí PowerShell pomocí KEK:
```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```
CLI pomocí KEK:
```bash
az vm encryption enable \
--resource-group ${RGNAME} \
--name ${VMNAME} \
--disk-encryption-keyvault ${KEYVAULTNAME} \
--key-encryption-key ${KEYNAME} \
--key-encryption-keyvault ${KEYVAULTNAME} \
--volume-type "DATA" \
--encrypt-format-all \
-o table
```
### <a name="verify-the-encryption-status"></a>Ověření stavu šifrování

Pokračujte dalším krokem pouze v případě, že jsou všechny disky zašifrovány.

PowerShell:
```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![Zkontrolujte](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png) šifrování ps CLI:
```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Kontrola šifrování](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png) CLI ![portál:](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png) Zkontrolujte šifrování OS Úroveň:
```bash
lsblk
```
![Kontrola nastavení cli šifrování](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Rozšíření přidá souborové systémy do "/var/lib/azure_disk_encryption_config/azure_crypt_mount" (staré šifrování) nebo přidáno do "/etc/crypttab" (nová šifrování).

Neupravujte žádný z těchto souborů.

Tento soubor se bude starat o aktivaci těchto disků během procesu zavádění, takže mohou být později použity LVM nebo RAID. 

Nebojte se o přípojných bodů na tomto souboru, jako ADE ztratí schopnost získat disky namontovány jako normální souborový systém poté, co jsme si vytvořit fyzický svazek nebo raid zařízení na vrcholu těchto šifrovaných zařízení (který se zbavit formátu systému souborů jsme použili během přípravného procesu).
### <a name="remove-the-temp-folders-and-temp-fstab-entries"></a>Odebrání dočasných složek a dočasných fstab položek
Odpojit souborové systémy na discích, které budou použity jako součást LVM
```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
A odstranit / etc / fstab položky:
```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Ověřte, zda disky nejsou připojeny a zda byly odebrány položky na /etc/fstab
```bash
lsblk
```
![Zkontrolujte, zda](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png) dočasné souborové systémy unmounted A nakonfigurován:
```bash
cat /etc/fstab
```
![Zkontrolujte, zda jsou odebrány položky dočasného fstabu.](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)
## <a name="for-lvm-on-crypt"></a>Pro LVM-on-crypt
Nyní, když jsou podkladové disky zašifrovány, můžete pokračovat k vytvoření struktur LVM.

Namísto použití názvu zařízení použijte cesty /dev/mapper pro každý z disků k vytvoření fyzického svazku (na vrstvě kryptopty v horní části disku, který není na samotném disku).
### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Konfigurace LVM nad šifrovanými vrstvami
#### <a name="create-the-physical-volumes"></a>Vytvoření fyzických svazků
Zobrazí se upozornění s dotazem, zda je v pořádku vymazat podpis souborového systému. 

Můžete pokračovat zadáním "y" nebo použít echo "y", jak je znázorněno na obrázku:
```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![pvcreate](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)
>[!NOTE] 
>Názvy /dev/mapper/device zde je třeba nahradit pro vaše skutečné hodnoty na základě výstupu lsblk.
#### <a name="verify-the-physical-volumes-information"></a>Ověření informací o fyzických svazcích
```bash
pvs
```
![kontrola fyzických objemů 1](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)
#### <a name="create-the-volume-group"></a>Vytvoření skupiny svazků
Vytvoření VG pomocí stejných zařízení, která již byla inicializována
```bash
vgcreate vgdata /dev/mapper/
```
### <a name="check-the-volume-group-information"></a>Kontrola informací o skupině svazků
```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![kontrola fyzických objemů 2](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)
#### <a name="create-logical-volumes"></a>Vytvoření logických svazků
```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 
#### <a name="check-the-logical-volumes-created"></a>Kontrola vytvořených logických svazků
```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![zkontrolovat lvs](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)
#### <a name="create-filesystems-on-top-of-the-logical-volumes-structures"></a>Vytvoření souborových systémů nad strukturou logického svazku (struktur)
```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```
#### <a name="create-the-mount-points-for-the-new-filesystems"></a>Vytvoření přípojných bodů pro nové souborové systémy
```bash
mkdir /data0
mkdir /data1
```
#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Přidejte nové systémy souborů do /etc/fstab a připojte je
```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```
#### <a name="verify-that-the-new-filesystems-are-mounted"></a>Ověřte, zda jsou připojeny nové souborové systémy
```bash
lsblk -fs
df -h
```
![zkontrolujte logické](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png) svazky Na této variantě lsblk uvádíme zařízení zobrazující závislosti na obráceném pořadí, tato možnost pomáhá identifikovat zařízení seskupená podle logického svazku namísto původních názvů zařízení /dev/sd[disk].

Důležité: Ujistěte se, že je možnost "nofail" přidána do možností přípojného bodu svazků LVM vytvořených nad šifrovaným zařízením ADE. Je důležité, aby se zabránilo OS z uvíznutí během procesu zavádění (nebo v režimu údržby). 

Šifrovaný disk se odemkne na konci zaváděcího procesu, svazky LVM a souborové systémy budou automaticky připojeny.

Pokud se nepoužije možnost nofail, operační systém se nikdy nedostane do fáze, kdy je spuštěn ade a datové disky jsou odemčeny a připojeny.

Můžete otestovat restartování virtuálního počítače a ověřování souborových systémů jsou také automaticky připojeny po spuštění. 

Vezměte v úvahu, že tento proces může trvat několik minut v závislosti na počtu systémů souborů a velikostech
#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Restartujte virtuální počítač a ověřte po restartování
```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="for-raid-on-crypt"></a>Pro RAID-on-Crypt
Nyní jsou podkladové disky šifrovány, můžete pokračovat ve vytváření struktur RAID, stejně jako LVM, namísto použití názvu zařízení použijte cesty /dev/mapper pro každý z disků.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Konfigurace pole RAID v horní části šifrované vrstvy disků
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![mdadm vytvořit](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)
>[!NOTE] 
>Názvy /dev/mapper/device zde je třeba nahradit pro vaše skutečné hodnoty na základě výstupu lsblk.
#### <a name="checkmonitor-the-raid-creation"></a>Zkontrolujte/sledujte vytvoření raidu:
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![zkontrolovat mdadm](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)
#### <a name="create-a-filesystem-on-top-of-the-new-raid-device"></a>Vytvořte souborový systém nad novým zařízením Raid:
```bash
mkfs.ext4 /dev/md10
```
Vytvořte nový přípojný bod pro souborový systém, přidejte nový systém souborů do /etc/fstab a připojte jej
```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```
Ověřte, zda jsou připojeny nové souborové systémy
```bash
lsblk -fs
df -h
```
![zkontrolovat mdadm](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Důležité: Ujistěte se, že je možnost "nofail" přidána k možnostem přípojného bodu svazků RAID vytvořených nad šifrovaným zařízením ADE. 

Je velmi důležité, aby se zabránilo OS z uvíznutí během procesu zavádění (nebo v režimu údržby). 

Šifrovaný disk bude odemknut na konci zaváděcího procesu a svazky RAID a souborové systémy budou automaticky připojeny, dokud nebudou odemčeny ADE, pokud není použita možnost nofail.

Operační systém se nikdy nedostane do fáze, kdy je Spuštěno ADE, a datové disky jsou odemčeny a připojeny.

Můžete otestovat restartování virtuálního počítače a ověřování souborových systémů jsou také automaticky připojeny po spuštění. Vezměte v úvahu, že tento proces může trvat několik minut v závislosti na počtu systémů souborů a velikostech
```bash
shutdown -r now
```
A když se můžete přihlásit:
```bash
lsblk
df -h
```
## <a name="next-steps"></a>Další kroky

- [Řešení potíží se službou Azure Disk Encryption](disk-encryption-troubleshooting.md)

