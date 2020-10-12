---
title: Jak se připojit k Azure Lab Servicesmu virtuálnímu počítači z Chromebook | Microsoft Docs
description: Přečtěte si, jak se připojit z Chromebook k virtuálnímu počítači v Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: d2a936a6acb6491c57cb1460c83ec0253561b102
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85445657"
---
# <a name="connect-to-a-vm-using-remote-desktop-protocol-on-a-chromebook"></a>Připojení k virtuálnímu počítači pomocí protokol RDP (Remote Desktop Protocol) Chromebook
V této části se dozvíte, jak se student může připojit k virtuálnímu počítači učeben Labs z Chromebook pomocí protokolu RDP.

## <a name="install-microsoft-remote-desktop-on-a-chromebook"></a>Instalace Vzdálená plocha Microsoft na Chromebook
1. Otevřete App Store na Chromebook a vyhledejte **Vzdálená plocha Microsoft**.

    ![Vzdálená plocha Microsoft](./media/how-to-use-classroom-lab/install-ms-remote-desktop-chromebook.png)
1. Nainstalujte nejnovější verzi Vzdálená plocha Microsoft. 

## <a name="access-the-vm-from-your-chromebook-using-rdp"></a>Přístup k virtuálnímu počítači z Chromebook pomocí protokolu RDP
1. Otevřete soubor **RDP** , který je stažený na počítači s nainstalovaným **Vzdálená plocha Microsoft** . Mělo by se začít připojovat k virtuálnímu počítači. 

    ![Připojení k virtuálnímu počítači](./media/how-to-use-classroom-lab/connect-vm-chromebook.png)

1. Po zobrazení výzvy zadejte svoje heslo.
    ![Připojení k virtuálnímu počítači](./media/how-to-use-classroom-lab/password-chromebook.png)


1. Pokud se zobrazí následující upozornění, vyberte **pokračovat** . 

    ![Upozornění certifikátu](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Měli byste vidět plochu virtuálního počítače, ke kterému se připojujete.

## <a name="next-steps"></a>Další kroky
Další informace o připojení k virtuálním počítačům se systémem Linux najdete v tématu [připojení k virtuálním počítačům](how-to-use-remote-desktop-linux-student.md) se systémem Linux.


