---
title: Zmírnění rizik – nástroj pro modelování hrozeb Microsoftu – Azure | Dokumentace Microsoftu
description: Stránka způsoby zmírnění rizik pro Microsoft Threat modelování nástroj zvýraznění možná řešení nejvíce ohrožených generována hrozby.
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
ms.openlocfilehash: 24aa49fd4ccccda372d2632ef4aee22bd5cb2bf6
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359132"
---
# <a name="microsoft-threat-modeling-tool-mitigations"></a>Nástroj pro modelování hrozeb Microsoftu způsoby zmírnění rizik

Nástroj pro modelování hrozeb je základní prvek z Microsoft Security Development Lifecycle (SDL). To umožňuje softwarové architekty k identifikaci a zmírnění potenciální potíže se zabezpečením již v rané fázi, když jsou poměrně snadné a hospodárné řešení. V důsledku toho výrazně snižuje celkové náklady na vývoj. Kromě toho jsme navrhovali nástroj s odborníky nesouvisí se zabezpečením na paměti, usnadnění modelování hrozeb pro všechny vývojáře tím, že poskytuje jasné pokyny k vytváření a analýze modely hrozeb.

Přejděte **[nástroj pro modelování hrozeb](./azure-security-threat-modeling-tool.md)** a začněte ještě dnes!

## <a name="mitigation-categories"></a>Zmírnění dopadů kategorie

Nástroj pro modelování hrozeb způsoby zmírnění rizik jsou rozdělené podle rámce zabezpečení webové aplikace, který se skládá z následujících akcí:

| Kategorie | Popis |
| -------- | ----------- |
| **[Auditování a protokolování](./azure-security-threat-modeling-tool-auditing-and-logging.md)** | Kdo jste, co a kdy? Auditování a protokolování odkazovat jak vaše aplikace zaznamenává události související se zabezpečením |
| **[Ověřování](./azure-security-threat-modeling-tool-authentication.md)** | Kdo jste? Ověřování je proces, ve kterém entity prokáže vaše oprávnění identitě z druhé entity, obvykle pomocí přihlašovacích údajů, jako je například uživatelské jméno a heslo |
| **[Autorizace](./azure-security-threat-modeling-tool-authorization.md)** | Co můžete dělat? Autorizace je, jak vaše aplikace poskytuje řízení přístupu pro prostředky a operace |
| **[Zabezpečení komunikace](./azure-security-threat-modeling-tool-communication-security.md)** | Kdo jste mluvení do? Zabezpečení komunikace se zajistí, že se veškerá komunikace se provádí co nevíce zabezpečený |
| **[Správa konfigurace](./azure-security-threat-modeling-tool-configuration-management.md)** | Který běží vaše aplikace jako? Jeho databáze, která připojení k? Jak je spravována vaší aplikace? Jak jsou tato nastavení zabezpečené? Správa konfigurace odkazuje na způsob, jakým aplikace zpracovává tyto provozní problémy |
| **[Cryptography](./azure-security-threat-modeling-tool-cryptography.md)** | Jak jsou udržování tajné kódy (utajení)? Jak se máš kontroly pravopisu úmyslnému poškození dat nebo knihovny (integrita)? Jak vám poskytovala rychlosti pro náhodné hodnoty, které musí být kryptograficky silnou? Kryptografie odkazuje na tom, jak vaše aplikace vynucuje důvěrnost a integrita |
| **[Správa výjimek](./azure-security-threat-modeling-tool-exception-management.md)** | Pokud selže volání metody ve vaší aplikaci, co aplikace dělá? Kolik uvedete? Vrátit chybové informace pro koncové uživatele? Předat zpět do volajícího výjimka cenné informace? Selže bez výpadku aplikace? |
| **[Ověření vstupu](./azure-security-threat-modeling-tool-input-validation.md)** | Jak víte, že vstup, který vaše aplikace obdrží je platný a bezpečný? Ověření vstupu odkazuje na jak vaše aplikace filtry, scrubs nebo odmítne vstup před další zpracování. Vezměte v úvahu omezení vstupu prostřednictvím vstupní body a kódování výstup prostřednictvím výstupech. Důvěřujete data ze zdrojů, jako jsou databáze a sdílené složky? |
| **[Citlivá Data](./azure-security-threat-modeling-tool-sensitive-data.md)** | Jak vaše aplikace zpracovává citlivá data? Citlivá data představují způsob, jakým aplikace zpracovává všechna data, která musí být chráněn v paměti, přes síť, nebo v trvalé úložiště |
| **[Správa relací](./azure-security-threat-modeling-tool-session-management.md)** | Jak probíhá aplikace zpracování a ochranu uživatelských relací? Relace odkazuje na řadu souvisejících interakcí mezi uživatelem a webové aplikace |

Toto pomáhá identifikovat:

* Kde probíhají většina běžných chyb
* Kde jsou nejužitečnější vylepšení

V důsledku toho pomocí těchto kategorií fokus a stanovení priorit práce zabezpečení, takže pokud víte, že dojde k nejrozšířenější problémy se zabezpečením v kategoriích vstupní ověřování, ověřování a autorizace, můžete začít existuje. Další informace najdete v článku  **[tento patentové odkaz](https://www.google.com/patents/US7818788)**

## <a name="next-steps"></a>Další postup

Navštivte **[hrozby nástroj pro modelování hrozeb](./azure-security-threat-modeling-tool-threats.md)** Další informace o kategoriích hrozeb nástroj používá k vygenerování možného ohrožení.