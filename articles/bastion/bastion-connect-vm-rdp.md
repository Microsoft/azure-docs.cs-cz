---
title: Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu | Microsoft Docs
description: V tomto článku se dozvíte, jak se připojit k virtuálnímu počítači Azure s Windows pomocí Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: cherylmc
ms.openlocfilehash: dc741007c7de8d8e24f9c0f9e4e0c03306d036a4
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498353"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu

V tomto článku se dozvíte, jak bezpečně a hladce RDP na virtuální počítače s Windows ve službě Azure Virtual Network pomocí Azure bastionu. K virtuálnímu počítači se můžete připojit přímo z webu Azure Portal. Při použití Azure Bastionu virtuální počítače nevyžadují žádného klienta, agenta ani další software. Další informace o Azure bastionu najdete v tématu [Přehled](bastion-overview.md).

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že jste nastavili hostitele Azure bastionu pro virtuální síť, ve které se virtuální počítač nachází. Další informace najdete v tématu [Vytvoření hostitele Azure bastionu](bastion-create-host-portal.md). Jakmile se služba bastionu zřídí a nasadí ve vaší virtuální síti, můžete ji použít pro připojení k libovolnému virtuálnímu počítači v této virtuální síti. Bastionu předpokládá, že používáte protokol RDP pro připojení k virtuálnímu počítači s Windows a SSH pro připojení k virtuálním počítačům Linux. Informace o připojení k virtuálnímu počítači se systémem Linux najdete v tématu [připojení k virtuálnímu počítači](bastion-connect-vm-ssh.md)se systémem Linux.

Aby bylo možné vytvořit připojení, jsou vyžadovány následující role:

* Role čtenář na virtuálním počítači
* Role čtecího zařízení na síťové kartě s privátní IP adresou virtuálního počítače
* Role čtenář v prostředku Azure bastionu

## <a name="rdp"></a>Připojit pomocí protokolu RDP

1. Otevřete web [Azure Portal](https://portal.azure.com). Přejděte k virtuálnímu počítači, ke kterému se chcete připojit, a pak klikněte na **připojit**. Virtuální počítač by měl být virtuální počítač s Windows, když se používá připojení RDP.

   ![Připojení k virtuálnímu počítači](./media/bastion-connect-vm-rdp/connect.png)
1. Po kliknutí na připojit se zobrazí boční panel se třemi kartami – RDP, SSH a bastionu. Pokud byl pro virtuální síť zřízen bastionu, karta bastionu je ve výchozím nastavení aktivní. Pokud jste nezřídili bastionu pro virtuální síť, můžete kliknout na odkaz a nakonfigurovat bastionu. Pokyny ke konfiguraci najdete v tématu [Konfigurace bastionu](bastion-create-host-portal.md).

   ![Připojení k virtuálnímu počítači](./media/bastion-connect-vm-rdp/bastion.png)
1. Na kartě bastionu, uživatelské jméno a heslo pro váš virtuální počítač a pak klikněte na **připojit**. Připojení RDP k tomuto virtuálnímu počítači prostřednictvím bastionu se otevře přímo v Azure Portal (přes HTML5) pomocí portu 443 a služby bastionu.

   ![Připojení k virtuálnímu počítači](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md)