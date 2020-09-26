---
title: Postup změny velikosti šifrovaných disků správy logických svazků pomocí Azure Disk Encryption
description: Tento článek poskytuje pokyny pro změnu velikosti šifrovaných disků ADE pomocí správy logických svazků.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: ba652b9424b8d5ce1b6a2c5b7d70b8fd9e999323
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91342348"
---
# <a name="how-to-resize-logical-volume-management-devices-encrypted-with-azure-disk-encryption"></a>Postup změny velikosti zařízení pro správu logických svazků zašifrovaných pomocí Azure Disk Encryption

Tento článek popisuje podrobný postup, jak změnit velikost disků s šifrovanými daty ADE pomocí správy logických svazků (LVM) v systému Linux, která se vztahuje na více scénářů.

Tento postup platí pro následující prostředí:

- Distribuce systému Linux
    - RHEL 7 nebo novější
    - Ubuntu 16 +
    - SUSE 12 +
- Rozšíření pro jedno průchod Azure Disk Encryption
- Azure Disk Encryption rozšíření pro duální průchod

## <a name="considerations"></a>Požadavky

Tento dokument předpokládá, že:

1. Existuje existující konfigurace LVM.
   
   Další informace o konfiguraci LVM na VIRTUÁLNÍm počítači Linux najdete [v LVM konfigurace virtuálního počítače Linux](configure-lvm.md) .

2. Disky jsou už zašifrované pomocí Azure Disk Encryption pro informace o tom, jak nakonfigurovat LVM, můžete nakonfigurovat [LVM na](how-to-configure-lvm-raid-on-crypt.md) nešifrovaných discích.

3. Pro následující příklady máte požadované odborné znalosti pro Linux a LVM.

4. Rozumíte tomu, že doporučení k používání datových disků v Azure, jak je popsáno v tématu řešení potíží s [názvy zařízení](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems), je použití cest/dev/disk/scsi1/.

## <a name="scenarios"></a>Scénáře

Postupy v tomto článku se týkají následujících scénářů:

### <a name="for-traditional-lvm-and-lvm-on-crypt-configurations"></a>Pro tradiční konfigurace LVM a LVM

- Rozšíření logického svazku, když je dostupné místo v VG

### <a name="for-traditional-lvm-encryption-the-logical-volumes-are-encrypted-not-the-whole-disk"></a>Pro tradiční LVM šifrování (logické svazky se šifrují, ne celý disk)

- Rozšíření tradičního svazku LVM přidáním nové PV
- Rozšíření tradičního svazku LVM změnou velikosti stávající PV

### <a name="for-lvm-on-crypt-recommended-method-the-entire-disk-is-encrypted-not-only-the-logical-volume"></a>Pro LVM (doporučenou metodu je celý disk zašifrovaný, nikoli jenom logický svazek)

- Rozšíření LVM na svazek s nešifrovaným přidáním nové PV
- Rozšíření LVM na svazek nešifrované změny velikosti stávající PV

> [!NOTE]
> Nedoporučuje se míchat tradiční šifrování LVM a LVM-on-crypt na stejném virtuálním počítači.

> [!NOTE]
> V těchto příkladech se používají předem existující názvy disků, fyzických svazků, skupin svazků, logických svazků, systému souborů, identifikátorů UUID a mountpoints, a proto je potřeba nahradit hodnoty uvedené v těchto příkladech tak, aby vyhovovaly vašemu prostředí.

#### <a name="extending-a-logical-volume-when-theres-available-space-in-the-vg"></a>Rozšíření logického svazku, když je dostupné místo v VG

Tradiční metoda používaná při změně velikosti logických svazků, kterou je možné použít pro nešifrované disky, tradiční šifrované svazky LVM a konfigurace LVM.

