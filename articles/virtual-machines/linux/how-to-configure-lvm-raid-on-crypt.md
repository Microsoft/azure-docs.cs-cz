---
title: Konfigurace LVM a RAID na šifrovaných zařízeních – Azure Disk Encryption
description: Tento článek obsahuje pokyny pro konfiguraci LVM a RAID na šifrovaných zařízeních pro virtuální počítače s Linuxem.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: 4e342ff44af38b8e79dc8695c1270b1f5c68e0a8
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657448"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Konfigurace LVM a RAID na šifrovaných zařízeních

Tento článek je podrobný proces, jak provádět správu logických svazků (LVM) a RAID na šifrovaných zařízeních. Tento proces se vztahuje na následující prostředí:

- Linuxové distribuce
    - RHEL 7,6+
    - Ubuntu 18.04+
    - SUSE 12+
- Jednoprůchodové rozšíření azure diskového šifrování
- Rozšíření o dvouprůchodové šifrování disku Azure


## <a name="scenarios"></a>Scénáře

Postupy v tomto článku podporují následující scénáře:  

- Konfigurace LVM nad šifrovanými zařízeními (LVM-on-crypt)
- Konfigurace raidu na šifrovaných zařízeních (RAID-on-crypt)

Po zašifrování podkladového zařízení nebo zařízení můžete vytvořit struktury LVM nebo RAID nad tuto šifrovanou vrstvou. 

Fyzické svazky (PV) jsou vytvořeny nad šifrovanou vrstvou. Fyzické svazky se používají k vytvoření skupiny svazků. Vytvoříte svazky a přidáte požadované položky na /etc/fstab. 

