---
title: Metody ověřování – Azure Active Directory
description: Dostupné metody ověřování ve službě Azure AD pro vícefaktorové ověřování a samoobslužné resetování HESLA
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1322c919906dc2d0dd23de538fa2c1992fbe5da0
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164832"
---
# <a name="what-are-authentication-methods"></a>Co jsou metody ověřování?

Jako správce výběr metod ověřování pro Azure Multi-Factor Authentication a samoobslužné resetování hesla (SSPR) se doporučuje vyžadovat od uživatelů registraci několika metod ověřování. Pokud metoda ověřování není k dispozici pro uživatele, můžete se k ověření pomocí jiné metody.

Správci můžou určit v zásadách, které metody ověřování jsou k dispozici pro uživatele samoobslužné resetování HESLA a vícefaktorové ověřování. Některé metody ověřování, nemusí být k dispozici ke všem funkcím. Další informace o konfiguraci zásad najdete v článcích [jak úspěšné zavedení samoobslužného resetování hesla](howto-sspr-deployment.md) a [plánování ověřování Azure Multi-Factor Authentication založené na cloudu](howto-mfa-getstarted.md)

Společnost Microsoft důrazně doporučuje správcům umožňuje uživatelům vybrat více než minimální požadovaný počet metod ověřování v případě, že nemají přístup k jednomu.

|Metoda ověřování|Využití|
| --- | --- |
| Heslo | Vícefaktorové ověřování a samoobslužné resetování HESLA |
| Bezpečnostní otázky | Pouze samoobslužné resetování HESLA |
| E-mailová adresa | Pouze samoobslužné resetování HESLA |
| Aplikace Microsoft Authenticator | Vícefaktorové ověřování a verze public preview pro samoobslužné resetování HESLA |
| Hardwarový token OATH | Verze Public preview pro vícefaktorové ověřování a samoobslužné resetování HESLA |
| SMS | Vícefaktorové ověřování a samoobslužné resetování HESLA |
| Hlasový hovor | Vícefaktorové ověřování a samoobslužné resetování HESLA |
| Hesla aplikací | MFA pouze v určitých případech |

