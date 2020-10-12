---
title: 'Azure AD Connect: bezproblémové jednoduché Sign-On – jak to funguje | Microsoft Docs'
description: Tento článek popisuje, jak Azure Active Directory funguje bezproblémové jednoduché funkce Sign-On.
services: active-directory
keywords: Co je Azure AD Connect, instalace služby Active Directory, požadované součásti pro Azure AD, jednotné přihlašování, jednotné přihlašování
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/16/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: bde937adba8d2469390a6cf404f6cce8c5008e87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86144696"
---
# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory bezproblémové jednotné přihlašování: technický hluboký podrobně

Tento článek obsahuje technické informace o tom, jak Azure Active Directory funguje bezproblémové jednotné Sign-On (bezproblémové jednotné přihlašování).

## <a name="how-does-seamless-sso-work"></a>Jak funguje bezproblémové přihlašování?

Tato část obsahuje tři části:

1. Nastavení bezproblémové funkce jednotného přihlašování.
2. Způsob, jakým přihlašovací transakce jednoho uživatele ve webovém prohlížeči funguje bez problémů jednotného přihlašování.
3. Způsob, jakým přihlašovací transakce jednoho uživatele u nativního klienta funguje bez problémů jednotného přihlašování.

### <a name="how-does-set-up-work"></a>Jak nastavujem práci?

Bezproblémové jednotné přihlašování je povolené pomocí Azure AD Connect, jak je znázorněno [zde](how-to-connect-sso-quick-start.md). Při povolování této funkce dojde k následujícím krokům:

- Účet počítače ( `AZUREADSSOACC` ) se vytvoří v místní službě Active Directory (AD) v každé doménové struktuře služby AD, kterou synchronizujete do Azure AD (pomocí Azure AD Connect).
- Kromě toho se vytvoří několik hlavních názvů služby Kerberos (SPN), které se použijí během procesu přihlášení k Azure AD.
- Dešifrovací klíč protokolu Kerberos účtu počítače se bezpečně sdílí s Azure AD. Pokud existuje více doménových struktur služby AD, každý účet počítače bude mít vlastní jedinečný dešifrovací klíč protokolu Kerberos.

>[!IMPORTANT]
> `AZUREADSSOACC`Účet počítače musí být z bezpečnostních důvodů důrazně chráněn. Pouze správci domény by měli být schopni spravovat účet počítače. Zajistěte, aby delegování protokolu Kerberos na účtu počítače bylo zakázané a aby žádný jiný účet ve službě Active Directory nemá oprávnění k delegování na `AZUREADSSOACC` účtu počítače. Uložte účet počítače do organizační jednotky (OU), kde jsou bezpečné před náhodným odstraněním a k nimž mají přístup pouze správci domény. Dešifrovací klíč protokolu Kerberos v účtu počítače by měl být také považován za citlivý. Důrazně doporučujeme, abyste [převzali šifrovací klíč protokolu Kerberos](how-to-connect-sso-faq.md) `AZUREADSSOACC` účtu počítače nejméně každých 30 dní.

>[!IMPORTANT]
> Bezproblémové jednotné přihlašování podporuje AES256_HMAC_SHA1 typy šifrování AES128_HMAC_SHA1 a RC4_HMAC_MD5 pro protokol Kerberos. Doporučuje se, aby se typ šifrování pro účet AzureADSSOAcc $ nastavil na AES256_HMAC_SHA1, nebo jeden z typů AES vs. RC4 pro zvýšení zabezpečení. Typ šifrování je uložený v atributu msDS-SupportedEncryptionTypes účtu účtu ve službě Active Directory.  Pokud je typ šifrování účtu AzureADSSOAcc $ nastavený na RC4_HMAC_MD5 a chcete ho změnit na jeden z typů šifrování AES, ujistěte se, že jste nejdřív převzali dešifrovací klíč protokolu Kerberos účtu AzureADSSOAcc $, jak je vysvětleno v [dokumentu nejčastějších dotazů](how-to-connect-sso-faq.md) v příslušné otázce. v opačném případě bezproblémové jednotné přihlašování neproběhne.

Po dokončení nastavení funguje bezproblémové jednotné přihlašování stejným způsobem jako jakékoli jiné přihlášení, které používá integrované ověřování systému Windows (IWA).

### <a name="how-does-sign-in-on-a-web-browser-with-seamless-sso-work"></a>Jak se přihlašujete pomocí bezproblémového jednotného přihlašování ve webovém prohlížeči?

Tok přihlášení ve webovém prohlížeči je následující:

