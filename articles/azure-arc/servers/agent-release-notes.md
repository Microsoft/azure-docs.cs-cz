---
title: Co je nového u agenta serverů s podporou ARC Azure
description: Tento článek obsahuje poznámky k verzi pro agenta serverů s podporou ARC Azure. Mnohé z mnoha souhrnných problémů jsou odkazy na další podrobnosti.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: ecff23225f4d482cc1e9a4f7b7724c8ffe0a1d73
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109063"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Co je nového u agenta serverů s podporou ARC Azure

Server s podporou ARC Azure, který je připojený agentem počítače, obdrží vylepšení průběžně. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb

## <a name="march-2021"></a>Březen 2021

Verze 1,4

## <a name="new-feature"></a>Nová funkce

- Byla přidána podpora privátních koncových bodů, který je aktuálně ve verzi omezené verze Preview.
- Rozbalený seznam ukončovacích kódů pro azcmagent.
- Parametry konfigurace agenta se teď dají číst ze souboru s `--config` parametrem.

## <a name="fixed"></a>Pevný

Kontroly síťových koncových bodů jsou teď rychlejší.

## <a name="december-2020"></a>Prosinec 2020

Verze: 1,3

### <a name="new-feature"></a>Nová funkce

Byla přidána podpora pro systém Windows Server 2008 R2.

### <a name="fixed"></a>Pevný

Vyřešil se problém, který brání úspěšné instalaci rozšíření vlastních skriptů v systému Linux.

## <a name="november-2020"></a>Listopad 2020

Verze: 1,2

### <a name="fixed"></a>Pevný

Vyřešil se problém, kdy může dojít ke ztrátě konfigurace proxy serveru po upgradu na distribucí na bázi ot./min..

## <a name="october-2020"></a>Říjen 2020

Version: 1.1

### <a name="fixed"></a>Pevný

- Pevný skript proxy, který zpracovává alternativní umístění souboru jednotek démona GC.
- Změny spolehlivosti agenta GuestConfig.
- Podpora agenta GuestConfig pro oblast US Gov – Virginie.
- Pokud dojde k selhání, GuestConfig se zprávy o rozšíření agenta v podrobnostech.

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
- Přidání `azcmagent connect --cloud` Možnosti pro podporu jiných cloudů. V této verzi podporuje služba v době vydání agenta jenom Azure.
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

Pokud používáte starší verzi agenta pro Linux a je nakonfigurované na použití proxy server, budete muset po upgradu překonfigurovat nastavení proxy server. Pokud to chcete provést, spusťte příkaz `sudo azcmagent_proxy add http://proxyserver.local:83` .

## <a name="next-steps"></a>Další kroky

Před vyhodnocením nebo povolením serverů s povoleným obloukem na více hybridních počítačích si Projděte [Přehled připojení agenta připojeného počítače](agent-overview.md) , abyste porozuměli požadavkům, technickým podrobnostem o agentovi a metodám