---
title: Změna velikosti šifrovaných disků správy logických svazků pomocí Azure Disk Encryption
description: Tento článek poskytuje pokyny pro změnu velikosti šifrovaných disků ADE pomocí správy logických svazků.
author: jofrance
ms.service: security
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: 3a3e9b7406e11261aff12d77d9fbeed5debbe938
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744266"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>Postup změny velikosti zařízení pro správu logických svazků, která používají Azure Disk Encryption

V tomto článku se dozvíte, jak změnit velikost datových disků, které používají Azure Disk Encryption. Pokud chcete změnit velikost disků, budete v systému Linux používat správu logických svazků (LVM). Tento postup se týká více scénářů.

Tento proces změny velikosti můžete použít v následujících prostředích:

- Distribuce systému Linux:
    - Red Hat Enterprise Linux (RHEL) 7 nebo novější
    - Ubuntu 16 nebo novější
    - SUSE 12 nebo novější
- Verze Azure Disk Encryption: 
    - Rozšíření s jedním průchodem
    - Rozšíření pro duální průchod

## <a name="prerequisites"></a>Předpoklady

V tomto článku se předpokládá, že máte následující:

- Existující konfigurace LVM Další informace najdete v tématu [Konfigurace LVM na virtuálním počítači se systémem Linux](configure-lvm.md).

- Disky, které jsou již zašifrované pomocí Azure Disk Encryption. Další informace najdete v tématu [Konfigurace LVM a RAID na šifrovaných zařízeních](how-to-configure-lvm-raid-on-crypt.md).

- Zkušenosti s používáním systému Linux a LVM.