1. Uživatel se pokusí o přístup k webové aplikaci (například k aplikaci Outlook Web App – https://outlook.office365.com/owa/) z podnikového zařízení připojeného k doméně v rámci podnikové sítě).
2. Pokud uživatel ještě není přihlášený, uživatel se přesměruje na přihlašovací stránku služby Azure AD.
3. Uživatel zadá uživatelské jméno do přihlašovací stránky služby Azure AD.

   >[!NOTE]
   >U [některých aplikací](./how-to-connect-sso-faq.md)se přeskočí kroky 2 & 3.

4. Při použití JavaScriptu na pozadí se Azure AD dohlásí do prohlížeče prostřednictvím neautorizované odpovědi 401, aby poskytoval lístek protokolu Kerberos.
5. V prohlížeči pak aplikace vyžádá lístek ze služby Active Directory pro `AZUREADSSOACC` účet počítače (který představuje Azure AD).
6. Služba Active Directory vyhledá účet počítače a vrátí lístek protokolu Kerberos do prohlížeče šifrovaného pomocí tajného klíče účtu počítače.
7. Prohlížeč přepošle lístek protokolu Kerberos, který získal ze služby Active Directory do Azure AD.
8. Azure AD dešifruje lístek protokolu Kerberos, který zahrnuje identitu uživatele přihlášeného k firemnímu zařízení pomocí dříve sdíleného klíče.
9. Po vyhodnocení služba Azure AD vrátí token zpátky do aplikace nebo požádá uživatele o provedení dalších důkazů, jako je například Multi-Factor Authentication.
10. Pokud je přihlášení uživatele úspěšné, uživatel bude moci získat přístup k aplikaci.

Následující diagram znázorňuje všechny komponenty a postup, který je součástí.

![Bezproblémové jednotné přihlašování – tok webové aplikace](./media/how-to-connect-sso-how-it-works/sso2.png)

Bezproblémové jednotné přihlašování je příležitostné, což znamená, že pokud se to nepovede, přihlašovací prostředí se vrátí k běžnému chování – tzn. uživatel musí zadat heslo pro přihlášení.

### <a name="how-does-sign-in-on-a-native-client-with-seamless-sso-work"></a>Jak se v nativním klientovi přihlašuje aplikace bez problémů jednotného přihlašování?

Tok přihlášení na nativním klientovi je následující:

1. Uživatel se pokusí o přístup k nativní aplikaci (například k klientovi Outlook) z podnikového zařízení připojeného k doméně ve vaší podnikové síti.
2. Pokud uživatel ještě není přihlášený, nativní aplikace získá uživatelské jméno uživatele z relace systému Windows daného zařízení.
3. Aplikace pošle uživatelské jméno do Azure AD a načte koncový bod klienta WS-Trust MEX. Tento koncový bod WS-Trust používá výhradně funkce bezproblémového jednotného přihlašování a nejedná se o obecnou implementaci WS-Trustho protokolu v Azure AD.
4. Aplikace pak zadá dotaz na koncový bod WS-Trust MEX a zjistí, jestli je k dispozici integrovaný koncový bod ověřování. Integrovaný koncový bod ověřování používá výhradně funkce bezproblémového přihlašování.
5. V případě úspěchu kroku 4 se vydá výzva protokolu Kerberos.
6. Pokud je aplikace schopná načíst lístek protokolu Kerberos, přepošle ho do koncového bodu integrovaného ověřování služby Azure AD.
7. Azure AD dešifruje lístek Kerberos a ověří ho.
8. Služba Azure AD uživatele podepíše a vydá aplikaci token SAML.
9. Aplikace pak odešle token SAML do koncového bodu tokenu OAuth2 služby Azure AD.
10. Azure AD ověří token SAML a v případě, že je přístupový token a obnovovací token pro zadaný prostředek, a token ID.
11. Uživatel získá přístup k prostředku aplikace.

Následující diagram znázorňuje všechny komponenty a postup, který je součástí.

![Bezproblémové jednotné přihlašování – nativní tok aplikací](./media/how-to-connect-sso-how-it-works/sso14.png)

## <a name="next-steps"></a>Další kroky

- [**Rychlé zprovoznění**](how-to-connect-sso-quick-start.md) – zprovoznění služby Azure AD bez problémů s jednotným přihlašováním
- [**Nejčastější dotazy**](how-to-connect-sso-faq.md) – odpovědi na nejčastější dotazy
- [**Řešení potíží**](tshoot-connect-sso.md) – Naučte se řešit běžné problémy s touto funkcí.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – pro nové žádosti o funkce
