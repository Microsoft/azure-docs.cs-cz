---
title: 'Agent zřizování Azure AD Connect: Historie vydání verze | Microsoft Docs'
description: V tomto článku jsou uvedené všechny verze Azure AD Connect agenta zřizování a popisuje nové funkce a opravené problémy.
services: active-directory
documentationcenter: ''
author: cmmdesai
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2ce9549765f6a912b3e95f99d11da20347b82ad8
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71326464"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agent zřizování Azure AD Connect: Historie vydaných verzí
V tomto článku jsou uvedené verze a funkce Azure Active Directory Connect zřizovacího agenta, který byl vydán. Tým Azure AD pravidelně aktualizuje agenta zřizování o nové funkce a funkce. Agent zřizování se aktualizuje automaticky, když se uvolní nová verze. 

Pro vaše agenty doporučujeme povolit automatické aktualizace, abyste měli jistotu, že máte nejnovější funkce a opravy chyb. Microsoft poskytuje přímou podporu pro nejnovější verzi agenta a jednu verzi.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stav verze

9\. září 2019: Vydaná pro automatickou aktualizaci

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Možnost konfigurovat další trasování a protokolování pro problémy s agentem zřizování ladění
* Možnost načíst jenom ty atributy služby Azure AD, které jsou nakonfigurované v mapování, aby se zlepšil výkon synchronizace

### <a name="fixed-issues"></a>Oprava potíží

* Opravili jsme chybu, kterou agent přešel do nereagující na stav, pokud došlo k problémům s chybami připojení Azure AD.
* Opravili jsme chybu, která způsobila problémy při čtení binárních dat z Azure Active Directory
* Opravili jsme chybu, která agentovi selhala při obnovení vztahu důvěryhodnosti se službou Cloud Hybrid identity.

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stav verze

23. ledna 2019: Vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Přepracované zřizovacího agenta a architektury konektoru pro zajištění lepšího výkonu, stability a spolehlivosti 
* Zjednodušená konfigurace agenta zřizování pomocí Průvodce instalací na základě uživatelského rozhraní 
* Přidání podpory pro automatické aktualizace agentů

