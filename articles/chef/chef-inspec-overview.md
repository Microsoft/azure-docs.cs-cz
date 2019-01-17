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
ms.openlocfilehash: a5de2ca04a193f97a2a65a043f744abb8e0ea758
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359217"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>Použití InSpec pro dodržování předpisů automatizace infrastruktury Azure
[InSpec](https://www.chef.io/inspec/) je Chefu open-source jazyk pro popis security and compliance pravidla, která lze sdílet mezi softwarové inženýry, operace a zabezpečení technikům. InSpec funguje tak, že porovnání skutečného stavu infrastruktury s požadovaným stavem, které uvedete ve snadné čtení a snadno zápis InSpec kódu. InSpec rozpozná porušení a zobrazí výsledky ve formě sestavy, ale vám dává kontrolu nad nápravu.

InSpec můžete použít k ověření stavu prostředků a skupin prostředků v rámci předplatného, včetně virtuálních počítačů, síťových konfigurací, nastavení služby Azure Active Directory a další.

Tento článek popisuje výhody použití InSpec pro usnadnění zabezpečení a dodržování předpisů v Azure.

## <a name="make-compliance-easy-to-understand-and-assess"></a>Bylo možné snadno pochopit a vyhodnotit dodržování předpisů
Dokumentace k dodržování předpisů zapisují do tabulek nebo dokumentů aplikace Word ponechat požadavky výkladu. S InSpec transformovat vaše požadavky do kódu označené verzí, spustitelný soubor, čitelné. Kód nahradí konverzací o co by měl být použit k vyhodnocení a místo toho použití hmatatelnými testy s jasný smysl.

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>Zjištění problémů s portfoliem celou a stanovení priorit jejich nápravu
InSpec uživatele bez agentů zjišťování režim vám umožní rychle - ve velkém měřítku – vyhodnotit úroveň ohrožení. Integrovaná metadata pro vyhodnocení dopadu/závažnost pomáhá určit, jaké oblasti se soustředit na na odstranění problému. Můžete také rychle psát pravidla v reakci na nová ohrožení zabezpečení a nařízení a je okamžitě zavést.

## <a name="satisfy-audits"></a>Splňují auditů
S InSpec můžete reagovat auditovat otázek v každém okamžiku – nejen v předurčeném intervalech, například čtvrtletně nebo ročně. Běží nepřetržitě InSpec testy, zadejte cyklu auditů znalost přesné shody stav a historie, místo právě sledován auditor zjištění.

## <a name="next-steps"></a>Další postup

* Zkuste InSpec ve službě Azure Cloud Shell [ ![spustit Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "spustit Cloud Shell")](https://shell.azure.com)
