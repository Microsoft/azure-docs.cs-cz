---
title: Vyžaduje hybridní identita porty a protokoly – Azure | Dokumentace Microsoftu
description: Tato stránka je stránka technické reference pro porty, které musí být otevřen pro služby Azure AD Connect
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
ms.topic: article
ms.date: 08/02/2017
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 38a344fb6e67beae6310480646d84a1fe7730f84
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54461030"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Porty a protokoly, které vyžaduje hybridní identita
Následující dokument je technické referenční informace k požadované porty a protokoly pro implementaci řešení s hybridní identitou. Použijte následující obrázek a najdete v příslušné tabulce.

![Co je služba Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabulka 1 - Azure AD Connect a místní služby AD
Tato tabulka popisuje porty a protokoly, které jsou nutné pro komunikaci mezi serverem Azure AD Connect a místní AD.

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Vyhledávání DNS v cílové doménové struktuře. |
| Kerberos |88 (TCP/UDP) |Ověřování pomocí protokolu Kerberos k doménové struktuře AD. |
| MS-RPC |135 (TCP/UDP) |Použít během počáteční konfiguraci průvodce Azure AD Connect, když se váže k doménové struktuře AD a také během synchronizace hesel. |
| LDAP |389 (TCP/UDP) |Používá se pro import dat ze služby AD. Data se šifrují pomocí protokolu Kerberos podepsat a zapečetění. |
| RPC | 445 (TCP/UDP) |Bezproblémové jednotné přihlašování se používá k vytvoření účtu počítače v doménové struktuře AD. |
| LDAP/SSL |636 (TCP/UDP) |Používá se pro import dat ze služby AD. Přenos dat je podepsaný a zašifrovaný. Použít pouze pokud používáte protokol SSL. |
| RPC |49152 - 65535 (Random vysokou RPC Port)(TCP/UDP) |Použít během počáteční konfigurace služby Azure AD Connect, když se váže k doménových struktur AD a během synchronizace hesel. Zobrazit [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017), a [KB224196](https://support.microsoft.com/kb/224196) Další informace. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabulka 2 - Azure AD Connect a službou Azure AD
Tato tabulka popisuje porty a protokoly, které jsou nutné pro komunikaci mezi serverem Azure AD Connect a službou Azure AD.

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Používá ke stahování seznamů odvolaných certifikátů (seznamy odvolaných certifikátů) k ověření certifikátů SSL. |
| HTTPS |443(TCP/UDP) |Používá k synchronizaci se službou Azure AD. |

Seznam adres URL a IP adresy, budete muset otevřít v bráně firewall najdete v článku [Office 365 – adresy URL a rozsahy IP adres](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabulka 3 – Azure AD Connect a AD FS federační servery/WAP
Tato tabulka popisuje porty a protokoly, které jsou nutné pro komunikaci mezi serverem Azure AD Connect a servery AD FS federace/WAP.  

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTP |80 (TCP/UDP) |Používá ke stahování seznamů odvolaných certifikátů (seznamy odvolaných certifikátů) k ověření certifikátů SSL. |
| HTTPS |443(TCP/UDP) |Používá k synchronizaci se službou Azure AD. |
| WinRM |5985 |Naslouchací proces služby WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabulka 4 - WAP a federační servery
Tato tabulka popisuje porty a protokoly, které jsou nutné pro komunikaci mezi federačními servery a servery WAP.

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Slouží k ověření. |

## <a name="table-5---wap-and-users"></a>Tabulka 5 - WAP a uživatelé
Tato tabulka popisuje porty a protokoly, které jsou nutné pro komunikaci mezi uživateli a servery WAP.

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Používá se pro ověřování zařízení. |
| TCP |49443 (TCP) |Použít pro ověření certifikátu. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabulka 6a & 6b - předávací ověřování pomocí jednotného přihlašování (SSO) a synchronizací hodnot Hash hesel se jednotné přihlašování (SSO)
Následující tabulka popisuje porty a protokoly, které jsou nutné pro komunikaci mezi Azure AD Connect a službou Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabulka 6a - předávacího ověřování s jednotným Přihlašováním
|Protocol (Protokol)|Číslo portu|Popis
| --- | --- | ---
|HTTP|80|Povolení odchozího přenosu protokolu HTTP pro bezpečnostní ověření, např. SSL. Nutná pro funkce Automatické aktualizace konektoru fungovat správně.
|HTTPS|443| Povolte odchozí přenosy HTTPS pro operace, jako je povolení a zakázání funkce, registrace konektory, stahování aktualizace konektoru a zpracovává všechny přihlášení požadavky uživatelů.

Kromě toho musí být schopný navázat přímé připojení IP do služby Azure AD Connect [Azure datových center rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabulka 6b – synchronizace hodnot Hash hesel s jednotným Přihlašováním

|Protocol (Protokol)|Číslo portu|Popis
| --- | --- | ---
|HTTPS|443| Povolení jednotného přihlašování k registraci (vyžadováno pouze u registračního procesu jednotné přihlašování).

Kromě toho musí být schopný navázat přímé připojení IP do služby Azure AD Connect [Azure datových center rozsahy IP adres](https://www.microsoft.com/download/details.aspx?id=41653). Důvodem jsou opět pouze požadované pro proces registrace jednotného přihlašování.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabulka 7a & 7b – Azure AD a Azure AD Connect Health agent pro (AD FS/synchronizace)
Následující tabulky popisují koncových bodů, porty a protokoly, které jsou nutné pro komunikaci mezi agenty Azure AD Connect Health a Azure AD

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabulka 7a – porty a protokoly pro agenta Azure AD Connect Health pro (AD FS/Sync) a Azure AD
Tato tabulka popisuje následující Odchozí porty a protokoly, které jsou nutné pro komunikaci mezi agenty Azure AD Connect Health a Azure AD.  

| Protocol (Protokol) | Porty | Popis |
| --- | --- | --- |
| HTTPS |443(TCP/UDP) |Odchozí |
| Azure Service Bus |5671 (TCP/UDP) |Odchozí |

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b – koncové body pro agenta Azure AD Connect Health pro (AD FS/Sync) a Azure AD
Seznam koncových bodů najdete v tématu [část požadavky pro Azure AD Connect Health agent](how-to-connect-health-agent-install.md#requirements).

