---
title: Rezidence dat azure s vícefaktorovým ověřováním
description: Zjistěte, jaká osobní a organizační data Azure Multi-Factor Authentication ukládají o vás a vašich uživatelích a jaká data zůstávají v zemi původu.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sasubram
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29ce7631c0ce8ab83edc7b9cd31dfe0db3be5d7e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309803"
---
# <a name="data-residency-and-customer-data-for-azure-multi-factor-authentication"></a>Rezidence dat a zákaznická data pro azure multifaktorové ověřování

Zákaznická data jsou uložena službou Azure AD v geografickém umístění na základě adresy poskytnuté vaší organizací při přihlášení k odběru služby Microsoft Online, jako je Office 365 a Azure. Informace o tom, kde jsou uložena zákaznická data, naleznete v centru zabezpečení společnosti Microsoft v části [Kde se data nacházejí.](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located)

Cloudové azure multifaktorové ověřování a azure multifaktorový ověřovací server zpracovávají a ukládají určité množství osobních dat a organizačních dat. Tento článek popisuje, co a kde jsou data uložena.

Následující aktivity vícefaktorového ověřování aktuálně pocházejí z datových center v USA, pokud není uvedeno jinak:

* Dvoufaktorové ověřování pomocí telefonních hovorů nebo SMS obvykle pochází z datových center v USA a jsou směrovány globálními poskytovateli.
    * Požadavky na ověření uživatelů pro obecné účely z jiných oblastí, jako je Evropa nebo Austrálie, jsou aktuálně zpracovávány datovými centry v této oblasti. Jiné události, jako je samoobslužné resetování hesla, události Azure B2C nebo hybridní scénáře pomocí rozšíření NPS nebo adaptéru AD FS, jsou aktuálně zpracovány datovými centry v USA.
* Nabízená oznámení pomocí aplikace Microsoft Authenticator pocházejí z datových center v USA. Kromě toho služby specifické pro dodavatele zařízení může také přijít do hry z různých oblastí.
* Kódy OATH jsou obvykle v současné době ověřovány v USA.
    * Události ověřování uživatelů pro obecné účely, které pocházejí z jiných oblastí, jako je Evropa nebo Austrálie, jsou opět zpracovávány datovými centry v této oblasti. Další události jsou aktuálně zpracovány datovými centry v USA.

## <a name="personal-data-stored-by-azure-multi-factor-authentication"></a>Osobní údaje uložené pomocí azure multifaktorového ověřování

Osobní údaje jsou informace na úrovni uživatele spojené s konkrétní osobou. Následující úložiště dat obsahují osobní údaje:

* Blokovaní uživatelé
* Vynechání uživatelů
* Žádosti o změnu tokenu tokenu zařízení Microsoft Authenticator
* Sestavy aktivit vícefaktorového ověřování
* Aktivace programu Microsoft Authenticator

Tyto informace jsou uchovávány po dobu 90 dnů.

Azure Multi-Factor Authentication nezaznamenává osobní data, jako je uživatelské jméno, telefonní číslo nebo IP adresa, ale existuje *UserObjectId,* který identifikuje pokusy o vícefaktorové ověřování pro uživatele. Data protokolu jsou uložena po dobu 30 dnů.

### <a name="azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication

Pro veřejné cloudy Azure, s výjimkou ověřování Azure B2C, rozšíření NPS a adaptéru AD FS pro Windows Server 2016 nebo 2019, se ukládají následující osobní data:

| Typ události                           | Typ úložiště dat |
|--------------------------------------|-----------------|
| Token OATH                           | V protokolech vícefaktorového ověřování     |
| Jednosměrná SMS                          | V protokolech vícefaktorového ověřování     |
| Hlasový hovor                           | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Blokovaní uživatelé, pokud byl nahlášen podvod |
| Oznámení microsoft authenticator | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Blokovaní uživatelé, pokud byl nahlášen podvod<br />Změna požadavků při změně tokenu zařízení Microsoft Authenticator |

