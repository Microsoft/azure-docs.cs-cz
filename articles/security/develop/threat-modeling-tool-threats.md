---
title: Hrozby – Nástroj pro modelování hrozeb společnosti Microsoft – Azure | Dokumenty společnosti Microsoft
description: Stránka kategorie hrozeb pro nástroj Microsoft Threat Modeling Tool obsahující kategorie pro všechny vystavené generované hrozby.
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
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727828"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Hrozby nástroje Microsoft Pro modelování hrozeb

Nástroj pro modelování hrozeb je základním prvkem životního cyklu vývoje zabezpečení společnosti Microsoft (SDL). Umožňuje softwarovým architektům včas identifikovat a zmírnit potenciální problémy se zabezpečením, pokud jsou relativně snadné a nákladově efektivní řešení. V důsledku toho výrazně snižuje celkové náklady na vývoj. Také jsme navrhli nástroj s ohledem na odborníky, kteří nezabezpečují zabezpečení, což usnadňuje modelování hrozeb všem vývojářům tím, že poskytujete jasné pokyny k vytváření a analýze modelů hrozeb.

> Navštivte **[nástroj pro modelování hrozeb,](threat-modeling-tool.md)** abyste mohli začít ještě dnes!

Nástroj pro modelování hrozeb vám pomůže odpovědět na určité otázky, například na ty níže:

* Jak může útočník změnit ověřovací data?
* Jaký je dopad, pokud útočník může číst data profilu uživatele?
* Co se stane, pokud je přístup odepřen do databáze profilu uživatele?

## <a name="stride-model"></a>Model STRIDE

Aby vám společnost Microsoft lépe pomohla formulovat tyto druhy otázek, používá model STRIDE, který kategorizuje různé typy hrozeb a zjednodušuje celkové konverzace o zabezpečení.

| Kategorie | Popis |
| -------- | ----------- |
| **Falšování identity** | Zahrnuje nelegálně přístup a potom pomocí ověřovacích informací jiného uživatele, jako je uživatelské jméno a heslo |
| **Falšování** | Zahrnuje škodlivé změny dat. Mezi příklady patří neoprávněné změny trvalých dat, například uchovávané v databázi, a změna dat při jejich toku mezi dvěma počítači v otevřené síti, například v Síti Internet. |
| **Odmítnutí** | Přidružené s uživateli, kteří popírají provedení akce, aniž by jiné strany mají nějaký způsob, jak prokázat opak – například uživatel provádí nezákonnou operaci v systému, který nemá možnost sledovat zakázané operace. Neodvolatelnost se týká schopnosti systému čelit hrozbám odmítnutí. Uživatel, který si zakoupí položku, může být například po příjmu nucen podepsat položku. Dodavatel pak může použít podepsanou účtenku jako důkaz, že uživatel obdržel balíček. |
| **Zpřístupnění informací** | Zahrnuje vystavení informací jednotlivcům, kteří k nim nemají přístup – například schopnost uživatelů číst soubor, ke kterému jim nebyl udělen přístup, nebo schopnost narušitele číst data při přenosu mezi dvěma počítači |
| **Útok DoS** | Útoky typu DoS (Denial of Service) odepírají službu platným uživatelům– například tím, že webový server je dočasně nedostupný nebo nepoužitelný. Musíte se chránit před určitými typy hrozeb DoS jednoduše zlepšit dostupnost a spolehlivost systému |
| **Zvýšení oprávnění** | Neprivilegovaný uživatel získá privilegovaný přístup a tím má dostatečný přístup k ohrožení zabezpečení nebo zničení celého systému. Zvýšení hrozeb oprávnění zahrnuje situace, kdy útočník účinně pronikl do všech systémových obranných systémů a stal se součástí samotného důvěryhodného systému, což je skutečně nebezpečná situace. |

## <a name="next-steps"></a>Další kroky

Pokračujte k **[nástroji pro modelování hrozeb,](threat-modeling-tool-mitigations.md)** abyste zjistili různé způsoby, jak tyto hrozby zmírnit pomocí Azure.