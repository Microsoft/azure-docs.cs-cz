---
title: Metody ověřování Azure AD
description: Jaké metody ověřování jsou k dispozici ve službě Azure AD pro vícefaktorové ověřování a samoobslužné resetování HESLA
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: sahenry, michmcla
ms.openlocfilehash: 48f3a77d2aa81cda62f8206709268bae8e7c8737
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/19/2018
ms.locfileid: "39163886"
---
# <a name="what-are-authentication-methods"></a>Co jsou metody ověřování?

Azure AD samoobslužné resetování hesla (SSPR) a Vícefaktorové ověřování (MFA) požádat o další informace, známé jako metody ověřování nebo bezpečnostních údajů, abychom potvrdili, že jste koho se, že jsou při použití související funkce.

Správci můžou určit v zásadách, které metody ověřování jsou k dispozici pro uživatele samoobslužné resetování HESLA a vícefaktorové ověřování. Některé metody ověřování, nemusí být k dispozici ke všem funkcím.

Společnost Microsoft důrazně doporučuje správcům umožňuje uživatelům vybrat více než minimální požadovaný počet metod ověřování v případě, že nemají přístup k jednomu.

|Metoda ověřování|Využití|
| --- | --- |
| Heslo | Vícefaktorové ověřování a samoobslužné resetování HESLA |
| Bezpečnostní otázky | Pouze samoobslužné resetování HESLA |
| E-mailová adresa | Pouze samoobslužné resetování HESLA |
| Aplikace Microsoft Authenticator | Pouze MFA |
| SMS | Vícefaktorové ověřování a samoobslužné resetování HESLA |
| Hlasový hovor | Vícefaktorové ověřování a samoobslužné resetování HESLA |
| Hesla aplikací | MFA pouze v určitých případech |

## <a name="password"></a>Heslo

Hesla Azure AD se považuje za metodu ověřování. To je jedna z metod, které **nejde zakázat**.

## <a name="security-questions"></a>Bezpečnostní otázky

Bezpečnostní otázky jsou k dispozici **pouze v Azure AD hesla pomocí samoobslužné služby pro resetování** účtům bez oprávnění správce.

Pokud používáte bezpečnostní otázky, doporučujeme používat je společně s jinou metodu. Bezpečnostní otázky může být méně bezpečné než jiné metody, protože někteří lidé znát odpovědi na otázky jiným uživatelem.

> [!NOTE]
> Bezpečnostní otázky jsou uloženy privátně a bezpečně na objekt uživatele v adresáři a jako odpověď získat jenom uživatelé během registrace. Neexistuje žádný způsob, jak správci číst nebo upravovat uživatele otázek a odpovědí.
>

### <a name="predefined-questions"></a>Předdefinované dotazy

* V jakém městě jste potkali svého manžela nebo manželku (partnera nebo partnerku)?
* V jakém městě se potkali vaši rodiče?
* V jakém městě žije váš nejbližší sourozenec?
* V jakém městě se narodil váš otec?
* V jakém městě jste měli první práci?
* V jakém městě se narodila vaše matka?
* V jakém městě jste byli na Nový rok 2000?
* Jaké je příjmení vašeho oblíbeného učitele ze střední školy?
* Jaký je název univerzity, na kterou jste se hlásili, ale nechodili na ni?
* Jak se jmenuje město, kde jste měli svatební hostinu?
* Jaký je oblíbený sport vašeho otce?
* Jaké je vaše oblíbené jídlo?
* Jaké je jméno a příjmení vaší babičky z matčiny strany?
* Jak se za svobodna jmenovala vaše matka?
* Co je měsíci a roce svého nejstaršího sourozence? (např. listopad 1985)
* Jaké je křestní jméno vašeho nejstaršího sourozence?
* Jaké je jméno a příjmení vašeho dědečka z otcovy strany?
* Jaké je křestní jméno vašeho nejmladšího sourozence?
* Do jaké školy jste chodili v šesté třídě?
* Jaké měl jméno a příjmení váš nejlepší přítel v dětství?
* Jaké měl jméno a příjmení váš první partner nebo partnerka?
* Jaké bylo příjmení vašeho oblíbeného učitele ze základní školy?
* Jaká byla značka a model vašeho prvního auta nebo motorky?
* Jaký byl název první školy, do které jste chodili?
* Jaký název měla nemocnice, ve které jste se narodili?
* Jak se jmenovala ulice, kde jste v dětství bydleli?
* Jak se jmenoval váš dětský idol?
* Jaké bylo jméno vašeho oblíbeného plyšáka?
* Jak se jmenovalo vaše první domácí zvířátko?
* Jakou jste měli v dětství přezdívku?
* Jaký byl váš oblíbený sport na střední škole?
* Jaká byla vaše první práce?
* Jaké byly poslední čtyři číslice vašeho telefonu v dětství?
* Jaké bylo v dětství vaše vysněné povolání?
* Jakou nejznámější osobnost jste kdy potkali?

