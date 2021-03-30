---
title: Hrozby – Microsoft Threat Modeling Tool – Azure | Microsoft Docs
description: Stránka kategorie hrozeb pro Microsoft Threat Modeling Tool obsahující kategorie pro všechny vystavené vygenerované hrozby.
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
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "68727828"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Microsoft Threat Modeling Tool hrozby

Threat Modeling Tool je základní prvek Microsoft Security Development Lifecycle (SDL). Umožňuje softwarovým architektům včas identifikovat a zmírnit potenciální problémy se zabezpečením, když jsou poměrně jednoduché a nákladově efektivní. V důsledku toho významně snižuje celkové náklady na vývoj. Také jsme navrhli nástroj s odborníky mimo bezpečnostní specialisty, což usnadňuje modelování hrozeb všem vývojářům tím, že poskytuje jasné pokyny k vytváření a analýze modelů hrozeb.

> Navštivte **[Threat Modeling Tool](threat-modeling-tool.md)** a začněte ještě dnes!

Threat Modeling Tool vám pomůže odpovědět na některé otázky, jako jsou ty, které jsou uvedené níže:

* Jak může útočník změnit ověřovací data?
* Jaký je dopad, pokud může útočník přečíst data profilu uživatele?
* Co se stane, když je přístup k databázi profilů uživatelů odepřen?

## <a name="stride-model"></a>Model pro rozteč

K lepšímu seřazení těchto druhů podaných otázek Microsoft používá model pro rozteč, který kategorizuje různé typy hrozeb a zjednodušuje celkové bezpečnostní hovory.

| Kategorie | Popis |
| -------- | ----------- |
| **Falšování identity** | Zahrnuje neoprávněný přístup a následné použití ověřovacích informací jiného uživatele, jako je uživatelské jméno a heslo. |
| **Manipulace** | Zahrnuje škodlivou úpravu dat. Mezi příklady patří neoprávněné změny provedené u trvalých dat, jako je třeba ta, která se uchovávají v databázi, a změna dat při jejich toku mezi dvěma počítači přes otevřenou síť, jako je například Internet. |
| **Popírání odpovědnosti** | Přidruženo k uživatelům, kteří zakazují provedení akce bez dalších smluv, mají-li například možnost prokázat jinak, například uživatel provede neplatnou operaci v systému, který nemá schopnost trasovat zakázané operace. Neodmítnutím se rozumí schopnost systému vyhodnotit hrozby hrozeb. Například uživatel, který kupuje položku, může muset podepsat položku při přijetí. Dodavatel pak může použít podepsaný příjem jako důkaz, že uživatel balíček dostal. |
| **Zveřejňování informací** | Zahrnuje expozici informací jednotlivcům, kteří k nim nemají přístup, například schopnost uživatelů číst soubor, ke kterému nebyli udělen přístup, nebo může útočníkovi umožnit čtení dat při přenosu mezi dvěma počítači. |
| **Odepření služby** | Útoky DoS (Denial of Service) brání službě platným uživatelům – například tím, že webový server bude dočasně nedostupný nebo nepoužitelný. Abyste mohli zlepšit dostupnost a spolehlivost systému, musíte chránit před některými typy hrozeb pro DoS. |
| **Zvýšení oprávnění** | Neprivilegovaný uživatel získá privilegovaný přístup, a proto má dostatečný přístup k zabezpečení nebo zničení celého systému. Zvýšení úrovně hrozeb mezi hrozby zahrnuje situace, kdy útočník účinně provedl všechny obrany systému a se stane součástí samotného důvěryhodného systému, což je skutečně nebezpečná situace. |

## <a name="next-steps"></a>Další kroky

Pokud chcete zjistit různé způsoby, jak tyto hrozby zmírnit pomocí Azure, přejděte k **[Threat Modeling Tool zmírnění](threat-modeling-tool-mitigations.md)** .