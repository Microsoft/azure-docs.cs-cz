---
title: Jak se připojit k virtuálnímu počítači Azure Lab Services z Macu | Dokumenty společnosti Microsoft
description: Tento článek
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2020
ms.author: spelluru
ms.openlocfilehash: 0d3484c1008e00bcfde3adb399e925c0e054f49e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79503085"
---
# <a name="connect-to-a-vm-using-rdp-on-a-mac"></a>Připojení k virtuálnímu počítači pomocí rdp na Macu
Tato část ukazuje, jak se student může připojit k virtuálnímu počítači v laboratoři ve třídě z Macu pomocí RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Instalace vzdálené plochy Microsoft Na Mac
1. Otevřete Na Macu App Store a vyhledejte **microsoft remote desktop**.

    ![Vzdálená plocha Microsoft](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Nainstalujte nejnovější verzi vzdálené plochy microsoft. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Přístup k virtuálnímu počítači z Macu pomocí RDP
1. Otevřete soubor **RDP** stažený do počítače s **nainstalovanou vzdálenou plochou Microsoft.** Měl by se začít připojovat k virtuálnímu virtuálnímu zařízení. 

    ![Připojení k virtuálnímu počítači](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Pokud se zobrazí následující upozornění, vyberte **pokračovat.** 

    ![Upozornění na certifikát](../media/how-to-use-classroom-lab/certificate-error.png)
1. Měli byste vidět virtuální ho dispozičně. 

    > [!NOTE]
    > Následující příklad je pro virtuální počítač CentOS Linux. 

    ![Virtuální počítač](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Další kroky
Informace o připojení k virtuálním počítačům SIP pomocí rdp najdete v tématu [Použití vzdálené plochy pro virtuální počítače s Linuxem.](how-to-use-remote-desktop-linux-student.md)


