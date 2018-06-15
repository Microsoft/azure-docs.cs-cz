---
title: Řešení potíží s Azure Automation Hybrid Runbook Worker
description: Popisují příznaky, příčiny a řešení nejběžnějších problémů hybridní pracovní proces Runbooku ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 7fe0662d8bf3b52c0de94dbc70126eb813883402
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192614"
---
# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>Tipy pro odstraňování potíží pro hybridní pracovní proces Runbooku

Tento článek obsahuje nápovědu k odstraňování potíží chyby setkat s automatizace procesů Hybrid Runbook Worker a navrhne možná řešení jejich řešení.

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>Ukončí úlohy runbooku se stavem pozastaveno

Vaše sada runbook je pozastaven krátce po pokusu o spuštění ho třikrát. Existují podmínky, které mohou přerušit sady runbook nelze úspěšně dokončit a související chybová zpráva neobsahuje žádné další údaje, která udává, proč. Tento článek obsahuje pokyny k odstraňování potíží pro problémy související s selhání spuštění sady runbook hybridní pracovní proces Runbooku.

Pokud není Azure problém řešený v tomto článku, navštivte fórech Azure na [MSDN a Stack Overflow](https://azure.microsoft.com/support/forums/). Problém můžete účtovat na tyto fóra nebo na [ @AzureSupport na Twitteru](https://twitter.com/AzureSupport). Navíc můžete soubor žádost o podporu Azure tak, že vyberete **získat podporu** na [podporu Azure](https://azure.microsoft.com/support/options/) lokality.

### <a name="symptom"></a>Příznaky

Spuštění sady Runbook se nezdaří a Vrácená chyba je "úlohy akci, kterou 'Aktivovat' nelze spustit, protože proces se neočekávaně zastavil. Akce úlohy byl proveden pokus o třikrát."

Existuje několik možných příčin chyby:

1. Hybridní pracovní proces je za proxy nebo brány firewall
2. Sady runbook se nemohou ověřovat se místní prostředky

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Příčina 1: Hybridní pracovní proces Runbooku je za proxy nebo brány firewall

Počítač, na kterém běží hybridní pracovní proces Runbooku na je za serverem brány firewall nebo proxy server a odchozí síťový přístup nesmí být povoleny a správně nakonfigurován.

#### <a name="solution"></a>Řešení

Ověřte, zda že má počítač odchozí přístup k *.azure automation.net na portu 443.

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>2 příčina: Počítač má menší než minimální požadavky na hardware

Počítače se systémem hybridní pracovní proces Runbooku by měl splňovat minimální požadavky na hardware před označením ho k hostování této funkce. V závislosti na využití prostředků jiné procesy na pozadí a kolizí způsobené sady runbook během provádění, jinak počítač stane přetížen a způsobit zpoždění úlohy sady runbook nebo vypršení časových limitů.

#### <a name="solution"></a>Řešení

Nejdřív ověřte, zda počítač určený pro spouštění funkce Hybrid Runbook Worker splňuje minimální požadavky na hardware. Pokud ano, sledujte využití procesoru a paměti k určení všech korelace mezi výkon procesů Hybrid Runbook Worker a Windows. Pokud je paměť nebo zatížení procesoru, může to znamenat potřeba upgradovat nebo přidáním dalších procesorů, případně zvyšte paměti adres problémové místo prostředků a opravte případné chyby. Nebo vyberte jiný výpočtový prostředek, který může podporovat minimální požadavky a škálování při vytížení indikovat, že se o zvýšení nezbytné.

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Příčina 3: Sady Runbook se nemohou ověřovat se místní prostředky

#### <a name="solution"></a>Řešení

Zkontrolujte **Microsoft SMA** v protokolu událostí je odpovídající událost s popis *Win32 proces skončil s kódem [4294967295]*. Příčina této chyby je nebyly nakonfigurované ověřování ve vašich sadách runbook nebo zadaná pověření spustit jako pro skupinu hybridních pracovních procesů. Zkontrolujte [oprávnění sady Runbook](automation-hrw-run-runbooks.md#runbook-permissions) potvrďte jste správně nakonfigurovali ověřování pro vaše sady runbook.

## <a name="next-steps"></a>Další postup

Nápovědu k řešení potíží s další problémy v Automation najdete v tématu [běžných potíží s Azure Automation.](automation-troubleshooting-automation-errors.md)