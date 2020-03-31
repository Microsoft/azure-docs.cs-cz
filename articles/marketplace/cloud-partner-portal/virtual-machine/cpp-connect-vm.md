---
title: Připojení k virtuálnímu počítači založenému na Microsoft Azure | Azure Marketplace
description: Vysvětluje, jak se připojit k novému virtuálnímu počítači vytvořenému v Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: dsindona
ms.openlocfilehash: 2b7eb6d321a64835254b684c8faeedc53645dffe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278054"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Připojení k virtuálnímu počítači založenému na Azure

Tento článek vysvětluje, jak se připojit a přihlásit se k virtuálním počítačům,, které jste vytvořili v Azure.  Po úspěšném připojení můžete s virtuálním počítačem pracovat, jako byste byli místně přihlášeni k hostitelskému serveru. 

## <a name="connect-to-a-windows-based-vm"></a>Připojení k virtuálnímu virtuálnímu virtuálnímu mněmu založenému na Windows

Ke připojení k virtuálnímu počítači založenému na Windows hostovaném v Azure se připojíte pomocí klienta vzdálené plochy.  Většina verzí systému Windows nativně obsahuje podporu protokolu vzdálené plochy (RDP).  Pro ostatní počítače naleznete další informace o klientech v [klientech vzdálené plochy](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

V následujícím článku je podrobně uveden postup připojení integrované podpory protokolu RDP systému Windows k virtuálnímu počítači: [Jak se připojit a přihlásit k virtuálnímu počítači Azure se systémem Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Během procesu se mohou zobrazit upozornění zabezpečení, například že soubor RDP pochází od neznámého vydavatele nebo že pověření uživatele nelze ověřit.  Je bezpečné ignorovat tato upozornění.


## <a name="connect-to-a-linux-based-vm"></a>Připojení k virtuálnímu počítači založenému na Linuxu

Chcete-li připojit virtuální počítač založený na Linuxu, potřebujete klienta zabezpečeného shellového protokolu (SSH).  Tato diskuse bude používat zdarma [PuTTY](https://www.ssh.com/ssh/putty/) SHH terminálu.

1. Přejděte na [portál Azure](https://ms.portal.azure.com). Vyhledejte a vyberte **virtuální počítače**. 
2. Vyberte virtuální ms, ke kterému se chcete připojit.  
3. **Spusťte** virtuální ho, pokud už není spuštěný.
4. Kliknutím na název virtuálního počítače otevřete jeho stránku **Přehled.**
5. Poznamenejte si veřejnou IP adresu a název DNS virtuálního počítače.  (Pokud tyto hodnoty nejsou nastaveny, musíte [vytvořit síťové rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Nastavení přehledu virtuálního počítače](./media/publishvm_019.png)
 
6. Otevřete aplikaci PuTTY.  
7. V dialogovém okně Konfigurace PuTTY zadejte IP adresu nebo název DNS virtuálního počítače. 

   ![Nastavení terminálu PuTTY](./media/publishvm_020.png)
 
8. Klepnutím na **tlačítko Otevřít** otevřete terminál PuTTY.  
9. Až se zobrazí výzva, zadejte název účtu a heslo svého účtu virtuálního počítače s Linuxem. 

Pokud máte problémy s připojením, naleznete v dokumentaci ke klientovi SSH, například [kapitola 10: Běžné chybové zprávy](https://www.ssh.com/ssh/putty/putty-manuals).

Další informace, včetně toho, jak přidat plochu do zřízeného virtuálního počítače s Linuxem, najdete v [tématu Instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači SIP v Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Zastavení nepoužívaných virtuálních mích
Účty Azure za hostování virtuálních počítačí, když je virtuální počítač spuštěný *nebo nečinný*.  Jako takové je osvědčeným postupem zastavit virtuální chody, které se aktuálně nepoužívají.  Například test, zálohování nebo vyřazené virtuální počítače jsou kandidáty pro vypnutí. Pokud chcete virtuální ho zavřít, proveďte následující kroky:

1. Na okně **Virtuální počítače** vyberte virtuální počítač, který chcete zastavit. 
2. Na panelu nástrojů v horní části stránky klikněte na tlačítko **Zastavit.**

   ![Zastavení virtuálního počítače](./media/publishvm_018.png)

Azure rychle zastaví virtuální počítač v procesu s názvem *deallocation*, který nejen vypne operační systém na virtuálním počítači, ale také uvolní hardware a síťové prostředky, které pro něj byly dříve zřízeny.

Pokud chcete později znovu aktivovat zastavený virtuální virtuální účet, vyberte ho a klikněte na tlačítko **Start.**


## <a name="next-steps"></a>Další kroky

Po vzdáleném připojení jste připraveni [nakonfigurovat virtuální počítač](./cpp-configure-vm.md).