1. Ověřte aktuální velikost systému souborů, kterou chceme zvětšit:

    ``` bash
    df -h /mountpoint
    ```

    ![scénář-check-FS1](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Ověřte, že VG má dostatek místa pro rozšíření LV

    ``` bash
    vgs
    ```

    ![scénář-check-VG](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Můžete také použít "vgdisplay"

    ``` bash
    vgdisplay vgname
    ```

    ![scénář-check-vgdisplay](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Určete, který logický svazek se musí změnit.

    ``` bash
    lsblk
    ```

    ![scénář-check-lsblk1](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    Pro LVM-on-crypt je rozdíl v tomto výstupu, který ukazuje, že šifrovaná vrstva je na šifrované vrstvě pokrývající celý disk.

    ![scénář-check-lsblk2](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Ověřit velikost logického svazku

    ``` bash
    lvdisplay lvname
    ```

    ![scénář-check-lvdisplay01](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Zvětšete velikost LV pomocí příkazu-r pro online změnu velikosti systému souborů.

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scénář – Změna velikosti – LV](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Ověřte nové velikosti pro Lotyšsko a systém souborů.

    ``` bash
    df -h /mountpoint
    ```

    ![scénář-check-FS](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    Nová velikost se projeví, indikuje se úspěšná Změna velikosti LV a systému souborů.

7. Pro potvrzení změn na úrovni LV můžete znovu zkontrolovat informace pro Lotyšsko.

    ``` bash
    lvdisplay lvname
    ```

    ![scénář-check-lvdisplay2](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extending-a-traditional-lvm-volume-adding-a-new-pv"></a>Rozšíření tradičního svazku LVM přidáním nové PV

Platí v případě, že potřebujete přidat nový disk pro zvýšení velikosti skupiny svazků.

1. Ověřte aktuální velikost systému souborů, kterou chceme zvětšit:

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-check-FS](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Ověřit aktuální konfiguraci fyzického svazku

    ``` bash
    pvs
    ```

    ![scenariob-check-PVS](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Zkontroluje aktuální VG informace.

    ``` bash
    vgs
    ```

    ![scenariob-check-VGS](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Zkontroluje aktuální seznam disků.

    Datové disky by se měly identifikovat kontrolou zařízení pod/dev/disk/Azure/scsi1/

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-check-scs1](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Kontrolovat výstup lsblk 

    ``` bash
    lsbk
    ```

    ![scenariob-check-lsblk](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Připojit nový disk k virtuálnímu počítači

    Pokračujte až na krok 4 v následujícím dokumentu.

   - [Připojení disku k virtuálnímu počítači](attach-disk-portal.md)

7. Po připojení disku se podívejte na seznam disků, Všimněte si nového disku.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenariob-check-scsi12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![scenariob-check-lsblk12](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Vytvoření nové PV nad novým datovým diskem

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![scenariob-pvcreate](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Tato metoda používá celý disk jako souč_hod bez oddílu, volitelně můžete pomocí příkazu "Fdisk" vytvořit oddíl a potom použít tento oddíl pro "pvcreate".

9. Ověřte, že byla souč_hod přidána do seznamu PV.

    ``` bash
    pvs
    ```

    ![scenariob-check-pvs1](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Rozšíření VG přidáním nové PV do IT

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![scenariob-VG – rozšiřování](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Podívejte se na novou velikost VG

    ``` bash
    vgs
    ```

    ![scenariob-check-vgs1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Pomocí lsblk určete, která z nich se má změnit.

    ``` bash
    lsblk
    ```

    ![scenariob-check-lsblk1](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Zvětšete velikost LV pomocí "-r" a proveďte online zvýšení systému souborů.

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![scenariob-lvextend](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Ověřte nové velikosti LV a systému souborů.

    ``` bash
    df -h /mountpoint
    ```

    ![scenariob-check-FS1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    Je důležité znát, že když se ADE používá pro tradiční konfigurace LVM, vytvoří se šifrovaná vrstva na úrovni LV, ne na úrovni disku.

    V tomto okamžiku je šifrovaná vrstva rozšířena na nový disk.
    Skutečný datový disk nemá žádná nastavení šifrování na úrovni platformy, takže jeho stav šifrování není aktualizovaný.

    >[!NOTE]
    >Jedná se o některé z důvodů, proč je LVM doporučený přístup. 

15. Podívejte se na informace o šifrování z portálu:

    ![scenariob-check-portal1](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Aby bylo možné aktualizovat nastavení šifrování na disku, je nutné přidat novou aplikaci LV a povolit rozšíření na VIRTUÁLNÍm počítači.
    
16. Přidejte nový LV, vytvořte v něm systém souborů a přidejte ho do/etc/fstab.

17. Nastavte znovu příponu šifrování, aby se nastavení šifrování na novém datovém disku na úrovni platformy nastavilo jako razítko.

    Příklad:

    Rozhraní příkazového řádku

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Podívejte se na informace o šifrování z portálu:

    ![scenariob-check-portal2](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

19. Po aktualizaci nastavení šifrování můžete nový příkaz LV odstranit, ale budete muset odstranit položku z/etc/fstab a/etc/crypttab, které se pro ni vytvořily.

    ![scenariob-Delete-fstab-crypttab](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

20. Odpojte logický svazek.

    ``` bash
    umount /mountpoint
    ```

21. Zavřít šifrovanou vrstvu svazku

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

22. Odstranit LV

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extending-a-traditional-lvm-volume-resizing-an-existing-pv"></a>Rozšíření tradičního svazku LVM změnou velikosti stávající PV

Některé scénáře nebo omezení vyžadují změnu velikosti existujícího disku.

1. Identifikace šifrovaných disků

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![scenarioc-check-scsi1](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![scenarioc-check-lsblk](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Podívejte se na informace o PV.

    ``` bash
    pvs
    ```

    ![scenarioc-check-PVS](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Veškeré místo na všech PVs se aktuálně používá.

3. Podívejte se na informace o VGs

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![scenarioc-check-VGS](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Ověřte velikosti disků, můžete použít nástroje Fdisk nebo lsblk k vypsání velikosti jednotek.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-check-Fdisk](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Zjistili jsme, které PVs jsou přidružené k tomu, které LVs pomocí lsblk-FS. můžete je taky identifikovat spuštěním lvdisplay.

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![check-lvdisplay](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    V tomto konkrétním případě všechny 4 datové jednotky jsou součástí stejného VG a jedné LV, konfigurace se může od tohoto příkladu lišit.

5. Zkontroluje aktuální využití systému souborů:

    ``` bash
    df -h /datalvm*
    ```

    ![scenarioc-check-DF](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Změnit velikost datových disků:

    Můžete odkazovat na [disky se systémem Linux](expand-disks.md) (jenom na změnu velikosti disku). Tento krok můžete provést pomocí portálu, rozhraní příkazového řádku nebo PowerShellu.

    >[!NOTE]
    >Zvažte prosím, že operace změny velikosti u virtuálních disků nejde provádět s virtuálním počítačem, na kterém běží. Pro tento krok budete muset zrušit přidělení virtuálního počítače.

7. Když se změní velikost disků na potřebnou hodnotu, spusťte virtuální počítač a ověřte nové velikosti pomocí nástroje Fdisk.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![scenarioc-check-fdisk1](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    V tomto konkrétním případě se velikost/dev/SDD změnila z 5G na 20G

8. Zkontroluje aktuální velikost PV.

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-check-pvdisplay](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    I v případě, že došlo ke změně velikosti disku, má souč_hod ještě předchozí velikost.

9. Změna velikosti PV

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![scenarioc-check-pvresize](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Ověřit velikost PV

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![scenarioc-check-pvdisplay1](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Použijte stejný postup pro všechny disky, u kterých chcete změnit velikost.

11. Podívejte se na informace o VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenarioc-check-vgdisplay1](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    VG má teď prostor, který se má přidělit LVs.

12. Změnit velikost pro LV

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![scenarioc-check-lvresize1](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Ověřit velikost FS

    ``` bash
    df -h /datalvm2
    ```

    ![scenarioc-check-df3](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extending-an-lvm-on-crypt-volume-adding-a-new-pv"></a>Rozšíření svazku LVM on-crypt přidáním nové PV

Tato metoda pečlivě postupuje podle kroků v části [Konfigurace LVM na nešifrovaný](how-to-configure-lvm-raid-on-crypt.md) objekt pro přidání nového disku a jeho konfigurace v rámci konfigurace LVM-on-crypt.

Tuto metodu můžete použít k přidání prostoru k již existujícímu objektu LV nebo místo toho, abyste mohli vytvořit nové VGs nebo LVs.

1. Ověřit aktuální velikost vašeho VGu

    ``` bash
    vgdisplay vgname
    ```

    ![scénář-check-vg01](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Ověřte velikost FS a LV, které chcete zvýšit.

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scénář-check-lv01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![scénář-check-FS01](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Přidejte do virtuálního počítače nový datový disk a Identifikujte ho.

    Než disk přidáte, Projděte si disky.

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scénář-check-newdisk01](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Před přidáním nového disku ověřte disky.

    ``` bash
    lsblk
    ```

    ![scénář-check-newdisk002](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Přidejte nový disk buď pomocí PowerShellu, rozhraní příkazového řádku Azure nebo Azure Portal. Podívejte se, jak [připojit disk](attach-disk-portal.md) pro referenci na přidávání disků do virtuálního počítače.

    Po schématu názvu jádra pro zařízení se nové jednotce normálně přiřadí další dostupné písmeno, v tomto konkrétním případě je nový přidaný disk SDD.

4. Po přidání nového disku ověřte disky.

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![scénář-check-newdisk02](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)-

    ``` bash
    lsblk
    ```

    ![scénář-check-newdisk003](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Vytvoření systému souborů nad nedávno přidaným diskem

    Porovnat nedávno přidaný disk s propojenými zařízeními v/dev/disk/Azure/scsi1/

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![scénář-check-newdisk03](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![scénář – mkfs01](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Vytvoření nového dočasného přípojný bodu pro nový přidaný disk

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Přidat nedávno vytvořený systém souborů do/etc/fstab

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Připojit novou vytvořenou FS pomocí Mount-a

    ``` bash
    mount -a
    ```

9. Ověřte, že je připojená nová přidaná FS.

    ``` bash
    df -h
    ```

    ![Změna velikosti – LVM – scénář – DF](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Změna velikosti – LVM – scénář – lsblk](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Znovu spustit šifrování dříve spuštěno pro datové jednotky

    V případě LVM-on-crypt doporučujeme použít EncryptFormatAll, jinak může při nastavování dalších disků dojít k dvojitému šifrování.

    Informace o využití najdete v tématu [Konfigurace LVM na nešifrovaných počítačích](how-to-configure-lvm-raid-on-crypt.md).

    Příklad:

    ``` bash
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

    Po dokončení šifrování se na nově přidaném disku zobrazí vrstva kryptografie.

    ``` bash
    lsblk
    ```

    ![scénář – lsblk2](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Odpojte zašifrovanou vrstvu nového disku.

    ``` bash
    umount ${newmount}
    ```

12. Zkontroluje aktuální PVS informace.

    ``` bash
    pvs
    ```

    ![scénář – currentpvs](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Vytvoření PV na zašifrované vrstvě disku

    Chcete-li vytvořit PV, přitáhněte název zařízení z předchozího příkazu lsblk a přidejte/dev/Mapper před název zařízení.

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![scénář – pvcreate](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Zobrazí se upozornění týkající se Vymazání aktuálního podpisu ext4 FS, očekává se, odpověď y na tuto otázku.

14. Ověřte, že se nová souč_hod přidala do konfigurace LVM.

    ``` bash
    pvs
    ```

    ![scénář – newpv](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Přidejte novou souč_hod do VG, kterou potřebujete zvýšit.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![scénář – vgextent](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Ověřte novou velikost a volné místo pro VG.

    ``` bash
    vgdisplay vgname
    ```

    ![scénář – vgdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Všimněte si zvýšení celkového počtu PE a volného PE/velikosti.

17. Zvětšete velikost LV a FileSystem systému pomocí možnosti-r na lvextend (v tomto příkladu převezmeme celkový dostupný prostor v VG a přidáme ho do daného logického svazku).

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![scénář – lvextend](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

18. Ověřit velikost pro Lotyšsko

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![scénář – lvdisplay](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

19. Ověřte velikost systému souborů, u kterého se změnila velikost.

    ``` bash
    df -h mountpoint
    ```

    ![scénář – DF1](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

20. Ověřte, že je na zašifrované vrstvě vytvořená vrstva LVM.

    ``` bash
    lsblk
    ```

    ![scénář – lsblk3](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Když použijete lsblk bez možností, odeberou se přípojné body víckrát, protože se seřadí podle zařízení a logických svazků, možná budete chtít použít lsblk-FS,-s, aby se mountpoints zobrazovaly jenom jednou, a disky se zobrazí víckrát.

    ``` bash
    lsblk -fs
    ```

    ![scénář – lsblk4](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extending-an-lvm-on-crypt-volume-resizing-an-existing-pv"></a>Rozšíření LVM na svazek nešifrované změny velikosti stávající PV

1. Identifikace šifrovaných disků

    ``` bash
    lsblk
    ```

    ![scenariof-lsblk01](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![scenariof-lsblk012](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Podívejte se na informace o vaší PV.

    ``` bash
    pvs
    ```

    ![scenariof-pvs1](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Podívejte se na informace o VG

    ``` bash
    vgs
    ```

    ![scenariof-vgs](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Projděte si informace o aplikaci Lotyšsko

    ``` bash
    lvs
    ```

    ![scenariof-lvs](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Ověřit využití systému souborů

    ``` bash
    df -h /mountpoint(s)
    ```

    ![LVM-scenariof – FS](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Kontrolovat velikosti disků

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk01](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Změna velikosti datového disku

    Můžete odkazovat na [disky s rozbalenými Linuxy](expand-disks.md) (jenom na změnu velikosti disku). k provedení tohoto kroku můžete použít portál, CLI nebo PowerShell.

    >[!NOTE]
    >Zvažte prosím, že operace změny velikosti u virtuálních disků nejde provádět s virtuálním počítačem, na kterém běží. Pro tento krok budete muset zrušit přidělení virtuálního počítače.

8. Kontrolovat velikosti disků

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![scenariof-fdisk02](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    Všimněte si, že (v tomto případě) se velikost obou disků změnila z 2 GB na 4 GB, ale velikost FS, LV a souč_hod zůstane stejná.

9. Zkontroluje aktuální velikost PV.

    Pamatujte na to, že v LVM je to, že Souč_hod je zařízení/dev/Mapper/, ne zařízení/dev/SD *.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof-pvs](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Změna velikosti PV

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![scenariof – Změna velikosti – PV](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Po změně velikosti se podívejte na velikost PV.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![scenariof – PV](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Změnit velikost šifrované vrstvy na SOUČHODNOTA

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Použijte stejný postup pro všechny disky, u kterých chcete změnit velikost.

13. Podívejte se na informace o VG

    ``` bash
    vgdisplay vgname
    ```

    ![scenariof-vg](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    VG má teď prostor, který se má přidělit LVs.

14. Podívejte se na informace v části LV

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof – LV](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Ověření využití FS

    ``` bash
    df -h /mountpoint
    ```

    ![scenariof – FS](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Změnit velikost pro LV

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![scenariof-lvresize](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    K provedení změny velikosti FS používáme možnost-r.

17. Podívejte se na informace v části LV

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![scenariof-lvsize](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Ověřit využití systému souborů

    ``` bash
    df -h /mountpoint
    ```

    ![vytvořit systém souborů](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

    Použijte stejný postup změny velikosti u všech dalších LV, které to vyžadují.

## <a name="next-steps"></a>Další kroky

- [Řešení potíží se službou Azure Disk Encryption](disk-encryption-troubleshooting.md)
