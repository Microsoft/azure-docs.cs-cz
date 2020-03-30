---
title: Připojení k virtuálnímu počítači s Windows pomocí Azure Bastion
description: V tomto článku se dozvíte, jak se připojit k virtuálnímu počítači Azure se systémem Windows pomocí Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 6ec60dc313c8a4374637adf38ea0e5a7d4ed964b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77597336"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Připojení k virtuálnímu počítači s Windows pomocí Azure Bastion

Pomocí Azure Bastion se můžete bezpečně a bez problémů připojit k virtuálním počítačům přes SSL přímo na webu Azure Portal. Když používáte Azure Bastion, vaše virtuální počítače nevyžadují klienta, agenta nebo další software. V tomto článku se zobrazí postup připojení k virtuálním počítačům s Windows. Informace o připojení k virtuálnímu počítači s Linuxem najdete v [tématu Připojení k virtuálnímu počítači pomocí Azure Bastion - Linux](bastion-connect-vm-ssh.md).

Azure Bastion poskytuje zabezpečené připojení ke všem virtuálním počítačům ve virtuální síti, ve které se zřápějí. Použití Azure Bastion chrání vaše virtuální počítače před vystavením portů RDP/SSH vnějšímu světu a zároveň poskytuje tezi zabezpečený přístup pomocí RDP/SSH. Další informace najdete v tématu [Přehled](bastion-overview.md).

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že jste nastavili hostitele Azure Bastion pro virtuální síť, ve které je virtuální počítač umístěn. Jakmile se služba Bastion zřídí a nasadí ve vaší virtuální síti, můžete ji použít k připojení k libovolnému virtuálnímu počítači ve virtuální síti. Pokud chcete nastavit hostitele Azure Bastion, přečtěte si informace [o vytvoření hostitele Azure Bastion](bastion-create-host-portal.md).

### <a name="required-roles"></a>Požadované role

Chcete-li navázat spojení, jsou vyžadovány následující role:

* Role čtenáře na virtuálním počítači
* Role čtečky na nic s privátní IP adresou virtuálního počítače
* Role čtečky v prostředku Azure Bastion

### <a name="ports"></a>Porty

Chcete-li se připojit k virtuálnímu počítači s Windows, musíte mít na virtuálním počítači se systémem Windows otevřené následující porty:

* Příchozí porty: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Připojení

1. Otevřete [portál Azure](https://portal.azure.com). Přejděte na virtuální počítač, ke kterému se chcete připojit, klikněte na **Připojit** a v rozevíracím seznamové nabídce vyberte **Bašta.**

   ![Připojení virtuálního uživatele](./media/bastion-connect-vm-rdp/connect.png)
1. Po klepnutí na položku Bastion se zobrazí boční panel, který má tři karty – RDP, SSH a Bastion. Pokud byla bastion zřízena pro virtuální síť, karta Bastion je ve výchozím nastavení aktivní. Pokud jste baštu pro virtuální síť nezřizovali, můžete kliknutím na odkaz nakonfigurovat baštu. Pokyny ke konfiguraci naleznete v [tématu Konfigurace bašty](bastion-create-host-portal.md).

   ![karta bašta](./media/bastion-connect-vm-rdp/bastion.png)
1. Na kartě Bastion zadejte uživatelské jméno a heslo virtuálního počítače a klikněte na **Připojit**. Připojení RDP k tomuto virtuálnímu počítači přes Bastion se otevře přímo na portálu Azure (přes HTML5) pomocí portu 443 a služby Bastion.

   ![Připojení k prv](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k [baště](bastion-faq.md)
