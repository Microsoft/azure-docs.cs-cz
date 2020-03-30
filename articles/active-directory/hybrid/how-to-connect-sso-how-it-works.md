---
title: 'Azure AD Connect: Bezproblémové jednotné přihlašování – jak funguje | Dokumenty společnosti Microsoft'
description: Tento článek popisuje, jak funguje funkce bezproblémového jednotného přihlašování služby Azure Active Directory.
services: active-directory
keywords: co je Azure AD Connect, nainstalujte službu Active Directory, požadované součásti pro Azure AD, Jednotné přihlašování, jednotné přihlašování
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
ms.openlocfilehash: bd4743bc38c3b2b4b9495b33535b4b73f48d1372
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71176671"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Bezproblémové jednotné přihlašování služby Azure Active Directory: Podrobné technické informace

Tento článek poskytuje technické podrobnosti o tom, jak funguje funkce bezproblémového jednotného přihlašování služby Azure Active Directory (Seamless SSO).

## <a name="how-does-seamless-sso-work"></a>Jak bezproblémové jednotné přihlašoty fungují?

Tato část má tři části:

1. Nastavení funkce bezproblémové jednotné přihlašome.
2. Jak transakce přihlášení jednoho uživatele ve webovém prohlížeči funguje s bezproblémovým jednotném přihlašování.
3. Jak transakce přihlášení jednoho uživatele na nativníklient pracuje s bezproblémové jednotné přihlašování.

### <a name="how-does-set-up-work"></a>Jak funguje nastavení?

Bezproblémové jednotné přihlašování je povoleno pomocí Azure AD Connect, jak je znázorněno [zde](how-to-connect-sso-quick-start.md). Při povolení funkce dochází k následujícím krokům:

- Účet počítače`AZUREADSSOACC`( ) se vytvoří ve vaší místní službě Active Directory (AD) v každé doménové struktuře služby AD, kterou synchronizujete se službou Azure AD (pomocí služby Azure AD Connect).
- Kromě toho se vytvoří řada hlavních názvů služeb Protokolu Kerberos (SPN) pro použití během procesu přihlášení služby Azure AD.
- Dešifrovací klíč protokolu Kerberos účtu počítače je bezpečně sdílen se službou Azure AD. Pokud existuje více doménových struktur ad, každý účet počítače bude mít svůj vlastní jedinečný dešifrovací klíč Kerberos.

>[!IMPORTANT]
> Účet `AZUREADSSOACC` počítače musí být z bezpečnostních důvodů silně chráněn. Účet počítače by měli spravovat pouze správci domény. Ujistěte se, že je delegování protokolu Kerberos v účtu počítače `AZUREADSSOACC` zakázáno a zda žádný jiný účet ve službě Active Directory nemá oprávnění k delegování účtu počítače. Uložte účet počítače do organizační jednotky (OU), kde jsou v bezpečí před náhodným odstraněním a kde mají přístup pouze správci domény. Dešifrovací klíč Kerberos v účtu počítače by měl být také považován za citlivý. Důrazně doporučujeme [převrátit dešifrovací klíč protokolu Kerberos](how-to-connect-sso-faq.md) účtu `AZUREADSSOACC` počítače alespoň každých 30 dní.