Všechny předdefinované bezpečnostní otázky jsou přeloženy a lokalizován do úplnou sadu Office 365 jazyky podle národního prostředí prohlížeče uživatele.

### <a name="custom-security-questions"></a>Vlastní bezpečnostní otázky

Vlastní bezpečnostní otázky nejsou lokalizovány. Všechny vlastní otázky se zobrazují ve stejném jazyce jako jsou zapsány v rozhraní administrativního uživatele, i v případě, že národní prostředí uživatele prohlížeče se liší. Pokud potřebujete lokalizovaná dotazy, měli byste použít předdefinované dotazy.

Maximální délka vlastní bezpečnostní otázky je 200 znaků.

### <a name="security-question-requirements"></a>Požadavky na bezpečnostní otázku

* Limit počtu znaků minimální odpovědí je tři znaky.
* Limit počtu znaků maximální odpovědí je 40 znaků.
* Uživatele nelze odpovězte na stejnou otázku více než jednou.
* Uživatelé nelze zadat stejnou odpověď pro více než jednu otázku.
* Všechny znakové sady lze definovat na otázky a odpovědi, včetně znaků Unicode.
* Počet otázek musí být větší než nebo rovna počtu otázek, které byly zapotřebí k registraci.

## <a name="email-address"></a>E-mailová adresa

E-mailová adresa je k dispozici **pouze v Azure AD hesla pomocí samoobslužné služby pro resetování**.

Společnost Microsoft doporučuje použití e-mailový účet, který nepotřebuje heslo uživatele Azure AD pro přístup.

## <a name="microsoft-authenticator-app"></a>Aplikace Microsoft Authenticator

Aplikace Microsoft Authenticator poskytuje další úroveň zabezpečení, které vaší služby Azure AD pracovní nebo školní účet nebo účtu Microsoft.

Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) a [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071).

### <a name="notification-through-mobile-app"></a>Oznámení přes mobilní aplikaci

Aplikace Microsoft Authenticator pomáhá zabránit neoprávněnému přístupu k účtům a zastavte podvodné transakce doručením (push) oznámení na tablet nebo smartphone. Uživatelé oznámení zobrazte a pokud je legitimní, vyberte možnost ověřit. V opačném případě může vybrat odepřít.

### <a name="verification-code-from-mobile-app"></a>Ověřovací kód z mobilní aplikace

Aplikace Microsoft Authenticator nebo jiným aplikacím třetích stran může sloužit jako softwarový token k vygenerování kódu ověřování OAuth. Po zadání uživatelského jména a hesla, zadejte kód aplikace k dispozici na přihlašovací obrazovku. Ověřovací kód, který obsahuje druhou podobu ověřování.

## <a name="mobile-phone"></a>Mobilní telefon

Dvě možnosti jsou dostupné pro uživatele s mobilní telefony.