- Zkušenosti s používáním */dev/disk/scsi1/* cest k datovým diskům v Azure. Další informace najdete v tématu [řešení potíží s názvem zařízení virtuálních počítačů se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-device-names-problems). 

## <a name="scenarios"></a>Scénáře

Postupy v tomto článku se týkají následujících scénářů:

- Tradiční konfigurace LVM a LVM
- Tradiční šifrování LVM 
- LVM – on-crypt 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>Tradiční konfigurace LVM a LVM

Tradiční konfigurace LVM a LVM-on-crypt rozšíří logický svazek (LV), pokud má skupina svazků (VG) dostupný prostor.

### <a name="traditional-lvm-encryption"></a>Tradiční šifrování LVM 

V tradičním šifrování LVM jsou LVs šifrované. Celý disk není zašifrovaný.

Pomocí tradičního šifrování LVM můžete:

- Po přidání nového fyzického svazku (PV) rozšíříte LV.
- Když změníte velikost stávající PV, rozšíříte v části LV.

### <a name="lvm-on-crypt"></a>LVM – on-crypt 

Doporučeným způsobem šifrování disku je LVM šifrování. Tato metoda šifruje celý disk, nikoli pouze LV.

Pomocí LVM-on-crypt můžete: 

- Když přidáte novou PV, rozšíříte LV.
- Když změníte velikost stávající PV, rozšíříte v části LV.

> [!NOTE]
> Nedoporučujeme míchat tradiční šifrování LVM a LVM-on-crypt na stejném virtuálním počítači.

Následující části obsahují příklady použití LVM a LVM-on-crypt. V příkladech se používají předem existující hodnoty pro disky, PVs, VGs, LVs, souborové systémy, univerzální jedinečné identifikátory (UUID) a přípojné body. Nahraďte tyto hodnoty vlastními hodnotami, které odpovídají vašemu prostředí.

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>Pokud má VG volné místo, rozšíříte LV.

Tradiční způsob, jak změnit velikost LVs, je rozmístit LV, pokud má VG k dispozici místo. Tuto metodu můžete použít pro nešifrované disky, tradiční svazky zašifrované LVM a konfigurace LVM-on-crypt.

1. Ověřte aktuální velikost systému souborů, který chcete zvětšit:

    ``` bash
    df -h /mountpoint
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost systému souborů. Příkaz a výsledek jsou zvýrazněny.](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. Ověřte, zda má VG dostatek místa pro zvýšení oblasti LV:

    ``` bash
    vgs
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje místo na VG Příkaz a výsledek jsou zvýrazněny.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    Můžete také použít `vgdisplay` :

    ``` bash
    vgdisplay vgname
    ```

    ![Snímek obrazovky zobrazující kód zobrazení V G, který kontroluje místo na VG Příkaz a výsledek jsou zvýrazněny.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. Určete, která pro Lotyšsko musí být změněna.

    ``` bash
    lsblk
    ```

    ![Snímek obrazovky zobrazující výsledek příkazu l s b l k Příkaz a výsledek jsou zvýrazněny.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    V případě LVM-on-crypt je rozdíl v tom, že se šifrovaná vrstva zobrazuje na úrovni disku.

    ![Snímek obrazovky zobrazující výsledek příkazu l s b l k Výstup je zvýrazněný. Zobrazuje šifrovanou vrstvu.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. Podívejte se na velikost LV:

    ``` bash
    lvdisplay lvname
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost logického svazku. Příkaz a výsledek jsou zvýrazněny.](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. Velikost v systému souborů zvětšete změnou velikosti v `-r` režimu online:

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Snímek obrazovky zobrazující kód, který zvětší velikost logického svazku. Příkaz a výsledky jsou zvýrazněné.](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. Ověřte nové velikosti pro Lotyšsko a systém souborů:

    ``` bash
    df -h /mountpoint
    ```

    ![Snímek obrazovky zobrazující kód, který ověřuje velikost souboru LV a systému souborů. Příkaz a výsledek jsou zvýrazněny.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    Výstupem velikosti je indikuje, že se změnila velikost LV a systému souborů.

Můžete znovu zkontrolovat informace LV a potvrdit změny na úrovni LV:

``` bash
lvdisplay lvname
```

![Snímek obrazovky zobrazující kód, který potvrdí nové velikosti. Velikosti se zvýrazní.](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>Rozšíření tradičního svazku LVM přidáním nové PV

Pokud potřebujete přidat nový disk, abyste zvětšili velikost VG, Rozšiřte svůj tradiční svazek LVM přidáním nové PV.

1. Ověřte aktuální velikost systému souborů, který chcete zvětšit:

    ``` bash
    df -h /mountpoint
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje aktuální velikost systému souborů. Příkaz a výsledek jsou zvýrazněny.](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. Ověřte aktuální konfiguraci PV:

    ``` bash
    pvs
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje aktuální konfiguraci PV. Příkaz a výsledek jsou zvýrazněny.](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. Podívejte se na aktuální informace o VG:

    ``` bash
    vgs
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje aktuální informace o skupině svazků Příkaz a výsledek jsou zvýrazněny.](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. Projděte si aktuální seznam disků. Identifikujte datové disky tak, že zkontrolujete zařízení v */dev/disk/Azure/scsi1/*.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje aktuální seznam disků. Příkaz a výsledky jsou zvýrazněné.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. Podívejte se na výstup `lsblk` : 

    ``` bash
    lsbk
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje výstup l s b l k. Příkaz a výsledky jsou zvýrazněné.](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. Připojte nový disk k virtuálnímu počítači podle pokynů v části [připojení datového disku k virtuálnímu počítači se systémem Linux](attach-disk-portal.md).

7. Podívejte se na seznam disků a Všimněte si nového disku.

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje seznam disků. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje seznam disků pomocí l s b l k. Příkaz a výsledek jsou zvýrazněny.](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. Vytvořte novou souč_hod nad novým datovým diskem:

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![Snímek obrazovky zobrazující kód, který vytváří novou PV. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    Tato metoda používá celý disk jako souč_hod bez oddílu. Případně můžete použít `fdisk` k vytvoření oddílu a pak použít tento oddíl pro `pvcreate` .

9. Ověřte, že se v seznamu PV přidala souč_hod:

    ``` bash
    pvs
    ```

    ![Snímek obrazovky zobrazující kód, který zobrazuje seznam fyzických svazků. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. Rozšíření VG přidáním nové PV do IT:

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![Snímek obrazovky zobrazující kód, který rozšiřuje skupinu svazků Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. Podívejte se na novou velikost VG:

    ``` bash
    vgs
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost skupiny svazků Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. Použijte `lsblk` k identifikaci LV, pro který je potřeba změnit velikost:

    ``` bash
    lsblk
    ```

    ![Snímek obrazovky zobrazující kód, který identifikuje místní svazek, u kterého je třeba změnit velikost. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. Rozšiřte systém souborů o v režimu online tak, že použijete velikost LV `-r` :

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![Snímek obrazovky zobrazující kód, který zvětšuje velikost systému souborů v režimu online. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. Ověřte nové velikosti aplikace LV a systému souborů:

    ``` bash
    df -h /mountpoint
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost místního svazku a systému souborů. Příkaz a výsledek jsou zvýrazněny.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >Když se šifrování dat Azure používá v tradičních konfiguracích LVM, zašifrovaná vrstva se vytvoří na úrovni LV, ne na úrovni disku.
    >
    >V tomto okamžiku je šifrovaná vrstva rozšířena na nový disk. Skutečný datový disk nemá na úrovni platformy žádná nastavení šifrování, takže jeho stav šifrování není aktualizovaný.
    >
    >Jedná se o některé z důvodů, proč je LVM doporučený přístup. 

15. Podívejte se na informace o šifrování z portálu:

    ![Snímek obrazovky zobrazující informace o šifrování na portálu Název disku a šifrování jsou zvýrazněné.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    Pokud chcete aktualizovat nastavení šifrování na disku, přidejte novou hodnotu LV a povolte rozšíření na virtuálním počítači.
    
16. Přidejte nový LV, vytvořte v něm systém souborů a přidejte ho do nástroje `/etc/fstab` .

17. Nastavte znovu příponu šifrování. Tentokrát budete razítkem nastavení šifrování na novém datovém disku na úrovni platformy. Tady je příklad rozhraní příkazového řádku:

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. Podívejte se na informace o šifrování z portálu:

    ![Snímek obrazovky zobrazující informace o šifrování na portálu Název disku a informace o šifrování jsou zvýrazněné.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

Po aktualizaci nastavení šifrování můžete odstranit nové LV. Odstraňte také položku z `/etc/fstab` a `/etc/crypttab` , kterou jste vytvořili.

![Snímek obrazovky zobrazující kód, který odstraní nový logický svazek. Odstraněná karta F S a karta kryptografie jsou zvýrazněné.](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

K dokončení čištění použijte následující postup:

1. Odpojte LV:

    ``` bash
    umount /mountpoint
    ```

1. Zavřete šifrovanou vrstvu svazku:

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. Odstraňte LV:

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>Rozšiřování tradičního LVM svazku změnou velikosti stávající PV

V některých případech im můžou vaše omezení vyžadovat změnu velikosti existujícího disku. Jak na to:

1. Identifikujte šifrované disky:

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![Snímek obrazovky zobrazující kód, který identifikuje šifrované disky. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![Snímek obrazovky se zobrazeným alternativním kódem, který identifikuje šifrované disky. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. Podívejte se na informace o PV:

    ``` bash
    pvs
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje informace o fyzickém svazku. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    Výsledky na obrázku ukazují, že se v současnosti používá veškeré místo ve všech PVs.

3. Podívejte se na informace o VG:

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje informace o skupině svazků Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. Ověřte velikosti disků. `fdisk` `lsblk` K vypsání velikosti jednotek můžete použít nebo.

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikosti disků. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    Tady jsme zjistili, které PVs jsou přidružené k tomu, které LVs pomocí `lsblk -fs` . Přidružení můžete identifikovat spuštěním `lvdisplay` .

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![Snímek obrazovky znázorňující alternativní způsob identifikace přidružení fyzických svazků k místním svazkům. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    V takovém případě jsou všechny čtyři datové jednotky součástí stejného VG a jedné LV. Vaše konfigurace se může lišit.

5. Ověřit aktuální využití systému souborů:

    ``` bash
    df -h /datalvm*
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje využití systému souborů. Příkaz a výsledky jsou zvýrazněné.](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. Změňte velikost datových disků podle pokynů v části [rozšíření spravovaného disku Azure](expand-disks.md#expand-an-azure-managed-disk). Můžete použít portál, rozhraní příkazového řádku nebo PowerShell.

    >[!IMPORTANT]
    >Když je virtuální počítač spuštěný, nemůžete změnit velikost virtuálních disků. Zrušte přidělení virtuálního počítače pro tento krok.

7. Spusťte virtuální počítač a ověřte nové velikosti pomocí `fdisk` .

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost disku. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    V tomto případě se `/dev/sdd` změnila velikost z 5 g na 20 g.

8. Ověřte aktuální velikost PV:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost P V. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    I když se změnila velikost disku, má souč_hod ještě předchozí velikost.

9. Změna velikosti PV:

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![Snímek obrazovky zobrazující kód, který mění velikost fyzického svazku. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. Ověřte velikost PV:

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost fyzického svazku. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    Použijte stejný postup pro všechny disky, u kterých chcete změnit velikost.

11. Podívejte se na informace o VG.

    ``` bash
    vgdisplay vgname
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje informace pro skupinu svazků Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    VG nyní má dostatek místa pro přidělení LVs.

12. Změňte velikost pole LV:

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![Snímek obrazovky zobrazující kód, který mění velikost L V. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. Ověřte velikost systému souborů:

    ``` bash
    df -h /datalvm2
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost systému souborů. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>Rozšíření svazku LVM-on-crypt přidáním nové PV

Přidáním nové PV můžete také roztáhnout svazek LVM na nešifrovaný svazek. Tato metoda úzce postupuje podle kroků v části [Konfigurace LVM a RAID na šifrovaných zařízeních](how-to-configure-lvm-raid-on-crypt.md#general-steps). Podívejte se na oddíly, které vysvětlují, jak přidat nový disk a nastavit ho v konfiguraci LVM-on-crypt.

Tuto metodu lze použít k přidání prostoru do existující aplikace LV. Nebo můžete vytvořit nové VGs nebo LVs.

1. Ověřte aktuální velikost svého VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost skupiny svazků Jsou zvýrazněny výsledky.](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. Ověřte velikost systému souborů a LV, které chcete rozšířit:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost místního svazku. Jsou zvýrazněny výsledky.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost systému souborů. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. Přidejte do virtuálního počítače nový datový disk a Identifikujte ho.

    Před přidáním nového disku ověřte disky:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost disků. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    Před přidáním nového disku je třeba ještě jednou ověřit disky:

    ``` bash
    lsblk
    ```

    ![Snímek obrazovky se zobrazeným alternativním kódem, který kontroluje velikost disků. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    Pokud chcete přidat nový disk, můžete použít PowerShell, rozhraní příkazového řádku Azure nebo Azure Portal. Další informace najdete v tématu [připojení datového disku k virtuálnímu počítači se systémem Linux](attach-disk-portal.md).

    Schéma názvů jádra se vztahuje na nově přidané zařízení. Nové jednotce se normálně přiřadí další dostupný znak. V tomto případě je přidaný disk `sdd` .

4. Zkontrolujte disky a ujistěte se, že byl přidán nový disk:

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![Snímek obrazovky zobrazující kód, který obsahuje seznam disků. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![Snímek obrazovky zobrazující nově přidaný disk ve výstupu](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. Na naposledy přidaném disku vytvořte systém souborů. Porovnejte disk s připojenými zařízeními `/dev/disk/azure/scsi1/` .

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![Snímek obrazovky zobrazující kód, který vytváří systém souborů. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![Snímek obrazovky zobrazující další kód, který vytvoří systém souborů a odpovídá disku na propojená zařízení. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. Vytvořte dočasný přípojný bod pro nově přidaný disk:

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. Přidejte do nástroje naposledy vytvořený systém souborů `/etc/fstab` .

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. Připojte nově vytvořený systém souborů:

    ``` bash
    mount -a
    ```

9. Ověřte, zda je nový systém souborů připojen:

    ``` bash
    df -h
    ```

    ![Snímek obrazovky zobrazující kód, který ověřuje, zda je systém souborů připojen. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![Snímek obrazovky zobrazující další kód, který ověřuje, zda je systém souborů připojen. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. Restartujte šifrování, které jste předtím spustili pro datové jednotky.

    >[!TIP]
    >Pro LVM-on-crypt doporučujeme, abyste používali `EncryptFormatAll` . V opačném případě se při nastavování dalších disků může zobrazit dvojité šifrování.
    >
    >Další informace najdete v tématu [Konfigurace LVM a RAID na šifrovaných zařízeních](how-to-configure-lvm-raid-on-crypt.md).

    Tady je příklad:

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

    Po dokončení šifrování se na nově přidaném disku zobrazí vrstva kryptografie:

    ``` bash
    lsblk
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje vrstvu crypt. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. Odpojte zašifrovanou vrstvu nového disku:

    ``` bash
    umount ${newmount}
    ```

12. Podívejte se na informace o aktuálním PV:

    ``` bash
    pvs
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje informace o fyzickém svazku. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. Vytvořte souč_hod na zašifrované vrstvě disku. Z předchozího příkazu Vezměte název zařízení `lsblk` . `/dev/`Chcete-li vytvořit PV, přidejte na začátek názvu zařízení Mapovač a vytvořte souč_hod:

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![Snímek obrazovky zobrazující kód, který vytváří fyzický svazek na šifrované vrstvě. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    Zobrazí se upozornění týkající se Vymazání aktuálního `ext4 fs` podpisu. Toto upozornění je očekávané. Odpovězte na tuto otázku pomocí `y` .

14. Ověřte, že se nová souč_hod přidala do konfigurace LVM:

    ``` bash
    pvs
    ```

    ![Snímek obrazovky zobrazující kód, který ověřuje, že byl do konfigurace LVM přidaný fyzický svazek. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. Přidejte novou souč_hod do VG, kterou potřebujete zvýšit.

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![Snímek obrazovky zobrazující kód, který přidá fyzický svazek do skupiny svazků. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. Ověřte novou velikost a volné místo VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Snímek obrazovky zobrazující kód, který ověřuje velikost a volné místo skupiny svazků. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    Všimněte si zvýšení `Total PE` počtu a `Free PE / Size` .

17. Zvětšete velikost souboru LV a systému souborů. Použijte `-r` možnost Zapnuto `lvextend` . V tomto příkladu přidáváme celkové dostupné místo v VG k danému LV.

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![Snímek obrazovky zobrazující kód, který zvětšuje velikost místního svazku a systému souborů. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

Postupujte podle dalších kroků a ověřte provedené změny.

1. Ověřte velikost části LV:

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![Snímek obrazovky zobrazující kód, který ověřuje novou velikost místního svazku. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. Ověřte novou velikost systému souborů:

    ``` bash
    df -h mountpoint
    ```

    ![Snímek obrazovky zobrazující kód, který ověřuje novou velikost systému souborů. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. Ověřte, že je vrstva LVM na zašifrované vrstvě:

    ``` bash
    lsblk
    ```

    ![Snímek obrazovky zobrazující kód, který ověřuje, zda je vrstva LVM na zašifrované vrstvě Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    Pokud použijete `lsblk` možnost bez možností, pak se přípojné body zobrazí několikrát. Příkaz seřadí podle zařízení a LVs. 

    Možná budete chtít použít `lsblk -fs` . V tomto příkazu `-fs` obrátí pořadí řazení tak, aby se přípojné body zobrazovaly jednou. Disky se zobrazují víckrát.

    ``` bash
    lsblk -fs
    ```

    ![Snímek obrazovky se zobrazeným alternativním kódem, který ověřuje, zda je vrstva LVM na zašifrované vrstvě. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>Rozšíří LVM na svazek s nešifrovaným použitím změny velikosti stávající PV.

1. Identifikujte šifrované disky:

    ``` bash
    lsblk
    ```

    ![Snímek obrazovky zobrazující kód, který identifikuje šifrované disky. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![Snímek obrazovky se zobrazeným alternativním kódem, který identifikuje šifrované disky. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. Podívejte se na informace o vaší PV:

    ``` bash
    pvs
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje informace pro fyzické svazky. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. Podívejte se na informace o VG:

    ``` bash
    vgs
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje informace pro skupiny svazků Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. Podívejte se na informace v aplikaci Lotyšsko:

    ``` bash
    lvs
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje informace pro místní svazek. Výsledek je zvýrazněný.](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. Podívejte se na využití systému souborů:

    ``` bash
    df -h /mountpoint(s)
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje, jak velká část systému souborů je používána. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. Ověřte velikosti svých disků:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost disků. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. Změňte velikost datového disku. Můžete použít portál, CLI nebo PowerShell. Další informace najdete v části Změna velikosti disku v tématu [rozšíření virtuálních pevných disků na virtuálním počítači se systémem Linux](expand-disks.md#expand-an-azure-managed-disk). 

    >[!IMPORTANT]
    >Když je virtuální počítač spuštěný, nemůžete změnit velikost virtuálních disků. Zrušte přidělení virtuálního počítače pro tento krok.

8. Ověřte velikosti disků:

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikosti disků. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    V takovém případě se velikost obou disků změnila z 2 GB na 4 GB. Velikost systému souborů, LV a PV ale zůstanou stejná.

9. Zkontroluje aktuální velikost PV. Mějte na paměti, že v LVM-on-crypt je to `/dev/mapper/` zařízení, ne `/dev/sd*` zařízení.

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost aktuálního fyzického svazku. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. Změna velikosti PV:

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![Snímek obrazovky zobrazující kód, který mění velikost fyzického svazku. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. Podívejte se na novou velikost PV:

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje velikost fyzického svazku. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. Změnit velikost šifrované vrstvy na SOUČHODNOTA:

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    Použijte stejný postup pro všechny disky, u kterých chcete změnit velikost.

13. Podívejte se na informace o VG:

    ``` bash
    vgdisplay vgname
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje informace pro skupinu svazků Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    VG nyní má dostatek místa pro přidělení LVs.

14. Podívejte se na informace v části LV:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje informace pro místní svazek. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. Podívejte se na využití systému souborů:

    ``` bash
    df -h /mountpoint
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje využití systému souborů. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. Změňte velikost pole LV:

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![Snímek obrazovky zobrazující kód, který mění velikost místního svazku. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    V tomto případě používáme `-r` k změně velikosti systému souborů taky možnost.

17. Podívejte se na informace v části LV:

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![Snímek obrazovky zobrazující kód, který získává informace o místním svazku. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. Podívejte se na využití systému souborů:

    ``` bash
    df -h /mountpoint
    ```

    ![Snímek obrazovky zobrazující kód, který kontroluje využití systému souborů. Výsledky jsou zvýrazněny.](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

Použijte stejný postup změny velikosti na jakékoli jiné LV, který to vyžaduje.

## <a name="next-steps"></a>Další kroky

[Řešení potíží s Azure Disk Encryption](disk-encryption-troubleshooting.md)