Po dokončení nastavení bezproblémové jednotné přihlašování funguje stejným způsobem jako jakékoli jiné přihlášení, které používá integrované ověřování systému Windows (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Jak funguje přihlášení ve webovém prohlížeči pomocí bezproblémového jednotného přihlašování?

Tok přihlášení ve webovém prohlížeči je následující:

1. Uživatel se pokusí získat přístup k webové aplikaci https://outlook.office365.com/owa/) (například Outlook Web App – z podnikového zařízení připojeného k doméně v podnikové síti.
2. Pokud uživatel ještě není přihlášen, je uživatel přesměrován na přihlašovací stránku Azure AD.
3. Uživatel zadá své uživatelské jméno do přihlašovací stránky Azure AD.

   >[!NOTE]
   >U [některých aplikací](./how-to-connect-sso-faq.md)jsou kroky 2 & 3 přeskočeny.

4. Pomocí JavaScriptu na pozadí Azure AD výzvy prohlížeče prostřednictvím 401 neoprávněné odpovědi, poskytnout lístek Protokolu Kerberos.
5. Prohlížeč zase požaduje lístek ze služby Active `AZUREADSSOACC` Directory pro účet počítače (který představuje Azure AD).
6. Služba Active Directory vyhledá účet počítače a vrátí lístek protokolu Kerberos prohlížeči zašifrovanému pomocí tajného klíče účtu počítače.
7. Prohlížeč předá lístek protokolu Kerberos, který získal ze služby Active Directory, do služby Azure AD.
8. Azure AD dešifruje lístek Kerberos, který zahrnuje identitu uživatele přihlášeného k podnikovému zařízení pomocí dříve sdíleného klíče.
9. Po vyhodnocení Azure AD vrátí token zpět do aplikace nebo požádá uživatele k provedení dalších ověření, jako je například vícefaktorové ověřování.
10. Pokud je přihlášení uživatele úspěšné, uživatel má přístup k aplikaci.

Následující diagram znázorňuje všechny součásti a kroky.

![Bezproblémové jednotné přihlašování – tok webové aplikace](./media/how-to-connect-sso-how-it-works/sso2.png)

Bezproblémové jednotné přihlašování je oportunistické, což znamená, že pokud selže, přihlašovací prostředí se vrátí do svého pravidelného chování - tj.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Jak funguje přihlášení k nativnímu klientovi pomocí bezproblémového jednotného přihlašování?

Tok přihlášení na nativníklientje následující:

1. Uživatel se pokusí získat přístup k nativní aplikaci (například klientovi aplikace Outlook) z podnikového zařízení přilehlého k doméně v podnikové síti.
2. Pokud uživatel ještě není přihlášen, nativní aplikace načte uživatelské jméno uživatele z relace systému Windows zařízení.
3. Aplikace odešle uživatelské jméno do Služby Azure AD a načte koncový bod WS-Trust MEX vašeho klienta. Tento koncový bod WS-Trust se používá výhradně bezešvé jednotné přihlašující funkce a není obecnou implementaci protokolu WS-Trust ve službě Azure AD.
4. Aplikace pak dotazuje WS-Trust MEX koncový bod, aby zjistil, zda je k dispozici koncový bod integrovaného ověřování. Koncový bod integrovaného ověřování se používá výhradně pomocí funkce jednotného přihlašujícího systému.
5. Pokud je krok 4 úspěšný, je vydána výzva protokolu Kerberos.
6. Pokud aplikace je schopná načíst lístek Kerberos, předá ji až do koncového bodu integrovaného ověřování Azure AD.
7. Azure AD dešifruje lístek Kerberos a ověří ho.
8. Azure AD přihlásí uživatele a vydá token SAML do aplikace.
9. Aplikace pak odešle token SAML do koncového bodu tokenu OAuth2 služby Azure AD.
10. Azure AD ověřuje token SAML a problémy do aplikace přístupový token a obnovovací token pro zadaný prostředek a token id.
11. Uživatel získá přístup k prostředku aplikace.

Následující diagram znázorňuje všechny součásti a kroky.

![Bezproblémové jednotné přihlašování – nativní tok aplikací](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Další kroky

- [**Rychlý start**](how-to-connect-sso-quick-start.md) – zprovoznění bezproblémového jednotného přihlašování Azure AD.
- [**Nejčastější dotazy**](how-to-connect-sso-faq.md) - Odpovědi na často kladené otázky.
- [**Poradce při potížích**](tshoot-connect-sso.md) – Přečtěte si, jak vyřešit běžné problémy s funkcí.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - Pro podání nových žádostí o funkce.
