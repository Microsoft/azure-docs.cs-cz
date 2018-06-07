---
title: 'Azure AD Connect: Bezproblémové Single Sign-On - jak to funguje | Microsoft Docs'
description: Tento článek popisuje, jak funkce Azure Active Directory bezproblémové jednotné přihlašování funguje.
services: active-directory
keywords: Co je Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: bcd9ec44eafd586648ba964c5cba248a184a8ec3
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34591557"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory bezproblémové jednotné přihlašování: podrobné technické informace

Tento článek poskytuje podrobné technické informace do Princip funkce Azure Active Directory bezproblémové jednotné přihlašování (SSO bezproblémové).

## <a name="how-does-seamless-sso-work"></a>Jak funguje bezproblémové jednotné přihlašování?

Tato část obsahuje tři části:
1. Instalace funkce bezproblémové jednotné přihlašování.
2. Jak funguje jednoho uživatele přihlásit transakce ve webovém prohlížeči pomocí bezproblémové jednotného přihlašování.
3. Jak funguje jednoho uživatele přihlásit transakce na nativního klienta pomocí bezproblémové jednotného přihlašování.

### <a name="how-does-set-up-work"></a>Jak nastavit pracovní?

Bezproblémové jednotného přihlašování je povoleno pomocí Azure AD Connect, jak je znázorněno [zde](active-directory-aadconnect-sso-quick-start.md). Při povolení funkce, jsou provedeny následující kroky:
- Účet počítače s názvem `AZUREADSSOACC` (která představuje Azure AD) je vytvořen v místní službě Active Directory (AD).
- Účet počítače pomocí protokolu Kerberos dešifrovací klíč je bezpečně sdílet s Azure AD.
- Kromě toho jsou dvě Kerberos hlavní názvy služby (SPN) vytvoří představují dvou adres URL, které se používají při přihlášení k Azure AD.

>[!NOTE]
> V každé doménové struktuře AD synchronizovat do Azure AD (přes Azure AD Connect) a pro uživatele, jehož chcete bezproblémové jednotné přihlašování se vytvoří účet počítače a SPN protokolu Kerberos. Přesunout `AZUREADSSOACC` účet počítače pro organizaci jednotce (OU) ukládat další účty počítačů zajistit, že je spravovat stejným způsobem a není odstraněn.

>[!IMPORTANT]
>Důrazně doporučujeme, aby vám [mění dešifrovací klíč protokolu Kerberos](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account) z `AZUREADSSOACC` účet počítače minimálně každých 30 dnů.

Po dokončení instalace bezproblémové jednotné přihlašování funguje stejně jako všechny ostatní přihlášení používající integrované ověřování systému Windows (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Jak přihlášení ve webovém prohlížeči s pracovní bezproblémové jednotné přihlašování?

Tok přihlášení ve webovém prohlížeči vypadá takto:

1. Uživatel se pokusí o přístup k webové aplikaci (například aplikaci Outlook Web App - https://outlook.office365.com/owa/) z podnikové zařízení uvnitř firemní sítě připojené k doméně.
2. Pokud již není přihlášený uživatel, se uživatel přesměruje na přihlašovací stránku služby Azure AD.
3. Typy uživatelů ve své uživatelské jméno na přihlašovací stránku služby Azure AD.

  >[!NOTE]
  >Pro [některých aplikací](./active-directory-aadconnect-sso-faq.md#what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso), jsou kroky 2 a 3 přeskočeny.

4. Azure AD pomocí jazyka JavaScript na pozadí, vyzve prohlížeče, prostřednictvím 401 neoprávněný odpověď, k poskytování lístek protokolu Kerberos.
5. Prohlížeč, pak vyžádá lístek ze služby Active Directory pro `AZUREADSSOACC` účet počítače (což představuje Azure AD).
6. Služby Active Directory vyhledá účet počítače a vrátí lístek protokolu Kerberos v prohlížeči šifrován tajný klíč pro účet počítače.
7. V prohlížeči předává lístek protokolu Kerberos, který ho získat ze služby Active Directory do Azure AD.
8. Azure AD dešifruje lístek protokolu Kerberos, která zahrnuje identitu uživatele přihlášeni firemních zařízení pomocí dříve sdílený klíč.
9. Po vyhodnocení, Azure AD buď vrátí token zpět do aplikace nebo vyzývá uživatel k provedení dodatečné důkazy, jako je Vícefaktorové ověřování.
10. Pokud přihlášení uživatele je úspěšné, uživatel je možné získat přístup k aplikaci.

Následující diagram znázorňuje všechny součásti a kroky.

![Bezproblémové jednotného přihlašování na - webové aplikace toku](./media/active-directory-aadconnect-sso/sso2.png)

Bezproblémové jednotného přihlašování je oportunistické, což znamená, že pokud se nezdaří, přihlašování uživatelů spadne zpět na regulární chování – tj, uživatel musí zadat svoje heslo k přihlášení.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Jak přihlášení na nativní klient s pracovní bezproblémové jednotné přihlašování?

Tok přihlášení na nativního klienta vypadá takto:

1. Uživatel se pokusí získat přístup k nativní aplikaci (například klient Outlooku) z připojených k doméně podnikové zařízení uvnitř firemní sítě.
2. Pokud již není přihlášený uživatel, nativní aplikace načte uživatelské jméno uživatele z relace systému Windows v zařízení.
3. Aplikace odešle uživatelské jméno do služby Azure AD a načte koncový bod MEX WS-Trust vašeho klienta.
4. Aplikace pak dotazuje koncový bod MEX WS-Trust pro případ, integrované ověřování koncový bod je k dispozici.
5. Pokud krok 4 úspěšné, je vydána výzvy protokolu Kerberos.
6. Pokud aplikace je možné načíst lístek protokolu Kerberos, předá ji až koncový bod integrované ověřování služby Azure AD.
7. Azure AD dešifruje lístek protokolu Kerberos a ověří jeho platnost.
8. Azure AD přihlášení uživatele v a vydá SAML token do aplikace.
9. Aplikace pak odešle token SAML, který má koncový bod tokenu OAuth2 Azure AD.
10. Azure AD ověří SAML token a problémy v aplikaci přístupový token a obnovovací token pro zadaný prostředek a id token.
11. Uživatel získá přístup k prostředku aplikace.

Následující diagram znázorňuje všechny součásti a kroky.

![Bezproblémové jedním přihlásit - tok nativní aplikace](./media/active-directory-aadconnect-sso/sso14.png)

## <a name="next-steps"></a>Další postup

- [**Rychlý Start** ](active-directory-aadconnect-sso-quick-start.md) – zprovoznění a systémem Azure bezproblémové jednotného přihlašování k AD.
- [**Nejčastější dotazy** ](active-directory-aadconnect-sso-faq.md) -odpovědi na nejčastější dotazy.
- [**Řešení potíží s** ](active-directory-aadconnect-troubleshoot-sso.md) – zjistěte, jak řešit obvyklé problémy s funkcí.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – pro vyplnění žádosti o nové funkce.
