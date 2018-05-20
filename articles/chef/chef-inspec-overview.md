---
title: Použití InSpec pro automatizaci dodržování předpisů infrastruktury Azure
description: Další informace o použití InSpec rozpoznat problémy v nasazeních Azure
keywords: Azure, chef, devops, virtuální počítače, přehled, automatizovat, inspce
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4193b7fdb3932cbffa2b56b5d7eee6f3b573bd99
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Použití InSpec pro automatizaci dodržování předpisů infrastruktury Azure
[InSpec](https://www.chef.io/inspec/) je bezplatná a open source rozhraní pro testování a auditování aplikace a infrastrukturu. InSpec funguje tak, že porovnáte skutečný stav systému s požadovaný stav, který express ve snadno čitelném a: zápis InSpec kódu. InSpec zjistí porušení a zobrazí zjištění ve formě sestavy, které vám umožňuje řídit nápravy. InSpec můžete použít k ověření stavu virtuální počítače běžící v Azure. Můžete taky InSpec můžete zkontrolovat a ověřit stav prostředků a skupin prostředků v rámci předplatného.

Tento článek popisuje výhody použití InSpec usnadnění zabezpečení a dodržování předpisů v Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Zajistit snadno pochopit a vyhodnotit dodržování předpisů
S InSpec transformovat požadavků do kódu verzí, spustitelný soubor, čitelná pro člověka. To umožňuje uspořádat testů do složení profily, kde definice a přizpůsobení výjimky, podle potřeby.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Rozpoznat problémy s firemního vozového celou a určit jejich prioritu jejich nápravy
Bez agentů InSpec zjistit režimu vám umožní rychle posoudit - škálované - odpovídající úrovni vašeho ohrožení. Předdefinované metadata pro vyhodnocování dopad nebo závažnosti pomáhá určit oblasti, které zaměřit se na pro nápravu.

## <a name="inspect-machines-data-and-new-saas-apis"></a>Zkontrolujte počítačů, dat a nových rozhraní API SaaS
InSpec cloudové funkce dodržování předpisů rozhraní API umožňují nepřetržitě zkontrolujte hrubým a podrobných tvrzení o dodržování předpisů cloudu a sestavy na něm.

## <a name="satisfy-audits"></a>Uspokojení audity
S InSpec reagovat na audit otázky kdykoli – ne jenom v předem stanovených intervalech, jako je čtvrtletní nebo ročně. InSpec umožňuje zadat auditu cyklus zároveň budete vědět, vaše přesné shody postojů, místo právě sledován auditor zjištění.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>Snížit nejednoznačnosti a chybnou komunikaci týkající se pravidla
Dokumenty nechte konfigurace a procesy otevřete k interpretaci. Spustitelný kód odebere konverzace, o co by měl vyhodnocení považuje konkrétní testy záměrem zrušte.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>Přečtěte si rychle mění krajiny hrozbě a dodržování předpisů
InSpec umožňuje zapsat a publikovat kód pro detekci stejný den a zápis nové pravidel v rychlou reakci na nové předpisy. To znamená, že změny v hrozeb a nařízení již roven naléhavých případech.

## <a name="next-steps"></a>Další postup
* [Vytvoření virtuálního počítače s Windows v Azure pomocí Chef](/azure/virtual-machines/windows/chef-automation)