---
title: Co je nového u agenta serverů s podporou ARC Azure
description: Tento článek obsahuje poznámky k verzi pro agenta serverů s podporou ARC Azure. Pro mnohé z souhrnných problémů existují odkazy na další podrobnosti.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 5322a92bfc67d9f80c2271a3ef9b5626455e9ad7
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94445281"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Co je nového u agenta serverů s podporou ARC Azure

Server s podporou ARC Azure, který je připojený agentem počítače, obdrží vylepšení průběžně. V tomto článku najdete informace o tom, jak se můžete seznámit s nejnovějším vývojem.

- Nejnovější verze
- Známé problémy
- Opravy chyb

## <a name="november-2020"></a>Listopad 2020

### <a name="fixed"></a>Pevný

Vyřešil se problém, kdy může dojít ke ztrátě konfigurace proxy serveru po upgradu na distribucí na bázi ot./min..

## <a name="october-2020"></a>Říjen 2020

### <a name="fixed"></a>Pevný

- Pevný skript proxy, který zpracovává alternativní umístění souboru jednotek démona GC.
- Změny spolehlivosti agenta GuestConfig.
- Podpora agenta GuestConfig pro oblast US Gov – Virginie.
- Rozšíření agenta GuestConfig hlásí zprávy, aby byly pro případ selhání podrobnější.

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

Před vyhodnocením nebo povolením serverů s povoleným obloukem na více hybridních počítačích si Projděte [Přehled připojení agenta připojeného počítače](agent-overview.md) , abyste porozuměli požadavkům, technickým podrobnostem o agentovi a metodám