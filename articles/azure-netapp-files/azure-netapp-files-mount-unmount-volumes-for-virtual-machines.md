---
title: Připojení Azure NetApp Files svazků pro virtuální počítače
description: Zjistěte, jak připojit nebo odpojit svazek pro virtuální počítače s Windows nebo pro virtuální počítače se systémem Linux v Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 83d6e051f520737e750e6c46c192eb698e7bf0e3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94842253"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Připojování nebo odpojování svazku pro virtuální počítače s Windows nebo Linuxem 

V případě potřeby můžete připojit nebo odpojit svazek pro virtuální počítače se systémem Windows nebo Linux.  Pokyny k připojení pro virtuální počítače se systémem Linux jsou k dispozici na Azure NetApp Files.  

## <a name="requirements"></a>Požadavky 

* Aby bylo možné získat přístup ke svazku systému souborů NFS, musíte mít aspoň jednu zásadu exportu.
* Chcete-li úspěšně připojit svazek systému souborů NFS, zajistěte, aby byly mezi klientem a svazky systému souborů NFS otevřeny následující porty systému souborů NFS:
    * 111 TCP/UDP = `RPCBIND/Portmapper`
    * 635 TCP/UDP = `mountd`
    * 2049 TCP/UDP = `nfs`
    * 4045 TCP/UDP = `nlockmgr` (jenom NFSv3)
    * 4046 TCP/UDP = `status` (jenom NFSv3)

## <a name="steps"></a>Postup

1. Klikněte na okno **svazky** a potom vyberte svazek, ke kterému se chcete připojit. 
2. Klikněte na **připojit pokyny** z vybraného svazku a pak podle pokynů připojte svazek. 

    ![Pokyny k připojení NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Pokyny k připojení SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * Pokud připojujete svazek systému souborů NFS, nezapomeňte použít `vers` možnost v `mount` příkazu k určení verze protokolu NFS, která odpovídá svazku, který chcete připojit. 
    * Pokud používáte NFSv 4.1, připojte systém souborů pomocí následujícího příkazu:  `sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > Pokud používáte NFSv 4.1, ujistěte se, že všechny virtuální počítače, které namontují export, používají jedinečné názvy hostitelů.

3. Pokud chcete připojit svazek systému souborů NFS automaticky při spuštění nebo restartování virtuálního počítače Azure, přidejte položku do `/etc/fstab` souboru na hostiteli. 

    Například:  `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` je IP adresa Azure NetApp Filesho svazku, který se nachází v okně vlastností svazku.
    * `$FILEPATH` je cesta exportu Azure NetApp Filesho svazku.
    * `$MOUNTPOINT` je adresář vytvořený na hostiteli systému Linux, který slouží k připojení exportu NFS.

4. Pokud chcete připojit svazek k systému Windows pomocí systému souborů NFS:

    a. Nejdřív připojte svazek k virtuálnímu počítači se systémem UNIX nebo Linux.  
    b. Spusťte `chmod 777` příkaz nebo pro `chmod 775` svazek.  
    c. Připojte svazek přes klienta NFS ve Windows.
    
5. Pokud chcete připojit svazek protokolu Kerberos systému souborů NFS, přečtěte si další podrobnosti v tématu [Konfigurace šifrování protokolu Kerberos nfsv 4.1](configure-kerberos-encryption.md) . 

## <a name="next-steps"></a>Další kroky

* [Konfigurace výchozí domény NFSv4.1 pro Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Nejčastější dotazy k systému souborů NFS](./azure-netapp-files-faqs.md#nfs-faqs)
* [Přehled systému souborů sítě](/windows-server/storage/nfs/nfs-overview)
* [Připojení svazku protokolu Kerberos systému souborů NFS](configure-kerberos-encryption.md#kerberos_mount)
