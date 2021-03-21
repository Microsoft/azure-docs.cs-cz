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
ms.openlocfilehash: 7381ab62eb39c555c6b4eb34f150fc71bea1f10f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561460"
---
# <a name="data-residency-and-customer-data-for-azure-ad-multifactor-authentication"></a>Data zasídla a zákaznická data pro vícefaktorové ověřování Azure AD

Azure Active Directory (Azure AD) ukládá zákaznická data v geografickém umístění na základě adresy, kterou organizace poskytuje při přihlášení k odběru online služby Microsoftu, jako je třeba Microsoft 365 nebo Azure. Informace o tom, kde jsou uložená zákaznická data, najdete v tématu [kde se nacházejí vaše data?](https://www.microsoft.com/trustcenter/privacy/where-your-data-is-located) na webu Microsoft Trust Center.

Vícefaktorové ověřování Azure AD založené na cloudu a zpracování vícefaktorového ověřování serveru Azure a ukládání osobních údajů a dat organizace. Tento článek popisuje, co a kde jsou data uložená.

Služba Azure AD vícefaktorového ověřování obsahuje datová centra v USA, Evropě a Asie a Tichomoří. Následující činnosti pocházejí z regionálních Datacenter, s výjimkou případů, kdy je uvedeno níže:

* Telefonní hovory s více fakty pocházejí z USA datacentra a jsou směrovány globálními poskytovateli.
* Požadavky na ověření uživatele pro obecné účely z jiných oblastí se aktuálně zpracovávají na základě umístění uživatele.
* Nabízená oznámení, která používají aplikaci Microsoft Authenticator jsou aktuálně zpracovávána v místních datových centrech na základě umístění uživatele. Služby zařízení specifické pro dodavatele, například Apple Push Notification Service, mohou být mimo umístění uživatele.

## <a name="personal-data-stored-by-azure-ad-multifactor-authentication"></a>Osobní údaje uložené ve službě Azure AD vícefaktorového ověřování

Osobní údaje jsou informace na úrovni uživatele, která je přidružená ke konkrétní osobě. Následující úložiště dat obsahují osobní údaje:

* Zablokované uživatele
* Obcházení uživatelé
* Žádosti o změnu tokenu zařízení Microsoft Authenticator
* Sestavy aktivit vícefaktorového ověřování
* Microsoft Authenticator aktivace

Tyto informace se uchovávají po dobu 90 dnů.

Vícefaktorové ověřování Azure AD neprotokoluje osobní údaje, jako jsou uživatelská jména, telefonní čísla nebo IP adresy. *UserObjectId* však identifikuje pokusy o ověření pro uživatele. Data protokolu se ukládají po dobu 30 dnů.

### <a name="data-stored-by-azure-ad-multifactor-authentication"></a>Data uložená vícefaktorového ověřováním Azure AD

Pro veřejné cloudy Azure, kromě Azure AD B2C ověřování, rozšíření serveru NPS a adaptéru Active Directory Federation Services (AD FS) (AD FS) s Windows serverem 2016 nebo 2019 jsou uloženy následující osobní údaje:

| Typ události                           | Typ úložiště dat |
|--------------------------------------|-----------------|
| Token OATH                           | Protokoly vícefaktorového ověřování     |
| Jednosměrná zpráva SMS                          | Protokoly vícefaktorového ověřování     |
| Hlasový hovor                           | Protokoly vícefaktorového ověřování<br/>Úložiště dat sestavy aktivity vícefaktorového ověřování<br/>Zablokované uživatelé (Pokud se nahlásil podvod) |
| Oznámení Microsoft Authenticator | Protokoly vícefaktorového ověřování<br/>Úložiště dat sestavy aktivity vícefaktorového ověřování<br/>Zablokované uživatelé (Pokud se nahlásil podvod)<br/>Žádosti o změnu při změně tokenu Microsoft Authenticator zařízení |

V případě Microsoft Azure Government, Microsoft Azure (Německo) Microsoft Azure provozovaných společností 21Vianet, Azure AD B2C ověřování, rozšíření serveru NPS a Windows Server 2016 nebo 2019 AD FS adaptéru jsou uloženy následující osobní údaje:

| Typ události                           | Typ úložiště dat |
|--------------------------------------|-----------------|
| Token OATH                           | Protokoly vícefaktorového ověřování<br/>Úložiště dat sestavy aktivity vícefaktorového ověřování |
| Jednosměrná zpráva SMS                          | Protokoly vícefaktorového ověřování<br/>Úložiště dat sestavy aktivity vícefaktorového ověřování |
| Hlasový hovor                           | Protokoly vícefaktorového ověřování<br/>Úložiště dat sestavy aktivity vícefaktorového ověřování<br/>Zablokované uživatelé (Pokud se nahlásil podvod) |
| Oznámení Microsoft Authenticator | Protokoly vícefaktorového ověřování<br/>Úložiště dat sestavy aktivity vícefaktorového ověřování<br/>Zablokované uživatelé (Pokud se nahlásil podvod)<br/>Žádosti o změnu při změně tokenu Microsoft Authenticator zařízení |

### <a name="data-stored-by-azure-multifactor-authentication-server"></a>Data uložená ve službě Azure vícefaktorového ověřování serveru

Pokud používáte Azure vícefaktorového ověřování server, ukládají se následující osobní údaje.

> [!IMPORTANT]
> Od 1. července 2019 společnost Microsoft už nenabízí vícefaktorové ověřovací server pro nová nasazení. Noví zákazníci, kteří chtějí vyžadovat vícefaktorové ověřování od svých uživatelů, by měli používat vícefaktorové ověřování Azure AD založené na cloudu. Stávající zákazníci, kteří provedli vícefaktorové ověřování server do 1. července 2019, si mohou stáhnout nejnovější verzi a aktualizace a vygenerovat aktivační přihlašovací údaje obvyklým způsobem.

| Typ události                           | Typ úložiště dat |
|--------------------------------------|-----------------|
| Token OATH                           | Protokoly vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování |
| Jednosměrná zpráva SMS                          | Protokoly vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování |
| Hlasový hovor                           | Protokoly vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Zablokované uživatelé (Pokud se nahlásil podvod) |
| Oznámení Microsoft Authenticator | Protokoly vícefaktorového ověřování<br />Úložiště dat sestavy aktivity vícefaktorového ověřování<br />Zablokované uživatelé (Pokud se nahlásil podvod)<br />Žádosti o změnu při Microsoft Authenticator změně tokenu zařízení |

## <a name="organizational-data-stored-by-azure-ad-multifactor-authentication"></a>Data organizace uložená pomocí vícefaktorového ověřování Azure AD

Data organizace jsou informace na úrovni tenanta, které můžou vystavovat konfiguraci nebo nastavení prostředí. Nastavení klienta z následujících Azure Portal ověřovací stránky s více fakty můžou ukládat podniková data, jako jsou prahové hodnoty uzamčení nebo informace o ID volajícího pro požadavky na příchozí ověřování na telefon:

* Uzamčení účtu
* Výstraha podvodů
* Oznámení
* Nastavení telefonního hovoru

Pro server Azure s více fakty můžou následující stránky Azure Portal obsahovat data organizace:

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
| Austrálie   | Austrálie    | USA<sup>1</sup>                         | Austrálie <sup>2</sup> |

<sup>1</sup> Protokoly kódu OATH jsou uloženy v Austrálii.

<sup>2</sup> . Hlasové volání: protokoly služby vícefaktorového ověřování se ukládají do USA.

V následující tabulce je uvedeno umístění pro protokoly služby pro cloudy svrchovan.

| Suverénní cloud                      | Protokoly přihlašování                         | Sestava aktivity vícefaktorového ověřování (včetně osobních údajů)| Protokoly služby vícefaktorového ověřování |
|--------------------------------------|--------------------------------------|-------------------------------|------------------|
| Microsoft Azure Germany              | Německo                              | USA                            | USA               |
| Azure (Čína) 21Vianet                 | Čína                                | USA                            | USA               |
| Cloud pro státní správu Microsoftu           | USA                                   | USA                            | USA               |

Sestavy aktivit vícefaktorového ověřování obsahují osobní údaje, například hlavní název uživatele (UPN) a kompletní telefonní číslo.

K provozu služby se používají protokoly služby vícefaktorového ověřování.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jaké informace o uživateli jsou shromažďovány pomocí vícefaktorového ověřování Azure AD založeného na cloudu a Azure vícefaktorového ověřování, najdete v tématu [shromažďování uživatelských dat s více fakty Azure AD](howto-mfa-reporting-datacollection.md).
