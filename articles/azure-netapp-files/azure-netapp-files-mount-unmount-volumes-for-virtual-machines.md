---
title: Připojení svazků souborů Azure NetApp pro virtuální počítače
description: Přečtěte si, jak připojit nebo odpojit svazek pro virtuální počítače s Windows nebo virtuální počítače s Linuxem v Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 3457adf67fa067a154eef008b08d8cfcc1d9eaa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551535"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Připojování nebo odpojování svazku pro virtuální počítače s Windows nebo Linuxem 

Podle potřeby můžete připojit nebo odpojit svazek pro virtuální počítače se systémem Windows nebo Linux.  Pokyny pro připojení pro virtuální počítače s Linuxem jsou dostupné v azure netappových souborech.  

1. Klepněte na okno **Objemy** a vyberte hlasitost, na kterou chcete připojit. 
2. Klepněte na **tlačítko Připojit pokyny** z vybraného svazku a podle pokynů svazek připojte. 

    ![Pokyny k montáži NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Pokyny k montáži SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Pokud používáte systém Souborů NFSv4.1, připojte systém souborů pomocí následujícího příkazu:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Pokud chcete mít svazek systému souborů NFS automaticky připojen při spuštění nebo `/etc/fstab` restartování virtuálního počítače Azure, přidejte položku do souboru na hostiteli. 

    Například:`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`je IP adresa svazku souborů Azure NetApp nalezená v okně vlastností svazku.
    * `$FILEPATH`je cesta exportu svazku soubory Azure NetApp.
    * `$MOUNTPOINT`je adresář vytvořený na hostiteli Linuxu, který slouží k připojení exportu systému souborů NFS.

4. Chcete-li připojit svazek do systému Windows pomocí služby NFS:

    a. Nejprve připojte svazek na unixový nebo linuxový virtuální počítač.  
    b. `chmod 777` Spusťte `chmod 775` příkaz nebo příkaz proti svazku.  
    c. Připojte svazek prostřednictvím klienta systému NFS v systému Windows.

## <a name="next-steps"></a>Další kroky

* [Konfigurace výchozí domény NFSv4.1 pro Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Nejčastější dotazy k NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
