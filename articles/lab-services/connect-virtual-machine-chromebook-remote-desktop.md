---
title: Jak se připojit k Azure Lab Servicesmu virtuálnímu počítači z Chromebook | Microsoft Docs
description: Přečtěte si, jak se připojit z Chromebook k virtuálnímu počítači v Azure Lab Services.
services: devtest-lab, lab-services, virtual-machines
author: nicolela
ms.topic: article
ms.date: 06/26/2020
ms.author: nicolela
ms.openlocfilehash: 74135c0b36f533ebfbba6422bc79af47825a1a3b
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97813220"
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

    ![Snímek obrazovky zobrazující přihlašovací obrazovku, kde můžete zadat uživatelské jméno a heslo.](./media/how-to-use-classroom-lab/password-chromebook.png)

1. Pokud se zobrazí následující upozornění, vyberte **pokračovat** . 

    ![Upozornění certifikátu](./media/how-to-use-classroom-lab/certificate-error-chromebook.png)

1. Měli byste vidět plochu virtuálního počítače, ke kterému se připojujete.

## <a name="next-steps"></a>Další kroky

Další informace o připojení k virtuálním počítačům se systémem Linux najdete v tématu [připojení k virtuálním počítačům](how-to-use-remote-desktop-linux-student.md) se systémem Linux.

