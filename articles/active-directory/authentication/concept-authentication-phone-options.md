---
title: Metody ověřování telefonem – Azure Active Directory
description: Další informace o použití metod ověřování na telefonu v Azure Active Directory k usnadnění zlepšování a zabezpečení událostí přihlašování
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/18/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9d3a00491bc0628244a5a7907f0dee03a5f8d3c
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744189"
---
# <a name="authentication-methods-in-azure-active-directory---phone-options"></a>Metody ověřování v Azure Active Directory Možnosti telefonu

Pro přímé ověřování pomocí textové zprávy můžete [nakonfigurovat a povolit uživatele ověřování pomocí serveru SMS (Preview)](howto-authentication-sms-signin.md). Přihlášení pomocí SMS je skvělé pro klientské pracovníky. Při přihlášení pomocí SMS nemusí uživatelé znát uživatelské jméno a heslo pro přístup k aplikacím a službám. Uživatel místo toho zadá své registrované mobilní telefonní číslo, obdrží textovou zprávu s ověřovacím kódem a zadá ho do přihlašovacího rozhraní.

Uživatelé se také můžou sami ověřit pomocí mobilního telefonu nebo telefonu v kanceláři jako sekundární formulář ověřování, který se používá během Multi-Factor Authentication služby Azure AD nebo Samoobslužné resetování hesla (SSPR).

Aby telefonní čísla fungovala správně, musí být ve formátu *+ CountryCode PhoneNumber*, například *+ 1 4251234567*.

> [!NOTE]
> Musí existovat mezera mezi kódem země/oblasti a telefonním číslem.
>
> Resetování hesla nepodporuje telefonní linky. I ve formátu *+ 1 4251234567X12345* jsou rozšíření odebrána před tím, než je volání umístěno.

## <a name="mobile-phone-verification"></a>Ověření mobilního telefonu

V případě Multi-Factor Authentication nebo SSPR Azure AD se uživatelé můžou rozhodnout, že obdrží textovou zprávu s ověřovacím kódem, který se má zadat do přihlašovacího rozhraní, nebo získá telefonní hovor.

Pokud uživatelé nechtějí, aby se jejich mobilní telefonní číslo zobrazoval v adresáři, ale chtějí je použít pro resetování hesla, Správci by neměli v adresáři naplnit telefonní číslo. Místo toho by měli uživatelé naplnit svůj ověřovací atribut pro **ověřování** prostřednictvím kombinovaného zápisu bezpečnostních údajů na adrese [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . Správci můžou tyto informace zobrazit v profilu uživatele, ale nepublikují se jinde.

:::image type="content" source="media/concept-authentication-methods/user-authentication-methods.png" alt-text="Snímek obrazovky Azure Portal, který zobrazuje metody ověřování s vyplněným telefonním číslem":::

Společnost Microsoft nezaručuje konzistentní doručování výzev služby Azure AD na základě služby SMS nebo hlasové služby v podobě hlasu Multi-Factor Authentication stejným číslem. V zájmu našich uživatelů můžeme kdykoli přidat nebo odebrat krátké kódy, protože provádíme úpravy postupů pro zlepšení poskytování služby SMS. Microsoft nepodporuje krátké kódy pro země a oblasti kromě USA a Kanady.

### <a name="text-message-verification"></a>Ověření textové zprávy

Při ověřování pomocí textové zprávy během SSPR nebo Azure Multi-Factor Authentication AD se pošle SMS na číslo mobilního telefonu obsahující ověřovací kód. Pro dokončení procesu přihlašování se zadaný ověřovací kód zadá do přihlašovacího rozhraní.

### <a name="phone-call-verification"></a>Ověření telefonního hovoru

Při ověřování pomocí telefonního hovoru během SSPR nebo Azure Multi-Factor Authentication AD se na telefonním čísle zaregistrovaného uživatelem přivede automatizované hlasové volání. K dokončení procesu přihlašování se uživateli zobrazí výzva k stisknutí klávesy # na své klávesnici.

## <a name="office-phone-verification"></a>Ověřování pro telefon do kanceláře

Při ověřování pomocí telefonního hovoru během SSPR nebo Azure Multi-Factor Authentication AD se na telefonním čísle zaregistrovaného uživatelem přivede automatizované hlasové volání. K dokončení procesu přihlašování se uživateli zobrazí výzva k stisknutí klávesy # na své klávesnici.

## <a name="troubleshooting-phone-options"></a>Řešení potíží s možnostmi telefonu

Pokud máte problémy s ověřováním pomocí telefonu pro Azure AD, přečtěte si následující postup řešení potíží:

* "Dosáhli jste limitu ověřovacích hovorů" nebo "při přihlašování jste dosáhli limitu pro ověřovací kódy textu".
   * Společnost Microsoft může v krátké době omezit opakované pokusy o ověření prováděné stejným uživatelem. Toto omezení se nevztahuje na Microsoft Authenticator nebo ověřovací kód. Pokud jste dosáhli těchto omezení, můžete použít ověřovací aplikaci, ověřovací kód nebo se pokusit znovu přihlásit za několik minut.
* Blokované ID volajícího na jednom zařízení.
   * Zkontrolujte všechna blokovaná čísla nakonfigurovaná na zařízení.
* Chybné telefonní číslo nebo nesprávné číslo země/oblasti nebo nejasnost mezi osobním telefonním číslem a pracovním telefonním číslem.
   * Řešení potíží s uživatelským objektem a nakonfigurovanými metodami ověřování. Ujistěte se, že jsou registrována správná telefonní čísla.
* Byl zadán nesprávný kód PIN.
   * Potvrďte, že uživatel použil pro svůj účet správný PIN kód (jenom uživatelé MFA serveru).
* Volání předané do hlasové pošty.
   * Ujistěte se, že má uživatel zapnutý telefon a že je tato služba k dispozici ve své oblasti, nebo použijte alternativní metodu.
* Uživatel je blokovaný
   * Správce Azure AD odblokuje uživatele v Azure Portal.
* Server SMS není přihlášen k odběru zařízení.
   * Proveďte metody změny uživatele nebo na zařízení aktivujte SMS.
* Chybné poskytovatele telekomunikačních služeb, jako je například nenalezen žádný telefonní vstup, chybějící problémy tónů DTMF, blokované ID volajícího na více zařízeních nebo blokované SMS přes více zařízení.
   * Společnost Microsoft používá více poskytovatelů telekomunikačních služeb ke směrování telefonních hovorů a SMS zpráv pro ověřování. Pokud se zobrazí některý z výše uvedených problémů, uživatel se pokusí použít metodu alespoň pětkrát během 5 minut a nechat informace o tomto uživateli k dispozici při kontaktování podpory Microsoftu.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít, najdete v [kurzu samoobslužného resetování hesla (SSPR)][tutorial-sspr] a [Azure AD Multi-Factor Authentication][tutorial-azure-mfa].

Další informace o konceptech SSPR najdete v tématu [Jak funguje Samoobslužné resetování hesla služby Azure AD][concept-sspr].

Další informace o principech vícefaktorového ověřování najdete v tématu [Jak funguje Azure AD Multi-Factor Authentication][concept-mfa].

Přečtěte si další informace o konfiguraci metod ověřování pomocí [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta&preserve-view=true).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
