---
title: Hybridní identita požadované porty a protokoly - Azure | Microsoft Docs
description: Tato stránka je stránka technické reference pro porty, které musejí být otevřené pro Azure AD Connect
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: curtand
ms.assetid: de97b225-ae06-4afc-b2ef-a72a3643255b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: billmath
ms.openlocfilehash: 79c339c136e614be8a98461533c63d244dabc166
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Porty a protokoly, které vyžaduje hybridní identita
Následující dokument je technické na požadované porty a protokoly pro implementaci řešení hybridní identity. Použijte následující obrázek a naleznete v příslušné tabulce.

![Co je služba Azure AD Connect](./media/active-directory-aadconnect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>V tabulce 1 - Azure AD Connect a místní AD
Tato tabulka popisuje porty a protokoly, které jsou požadovány pro komunikaci mezi serverem Azure AD Connect a místní AD.

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Vyhledávání DNS v cílové doménové struktuře. |
| Kerberos |88 (TCP/UDP) |Ověřování protokolu Kerberos k doménové struktuře AD. |
| MS-RPC |135 (TCP/UDP) |Použít během počáteční konfigurace Průvodce Azure AD Connect, když se váže k doménové struktuře AD a taky během synchronizace hesel. |
| LDAP |389 (TCP/UDP) |Používá pro import dat ze služby Active Directory. Data se šifrují pomocí protokolu Kerberos přihlašovací & zapečetění. |
| RPC | 445 (TCP/UDP) |Používá bezproblémové jednotného přihlašování k vytvoření účtu počítače v doménové struktuře AD. |
| LDAP/SSL |636 (TCP/UDP) |Používá pro import dat ze služby Active Directory. Přenos dat je podepsat a zašifrovat. Použít jen v případě, že používáte protokol SSL. |
| RPC |49152 až 65535 (náhodných vysoké RPC Port)(TCP/UDP) |Použít během počáteční konfigurace služby Azure AD Connect, když se váže k doménových struktur služby AD a synchronizace hesel. V tématu [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017), a [KB224196](https://support.microsoft.com/kb/224196) Další informace. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabulka 2 - Azure AD Connect a službou Azure AD
Tato tabulka popisuje porty a protokoly, které jsou požadovány pro komunikaci mezi server Azure AD Connect a službou Azure AD.

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Používá ke stahování seznamů odvolaných certifikátů (seznamy odvolaných certifikátů) k ověření certifikátů SSL. |
| HTTPS |443(TCP/UDP) |Umožňuje synchronizovat s Azure AD. |

Seznam adres URL a IP adresy, je třeba otevřít v bráně firewall najdete v části [Office 365 adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabulka 3 – Azure AD Connect a AD FS federační servery/WAP
Tato tabulka popisuje porty a protokoly, které jsou požadovány pro komunikaci mezi serverem Azure AD Connect a serverech AD FS Federation/WAP.  

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Používá ke stahování seznamů odvolaných certifikátů (seznamy odvolaných certifikátů) k ověření certifikátů SSL. |
| HTTPS |443(TCP/UDP) |Umožňuje synchronizovat s Azure AD. |
| WinRM |5985 |Naslouchací proces služby WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabulka 4 - WAP a federační servery
Tato tabulka popisuje porty a protokoly, které jsou požadovány pro komunikaci mezi federačními servery a servery WAP.

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Slouží k ověření. |

## <a name="table-5---wap-and-users"></a>Tabulka 5 - WAP a uživatelé
Tato tabulka popisuje porty a protokoly, které jsou požadovány pro komunikaci mezi uživateli a serverech WAP.

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Použít pro ověřování zařízení. |
| TCP |49443 (TCP) |Použít pro ověření certifikátu. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabulka 6a & 6b - předávacího ověřování s jednotné přihlašování (SSO) a synchronizací hodnot Hash hesel s jednotné přihlašování (SSO)
Následující tabulka popisuje porty a protokoly, které jsou požadovány pro komunikaci mezi Azure AD Connect a službou Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabulka 6a - předávací ověřování pomocí jednotného přihlašování
|Protocol (Protokol)|Číslo portu|Popis
| --- | --- | ---
|HTTP|80|Povolte odchozí přenosy protokolu HTTP pro bezpečnostní ověření, např. SSL. Také potřebná pro funkci Automatické aktualizace konektor fungovat správně.
|HTTPS|443| Povolte odchozí přenosy HTTPS pro operace, například povolení a zakázání funkce, registrace konektory, stahování aktualizací konektoru a zpracování všechny uživatele žádostí o přihlášení.

Kromě toho musí být schopen vytvořit přímé připojení IP a Azure AD Connect [Azure datového centra rozsahy IP adres](https://www.microsoft.com/en-us/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabulka 6b - synchronizace hodnot Hash hesel pomocí jednotného přihlašování

|Protocol (Protokol)|Číslo portu|Popis
| --- | --- | ---
|HTTPS|443| Povolte registraci jednotné přihlašování (vyžadováno pouze u registračního procesu jednotné přihlašování).

Kromě toho musí být schopen vytvořit přímé připojení IP a Azure AD Connect [Azure datového centra rozsahy IP adres](https://www.microsoft.com/en-us/download/details.aspx?id=41653). Opakujte tento krok jenom požadované pro proces registrace jednotné přihlašování.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabulka 7a & 7b - agenta Azure AD Connect Health pro (AD FS nebo Sync) a Azure AD
Následující tabulky popisují koncové body, porty a protokoly, které jsou požadovány pro komunikaci mezi agenty Azure AD Connect Health a Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabulka 7a – porty a protokoly pro agenta Azure AD Connect Health pro (AD FS nebo Sync) a Azure AD
Tato tabulka popisuje následující Odchozí porty a protokoly, které jsou požadovány pro komunikaci mezi agenty Azure AD Connect Health a Azure AD.  

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Odchozí |
| Azure Service Bus |5671 (TCP/UDP) |Odchozí |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b – koncové body pro agenta Azure AD Connect Health pro (AD FS nebo Sync) a Azure AD
Seznam koncových bodů najdete v tématu [části požadavky pro agenta služby Azure AD Connect Health](../connect-health/active-directory-aadconnect-health-agent-install.md#requirements).

