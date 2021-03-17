---
title: Řešení potíží s rozšířením virtuálního počítače Azure pro zotavení po havárii pomocí Azure Site Recovery
description: Řešení potíží s rozšířením virtuálního počítače Azure pro zotavení po havárii s Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009700"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Řešení potíží s rozšířením virtuálního počítače Azure

Tento článek popisuje kroky pro řešení potíží, které vám pomůžou vyřešit chyby Azure Site Recovery související s agentem a rozšířením virtuálního počítače.

## <a name="low-system-resources"></a>Nedostatek systémových prostředků

K tomuto problému dochází, když má systém nedostatek dostupné paměti a nemůže přidělit paměť pro instalaci služby mobility. Zajistěte, aby byla pro instalaci dokončena dostatek paměti, aby bylo možné pokračovat v jejím dokončení.

## <a name="azure-site-recovery-extension-time-out"></a>Časový limit rozšíření Azure Site Recovery  

Chybová zpráva: při sledování operace rozšíření vypršel časový limit pro spuštění úlohy.<br>
Kód chyby: "151076"

 V rámci úlohy povolení ochrany Azure Site Recovery nainstalovaná rozšíření na virtuálním počítači. Některá z následujících podmínek může zabránit aktivované ochraně a způsobit selhání úlohy. Proveďte následující kroky pro řešení potíží a opakujte operaci:

- [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Nepodařilo se aktualizovat nebo načíst rozšíření Site Recovery.](#the-site-recovery-extension-fails-to-update-or-load)

Chybová zpráva: "předchozí operace rozšíření Site Recovery trvá déle, než se čekalo."<br>
Kód chyby: "150066"

- [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Stav rozšíření Site Recovery není správný.](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Ochrana se nezdařila, protože agent virtuálního počítače nereaguje.

Chybová zpráva: během sledování operace rozšíření vypršel časový limit pro spuštění úlohy.<br>
Kód chyby: "151099"

K této chybě může dojít, pokud Agent hosta Azure ve virtuálním počítači není ve stavu připraveno.

Stav agenta hosta Azure můžete zjistit v [Azure Portal](https://portal.azure.com/). Přejít na virtuální počítač, který se pokoušíte chránit, a zkontroluje stav ve vlastnostech nastavení **virtuálního počítače**  >  **Settings**  >  **Properties**  >  **Stav agenta**. Ve většině případů je stav agenta připravený po restartování virtuálního počítače. Pokud se ale nemůžete restartovat nebo máte problém, proveďte následující kroky pro řešení potíží:

- [Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows).](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Chybová zpráva: během sledování operace rozšíření vypršel časový limit pro spuštění úlohy.<br>
Kód chyby: "151095"

K této chybě dochází, pokud je verze agenta v počítači se systémem Linux zastaralá. Proveďte následující krok řešení potíží:

- [Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Příčiny a řešení

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent je nainstalovaný na virtuálním počítači, ale nereaguje (pro virtuální počítače s Windows).

#### <a name="solution"></a>Řešení
Je možné, že agent virtuálního počítače je poškozený nebo se služba zastavila. Opětovná instalace agenta virtuálního počítače pomáhá získat nejnovější verzi. Také pomáhá s restartováním komunikace se službou.

1. Zjistěte, jestli je služba agenta hosta systému Windows Azure spuštěná ve službě VM (Services. msc). Restartujte službu agenta hosta systému Windows Azure.    
1. Pokud služba agenta hosta systému Windows Azure není v rámci služeb viditelná, otevřete ovládací panely. Pokud chcete zjistit, jestli je nainstalovaná služba Agent hosta systému Windows, přejděte na **programy a funkce** .
1. Pokud se Agent hosta systému Windows Azure zobrazí v části **programy a funkce**, odinstalujte agenta hosta systému Windows Azure.
1. Stáhněte a nainstalujte si [nejnovější verzi MSI agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace potřebujete oprávnění správce.
1. Ověřte, že se v části služby zobrazuje služba agenta hosta systému Windows Azure.
1. Restartujte úlohu ochrany.

Ověřte také, že na virtuálním počítači [je nainstalováno rozhraní Microsoft .net 4,5](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) . Pro komunikaci s touto službou potřebujete .NET 4,5 pro agenta virtuálního počítače.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent nainstalovaný na virtuálním počítači je zastaralý (pro virtuální počítače se systémem Linux).

#### <a name="solution"></a>Řešení
Většina selhání souvisejících s agentem nebo rozšířeními pro virtuální počítače se systémem Linux je způsobena problémy, které mají vliv na zastaralého agenta virtuálního počítače. Pokud chcete tento problém vyřešit, postupujte podle následujících obecných pokynů:

1. Postupujte podle pokynů pro [aktualizaci agenta virtuálního počítače se systémem Linux](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > *Důrazně doporučujeme* , abyste agenta aktualizovali pouze prostřednictvím distribučního úložiště. Nedoporučujeme stahovat kód agenta přímo z GitHubu a aktualizovat ho. Pokud není k dispozici nejnovější agent pro distribuci, obraťte se na podporu distribuce, kde najdete pokyny k jeho instalaci. Chcete-li zjistit nejnovějšího agenta, v úložišti GitHubu, navštivte stránku [agenta Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) .

1. Spuštěním následujícího příkazu zajistěte, aby byl na virtuálním počítači spuštěný agent Azure: `ps -e`

   Pokud proces není spuštěný, restartujte ho pomocí následujících příkazů:

   - Pro Ubuntu: `service walinuxagent start`
   - Pro ostatní distribuce: `service waagent start`

1. [Nakonfigurujte agenta automatického restartování](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Povolte ochranu virtuálního počítače.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Nepodařilo se aktualizovat nebo načíst rozšíření Site Recovery.

Stav rozšíření ukazuje na "prázdný", "nepříliš stupňů" nebo "Transition".

#### <a name="solution"></a>Řešení

Odinstalujte rozšíření a znovu operaci spusťte.

Postup při odinstalaci rozšíření:

1. V [Azure Portal](https://portal.azure.com/)přejdete na virtuální počítač, u kterého dochází k chybě zálohování.
1. Vyberte **Nastavení**.
1. Vyberte **Rozšíření**.
1. Vyberte **rozšíření Site Recovery**.
1. Vyberte **Odinstalovat**.

Pokud se u virtuálního počítače se systémem Linux v Azure Portal nezobrazuje rozšíření VMSnapshot, [aktualizujte agenta Azure Linux](../virtual-machines/extensions/update-linux-agent.md). Pak spusťte ochranu.

Po dokončení těchto kroků dojde k přeinstalování rozšíření během ochrany.
