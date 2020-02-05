---
title: Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu | Microsoft Docs
description: V tomto článku se dozvíte, jak se připojit k virtuálnímu počítači Azure s Windows pomocí Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: cherylmc
ms.openlocfilehash: 7dad6a517341f83f693e1e7e1f7d27e899e00f7e
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990483"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu

V tomto článku se dozvíte, jak bezpečně a hladce RDP na virtuální počítače s Windows ve službě Azure Virtual Network pomocí Azure bastionu. K virtuálnímu počítači se můžete připojit přímo z webu Azure Portal. Při použití Azure Bastionu virtuální počítače nevyžadují žádného klienta, agenta ani další software. Další informace o Azure bastionu najdete v tématu [Přehled](bastion-overview.md).

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že jste nastavili hostitele Azure bastionu pro virtuální síť, ve které se virtuální počítač nachází. Další informace najdete v tématu [Vytvoření hostitele Azure bastionu](bastion-create-host-portal.md). Jakmile se služba bastionu zřídí a nasadí ve vaší virtuální síti, můžete ji použít pro připojení k libovolnému virtuálnímu počítači v této virtuální síti.

Bastionu předpokládá, že používáte protokol RDP pro připojení k virtuálnímu počítači s Windows a SSH pro připojení k virtuálním počítačům Linux. Informace o připojení k virtuálnímu počítači se systémem Linux najdete v tématu [připojení k virtuálnímu počítači](bastion-connect-vm-ssh.md)se systémem Linux.

### <a name="required-roles"></a>Požadované role
Aby bylo možné vytvořit připojení, jsou vyžadovány následující role:

* Role čtenář na virtuálním počítači
* Role čtecího zařízení na síťové kartě s privátní IP adresou virtuálního počítače
* Role čtenář v prostředku Azure bastionu

### <a name="ports"></a>Porty

Aby bylo možné se připojit k virtuálnímu počítači s Windows přes RDP, musíte mít na VIRTUÁLNÍm počítači s Windows otevřené následující porty:

* Příchozí porty: RDP (3389)

## <a name="rdp"></a>Připojit pomocí protokolu RDP

1. Otevřete web [Azure Portal](https://portal.azure.com). Přejděte k virtuálnímu počítači, ke kterému se chcete připojit, a pak klikněte na **připojit**. Virtuální počítač by měl být virtuální počítač s Windows, když se používá připojení RDP.

   ![Připojení k virtuálnímu počítači](./media/bastion-connect-vm-rdp/connect.png)
1. Po kliknutí na připojit se zobrazí boční panel se třemi kartami – RDP, SSH a bastionu. Pokud byl pro virtuální síť zřízen bastionu, karta bastionu je ve výchozím nastavení aktivní. Pokud jste nezřídili bastionu pro virtuální síť, můžete kliknout na odkaz a nakonfigurovat bastionu. Pokyny ke konfiguraci najdete v tématu [Konfigurace bastionu](bastion-create-host-portal.md).

   ![Připojení k virtuálnímu počítači](./media/bastion-connect-vm-rdp/bastion.png)
1. Na kartě bastionu, uživatelské jméno a heslo pro váš virtuální počítač a pak klikněte na **připojit**. Připojení RDP k tomuto virtuálnímu počítači prostřednictvím bastionu se otevře přímo v Azure Portal (přes HTML5) pomocí portu 443 a služby bastionu.

   ![Připojení k virtuálnímu počítači](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md)
