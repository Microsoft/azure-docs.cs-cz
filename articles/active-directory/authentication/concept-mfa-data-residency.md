---
title: Zasídlí dat vícefaktorového ověřování Azure AD
description: Seznamte se s informacemi o tom, jaká data osobního a organizačního ověřování Azure AD jsou o vás a na vašich uživatelích a jaká data zůstanou v zemi nebo oblasti původu.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788a666e8ec509bbd29a8dbd503a60b3dddefd6b
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98208348"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Data zasídla a zákaznická data pro vícefaktorové ověřování Azure AD

Zákaznická data jsou uložená v rámci Azure AD v geografickém umístění na základě adresy poskytnuté vaší organizací při přihlášení k odběru online služby Microsoftu, jako je Microsoft 365 a Azure. Informace o tom, kde jsou uložená zákaznická data, najdete v části [kde se nacházejí vaše data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) v centru zabezpečení Microsoftu.

Cloudové ověřování Azure AD s využitím vícefaktorového ověřování a zpracování serveru Azure AD s více fakty ověřování a ukládání některých osobních údajů a dat organizace. Tento článek popisuje, co a kde jsou data uložená.

Služba Azure AD vícefaktorového ověřování má datová centra v USA, Evropě a Asie a Tichomoří. Následující činnosti pocházejí z regionálních Datacenter, s výjimkou případů, kdy je uvedeno níže:

* Vícefaktorové ověřování pomocí telefonních hovorů pochází z Datacenter USA a jsou směrováni globálními poskytovateli.
* Požadavky na ověřování uživatelů pro obecné účely z jiných oblastí, jako je Evropa nebo Austrálie, se aktuálně zpracovávají na základě umístění uživatele.
* Nabízená oznámení používající Microsoft Authenticator aplikace se aktuálně zpracovávají v regionálních datacentrech na základě umístění uživatele.
    * Služby specifické pro dodavatele zařízení, jako je například nabízená oznámení Apple, můžou být mimo umístění uživatele.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Osobní údaje uložené ve službě Azure AD vícefaktorového ověřování

Osobní údaje jsou informace na úrovni uživatele spojené s konkrétní osobou. Následující úložiště dat obsahují osobní údaje:

* Zablokované uživatele
* Obcházení uživatelé
* Žádosti o změnu tokenu zařízení Microsoft Authenticator
* Sestavy aktivit vícefaktorového ověřování
* Microsoft Authenticator aktivace

Tyto informace se uchovávají po dobu 90 dnů.

Vícefaktorové ověřování Azure AD neprotokoluje osobní údaje, jako je uživatelské jméno, telefonní číslo nebo IP adresa, ale existuje *UserObjectId* , který identifikuje vícefaktorové pokusy o ověření pro uživatele. Data protokolu se ukládají po dobu 30 dnů.

### <a name="azure-ad-multifactor-authentication"></a>Vícefaktorového ověřování Azure AD

Pro veřejné cloudy Azure, kromě ověřování Azure B2C, serveru NPS a Windows Server 2016 nebo 2019 AD FS adaptéru se ukládají následující osobní údaje:

| Typ události                           | Typ úložiště dat |
|--------------------------------------|-----------------|
| Token OATH                           | V protokolech vícefaktorového ověřování     |
| Jednosměrná zpráva SMS                          | V protokolech vícefaktorového ověřování     |
| Hlasový hovor                           | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Blokování uživatelů při nahlášení podvodu |
| Oznámení Microsoft Authenticator | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Blokování uživatelů při nahlášení podvodu<br />Žádosti o změnu při Microsoft Authenticator změně tokenu zařízení |

V případě Microsoft Azure Government, Microsoft Azure (Německo) Microsoft Azure provozovaných společností 21Vianet, ověřování Azure B2C, rozšíření serveru NPS a Windows Server 2016 nebo 2019 AD FS adaptér jsou uloženy následující osobní údaje:

