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
ms.openlocfilehash: de6ef5dedb0779536cfbc1e6a3440e748ff78524
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70862106"
---
# <a name="azure-ad-connect-provisioning-agent-version-release-history"></a>Agent zřizování Azure AD Connect: Historie vydaných verzí
V tomto článku jsou uvedené verze a funkce Azure AD Connect zřizovacího agenta, který byl vydán. Tým Azure AD pravidelně aktualizuje agenta zřizování o nové funkce a funkce. Agenti zřizování se aktualizují automaticky, když se uvolní nová verze. 

Pro vaše agenty doporučujeme povolit automatické aktualizace, abyste měli jistotu, že máte nejnovější funkce a opravy chyb. Microsoft poskytuje přímou podporu pro nejnovější verzi agenta a jednu verzi.

## <a name="11670"></a>1.1.67.0

### <a name="release-status"></a>Stav verze

9\. září 2019: Vydaná pro automatickou aktualizaci

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Možnost konfigurovat další trasování a protokolování pro problémy s agentem zřizování ladění
* Možnost načítat pouze ty atributy služby Active Directory, které jsou konfigurovány v mapování pro zlepšení výkonu synchronizace

### <a name="fixed-issues"></a>Oprava potíží

* Opravili jsme chybu, která v agentovi přešla do nereagující stav, pokud došlo k problémům se selháním připojení AD.
* Opravili jsme chybu, která způsobila problémy při čtení binárních dat ze služby Active Directory.
* Opravili jsme chybu, kdy v agentovi se nepovedlo obnovit vztah důvěryhodnosti se službou Cloud Hybrid identity.

## <a name="11300"></a>1.1.30.0

### <a name="release-status"></a>Stav verze

23. ledna 2019: Vydáno ke stažení

### <a name="new-features-and-improvements"></a>Nové funkce a vylepšení

* Architektura přepracované zřizování agenta & pro lepší výkon, stabilitu a spolehlivost 
* Zjednodušená konfigurace agenta zřizování pomocí Průvodce instalací založenou na uživatelském rozhraní 
* Přidání podpory pro automatické aktualizace agentů

