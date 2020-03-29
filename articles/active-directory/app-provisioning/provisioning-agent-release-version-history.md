---
title: 'Agent zřizování Azure AD Connect: Historie verzí verze | Dokumenty společnosti Microsoft'
description: Tento článek obsahuje seznam všech verzí agenta Azure AD Connect Provisioning Agent a popisuje nové funkce a opravené problémy
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
ms.date: 02/26/2020
ms.subservice: app-provisioning
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 559bca4f5020cebe06be7f24f7af5ec2e94bec0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183240"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agent zřizování Azure AD Connect: Historie verzí vydání
Tento článek uvádí verze a funkce agenta Azure Active Directory Connect Provisioning Agent, které byly vydány. Tým Azure AD pravidelně aktualizuje zřizovací agent s novými funkcemi a funkcemi. Agent zřizování se aktualizuje automaticky při vydání nové verze. 

Společnost Microsoft poskytuje přímou podporu pro nejnovější verzi agenta a jednu verzi před.

## <a name="11960"></a>1.1.96.0

### <a name="release-status"></a>Stav verze

Prosinec 4, 2019: Vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Zahrnuje podporu [zřizování cloudu Azure AD Connect](../cloud-provisioning/what-is-cloud-provisioning.md) pro synchronizaci dat uživatelů, kontaktů a skupin z místní služby Active Directory do Azure AD.


## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stav verze

9. září 2019: Vydáno pro automatickou aktualizaci

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Možnost konfigurace dalšího trasování a protokolování pro problémy s ladicím agentem zřizování
* Možnost načíst pouze ty atributy Azure AD, které jsou nakonfigurované v mapování ke zlepšení výkonu synchronizace

### <a name="fixed-issues"></a>Oprava potíží

* Opravena chyba, ve které agent přešel do stavu nereagujícího, pokud došlo k problémům s chybami připojení služby Azure AD
* Opravena chyba, která způsobovala problémy při čtení binárních dat ze služby Azure Active Directory
* Opravena chyba, kdy se agentovi nepodařilo obnovit důvěryhodnost cloudové hybridní identity

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stav verze

23. ledna 2019: Vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Vylepšená architektura Zřizovacího agenta a konektoru pro lepší výkon, stabilitu a spolehlivost 
* Zjednodušená konfigurace agenta zřizování pomocí Průvodce instalací řízeným ui 
* Přidána podpora pro automatické aktualizace agentů

