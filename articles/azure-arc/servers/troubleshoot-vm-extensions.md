---
title: Řešení potíží s rozšířením virtuálních počítačů s povolenými servery Azure ARC
description: V tomto článku se dozvíte, jak řešit problémy s rozšířeními virtuálních počítačů Azure, která vznikají u serverů s podporou ARC Azure.
ms.date: 09/24/2020
ms.topic: conceptual
ms.openlocfilehash: 799e5c8ec00a894c6a54c64494edd8f259faf2dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100584663"
---
# <a name="troubleshoot-arc-enabled-servers-vm-extension-issues"></a>Řešení potíží s rozšířením virtuálních počítačů s povolenými ARC

Tento článek poskytuje informace o řešení potíží a řešení problémů, ke kterým může dojít při pokusu o nasazení nebo odebrání rozšíření virtuálních počítačů Azure na serverech s podporou ARC. Obecné informace najdete v tématu [Správa a používání rozšíření virtuálních počítačů Azure](./manage-vm-extensions.md).

## <a name="general-troubleshooting"></a>Obecné řešení potíží

Data o stavu nasazení rozšíření lze načíst z Azure Portal.

Následující kroky pro řešení potíží se vztahují na všechna rozšíření virtuálních počítačů.

1. Pokud chcete zkontrolovat protokol hostovaného agenta, podívejte se na aktivitu při zřizování rozšíření `%SystemDrive%\ProgramData\GuestConfig\ext_mgr_logs` pro Windows a v části pro Linux v `/var/lib/GuestConfig/ext_mgr_logs` .

2. Další podrobnosti v systému Windows najdete v protokolech rozšíření pro konkrétní rozšíření `%SystemDrive%\ProgramData\GuestConfig\extension_logs\<Extension>` . Výstup rozšíření se zaznamená do souboru pro každé rozšíření nainstalované v systému Linux `/var/lib/GuestConfig/extension_logs` .

3. Podívejte se na oddíly řešení potíží v dokumentaci ke konkrétnímu rozšíření pro kódy chyb, známé problémy atd. Další informace o řešení potíží pro jednotlivá rozšíření najdete v části **řešení potíží a podpora** v tématu Přehled tohoto rozšíření. To zahrnuje Popis chybových kódů zapsaných do protokolu. Články rozšíření jsou propojeny v [tabulce rozšíření](manage-vm-extensions.md#extensions).

4. Podívejte se na systémové protokoly. Vyhledejte další operace, které mohly být v konfliktu s rozšířením, například dlouhodobě běžící instalace jiné aplikace, která vyžadovala výhradní přístup správce balíčků.

## <a name="troubleshooting-specific-extension-scenarios"></a>Řešení potíží s konkrétní příponou

### <a name="vm-insights"></a>Přehledy virtuálních počítačů

- Když povolíte službu VM Insights pro server s podporou ARC Azure, nainstaluje se závislost a Agent Log Analytics. V pomalých počítačích nebo v jednom s pomalým síťovým připojením je možné během procesu instalace zobrazit časové limity. Společnost Microsoft přebírá kroky, které tuto situaci provedou v agentovi připojeného počítače, aby bylo možné tuto podmínku vylepšit. V provizorním případě může být opakování instalace úspěšné.

### <a name="log-analytics-agent-for-linux"></a>Agent Log Analytics pro Linux

- Agent Log Analytics verze 1.13.9 (odpovídající verze rozšíření je 1.13.15) nesprávně označuje nahraná data pomocí ID prostředku serveru s podporou ARC Azure. I když se protokoly odesílají do služby, když se pokusíte zobrazit data z vybraného povoleného serveru po výběru **protokolů** nebo **přehledů**, nevrátí se žádná data. Data můžete zobrazit spuštěním dotazů z protokolů Azure Monitor nebo z Azure Monitor pro virtuální počítače, která jsou vymezena v pracovním prostoru.

- Agent Log Analytics pro Linux v současné době nepodporuje některé distribuce. Agent vyžaduje, aby byly nainstalovány další závislosti, včetně Pythonu 2. Seznamte se s maticí podpory a požadavky [tady](../../azure-monitor/agents/agents-overview.md#supported-operating-systems).

- Kód chyby 52 ve stavové zprávě značí chybějící závislost. Další informace o tom, která závislost chybí, najdete ve výstupu a v protokolech.

- Pokud se instalace nepovede, přečtěte si část věnované **potížím a podpoře** v tématu Přehled rozšíření. Ve většině případů je ve stavové zprávě obsažen kód chyby. V případě agenta Log Analytics pro Linux jsou [zde](../../virtual-machines/extensions/oms-linux.md#troubleshoot-and-support)popsané stavové zprávy společně s obecnými informacemi o odstraňování potíží pro toto rozšíření virtuálního počítače.

## <a name="next-steps"></a>Další kroky

Pokud tady nevidíte svůj problém nebo nemůžete problém vyřešit, zkuste další podporu vyzkoušet u některého z následujících kanálů:

- Získejte odpovědi od odborníků na Azure prostřednictvím [Microsoft Q&A](/answers/topics/azure-arc.html).

- Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) oficiálního Microsoft Azure účtu pro zlepšení prostředí pro zákazníky. Podpora Azure spojuje komunitu Azure s odpověďmi, podporou a odborníky.

- Zasouborové incidenty podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/)a vyberte **získat podporu**.
