---
title: Jak obnovit heslo místního Linux na virtuálních počítačích Azure | Microsoft Docs
description: Zavést postup obnovit heslo místního Linux na virtuálním počítači Azure
services: virtual-machines-linux
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: delhan
ms.openlocfilehash: b9182ec2a974de06c2bd45928b9964f253653bf6
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/03/2018
ms.locfileid: "27578358"
---
# <a name="how-to-reset-local-linux-password-on-azure-vms"></a>Jak obnovit heslo místního Linux na virtuálních počítačích Azure

Tento článek představuje několik metod k resetování hesel místní systém Linux virtuálního počítače (VM). Pokud vypršela platnost uživatelského účtu nebo jenom chcete vytvořit nový účet, můžete použít následující metody vytvořit nový účet místního správce a znovu získat přístup k virtuálnímu počítači.

## <a name="symptoms"></a>Příznaky

Se nemůže přihlásit k virtuálnímu počítači a obdržíte zprávu, která označuje, že je nesprávné heslo, které jste použili. Kromě toho nelze použít VMAgent k resetování hesla na portálu Azure. 

## <a name="manual-password-reset-procedure"></a>Ruční postup vytvoření nového hesla

1.  Odstraňte virtuální počítač a zachovat připojených disků.

2.  Připojte jednotku operačního systému jako datový disk k jiné dočasné virtuálních počítačů ve stejném umístění.

3.  Pomocí následujícího příkazu SSH na dočasný virtuální počítač stane nadtypem uživatele.


    ~~~~
    sudo su
    ~~~~

4.  Spustit **fdisk -l** nebo v protokolech systému najít nově připojený disk. Vyhledejte název jednotky připojit. Pak na dočasný virtuální počítač, zkontrolujte soubor protokolu relevantní.

    ~~~~
    grep SCSI /var/log/kern.log (ubuntu)
    grep SCSI /var/log/messages (centos, suse, oracle)
    ~~~~

    Následuje příklad výstupu příkazu grep:

    ~~~~
    kernel: [ 9707.100572] sd 3:0:0:0: [sdc] Attached SCSI disk
    ~~~~

5.  Vytvořit bod připojení s názvem **tempmount**.

    ~~~~
    mkdir /tempmount
    ~~~~

6.  Připojte disk operačního systému na přípojného bodu. Obvykle potřebujete připojit sdc1 nebo sdc2. To bude záviset na hostování oddílu v adresáři/etc z disku porušený počítače.

    ~~~~
    mount /dev/sdc1 /tempmount
    ~~~~

7.  Proveďte zálohu před provedením jakýchkoli změn:

    ~~~~
    cp /etc/passwd /etc/passwd_orig    
    cp /etc/shadow /etc/shadow_orig    
    cp /tempmount/etc/passwd /etc/passwd
    cp /tempmount/etc/shadow /etc/shadow 
    cp /tempmount/etc/passwd /tempmount/etc/passwd_orig
    cp /tempmount/etc/shadow /tempmount/etc/shadow_orig
    ~~~~

8.  Resetovat heslo uživatele, které budete potřebovat:

    ~~~~
    passwd <<USER>> 
    ~~~~

9.  Přesuňte změněné soubory do správného umístění na disku porušený počítače.

    ~~~~
    cp /etc/passwd /tempmount/etc/passwd
    cp /etc/shadow /tempmount/etc/shadow
    cp /etc/passwd_orig /etc/passwd
    cp /etc/shadow_orig /etc/shadow
    ~~~~

10. Přejděte zpět do kořenového adresáře a odpojit disk.

    ~~~~
    cd /
    umount /tempmount
    ~~~~

11. Odpojte disk z portálu pro správu.

12. Znovu vytvořte virtuální počítač.

## <a name="next-steps"></a>Další postup

* [Řešení potíží s virtuální počítač Azure podle operačního systému disk se připojuje k jiným virtuálním Počítačem Azure](http://social.technet.microsoft.com/wiki/contents/articles/18710.troubleshoot-azure-vm-by-attaching-os-disk-to-another-azure-vm.aspx)

* [Azure CLI: Jak odstranit a znovu nasadit virtuální počítač z virtuálního pevného disku](https://blogs.msdn.microsoft.com/linuxonazure/2016/07/21/azure-cli-how-to-delete-and-re-deploy-a-vm-from-vhd/)
