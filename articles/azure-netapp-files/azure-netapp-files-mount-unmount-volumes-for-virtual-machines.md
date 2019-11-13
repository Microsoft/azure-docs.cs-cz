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
ms.openlocfilehash: 4cd3bc7e4f95869d3efd2d92a7cdf1addc7ce5b2
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953100"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Připojování nebo odpojování svazku pro virtuální počítače s Windows nebo Linuxem 

V případě potřeby můžete připojit nebo odpojit svazek pro virtuální počítače se systémem Windows nebo Linux.  Pokyny k připojení pro virtuální počítače se systémem Linux jsou k dispozici na Azure NetApp Files.  

1. Klikněte na okno **svazky** a potom vyberte svazek, ke kterému se chcete připojit. 
2. Klikněte na **připojit pokyny** z vybraného svazku a pak podle pokynů připojte svazek. 

    ![Pokyny k připojení NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Pokyny k připojení SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
Pokud používáte NFSv 4.1, připojte systém souborů pomocí následujícího příkazu:  

`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

## <a name="next-steps"></a>Další kroky

* [Nakonfigurujte výchozí doménu NFSv 4.1 pro Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* Další informace najdete v tématu [Nejčastější dotazy k systému](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs) souborů NFS, které se automaticky připevní, když se spustí nebo restartuje virtuální počítač Azure.
