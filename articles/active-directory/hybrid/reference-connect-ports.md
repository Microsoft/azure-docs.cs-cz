---
title: Porty a protokoly, které vyžaduje hybridní identita – Azure | Microsoft Docs
description: Tato stránka je technickou referenční stránkou pro porty, které je potřeba otevřít pro Azure AD Connect
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80331093"
---
# <a name="hybrid-identity-required-ports-and-protocols"></a>Porty a protokoly, které vyžaduje hybridní identita
Následující dokument představuje technické informace o požadovaných portech a protokolech pro implementaci řešení hybridní identity. Použijte následující obrázek a přečtěte si odpovídající tabulku.

![Co je služba Azure AD Connect](./media/reference-connect-ports/required3.png)

## <a name="table-1---azure-ad-connect-and-on-premises-ad"></a>Tabulka 1 – Azure AD Connect a místní služba AD
V této tabulce jsou popsány porty a protokoly, které jsou vyžadovány pro komunikaci mezi Azure AD Connect serverem a místní službou AD.

| Protokol | Porty | Description |
| --- | --- | --- |
| DNS |53 (TCP/UDP) |Hledání DNS v cílové doménové struktuře. |
| Kerberos |88 (TCP/UDP) |Ověřování protokolem Kerberos pro doménovou strukturu AD. |
| MS-RPC |135 (TCP) |Používá se při počáteční konfiguraci Průvodce Azure AD Connect při vytváření vazby k doménové struktuře služby AD a také při synchronizaci hesel. |
| LDAP |389 (TCP/UDP) |Používá se pro import dat ze služby AD. Data se šifrují pomocí podpisu protokolu Kerberos & zapečetit. |
| SMB | 445 (TCP) |Pomocí bezproblémového jednotného přihlašování můžete v doménové struktuře služby AD vytvořit účet počítače. |
| PROTOKOL LDAP/SSL |636 (TCP/UDP) |Používá se pro import dat ze služby AD. Přenos dat je podepsaný a zašifrovaný. Používá se pouze v případě, že používáte protokol TLS. |
| RPC |49152 – 65535 (náhodný port s vysokým protokolem RPC) (TCP) |Používá se při počáteční konfiguraci Azure AD Connect při vytváření vazby k doménovým strukturám AD a při synchronizaci hesel. Další informace najdete v tématech [KB929851](https://support.microsoft.com/kb/929851), [KB832017](https://support.microsoft.com/kb/832017)a [KB224196](https://support.microsoft.com/kb/224196) . |
|WinRM  | 5985 (TCP) |Používá se jenom v případě, že instalujete AD FS pomocí Průvodce Azure AD Connectm v nástroji gMSA.|
|služba AD DS webové služby | 9389 (TCP) |Používá se jenom v případě, že instalujete AD FS pomocí Průvodce Azure AD Connectm v nástroji gMSA. |

## <a name="table-2---azure-ad-connect-and-azure-ad"></a>Tabulka 2 – Azure AD Connect a Azure AD
Tato tabulka popisuje porty a protokoly, které jsou vyžadovány pro komunikaci mezi Azure AD Connect serverem a službou Azure AD.

| Protokol | Porty | Description |
| --- | --- | --- |
| HTTP |80 (TCP) |Slouží ke stažení seznamů CRL (seznamy odvolaných certifikátů) k ověření certifikátů TLS/SSL. |
| HTTPS |443 (TCP) |Používá se k synchronizaci s Azure AD. |

Seznam adres URL a IP adres, které musíte otevřít v bráně firewall, najdete v tématu [adresy URL a rozsahy IP adres pro Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) a [řešení potíží s Azure AD Connect připojením](tshoot-connect-connectivity.md#troubleshoot-connectivity-issues-in-the-installation-wizard).

## <a name="table-3---azure-ad-connect-and-ad-fs-federation-serverswap"></a>Tabulka 3 – Azure AD Connect a AD FS federační servery/WAP
V této tabulce jsou popsány porty a protokoly, které jsou vyžadovány pro komunikaci mezi serverem Azure AD Connect a servery AD FS federačního/WAP.  

| Protokol | Porty | Description |
| --- | --- | --- |
| HTTP |80 (TCP) |Slouží ke stažení seznamů CRL (seznamy odvolaných certifikátů) k ověření certifikátů TLS/SSL. |
| HTTPS |443 (TCP) |Používá se k synchronizaci s Azure AD. |
| WinRM |5985 |Naslouchací proces WinRM |

## <a name="table-4---wap-and-federation-servers"></a>Tabulka 4 – WAP a federační servery
V této tabulce jsou popsány porty a protokoly, které jsou vyžadovány pro komunikaci mezi federačními servery a servery WAP.

| Protokol | Porty | Description |
| --- | --- | --- |
| HTTPS |443 (TCP) |Používá se pro ověřování. |

## <a name="table-5---wap-and-users"></a>Tabulka 5 – WAP a uživatelé
V této tabulce jsou popsány porty a protokoly, které jsou vyžadovány pro komunikaci mezi uživateli a servery WAP.

| Protokol | Porty | Description |
| --- | --- | --- |
| HTTPS |443 (TCP) |Používá se pro ověřování zařízení. |
| TCP |49443 (TCP) |Používá se pro ověřování certifikátů. |

## <a name="table-6a--6b---pass-through-authentication-with-single-sign-on-sso-and-password-hash-sync-with-single-sign-on-sso"></a>Tabulka 6a & 6b – předávací ověřování s jednotným přihlašováním (SSO) a synchronizace hodnot hash hesel s jednotným přihlašováním (SSO)
V následujících tabulkách jsou popsány porty a protokoly, které jsou vyžadovány pro komunikaci mezi Azure AD Connect a službou Azure AD.

### <a name="table-6a---pass-through-authentication-with-sso"></a>Tabulka 6a – předávací ověřování s jednotným přihlašováním
|Protokol|Číslo portu|Description
| --- | --- | ---
|HTTP|80|Povolte odchozí přenosy HTTP pro ověření zabezpečení, jako je například SSL. Také je nutné, aby funkce automaticky aktualizovaného konektoru správně fungovala.
|HTTPS|443| Povolte odchozí přenosy HTTPS pro operace, jako je povolení a zakázání funkce, registrace konektorů, stahování aktualizací konektoru a zpracování všech uživatelských žádostí o přihlášení.

Kromě toho Azure AD Connect třeba umožnit přímé připojení IP k [rozsahům IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="table-6b---password-hash-sync-with-sso"></a>Tabulka 6b – synchronizace hodnot hash hesel pomocí jednotného přihlašování

|Protokol|Číslo portu|Description
| --- | --- | ---
|HTTPS|443| Povolit registraci jednotného přihlašování (vyžaduje se jenom pro proces registrace jednotného přihlašování)

Kromě toho Azure AD Connect třeba umožnit přímé připojení IP k [rozsahům IP adres datového centra Azure](https://www.microsoft.com/download/details.aspx?id=41653). Tato operace se bude vyžadovat jenom pro proces registrace jednotného přihlašování.

## <a name="table-7a--7b---azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabulka 7a & Azure AD Connect Health agenta pro (AD FS/synchronizaci) a Azure AD
V následujících tabulkách jsou popsány koncové body, porty a protokoly, které jsou vyžadovány pro komunikaci mezi agenty Azure AD Connect Health a službou Azure AD.

### <a name="table-7a---ports-and-protocols-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>Tabulka 7a – porty a protokoly pro agenta Azure AD Connect Health pro (AD FS/synchronizovat) a Azure AD
Tato tabulka popisuje následující Odchozí porty a protokoly, které jsou vyžadovány pro komunikaci mezi agenty Azure AD Connect Health a službou Azure AD.  

| Protokol | Porty | Description |
| --- | --- | --- |
| HTTPS |443 (TCP) |Odchozí |
| Azure Service Bus |5671 (TCP) |Odchozí |

Azure Service Bus port 5671 již není požadován pro nejnovější verzi agenta. Nejnovější verze agenta Azure AD Connect Health vyžaduje pouze port 443.

### <a name="7b---endpoints-for-azure-ad-connect-health-agent-for-ad-fssync-and-azure-ad"></a>7b – koncové body pro agenta Azure AD Connect Health pro (AD FS/synchronizovat) a Azure AD
Seznam koncových bodů najdete v [části požadavky pro agenta Azure AD Connect Health](how-to-connect-health-agent-install.md#requirements).

