---
title: Hybridní identita požadované porty a protokoly - Azure | Dokumenty společnosti Microsoft
description: Tato stránka je technická referenční stránka pro porty, které musí být otevřené pro Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 03/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: da318840426d1c0b94eab06b89ff3152df9d26fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331093"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Porty a protokoly, které vyžaduje hybridní identita
Následující dokument je technický odkaz na požadované porty a protokoly pro implementaci řešení hybridní identity. Použijte následující obrázek a podívejte se na odpovídající tabulku.

![Co je služba Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabulka 1 – Azure AD Connect a místní služba AD
Tato tabulka popisuje porty a protokoly, které jsou vyžadovány pro komunikaci mezi serverem Azure AD Connect a místním službou AD.

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Vyhledávání DNS v cílové doménové struktuře. |
| Kerberos |88 (TCP/UDP) |Ověřování protokolem Kerberos do doménové struktury služby AD. |
| MS-RPC |135 (TCP) |Používá se během počáteční konfigurace průvodce připojením Azure AD, když se váže na doménovou strukturu služby AD, a také během synchronizace hesel. |
| LDAP |389 (TCP/UDP) |Používá se pro import dat ze ad. Data jsou šifrována pomocí protokolu Kerberos Sign & Seal. |
| SMB | 445 (TCP) |Používá bezproblémové jednotné přihlašování k vytvoření účtu počítače v doménové struktuře služby AD. |
| PROTOKOL LDAP/SSL |636 (TCP/UDP) |Používá se pro import dat ze ad. Přenos dat je podepsán a zašifrován. Používá se pouze v případě, že používáte TLS. |
| RPC |49152- 65535 (Náhodný vysoký port RPC)(TCP) |Používá se během počáteční konfigurace Azure AD Connect při vazbě na doménové struktury služby AD a během synchronizace hesla. Další informace naleznete [v kb929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)a [KB224196.](https://support.microsoft.com/kb/224196) |
|WinRM  | 5985 (TCP) |Používá se pouze v případě, že instalujete službu AD FS s gMSA průvodcem Azure AD Connect|
|Webové služby služby AD DS | 9389 (TCP) |Používá se pouze v případě, že instalujete službu AD FS s gMSA průvodcem Azure AD Connect |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabulka 2 – Azure AD Connect a Azure AD
Tato tabulka popisuje porty a protokoly, které jsou vyžadovány pro komunikaci mezi serverem Azure AD Connect a Azure AD.

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTP |80 (TCP) |Slouží ke stažení seznamů odvolaných certifikátů (seznamy odvolaných certifikátů) k ověření certifikátů TLS/SSL. |
| HTTPS |443(TCP) |Používá se k synchronizaci s Azure AD. |

Seznam adres URL a IP adres, které je třeba otevřít v bráně firewall, najdete v [tématu Adresy URL office 365 a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) a [řešení potíží s připojením Azure AD Connect](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabulka 3 – Federační servery Azure AD Connect a AD FS/WAP
Tato tabulka popisuje porty a protokoly, které jsou vyžadovány pro komunikaci mezi serverem Azure AD Connect a servery AD FS Federation/WAP.  

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTP |80 (TCP) |Slouží ke stažení seznamů odvolaných certifikátů (seznamy odvolaných certifikátů) k ověření certifikátů TLS/SSL. |
| HTTPS |443(TCP) |Používá se k synchronizaci s Azure AD. |
| WinRM |5985 |Naslouchací proces WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabulka 4 - WAP a federační servery
Tato tabulka popisuje porty a protokoly, které jsou požadovány pro komunikaci mezi federačními servery a servery WAP.

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTPS |443(TCP) |Používá se pro ověřování. |

## <a name="table-5---wap-and-users"></a>Tabulka 5 - WAP a uživatelé
Tato tabulka popisuje porty a protokoly, které jsou požadovány pro komunikaci mezi uživateli a servery WAP.

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTPS |443(TCP) |Používá se pro ověřování zařízení. |
| TCP |49443 (TCP) |Používá se pro ověřování certifikátů. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabulka 6a & 6b – předávací ověřování s jedním přihlášením (SSO) a synchronizací hash hesel s jedním přihlášením (SSO)
Následující tabulky popisují porty a protokoly, které jsou vyžadovány pro komunikaci mezi Azure AD Connect a Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabulka 6a – předávací ověřování s přiizačním služby
|Protocol (Protokol)|Číslo portu|Popis
| --- | --- | ---
|HTTP|80|Povolte odchozí přenosy HTTP pro ověření zabezpečení, jako je například protokol SSL. Také potřebné pro funkci automatické aktualizace konektoru správně fungovat.
|HTTPS|443| Povolte odchozí přenosy HTTPS pro operace, jako je povolení a zakázání funkce, registrace konektorů, stahování aktualizací konektorů a zpracování všech požadavků na přihlášení uživatelů.

Kromě toho musí být Azure AD Connect schopná provádět přímá IP připojení k [rozsahům IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabulka 6b – synchronizace hash hesel se službou SSO

|Protocol (Protokol)|Číslo portu|Popis
| --- | --- | ---
|HTTPS|443| Povolit registraci přistupu (vyžadováno pouze pro proces registrace s přistupu).

Kromě toho musí být Azure AD Connect schopná provádět přímá IP připojení k [rozsahům IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Opět platí, že je to vyžadováno pouze pro proces registrace přizpůsobování a so.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabulka 7a & 7b – agent azure ad připojení pro (AD FS/Sync) a Azure AD
Následující tabulky popisují koncové body, porty a protokoly, které jsou vyžadovány pro komunikaci mezi agenty Azure AD Connect Health a Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabulka 7a – porty a protokoly pro agenta azure ad připojení pro (AD FS/Sync) a Azure AD
Tato tabulka popisuje následující odchozí porty a protokoly, které jsou vyžadovány pro komunikaci mezi agenty Azure AD Connect Health a Azure AD.  

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTPS |443(TCP) |Odchozí |
| Azure Service Bus |5671 (TCP) |Odchozí |

Pro nejnovější verzi agenta už není potřeba port Azure Service Bus 5671. Nejnovější verze agenta Azure AD Connect Health jepouze jako port 443.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b – koncové body pro agenta Azure AD Connect Health pro (AD FS/Sync) a Azure AD
Seznam koncových bodů najdete [v části Požadavky pro agenta Azure AD Connect Health](how-to-connect-health-agent-install.md#requirements).

