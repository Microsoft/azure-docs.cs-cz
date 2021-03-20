---
title: Připojení k sadě škálování virtuálních počítačů s Windows pomocí Azure bastionu | Microsoft Docs
description: V tomto článku se dozvíte, jak se připojit k sadě škálování virtuálních počítačů Azure pomocí Azure bastionu.
services: bastion
author: charwen
ms.service: bastion
ms.topic: how-to
ms.date: 10/14/2020
ms.author: charwen
ms.openlocfilehash: 001d2ff6789ec4cfcc391171f0859b67ab1ee0a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92077775"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Připojení k sadě škálování virtuálního počítače pomocí Azure bastionu

V tomto článku se dozvíte, jak bezpečně a hladce RDP na instanci sady škálování virtuálních počítačů s Windows ve službě Azure Virtual Network pomocí Azure bastionu. K instanci sady škálování virtuálního počítače se můžete připojit přímo z Azure Portal. Při použití Azure bastionu virtuální počítače nevyžadují klienta, agenta ani další software. Další informace o Azure bastionu najdete v tématu [Přehled](bastion-overview.md).

## <a name="prerequisites"></a>Předpoklady

Ujistěte se, že jste pro virtuální síť nastavili hostitele Azure bastionu, ve kterém se sada škálování virtuálního počítače nachází. Další informace najdete v tématu [Vytvoření hostitele Azure bastionu](./tutorial-create-host-portal.md). Jakmile je služba bastionu zřízená a nasazená ve vaší virtuální síti, můžete ji použít pro připojení k instanci sady škálování virtuálního počítače v této virtuální síti. Bastionu předpokládá, že používáte protokol RDP pro připojení k sadě škálování virtuálních počítačů s Windows, a SSH pro připojení k sadě škálování virtuálních počítačů se systémem Linux. Informace o připojení k virtuálnímu počítači se systémem Linux najdete v tématu [připojení k virtuálnímu počítači](bastion-connect-vm-ssh.md)se systémem Linux.

## <a name="connect-using-rdp"></a><a name="rdp"></a>Připojit pomocí protokolu RDP

1. Otevřete [Azure Portal](https://portal.azure.com). Přejděte do sady škálování virtuálních počítačů, ke které se chcete připojit.

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. Přejděte do instance sady škálování virtuálních počítačů, ke které se chcete připojit, a pak vyberte **připojit**. Při použití připojení RDP by měla být sada škálování virtuálního počítače nastavená na sadu škálování virtuálních počítačů s Windows.

   ![sada škálování virtuálních počítačů](./media/bastion-connect-vm-scale-set/2.png)
3. Po výběru **připojit** se zobrazí boční panel se třemi kartami – RDP, SSH a bastionu. Na bočním panelu vyberte kartu **bastionu** . Pokud jste nezřídili bastionu pro virtuální síť, můžete vybrat odkaz pro konfiguraci bastionu. Pokyny ke konfiguraci najdete v tématu [Konfigurace bastionu](./tutorial-create-host-portal.md).

   ![Karta bastionu](./media/bastion-connect-vm-scale-set/3.png)
4. Na kartě bastionu zadejte uživatelské jméno a heslo pro sadu škálování virtuálního počítače a pak vyberte **připojit**.

   ![připojit](./media/bastion-connect-vm-scale-set/4.png)
5. Připojení RDP k tomuto virtuálnímu počítači prostřednictvím bastionu se otevře přímo v Azure Portal (přes HTML5) pomocí portu 443 a služby bastionu.

## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md).