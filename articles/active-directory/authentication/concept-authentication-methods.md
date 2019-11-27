---
title: Metody ověřování – Azure Active Directory
description: Metody ověřování dostupné ve službě Azure AD pro MFA a SSPR
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4a7110ad3e6c9c66179c90c02c1572ee83cb9f15
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420752"
---
# <a name="what-are-authentication-methods"></a>Co jsou metody ověřování?

Jako správce vyberte metody ověřování pro Azure Multi-Factor Authentication a Samoobslužné resetování hesla (SSPR). doporučuje se, abyste uživatelům vyžadovali registraci více metod ověřování. Pokud není metoda ověřování pro uživatele k dispozici, může se rozhodnout ověřit jinou metodu.

Správci můžou v zásadách definovat, které metody ověřování jsou dostupné pro uživatele SSPR a MFA. Některé metody ověřování nemusí být k dispozici pro všechny funkce. Další informace o konfiguraci zásad najdete v článcích [postup úspěšného zavedení samoobslužného resetování hesla](howto-sspr-deployment.md) a [plánování cloudového Multi-Factor Authentication Azure](howto-mfa-getstarted.md) .

Microsoft důrazně doporučuje správcům, aby uživatelům umožnil vybrat více než minimální požadovaný počet metod ověřování v případě, že k tomu nemají přístup.

|Metoda ověřování|Využití|
| --- | --- |
| Heslo | MFA a SSPR |
| Bezpečnostní otázky | Jenom SSPR |
| E-mailová adresa | Jenom SSPR |
| Aplikace Microsoft Authenticator | MFA a SSPR |
| Hardwarový token OATH | Verze Public Preview pro MFA a SSPR |
| SMS | MFA a SSPR |
| Hlasový hovor | MFA a SSPR |
| Hesla aplikací | Vícefaktorové ověřování pouze v určitých případech |