| Typ události                           | Typ úložiště dat |
|--------------------------------------|-----------------|
| Token OATH                           | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování |
| Jednosměrná zpráva SMS                          | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování |
| Hlasový hovor                           | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Blokování uživatelů při nahlášení podvodu |
| Oznámení Microsoft Authenticator | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Blokování uživatelů při nahlášení podvodu<br />Žádosti o změnu při Microsoft Authenticator změně tokenu zařízení |

### <a name="multifactor-authentication-server"></a>Server vícefaktorového ověřování

Pokud nasadíte a spustíte vícefaktorového ověřování serveru Azure AD, uloží se následující osobní údaje:

> [!IMPORTANT]
> Od 1. července 2019 už společnost Microsoft nenabízí vícefaktorové ověřovací server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat vícefaktorové ověřování od svých uživatelů, by měli používat vícefaktorové ověřování Azure AD založené na cloudu. Stávající zákazníci, kteří aktivovali vícefaktorové ověřování server před 1. července, budou moci stáhnout nejnovější verzi, budoucí aktualizace a generovat přihlašovací údaje pro aktivaci obvyklým způsobem.

| Typ události                           | Typ úložiště dat |
|--------------------------------------|-----------------|
| Token OATH                           | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování |
| Jednosměrná zpráva SMS                          | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování |
| Hlasový hovor                           | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Blokování uživatelů při nahlášení podvodu |
| Oznámení Microsoft Authenticator | V protokolech vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Blokování uživatelů při nahlášení podvodu<br />Žádosti o změnu při Microsoft Authenticator změně tokenu zařízení |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Data organizace uložená pomocí vícefaktorového ověřování Azure AD

Data organizace jsou informace na úrovni tenanta, které by mohly vystavit konfiguraci nebo nastavení prostředí. Nastavení klienta z následujících Azure Portal ověřovací stránky s více fakty můžou ukládat data organizace, jako jsou mezní hodnoty uzamčení nebo informace o ID volajícího pro požadavky na příchozí ověřování na telefon:

* Uzamčení účtu
* Výstraha podvodů
* Oznámení
* Nastavení telefonního hovoru

A u vícefaktorového ověřování serveru Azure AD můžou následující stránky Azure Portal obsahovat data organizace:

* Nastavení serveru
* Jednorázové přihlášení
* Pravidla ukládání do mezipaměti
* Stav vícefaktorového ověřovacího serveru

## <a name="multifactor-authentication-logs-location"></a>Umístění protokolů vícefaktorového ověřování

Následující tabulka uvádí umístění protokolů služby pro veřejné cloudy.

| Veřejný cloud| Protokoly přihlašování | Sestava o aktivitě vícefaktorového ověřování        | Protokoly služby vícefaktorového ověřování       |
|-------------|--------------|----------------------------------------|------------------------|
| USA          | USA           | USA                                     | USA                     |
| Evropa      | Evropa       | USA                                     | Evropa <sup>2</sup>    |
| Austrálie   | Austrálie    | US<sup>1</sup>                         | Austrálie <sup>2</sup> |

<sup>1</sup> Protokoly kódu OATH se ukládají v Austrálii.

<sup>2</sup> . Hlasové hovory – protokoly služby vícefaktorového ověřování se ukládají do USA.

V následující tabulce je uvedeno umístění pro protokoly služby pro cloudy svrchovan.

| Suverénní cloud                      | Protokoly přihlašování                         | Sestava aktivity vícefaktorového ověřování (včetně osobních údajů)| Protokoly služby vícefaktorového ověřování |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Germany              | Německo                              | USA                            | USA               |
| Microsoft Azure provozovaný společností 21Vianet | Čína                                | USA                            | USA               |
| Cloud pro státní správu Microsoftu           | USA                                   | USA                            | USA               |

Data sestavy s více fakty ověřování obsahují osobní údaje, jako je hlavní název uživatele (UPN) a úplné telefonní číslo.

K provozu služby se používají protokoly služby vícefaktorového ověřování.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jaké informace o uživateli jsou shromažďovány pomocí vícefaktorového ověřování Azure AD založeného na cloudu a serveru Azure AD s více fakty, najdete v tématu [shromažďování uživatelských dat ve službě Azure AD s více fakty ověřování](howto-mfa-reporting-datacollection.md).
