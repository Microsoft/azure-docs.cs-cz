---
title: Metody a funkce ověřování – Azure Active Directory
description: Přečtěte si o různých metodách ověřování a funkcích, které jsou k dispozici v Azure Active Directory, které vám pomůžou zlepšit a zabezpečit události přihlášení.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 08f3a3c17a5f6ea519eea04c4b8c5e7025adcbac
ms.sourcegitcommit: 814778c54b59169c5899199aeaa59158ab67cf44
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2020
ms.locfileid: "90052354"
---
# <a name="what-authentication-and-verification-methods-are-available-in-azure-active-directory"></a>Jaké metody ověřování jsou k dispozici v Azure Active Directory?

V rámci přihlašovacího prostředí pro účty v Azure Active Directory (Azure AD) existují různé způsoby, jak se může uživatel sami ověřit. Uživatelské jméno a heslo představují nejběžnější způsob, jak by uživatel historicky poskytoval přihlašovací údaje. Díky modernímu ověřování a funkcím zabezpečení v Azure AD může být základní heslo doplněno nebo nahrazeno dalšími metodami ověřování.

Uživatel ve službě Azure AD se může rozhodnout ověřit pomocí jedné z následujících metod ověřování:

* Tradiční uživatelské jméno a heslo
* Microsoft Authenticator přihlašování se nejenom heslem aplikace
* Hardwarový token OATH nebo klíč zabezpečení FIDO2
* Přihlášení k neheslům založené na SMS

Řada účtů ve službě Azure AD je povolená pro Samoobslužné resetování hesla (SSPR) nebo Azure Multi-Factor Authentication. Mezi tyto funkce patří další metody ověřování, například telefonní hovor nebo bezpečnostní otázky. Doporučuje se, abyste uživatelům vyžadovali registraci více metod ověřování. Když pro uživatele není k dispozici jedna metoda, můžou se rozhodnout ověřit jinou metodu.

Následující tabulka popisuje, jaké metody jsou k dispozici pro primární nebo sekundární ověřování:

