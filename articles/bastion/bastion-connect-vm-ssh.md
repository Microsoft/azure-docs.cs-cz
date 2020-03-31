---
title: Připojení k virtuálnímu počítači SUP pomocí Azure Bastion
description: V tomto článku se dozvíte, jak se připojit k virtuálnímu počítači Linux pomocí Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 381f45a51002188c72174cff83c26b829912a0b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596823"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion"></a>Připojení pomocí SSH k virtuálnímu počítači Linuxu pomocí Azure Bastion

V tomto článku se ukazuje, jak bezpečně a bezproblémově SSH na vaše virtuální počítače s Linuxem ve virtuální síti Azure. K virtuálnímu počítači se můžete připojit přímo z webu Azure Portal. Při použití Azure Bastion virtuální počítače nevyžadují klienta, agenta nebo další software. Další informace o Azure Bastion najdete v tématu [Přehled](bastion-overview.md).

Azure Bastion můžete použít k připojení k virtuálnímu počítači Linuxpomocí SSH. Pro ověřování můžete použít uživatelské jméno/heslo i klíče SSH. K virtuálnímu počítači se můžete připojit pomocí kláves SSH pomocí:

* Soukromý klíč, který zadáte ručně
* Soubor, který obsahuje informace o soukromém klíči

Soukromý klíč SSH musí být ve formátu, který začíná `"-----BEGIN RSA PRIVATE KEY-----"` a končí . `"-----END RSA PRIVATE KEY-----"`

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že jste nastavili hostitele Azure Bastion pro virtuální síť, ve které se virtuální počítač nachází. Další informace najdete [v tématu Vytvoření hostitele Bašty Azure](bastion-create-host-portal.md). Jakmile je služba Bastion zřízená a nasazená ve vaší virtuální síti, můžete ji použít k připojení k libovolnému virtuálnímu počítači v této virtuální síti. 

Při použití Bastion pro připojení, předpokládá, že používáte RDP pro připojení k virtuálnímu počítači se systémem Windows a SSH pro připojení k virtuálním počítačům SLinux. Informace o připojení k virtuálnímu virtuálnímu virtuálnímu virtuálnímu zařízení s Windows najdete [v tématu Připojení k virtuálnímu virtuálnímu virtuálnímu zařízení – Windows](bastion-connect-vm-rdp.md).

### <a name="required-roles"></a>Požadované role

Chcete-li navázat spojení, jsou vyžadovány následující role:

* Role čtenáře na virtuálním počítači
* Role čtečky na nic s privátní IP adresou virtuálního počítače
* Role čtečky v prostředku Azure Bastion

### <a name="ports"></a>Porty

Chcete-li se připojit k virtuálnímu počítači SM s Linuxem přes SSH, musíte mít na virtuálním počítači otevřené následující porty:

* Vstupní port: SSH (22)

## <a name="connect-using-username-and-password"></a><a name="username"></a>Připojit: Použití uživatelského jména a hesla

1. Otevřete [portál Azure](https://portal.azure.com). Přejděte na virtuální počítač, ke kterému se chcete připojit, klikněte na **Připojit** a v rozevíracím seznamové nabídce vyberte **Bašta.**

   ![Připojení](./media/bastion-connect-vm-ssh/connect.png)
1. Po klepnutí na položku Bastion se zobrazí boční panel, který má tři karty – RDP, SSH a Bastion. Pokud byla bastion zřízena pro virtuální síť, karta Bastion je ve výchozím nastavení aktivní. Pokud jste baštu pro virtuální síť nezřizovali, přečtěte si to v [tématu Konfigurace bašty](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Zadejte uživatelské jméno a heslo pro SSH do vašeho virtuálního počítače.
1. Po zadání klávesy klikněte na **tlačítko Připojit.**

## <a name="connect-manually-enter-a-private-key"></a><a name="privatekey"></a>Připojit: Ruční zadání soukromého klíče

1. Otevřete [portál Azure](https://portal.azure.com). Přejděte na virtuální počítač, ke kterému se chcete připojit, klikněte na **Připojit** a v rozevíracím seznamové nabídce vyberte **Bašta.**

   ![Připojení](./media/bastion-connect-vm-ssh/connect.png)
1. Po klepnutí na položku Bastion se zobrazí boční panel, který má tři karty – RDP, SSH a Bastion. Pokud byla bastion zřízena pro virtuální síť, karta Bastion je ve výchozím nastavení aktivní. Pokud jste baštu pro virtuální síť nezřizovali, přečtěte si to v [tématu Konfigurace bašty](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Zadejte uživatelské jméno a vyberte **položku SSH Private Key**.
1. Zadejte svůj soukromý klíč do textové oblasti **SSH Private Key** (nebo jej vložte přímo).
1. Po zadání klávesy klikněte na **tlačítko Připojit.**

## <a name="connect-using-a-private-key-file"></a><a name="ssh"></a>Připojit: Použití souboru soukromého klíče

1. Otevřete [portál Azure](https://portal.azure.com). Přejděte na virtuální počítač, ke kterému se chcete připojit, klikněte na **Připojit** a v rozevíracím seznamové nabídce vyberte **Bašta.**

   ![Připojení](./media/bastion-connect-vm-ssh/connect.png)
1. Po klepnutí na položku Bastion se zobrazí boční panel, který má tři karty – RDP, SSH a Bastion. Pokud byla bastion zřízena pro virtuální síť, karta Bastion je ve výchozím nastavení aktivní. Pokud jste baštu pro virtuální síť nezřizovali, přečtěte si to v [tématu Konfigurace bašty](bastion-create-host-portal.md).

   ![Bastion](./media/bastion-connect-vm-ssh/bastion.png)
1. Zadejte uživatelské jméno a **z místního souboru**vyberte soukromý klíč SSH .
1. Klepněte na tlačítko **Procházet** (ikona složky v místním souboru).
1. Vyhledejte soubor a klepněte na tlačítko **Otevřít**.
1. Kliknutím na **Připojit** se připojte k virtuálnímu virtuálnímu zařízení. Po kliknutí na připojit, SSH do tohoto virtuálního počítače se přímo otevře na webu Azure Portal. Toto připojení je přes HTML5 pomocí portu 443 ve službě Bastion přes privátní IP vašeho virtuálního počítače.

## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k [baště](bastion-faq.md)
