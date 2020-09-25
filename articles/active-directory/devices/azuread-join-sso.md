---
title: Jak funguje jednotné přihlašování k místním prostředkům na zařízeních připojených k Azure AD | Microsoft Docs
description: Naučte se, jak nastavovat možnosti jednotného přihlašování konfigurací zařízení připojených k hybridním Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: ba802cb86d68298cd4dfff94162069590744833c
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91256458"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Jak jednotné přihlašování k místním prostředkům funguje v zařízeních připojených ke službě Azure AD

Pravděpodobně se nejedná o neočekávaně, že zařízení připojené k Azure Active Directory (Azure AD) poskytuje prostředí s jednotným přihlašováním k cloudovým aplikacím vašeho tenanta. Pokud má vaše prostředí místní službu Active Directory (AD), můžete na těchto zařízeních rozmístit možnosti jednotného přihlašování (SSO) na prostředky a aplikace, které se spoléhají i na místní AD. 

Tento článek vysvětluje, jak to funguje.

## <a name="prerequisites"></a>Požadavky

 Pokud počítače připojené k Azure AD nejsou připojené k síti vaší organizace, vyžaduje se síť VPN nebo jiná síťová infrastruktura. Místní jednotné přihlašování vyžaduje komunikaci s místními služba AD DS řadiči domény od začátku pohledu.

## <a name="how-it-works"></a>Jak to funguje 

U zařízení připojeného k Azure AD už uživatelé mají v prostředí cloudové aplikace i možnost jednotného přihlašování. Pokud má vaše prostředí Azure AD a místní službu AD, můžete chtít rozšířit rozsah možností jednotného přihlašování na vaše místní obchodní aplikace, sdílené složky a tiskárny.

Zařízení připojená k Azure AD nemají žádné znalosti o místním prostředí služby AD, protože k němu nejsou připojená. K těmto zařízením ale můžete pomocí Azure AD Connect zadat další informace o vaší místní službě AD.

Také známé prostředí, které obsahuje službu Azure AD i místní službu AD, má hybridní prostředí. Pokud máte hybridní prostředí, je možné, že už máte Azure AD Connect nasazené, abyste mohli synchronizovat vaše místní informace o identitě do cloudu. V rámci procesu synchronizace Azure AD Connect synchronizuje místní informace o uživatelích s Azure AD. Když se uživatel přihlásí k zařízení připojenému k Azure AD v hybridním prostředí:

1. Azure AD pošle podrobnosti o místní doméně uživatele zpátky do zařízení společně s [primárním aktualizačním tokenem](concept-primary-refresh-token.md) .
1. Služba místního úřadu zabezpečení (LSA) umožňuje ověřování pomocí protokolu Kerberos a NTLM na zařízení.

Během pokusu o přístup k prostředku požadujícímu protokol Kerberos nebo NTLM v místním prostředí uživatele Toto zařízení:

1. Odešle informace o místní doméně a přihlašovací údaje uživatele k umístěnému řadiči domény, aby se získal ověřený uživatel.
1. Přijme [lístek TGT (Ticket-Granting Ticket)](/windows/desktop/secauthn/ticket-granting-tickets) protokolu Kerberos nebo token NTLM založený na protokolu, který podporuje místní prostředek nebo aplikace. Pokud se pokus o získání lístku TGT protokolu Kerberos nebo tokenu NTLM pro doménu nezdaří (související časový limit DCLocator může způsobit zpoždění), dojde k pokusu o odeslání záznamů správce přihlašovacích údajů nebo může uživatel obdržet místní nabídku ověřování požadující přihlašovací údaje pro cílový prostředek.

Všechny aplikace, které jsou nakonfigurované pro **ověřování integrované v systému Windows** , bez problémů získají jednotné přihlašování, když se k nim uživatel pokusí získat přístup.

Windows Hello pro firmy vyžaduje další konfiguraci, aby bylo možné místní jednotné přihlašování povolit z zařízení připojeného k Azure AD. Další informace najdete v tématu [Konfigurace zařízení připojených k Azure AD pro místní jednotné přihlašování pomocí Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Co získáte

Pomocí jednotného přihlašování na zařízení připojeném k Azure AD můžete: 

- Přístup k cestě UNC na členském serveru AD
- Přístup ke členskému webovému serveru služby Active Directory, který je nakonfigurovaný pro zabezpečení integrované s Windows 

Pokud chcete spravovat místní službu AD ze zařízení s Windows, nainstalujte [Nástroje pro vzdálenou správu serveru pro Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

Můžete použít:

- Modul snap-in Uživatelé a počítače služby Active Directory (ADUC) pro správu všech objektů služby AD. Je však nutné zadat doménu, ke které se chcete připojit ručně.
- Modul snap-in DHCP pro správu serveru DHCP připojeného k AD. Je ale možné, že budete muset zadat název nebo adresu serveru DHCP.
 
## <a name="what-you-should-know"></a>Co byste měli vědět

V Azure AD Connect možná budete muset upravit vaše [filtrování založené na doméně](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) , aby se zajistilo, že budou synchronizovaná data o požadovaných doménách.

Aplikace a prostředky, které závisí na ověřování počítače služby Active Directory, nefungují, protože zařízení připojená k Azure AD nemají objekt počítače ve službě AD. 

Nemůžete sdílet soubory s ostatními uživateli na zařízení připojeném k Azure AD.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [co je Správa zařízení v Azure Active Directory?](overview.md) 
