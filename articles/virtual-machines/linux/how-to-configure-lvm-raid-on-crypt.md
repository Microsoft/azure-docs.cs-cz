---
title: Konfigurace LVM a RAID na šifrovaných zařízeních – Azure Disk Encryption
description: Tento článek poskytuje pokyny pro konfiguraci LVM a RAID na šifrovaných zařízeních pro virtuální počítače se systémem Linux.
author: jofrance
ms.service: virtual-machines
ms.subservice: security
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18
ms.openlocfilehash: b65c37ab06092be63cbb2ad9fb5e23cdb8324e80
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92476157"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>Konfigurace LVM a RAID na šifrovaných zařízeních

Tento článek popisuje podrobný postup, jak provádět správu logických svazků (LVM) a RAID v zašifrovaných zařízeních. Tento postup platí pro následující prostředí:

- Distribuce systému Linux
    - RHEL 7.6 +
    - Ubuntu 18.04 +
    - SUSE 12 +
- Rozšíření pro jedno průchod Azure Disk Encryption
- Azure Disk Encryption rozšíření pro duální průchod


## <a name="scenarios"></a>Scénáře

Postupy v tomto článku podporují následující scénáře:  

- Konfigurace LVM na šifrovaných zařízeních (LVM-on-crypt)
- Konfigurace RAID na zašifrovaných zařízeních (RAID-on-crypt)

Až budou základní zařízení nebo zařízení zašifrovaná, můžete vytvořit struktury LVM nebo RAID nad touto šifrovanou vrstvou. 

Fyzické svazky (PVs) se vytvoří v zašifrované vrstvě. Fyzické svazky slouží k vytvoření skupiny svazků. Vytvoříte svazky a přidáte požadované položky v adresáři/etc/fstab. 