Pokud uživatelé nechtějí zadání čísla mobilního telefonu viditelný v adresáři, ale chtějí používat pro resetování hesla, Správci by neměl naplnit v adresáři. Uživatelé měli naplnit jejich **telefon pro ověření** atribut prostřednictvím [registrační portál pro resetování hesel](https://aka.ms/ssprsetup). Správci mohou zobrazit tyto informace v profilu uživatele, ale nebude publikován jinde.

Pro vše správně fungovalo, musí být telefonní čísla ve formátu *+ CountryCode PhoneNumber*, například 4255551234 + 1.

> [!NOTE]
> Musí být mezera mezi směrové číslo země a telefonní číslo.
>
> Resetování hesla nepodporuje telefonní linky. Dokonce i ve formátu 12345 4255551234 + 1 X se odeberou rozšíření před uvedením volání.

### <a name="text-message"></a>Textová zpráva

Číslo mobilního telefonu s ověřovacím kódem se pošle zprávu SMS. Zadejte ověřovací kód, který je k dispozici v rozhraní přihlášení pokračujte.

### <a name="phone-call"></a>Telefonní hovor

Automatizovaný hlasový hovor provádí na telefonní číslo, které zadáte. Odpovědět na volání a stisknutím klávesy # na klávesnici telefonu provede ověření

## <a name="office-phone"></a>Telefon do kanceláře

Automatizovaný hlasový hovor provádí na telefonní číslo, které zadáte. Odpovězte volání a stiskem tlačítka # na klávesnici telefonu provede ověření.

Pro vše správně fungovalo, musí být telefonní čísla ve formátu *+ CountryCode PhoneNumber*, například 4255551234 + 1.

Atribut telefonního office spravuje správce.

> [!NOTE]
> Musí být mezera mezi směrové číslo země a telefonní číslo.
>
> Resetování hesla nepodporuje telefonní linky. Dokonce i ve formátu 12345 4255551234 + 1 X se odeberou rozšíření před uvedením volání.

## <a name="app-passwords"></a>Hesla aplikací

Některé aplikace nezaložené na prohlížeči nepodporují ověření službou Multi-Factor Authentication, pokud uživatel je povolen pro ověřování službou Multi-Factor Authentication a pokus o použití aplikace nezaložené na prohlížeči, nelze ověřit. Heslo aplikace umožňuje uživatelům a pokračujte v ověřování

Pokud jste vynutit ověřování Multi-Factor Authentication pomocí zásad podmíněného přístupu a ne prostřednictvím jednotlivých uživatelů MFA, nebude možné vytvořit hesla aplikací. Aplikace, které používají zásady podmíněného přístupu pro řízení přístupu není nutné hesla aplikací.

Pokud je vaše organizace Federovaná pro jednotné přihlašování s Azure AD a chcete používat Azure MFA, pak mějte na paměti následující:

* Heslo aplikace je ověřováno Azure AD a proto obchází federace. Federace se používá pouze při nastavování hesla aplikace. U federovaných uživatelů (SSO) ukládají hesla do ID organizace. Pokud uživatel odejde ze společnosti, musí tyto informace do ID organizace pomocí nástroje DirSync. Zakázání/odstranění účtu může trvat až tři hodiny pro synchronizaci, což způsobuje zpoždění zakázání/odstranění hesla aplikace ve službě Azure AD.
* Místní nastavení služby Access Control klienta není dodrženo heslem aplikace.
* Žádné místní ověřování protokolování a auditování funkce je dostupná na hesla aplikací.
* Některé pokročilé architektury návrhy může vyžadovat při použití dvoustupňové ověření u klientů, v závislosti na tom, kde se ověřit pomocí kombinace organizační uživatelského jména a hesla aplikace. Pro klienty, kteří ověřování na základě místní infrastrukturu můžete využít organizační uživatelské jméno a heslo. Pro klienty, které se ověřují ve službě Azure AD by použít heslo aplikace.
* Ve výchozím nastavení uživatelé nemůžou vytvářet hesla aplikací. Pokud je potřeba povolit uživatelům vytvoření hesel aplikací, vyberte **povolit uživatelům vytvářet hesla aplikací pro přihlášení do neprohlížečových aplikací možnost** v části Nastavení služby.

## <a name="next-steps"></a>Další postup

[Povolení samoobslužného resetování hesla pro organizaci](quickstart-sspr.md)

[Povolit ověřování Azure Multi-Factor Authentication pro vaši organizaci](howto-mfa-getstarted.md)