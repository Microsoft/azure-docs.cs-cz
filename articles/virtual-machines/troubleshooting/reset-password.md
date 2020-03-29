---
title: Jak resetovat místní heslo k Linuxu na virtuálních počítačích Azure | Dokumenty společnosti Microsoft
description: Zavedení kroků k resetování místního hesla k Linuxu na virtuálním počítači Azure
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: delhan
ms.openlocfilehash: 83751538efe4f3d3af5928caa04b265b6c867442
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71153579"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Jak resetovat místní linuxové heslo ve virtuálních počítačích Azure

Tento článek zavádí několik metod resetování místních hesel virtuálního počítače (VM) systému Linux. Pokud vypršela platnost uživatelského účtu nebo chcete vytvořit nový účet, můžete pomocí následujících metod vytvořit nový účet místního správce a znovu získat přístup k virtuálnímu virtuálnímu účtu.

## <a name="symptoms"></a>Příznaky

Nemůžete se přihlásit k virtuálnímu počítače a zobrazí se zpráva, že heslo, které jste použili, je nesprávné. Kromě toho nelze použít VMAgent k resetování hesla na webu Azure Portal.

## <a name="manual-password-reset-procedure"></a>Ruční postup resetování hesla

> [!NOTE]
> Následující kroky se nevztahují na virtuální počítače s nespravovaným diskem.

1. Pořiďte snímek pro disk operačního systému postiženého virtuálního počítače, vytvořte disk ze snímku a pak připojte disk k virtuálnímu virtuálnímu počítače pro řešení potíží. Další informace najdete [v tématu Poradce při potížích s virtuálním počítačem s Windows připojením disku operačního systému k virtuálnímu počítači pro obnovení pomocí portálu Azure](troubleshoot-recovery-disks-portal-linux.md).

2. Připojte se k virtuálnímu počítači pro řešení potíží pomocí vzdálené plochy.

3.  Spusťte následující příkaz SSH na řešení potíží virtuálního počítači, aby se stal super-uživatele.

    ```bash
    sudo su
    ```

4.  Spusťte **fdisk -l** nebo se podívejte na systémové protokoly a najděte nově připojený disk. Vyhledejte název jednotky, kterou chcete připojit. Potom na dočasném virtuálním počítači, podívejte se do příslušného souboru protokolu.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Následuje příklad výstupu příkazu grep:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Vytvořte přípojný bod s názvem **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Připojte disk operačního systému na přípojné místo. Obvykle je třeba připojit *sdc1* nebo *sdc2*. To bude záviset na hostitelském oddílu v adresáři */etc* z poškozeného počítače.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Před provedením jakýchkoli změn vytvořte kopie základních souborů pověření:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Obnovte heslo uživatele, které potřebujete:

    ```bash
    passwd <<USER>> 
    ```

9.  Přesuňte upravené soubory do správného umístění na disku poškozeného počítače.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Vraťte se ke kořenovému adresáři a odpojte disk.

    ```bash
    cd /
    umount /tempmount
    ```

11. Na webu Azure Portal odpojte disk od virtuálního počítače pro řešení potíží.

12. [Změňte disk operačního systému pro ohrožený virtuální modul](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>Další kroky

* [Poradce při potížích s virtuálním počítačem Azure připojením disku operačního systému k jinému virtuálnímu počítači Azure](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: Jak odstranit a znovu nasadit virtuální počítač z virtuálního počítače](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
