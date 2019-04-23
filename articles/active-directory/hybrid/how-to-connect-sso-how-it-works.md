---
title: 'Azure AD Connect: Bezproblémové jednotné přihlašování – jak to funguje | Dokumentace Microsoftu'
description: Tento článek popisuje, jak funkce Azure Active Directory bezproblémové jednotné přihlašování funguje.
services: active-directory
keywords: Co je Azure AD Connect, instalace Active Directory, požadované součásti pro službu Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 907abe3b09f9999b30703281f7e4ff286e2bae14
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60242355"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory bezproblémové jednotné přihlašování: Podrobné technické informace

Tento článek obsahuje podrobné technické informace do fungování funkce Azure Active Directory bezproblémové jednotné přihlašování (bezproblémové jednotné přihlašování).

## <a name="how-does-seamless-sso-work"></a>Jak funguje bezproblémového jednotného přihlašování?

Tato část obsahuje tři části:

1. Nastavení funkce bezproblémového jednotného přihlašování.
2. Jak funguje jednotné uživatelské přihlášení transakce ve webovém prohlížeči pomocí bezproblémového jednotného přihlašování.
3. Jak funguje jednoho uživatele přihlásit transakce v nativním klientovi pomocí bezproblémového jednotného přihlašování.

### <a name="how-does-set-up-work"></a>Jak nastavit práce?

Bezproblémového jednotného přihlašování se aktivuje pomocí služby Azure AD Connect, jak je znázorněno [tady](how-to-connect-sso-quick-start.md). Při povolení funkce, dojde k následujícím krokům:

- Účtu počítače (`AZUREADSSOACC`) se vytvoří ve vaší místní služby Active Directory (AD) v každé doménové struktuře AD, kterou synchronizovat do služby Azure AD (pomocí služby Azure AD Connect).
- Kromě toho se vytvoří řada Kerberos hlavních názvů služby (SPN) používané během procesu přihlášení Azure AD.
- Účet počítače Kerberos dešifrovací klíč je bezpečně sdílet s Azure AD. Pokud existuje více doménových struktur AD, každý účet počítače bude mít svůj vlastní jedinečný dešifrovací klíč protokolu Kerberos.

>[!IMPORTANT]
> `AZUREADSSOACC` Účet počítače musí být silného chráněn z bezpečnostních důvodů. Pouze Domain Admins by měl mít možnost Spravovat účet počítače. Ujistěte se, že je zakázána delegování protokolu Kerberos na účet počítače a že žádný jiný účet ve službě Active Directory má oprávnění k delegování na `AZUREADSSOACC` účet počítače... Store účet počítače v jednotce organizace (OU), kde jsou bezpečné před náhodným odstraněním a mají přístup pouze Domain Admins. Dešifrovací klíč protokolu Kerberos na účet počítače by měly také považovat za citlivé. Důrazně doporučujeme vám [nespotřebujete dešifrovací klíče Kerberos](how-to-connect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) z `AZUREADSSOACC` účet počítače nejméně každých 30 dnů.

