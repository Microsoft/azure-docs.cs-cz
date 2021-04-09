---
title: Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu
description: V tomto článku se dozvíte, jak se připojit k virtuálnímu počítači Azure s Windows pomocí Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/21/2020
ms.author: cherylmc
ms.openlocfilehash: 708bd1f61da2f3973333f8e68cabdceee0717bee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92521526"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu

Pomocí Azure bastionu se můžete bezpečně a bez problémů připojit k virtuálním počítačům přes SSL přímo v Azure Portal. Pokud používáte Azure bastionu, vaše virtuální počítače nevyžadují klienta, agenta ani další software. V tomto článku se dozvíte, jak se připojit k virtuálním počítačům s Windows. Informace o připojení k virtuálnímu počítači se systémem Linux najdete v tématu [připojení k virtuálnímu počítači](bastion-connect-vm-ssh.md)se systémem Linux.

Azure bastionu poskytuje zabezpečené připojení ke všem virtuálním počítačům ve virtuální síti, ve které se zřídí. Použití Azure bastionu chrání vaše virtuální počítače před vystavení portů RDP/SSH na vnějším světě a zároveň zajišťuje zabezpečený přístup pomocí protokolu RDP/SSH. Další informace najdete v tématu [co je Azure bastionu?](bastion-overview.md).

## <a name="prerequisites"></a>Požadavky

Než začnete, ověřte, že jste splnili následující kritéria:

* Virtuální síť s hostitelem bastionu je už nainstalovaná.

   Ujistěte se, že jste nastavili hostitele Azure bastionu pro virtuální síť, ve které se virtuální počítač nachází. Jakmile se služba bastionu zřídí a nasadí ve vaší virtuální síti, můžete ji použít pro připojení k libovolnému virtuálnímu počítači ve virtuální síti. Pokud chcete nastavit hostitele Azure bastionu, přečtěte si téma [Vytvoření hostitele bastionu](tutorial-create-host-portal.md#createhost).
* Virtuální počítač s Windows ve virtuální síti.
* Následující požadované role:
  * Role čtenáře na virtuálním počítači.
  * Role čtecího zařízení na síťové kartě s privátní IP adresou virtuálního počítače
  * Role čtecího modulu v prostředku Azure bastionu
* Porty: pro připojení k virtuálnímu počítači s Windows musíte na svém VIRTUÁLNÍm počítači s Windows otevřít následující porty:
  * Příchozí porty: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Síti

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md).