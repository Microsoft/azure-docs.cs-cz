---
title: Jak se připojit k virtuálnímu počítači s Azure Lab Services z Mac | Microsoft Docs
description: Přečtěte si, jak se připojit z počítače Mac k virtuálnímu počítači v Azure Lab Services.
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
ms.openlocfilehash: 9087e4fb7989f89c19a70afd9ee8f061f9061166
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2020
ms.locfileid: "83704461"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-mac"></a>Připojení k virtuálnímu počítači pomocí protokol RDP (Remote Desktop Protocol) na Macu
V této části se dozvíte, jak se student může připojit k virtuálnímu počítači učeben Labs z Mac pomocí protokolu RDP.

## <a name="install-microsoft-remote-desktop-on-a-mac"></a>Instalace Vzdálená plocha Microsoft na Macu
1. Otevřete App Store na Macu a vyhledejte **Vzdálená plocha Microsoft**.

    ![Vzdálená plocha Microsoft](../media/how-to-use-classroom-lab/install-ms-remote-desktop.png)
1. Nainstalujte nejnovější verzi Vzdálená plocha Microsoft. 

## <a name="access-the-vm-from-your-mac-using-rdp"></a>Přístup k virtuálnímu počítači z Mac pomocí protokolu RDP
1. Otevřete soubor **RDP** , který je stažený na počítači s nainstalovaným **Vzdálená plocha Microsoft** . Mělo by se začít připojovat k virtuálnímu počítači. 

    ![Připojení k virtuálnímu počítači](../media/how-to-use-classroom-lab/connect-linux-vm.png)
1. Pokud se zobrazí následující upozornění, vyberte **pokračovat** . 

    ![Upozornění certifikátu](../media/how-to-use-classroom-lab/certificate-error.png)
1. Měl by se zobrazit virtuální počítač. 

    > [!NOTE]
    > Následující příklad je pro virtuální počítač s CentOS Linux. 

    ![Virtuální počítač](../media/how-to-use-classroom-lab/vm-ui.png)


## <a name="next-steps"></a>Další kroky
Informace o připojení k virtuálním počítačům se systémem Linux pomocí protokolu RDP najdete v tématu [použití vzdálené plochy pro virtuální počítače se systémem Linux](how-to-use-remote-desktop-linux-student.md) .


