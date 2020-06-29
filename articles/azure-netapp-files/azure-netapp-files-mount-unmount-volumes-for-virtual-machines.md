---
title: Připojení Azure NetApp Files svazků pro virtuální počítače
description: Zjistěte, jak připojit nebo odpojit svazek pro virtuální počítače s Windows nebo pro virtuální počítače se systémem Linux v Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 04/30/2020
ms.openlocfilehash: b7586fcd0a262fa325e171480ec0becc297f65fe
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483580"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Připojování nebo odpojování svazku pro virtuální počítače s Windows nebo Linuxem 

V případě potřeby můžete připojit nebo odpojit svazek pro virtuální počítače se systémem Windows nebo Linux.  Pokyny k připojení pro virtuální počítače se systémem Linux jsou k dispozici na Azure NetApp Files.  

> [!IMPORTANT] 
> Aby bylo možné získat přístup ke svazku systému souborů NFS, musíte mít aspoň jednu zásadu exportu.

1. Klikněte na okno **svazky** a potom vyberte svazek, ke kterému se chcete připojit. 
2. Klikněte na **připojit pokyny** z vybraného svazku a pak podle pokynů připojte svazek. 

    ![Pokyny k připojení NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Pokyny k připojení SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Pokud používáte NFSv 4.1, připojte systém souborů pomocí následujícího příkazu:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Pokud chcete připojit svazek systému souborů NFS automaticky při spuštění nebo restartování virtuálního počítače Azure, přidejte položku do `/etc/fstab` souboru na hostiteli. 

    Například:`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`je IP adresa Azure NetApp Filesho svazku, který se nachází v okně vlastností svazku.
    * `$FILEPATH`je cesta exportu Azure NetApp Filesho svazku.
    * `$MOUNTPOINT`je adresář vytvořený na hostiteli systému Linux, který slouží k připojení exportu NFS.

4. Pokud chcete připojit svazek k systému Windows pomocí systému souborů NFS:

    a. Nejdřív připojte svazek k virtuálnímu počítači se systémem UNIX nebo Linux.  
    b. Spusťte `chmod 777` příkaz nebo pro `chmod 775` svazek.  
    c. Připojte svazek přes klienta NFS ve Windows.

## <a name="next-steps"></a>Další kroky

* [Konfigurace výchozí domény NFSv4.1 pro Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Nejčastější dotazy k systému souborů NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Přehled systému souborů sítě](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
