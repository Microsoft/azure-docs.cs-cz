---
title: Použití inspec pro automatizaci dodržování předpisů infrastruktury Azure
description: Přečtěte si, jak pomocí inspec zjistit problémy ve vašich nasazeních Azure
keywords: azure, chef, devops, virtuální stroje, přehled, automatizace, inspec
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: 2531277eb1aa6048c93240031652e09582409e56
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74158224"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Použití inspec pro automatizaci dodržování předpisů infrastruktury Azure

[InSpec](https://www.chef.io/inspec/) je open source jazyk šéfkuchaře pro popis pravidel dodržování předpisů & zabezpečení, které lze sdílet mezi softwarovými inženýry, operacemi a bezpečnostními inženýry. InSpec funguje tak, že porovnává skutečný stav infrastruktury s požadovaným stavem, který vyjadřujete v snadno čitelném a snadno čitelném kódu InSpec. InSpec detekuje porušení a zobrazuje zjištění ve formě sestavy, ale dává vám kontrolu nad nápravou.

InSpec můžete použít k ověření stavu prostředků a skupin prostředků v předplatném, včetně virtuálních počítačů, konfigurace sítě, nastavení služby Azure Active Directory a další.

Tento článek popisuje výhody použití InSpec usnadnit zabezpečení a dodržování předpisů v Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Usnadnit pochopení a posouzení dodržování předpisů

Dokumentace k kompatibilitě napsaná v tabulkách nebo dokumentech aplikace Word ponechává požadavky otevřené interpretaci. S InSpec, můžete transformovat své požadavky do verzí, spustitelný, čitelný pro člověka kód. Kód nahrazuje konverzace o tom, co by mělo být posuzováno ve prospěch hmatatelných testů s jasným záměrem.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Detekce problémů v celém vozovém parku a stanovení priorit jejich nápravy

Režim detekce bez agentů inSpec umožňuje rychle posoudit - ve velkém měřítku - úroveň expozice. Vestavěná metadata pro vyhodnocování dopadu a závažnosti pomáhají určit, na které oblasti se mají zaměřit při nápravě. Můžete také rychle napsat pravidla v reakci na nové chyby zabezpečení nebo předpisy a okamžitě je zavést.

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>Audit virtuálních počítačů Azure pomocí konfigurace hosta zásad

Azure přímo podporuje použití definic Chef InSpec k auditování virtuálních počítačů Azure prostřednictvím [konfigurace hosta zásad Azure](/azure/governance/policy/concepts/guest-configuration). Konfigurace hosta vyhodnotí virtuální počítač Linux u poskytované definice Chef InSpec a hlásí dodržování předpisů zpět prostřednictvím zásad Azure. Výsledky těchto auditů jsou také hlášeny prostřednictvím protokolů Azure Monitor; povolení výstrah a dalších scénářů automatizace.

## <a name="satisfy-audits"></a>Uspokojit audity

S InSpec můžete odpovědět na auditní otázky kdykoli – nejen v předem určených intervalech, například čtvrtletně nebo ročně. Neustálým spouštěním testů InSpec zadáte auditní cyklus s vědomím přesného stavu dodržování předpisů a historie, místo abyste byli překvapeni zjištěními auditora.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Vyzkoušejte InSpec v prostředí Azure Cloud Shell](https://shell.azure.com)