> [!NOTE]
> Úložiště dat sestavy aktivity vícefaktorového ověřování je uloženo ve Spojených státech pro všechny cloudy bez ohledu na oblast, která zpracovává požadavek na ověření. Microsoft Azure Německo, Microsoft Azure provozovaný 21Vianet a Microsoft Government Cloud mají vlastní nezávislá úložiště dat oddělená od úložišť dat v oblasti veřejného cloudu, ale tato data jsou vždy uložena ve Spojených státech.

Pro Microsoft Azure Government, Microsoft Azure Germany, Microsoft Azure provozovaný 21Vianet, ověřování Azure B2C, rozšíření NPS a adaptér Windows Server 2016 nebo 2019 AD FS se ukládají následující osobní data:

| Typ události                           | Typ úložiště dat |
|--------------------------------------|-----------------|
| Token OATH                           | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování |
| Jednosměrná SMS                          | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování |
| Hlasový hovor                           | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Blokovaní uživatelé, pokud byl nahlášen podvod |
| Oznámení microsoft authenticator | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Blokovaní uživatelé, pokud byl nahlášen podvod<br />Změna požadavků při změně tokenu zařízení Microsoft Authenticator |

### <a name="multi-factor-authentication-server"></a>Server Multi-Factor Authentication

Pokud nasadíte a spustíte server Azure Multi-Factor Authentication Server, uloží se následující osobní data:

> [!IMPORTANT]
> července 2019 již společnost Microsoft nebude nabízet vícefaktorový ověřovací server pro nová nasazení. Noví zákazníci, kteří by chtěli od svých uživatelů vyžadovat vícefaktorové ověřování, by měli používat vícefaktorové ověřování Azure na základě cloudu. Stávající zákazníci, kteří aktivovali vícefaktorový ověřovací server před 1.

| Typ události                           | Typ úložiště dat |
|--------------------------------------|-----------------|
| Token OATH                           | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování |
| Jednosměrná SMS                          | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování |
| Hlasový hovor                           | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Blokovaní uživatelé, pokud byl nahlášen podvod |
| Oznámení microsoft authenticator | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Blokovaní uživatelé, pokud byl nahlášen podvod<br />Změna požadavků při změně tokenu zařízení Microsoft Authenticator |

## <a name="organizational-data-stored-by-azure-multi-factor-authentication"></a>Organizační data uložená pomocí azure multifaktorového ověřování

Organizační data jsou informace na úrovni klienta, které by mohly vystavit konfiguraci nebo nastavení prostředí. Nastavení klienta z následujících stránek vícefaktorového ověřování na webu Azure Portal může ukládat data organizace, jako jsou prahové hodnoty uzamčení nebo informace o ID volajícího pro příchozí požadavky na ověření telefonu:

* Uzamčení účtu
* Výstraha podvodů
* Oznámení
* Nastavení telefonního hovoru

A pro Server azure vícefaktorového ověřování mohou následující stránky portálu Azure obsahovat data organizace:

* Nastavení serveru
* Jednorázový obtok
* Pravidla ukládání do mezipaměti
* Stav vícefaktorového ověřovacího serveru

## <a name="log-data-location"></a>Umístění dat protokolu

Kde jsou uloženy informace protokolu závisí na oblasti, které jsou zpracovány v. Většina zeměpisných oblastí má nativní možnosti vícefaktorového ověřování Azure, takže data protokolu se ukládají ve stejné oblasti, která zpracovává požadavek na vícefaktorové ověřování. V zeměpisných oblastech bez nativní podpory azure multi-factor authentication jsou obsluhovány zeměpisnými oblastmi Spojených států nebo Evropy a data protokolu jsou uložena ve stejné oblasti, která zpracovává požadavek na vícefaktorové ověřování.

Některá data protokolu základního ověřování jsou uložena pouze ve Spojených státech. Microsoft Azure Německo a Microsoft Azure provozované společností 21Vianet jsou vždy uloženy v příslušném cloudu. Data protokolu služby Microsoft Government Cloud jsou vždy uložena ve Spojených státech.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jaké informace o uživateli jsou shromažďovány pomocí cloudového vícefaktorového ověřování Azure a serveru Azure Multi-Factor Authentication Server, najdete v [tématu Shromažďování uživatelských dat azure vícefaktorového ověřování](howto-mfa-reporting-datacollection.md).