![Metody ověřování používané na přihlašovací obrazovce](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Hardwarové tokeny OATH pro MFA a SSPR jsou funkce verze Public Preview Azure Active Directory. Další informace o verzích Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview.|
|     |

## <a name="password"></a>Heslo

Heslo Azure AD se považuje za metodu ověřování. Jedná se o jednu metodu, kterou **nelze zakázat**.

## <a name="security-questions"></a>Bezpečnostní otázky

Bezpečnostní otázky jsou k dispozici **pouze v případě samoobslužného resetování hesla služby Azure AD** na účty bez oprávnění správce.

Pokud používáte bezpečnostní otázky, doporučujeme je používat ve spojení s jinou metodou. Bezpečnostní otázky můžou být méně bezpečné než jiné metody, protože někteří lidé můžou znát odpovědi na otázky jiného uživatele.

> [!NOTE]
> Bezpečnostní otázky se ukládají soukromě a bezpečně na objekt uživatele v adresáři a můžou na ně odpovědět jenom uživatelé během registrace. Neexistuje žádný způsob, jak správce číst ani upravovat otázky a odpovědi uživatele.
>

### <a name="predefined-questions"></a>Předdefinované otázky

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
* Kolik je měsíc a rok narození na stejné úrovni? (např. listopadu 1985)
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

Všechny předdefinované bezpečnostní otázky jsou přeloženy a lokalizovány do úplné sady jazyků Office 365 na základě národního prostředí prohlížeče uživatele.

### <a name="custom-security-questions"></a>Vlastní bezpečnostní otázky

Vlastní bezpečnostní otázky nejsou lokalizovány. Všechny vlastní otázky se zobrazí ve stejném jazyce jako v uživatelském rozhraní pro správu, a to i v případě, že se národní prostředí prohlížeče uživatele liší. Pokud potřebujete lokalizované otázky, měli byste použít předdefinované otázky.

Maximální délka vlastní bezpečnostní otázky je 200 znaků.

### <a name="security-question-requirements"></a>Požadavky na bezpečnostní otázku

* Minimální povolený počet znaků odpovědi je tři znaky.
* Maximální povolený počet znaků odpovědi je 40 znaků.
* Uživatelé nemůžou odpovědět na stejnou otázku více než jednou.
* Uživatelé nemůžou stejnou odpověď poskytnout na více než jednu otázku.
* Všechny znakové sady lze použít k definování otázek a odpovědí, včetně znaků Unicode.
* Počet definovaných otázek musí být větší nebo roven počtu otázek, které byly požadovány k registraci.

## <a name="email-address"></a>E-mailová adresa

E-mailová adresa je k dispozici **pouze v samoobslužném resetování hesla služby Azure AD**.

Microsoft doporučuje používat e-mailový účet, který nepotřebuje k přístupu heslo uživatele Azure AD.

## <a name="microsoft-authenticator-app"></a>Aplikace Microsoft Authenticator

Microsoft Authenticator aplikace poskytuje další úroveň zabezpečení pro pracovní nebo školní účet služby Azure AD nebo účet Microsoft.

Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) a [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

> [!NOTE]
> Uživatelé nebudou mít možnost registrovat svou mobilní aplikaci při registraci pro Samoobslužné resetování hesla. Místo toho si uživatelé můžou svou mobilní aplikaci zaregistrovat na [https://aka.ms/mfasetup](https://aka.ms/mfasetup) nebo ve verzi Preview registrace bezpečnostních údajů v [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).
>

### <a name="notification-through-mobile-app"></a>Oznámení prostřednictvím mobilní aplikace

Aplikace Microsoft Authenticator může přispět k zabránění neoprávněnému přístupu k účtům a zastavovat podvodné transakce vložením oznámení do telefonu Smartphone nebo tabletu. Uživatelé zobrazí oznámení a pokud je to legitimní, vyberte ověřit. V opačném případě mohou vybrat odepřít.

> [!WARNING]
> Pro Samoobslužné resetování hesla, když se pro resetování vyžaduje jenom jedna metoda, je ověřovací kód jedinou možností dostupnou uživatelům, **aby se zajistila nejvyšší úroveň zabezpečení**.
>
> Pokud jsou vyžadovány dvě metody, budou moci uživatelé obnovit pomocí oznámení **nebo** ověřovacího kódu **spolu s jinými** povolenými metodami.
>

Pokud povolíte použití oznámení prostřednictvím mobilní aplikace a ověřovacího kódu z mobilní aplikace, uživatelé, kteří zaregistrují Microsoft Authenticator aplikaci pomocí oznámení, můžou pomocí oznámení a kódu ověřit jejich identitu.

> [!NOTE]
> Pokud má vaše organizace zaměstnanci pracující v nebo na cestách na Čínu, **oznámení prostřednictvím metody mobilní aplikace** v **zařízeních s Androidem** v dané zemi nefunguje. Pro tyto uživatele by měly být k dispozici alternativní metody.

### <a name="verification-code-from-mobile-app"></a>Ověřovací kód z mobilní aplikace

Aplikaci Microsoft Authenticator nebo jiné aplikace třetích stran lze použít jako softwarový token k vytvoření ověřovacího kódu OATH. Po zadání uživatelského jména a hesla zadáte kód poskytnutý aplikací do přihlašovací obrazovky. Ověřovací kód poskytuje druhou formu ověřování.

> [!WARNING]
> Pro Samoobslužné resetování hesla, je-li pro ověřovací kód obnovení k dispozici pouze jedna metoda, je k dispozici pouze možnost pro uživatele, aby se **zajistila nejvyšší úroveň zabezpečení**.
>

Uživatelé můžou mít kombinaci až pěti hardwarových tokenů OATH nebo ověřovacích aplikací, jako je například aplikace Microsoft Authenticator nakonfigurovaná pro použití kdykoli.

## <a name="oath-hardware-tokens-public-preview"></a>Hardwarové tokeny OATH (Public Preview)

OATH je otevřený standard, který určuje, jak jsou generovány kódy jednorázového hesla (jednorázového hesla). Azure AD bude podporovat použití tokenů SHA-1 typu OATH-TOTP pro celou řadu 30 sekund nebo 60 sekund. Zákazníci si tyto tokeny můžou vyřídit od dodavatele dle svého výběru. Tajné klíče jsou omezené na 128 znaků, které nemusí být kompatibilní se všemi tokeny. Tajné klíče musí být kódované v Base32.

![Nahrávají se tokeny OATH do okna tokeny OATH serveru MFA.](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Hardwarové tokeny OATH jsou podporovány v rámci verze Public Preview. Další informace o verzích Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview.

Po získání tokenů musí být nahrané ve formátu CSV (textový soubor s oddělovači (CSV), který obsahuje hlavní název uživatele, sériové číslo, tajný klíč, časový interval, výrobce a model, jak ukazuje následující příklad.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567890abcdef1234567890abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Ujistěte se, že jste do souboru CSV zahrnuli řádek záhlaví, jak vidíte výše.

Po správném formátování souboru CSV se správce může přihlásit k Azure Portal a přejít na **Azure Active Directory**, **MFA Server**, **tokeny Oath**a nahrát výsledný soubor CSV.

V závislosti na velikosti souboru CSV může zpracování trvat několik minut. Kliknutím na tlačítko **aktualizovat** zobrazíte aktuální stav. Pokud v souboru dojde k nějakým chybám, budete mít možnost stáhnout si soubor CSV se seznamem všech chyb, které můžete vyřešit.

Až budou všechny chyby vyřešené, může správce aktivovat každý klíč tak, že klikne na **aktivovat** , aby se token aktivoval a vstoupil do něj heslo zobrazené na tokenu.

Uživatelé můžou mít kombinaci až pěti hardwarových tokenů OATH nebo ověřovacích aplikací, jako je například aplikace Microsoft Authenticator nakonfigurovaná pro použití kdykoli.

## <a name="phone-options"></a>Možnosti telefonu

### <a name="mobile-phone"></a>Mobilní telefon

Pro uživatele s mobilními telefony jsou k dispozici dvě možnosti.

Pokud uživatelé nechtějí, aby se jejich mobilní telefonní číslo zobrazoval v adresáři, ale přesto se chtějí použít pro resetování hesla, Správci by ho neměli naplnit v adresáři. Uživatelé by měli naplnit svůj ověřovací atribut pro **ověřování** pomocí [registračního portálu pro resetování hesla](https://aka.ms/ssprsetup). Správci můžou tyto informace zobrazit v profilu uživatele, ale nepublikují se jinde.

Aby telefonní čísla fungovala správně, musí být ve formátu *+ CountryCode PhoneNumber*, například + 1 4255551234.

> [!NOTE]
> Musí existovat mezera mezi kódem země a telefonním číslem.
>
> Resetování hesla nepodporuje telefonní linky. I ve formátu + 1 4255551234X12345 jsou rozšíření odebrána před tím, než je volání umístěno.

Společnost Microsoft nezaručuje konzistentní doručování zpráv SMS nebo hlasové Multi-Factor Authentication s využitím stejného čísla. V zájmu našich uživatelů může společnost Microsoft kdykoli přidat nebo odebrat krátké kódy, protože provádíme úpravy postupů pro zlepšení poskytování služby SMS. Společnost Microsoft nepodporuje krátké kódy pro země a oblasti kromě USA a Kanady.

#### <a name="text-message"></a>Textová zpráva

SMS se pošle na číslo mobilního telefonu obsahující ověřovací kód. Pokračujte zadáním ověřovacího kódu, který je k dispozici v přihlašovacím rozhraní.

#### <a name="phone-call"></a>Telefonní hovor

Na telefonní číslo, které zadáte, se přivede automatizované hlasové volání. Přihlaste se ke volání a stisknutím klávesy # na klávesnici telefonu proveďte ověření.

> [!IMPORTANT]
> Od března 2019 nebudou možnosti telefonního hovoru k dispozici pro MFA a SSPR uživatele v bezplatných nebo zkušebních klientech Azure AD. Tato změna nemá vliv na zprávy SMS. Telefonní hovor bude dál k dispozici uživatelům v placených klientech Azure AD. Tato změna má vliv jenom na bezplatné nebo zkušební klienty Azure AD.

### <a name="office-phone"></a>Telefon do kanceláře

Na telefonní číslo, které zadáte, se přivede automatizované hlasové volání. Přihlaste se ke volání a stisknutím klávesy # na klávesnici telefonu proveďte ověření.

Aby telefonní čísla fungovala správně, musí být ve formátu *+ CountryCode PhoneNumber*, například + 1 4255551234.

Atribut Office Phone spravuje správce.

> [!IMPORTANT]
> Od března 2019 nebudou možnosti telefonního hovoru k dispozici pro MFA a SSPR uživatele v bezplatných nebo zkušebních klientech Azure AD. Tato změna nemá vliv na zprávy SMS. Telefonní hovor bude dál k dispozici uživatelům v placených klientech Azure AD. Tato změna má vliv jenom na bezplatné nebo zkušební klienty Azure AD.

> [!NOTE]
> Musí existovat mezera mezi kódem země a telefonním číslem.
>
> Resetování hesla nepodporuje telefonní linky. I ve formátu + 1 4255551234X12345 jsou rozšíření odebrána před tím, než je volání umístěno.

### <a name="troubleshooting-phone-options"></a>Řešení potíží s možnostmi telefonu

Běžné problémy související s metodami ověřování pomocí telefonního čísla:

* Blokované ID volajícího na jednom zařízení
   * Řešení potíží s zařízením
* Špatné telefonní číslo, nesprávný kód země, telefonní číslo domů vs. telefonní číslo pracovníka
   * Řešení potíží s uživatelským objektem a nakonfigurovanými metodami ověřování. Zajistěte, aby byla zaregistrována správná telefonní čísla.
* Byl zadán nesprávný kód PIN.
   * Potvrzení, že uživatel použil správný PIN zaregistrovaný v Azure MFA serveru
* Volání předané do hlasové pošty
   * Ujistěte se, že má uživatel zapnutý telefon a že je tato služba dostupná ve své oblasti, nebo použijte jinou metodu.
* Uživatel je blokovaný
   * Správce odblokuje uživatele v Azure Portal.
* Server SMS není přihlášen k odběru zařízení.
   * Proveďte metody změny uživatele nebo na zařízení aktivujte SMS.
* Vadná poskytovatelé telekomunikační služby (nezjistil se žádný vstup na telefonu, chybějící problémy tónů DTMF, blokované ID volajícího na více zařízeních nebo blokované SMS přes více zařízení)
   * Společnost Microsoft používá více poskytovatelů telekomunikačních služeb ke směrování telefonních hovorů a SMS zpráv pro ověřování. Pokud se zobrazí některá z výše uvedených problémů, uživatel se pokusí použít tuto metodu alespoň pětkrát během 5 minut a bude mít k dispozici informace o tomto uživateli při kontaktování podpory Microsoftu.

## <a name="app-passwords"></a>Hesla aplikací

Některé neprohlížečové aplikace nepodporují službu Multi-Factor Authentication, pokud byl uživatel povolen pro vícefaktorové ověřování a pokusí se použít neprohlížečové aplikace, nejde ověřit. Heslo aplikace umožňuje uživatelům pokračovat v ověřování.

Pokud vynutilte Multi-Factor Authentication prostřednictvím zásad podmíněného přístupu, ne přes MFA pro jednotlivé uživatele, nemůžete vytvářet hesla aplikací. Aplikace, které používají zásady podmíněného přístupu k řízení přístupu, nepotřebují hesla aplikací.

Pokud je vaše organizace federované pro jednotné přihlašování pomocí Azure AD a budete používat Azure MFA, pamatujte na tyto podrobnosti:

* Heslo aplikace ověřuje služba Azure AD, takže se federace vynechá. Federace se používá jenom při nastavování hesel aplikací. Pro uživatele federovaného (SSO) se hesla ukládají do ID organizace. Pokud uživatel odejde ze společnosti, musí tyto informace nasměrovat do ID organizace pomocí DirSync. Vypnutí nebo odstranění účtu může trvat až tři hodiny, než se synchronizuje, což zpozdí zakázání a odstranění hesel aplikací v Azure AD.
* Místní nastavení služby Access Control klienta není dodrženo heslem aplikace.
* Pro hesla aplikací nejsou k dispozici žádné místní možnosti protokolování a auditování ověřování.
* Některé pokročilé návrhy architektury mohou vyžadovat použití kombinace uživatelského jména a hesla organizace a hesla aplikací při použití dvoustupňového ověřování u klientů v závislosti na tom, kde jsou ověřovány. U klientů, kteří se ověřují s místní infrastrukturou, byste měli použít uživatelské jméno a heslo organizace. Pro klienty, kteří se ověřují v Azure AD, byste použili heslo aplikace.
* Ve výchozím nastavení uživatelé nemůžou vytvářet hesla aplikací. Pokud potřebujete uživatelům dovolit vytvářet hesla aplikací, vyberte v části nastavení služby **možnost dovolit uživatelům vytvářet hesla aplikací pro přihlášení k neprohlížečovým aplikacím** .

## <a name="next-steps"></a>Další kroky

[Povolení samoobslužného resetování hesla pro vaši organizaci](quickstart-sspr.md)

[Povolení služby Azure Multi-Factor Authentication pro vaši organizaci](howto-mfa-getstarted.md)

[Povolit kombinovanou registraci ve vašem tenantovi](howto-registration-mfa-sspr-combined.md)

[Dokumentace ke konfiguraci metody ověřování koncového uživatele](https://aka.ms/securityinfoguide)
