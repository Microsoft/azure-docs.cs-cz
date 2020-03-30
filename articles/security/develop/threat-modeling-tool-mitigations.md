---
title: Skutečnosti snižující závažnost rizika – Nástroj pro modelování hrozeb společnosti Microsoft – Azure | Dokumenty společnosti Microsoft
description: Stránka skutečnostisnižující závažnost rizika pro nástroj Microsoft Threat Modeling Tool zvýrazňuje možná řešení nejexponovanějších generovaných hrozeb.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 748d10b994080b667885e5d0d5f4d688269e86ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68728031"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Zmírnění rizik nástroje Microsoft Threat Modeling Tool

Nástroj pro modelování hrozeb je základním prvkem životního cyklu vývoje zabezpečení společnosti Microsoft (SDL). Umožňuje softwarovým architektům včas identifikovat a zmírnit potenciální problémy se zabezpečením, pokud jsou relativně snadné a nákladově efektivní řešení. V důsledku toho výrazně snižuje celkové náklady na vývoj. Také jsme navrhli nástroj s ohledem na odborníky, kteří nezabezpečují zabezpečení, což usnadňuje modelování hrozeb všem vývojářům tím, že poskytujete jasné pokyny k vytváření a analýze modelů hrozeb.

Navštivte **[nástroj pro modelování hrozeb,](threat-modeling-tool.md)** abyste mohli začít ještě dnes!

## <a name="mitigation-categories"></a>Kategorie zmírnění rizik

Nástroj pro modelování hrozeb jsou rozděleny do kategorií podle rámce zabezpečení webové aplikace, který se skládá z následujících položek:

| Kategorie | Popis |
| -------- | ----------- |
| **[Auditování a protokolování](threat-modeling-tool-auditing-and-logging.md)** | Kdo co a kdy udělal? Auditování a protokolování odkazuje na to, jak aplikace zaznamenává události související se zabezpečením |
| **[Ověřování](threat-modeling-tool-authentication.md)** | Kdo jsi? Ověřování je proces, při kterém entita prokáže identitu jiné entity, obvykle prostřednictvím pověření, jako je uživatelské jméno a heslo. |
| **[Autorizace](threat-modeling-tool-authorization.md)** | Co můžete dělat? Autorizace je způsob, jakým aplikace poskytuje ovládací prvky přístupu pro prostředky a operace |
| **[Zabezpečení komunikace](threat-modeling-tool-communication-security.md)** | S kým to mluvíš? Zabezpečení komunikace zajišťuje, že veškerá vykonaná komunikace je co nejbezpečnější |
| **[Správa konfigurace](threat-modeling-tool-configuration-management.md)** | Za koho vaše aplikace běží? Ke kterým databázím se připojuje? Jak se vaše aplikace podává? Jak jsou tato nastavení zabezpečena? Správa konfigurace označuje, jak vaše aplikace řeší tyto provozní problémy |
| **[Kryptografie](threat-modeling-tool-cryptography.md)** | Jak udržujete tajemství (důvěrnost)? Jak jste falšování dat nebo knihoven (integrity)? Jak poskytujete semena pro náhodné hodnoty, které musí být kryptograficky silné? Kryptografie označuje, jak vaše aplikace vynucuje důvěrnost a integritu |
| **[Správa výjimek](threat-modeling-tool-exception-management.md)** | Když volání metody ve vaší aplikaci selže, co provede vaše aplikace? Kolik toho odhalíš? Vracíte koncovým uživatelům popisné informace o chybách? Předáte informace o cenných výjimkách zpět volajícímu? Selhat vaše aplikace řádně? |
| **[Ověření vstupu](threat-modeling-tool-input-validation.md)** | Jak víte, že vstup, který vaše aplikace obdrží, je platný a bezpečný? Ověření vstupu odkazuje na to, jak aplikace filtruje, drhne nebo odmítá vstup před dalším zpracováním. Zvažte omezení vstupu prostřednictvím vstupních bodů a kódování výstupu prostřednictvím výstupních bodů. Důvěřujete datům ze zdrojů, jako jsou databáze a sdílené složky? |
| **[Citlivá data](threat-modeling-tool-sensitive-data.md)** | Jak vaše aplikace zpracovává citlivá data? Citlivá data označují způsob, jakým aplikace zpracovává všechna data, která musí být chráněna v paměti, v síti nebo v trvalých úložištích. |
| **[Správa relací](threat-modeling-tool-session-management.md)** | Jak vaše aplikace zpracovává a chrání relace uživatelů? Relace odkazuje na řadu souvisejících interakcí mezi uživatelem a webovou aplikací. |

To vám pomůže identifikovat:

* Kde jsou nejčastější chyby
* Kde jsou nejnavátější zlepšení

V důsledku toho můžete tyto kategorie použít k zaměření a prioritu práce zabezpečení, takže pokud víte, že nejrozšířenější problémy se zabezpečením dochází v kategoriích ověřování, ověřování a autorizace vstupu, můžete začít tam. Pro více informací navštivte ** [tento patent odkaz](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Další kroky

Navštivte **[hrozby pro modelování hrozeb,](threat-modeling-tool-threats.md)** kde se dozvíte více o kategoriích hrozeb, které nástroj používá ke generování možných hrozeb návrhu.