![Diagram vrstev struktur LVM](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

Podobným způsobem se zařízení RAID vytvoří v horní části zašifrované vrstvy na discích. Na zařízení RAID se vytvoří systém souborů a jako běžné zařízení se přidají do/etc/fstab.

## <a name="considerations"></a>Požadavky

Doporučujeme použít LVM-on-crypt. RAID je možnost, když LVM nejde použít kvůli konkrétnímu omezení aplikace nebo prostředí.

Použijete možnost **EncryptFormatAll** . Další informace o této možnosti najdete v tématu [použití funkce EncryptFormatAll pro datové disky na virtuálních počítačích se systémem Linux](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

I když můžete použít tuto metodu, když šifrujete i operační systém, právě šifrujeme datové jednotky.

V těchto postupech se předpokládá, že už jste si přečetli požadavky v [Azure Disk Encryption scénářích na virtuálních počítačích s Linux](./disk-encryption-linux.md) a v [rychlém startu: vytvoření a šifrování virtuálního počítače se systémem Linux pomocí Azure CLI](./disk-encryption-cli-quickstart.md).

Verze Azure Disk Encryption s dvojím průchodem je na cestě pro vyřazení a neměla by se používat pro nová šifrování.

## <a name="general-steps"></a>Obecné kroky

Pokud používáte konfigurace "on-crypt", použijte proces popsaný v následujících postupech.

>[!NOTE] 
>V celém článku používáme proměnné. Nahraďte hodnoty odpovídajícím způsobem.

### <a name="deploy-a-vm"></a>Nasazení virtuálního počítače 
Následující příkazy jsou volitelné, ale doporučujeme je použít na nově nasazeném virtuálním počítači (VM).

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
Rozhraní příkazového řádku Azure:

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
### <a name="attach-disks-to-the-vm"></a>Připojit disky k virtuálnímu počítači
Pro `$N` počet nových disků, které se mají připojit k virtuálnímu počítači, opakujte následující příkazy.

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

Rozhraní příkazového řádku Azure:

```bash
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>Ověřte, že jsou disky připojené k virtuálnímu počítači.
PowerShell:
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![Seznam připojených disků v PowerShellu](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Rozhraní příkazového řádku Azure:

```bash
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Seznam připojených disků v Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

Portál:

![Seznam připojených disků na portálu](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

Operační systém:

```bash
lsblk 
```
![Seznam připojených disků v operačním systému](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>Konfigurace disků, které mají být zašifrovány
Tato konfigurace se provádí na úrovni operačního systému. Odpovídající disky jsou nakonfigurovány pro tradiční šifrování prostřednictvím Azure Disk Encryption:

- Systémy souborů jsou vytvořeny na discích.
- Pro připojení systémů souborů jsou vytvořeny dočasné přípojné body.
- Systémy souborů jsou nakonfigurovány na/etc/fstab, aby byly připojeny při spuštění.

Podívejte se na písmeno zařízení přiřazené novým diskům. V tomto příkladu používáme čtyři datové disky.

```bash
lsblk 
```
![Datové disky připojené k operačnímu systému](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>Vytvoření systému souborů na každém disku
Tento příkaz provede iteraci vytvoření systému souborů ext4 na všech discích definovaných v rámci cyklu for.

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![Vytvoření systému souborů ext4](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

Najděte univerzálně jedinečný identifikátor (UUID) systémů souborů, které jste nedávno vytvořili, vytvořte dočasnou složku, přidejte odpovídající položky v/etc/fstab a připojte všechny systémy souborů.

Tento příkaz také prochází na všech discích definovaných v části "pro" cyklus:

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>Ověřte, zda jsou disky správně připojeny.
```bash
lsblk
```
![Seznam připojených dočasných systémů souborů](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

Ověřte také, zda jsou disky konfigurovány:

```bash
cat /etc/fstab
```
![Konfigurační informace prostřednictvím fstab](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>Šifrování datových disků
PowerShell s použitím klíčového šifrovacího klíče (KEK):

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

Rozhraní příkazového řádku Azure pomocí KEK:

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

Rozhraní příkazového řádku Azure:

```bash
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Stav šifrování v Azure CLI](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

Portál:

![Stav šifrování na portálu](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

Úroveň operačního systému:

```bash
lsblk
```
![Stav šifrování v operačním systému](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

Rozšíření přidá systémy souborů do/var/lib/azure_disk_encryption_config/azure_crypt_mount (staré šifrování) nebo/etc/crypttab (nové šifrování).

>[!NOTE] 
>Neměňte žádné z těchto souborů.

Tento soubor se postará o aktivaci těchto disků během procesu spouštění, aby je LVM nebo RAID mohl použít později. 

Nedělejte si starosti s přípojnými body tohoto souboru. Po vytvoření fyzického svazku nebo zařízení RAID na těchto zašifrovaných zařízeních ztratí Azure Disk Encryption možnost získat disky připojené jako normální systém souborů. (Tato akce odebere formát systému souborů, který jsme použili během procesu přípravy.)

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>Odebrat dočasné složky a dočasné položky fstab
Systémy souborů odpojíte na discích, které se budou používat jako součást LVM.

```bash
for disk in c d e f; do unmount /tempdata${disk}; done
```
A odeberte položky/etc/fstab:

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>Ověřte, že disky nejsou připojené a že se odstranily položky v/etc/fstab.

```bash
lsblk
```
![Ověření, že jsou dočasné systémy souborů odpojeny](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

A ověřte, zda jsou disky konfigurovány:
```bash
cat /etc/fstab
```
![Ověření, že se odstranily dočasné položky fstab](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>Kroky pro LVM-on-crypt
Teď, když jsou základní disky šifrované, můžete vytvořit LVM struktury.

Místo použití názvu zařízení použijte cesty/dev/Mapper pro jednotlivé disky k vytvoření fyzického svazku (na vrstvě Crypto nad diskem, ne na samotném disku).

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>Konfigurace LVM nad šifrovanými vrstvami
#### <a name="create-the-physical-volumes"></a>Vytvoření fyzických svazků
Zobrazí se upozornění s dotazem, jestli chcete vymazat signaturu systému souborů. Pokračujte zadáním **y**, nebo použijte **echo "y"** , jak je znázorněno níže:

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Ověření, že byl vytvořen fyzický svazek](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>Názvy/dev/Mapper/Device je třeba nahradit skutečnými hodnotami na základě výstupu **lsblk**.

#### <a name="verify-the-information-for-physical-volumes"></a>Ověřte informace o fyzických svazcích.
```bash
pvs
```

![Informace o fyzických svazcích](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>Vytvoření skupiny svazků
Vytvořte skupinu svazků pomocí stejných zařízení, která jsou už inicializovaná:

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>Ověřte informace o skupině svazků.

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![Informace pro skupinu svazků](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>Vytváření logických svazků

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>Ověřte vytvořené logické svazky.

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![Informace pro logické svazky](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>Vytváření systémů souborů nad strukturami pro logické svazky

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>Vytvoření přípojných bodů pro nové systémy souborů

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>Přidejte nové systémy souborů pro/etc/fstab a připojte je.

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>Ověřte, že jsou připojené nové systémy souborů.

```bash
lsblk -fs
df -h
```
![Informace pro připojené systémy souborů](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

V této variaci **lsblk**uvádíme zařízení, která zobrazují závislosti v obráceném pořadí. Tato možnost pomáhá identifikovat zařízení seskupená podle logického svazku místo původních názvů zařízení/dev/SD [disk].

Je důležité zajistit, aby se možnost **neúspěšného** připojení přidala do možností přípojného bodu LVM svazků vytvořených na zařízení zašifrovaném pomocí Azure Disk Encryption. Zabrání operačnímu systému v zablokování během procesu spouštění (nebo v režimu údržby).

Pokud nepoužijete možnost **neúspěšné** :

- Operační systém se nikdy neobjeví ve fázi, kde je spuštěný Azure Disk Encryption a datové disky jsou odemčené a připojené. 
- Šifrované disky budou odemčeny na konci procesu spouštění. LVM svazky a systémy souborů budou automaticky připojeny, dokud je Azure Disk Encryption odemkne. 

Můžete otestovat restartování virtuálního počítače a ověřit, zda jsou systémy souborů také automaticky připojeny po čase spuštění. Tento proces může trvat několik minut v závislosti na počtu a velikosti systémů souborů.

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>Restartujte virtuální počítač a po restartování ověřte

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>Postup pro RAID-on-crypt
Teď, když jsou základní disky šifrované, můžete pokračovat v vytváření struktur RAID. Proces je stejný jako ten pro LVM, ale místo použití názvu zařízení použijte cestu/dev/Mapper pro každý disk.

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>Konfigurace RAID v horní části zašifrované vrstvy disků
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![Informace pro konfiguraci RAID pomocí příkazu mdadm](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>Názvy/dev/Mapper/Device je třeba nahradit skutečnými hodnotami, a to na základě výstupu **lsblk**.

### <a name="checkmonitor-raid-creation"></a>Kontrolovat a monitorovat vytváření polí RAID
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![Stav RAID](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>Vytvoření systému souborů nad novým zařízením RAID
```bash
mkfs.ext4 /dev/md10
```

Vytvořte nový přípojný bod pro systém souborů, přidejte nový systém souborů do/etc/fstab a připojte ho:

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
![Informace pro připojené systémy souborů](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

Je důležité zajistit, aby se možnost **neúspěšného** připojení přidala do možností přípojných bodů svazků RAID vytvořených na zařízení zašifrovaném pomocí Azure Disk Encryption. Zabrání operačnímu systému v zablokování během procesu spouštění (nebo v režimu údržby).

Pokud nepoužijete možnost **neúspěšné** :

- Operační systém se nikdy neobjeví ve fázi, kde je spuštěný Azure Disk Encryption a datové disky jsou odemčené a připojené.
- Šifrované disky budou odemčeny na konci procesu spouštění. Svazky a systémy souborů RAID budou automaticky připojeny, dokud je Azure Disk Encryption odemkne.

Můžete otestovat restartování virtuálního počítače a ověřit, zda jsou systémy souborů také automaticky připojeny po čase spuštění. Tento proces může trvat několik minut v závislosti na počtu a velikosti systémů souborů.

```bash
shutdown -r now
```

A když se můžete přihlásit:

```bash
lsblk
df -h
```
## <a name="next-steps"></a>Další kroky

- [Změna velikosti zařízení pro správu logických svazků zašifrovaných pomocí Azure Disk Encryption](how-to-resize-encrypted-lvm.md)
- [Řešení potíží se službou Azure Disk Encryption](disk-encryption-troubleshooting.md)
