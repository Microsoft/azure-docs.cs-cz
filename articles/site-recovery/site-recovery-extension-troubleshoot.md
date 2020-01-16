---
title: Řešení potíží s rozšířením virtuálního počítače Azure pro zotavení po havárii pomocí Azure Site Recovery
description: Řešení potíží s rozšířením virtuálního počítače Azure pro zotavení po havárii s Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: a9d28a12f5f1fa32d2bc3bcf590134930503f2ac
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75970386"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Řešení potíží s rozšířením virtuálních počítačů Azure

Tento článek popisuje kroky pro řešení potíží, které vám pomůžou vyřešit chyby Azure Site Recovery související s agentem a rozšířením virtuálního počítače.


## <a name="azure-site-recovery-extension-time-out"></a>Vypršel časový limit rozšíření Azure Site Recovery.  

Chybová zpráva: při sledování operace rozšíření vypršel časový limit pro spuštění úlohy.<br>
Kód chyby: "151076"

 Azure Site Recovery do virtuálního počítače nainstalovat rozšíření jako součást úlohy povolení ochrany. Některá z následujících podmínek může zabránit aktivované ochraně a selhání úlohy. Proveďte následující kroky pro řešení potíží a opakujte operaci:

**Příčina 1: [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Příčina 2: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) .**  
**Příčina 3: Nepodařilo se [aktualizovat nebo načíst rozšíření Site Recovery](#the-site-recovery-extension-fails-to-update-or-load) .**  

Chybová zpráva: "předchozí operace rozšíření Site Recovery trvá déle, než se čekalo."<br>
Kód chyby: "150066"<br>

**Příčina 1: [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Příčina 2: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) .**  
**Příčina 3: [stav rozšíření Site Recovery není správný](#the-site-recovery-extension-fails-to-update-or-load) .**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Ochrana se nezdařila, protože agent virtuálního počítače nereaguje.

Chybová zpráva: během sledování operace rozšíření vypršel časový limit pro spuštění úlohy.<br>
Kód chyby: "151099"<br>

K této chybě může dojít, pokud Agent hosta Azure ve virtuálním počítači není ve stavu připraveno.
Stav agenta hosta Azure můžete zjistit v [Azure Portal](https://portal.azure.com/). Přejít na virtuální počítač, který se pokoušíte chránit, a v části nastavení virtuálního počítače > Vlastnosti > stav agenta vyberte stav >. Ve většině případů se stav agenta stane připravený po restartování virtuálního počítače. Pokud však není možné provést restart nebo se k problému stále přihlašovat, proveďte následující kroky pro řešení potíží.

**Příčina 1: [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**     
**Příčina 2: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) .**  


Chybová zpráva: během sledování operace rozšíření vypršel časový limit pro spuštění úlohy.<br>
Kód chyby: "151095"<br>

K tomu dochází, když je verze agenta v počítači se systémem Linux stará. Dokončete prosím následující krok řešení potíží.<br>
  **Příčina 1: [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms) .**  
## <a name="causes-and-solutions"></a>Příčiny a řešení

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows).

#### <a name="solution"></a>Řešení
Je možné, že agent virtuálního počítače je poškozený nebo se služba zastavila. Opětovné instalace agenta virtuálního počítače pomáhá získat nejnovější verzi. Také pomáhá s restartováním komunikace se službou.

1. Zjistěte, jestli je na službě VM Services (Services. msc) spuštěná služba agenta hosta systému Windows Azure. Pokuste se restartovat službu agenta hosta systému Windows Azure.    
2. Pokud služba agenta hosta systému Windows Azure není v rámci služeb viditelná, v Ovládacích panelech klikněte na **programy a funkce** , abyste zjistili, jestli je nainstalovaná služba Agent hosta systému Windows.
4. Pokud se Agent hosta systému Windows Azure zobrazí v části **programy a funkce**, odinstalujte agenta hosta systému Windows.
5. Stáhněte a nainstalujte si [nejnovější verzi MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace musíte mít oprávnění správce.
6. Ověřte, že se v části služby zobrazují služby agenta hosta systému Windows Azure.
7. Restartujte úlohu ochrany.

Ověřte také, že na virtuálním počítači [je nainstalováno rozhraní Microsoft .net 4,5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) . Aby agent virtuálního počítače mohl komunikovat se službou, je vyžadován .NET 4,5.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).

#### <a name="solution"></a>Řešení
Většina selhání souvisejících s agentem nebo rozšířeními pro virtuální počítače se systémem Linux je způsobena problémy, které mají vliv na zastaralého agenta virtuálního počítače. Pokud chcete tento problém vyřešit, postupujte podle následujících obecných pokynů:

1. Postupujte podle pokynů pro [aktualizaci agenta virtuálního počítače se systémem Linux](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > *Důrazně doporučujeme* , abyste agenta aktualizovali pouze prostřednictvím distribučního úložiště. Nedoporučujeme stahovat kód agenta přímo z GitHubu a aktualizovat ho. Pokud není k dispozici nejnovější agent pro distribuci, obraťte se na podporu distribuce, kde najdete pokyny k jeho instalaci. Chcete-li zjistit nejnovějšího agenta, v úložišti GitHubu, navštivte stránku [agenta Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) .

2. Spuštěním následujícího příkazu zajistěte, aby byl na virtuálním počítači spuštěný agent Azure: `ps -e`

   Pokud proces není spuštěný, restartujte ho pomocí následujících příkazů:

   * Pro Ubuntu: `service walinuxagent start`
   * Pro ostatní distribuce: `service waagent start`

3. [Nakonfigurujte agenta automatického restartování](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Povolte ochranu virtuálního počítače.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Nepodařilo se aktualizovat nebo načíst rozšíření Site Recovery.
Pokud je stav rozšíření "prázdné", "nenastavené" nebo "přechod".

#### <a name="solution"></a>Řešení

Odinstalujte rozšíření a znovu operaci spusťte.

Postup při odinstalaci rozšíření:

1. V [Azure Portal](https://portal.azure.com/)přejdete na virtuální počítač, u kterého dochází k chybě zálohování.
2. Vyberte **nastavení**.
3. Vyberte **Extensions** (Rozšíření).
4. Vyberte **rozšíření Site Recovery**.
5. Vyberte **Odinstalovat**.

Pokud se u virtuálního počítače se systémem Linux v Azure Portal nezobrazuje rozšíření VMSnapshot, [aktualizujte agenta Azure Linux](../virtual-machines/linux/update-agent.md)a pak spusťte ochranu.

Dokončením těchto kroků dojde k přeinstalování rozšíření během ochrany.
