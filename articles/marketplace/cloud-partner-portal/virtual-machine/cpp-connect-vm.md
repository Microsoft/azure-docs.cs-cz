---
title: Připojení k virtuálnímu počítači na Microsoft Azure | Azure Marketplace
description: Vysvětluje, jak se připojit k novému virtuálnímu počítači vytvořenému v Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 10/19/2018
ms.author: pabutler
ms.openlocfilehash: 256952d06e8b417be9ab2bc3c6c8c6857d50e2ea
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73817375"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Připojení k virtuálnímu počítači založenému na Azure

Tento článek vysvětluje, jak se připojit k virtuálním počítačům, které jste vytvořili v Azure, a přihlašovat se k nim.  Po úspěšném připojení můžete s virtuálním počítačem pracovat jako v případě, že jste byli místně přihlášení k hostitelskému serveru. 

## <a name="connect-to-a-windows-based-vm"></a>Připojení k virtuálnímu počítači se systémem Windows

Pomocí klienta vzdálené plochy se připojíte k virtuálnímu počítači s Windows hostovanému v Azure.  Většina verzí Windows nativně obsahuje podporu protokolu RDP (Remote Desktop Protocol).  Pro jiné počítače můžete najít další informace o klientech v [klientech vzdálené plochy](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

Následující článek podrobně popisuje, jak používat integrovanou podporu Windows RDP pro připojení k vašemu VIRTUÁLNÍmu počítači: [jak se připojit a přihlásit k virtuálnímu počítači Azure s Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Během procesu můžete obdržet upozornění zabezpečení, například to, že soubor. RDP pochází od neznámého vydavatele nebo že vaše přihlašovací údaje uživatele nelze ověřit.  Tato upozornění je bezpečné ignorovat.


## <a name="connect-to-a-linux-based-vm"></a>Připojení k virtuálnímu počítači se systémem Linux

Aby bylo možné připojit virtuální počítač se systémem Linux, budete potřebovat klienta SSH (Secure Shell Protocol).  Tato diskuze bude [používat bezplatný SSH](https://www.ssh.com/ssh/putty/) terminál.

1. V okně **virtuální počítače** v [Azure Portal](https://ms.portal.azure.com)vyberte virtuální počítač, ke kterému se chcete připojit.  
2. **Spusťte** virtuální počítač, pokud ještě není spuštěný.
3. Kliknutím na název virtuálního počítače otevřete jeho stránku **Přehled** .
4. Poznamenejte si veřejnou IP adresu a název DNS svého virtuálního počítače.  (Pokud tyto hodnoty nejsou nastavené, musíte [vytvořit síťové rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface) .

   ![Nastavení přehledu virtuálních počítačů](./media/publishvm_019.png)
 
5. Otevřete aplikaci pro výstup.  
6. V dialogovém okně Konfigurace výstupu zadejte IP adresu nebo název DNS vašeho virtuálního počítače. 

   ![Nastavení terminálu pro výstup](./media/publishvm_020.png)
 
7. Kliknutím na **otevřít** otevřete terminál k výstupu.  
8. Po zobrazení výzvy zadejte název účtu a heslo účtu virtuálního počítače se systémem Linux. 

   Pokud máte problémy s připojením, přečtěte si dokumentaci k vašemu klientovi SSH, například [Kapitola 10: běžné chybové zprávy](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors).

Další informace, včetně postupu přidání plochy do zřízeného virtuálního počítače se systémem Linux, najdete v tématu [instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači se systémem Linux v Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Zastavit nepoužívané virtuální počítače
Azure účtuje pro hostování virtuálních počítačů, když je virtuální počítač spuštěný *nebo nečinný*.  Proto je osvědčeným postupem zastavit virtuální počítače, které se momentálně nepoužívají.  Například test, zálohování nebo vyřazené virtuální počítače jsou kandidáty na vypnutí. Pokud chcete virtuální počítač vypnout, proveďte následující kroky:

1. V okně **virtuální počítače** vyberte virtuální počítač, který chcete zastavit. 
2. Na panelu nástrojů v horní části stránky klikněte na tlačítko **zastavit** .

   ![Zastavení virtuálního počítače](./media/publishvm_018.png)

Azure rychle zastaví virtuální počítač v procesu, který se nazývá zrušení *přidělení*, a to nejen ukončí operační systém na virtuálním počítači, ale taky uvolní hardwarové a síťové prostředky, které pro něj byly dřív zřízené.

Pokud chcete později zastavený virtuální počítač znovu aktivovat, vyberte ho a klikněte na tlačítko **Spustit** .


## <a name="next-steps"></a>Další kroky

Po vzdáleném připojení budete připraveni ke [konfiguraci virtuálního počítače](./cpp-configure-vm.md).
