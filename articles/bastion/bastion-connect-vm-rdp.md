---
title: Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu
description: V tomto článku se dozvíte, jak se připojit k virtuálnímu počítači Azure s Windows pomocí Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 10/12/2020
ms.author: cherylmc
ms.openlocfilehash: 8b9653daf945b6a189bc528cd00de832ae97c03b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978123"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu

Pomocí Azure bastionu se můžete bezpečně a bez problémů připojit k virtuálním počítačům přes SSL přímo v Azure Portal. Pokud používáte Azure bastionu, vaše virtuální počítače nevyžadují klienta, agenta ani další software. V tomto článku se dozvíte, jak se připojit k virtuálním počítačům s Windows. Informace o připojení k virtuálnímu počítači se systémem Linux najdete v tématu [připojení k virtuálnímu počítači pomocí Azure bastionu-Linux](bastion-connect-vm-ssh.md).

Azure bastionu poskytuje zabezpečené připojení ke všem virtuálním počítačům ve virtuální síti, ve které se zřídí. Použití Azure bastionu chrání vaše virtuální počítače před vystavení portů RDP/SSH na vnějším světě a zároveň zajišťuje zabezpečený přístup pomocí protokolu RDP/SSH. Další informace najdete v tématu [Přehled](bastion-overview.md).

## <a name="before-you-begin"></a>Než začnete

### <a name="install-the-bastion-host"></a>Instalace hostitele bastionu

Ujistěte se, že jste nastavili hostitele Azure bastionu pro virtuální síť, ve které se virtuální počítač nachází. Jakmile se služba bastionu zřídí a nasadí ve vaší virtuální síti, můžete ji použít pro připojení k libovolnému virtuálnímu počítači ve virtuální síti. Pokud chcete nastavit hostitele Azure bastionu, přečtěte si téma [Vytvoření hostitele Azure bastionu](bastion-create-host-portal.md).

### <a name="required-roles"></a>Požadované role

Chcete-li vytvořit připojení, jsou vyžadovány následující role:

* Role čtenář na virtuálním počítači
* Role čtecího zařízení na síťové kartě s privátní IP adresou virtuálního počítače
* Role čtenář v prostředku Azure bastionu

### <a name="ports"></a>Porty

Pokud se chcete připojit k virtuálnímu počítači s Windows, musíte mít na VIRTUÁLNÍm počítači s Windows otevřené následující porty:

* Příchozí porty: RDP (3389)

## <a name="connect"></a><a name="rdp"></a>Připojit

[!INCLUDE [Connect to a Windows VM](../../includes/bastion-vm-rdp.md)]
 
## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md).