Po dokončení instalace bezproblémové jednotné přihlašování funguje stejně jako jakékoli jiné přihlášení, která používá integrované ověřování Windows (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Jak přihlášení ve webovém prohlížeči pomocí bezproblémového jednotného přihlašování práce?

Tok přihlášení ve webovém prohlížeči vypadá takto:

1. Uživatel se pokusí získat přístup k webové aplikaci (například aplikaci Outlook Web App - https://outlook.office365.com/owa/) z připojených k doméně podnikové zařízení ve vaší podnikové síti.
2. Pokud ještě není přihlášení uživatele, uživatel je přesměrován na přihlašovací stránku Azure AD.
3. Uživatel zadá své uživatelské jméno na přihlašovací stránce služby Azure AD.

   >[!NOTE]
   >Pro [některých aplikací](./how-to-connect-sso-faq.md#what-applications-take-advantage-of-domain_hint-or-login_hint-parameter-capability-of-seamless-sso), jsou kroky 2 a 3 přeskočeny.

4. Pomocí jazyka JavaScript na pozadí, Azure AD vyzve prohlížeče prostřednictvím 401 neoprávněný odpověď, zadejte lístek protokolu Kerberos.
5. Prohlížeč, pak vyžádá lístek ze služby Active Directory pro `AZUREADSSOACC` účtu počítače (který představuje Azure AD).
6. Služby Active Directory vyhledá účet počítače a vrátí lístek protokolu Kerberos v prohlížeči šifrován tajný klíč pro účet počítače.
7. Prohlížeč předá lístek protokolu Kerberos, který, který získal ze služby Active Directory do služby Azure AD.
8. Azure AD dešifruje lístek protokolu Kerberos, který obsahuje identitu uživatele přihlášení firemních zařízení pomocí dříve sdílený klíč.
9. Po vyhodnocení Azure AD zpět do aplikace vrátí token nebo vyzve uživatele k provedení další testování konceptů, jako je ověřování službou Multi-Factor Authentication.
10. Pokud přihlášení uživatele úspěšné, uživatel je přístup k aplikaci.

Následující diagram znázorňuje všechny komponenty a potřebnými kroky.

![Bezproblémové jednotné přihlašování na – webové aplikace flow](./media/how-to-connect-sso-how-it-works/sso2.png)

Bezproblémové jednotné přihlašování je zaměřený na příležitosti, což znamená, že pokud selže, přihlašovací prostředí spadne zpět na běžného chování – tj, uživatel musí zadat heslo k přihlášení.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Jak přihlásit na nativní klientovi pomocí bezproblémového jednotného přihlašování práce?

Tok přihlášení na nativního klienta vypadá takto:

1. Uživatel se pokusí o přístup k nativní aplikaci (například klient Outlooku) z firemních zařízení uvnitř firemní sítě připojené k doméně.
2. Pokud již není přihlášený uživatel, nativní aplikace načítá uživatelské jméno uživatele, z relace zařízení Windows.
3. Aplikace odešle do služby Azure AD uživatelského jména a načte koncového bodu WS-Trust MEX vašeho tenanta. Tento koncový bod WS-Trust používá výhradně funkci bezproblémového jednotného přihlašování a není obecnou implementaci protokolu WS-Trust v Azure AD.
4. Následně se dotazuje aplikace koncového bodu MEX WS-Trust a zjistěte, jestli integrovaný koncový bod ověřování je k dispozici. Koncový bod integrovaného ověřování se používá výhradně funkcí bezproblémového jednotného přihlašování.
5. Krok 4 úspěšné, objeví se výzva protokolu Kerberos.
6. Pokud je aplikace se nepovedlo načíst lístek protokolu Kerberos, se předává až do koncového bodu integrované ověřování Azure AD.
7. Azure AD dešifruje lístek protokolu Kerberos a ověří jeho platnost.
8. Azure AD podepisuje uživatele v a vydá SAML token pro aplikaci.
9. Aplikace pak odešle token SAML, který má koncový bod tokenu OAuth2 Azure AD.
10. Azure AD ověří SAML token a vydá do aplikace přístupový token a obnovovací token pro zadaný prostředek a tokenu id.
11. Uživatel získá přístup k prostředek vaší aplikace.

Následující diagram znázorňuje všechny komponenty a potřebnými kroky.

![Bezproblémové jedním přihlášení – nativní aplikaci flow](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Další postup

- [**Rychlý Start** ](how-to-connect-sso-quick-start.md) – rychle zprovoznit a systémem Azure bezproblémového jednotného přihlašování AD.
- [**Nejčastější dotazy** ](how-to-connect-sso-faq.md) – odpovědi na nejčastější dotazy.
- [**Řešení potíží s** ](tshoot-connect-sso.md) – zjistěte, jak vyřešit běžné problémy s funkcí.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – k podání žádostí o nové funkce.
