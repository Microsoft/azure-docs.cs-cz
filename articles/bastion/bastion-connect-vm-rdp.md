---
title: Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu
description: V tomto článku se dozvíte, jak se připojit k virtuálnímu počítači Azure s Windows pomocí Azure bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: how-to
ms.date: 02/24/2020
ms.author: cherylmc
ms.openlocfilehash: 79eb09a005f62846fc2f7e3e7b493d5e366edabc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84744319"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion"></a>Připojení k virtuálnímu počítači s Windows pomocí Azure bastionu

Pomocí Azure bastionu se můžete bezpečně a bez problémů připojit k virtuálním počítačům přes SSL přímo v Azure Portal. Pokud používáte Azure bastionu, vaše virtuální počítače nevyžadují klienta, agenta ani další software. V tomto článku se dozvíte, jak se připojit k virtuálním počítačům s Windows. Informace o připojení k virtuálnímu počítači se systémem Linux najdete v tématu [připojení k virtuálnímu počítači pomocí Azure bastionu-Linux](bastion-connect-vm-ssh.md).

Azure bastionu poskytuje zabezpečené připojení ke všem virtuálním počítačům ve virtuální síti, ve které se zřídí. Použití Azure bastionu chrání vaše virtuální počítače před vystavení portů RDP/SSH na vnějším světě a zároveň zajišťuje zabezpečený přístup pomocí protokolu RDP/SSH. Další informace najdete v tématu [Přehled](bastion-overview.md).

## <a name="before-you-begin"></a>Než začnete

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

1. Otevřete web [Azure Portal](https://portal.azure.com). Přejděte k virtuálnímu počítači, ke kterému se chcete připojit, a pak klikněte na **připojit** a v rozevíracím seznamu vyberte **bastionu** .

   ![Připojení k virtuálnímu počítači](./media/bastion-connect-vm-rdp/connect.png)
1. Po kliknutí na bastionu se zobrazí postranní panel se třemi kartami – RDP, SSH a bastionu. Pokud byl pro virtuální síť zřízen bastionu, karta bastionu je ve výchozím nastavení aktivní. Pokud jste nezřídili bastionu pro virtuální síť, můžete kliknout na odkaz a nakonfigurovat bastionu. Pokyny ke konfiguraci najdete v tématu [Konfigurace bastionu](bastion-create-host-portal.md).

   ![karta bastionu](./media/bastion-connect-vm-rdp/bastion.png)
1. Na kartě bastionu zadejte uživatelské jméno a heslo pro váš virtuální počítač a pak klikněte na **připojit**. Připojení RDP k tomuto virtuálnímu počítači prostřednictvím bastionu se otevře přímo v Azure Portal (přes HTML5) pomocí portu 443 a služby bastionu.

   ![Připojení RDP](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md)