![Diagram vrstev struktur LVM](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Podobným způsobem je zařízení RAID vytvořeno nad šifrovanou vrstvou na discích. Souborový systém je vytvořen na vrcholu zařízení RAID a přidán do /etc/fstab jako běžné zařízení.

## <a name="considerations"></a>Požadavky

Doporučujeme používat LVM-on-crypt. RAID je možnost, když LVM nelze použít z důvodu omezení konkrétní aplikace nebo prostředí.

Budete používat možnost **EncryptFormatAll.** Další informace o této možnosti naleznete [v tématu Použití funkce EncryptFormatAll pro datové disky na virtuálních počítačích s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux#use-encryptformatall-feature-for-data-disks-on-linux-vms).

I když můžete použít tuto metodu, když jste také šifrování operačního systému, jsme jen šifrování datových jednotek zde.

Postupy předpokládají, že jste už zkontrolovali požadavky ve [scénářích Azure Disk Encryption na virtuálních počítačích s Linuxem](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-linux) a na [úvodním panelu: Vytvoření a šifrování virtuálního počítače s Linuxem pomocí azure cli](https://docs.microsoft.com/azure/virtual-machines/linux/disk-encryption-cli-quickstart).

Dvouprůchodová verze Azure Disk Encryption je na cestě vyřazení a už by se neměla používat na nových šifrováních.

## <a name="general-steps"></a>Obecné kroky

Pokud používáte konfigurace "on-crypt", použijte proces popsaný v následujících postupech.

>[!NOTE] 
>V celém článku používáme proměnné. Odpovídajícím způsobem nahraďte hodnoty.

### <a name="deploy-a-vm"></a>Nasazení virtuálního počítače 
Následující příkazy jsou volitelné, ale doporučujeme je použít na nově nasazený virtuální počítač (VM).

PowerShell:

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Cli Azure:

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
### <a name="attach-disks-to-the-vm"></a>Připojení disků k virtuálnímu počítače
Opakujte následující příkazy pro `$N` počet nových disků, které chcete připojit k virtuálnímu počítače.

PowerShell:

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Cli Azure:

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Ověřte, že jsou disky připojené k virtuálnímu počítače
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Seznam připojených disků v PowerShellu](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Cli Azure:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Seznam připojených disků v příkazovém příkazu k azure](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portál:

![Seznam připojených disků na portálu](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

Operační systém:

```bash
lsblk 
```
![Seznam připojených disků v os](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Konfigurace zašifrovaných disků
Tato konfigurace se provádí na úrovni operačního systému. Odpovídající disky jsou nakonfigurované pro tradiční šifrování prostřednictvím šifrování disku Azure:

- Systémy souborů jsou vytvořeny na horní části disků.
- Pro připojení systémů souborů jsou vytvořeny dočasné přípojné body.
- Souborové systémy jsou konfigurovány na /etc/fstab, které mají být namontovány při startu.

Zkontrolujte písmeno zařízení přiřazené k novým diskům. V tomto příkladu používáme čtyři datové disky.

```bash
lsblk 
```
![Datové disky připojené k os](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Vytvoření systému souborů nad každým diskem
Tento příkaz itetuje vytvoření systému souborů ext4 na každém disku definovaném v části "pro" cyklu.

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Vytvoření souborového systému ext4](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Najděte univerzálně jedinečný identifikátor (UUID) systémů souborů, které jste nedávno vytvořili, vytvořte dočasnou složku, přidejte odpovídající položky na /etc/fstab a připojte všechny systémy souborů.

Tento příkaz také iterates na každém disku definované na "in" část "pro" cyklu:

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Ověřte, zda jsou disky správně připojeny
```bash
lsblk
```
![Seznam namontovaných dočasných souborových systémů](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Ověřte také, zda jsou disky nakonfigurovány:

```bash
cat /etc/fstab
```
![Informace o konfiguraci prostřednictvím fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Šifrování datových disků
Prostředí PowerShell pomocí šifrovacího klíče (KEK):

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

Azure CLI pomocí KEK:

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
![Stav šifrování v PowerShellu](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Cli Azure:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Stav šifrování v nastavení nastavení nastavení příkazu Azure](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portál:

![Stav šifrování na portálu](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Úroveň operačního es:

```bash
lsblk
```
![Stav šifrování v os](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Přípona přidá souborové systémy do /var/lib/azure_disk_encryption_config/azure_crypt_mount (staré šifrování) nebo do /etc/crypttab (nová šifrování).

>[!NOTE] 
>Neupravujte žádný z těchto souborů.

Tento soubor se postará o aktivaci těchto disků během procesu spouštění, aby je LVM nebo RAID mohly později použít. 

Nestarejte se o přípojné body v tomto souboru. Azure Disk Encryption ztratí možnost připojit disky jako normální systém souborů poté, co vytvoříme fyzický svazek nebo zařízení RAID nad těmito šifrovanými zařízeními. (Tím se odstraní formát systému souborů, který jsme použili během procesu přípravy.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Odebrání dočasných složek a dočasných položek fstab
Odpojit systémy souborů na discích, které budou použity jako součást LVM.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
A odstranit / etc / fstab položky:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Ověřte, zda disky nejsou připojeny a zda byly odebrány položky na /etc/fstab

```bash
lsblk
```
![Ověření, že dočasné systémy souborů jsou odpojeny](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

A ověřte, zda jsou disky nakonfigurovány:
```bash
cat /etc/fstab
```
![Ověření, že dočasné fstab položky jsou odstraněny](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Kroky pro LVM-on-crypt
Nyní, když jsou podkladové disky zašifrovány, můžete vytvořit struktury LVM.

Namísto použití názvu zařízení použijte cesty /dev/mapper pro každý z disků k vytvoření fyzického svazku (na vrstvě kryptopty v horní části disku, nikoli na samotném disku).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Konfigurace LVM nad šifrovanými vrstvami
#### <a name="create-the-physical-volumes"></a>Vytvoření fyzických svazků
Zobrazí se upozornění, které se zeptá, zda je v pořádku vymazat podpis systému souborů. Pokračujte zadáním **y**nebo použijte **echo "y",** jak je znázorněno na obrázku:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Ověření, že byl vytvořen fyzický svazek](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>Názvy /dev/mapper/device zde je třeba nahradit pro vaše skutečné hodnoty na základě výstupu **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Ověření informací o fyzických svazcích
```bash
pvs
```

![Informace pro fyzické svazky](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Vytvoření skupiny svazků
Vytvořte skupinu svazků pomocí stejných zařízení, která již byla inicializována:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Kontrola informací pro skupinu svazků

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Informace pro skupinu svazků](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Vytvoření logických svazků

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Kontrola vytvořených logických svazků

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Informace pro logické svazky](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Vytvoření systémů souborů nad strukturami logických svazků

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Vytvoření přípojných bodů pro nové systémy souborů

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

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Ověřte, zda jsou připojeny nové systémy souborů

```bash
lsblk -fs
df -h
```
![Informace pro připojené souborové systémy](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

Na této variantě **lsblk**, jsme seznam zařízení zobrazující závislosti v opačném pořadí. Tato možnost pomáhá identifikovat zařízení seskupená podle logického svazku namísto původních názvů zařízení /dev/sd[disk].

Je důležité se ujistit, že možnost **nofail** je přidána do možností přípojného bodu svazků LVM vytvořených nad zařízením šifrovaným prostřednictvím šifrování disku Azure. Zabraňuje tomu, aby se operační systém zasekl během procesu spouštění (nebo v režimu údržby).

Pokud nepoužijete možnost **nofail:**

- Operační systém se nikdy nedostane do fáze, kdy je spuštěno azure disk encryption a datové disky jsou odemčené a připojené. 
- Šifrované disky budou na konci zaváděcího procesu odemčeny. Svazky LVM a systémy souborů se automaticky připojí, dokud je Azure Disk Encryption neodemkne. 

Můžete otestovat restartování virtuálního počítače a ověřit, že systémy souborů jsou také automaticky připojeny po spuštění. Tento proces může trvat několik minut, v závislosti na počtu a velikosti systémů souborů.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Restartujte virtuální počítač a ověřte po restartování

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Kroky pro RAID-on-crypt
Nyní, když jsou podkladové disky zašifrovány, můžete pokračovat ve vytváření struktur RAID. Proces je stejný jako pro LVM, ale místo použití názvu zařízení použijte /dev/mapper cesty pro každý disk.

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
![Informace pro nakonfigurovaný příkaz RAID pomocí příkazu mdadm](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>Názvy /dev/mapper/device zde musí být nahrazeny skutečnými hodnotami založenými na výstupu **lsblk**.

### <a name="checkmonitor-raid-creation"></a>Kontrola/monitorování vytvoření raidu
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Stav RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Vytvoření souborového systému nad novým zařízením RAID
```bash
mkfs.ext4 /dev/md10
```

Vytvořte nový přípojný bod pro systém souborů, přidejte nový systém souborů do /etc/fstab a připojte jej:

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

Ověřte, zda je nový systém souborů připojen:

```bash
lsblk -fs
df -h
```
![Informace pro připojené souborové systémy](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Je důležité se ujistit, že možnost **nofail** je přidána do možností přípojného bodu svazků RAID vytvořených nad zařízením šifrovaným prostřednictvím šifrování disku Azure. Zabraňuje tomu, aby se operační systém zasekl během procesu spouštění (nebo v režimu údržby).

Pokud nepoužijete možnost **nofail:**

- Operační systém se nikdy nedostane do fáze, kdy je spuštěno azure disk encryption a datové disky jsou odemčené a připojené.
- Šifrované disky budou na konci zaváděcího procesu odemčeny. Svazky raidů a souborové systémy se automaticky připojí, dokud je Azure Disk Encryption neodemkne.

Můžete otestovat restartování virtuálního počítače a ověřit, že systémy souborů jsou také automaticky připojeny po spuštění. Tento proces může trvat několik minut, v závislosti na počtu a velikosti systémů souborů.

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

