---
title: Služba Azure AD Multi-Factor Authentication zasídlí dat
description: Přečtěte si, jaká data v osobním a organizačním prostředí Azure AD Multi-Factor Authentication ukládá o vás a o vašich uživatelích a jaká data zůstanou v zemi nebo oblasti původu.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/11/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788512db242bf1a1c6f18ffc0ee773bd3372aa42
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97355862"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multi-factor-authentication"></a>Data o sídle a zákaznických datech pro Azure AD Multi-Factor Authentication

Zákaznická data jsou uložená v rámci Azure AD v geografickém umístění na základě adresy poskytnuté vaší organizací při přihlášení k odběru online služby Microsoftu, jako je Microsoft 365 a Azure. Informace o tom, kde jsou uložená zákaznická data, najdete v části [kde se nacházejí vaše data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v centru zabezpečení Microsoftu.

Cloudová Multi-Factor Authentication služby Azure AD a Azure Multi-Factor Authentication Server a ukládají určité množství osobních údajů a dat organizace. Tento článek popisuje, co a kde jsou data uložená.

Služba Azure AD Multi-Factor Authentication obsahuje datová centra v USA, Evropě a Asie a Tichomoří. Následující činnosti pocházejí z regionálních Datacenter, s výjimkou případů, kdy je uvedeno níže:

* Multi-Factor Authentication využívající telefonní hovory pocházející z Datacenter USA a jsou směrováni globálními poskytovateli.
* Požadavky na ověřování uživatelů pro obecné účely z jiných oblastí, jako je Evropa nebo Austrálie, se aktuálně zpracovávají na základě umístění uživatele.
* Nabízená oznámení používající Microsoft Authenticator aplikace se aktuálně zpracovávají v regionálních datacentrech na základě umístění uživatele.
    * Služby specifické pro dodavatele zařízení, jako je například nabízená oznámení Apple, můžou být mimo umístění uživatele.

## <a name="personal-data-stored-by-azure-ad-multi-factor-authentication"></a>Osobní údaje uložené Multi-Factor Authentication Azure AD

Osobní údaje jsou informace na úrovni uživatele spojené s konkrétní osobou. Následující úložiště dat obsahují osobní údaje:

* Zablokované uživatele
* Obcházení uživatelé
* Žádosti o změnu tokenu zařízení Microsoft Authenticator
* Sestavy aktivit Multi-Factor Authentication
* Microsoft Authenticator aktivace

Tyto informace se uchovávají po dobu 90 dnů.

Služba Azure AD Multi-Factor Authentication neprotokoluje osobní údaje, jako je uživatelské jméno, telefonní číslo nebo IP adresa, ale existuje *UserObjectId* , který identifikuje Multi-Factor Authentication pokusů o přihlášení uživatelů. Data protokolu se ukládají po dobu 30 dnů.

### <a name="azure-ad-multi-factor-authentication"></a>Vícefaktorové ověřování Azure AD

Pro veřejné cloudy Azure, kromě ověřování Azure B2C, serveru NPS a Windows Server 2016 nebo 2019 AD FS adaptéru se ukládají následující osobní údaje:

| Typ události                           | Typ úložiště dat |
|--------------------------------------|-----------------|
| Token OATH                           | V protokolech Multi-Factor Authentication     |
| Jednosměrná zpráva SMS                          | V protokolech Multi-Factor Authentication     |
| Hlasový hovor                           | V protokolech Multi-Factor Authentication<br />Úložiště dat sestavy Multi-Factor Authentication aktivit<br />Blokování uživatelů při nahlášení podvodu |
| Oznámení Microsoft Authenticator | V protokolech Multi-Factor Authentication<br />Úložiště dat sestavy Multi-Factor Authentication aktivit<br />Blokování uživatelů při nahlášení podvodu<br />Žádosti o změnu při Microsoft Authenticator změně tokenu zařízení |

> [!NOTE]
> Úložiště dat sestavy Multi-Factor Authentication aktivita je uloženo v USA pro všechny cloudy, bez ohledu na oblast, která žádost o ověření zpracovává. Microsoft Azure (Německo), Microsoft Azure provozovaný společností 21Vianet a Cloud pro státní správu Microsoftu mají svá vlastní nezávislá úložiště dat odděleně od úložišť dat oblasti veřejného cloudu, ale tato data se vždycky ukládají do USA. Tato úložiště dat obsahují osobní údaje, například hlavní název uživatele (UPN) a úplné telefonní číslo. 

V případě Microsoft Azure Government, Microsoft Azure (Německo) Microsoft Azure provozovaných společností 21Vianet, ověřování Azure B2C, rozšíření serveru NPS a Windows Server 2016 nebo 2019 AD FS adaptér jsou uloženy následující osobní údaje:

| Typ události                           | Typ úložiště dat |
|--------------------------------------|-----------------|
| Token OATH                           | V protokolech Multi-Factor Authentication<br />Úložiště dat sestavy Multi-Factor Authentication aktivit |
| Jednosměrná zpráva SMS                          | V protokolech Multi-Factor Authentication<br />Úložiště dat sestavy Multi-Factor Authentication aktivit |
| Hlasový hovor                           | V protokolech Multi-Factor Authentication<br />Úložiště dat sestavy Multi-Factor Authentication aktivit<br />Blokování uživatelů při nahlášení podvodu |
| Oznámení Microsoft Authenticator | V protokolech Multi-Factor Authentication<br />Úložiště dat sestavy Multi-Factor Authentication aktivit<br />Blokování uživatelů při nahlášení podvodu<br />Žádosti o změnu při Microsoft Authenticator změně tokenu zařízení |

### <a name="multi-factor-authentication-server"></a>Server Multi-Factor Authentication

Pokud nasadíte a spustíte Azure Multi-Factor Authentication Server, ukládají se následující osobní údaje:

> [!IMPORTANT]
> Od 1. července 2019 už společnost Microsoft nebude nabízet Multi-Factor Authentication Server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat službu Multi-Factor Authentication od uživatelů, by měli používat cloudovou Multi-Factor Authentication služby Azure AD. Stávající zákazníci, kteří si aktivovali Multi-Factor Authentication Server před 1. července, budou moci stáhnout nejnovější verzi, budoucí aktualizace a generovat přihlašovací údaje pro aktivaci jako obvykle.

| Typ události                           | Typ úložiště dat |
|--------------------------------------|-----------------|
| Token OATH                           | V protokolech Multi-Factor Authentication<br />Úložiště dat sestavy Multi-Factor Authentication aktivit |
| Jednosměrná zpráva SMS                          | V protokolech Multi-Factor Authentication<br />Úložiště dat sestavy Multi-Factor Authentication aktivit |
| Hlasový hovor                           | V protokolech Multi-Factor Authentication<br />Úložiště dat sestavy Multi-Factor Authentication aktivit<br />Blokování uživatelů při nahlášení podvodu |
| Oznámení Microsoft Authenticator | V protokolech Multi-Factor Authentication<br />Úložiště dat sestavy Multi-Factor Authentication aktivit<br />Blokování uživatelů při nahlášení podvodu<br />Žádosti o změnu při Microsoft Authenticator změně tokenu zařízení |

## <a name="organizational-data-stored-by-azure-ad-multi-factor-authentication"></a>Data organizace uložená pomocí Multi-Factor Authentication Azure AD

Data organizace jsou informace na úrovni tenanta, které by mohly vystavit konfiguraci nebo nastavení prostředí. Nastavení klienta z následujících Azure Portal Multi-Factor Authentication stránky můžou ukládat podniková data, jako jsou prahové hodnoty uzamčení nebo informace o ID volajícího pro požadavky na příchozí ověřování na telefon:

* Uzamčení účtu
* Výstraha podvodů
* Oznámení
* Nastavení telefonního hovoru

A pro Azure Multi-Factor Authentication Server můžou následující Azure Portal stránky obsahovat data organizace:

* Nastavení serveru
* Jednorázové přihlášení
* Pravidla ukládání do mezipaměti
* Stav Multi-Factor Authentication Server

## <a name="log-data-location"></a>Umístění dat protokolu

Kde jsou uloženy informace protokolu, závisí na tom, v jaké oblasti jsou zpracovávány. Většina geografických oblastí má nativní možnosti služby Azure AD Multi-Factor Authentication, takže data protokolu se ukládají ve stejné oblasti, která zpracovává žádost o Multi-Factor Authentication. V zeměpisných oblastech bez podpory Azure AD Multi-Factor Authentication jsou geografické oblasti USA nebo Evropa uloženy ve stejné oblasti, která zpracovává požadavek Multi-Factor Authentication.

Některá data protokolu základního ověřování se ukládají pouze do USA. Microsoft Azure (Německo) a Microsoft Azure provozované společností 21Vianet jsou vždy uložené v jejich příslušném cloudu. Data v cloudovém protokolu Microsoftu pro státní správu se vždycky ukládají do USA.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jaké informace o uživatelích shromažďuje cloudová Multi-Factor Authentication Azure AD a Azure Multi-Factor Authentication Server, najdete v článku [shromažďování uživatelských dat v Azure ad Multi-Factor Authentication](howto-mfa-reporting-datacollection.md).
