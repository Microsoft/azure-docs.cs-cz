---
title: Začínáme s agentem virtuálního počítače s Windows
description: Základní informace o agentovi virtuálního počítače se systémem Windows a procesu aktualizace.
author: Sefriend
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 325502255e84e38a39ca5b90ee4126354c0d425b
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98601247"
---
# <a name="get-started-with-the-windows-virtual-desktop-agent"></a>Začínáme s agentem virtuálního počítače s Windows

V rámci služby Virtual Desktop Service v systému Windows existují tři hlavní součásti: klient vzdálené plochy, služba a virtuální počítače. Tyto virtuální počítače jsou živé v předplatném zákazníka, kde je nainstalovaný agent virtuálního počítače s Windows a program pro spouštění agentů. Agent funguje jako zprostředkující služba Communicator mezi službou a virtuálními počítači a umožňuje připojení. Pokud máte proto nějaké problémy s instalací agenta, aktualizací nebo konfigurací, virtuální počítače se nebudou moci připojit ke službě. Zaváděcí program agenta je spustitelný soubor, který načte agenta. 

Tento článek vám poskytne stručný přehled procesu instalace a aktualizace agenta.

>[!NOTE]
>Tato dokumentace není pro agenta FSLogix nebo klientského agenta vzdálené plochy.


## <a name="initial-installation-process"></a>Proces počáteční instalace

Agent virtuálního počítače s Windows je zpočátku nainstalovaný jedním ze dvou způsobů. Pokud zřizujete virtuální počítače v Azure Portal a Azure Marketplace, agent a zaváděcí program agenta se automaticky nainstalují. Pokud vytváříte virtuální počítače pomocí prostředí PowerShell, je nutné ručně stáhnout agenta a soubory. msi nástroje pro spouštění agenta při [vytváření fondu hostitelů virtuálních ploch Windows pomocí prostředí PowerShell](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool). Po instalaci agenta se současně nainstalují i Agent pro monitorování virtuálních počítačů ve Windows a v Ženevě. Komponenta souběžného zásobníku je nutná pro uživatele, aby mohli bezpečně navázat reverzní připojení ze strany serveru na klienta. Agent monitorování v Ženevě monitoruje stav agenta. Všechny tři tyto součásti jsou nezbytné pro zajištění správného připojení koncových uživatelů ke správné funkci.

>[!IMPORTANT]
>Chcete-li úspěšně nainstalovat agenta virtuálního počítače se systémem Windows, zásobník souběžného sdílení a agenta monitorování aplikace Ženeva, je nutné odblokovat všechny adresy URL uvedené v [seznamu požadované adresy URL](safe-url-list.md#virtual-machines). Odblokování těchto adres URL je nutné pro použití služby virtuální plochy systému Windows.

## <a name="agent-update-process"></a>Proces aktualizace agenta

Když bude aktualizace k dispozici, služba Windows Virtual Desktop automaticky aktualizuje agenta. Aktualizace agenta můžou zahrnovat nové funkce nebo opravit předchozí problémy. Po instalaci počáteční verze agenta virtuálního počítače se systémem Windows Agent pravidelně dotazuje službu virtuální plochy systému Windows, aby zjistil, zda je k dispozici novější verze agenta a jeho součástí. Pokud je k dispozici nová verze, program pro spouštění agenta automaticky stáhne nejnovější verzi agenta, souběžného zásobníku a agenta monitorování Ženeva.

>[!NOTE]
>- Když Ženeva Monitoring Agent aktualizuje na nejnovější verzi, stará úloha GenevaTask se nachází a zakáže před vytvořením nové úlohy pro nového agenta monitorování. Starší verze agenta monitorování se neodstraní v případě, že nejnovější verze agenta monitorování má problém, který vyžaduje, aby se oprava vrátila do starší verze. Pokud má nejnovější verze nějaký problém, Starý agent monitorování se znovu povolí, aby pokračoval v doručování dat monitorování. Všechny verze monitorování, které jsou starší než poslední, který jste nainstalovali před aktualizací, se z virtuálního počítače odstraní.
>- Váš virtuální počítač udržuje v čase tři verze souběžného zásobníku. To umožňuje rychlé obnovení, pokud se s aktualizací něco nepovede. Nejstarší verze zásobníku se odebere z virtuálního počítače při každé aktualizaci zásobníku.

Tato instalace aktualizace obvykle na novém virtuálním počítači trvá 2-3 minut a neměla by způsobit ztrátu připojení nebo vypnutí počítače. Tento proces aktualizace se vztahuje na virtuální plochu Windows (Classic) i na nejnovější verzi virtuálního klienta Windows s Azure Resource Manager.

## <a name="next-steps"></a>Další kroky

Teď, když máte lepší znalosti o agentu virtuálních počítačů s Windows, najdete tady některé materiály, které vám můžou usnadnit:

- Pokud se setkáváte s agentem nebo problémy související s připojením, přečtěte si [Průvodce řešením potíží s agentem pro virtuální počítače s Windows](troubleshoot-agent.md).