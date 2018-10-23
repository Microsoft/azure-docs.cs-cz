---
title: Připojte se k virtuálnímu počítači Microsoft Azure na základě | Dokumentace Microsoftu
description: Vysvětluje, jak se připojit k novému virtuálnímu počítači v Azure vytvoří.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: fd68846b9144c3efcc71dec369d64119427758a3
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639617"
---
# <a name="connect-to-your-azure-based-virtual-machine"></a>Připojte se k virtuálnímu počítači založené na Azure

Tento článek vysvětluje, jak se připojit k a přihlaste se do virtuálních počítačů (VM), který jste vytvořili v Azure.  Jakmile se úspěšně připojíte, můžete pracovat s virtuálním Počítačem jako kdyby byly místní přihlášení k serveru hostitele. 

## <a name="connect-to-a-windows-based-vm"></a>Připojte se k virtuálnímu počítači s operačním systémem Windows

Klient vzdálené plochy použijete pro připojení k založené na Windows virtuální počítač hostovaný v Azure.  Většina verze Windows nativně obsahují podporu pro remote desktop protocol (RDP).  Pro jiné počítače, můžete najít další informace o klientech v [klienty vzdálené plochy](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients).  

Následující článek podrobně popisuje, jak používat integrovanou podporu Windows RDP pro připojení k vašemu virtuálnímu počítači: [jak se připojit a přihlaste se na virtuálním počítači Azure s Windows](../../../virtual-machines/windows/connect-logon.md).  

>[!TIP]
> Může se zobrazit upozornění zabezpečení během procesu, například, že soubor .rdp je od neznámého vydavatele nebo že nelze ověřit vaše přihlašovací údaje uživatele.  Je bezpečné ignorovat těchto upozornění.


## <a name="connect-to-a-linux-based-vm"></a>Připojte se k virtuálnímu počítači s linuxem

Aby bylo možné připojit virtuální počítač založený na Linuxu, budete potřebovat klienta protokolu (SSH) zabezpečené prostředí.  Tato diskuse se používat bezplatnou [PuTTY](https://www.ssh.com/ssh/putty/) který terminálu.

1. V **virtuálních počítačů** okně [webu Azure portal](https://ms.portal.azure.com), vyberte virtuální počítač, které se chcete připojit.  
2. **Spustit** virtuálního počítače, pokud ještě není spuštěná.
3. Klikněte na název virtuálního počítače a otevřete její **přehled** stránky.
4. Poznamenejte si veřejnou IP adresu a název DNS virtuálního počítače.  (Pokud tyto hodnoty nejsou nastavené, je nutné [vytvořte síťové rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#create-a-network-interface)

   ![Přehled virtuálních počítačů nastavení](./media/publishvm_019.png)
 
5. Otevřete PuTTY aplikaci.  
6. V dialogovém okně Konfigurace PuTTY zadejte IP adresu nebo název DNS virtuálního počítače. 

   ![Nastavení puTTY terminálu](./media/publishvm_020.png)
 
7. Klikněte na tlačítko **otevřete** otevřete PuTTY terminálu.  
8. Když se zobrazí výzva, zadejte název účtu a heslo vašeho účtu virtuálního počítače s Linuxem. 

   Pokud máte problémy s připojením, najdete v dokumentaci k vašemu klientovi SSH, například [kapitoly 10: běžné chybové zprávy](https://www.ssh.com/ssh/putty/putty-manuals/0.68/Chapter10.html#errors).

Další informace, včetně přidání desktopů, které je zřízená virtuálního počítače s Linuxem, v tématu [instalace a konfigurace vzdálené plochy pro připojení k virtuálnímu počítači s Linuxem v Azure](../../../virtual-machines/linux/use-remote-desktop.md).


## <a name="stop-unused-vms"></a>Zastavit nevyužitých virtuálních počítačů
Azure účtuje poplatky za pro hostování virtuálních počítačů při spuštění virtuálního počítače *nebo nečinnosti*.  Proto je vhodné zastavit virtuální počítače, které nejsou aktuálně používá.  Například můžete testovat, zálohování, nebo vyřazeno virtuální počítače jsou kandidáty pro vypnutí. Vypnout virtuální počítač, proveďte následující kroky:

1. Na **virtuálních počítačů** okno, vyberte virtuální počítač, který chcete zastavit. 
2. Na panelu nástrojů v horní části stránky klikněte na **Zastavit** tlačítko.

   ![Zastavení virtuálního počítače](./media/publishvm_018.png)

Azure rychle zastaví virtuální počítač v procesu nazývaného *zrušení přidělení*, který nejen vypne operační systém na virtuálním počítači, ale navíc se uvolní hardwarové a síťové prostředky, které byly dříve zřízeny pro něj.

Pokud chcete později znovu aktivovat zastaveného virtuálního počítače, vyberte ho a klikněte **Start** tlačítko.


## <a name="next-steps"></a>Další postup

Až se vzdáleně připojíte, budete chtít [konfigurace virtuálního počítače](./cpp-configure-vm.md).
