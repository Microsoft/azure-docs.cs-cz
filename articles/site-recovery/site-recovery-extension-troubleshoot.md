---
title: 'Řešení potíží s selhání agenta služby Azure Site Recovery: hosta stavu agenta není k dispozici | Microsoft Docs'
description: Příznaky, příčiny a řešení Azure Site Recovery chyb související s agenta a rozšíření
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.date: 05/02/2018
ms.author: asgang
ms.openlocfilehash: 9bfe181b2271f4e8af6f43e1728167712dade8ee
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33777590"
---
# <a name="troubleshoot-azure-site-recovery-extension-failures-issues-with-the-agent-or-extension"></a>Řešení potíží s selhání rozšíření Azure Site Recovery: problémy s agenta nebo rozšíření

Tento článek obsahuje kroky řešení potíží, které vám mohou pomoci vyřešit Azure Site Recovery chyby související s agenta virtuálního počítače a rozšíření.


## <a name="azure-site-recovery-extension-time-out"></a>Azure Site Recovery rozšíření časového limitu  

Chybová zpráva: "úloha vypršel při sledování pro spuštění operace rozšíření"<br>
Kód chyby: "151076"

 Azure Site Recovery nainstalujte rozšíření virtuálního počítače jako součást úlohu povolení ochrany. Některé z následujících podmínek může zabránit ochranu z se aktivuje a selhání úlohy. Proveďte následující kroky řešení potíží a poté operaci:

**Příčina 1: [je agent nainstalován ve virtuálním počítači, ale jeho reagovat (pro virtuální počítače Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Příčina 2: [agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Příčina 3: [rozšíření Site Recovery se nepodařilo aktualizovat nebo načíst](#the-site-recovery-extension-fails-to-update-or-load)**  

Chybová zpráva: "předchozí lokality obnovení rozšíření trvá operace déle, než se očekávalo."<br>
Kód chyby: "150066"<br>

**Příčina 1: [je agent nainstalován ve virtuálním počítači, ale jeho reagovat (pro virtuální počítače Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Příčina 2: [agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
**Příčina 3: [stav rozšíření Site Recovery je nesprávný](#the-site-recovery-extension-fails-to-update-or-load)**  

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Ochrana se nezdaří, protože agent virtuálního počítače je reagovat

Chybová zpráva: "úloha vypršel při sledování pro spuštění operace rozšíření."<br>
Kód chyby: "151099"<br>

Této chybě může dojít, pokud agent Azure hosta ve virtuálním počítači není ve stavu Připraveno.
Můžete zkontrolovat stav agenta hosta Azure v [portál Azure](https://portal.azure.com/). Přejděte k virtuálnímu počítači, který chcete chránit a zkontrolujte stav "virtuálního počítače > Nastavení > Vlastnosti > Stav agenta". Ve většině případů bude stav agenta po restartování virtuálního počítače připravena. Ale pokud restartování není možné možnost nebo se stále setkávají problém, proveďte následující kroky řešení potíží.

**Příčina 1: [je agent nainstalován ve virtuálním počítači, ale jeho reagovat (pro virtuální počítače Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)**    
**Příčina 2: [agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  


Chybová zpráva: "úloha vypršel při sledování pro spuštění operace rozšíření."<br>
Kód chyby: "151095"<br>

Tato situace nastat při stará verze agenta na počítači systému Linux. Proveďte následující krok řešení potíží.<br>
  **Příčina 1: [agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)**  
## <a name="causes-and-solutions"></a>Příčiny a řešení

### <a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent byl nainstalován ve virtuálním počítači, ale je reagovat (pro virtuální počítače Windows)

#### <a name="solution"></a>Řešení
Agent virtuálního počítače může dojít k poškození nebo služba může byla zastavena. Opětovné instalaci agenta virtuálního počítače pomáhá získat nejnovější verzi. Pomáhá také restartovat komunikace se službou.

1. Určit, zda "Windows Azure hostovaného agenta služby" běží v služby virtuálních počítačů (services.msc). Pokuste se službu restartovat "Windows Azure agenta hosta služby".    
2. Pokud není viditelná v služby v Ovládacích panelech, službu Windows Azure hostovaného agenta, přejděte na **programy a funkce** k určení, zda je nainstalována služba Windows agenta hosta.
4. Pokud se zobrazí v agentovi hosta Windows Azure **programy a funkce**, odinstalujte agenta hosta systému Windows.
5. Stáhněte a nainstalujte [nejnovější verzi MSI agenta](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Musí mít oprávnění správce k dokončení instalace.
6. Ověřte, že služby Windows Azure hostovaného agenta se zobrazí v služby.
7. Restartujte úlohu ochrany.

Také ověřte, že [je nainstalované rozhraní Microsoft .NET 4.5](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ve virtuálním počítači. Rozhraní .NET 4.5 je vyžadována pro agenta virtuálního počítače ke komunikaci se službou.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent nainstalovaný ve virtuálním počítači je zastaralý (pro virtuální počítače s Linuxem)

#### <a name="solution"></a>Řešení
Většina související s agenta nebo rozšíření selhání pro virtuální počítače s Linuxem jsou způsobeny problémy, které ovlivňují zastaralé agenta virtuálního počítače. Chcete-li vyřešit tento problém, postupujte podle následujících obecných pokynů:

1. Postupujte podle pokynů pro [aktualizace agenta virtuálního počítače s Linuxem](../virtual-machines/linux/update-agent.md).

 > [!NOTE]
 > Jsme *důrazně doporučujeme* aktualizovat agenta pouze prostřednictvím distribuční úložiště. Nedoporučujeme stáhne kód agenta přímo z Githubu a jeho aktualizaci. Pokud nejnovější verzi agenta pro distribuční není k dispozici, obraťte distribuční podporu pokyny o tom, jak ji nainstalovat. Chcete-li vyhledat nejnovější agenta, přejděte na [Windows Azure Linux agent](https://github.com/Azure/WALinuxAgent/releases) stránky v úložišti GitHub.

2. Ujistěte se, že Azure agent běží ve virtuálním počítači tak, že spustíte následující příkaz: `ps -e`

 Pokud proces není spuštěná, restartujte ji pomocí následujících příkazů:

 * Pro Ubuntu: `service walinuxagent start`
 * Pro ostatní distribuce: `service waagent start`

3. [Konfigurace automatického restartování agenta](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
4. Povolení ochrany virtuálního počítače.



### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Rozšíření Site Recovery se nepodařilo aktualizovat nebo načíst
Pokud je stav rozšíření "prázdný ', 'NotReady' nebo přechod.

#### <a name="solution"></a>Řešení

Odinstalovat rozšíření a restartujte operaci opakujte.

Chcete-li odinstalovat rozšíření:

1. V [portál Azure](https://portal.azure.com/), přejděte k virtuálnímu počítači, kde dochází k selhání zálohování.
2. Vyberte **nastavení**.
3. Vyberte **rozšíření**.
4. Vyberte **lokality obnovení rozšíření**.
5. Vyberte **odinstalovat**.

Pro virtuální počítač s Linuxem, pokud rozšíření VMSnapshot nezobrazuje na webu Azure portal [aktualizovat Azure Linux Agent](../virtual-machines/linux/update-agent.md), a poté spusťte ochranu. 

Dokončení tohoto postupu způsobí, že rozšíření nutné přeinstalovat během ochrany.


