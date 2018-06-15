---
title: Způsoby zmírnění – nástroj Microsoft Threat modelování – Azure | Microsoft Docs
description: Stránka jejich zmírnění pro Microsoft Threat modelování nástroj zvýraznění možná řešení nejvíce ohrožených generována hrozeb.
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
ms.openlocfilehash: 07ef1fd3d81d795c9164741d22b5a689f86bd720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23867980"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Nástroje pro modelování hrozeb Microsoft jejich zmírnění

Nástroj modelování hrozeb je element základní životního cyklu pro vývoj zabezpečení Microsoft (SDL). To umožňuje architekty softwaru k identifikaci a zmírnit potenciální potíže se zabezpečením již v rané fázi, když jsou poměrně jednoduché a nákladově efektivní řešení. V důsledku toho výrazně snižuje celkové náklady na vývoj. Také jsme chtěli nástroj s odborníky nesouvisející se zabezpečením na paměti, snadněji modelování hrozeb pro všechny vývojáře tím, že poskytuje jasné pokyny o vytvoření a analýza hrozeb modelů.

Přejděte  **[nástroj modelování hrozeb](./azure-security-threat-modeling-tool.md)**  a začněte ještě dnes!

## <a name="mitigation-categories"></a>Zmírnění dopadů kategorií

Způsoby zmírnění hrozeb modelování nástroj jsou rozdělené podle rámce zabezpečení webové aplikace, který se skládá z následujících:

| Kategorie | Popis |
| -------- | ----------- |
| **[Auditování a protokolování](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | Kdo to udělal co a kdy? Protokolování a auditování odkazovat na tom, jak vaše aplikace zaznamenává události související se zabezpečením |
| **[Ověřování](./azure-security-threat-modeling-tool-authentication.md)** | Kdo jsi? Ověřování je proces, kde entity prokáže identity jinou entitou, obvykle prostřednictvím přihlašovacích údajů, jako je například uživatelské jméno a heslo |
| **[Autorizace](./azure-security-threat-modeling-tool-authorization.md)** | Co můžete dělat? Autorizace je, jak aplikace nabízí řízení přístupu pro prostředky a operace |
| **[Zabezpečení komunikace](./azure-security-threat-modeling-tool-communication-security.md)** | Kdo jste mluvení do? Zabezpečení komunikace zajistí, že je jako zabezpečené nejblíže veškerá komunikace provést |
| **[Správa konfigurací](./azure-security-threat-modeling-tool-configuration-management.md)** | Kdo aplikaci spustit jako? Je databáze, která připojení k? Jak je spravována aplikace? Jak jsou zabezpečené tato nastavení? Správa konfigurace odkazuje na tom, jak vaše aplikace zpracovává tyto provozní problémy |
| **[Kryptografie](./azure-security-threat-modeling-tool-cryptography.md)** | Jak se udržuje tajné klíče (utajení)? Jak jste kontroly pravopisu systému úmyslnému poškození dat nebo knihovny (integrita)? Jak jste poskytli semen náhodných hodnot, které musí být kryptograficky silnou? Kryptografie odkazuje na tom, jak vaše aplikace vynucuje důvěrnost a integrita |
| **[Správa výjimek](./azure-security-threat-modeling-tool-exception-management.md)** | Při volání metody ve vaší aplikaci nezdaří, jakým způsobem aplikace? Kolik můžete odhalit? Se vrátí informace o chybě popisný koncovým uživatelům? Předáte výjimka cenné informace zpět do volající? Aplikaci řádně nezdaří? |
| **[Ověření vstupu](./azure-security-threat-modeling-tool-input-validation.md)** | Jak poznáte, jestli vstupní aplikace přijímá je platný a bezpečný? Ověření vstupu odkazuje na tom, jak vaše aplikace filtry, scrubs nebo odmítne vstup před další zpracování. Zvažte chovaly vstupu prostřednictvím vstupní body a kódování výstup prostřednictvím ukončovací body. Důvěřujete data ze zdroje jako databáze a sdílené složky? |
| **[Citlivá Data](./azure-security-threat-modeling-tool-sensitive-data.md)** | Jak aplikace zpracovávat citlivá data? Citlivá data odkazují na tom, jak vaše aplikace zpracovává všechna data, která musí být chráněny v paměti, přes síť, nebo v trvalé úložiště |
| **[Správa relací](./azure-security-threat-modeling-tool-session-management.md)** | Jak aplikace zpracování a ochranu uživatelských relací? Relace odkazuje na řadu související interakce mezi uživateli a webové aplikace |

To pomáhá identifikovat:

* Kde jsou vytvářeny mezi nejběžnější chyby
* Kde jsou nejvíce řešitelné vylepšení

V důsledku toho používat tyto kategorie zaměřit a stanovení priorit práce zabezpečení, takže pokud víte, že současných převažujících problémy se zabezpečením, ke kterým došlo v vstupní kategorie ověřování, ověřování a autorizaci, můžete spustit existuje. Další informace najdete v článku  **[tento patentová odkaz](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Další kroky

Navštivte  **[Threat modelování hrozeb nástroj](./azure-security-threat-modeling-tool-threats.md)**  Další informace o kategoriích threat nástroj používá ke generování možné návrhu hrozeb.