---
title: Připojení nebo odpojení Azure NetApp Filesho svazku pro virtuální počítače se systémem Windows nebo Linux | Microsoft Docs
description: Popisuje postup připojení nebo odpojení svazku pro virtuální počítače nebo virtuální počítače se systémem Linux.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: b-juche
ms.openlocfilehash: 84496fbc8a415171172d0a138f647ecb0310b6c7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173585"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Připojování nebo odpojování svazku pro virtuální počítače s Windows nebo Linuxem 

V případě potřeby můžete připojit nebo odpojit svazek pro virtuální počítače se systémem Windows nebo Linux.  Pokyny k připojení pro virtuální počítače se systémem Linux jsou k dispozici na Azure NetApp Files.  

1. Klikněte na okno **svazky** a potom vyberte svazek, ke kterému se chcete připojit. 
2. Klikněte na **připojit pokyny** z vybraného svazku a pak podle pokynů připojte svazek. 

    ![Pokyny k připojení NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Pokyny k připojení SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Pokud používáte NFSv 4.1, připojte systém souborů pomocí následujícího příkazu: `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Pokud chcete svazek systému souborů NFS automaticky připojit při spuštění nebo restartování virtuálního počítače Azure, přidejte položku do souboru `/etc/fstab` na hostiteli. 

    Například: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` je IP adresa Azure NetApp Filesho svazku, který se nachází v okně vlastností svazku.
    * `$FILEPATH` je cesta exportu Azure NetApp Files svazku.
    * `$MOUNTPOINT` je adresář vytvořený na hostiteli se systémem Linux, který slouží k připojení exportu systému souborů NFS.

## <a name="next-steps"></a>Další kroky

* [Nakonfigurujte výchozí doménu NFSv 4.1 pro Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Nejčastější dotazy k systému souborů NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
