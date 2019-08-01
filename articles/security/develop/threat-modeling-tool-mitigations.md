---
title: Zmírnění rizik – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Stránka zmírňující rizika pro Microsoft Threat Modeling Tool zvýraznění možných řešení pro nejohroženější vygenerované hrozby.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 989d479df4ddfad3511c8b1138c59e87f631d1de
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68621244"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Omezení Microsoft Threat Modeling Tool

Threat Modeling Tool je základním prvkem Microsoft SDL (Security Development Lifecycle). Umožňuje softwarovým architektům včas identifikovat a zmírnit potenciální problémy se zabezpečením, když jsou poměrně jednoduché a nákladově efektivní. V důsledku toho významně snižuje celkové náklady na vývoj. Také jsme navrhli nástroj s odborníky mimo bezpečnostní specialisty, což usnadňuje modelování hrozeb všem vývojářům tím, že poskytuje jasné pokyny k vytváření a analýze modelů hrozeb.

Navštivte **[Threat Modeling Tool](threat-modeling-tool.md)** a začněte ještě dnes!

## <a name="mitigation-categories"></a>Kategorie zmírnění

Omezení Threat Modeling Tool jsou zařazená do kategorií podle rámce zabezpečení webových aplikací, který se skládá z následujících součástí:

| Kategorie | Popis |
| -------- | ----------- |
| **[Auditování a protokolování](threat-modeling-tool-auditing-and-logging.md)** | Kdo co a kdy? Auditování a protokolování: Přečtěte si, jak vaše aplikace zaznamenává události související se zabezpečením. |
| **[Přihlašovací](threat-modeling-tool-authentication.md)** | Kdo jste? Ověřování je proces, při kterém entita prokáže identitu jiné entity, obvykle prostřednictvím přihlašovacích údajů, jako je uživatelské jméno a heslo. |
| **[Udělován](threat-modeling-tool-authorization.md)** | Co můžete dělat? Autorizace je způsob, jakým vaše aplikace poskytuje řízení přístupu pro prostředky a operace. |
| **[Zabezpečení komunikace](threat-modeling-tool-communication-security.md)** | Na koho se mluvíte? Zabezpečení komunikace zajišťuje, že veškerá komunikace je co možná nejbezpečnější. |
| **[Správa konfigurace](threat-modeling-tool-configuration-management.md)** | Na koho vaše aplikace běží? K jakým databázím se připojuje? Jak vaše aplikace spravuje? Jak jsou tato nastavení zabezpečená? Správa konfigurace odkazuje na to, jak vaše aplikace zpracovává tyto provozní problémy. |
| **[Cryptography](threat-modeling-tool-cryptography.md)** | Jak si udržuje tajné kódy (důvěrnost)? Jak si nemůžete manipulovat s daty nebo knihovnami (integrity)? Jak poskytujete semena pro náhodné hodnoty, které musí být kryptograficky silné? Kryptografie odkazuje na to, jak vaše aplikace vynutila důvěrnost a integritu. |
| **[Správa výjimek](threat-modeling-tool-exception-management.md)** | Co vaše aplikace udělá při neúspěchu volání metody v aplikaci? Kolik se vám odhalí? Vrátíte koncovým uživatelům popisné informace o chybách? Předáte cenné informace o výjimkách zpět volajícímu? Selže vaše aplikace korektně? |
| **[Ověřování vstupu](threat-modeling-tool-input-validation.md)** | Jak zjistíte, že vstup, který vaše aplikace obdrží, je platný a bezpečný? Ověřování vstupu odkazuje na to, jak vaše aplikace filtruje, vynechává nebo odmítá vstup před dalším zpracováním. Zvažte omezení vstupu přes vstupní body a kódování výstupu prostřednictvím bodů výstupu. Důvěřujete jim data ze zdrojů, jako jsou databáze a sdílené složky? |
| **[Citlivá data](threat-modeling-tool-sensitive-data.md)** | Jak vaše aplikace zpracovává citlivá data? Citlivá data označují, jak vaše aplikace zpracovává všechna data, která musí být chráněná buď v paměti, v síti, nebo v trvalých úložištích. |
| **[Správa relací](threat-modeling-tool-session-management.md)** | Jak vaše aplikace zpracovává a chrání uživatelské relace? Relace odkazuje na řadu souvisejících interakcí mezi uživatelem a webovou aplikací. |

Pomůže vám to identifikovat:

* Kde jsou nejběžnější chyby
* Kde jsou co nejvíc akcí vylepšení

V důsledku toho se tyto kategorie používají k zaměření a stanovení priorit práce se zabezpečením, takže pokud víte, že k nejvyšším problémům se zabezpečením dochází ve kategoriích ověřování vstupu, ověřování a autorizace, můžete tam začít. Další informace najdete na  **[tomto patentovém odkazu](https://www.google.com/patents/US7818788) .**

## <a name="next-steps"></a>Další postup

Další informace o kategoriích hrozeb, které nástroj používá ke generování možných hrozeb návrhu, najdete v **[Threat Modeling Tool hrozeb](threat-modeling-tool-threats.md)** .