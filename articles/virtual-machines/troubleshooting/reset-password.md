---
title: Jak resetovat heslo místního systému Linux na virtuálních počítačích Azure | Dokumentace Microsoftu
description: Zavést kroky pro resetování hesla místního systému Linux na virtuálním počítači Azure
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: d96d75f4f2623476f7af4e6eea930c1f2c503e3a
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51226909"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Jak resetovat heslo místního systému Linux na virtuálních počítačích Azure

Tento článek představuje několik metod, jak resetovat hesla místního virtuálního počítače (virtuální počítač s Linuxem). Pokud vypršela platnost uživatelského účtu nebo jenom chcete vytvořit nový účet, můžete použít následující metody k vytvoření nového účtu místního správce a znovu získat přístup k virtuálnímu počítači.

## <a name="symptoms"></a>Příznaky

Se nemůže přihlásit k virtuálnímu počítači a zobrazí se zpráva, která určuje, že je nesprávné heslo, které jste použili. Kromě toho nelze použít VMAgent k resetování hesla na portálu Azure portal.

## <a name="manual-password-reset-procedure"></a>Postup vytvoření nového ruční hesla

1.  Odstranění virtuálního počítače a zachovat připojených disků.

2.  Připojte jednotku operačního systému jako datový disk k jinému virtuálnímu počítači dočasné ve stejném umístění.

3.  Spusťte následující příkaz SSH na dočasný virtuální počítač stane superuživatel.

    ```bash
    sudo su
    ```

4.  Spustit **fdisk -l** se také podívat na protokoly systému a hledat nově připojený disk. Vyhledejte název jednotky pro připojení. Potom na dočasný virtuální počítač, vyhledejte v příslušných protokolových souborů.

    ```bash
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ```

    Následuje příklad výstupu z příkazu grep:

    ```bash
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ```

5.  Vytvořit bod připojení volat **tempmount**.

    ```bash
    mkdir /tempmount
    ```

6.  Připojte disk s operačním systémem na přípojný bod. Obvykle bude potřeba připojit *sdc1* nebo *sdc2*. To bude záviset na hostování oddílu v */etc* z disku počítače poškozený.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Vytvoření kopie základní souborů přihlašovacích údajů před provedením jakýchkoli změn:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Resetovat heslo uživatele, které potřebujete:

    ```bash
    passwd <<USER>> 
    ```

9.  Přesuňte změněné soubory do správného umístění na disku počítače poškozený.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Přejděte zpět do kořenového adresáře a odpojit disk.

    ```bash
    cd /
    umount /tempmount
    ```

11. Odpojení disku z portálu pro správu.

12. Znovu vytvořte virtuální počítač.

## <a name="next-steps"></a>Další postup

* [Řešení potíží s virtuálním počítači Azure připojením disku s operačním systémem k jinému virtuálnímu počítači Azure](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: Jak odstranit a znovu nasadit virtuální počítač z virtuálního pevného disku](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
