---
title: Poradce při potížích s rozšířením virtuálního počítače Azure pro zotavení po havárii pomocí Azure Site Recovery
description: Řešení problémů s rozšířením virtuálního počítače Azure pro zotavení po havárii s Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: a780a42179a0bacf0e4a12ba1e75ae84943539b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190729"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Řešení potíží s rozšířením virtuálních počítačů Azure

Tento článek obsahuje kroky řešení potíží, které vám pomohou vyřešit chyby azure site recovery související s agentem virtuálního počítače a rozšířením.


## <a name="azure-site-recovery-extension-time-out"></a>Časový čas prodloužení obnovení webu Azure  

Chybová zpráva: "Při sledování operace rozšíření, která má být spuštěna, byl časový plán spuštění spuštění úlohy"<br>
Kód chyby: "151076"

 Azure Site Recovery nainstalovaný rozšíření na virtuální mši jako součást úlohy povolit ochranu. Některé z následujících podmínek může zabránit spuštění ochrany a způsobit selhání úlohy. Proveďte následující kroky řešení potíží a opakujte operaci:

- [Agent je nainstalovaný ve virtuálním virtuálním mísu, ale neodpovídá (pro virtuální aplikace Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Rozšíření obnovení webu se nepodaří aktualizovat nebo načíst](#the-site-recovery-extension-fails-to-update-or-load)

Chybová zpráva: "Předchozí operace rozšíření obnovení webu trvá déle, než bylo očekáváno."<br>
Kód chyby: "150066"

- [Agent je nainstalovaný ve virtuálním virtuálním mísu, ale neodpovídá (pro virtuální aplikace Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Stav rozšíření obnovení webu je nesprávný.](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Ochrana se nezdaří, protože agent virtuálního zařízení neodpovídá.

Chybová zpráva: "Při sledování operace rozšíření, která má být spuštěna, byl časový plán spuštění spuštění úlohy."<br>
Kód chyby: "151099"

K této chybě může dojít, pokud agent hosta Azure ve virtuálním počítači není ve stavu připraven.

Stav agenta hosta Azure můžete zkontrolovat na [webu Azure Portal](https://portal.azure.com/). Přejděte na virtuální počítač, který se snažíte chránit, a zkontrolujte stav ve stavu Agent**a** > **stav Vlastnosti vlastností** >  **virtuálního** > **počítače**. Většinu času je stav agenta připraven po restartování virtuálního počítače. Pokud se však nemůžete restartovat nebo stále čelíte problému, proveďte následující kroky řešení potíží:

- [Agent je nainstalovaný ve virtuálním virtuálním mísu, ale neodpovídá (pro virtuální aplikace Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Chybová zpráva: "Při sledování operace rozšíření, která má být spuštěna, byl časový plán spuštění spuštění úlohy."<br>
Kód chyby: "151095"

K této chybě dochází, když je verze agenta v počítači s Linuxem zastaralá. Proveďte následující krok řešení potíží:

- [Agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Příčiny a řešení

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent je nainstalovaný ve virtuálním virtuálním mísu, ale neodpovídá (pro virtuální aplikace Windows)

#### <a name="solution"></a>Řešení
Agent virtuálního soudu pravděpodobně byl poškozen nebo služba byla zastavena. Přeinstalace agenta virtuálního zařízení pomáhá získat nejnovější verzi. Pomáhá také restartovat komunikaci se službou.

1. Zjistěte, jestli služba Agent hosta Windows Azure běží ve službách virtuálních zařízení (services.msc). Restartujte službu Agent hosta Windows Azure.    
1. Pokud služba Agent hosta Windows Azure není viditelná ve službách, otevřete Ovládací panely. Přejděte na **položku Programy a funkce** a zjistěte, zda je nainstalována služba Agent hosta systému Windows.
1. Pokud se agent hosta Windows Azure zobrazí v **části Programy a funkce**, odinstalujte agenta hosta Windows Azure.
1. Stáhněte a nainstalujte [nejnovější verzi agenta MSI](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). K dokončení instalace potřebujete práva správce.
1. Ověřte, zda se služba Agent hosta Windows Azure zobrazuje ve službách.
1. Restartujte úlohu ochrany.

Ověřte také, že [microsoft .NET 4.5 je nainstalován](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ve virtuálním ms. Ke komunikaci se službou potřebujete rozhraní .NET 4.5 pro agenta virtuálního rozhraní.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)

#### <a name="solution"></a>Řešení
Většina selhání souvisejících s agentem nebo rozšíření mů e pro virtuální počítače s Linuxem jsou způsobeny problémy, které ovlivňují zastaralé hocha virtuálního počítače. Chcete-li tento problém vyřešit, postupujte podle těchto obecných pokynů:

1. Postupujte podle pokynů pro [aktualizaci agenta virtuálního počítače s Linuxem](../virtual-machines/linux/update-agent.md).

   > [!NOTE]
   > *Důrazně doporučujeme* aktualizovat agenta pouze prostřednictvím distribučního úložiště. Nedoporučujeme stahovat kód agenta přímo z GitHubu a aktualizovat ho. Pokud nejnovější agent pro vaši distribuci není k dispozici, obraťte se na podporu distribuce pokyny, jak ji nainstalovat. Pokud chcete vyhledat nejnovějšího agenta, přejděte na stránku [agenta Windows Azure Linux](https://github.com/Azure/WALinuxAgent/releases) v úložišti GitHub.

1. Ujistěte se, že agent Azure běží na virtuálním počítači spuštěním následujícího příkazu:`ps -e`

   Pokud proces není spuštěn, restartujte jej pomocí následujících příkazů:

   - Pro Ubuntu:`service walinuxagent start`
   - Pro ostatní distribuce:`service waagent start`

1. [Konfigurace agenta automatického restartování](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Povolte ochranu virtuálního počítače.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Rozšíření obnovení webu se nepodaří aktualizovat nebo načíst

Stav rozšíření se zobrazuje jako "Prázdný", "NotReady" nebo "Přechod".

#### <a name="solution"></a>Řešení

Odinstalujte rozšíření a znovu restartujte operaci.

Jak toto rozšíření odinstalovat:

1. Na [webu Azure Portal](https://portal.azure.com/)přejděte na virtuální počítač, u kterého dochází k selhání zálohování.
1. Vyberte **Nastavení**.
1. Vyberte **Extensions** (Rozšíření).
1. Vyberte **rozšíření obnovení webu**.
1. Vyberte **možnost Odinstalovat**.

Pokud se pro virtuální počítač S Linuxem nezobrazuje rozšíření VMSnapshot na webu Azure Portal, [aktualizujte agenta Azure Linux .](../virtual-machines/linux/update-agent.md) Pak spusťte ochranu.

Po dokončení těchto kroků způsobí rozšíření přeinstalovat během ochrany.
