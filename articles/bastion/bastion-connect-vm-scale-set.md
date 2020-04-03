---
title: Připojení k škálovací sadě virtuálních strojů s Windows pomocí Azure Bastion | Dokumenty společnosti Microsoft
description: V tomto článku se dozvíte, jak se připojit k škálovací sadě virtuálních strojů Azure pomocí Azure Bastion.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 290a20fcd827841c24983f3bdd54b6db8e154462
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619346"
---
# <a name="connect-to-a-virtual-machine-scale-set-using-azure-bastion"></a>Připojení k škálovací sadě virtuálních strojů pomocí Azure Bastion

Tento článek ukazuje, jak bezpečně a bezproblémově rdp do vašeho windows virtuálního počítače škálovací sady instance ve virtuální síti Azure pomocí Azure Bastion. K instanci škálovací sady virtuálního počítače se můžete připojit přímo z portálu Azure. Při použití Azure Bastion virtuální počítače nevyžadují klienta, agenta nebo další software. Další informace o Azure Bastion najdete v tématu [Přehled](bastion-overview.md).

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že jste nastavili hostitele Azure Bastion pro virtuální síť, ve které se nachází škálovací sada virtuálních strojů. Další informace najdete [v tématu Vytvoření hostitele Bašty Azure](bastion-create-host-portal.md). Jakmile je služba Bastion zřízena a nasazena ve vaší virtuální síti, můžete ji použít k připojení k instanci škálovací sady virtuálních strojů v této virtuální síti. Bastion předpokládá, že používáte RDP pro připojení k škálovací sadě virtuálních strojů Windows a SSH pro připojení k škálovací sadě virtuálních strojů linux. Informace o připojení k virtuálnímu počítači SIP najdete v [tématu Připojení k virtuálnímu počítači – Linux](bastion-connect-vm-ssh.md).

## <a name="connect-using-rdp"></a><a name="rdp"></a>Připojení pomocí prv

1. Otevřete [portál Azure](https://portal.azure.com). Přejděte na škálovací sadu virtuálních strojů, ke které se chcete připojit.

   ![navigate](./media/bastion-connect-vm-scale-set/1.png)
2. Přejděte na instanci škálovací sady virtuálního počítače, ke které se chcete připojit, a pak vyberte **Připojit**. Při použití připojení RDP by škálovací sada virtuálních strojů měla být škálovací sada virtuálního počítače windows.

   ![škálovací sada virtuálních strojů](./media/bastion-connect-vm-scale-set/2.png)
3. Po výběru **možnosti Připojit**se zobrazí boční panel se třemi kartami – RDP, SSH a Bastion. Na bočním panelu vyberte kartu **Bastion.** Pokud jste baštu pro virtuální síť nezřizovali, můžete vybrat odkaz pro konfiguraci Bastionu. Pokyny ke konfiguraci naleznete v [tématu Konfigurace bašty](bastion-create-host-portal.md).

   ![Karta Bašta](./media/bastion-connect-vm-scale-set/3.png)
4. Na kartě Bastion zadejte uživatelské jméno a heslo pro škálovací sadu virtuálních strojů a pak vyberte **Připojit**.

   ![připojit](./media/bastion-connect-vm-scale-set/4.png)
5. Připojení RDP k tomuto virtuálnímu počítači přes Bastion se otevře přímo na portálu Azure (přes HTML5) pomocí portu 443 a služby Bastion.

## <a name="next-steps"></a>Další kroky

Přečtěte si nejčastější dotazy k [baště](bastion-faq.md).