![Metody ověřování použité v přihlašovací obrazovky](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Tokeny OATH hardwaru pro oznámení vícefaktorové ověřování a samoobslužné resetování HESLA a mobilní aplikace nebo kód mobilní aplikace jako metody pro hesla pomocí samoobslužné služby Azure AD obnovení jsou funkce ve verzi public preview služby Azure Active Directory. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Heslo

Hesla Azure AD se považuje za metodu ověřování. To je jedna z metod, které **nejde zakázat**.

## <a name="security-questions"></a>Bezpečnostní otázky

Bezpečnostní otázky jsou k dispozici **pouze v Azure AD hesla pomocí samoobslužné služby pro resetování** účtům bez oprávnění správce.

Pokud používáte bezpečnostní otázky, doporučujeme používat je společně s jinou metodu. Bezpečnostní otázky může být méně bezpečné než jiné metody, protože někteří lidé znát odpovědi na otázky jiným uživatelem.

> [!NOTE]
> Bezpečnostní otázky jsou uloženy privátně a bezpečně na objekt uživatele v adresáři a jako odpověď získat jenom uživatelé během registrace. Neexistuje žádný způsob, jak správci číst nebo upravovat uživatele otázek a odpovědí.
>

### <a name="predefined-questions"></a>Předdefinované dotazy

* Ve kterém městě jste se seznámili s prvním manželem/partnera?
* Ve kterém městě seznámili vaši rodiče?
* Ve kterém městě váš sourozenec, kterého live?
* Ve kterém městě se narodil váš otec?
* Ve kterém městě bylo vaším prvním zaměstnáním?
* Ve kterém městě se narodila vaše matka?
* Městě jste v byli na nový rok 2000?
* Co je příjmení váš oblíbený učitel na střední škole?
* Co je název školy použitý pro ale konferenci?
* Jaký je název místa, ve kterém můžete uchovávat vaše první svatební příjem?
* Jaké je druhé křestní jméno vašeho otce?
* Co je vaše oblíbené jídlo?
* Co je vaše matka matky a příjmení název?
* Jaké je druhé křestní jméno vaší matky?
* Co je měsíci a roce svého nejstaršího sourozence? (např. listopad 1985)
* Jaké je druhé křestní jméno vašeho nejstaršího sourozence?
* Co je uveďte historická a příjmení název?
* Jaké je druhé křestní jméno vašeho nejmladšího sourozence?
* Do které školy jste chodili v šestém ročníku?
* Jak se jmenuje první a poslední název svého nejlepšího kamaráda?
* Jak se jmenuje první a poslední název vaší první partnerky?
* Jaký byl váš oblíbený na podnikové úrovni učitele na prvním stupni příjmení?
* Jak se jmenuje značku a model svého prvního auta nebo motocyklu?
* Jak se jmenovala první škola, které jste docházeli?
* Jak se jmenoval nemocnice, ve kterém jste se narodili?
* Jak se jmenoval ulici jste bydleli vaší úplně prvním bydlišti?
* Jak se jmenoval váš dětský hrdina?
* Co se jmenoval váš nejoblíbenější plyšák?
* Jak se jmenoval váš první domácí mazlíček?
* Jaký byl váš dětství přezdívku?
* Jak se jmenuje váš oblíbený sport na střední škole?
* Co bylo vaším prvním zaměstnáním?
* Co bylo poslední čtyři číslice vašeho telefonního čísla dětství?
* Pokud jste byli mladé, co mají být až vyrostete?
* Kdo je nejslavnějšího člověka, který jste?

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

Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) a [Windows Phone](https://go.microsoft.com/fwlink/?Linkid=825071).

> [!NOTE]
> Uživatelé nebudou mít možnost zaregistrovat svoje mobilní aplikace při registraci pro resetování hesla pomocí samoobslužné služby. Místo toho uživatelé můžou registrovat svoje mobilní aplikace na [ https://aka.ms/mfasetup ](https://aka.ms/mfasetup) nebo v náhledu registrační informace o zabezpečení v [ https://aka.ms/setupsecurityinfo ](https://aka.ms/setupsecurityinfo).
>

### <a name="notification-through-mobile-app"></a>Oznámení přes mobilní aplikaci

Aplikace Microsoft Authenticator pomáhá zabránit neoprávněnému přístupu k účtům a zastavte podvodné transakce doručením (push) oznámení na tablet nebo smartphone. Uživatelé oznámení zobrazte a pokud je legitimní, vyberte možnost ověřit. V opačném případě může vybrat odepřít.

> [!WARNING]
> Pro samoobslužné resetování hesla při jen jedním ze způsobů je požadovaný pro obnovení, ověřovací kód je jedinou možností, které jsou dostupné pro uživatele **k zajištění nejvyšší úrovně zabezpečení**.
>
> Když se vyžadují dvě metody budou uživatelé moct resetovat pomocí **buď** oznámení **nebo** ověřovací kód kromě jiných povolené metody.
>

Pokud povolíte použití obou oznámení přes mobilní aplikace a ověřovací kód z mobilní aplikace, uživatelé, kteří zaregistrovat aplikaci Microsoft Authenticator oznámení pomocí budou moct ověřit svoji identitu pomocí oznámení a kód.

> [!NOTE]
> Pokud má vaše organizace zaměstnanci práci nebo cestě do Číny, **oznámení přes mobilní aplikaci** metodu na **zařízení s Androidem** nefunguje v dané zemi. Alternativní metody by měla být k dispozici pro uživatele.

### <a name="verification-code-from-mobile-app"></a>Ověřovací kód z mobilní aplikace

Aplikace Microsoft Authenticator nebo jiným aplikacím třetích stran může sloužit jako softwarový token k vygenerování ověřovacího kódu OATH. Po zadání uživatelského jména a hesla, zadejte kód aplikace k dispozici na přihlašovací obrazovku. Ověřovací kód, který obsahuje druhou podobu ověřování.

> [!WARNING]
> Pro samoobslužné resetování hesla při pouze pro resetování ověřovací kód je jedinou možností, které jsou dostupné pro uživatele se vyžaduje jedna metoda **k zajištění nejvyšší úrovně zabezpečení**.
>

Uživatelé mohou mít kombinaci až o pěti hardwarové tokeny OATH nebo ověřovací aplikace, jako je nakonfigurován pro použití v každém okamžiku aplikaci Microsoft Authenticator.

## <a name="oath-hardware-tokens-public-preview"></a>Tokeny OATH hardwaru (public preview)

OATH je otevřený standard, která určuje, jak jednorázové heslo (OTP) kódy jsou generovány. Azure AD budou podporovat použití tokenů OATH-TOTP SHA-1 z různých 30sekundovém nebo 60 sekund. Zákazníky můžete pořídit tyto tokeny od dodavatele podle vlastní volby. Tajné klíče jsou omezené na 128 znaků, které nemusí být kompatibilní s všechny tokeny.

![Nahrávání tokenů OATH do okna pro tokeny OATH serveru MFA](media/concept-authentication-methods/oath-tokens-azure-ad.png)

Jako součást verze public preview se podporují hardwarové tokeny OATH. Další informace o verzích Preview najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Jakmile jsou získat tokeny musí být odeslán ve formátu souboru hodnot oddělených čárkami (CSV), stejně jako v příkladu níže ukazuje včetně hlavní název uživatele, sériové číslo, tajný klíč, časový interval, výrobce a model.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Zajistěte, aby že obsahovat řádek záhlaví v souboru CSV, jak je znázorněno výše.

Jakmile ve správném formátu jako soubor CSV může správce a přihlaste se k webu Azure portal a přejděte do **Azure Active Directory**, **MFA Server**, **tokeny OATH**, a Nahrajte výsledný soubor CSV.

V závislosti na velikosti souboru CSV může trvat několik minut ke zpracování. Klikněte na tlačítko **aktualizovat** tlačítko Zobrazit aktuální stav. Pokud v souboru nejsou žádné chyby, budete mít možnost Stáhnout soubor CSV výpis všechny chyby vyřešte.

Jakmile vyřeší všechny chyby, Správce pak může aktivovat každý klíč kliknutím **aktivovat** pro token aktivovat a zadávání kombinací jednorázového HESLA zobrazovaný na tokenu.

Uživatelé mohou mít kombinaci až o pěti hardwarové tokeny OATH nebo ověřovací aplikace, jako je nakonfigurován pro použití v každém okamžiku aplikaci Microsoft Authenticator.

## <a name="phone-options"></a>Možnosti telefonu

### <a name="mobile-phone"></a>Mobilní telefon

Dvě možnosti jsou dostupné pro uživatele s mobilní telefony.

Pokud uživatelé nechtějí zadání čísla mobilního telefonu viditelný v adresáři, ale chtějí používat pro resetování hesla, Správci by neměl naplnit v adresáři. Uživatelé měli naplnit jejich **telefon pro ověření** atribut prostřednictvím [registrační portál pro resetování hesel](https://aka.ms/ssprsetup). Správci mohou zobrazit tyto informace v profilu uživatele, ale nebude publikován jinde.

Pro vše správně fungovalo, musí být telefonní čísla ve formátu *+ CountryCode PhoneNumber*, například 4255551234 + 1.

> [!NOTE]
> Musí být mezera mezi směrové číslo země a telefonní číslo.
>
> Resetování hesla nepodporuje telefonní linky. Dokonce i ve formátu 12345 4255551234 + 1 X se odeberou rozšíření před uvedením volání.

#### <a name="text-message"></a>Textová zpráva

Číslo mobilního telefonu s ověřovacím kódem se pošle zprávu SMS. Zadejte ověřovací kód, který je k dispozici v rozhraní přihlášení pokračujte.

#### <a name="phone-call"></a>Telefonní hovor

Automatizovaný hlasový hovor provádí na telefonní číslo, které zadáte. Odpovědět na volání a stisknutím klávesy # na klávesnici telefonu provede ověření

> [!IMPORTANT]
> Počínaje březnem 2019 možnosti telefonního hovoru nebudou k dispozici uživatelům vícefaktorové ověřování a samoobslužné resetování HESLA v tenantech bezplatné a zkušební verze Azure AD. Zprávy SMS, nejsou ovlivněny tuto změnu. Telefonní hovor budou nadále dostupné uživatelům v placené tenantů Azure AD. Tato změna ovlivní jenom klienty bezplatné a zkušební verze Azure AD.

### <a name="office-phone"></a>Telefon do kanceláře

Automatizovaný hlasový hovor provádí na telefonní číslo, které zadáte. Odpovězte volání a stiskem tlačítka # na klávesnici telefonu provede ověření.

Pro vše správně fungovalo, musí být telefonní čísla ve formátu *+ CountryCode PhoneNumber*, například 4255551234 + 1.

Atribut telefonního office spravuje správce.

> [!IMPORTANT]
> Počínaje březnem 2019 možnosti telefonního hovoru nebudou k dispozici uživatelům vícefaktorové ověřování a samoobslužné resetování HESLA v tenantech bezplatné a zkušební verze Azure AD. Zprávy SMS, nejsou ovlivněny tuto změnu. Telefonní hovor budou nadále dostupné uživatelům v placené tenantů Azure AD. Tato změna ovlivní jenom klienty bezplatné a zkušební verze Azure AD.

> [!NOTE]
> Musí být mezera mezi směrové číslo země a telefonní číslo.
>
> Resetování hesla nepodporuje telefonní linky. Dokonce i ve formátu 12345 4255551234 + 1 X se odeberou rozšíření před uvedením volání.

### <a name="troubleshooting-phone-options"></a>Řešení potíží s Možnosti telefonu

Běžné problémy související s metod ověřování pomocí telefonního čísla:

* ID volajícího blokovaných na jednom zařízení
   * Řešení potíží s zařízení
* Nesprávné telefonní číslo, nesprávné směrové číslo země, telefonní číslo domů oproti telefonní číslo do zaměstnání
   * Řešení potíží s objekt uživatele a nakonfigurovali metody ověřování. Zkontrolujte, jestli jsou registrovány správné telefonní čísla.
* Zadán chybný kód PIN.
   * Potvrďte, že uživatel použil správný kód PIN, které jsou zaregistrované v Azure MFA serveru.
* Volání předané do hlasové pošty
   * Ujistěte se, že uživatel má telefonu zapnutá a tato služba k dispozici v jejich oblasti nebo použijte alternativní metodu.
* Uživatel je blokovaný
   * Požádejte správce odblokování uživatele na webu Azure Portal.
* Zařízení nemá předplatné služby SMS
   * Požádejte uživatele změnit metody nebo aktivace serveru SMS na zařízení.
* Chybný telekomunikačních poskytovatelů (žádný telefonní vstup zjistil, chybějící problémy tóny DTMF, ID volajícího blokovaných na různých zařízeních, nebo blokovaný SMS přes různá zařízení)
   * Společnost Microsoft používá více telekomunikačních poskytovatelů směrovat telefonní hovory a SMS zpráv pro ověřování. Pokud se vám zobrazují všechny výše uvedené problémy mít uživatel pokus použít metodu alespoň 5krát během 5 minut a budete mít tento uživatel informace k dispozici při kontaktování oddělení podpory společnosti Microsoft.

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

[Povolení kombinovaná registrace ve vašem tenantovi](howto-registration-mfa-sspr-combined.md)

[Dokumentace konfigurace metoda ověřování koncového uživatele](https://aka.ms/securityinfoguide)
