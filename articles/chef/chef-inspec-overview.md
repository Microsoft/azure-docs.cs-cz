---
title: Použití InSpec pro dodržování předpisů automatizace infrastruktury Azure
description: Další informace o použití InSpec a detekujte problémy ve vašich nasazeních Azure
keywords: Azure, chef, devops, virtuální počítače, přehled, automatizovat, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: e854b140c32773fc5d64e828e7dd40fab1f6ca8d
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332366"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Použití InSpec pro dodržování předpisů automatizace infrastruktury Azure
[InSpec](https://www.chef.io/inspec/) je zdarma a open source architektura pro účely testování a auditování vaše aplikace a infrastrukturu. InSpec funguje tak, že porovnání skutečného stavu vašeho systému s požadovaným stavem, které uvedete ve snadné čtení a snadno zápis InSpec kódu. InSpec rozpozná porušení a zobrazí výsledky ve formě sestavy, ale vám dává kontrolu nad nápravu. InSpec můžete použít k ověření stavu vašich virtuálních počítačů spuštěných v Azure. Můžete také použít InSpec kontrola a ověření stavu prostředků a skupin prostředků v rámci předplatného.

Tento článek popisuje výhody použití InSpec pro usnadnění zabezpečení a dodržování předpisů v Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Bylo možné snadno pochopit a vyhodnotit dodržování předpisů
S InSpec transformovat vaše požadavky do kódu označené verzí, spustitelný soubor, čitelné. To umožňuje uspořádat testy do sestavitelný profily, ve kterém definujete a podle potřeby upravte výjimky.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Zjištění problémů s portfoliem celou a stanovení priorit jejich nápravu
Režim zjišťování bez agenta InSpec vám umožní rychle - ve velkém měřítku – vyhodnotit úroveň ohrožení. Integrovaná metadata pro vyhodnocení dopadu/závažnost pomáhá určit, jaké oblasti se soustředit na na odstranění problému.

## <a name="inspect-machines-data-and-new-saas-apis"></a>Kontrola počítačů, data a nová rozhraní API SaaS
Možnosti dodržování předpisů InSpec cloudové rozhraní API umožňují průběžně provést kontrolní výrazy hrubý a podrobné informace o dodržování předpisů cloudu a sestavy na něm.

## <a name="satisfy-audits"></a>Splňují auditů
S InSpec můžete reagovat auditovat otázek v každém okamžiku – nejen v předurčeném intervalech, například čtvrtletně nebo ročně. InSpec umožňuje zadat cyklu auditů znalost tak přesné shody stav místo právě sledován auditor zjištění.

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>Omezte nejednoznačnost a chybnou komunikaci týkající se pravidel
Dokumenty ponechte konfigurace a otevřete procesy k interpretaci. Spustitelný kód odebere konverzací o co by měl být použit k vyhodnocení a místo toho použití hmatatelnými testy s jasný smysl.

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>Držet krok s rychle se měnící prostředí před internetovými útoky a dodržování předpisů
InSpec umožňuje zapisovat a publikování kódu zjišťování stejného dne a zápis nových pravidel v rychlé odpovědi na nové předpisy. To znamená, že změny v ohrožení nebo právní už rovnat nouzové situace co.

## <a name="next-steps"></a>Další postup
* [Vytvoření virtuálního počítače s Windows v Azure pomocí Chefu](/azure/virtual-machines/windows/chef-automation)
