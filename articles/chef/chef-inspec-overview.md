---
title: Použití INSPEC pro automatizaci dodržování předpisů vaší infrastruktury Azure
description: Naučte se používat INSPEC k detekci problémů v nasazeních Azure.
keywords: Azure, DevOps, virtuální počítače, přehled, automatizace, INSPEC
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158224"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Použití INSPEC pro automatizaci dodržování předpisů vaší infrastruktury Azure

[INSPEC](https://www.chef.io/inspec/) je open source jazyk od výrobce, který popisuje pravidla dodržování předpisů & zabezpečení, která je možné sdílet mezi softwarovými inženýry, operacemi a bezpečnostními technici. Inspecs funguje porovnáním skutečného stavu vaší infrastruktury s požadovaným stavem, který zadáváte snadno čitelným a snadno čitelným kódem INSPEC. INSPEC detekuje porušení a zobrazuje nálezy ve formě sestavy, ale přináší kontrolu nad nápravou.

Pomocí INSPEC můžete ověřit stav prostředků a skupin prostředků v rámci předplatného, včetně virtuálních počítačů, konfigurací sítě, Azure Active Directory nastavení a dalších.

V tomto článku se dozvíte o výhodách používání INSPEC, které usnadňují zajištění zabezpečení a dodržování předpisů v Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Usnadnění pochopení a vyhodnocení dodržování předpisů

Dokumentace k dodržování předpisů napsaná v tabulkách nebo dokumentech aplikace Word nechává požadavky otevřené ke výkladu. S použitím INSPEC můžete své požadavky transformovat do spustitelného souboru, spustitelného kódu, čitelného pro člověka. Kód nahrazuje konverzace o tom, co by mělo být hodnoceno s využitím hmotného testu s jasným záměrem.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Zjištění problémů v rámci loďstva a stanovení priorit při jejich nápravě

Režim rozpoznávání bez agentů nespecifikuje vám možnost rychle vyhodnotit škálování na úrovni expozice. Vestavěná metadata pro bodování dopadu a závažnosti pomáhají určit, které oblasti se mají soustředit na nápravu. Můžete také rychle napsat pravidla v reakci na nové chyby zabezpečení nebo předpisy a okamžitě je vystavit.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Auditovat virtuální počítače Azure s konfigurací hostů zásad

Azure přímo podporuje použití definic INSPEC pro hosty k auditování virtuálních počítačů Azure prostřednictvím [Konfigurace hosta Azure Policy](/azure/governance/policy/concepts/guest-configuration). Konfigurace hosta vyhodnocuje virtuální počítač se systémem Linux na poskytnutou definici INSPEC pro hosty a oznamuje dodržování předpisů zpětně Azure Policy. Výsledky těchto auditů jsou také hlášeny prostřednictvím protokolů Azure Monitor; povolení výstrah a dalších scénářů automatizace.

## <a name="satisfy-audits"></a>Vyhovět auditům

S neurčenými možnostmi můžete reagovat na otázky týkající se auditu kdykoli, a to nejen v předem určených intervalech, jako je čtvrtletní nebo roční. Průběžným spouštěním nesledovaných testů zadáte cyklus auditu, který bude znát přesnou stav a historii dodržování předpisů, ale nebudete překvapeni zjištění auditora.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Vyzkoušejte INSPEC v Azure Cloud Shell](https://shell.azure.com)