| Metoda | Primární ověření | Sekundární ověřování |
| --- | --- | --- |
| [Heslo](#password) | Yes | |
| [Aplikace Microsoft Authenticator](#microsoft-authenticator-app) | Ano (Preview) | MFA a SSPR |
| [FIDO2 klíče zabezpečení (Preview)](#fido2-security-keys) | Yes | Jenom MFA |
| [Tokeny softwaru OATH](#oath-software-tokens) | No | VÍCEFAKTOROVÉHO |
| [Hardwarové tokeny OATH (Preview)](#oath-hardware-tokens-preview) | No | VÍCEFAKTOROVÉHO |
| [SMS](#phone-options) | Ano (Preview) | MFA a SSPR |
| [Hlasový hovor](#phone-options) | No | MFA a SSPR |
| [Bezpečnostní otázky](#security-questions) | No | Jenom SSPR |
| [E-mailová adresa](#email-address) | No | Jenom SSPR |
| [Hesla aplikací](#app-passwords) | No | Vícefaktorové ověřování pouze v určitých případech |

Tyto metody ověřování se dají nakonfigurovat v Azure Portal a stále používají [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

Tento článek popisuje tyto různé metody ověřování a ověření dostupné ve službě Azure AD a veškerá konkrétní omezení nebo omezení.

![Metody ověřování používané na přihlašovací obrazovce](media/concept-authentication-methods/overview-login.png)

## <a name="password"></a>Heslo

Heslo k Azure AD často představuje jednu z primárních metod ověřování. Nemůžete zakázat metodu ověřování hesla.

I když použijete metodu ověřování, jako je například [přihlášení založené na SMS](howto-authentication-sms-signin.md) , když uživatel nepoužije heslo k podepsání, heslo zůstane jako dostupná metoda ověřování.

## <a name="microsoft-authenticator-app"></a>Aplikace Microsoft Authenticator

Ověřovací aplikace poskytuje další úroveň zabezpečení pro pracovní nebo školní účet služby Azure AD nebo účet Microsoft a je dostupná pro [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594)a [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6). S aplikací Microsoft Authenticator se uživatelé můžou během přihlašování ověřovat heslem bez hesla nebo jako další možnost ověření během samoobslužného resetování hesla (SSPR) nebo událostí Azure Multi-Factor Authentication.

Uživatelé mohou obdržet oznámení prostřednictvím mobilní aplikace, aby je schválili nebo zakázali, nebo pomocí aplikace ověřovatele vygenerovat ověřovací kód OATH, který lze zadat v přihlašovacím rozhraní. Pokud povolíte jak oznámení, tak ověřovací kód, můžou uživatelé, kteří si zaregistrují ověřovací aplikaci, použít kteroukoli z metod k ověření jejich identity.

Pokud chcete použít aplikaci ověřovatele na příkazovém řádku, nikoli na kombinaci uživatelského jména a hesla, přečtěte si téma [Povolení přihlášení bez hesla pomocí aplikace Microsoft Authenticator (Preview)](howto-authentication-passwordless-phone.md).

> [!NOTE]
> Uživatelé nemají možnost registraci své mobilní aplikace, když povolují SSPR. Místo toho mohou uživatelé zaregistrovat svou mobilní aplikaci na [https://aka.ms/mfasetup](https://aka.ms/mfasetup) nebo jako součást registrace v rámci kombinovaného zabezpečení údajů na adrese [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) .

### <a name="notification-through-mobile-app"></a>Oznámení prostřednictvím mobilní aplikace

Ověřovací aplikace může pomáhat zabránit neoprávněnému přístupu k účtům a zastavovat podvodné transakce tím, že do telefonu Smartphone nebo tabletu zadají oznámení. Uživatelé zobrazí oznámení a pokud je to legitimní, vyberte **ověřit**. V opačném případě mohou vybrat **Odepřít**.

![Snímek obrazovky s ukázkovým dotazem na přihlašovací aplikaci webového prohlížeče pro dokončení procesu přihlašování](media/tutorial-enable-azure-mfa/azure-multi-factor-authentication-browser-prompt.png)

> [!NOTE]
> Pokud má vaše organizace zaměstnanci pracující v nebo na cestách v Číně, *oznámení prostřednictvím metody mobilní aplikace* na zařízeních s Androidem nefunguje v dané zemi nebo oblasti jako služby Google Play (včetně nabízených oznámení) v této oblasti blokované. Oznámení pro iOS ale funguje. Pro zařízení s Androidem by se měli pro tyto uživatele zpřístupnit alternativní metody ověřování.

### <a name="verification-code-from-mobile-app"></a>Ověřovací kód z mobilní aplikace

Ověřovací kód OATH lze vytvořit pomocí aplikace ověřovatele jako softwarový token. Po zadání uživatelského jména a hesla zadáte kód poskytnutý ověřovací aplikací do přihlašovacího rozhraní. Ověřovací kód poskytuje druhý způsob ověřování.

Uživatelé můžou mít kombinaci až pěti hardwarových tokenů OATH nebo ověřovacích aplikací, jako je například aplikace Microsoft Authenticator, nakonfigurovaná pro použití kdykoli.

> [!WARNING]
> Aby se zajistila nejvyšší úroveň zabezpečení pro Samoobslužné resetování hesla, když se pro resetování vyžaduje jenom jedna metoda, je ověřovací kód jedinou možností, kterou uživatelé mají k dispozici.
>
> Pokud jsou vyžadovány dvě metody, mohou uživatelé kromě jakékoli jiné povolené metody resetovat pomocí oznámení nebo ověřovacího kódu.

## <a name="fido2-security-keys"></a>FIDO2 klíče zabezpečení

FIDO (Fast IDentity online) Alliance pomáhá zvýšit úroveň otevřených standardů ověřování a snížit uživatele hesel jako formy ověřování. FIDO2 je nejnovější standard, který zahrnuje standard webového ověřování (WebAuthn).

Pokud chcete použít klíče zabezpečení FIDO2 při přihlašování, a ne kombinaci uživatelského jména a hesla, přečtěte si téma [Povolení přihlášení k bezpečnostnímu klíči bez hesla FIDO2 (Preview)](howto-authentication-passwordless-security-key.md).

Uživatelé můžou zaregistrovat a pak vybrat FIDO2 klíč zabezpečení v přihlašovacím rozhraní jako hlavní způsob ověřování. Tyto klíče zabezpečení FIDO2 jsou obvykle zařízeními USB, ale mohou také používat Bluetooth nebo NFC. V případě hardwarového zařízení, které zpracovává ověřování, se zabezpečení účtu zvyšuje, protože není k dispozici žádné heslo, které by mohlo být zveřejněné nebo odhadované.

Klíče zabezpečení FIDO2 ve službě Azure AD jsou momentálně ve verzi Preview. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="oath-tokens"></a>Tokeny OATH

OATH TOTP (jednorázové heslo založené na čase) je otevřený standard, který určuje, jak se generují kódy jednorázového hesla (jednorázového hesla). TOTP OATH lze implementovat pomocí softwaru nebo hardwaru pro generování kódů. Azure AD nepodporuje HOTP OATH, což je jiný standard pro generování kódu.

### <a name="oath-software-tokens"></a>Tokeny softwaru OATH

Tokeny OATH softwaru jsou obvykle aplikace, jako je Microsoft Authenticator aplikace a další ověřovací aplikace. Azure AD vygeneruje tajný klíč neboli počáteční hodnotu, která je vstupem do aplikace a používá se ke generování každého jednorázového hesla.

Aplikace ověřovatele automaticky generuje kódy při nastavení nabízených oznámení, takže uživatel bude mít zálohu, i když jejich zařízení nemá připojení. Je také možné použít aplikace třetích stran, které používají OATH TOTP k vytváření kódů.

Některé hardwarové tokeny OATH TOTP jsou programovatelné, což znamená, že nepocházejí s tajným klíčem nebo předinstalovaným předplatným. Tyto programovatelné hardwarové tokeny je možné nastavit pomocí tajného klíče nebo počáteční hodnoty získané z toku nastavení softwarového tokenu. Zákazníci si můžou tyto tokeny koupit od dodavatele podle svého výběru a použít tajný klíč nebo osivo v procesu instalace jeho dodavatele.

### <a name="oath-hardware-tokens-preview"></a>Hardwarové tokeny OATH (Preview)

Azure AD podporuje použití tokenů SHA-1 TOTP OATH, které aktualizují kódy každých 30 nebo 60 sekund. Zákazníci si můžou tyto tokeny koupit od dodavatele dle svého výběru.

Tokeny OATH TOTP jsou obvykle dodávány s tajným klíčem neboli osivem předem naprogramované v tokenu. Tyto klíče musí být zadané ve službě Azure AD, jak je popsáno v následujících krocích. Tajné klíče jsou omezené na 128 znaků, které nemusí být kompatibilní se všemi tokeny. Tajný klíč může obsahovat pouze znaky *a-z* nebo *a-z* a číslice *1-7*a musí být kódovány v *Base32*.

Programovatelné hardwarové tokeny OATH TOTP, které je možné znovu vyhodnotit, můžete nastavit pomocí Azure AD v toku nastavení softwarového tokenu.

Hardwarové tokeny OATH jsou podporovány v rámci verze Public Preview. Další informace o verzích Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview.

![Nahrávají se tokeny OATH do okna tokeny OATH MFA.](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Po získání tokenů musí být nahrané ve formátu textového souboru s oddělovači (CSV), včetně hlavního názvu uživatele, sériového čísla, tajného klíče, časového intervalu, výrobce a modelu, jak je znázorněno v následujícím příkladu:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Ujistěte se, že jste do souboru CSV zahrnuli řádek záhlaví.

Po správném formátování jako souboru CSV se správce může přihlásit k Azure Portal, přejít k **Azure Active Directory > tokeny Oath zabezpečení > MFA >** a nahrát výsledný soubor CSV.

V závislosti na velikosti souboru CSV může zpracování trvat několik minut. Kliknutím na tlačítko **aktualizovat** zobrazíte aktuální stav. Pokud v souboru dojde k chybám, můžete si stáhnout soubor CSV se seznamem případných chyb, které můžete vyřešit. Názvy polí ve staženém souboru CSV se liší od nahrané verze.

Až budou všechny chyby vyřešené, může správce aktivovat každý klíč tak, že vybere možnost **aktivovat** pro token a vstoupí do jednorázového hesla zobrazeného na tokenu.

Uživatelé můžou mít kombinaci až pěti hardwarových tokenů OATH nebo ověřovacích aplikací, jako je například aplikace Microsoft Authenticator, nakonfigurovaná pro použití kdykoli.

## <a name="phone-options"></a>Možnosti telefonu

Pro přímé ověřování pomocí textové zprávy můžete [nakonfigurovat a povolit uživatele ověřování pomocí serveru SMS (Preview)](howto-authentication-sms-signin.md). Přihlášení pomocí SMS je skvělé pro klientské pracovníky. Při přihlášení pomocí SMS nemusí uživatelé znát uživatelské jméno a heslo pro přístup k aplikacím a službám. Uživatel místo toho zadá své registrované mobilní telefonní číslo, obdrží textovou zprávu s ověřovacím kódem a zadá ho do přihlašovacího rozhraní.

Uživatelé se také můžou sami ověřit pomocí mobilního telefonu nebo telefonu v kanceláři jako sekundární formulář ověřování, který se používá během Multi-Factor Authentication Azure nebo Samoobslužné resetování hesla (SSPR).

Aby telefonní čísla fungovala správně, musí být ve formátu *+ CountryCode PhoneNumber*, například *+ 1 4251234567*.

> [!NOTE]
> Musí existovat mezera mezi kódem země/oblasti a telefonním číslem.
>
> Resetování hesla nepodporuje telefonní linky. I ve formátu *+ 1 4251234567X12345* jsou rozšíření odebrána před tím, než je volání umístěno.

### <a name="mobile-phone-verification"></a>Ověření mobilního telefonu

V případě Azure Multi-Factor Authentication nebo SSPR se uživatelé mohou rozhodnout, že obdrží textovou zprávu s ověřovacím kódem, který se má zadat do přihlašovacího rozhraní, nebo přijmout telefonní hovor s výzvou k zadání kódu PIN.

Pokud uživatelé nechtějí, aby se jejich mobilní telefonní číslo zobrazoval v adresáři, ale chtějí je použít pro resetování hesla, Správci by neměli v adresáři naplnit telefonní číslo. Místo toho by měli uživatelé naplnit svůj ověřovací atribut pro **ověřování** prostřednictvím kombinovaného zápisu bezpečnostních údajů na adrese [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo) . Správci můžou tyto informace zobrazit v profilu uživatele, ale nepublikují se jinde.

![Snímek obrazovky Azure Portal, který zobrazuje metody ověřování s vyplněným telefonním číslem](media/concept-authentication-methods/user-authentication-methods.png)

Společnost Microsoft nezaručuje konzistentní doručování zpráv SMS Multi-Factor Authentication v rámci služby Azure nebo hlasové hovory stejným číslem. V zájmu našich uživatelů můžeme kdykoli přidat nebo odebrat krátké kódy, protože provádíme úpravy postupů pro zlepšení poskytování služby SMS. Microsoft nepodporuje krátké kódy pro země a oblasti kromě USA a Kanady.

#### <a name="text-message-verification"></a>Ověření textové zprávy

Při ověřování pomocí textové zprávy během SSPR nebo Azure Multi-Factor Authentication se zpráva SMS pošle na číslo mobilního telefonu obsahující ověřovací kód. Pro dokončení procesu přihlašování se zadaný ověřovací kód zadá do přihlašovacího rozhraní.

#### <a name="phone-call-verification"></a>Ověření telefonního hovoru

Při ověřování pomocí telefonního hovoru během SSPR nebo Azure Multi-Factor Authentication se na telefonním čísle zaregistrovaného uživatelem přivede automatizované hlasové volání. K dokončení procesu přihlašování se uživateli zobrazí výzva k zadání čísla PIN kódu, na kterém následuje znak # na své klávesnici.

### <a name="office-phone-verification"></a>Ověřování pro telefon do kanceláře

Atribut Office Phone spravuje správce Azure AD a nemůže být zaregistrovaný uživatelem.

Při ověřování pomocí telefonního hovoru během SSPR nebo Azure Multi-Factor Authentication se na telefonním čísle zaregistrovaného uživatelem přivede automatizované hlasové volání. K dokončení procesu přihlašování se uživateli zobrazí výzva k zadání čísla PIN kódu, na kterém následuje znak # na své klávesnici.

### <a name="troubleshooting-phone-options"></a>Řešení potíží s možnostmi telefonu

Pokud máte problémy s ověřováním pomocí telefonu pro Azure AD, přečtěte si následující postup řešení potíží:

* Blokované ID volajícího na jednom zařízení.
   * Zkontrolujte všechna blokovaná čísla nakonfigurovaná na zařízení.
* Chybné telefonní číslo nebo nesprávné číslo země/oblasti nebo nejasnost mezi osobním telefonním číslem a pracovním telefonním číslem.
   * Řešení potíží s uživatelským objektem a nakonfigurovanými metodami ověřování. Ujistěte se, že jsou registrována správná telefonní čísla.
* Byl zadán nesprávný kód PIN.
   * Potvrďte, že uživatel použil pro svůj účet správný PIN kód.
* Volání předané do hlasové pošty.
   * Ujistěte se, že má uživatel zapnutý telefon a že je tato služba k dispozici ve své oblasti, nebo použijte alternativní metodu.
* Uživatel je blokovaný
   * Správce Azure AD odblokuje uživatele v Azure Portal.
* Server SMS není přihlášen k odběru zařízení.
   * Proveďte metody změny uživatele nebo na zařízení aktivujte SMS.
* Chybné poskytovatele telekomunikačních služeb, jako je například nenalezen žádný telefonní vstup, chybějící problémy tónů DTMF, blokované ID volajícího na více zařízeních nebo blokované SMS přes více zařízení.
   * Společnost Microsoft používá více poskytovatelů telekomunikačních služeb ke směrování telefonních hovorů a SMS zpráv pro ověřování. Pokud se zobrazí některý z výše uvedených problémů, uživatel se pokusí použít metodu alespoň pětkrát během 5 minut a nechat informace o tomto uživateli k dispozici při kontaktování podpory Microsoftu.

## <a name="security-questions"></a>Bezpečnostní otázky

Bezpečnostní otázky se během přihlašovací události nepoužívají jako metoda ověřování. Místo toho se bezpečnostní otázky dají použít během procesu samoobslužného resetování hesla (SSPR), abyste se ujistili, kdo jste. Účty správců nemůžou použít bezpečnostní otázky jako metodu ověřování pomocí SSPR.

Když se uživatelé registrují pro SSPR, zobrazí se jim výzva k výběru metod ověřování, které se mají použít. Pokud se rozhodnete použít bezpečnostní otázky, vyberou ze sady otázek výzvu a pak poskytnou vlastní odpovědi.

![Snímek obrazovky Azure Portal, který zobrazuje metody a možnosti ověřování pro bezpečnostní otázky](media/concept-authentication-methods/security-questions-authentication-method.png)

> [!NOTE]
> Bezpečnostní otázky se ukládají soukromě a bezpečně na objekt uživatele v adresáři a můžou na ně odpovědět jenom uživatelé během registrace. Neexistuje žádný způsob, jak může správce číst ani upravovat otázky a odpovědi uživatele.

Bezpečnostní otázky můžou být méně bezpečné než jiné metody, protože někteří lidé můžou znát odpovědi na otázky jiného uživatele. Pokud používáte bezpečnostní otázky v SSPR, doporučujeme je používat ve spojení s jinou metodou. Uživatel může být vyzván, aby k ověření své identity během procesu SSPR použili aplikaci Microsoft Authenticator nebo telefon, a zvolit bezpečnostní otázky pouze v případě, že k nim nemají telefon nebo registrované zařízení.

### <a name="predefined-questions"></a>Předdefinované otázky

Následující předdefinované bezpečnostní otázky jsou k dispozici pro použití jako metoda ověřování pomocí SSPR. Všechny tyto bezpečnostní otázky jsou přeloženy a lokalizovány do úplné sady Microsoft 365 jazyků na základě národního prostředí prohlížeče uživatele:

* V jakém městě jste se vyplnili jako první manželka/partner?
* V jakém městě se vaše rodiče naplnily?
* V jakém městě je vaše nejbližší na stejné úrovni jako v reálném čase?
* V jakém městě se narodil váš otce?
* V jakém městě jste měli první úlohu?
* V jakém městě se narodila vaše matka?
* V jakém městě jste byli na novém roce 2000?
* Jaký je příjmení vašeho oblíbeného učitele v rámci střední školy?
* Jaký je název školy, na kterou jste použili, ale neúčastnili jste se?
* Jaký je název místa, ve kterém jste si podrželi svou první svatbu?
* Co je prostřední jméno vašeho otce?
* Co je vaše oblíbená jídla?
* Co je jméno a příjmení babičkye jméno?
* Jaké je prostřední jméno matky?
* Kolik je měsíc a rok narození na stejné úrovni? (například listopad 1985)
* Jaké je druhé křestní jméno vašeho nejstaršího sourozence?
* Co je jméno a příjmení dědečkae dědečka?
* Jaké je prostřední jméno na stejné úrovni nejmladšího sourozence?
* Jakou školu jste se účastnili pro šestou třídu?
* Jak se jednalo o křestní jméno a příjmení nejlepšího přítele v dětství?
* Jak se jednalo o první a poslední jméno vašeho prvního
* Jak se jednalo o příjmení vašeho oblíbeného učitele na úrovni školy?
* Jaká byla značka a model vašeho prvního automobilu nebo motocyklu?
* Jak se jednalo o název první školy, na kterou jste navštěvovali?
* Jak se jednalo o název nemocnice, ve které jste se narodili?
* Jak se jednalo o název ulice vaší první dětství v dětství?
* Jak se jednalo o název vaší dětství Hero?
* Jaký byl název vašeho oblíbeného zvířete?
* Jaký byl název vaší první PET?
* Jaká byla vaše dětská Přezdívka?
* Jaký byl váš oblíbený sport ve vysoké škole?
* Jaká byla vaše první úloha?
* Jaké byly poslední čtyři číslice telefonního čísla v dětství?
* Když jste byli mladí, co byste chtěli mít v době, kdy jste ho rozrostli?
* Kdo je to nejvíc slavnýchá osoba, kterou jste dřív splnili?

### <a name="custom-security-questions"></a>Vlastní bezpečnostní otázky

Pro větší flexibilitu můžete definovat vlastní bezpečnostní otázky. Maximální délka vlastní bezpečnostní otázky je 200 znaků.

Vlastní bezpečnostní otázky nejsou automaticky lokalizovány jako výchozí bezpečnostní otázky. Všechny vlastní otázky se zobrazí ve stejném jazyce jako v uživatelském rozhraní pro správu, a to i v případě, že se národní prostředí prohlížeče uživatele liší. Pokud potřebujete lokalizované otázky, měli byste použít předdefinované otázky.

### <a name="security-question-requirements"></a>Požadavky na bezpečnostní otázku

Pro výchozí i vlastní bezpečnostní otázky platí následující požadavky a omezení:

* Minimální povolený počet znaků odpovědi je tři znaky.
* Maximální povolený počet znaků odpovědi je 40 znaků.
* Uživatelé nemůžou odpovědět na stejnou otázku více než jednou.
* Uživatelé nemůžou stejnou odpověď poskytnout na více než jednu otázku.
* Všechny znakové sady lze použít k definování otázek a odpovědí, včetně znaků Unicode.
* Počet definovaných otázek musí být větší nebo roven počtu otázek, které byly požadovány k registraci.

## <a name="email-address"></a>E-mailová adresa

E-mailovou adresu nelze použít jako metodu přímého ověřování. E-mailová adresa je k dispozici pouze jako možnost ověření pro Samoobslužné resetování hesla (SSPR). Když se během SSPR vybere e-mailová adresa, pošle se uživateli e-mail, aby dokončil proces ověřování/ověřování.

Během registrace pro SSPR uživatel poskytne e-mailovou adresu, která se má použít. Doporučuje se použít jiný e-mailový účet než jejich podnikový účet, abyste se ujistili, že k nim budou mít přístup během SSPR.

## <a name="app-passwords"></a>Hesla aplikací

Některé starší neprohlížečové aplikace nerozumí pozastavení nebo přerušení v procesu ověřování. Pokud je pro službu Multi-Factor Authentication povolený uživatel a pokusí se použít některou z těchto starších aplikací, které nepoužívají prohlížeč, obvykle se nemůžou úspěšně ověřit. Heslo aplikace umožňuje uživatelům pokračovat v úspěšném ověřování pomocí starších neprohlížečových aplikací bez přerušení.

Ve výchozím nastavení uživatelé nemůžou vytvářet hesla aplikací. Pokud potřebujete, aby uživatelé mohli vytvářet hesla aplikací, vyberte možnost **dovolit uživatelům vytvářet hesla aplikací pro přihlášení k neprohlížečovým aplikacím** v části *nastavení služby* pro vlastnosti Azure Multi-Factor Authentication pro uživatele.

![Snímek obrazovky Azure Portal, který zobrazuje nastavení služby pro službu Multi-Factor Authentication, aby uživatel mohl použít hesla aplikací](media/concept-authentication-methods/app-password-authentication-method.png)

Pokud vynutili Azure Multi-Factor Authentication používáním zásad podmíněného přístupu, nikoli prostřednictvím MFA pro jednotlivé uživatele, nemůžete vytvářet hesla aplikací. Moderní aplikace, které používají zásady podmíněného přístupu k řízení přístupu, nepotřebují hesla aplikací.

Pokud je vaše organizace federované pro jednotné přihlašování (SSO) s Azure AD a používáte Azure Multi-Factor Authentication, platí následující požadavky:

* Heslo aplikace ověřuje služba Azure AD, takže se federace vynechá. Federace se používá jenom při nastavování hesel aplikací. Pro uživatele federovaného (SSO) se hesla ukládají do ID organizace. Pokud uživatel odejde ze společnosti, musí tyto informace nasměrovat do ID organizace pomocí DirSync. Synchronizace nebo události při zakázaném účtu můžou trvat až tři hodiny, což zpozdí vypnutí nebo odstranění hesel aplikací v Azure AD.
* Nastavení místního klienta Access Control nedodržují hesla aplikací.
* Pro hesla aplikací nejsou k dispozici žádné místní možnosti protokolování ověřování nebo auditování.
* Některé pokročilé návrhy architektury můžou vyžadovat použití kombinace uživatelského jména a hesla organizace a hesla aplikací při použití vícefaktorového ověřování v závislosti na tom, kde se ověřují.
    * U klientů, kteří se ověřují s místní infrastrukturou, byste měli použít uživatelské jméno a heslo organizace.
    * Pro klienty, kteří se ověřují v Azure AD, byste použili heslo aplikace.

## <a name="next-steps"></a>Další kroky

Informace o tom, jak začít, najdete v [kurzu samoobslužného resetování hesla (SSPR)][tutorial-sspr] a [Azure Multi-Factor Authentication][tutorial-azure-mfa].

Další informace o konceptech SSPR najdete v tématu [Jak funguje Samoobslužné resetování hesla služby Azure AD][concept-sspr].

Další informace o principech vícefaktorového ověřování najdete v tématu [Jak funguje Azure Multi-Factor Authentication][concept-mfa].

Přečtěte si další informace o konfiguraci metod ověřování pomocí [Microsoft Graph REST API beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
