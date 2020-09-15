---
title: Co je nového u agenta s povolenými servery Azure ARC (Preview)
description: V tomto článku najdete poznámky k verzi pro agenta serverů s podporou Azure ARC (Preview). Pro mnohé z souhrnných problémů existují odkazy na další podrobnosti.
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 3b739401603f6dc18b9f48fb3cb6e28023a051ab
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90532167"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>Co je nového u agenta s povolenými servery Azure ARC (Preview)

Server s podporou Azure ARC (ve verzi Preview) přijímá nepřetržité vylepšení. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb

## <a name="september-2020"></a>Září 2020

Verze: 1,0 (Obecná dostupnost)

### <a name="plan-for-change"></a>Plánovaná změna

- V budoucí aktualizaci služby se odebere podpora pro agenty Preview (všechny verze starší než 1,0).
- Odebrala se podpora záložního koncového bodu `.azure-automation.net` . Pokud máte proxy server, musíte koncový bod přidělit `*.his.arc.azure.com` .
- Pokud je agent připojeného počítače nainstalovaný na virtuálním počítači hostovaném v Azure, nejde rozšíření virtuálních počítačů nainstalovat nebo změnit z prostředku servery s podporou ARC. K tomu je potřeba zabránit konfliktování operací rozšíření prováděných z prostředku **Microsoft. COMPUTE** a **Microsoft. HybridCompute** virtuálního počítače. Použijte prostředek **Microsoft. COMPUTE** pro daný počítač pro všechny operace rozšíření.
- Název konfiguračního procesu hosta se změnil, od *GCD* do *Gcad* v systému Linux a *gcservice* až *gcarcservice* ve Windows.

### <a name="new-feature"></a>Nová funkce

- Přidání `azcmagent logs` Možnosti pro shromáždění informací pro podporu.
- Přidání `azcmagent license` Možnosti pro zobrazení smlouvy EULA.
- Přidání `azcmagent show --json` Možnosti do výstupního stavu agenta v snadno analyzovaném formátu.
- Ve `azcmagent show` výstupu se přidal příznak, který označuje, jestli je server na virtuálním počítači hostovaném v Azure.
- Přidání `azcmagent disconnect --force-local-only` Možnosti pro povolení resetování stavu místního agenta, když služba Azure není dostupná.
- Přidání `azcmagent connect --cloud` Možnosti pro podporu dalších cloudů. V této verzi podporuje služba v době vydání agenta jenom Azure.
- Agent byl lokalizován do jazyků podporovaných v Azure.

### <a name="fixed"></a>Pevný

- Vylepšení kontroly připojení.
- Oprava potíží s nastaveními proxy server, která se při upgradu agenta v systému Linux ztratí.
- Vyřešené problémy při pokusu o instalaci agenta na server se systémem Windows Server 2012 R2.
- Vylepšení spolehlivosti instalace rozšíření

## <a name="august-2020"></a>Srpen 2020

Verze: 0,11

- Tato verze dřív oznámila podporu pro Ubuntu 20,04. Vzhledem k tomu, že některá rozšíření virtuálních počítačů Azure nepodporuje Ubuntu 20,04, odebírá se podpora pro tuto verzi Ubuntu.

- Vylepšení spolehlivosti pro nasazení rozšíření.

### <a name="known-issues"></a>Známé problémy

Pokud používáte starší verzi agenta pro Linux a nakonfigurovali jste ho, aby používal proxy server, musíte po upgradu překonfigurovat nastavení proxy server. Pokud to chcete provést, spusťte příkaz `sudo azcmagent_proxy add http://proxyserver.local:83` .

## <a name="next-steps"></a>Další kroky

Před vyhodnocením nebo povolením serverů s povoleným ARC (ve verzi Preview) na více hybridních počítačích si přečtěte téma [Přehled agenta připojení počítačů](agent-overview.md) , abyste pochopili požadavky, technické podrobnosti o agentovi a metody nasazení.