---
title: Hrozby - modelování nástroj Microsoft Threat - Azure | Microsoft Docs
description: Stránka kategorie hrozeb pro nástroj Microsoft Threat modelování, obsahující kategorie pro všechny vystavené generována hrozeb.
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 704f9995828866d4d2e4969e3aa922ed1e23c4ea
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23867896"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Nástroje pro modelování hrozeb Microsoft hrozeb

Nástroj modelování hrozeb je element základní životního cyklu pro vývoj zabezpečení Microsoft (SDL). To umožňuje architekty softwaru k identifikaci a zmírnit potenciální potíže se zabezpečením již v rané fázi, když jsou poměrně jednoduché a nákladově efektivní řešení. V důsledku toho výrazně snižuje celkové náklady na vývoj. Také jsme chtěli nástroj s odborníky nesouvisející se zabezpečením na paměti, snadněji modelování hrozeb pro všechny vývojáře tím, že poskytuje jasné pokyny o vytvoření a analýza hrozeb modelů.

> Přejděte  **[nástroj modelování hrozeb](./azure-security-threat-modeling-tool.md)**  a začněte ještě dnes!

Nástroj modelování hrozeb lze zodpovědět určité otázky, jako jsou ty níže:

* Jak můžete změnit útočník ověřování dat?
* Co je vliv, pokud útočník může číst data uživatelského profilu?
* Co se stane, pokud byl odepřen přístup k databázi profilu uživatele?

## <a name="stride-model"></a>STRIDE modelu

Lepší nápovědě formulovali tyto druhy odkazováno otázky, společnost Microsoft používá STRIDE modelu, který rozděluje různých typů hrozeb a zjednodušuje celkového zabezpečení konverzace.

| Kategorie | Popis |
| -------- | ----------- |
| **Falšování identity** | Zahrnuje nelegálního přístupu a pak použití informace o ověřování druhého uživatele, jako je například uživatelské jméno a heslo |
| **Manipulaci** | Zahrnuje škodlivý úpravy dat. Mezi příklady patří neoprávněné změny provedené trvalá data, jako třeba které uchovávat v databázi a změnou dat, jako je toků mezi dvěma počítači otevřete síti, jako je Internet |
| **Popírání odpovědnosti** | Přidružená k uživatelům, kteří odepřít provádění akce bez jiných stran s nějaký způsob, jak prokázat, jinak – například uživatel provede neplatnou operaci v systému, jehož schopnost sledovat činnosti zakázané. Bez Odvolatelnost odkazuje na schopnost systému čítač odvolatelnost hrozby. Uživatel, který zakoupí položku možná muset přihlásit pro položku po přijetí. Dodavatele pak může použít k podepsaného potvrzení jako důkaz, že uživateli zobrazí balíček |
| **Zpřístupnění informací** | Zahrnuje vystavení informace jednotlivcům, kteří nemají co dělat tak, aby měl přístup k němu – například možnost uživatelů číst soubor, který mu přístup k nebo schopnost útočníka čtení dat během přenosu mezi dvěma počítači |
| **Odmítnutí služby** | Útok na dostupnost služby (DoS) odepření služby uživatelům platný – například tím, že webový server dočasně nedostupné nebo nepoužitelné. Je nutné chránit proti určitých typů hrozeb DoS jednoduše ke zlepšení dostupnosti systému a spolehlivost |
| **Zvýšení úrovně oprávnění** | Neprivilegovaný uživatelský získá privilegovaného přístupu a tím má dostatečný přístup k ohrožení nebo zničení celý systém. Zvýšení úrovně oprávnění hrozeb zahrnují tyto situace, ve kterých má útočník efektivně procházejí všechny obrany systému a stane součástí důvěryhodné systému samostatně, skutečně nebezpečnou situaci |

## <a name="next-steps"></a>Další kroky

Pokračujte  **[jejich zmírnění nástroj pro modelování hrozeb](./azure-security-threat-modeling-tool-mitigations.md)**  informace o různých způsobech zmírnit tyto hrozby s Azure.