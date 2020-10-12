---
title: Resetování hesla místního systému Linux na virtuálních počítačích Azure | Microsoft Docs
description: Zavedení kroků pro resetování hesla místního systému Linux na virtuálním počítači Azure
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
ms.openlocfilehash: c6bfd5b9ff3626593916533f27c5c2755cebcb13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87028477"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Jak resetovat místní linuxové heslo ve virtuálních počítačích Azure

Tento článek představuje několik způsobů, jak obnovit hesla místních virtuálních počítačů (VM) místního systému Linux. Pokud vypršela platnost uživatelského účtu nebo chcete vytvořit nový účet, můžete pomocí následujících metod vytvořit nový účet místního správce a znovu získat přístup k virtuálnímu počítači.

## <a name="symptoms"></a>Příznaky

K virtuálnímu počítači se nemůžete přihlásit a zobrazí se zpráva s oznámením, že heslo, které jste použili, je nesprávné. Kromě toho nemůžete použít VMAgent k resetování hesla na Azure Portal.

## <a name="manual-password-reset-procedure"></a>Postup ručního resetování hesla

> [!NOTE]
> Následující kroky se nevztahují na virtuální počítač s nespravovaným diskem.

1. Položte si snímek disku s operačním systémem ovlivněného virtuálního počítače, vytvořte disk ze snímku a pak ho připojte k virtuálnímu počítači pro odstraňování potíží. Další informace najdete v tématu [řešení potíží s virtuálním počítačem s Windows připojením disku s operačním systémem k virtuálnímu počítači pro obnovení pomocí Azure Portal](troubleshoot-recovery-disks-portal-linux.md).

2. Připojte se k virtuálnímu počítači pro řešení potíží pomocí vzdálené plochy.

3.  Spusťte následující příkaz SSH na virtuálním počítači pro řešení potíží, abyste se stali SuperM uživatelem.

    ```bash
    sudo su
    ```

4.  Spusťte **příkaz fdisk-l** nebo vyhledejte v systémových protokolech, abyste našli nově připojený disk. Vyhledejte název jednotky, který chcete připojit. Pak na dočasném virtuálním počítači vyhledejte příslušný soubor protokolu.

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

6.  Připojte disk s operačním systémem na přípojný bod. Obvykle musíte připojit *sdc1* nebo *sdc2*. To bude záviset na oddílu hostování v adresáři */etc* z poškozeného disku počítače.

    ```bash
    mount /dev/sdc1 /tempmount
    ```

7.  Před provedením změn vytvořte kopie souborů základních přihlašovacích údajů:

    ```bash
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ```

8.  Resetujte heslo uživatele, které potřebujete:

    ```bash
    passwd <<USER>> 
    ```

9.  Přesuňte změněné soubory do správného umístění na disku poškozeného počítače.

    ```bash
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ```

10. Vraťte se do kořenového adresáře a odpojte disk.

    ```bash
    cd /
    umount /tempmount
    ```

11. V Azure Portal odpojte disk od virtuálního počítače pro řešení potíží.

12. [Změňte disk s operačním systémem pro ovlivněný virtuální počítač](troubleshoot-recovery-disks-portal-linux.md#swap-the-os-disk-for-the-vm).

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s virtuálním počítačem Azure připojením disku s operačním systémem k jinému virtuálnímu počítači Azure](https://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: Jak odstranit a znovu nasadit virtuální počítač z virtuálního pevného disku](/archive/blogs/linuxonazure/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd)
