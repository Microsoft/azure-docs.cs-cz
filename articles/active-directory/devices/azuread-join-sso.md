---
title: Jak funguje přisypovat služby Přimístním přimístním zabezpečení na zařízeních azure ad připojen | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat hybridní zařízení připojená k Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9d8c0cd803424e117bd4dc7a3382b7b32df2d05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672702"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Jak funguje přisypovat při způsob, jakým funguje přisuzovací služby místním prostředkům na zařízeních azure ad

Pravděpodobně není překvapením, že zařízení spojené s Azure Active Directory (Azure AD) poskytuje prostředí jednotného přihlašování (SSO) ke cloudovým aplikacím vašeho tenanta. Pokud vaše prostředí obsahuje místní službu Active Directory (AD), můžete rozšířit možnosti stesa na těchto zařízeních na něj.

Tento článek vysvětluje, jak to funguje.

## <a name="prerequisites"></a>Požadavky

 Pokud počítače připojené k Azure AD nejsou připojené k síti vaší organizace, je vyžadována síť VPN nebo jiná síťová infrastruktura. Místní služby SSO vyžadují komunikaci zorného pole s místními řadiči domény služby AD DS.

## <a name="how-it-works"></a>Jak to funguje 

Vzhledem k tomu, že si musíte pamatovat pouze jedno uživatelské jméno a heslo, jednotné přistupující zařízení zjednodušuje přístup k vašim prostředkům a zlepšuje zabezpečení vašeho prostředí. Díky zařízení s připojením k Azure AD už vaši uživatelé mají prostředí pro přistouzené zabezpečení pro cloudové aplikace ve vašem prostředí. Pokud vaše prostředí má Azure AD a místní ad, pravděpodobně budete chtít rozšířit rozsah prostředí přihlašuje do místních line of business (LOB) aplikace, sdílené složky a tiskárny.

Zařízení připojená k Azure AD nemají žádné znalosti o místním prostředí služby AD, protože k němu nejsou připojená. Můžete však poskytnout další informace o místní službě AD pro tato zařízení pomocí služby Azure AD Connect.

Prostředí, které má obojí, Azure AD a místní služby AD, je také známo, že má hybridní prostředí. Pokud máte hybridní prostředí, je pravděpodobné, že už máte Azure AD Connect nasazené k synchronizaci informací o místní identitě do cloudu. Jako součást procesu synchronizace Azure AD Connect synchronizuje místní informace o uživateli do Azure AD. Když se uživatel přihlásí k zařízení azure ad připojen v hybridním prostředí:

1. Azure AD odešle název místní domény uživatel je členem zpět do zařízení.
1. Služba místního úřadu zabezpečení (LSA) umožňuje ověřování protokolem Kerberos v zařízení.

Během pokusu o přístup k prostředku požadujícímu protokol Kerberos v místním prostředí uživatele zařízení:

1. Odešle informace o místní doméně a pověření uživatele do umístěného řadiče domény, aby se uživatel ověřil.
1. Obdrží lístek [kerberos udělení lístku (TGT),](/windows/desktop/secauthn/ticket-granting-tickets) který se používá pro přístup k prostředkům spojených se společností AD. Pokud se nezdaří pokus o získání TGT pro doménu připojení AAD (související časový režim DCLocator může způsobit zpoždění), dojde k pokusu o správce pověření nebo uživatel může obdržet ověřování místní okno požadující pověření pro cílový prostředek.

Všechny aplikace, které jsou nakonfigurované pro **ověřování integrované se systémem Windows** bez problémů získat jednotné přihlašovat, když se uživatel pokusí o přístup k nim.

Windows Hello pro firmy vyžaduje další konfiguraci pro povolení místního připojovacího systému z zařízení s připojením KOnS z azure ad. Další informace najdete [v tématu Konfigurace zařízení spojených s Azure AD pro místní jednotné přihlašování pomocí Windows Hello pro firmy](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Co získáte

S přikazovacím systémem můžete na zařízení s připojením k Azure AD: 

- Přístup k cestě UNC na členském serveru služby AD
- Přístup k webovému serveru členů služby AD nakonfigurovanému pro zabezpečení integrované se systémem Windows 

Pokud chcete spravovat místní službu AD ze zařízení s Windows, nainstalujte [Nástroje pro vzdálenou správu serveru pro Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

Můžete použít:

- Modul snap-in Uživatelé a počítače služby Active Directory (ADUC) pro správu všech objektů služby AD. Je však nutné zadat doménu, ke které se chcete připojit ručně.
- Modul snap-in DHCP pro správu serveru DHCP připojovaného službou AD. Je však možné, že bude nutné zadat název nebo adresu serveru DHCP.
 
## <a name="what-you-should-know"></a>Co byste měli vědět

Možná budete muset upravit [filtrování na základě domény](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) v Azure AD Connect, abyste zajistili synchronizaci dat o požadovaných doménách.

Aplikace a prostředky, které závisí na ověřování počítače služby Active Directory, nefungují, protože zařízení připojená k Azure AD nemají ve službě AD objekt počítače. 

Soubory nelze sdílet s ostatními uživateli na zařízení s připojením k Azure AD.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [Co je správa zařízení ve službě Azure Active Directory?](overview.md) 
