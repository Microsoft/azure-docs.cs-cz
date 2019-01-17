---
title: Hrozby – modelování nástroj Microsoft Threat – Azure | Dokumentace Microsoftu
description: Stránka kategorie před internetovými útoky pro nástroj pro modelování hrozeb Microsoftu, obsahující kategorie pro všechny vystavené generována hrozby.
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
ms.openlocfilehash: fd7c5fd929163dc7fcd22fbb045dee0fe3070394
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359030"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Nástroj Microsoft Threat modelování hrozeb

Nástroj pro modelování hrozeb je základní prvek z Microsoft Security Development Lifecycle (SDL). To umožňuje softwarové architekty k identifikaci a zmírnění potenciální potíže se zabezpečením již v rané fázi, když jsou poměrně snadné a hospodárné řešení. V důsledku toho výrazně snižuje celkové náklady na vývoj. Kromě toho jsme navrhovali nástroj s odborníky nesouvisí se zabezpečením na paměti, usnadnění modelování hrozeb pro všechny vývojáře tím, že poskytuje jasné pokyny k vytváření a analýze modely hrozeb.

> Přejděte **[nástroj pro modelování hrozeb](./azure-security-threat-modeling-tool.md)** a začněte ještě dnes!

Umožňuje nástroj pro modelování hrozeb můžete nalézt odpovědi na některé otázky, jako je následující:

* Jak může útočník změnit ověřovací data?
* Jaký bude dopad, pokud útočník může číst data uživatelského profilu?
* Co se stane, když uživatel profilové databáze byl odepřen přístup?

## <a name="stride-model"></a>STRIDE modelu

Pro lepší nápovědu formulovali tyto druhy odkázáno dotazy, společnost Microsoft používá STRIDE modelu, který rozděluje různé typy hrozeb a zjednodušuje celkové zabezpečení konverzace.

| Kategorie | Popis |
| -------- | ----------- |
| **Falšování identity** | Zahrnuje neoprávněně přístupu a pak pomocí informací o ověřování jiného uživatele, jako je například uživatelské jméno a heslo |
| **Manipulace** | Zahrnuje škodlivé změny data. Mezi příklady patří neoprávněné změny trvalá data, jako například, které uloženy v databázi a změnu dat, jak probíhá mezi dvěma počítači otevřete sítě, jako je Internet |
| **odmítnutí** | Přidružená k uživatelům, kteří odepřít provést akci bez jiných stran s žádným způsobem prokázat, že jinak – například uživatel provede neplatnou operaci v systému, který nemá schopnost trasování zakázané operace. Nepopiratelnost odkazuje na schopnost systému na ně odvolatelnost čítače. Například uživatel, který nákupy položky může mít pro přihlášení pro položku přijetí. Dodavatele pak může použít k příjmu podepsaný prokázat, že uživatel nepřijal balíček |
| **Zpřístupnění informací** | Zahrnuje možnost vidět informace, které osobám, které jsou by neměl mít přístup k němu – například možnost uživatelů ke čtení, které jsou nebyly udělen přístup k souboru nebo schopnost útočníka číst data přenášená mezi dvěma počítači |
| **Útok DoS** | Útoky na dostupnost služby (DoS) odepření služby platným uživatelům – například tím, že webový server dočasně nedostupné nebo nepoužitelné. Je nutné chránit proti určité typy hrozeb DoS jednoduše ke zlepšení dostupnosti systému a spolehlivost |
| **Zvýšení úrovně oprávnění** | Neprivilegovaný uživatel získá privilegovaného přístupu a tím má dostatečný přístup k ohrožení bezpečnosti nebo zničení celý systém. Zvýšení úrovně oprávnění hrozby zahrnují tyto situace, ve kterých má útočník efektivně procházejí všechna ochrany systému a se stanou součástí důvěryhodné samotný systém, skutečně nebezpečné situace |

## <a name="next-steps"></a>Další postup

Přejít k **[zmírnění rizik nástroj pro modelování hrozeb](./azure-security-threat-modeling-tool-mitigations.md)** další různé způsoby, jak můžete zmírnit tyto hrozby s využitím Azure.