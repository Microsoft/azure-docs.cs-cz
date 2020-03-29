---
title: Metody ověřování – Služba Azure Active Directory
description: Metody ověřování dostupné ve službě Azure AD pro vícefaktorové ověřování a sspr
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5a82c69575e82a7cf397955f08c3f114e449ba6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968777"
---
# <a name="what-are-authentication-methods"></a>Co jsou metody ověřování?

Jako správce, výběr metod ověřování pro Azure Vícefaktorové ověřování a samoobslužné resetování hesla (SSPR) se doporučuje vyžadovat, aby uživatelé zaregistrovat více metod ověřování. Pokud metoda ověřování není pro uživatele k dispozici, může se rozhodnout pro ověření jinou metodou.

Správci mohou v zásadách definovat, které metody ověřování jsou k dispozici uživatelům sspr a mfa. Některé metody ověřování nemusí být k dispozici pro všechny funkce. Další informace o konfiguraci zásad najdete v článcích [Jak úspěšně zavést samoobslužné resetování hesla](howto-sspr-deployment.md) a plánování [cloudového vícefaktorového ověřování Azure.](howto-mfa-getstarted.md)

Společnost Microsoft důrazně doporučuje správcům, aby uživatelům umožnili vybrat více než minimální požadovaný počet metod ověřování v případě, že k ní nemají přístup.

|Metoda ověřování|Využití|
| --- | --- |
| Heslo | Vícefaktorové a sspr |
| Bezpečnostní otázky | Pouze sspr |
| E-mailová adresa | Pouze sspr |
| Aplikace Microsoft Authenticator | Vícefaktorové a sspr |
| Token hardwaru OATH | Veřejná předběžná verze pro vícefaktorové a sspr |
| SMS | Vícefaktorové a sspr |
| Hlasový hovor | Vícefaktorové a sspr |
| Hesla aplikací | Vícefaktorové finanční finanční vyhodství pouze v některých případech |

![Metody ověřování používané na přihlašovací obrazovce](media/concept-authentication-methods/overview-login.png)

|     |
| --- |
| Hardwarové tokeny OATH pro vícefaktorové ověřování a snop jsou funkce služby Azure Active Directory ve verzi Preview ve veřejné verzi. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="password"></a>Heslo

Heslo azure ad je považováno za metodu ověřování. Je to jedna metoda, která **nemůže být zakázána**.

## <a name="security-questions"></a>Bezpečnostní otázky

Otázky zabezpečení jsou k dispozici **jenom ve službě Azure AD samoobslužné heslo resetovat** na účty bez oprávnění správce.

Pokud používáte bezpečnostní otázky, doporučujeme je použít ve spojení s jinou metodou. Bezpečnostní otázky mohou být méně bezpečné než jiné metody, protože někteří uživatelé mohou znát odpovědi na otázky jiného uživatele.

> [!NOTE]
> Bezpečnostní otázky jsou uloženy soukromě a bezpečně na objekt uživatele v adresáři a mohou být zodpovězeny pouze uživateli při registraci. Neexistuje žádný způsob, jak by správce mohl číst nebo upravovat otázky nebo odpovědi uživatele.
>

### <a name="predefined-questions"></a>Předdefinované otázky

* V jakém městě jste se setkali se svým prvním manželem /partnerem?
* V jakém městě se tví rodiče setkali?
* V jakém městě žije váš nejbližší sourozenec?
* V jakém městě se narodil tvůj otec?
* V jakém městě jste měl první práci?
* V jakém městě se narodila tvoje matka?
* V jakém městě jste byl na Nový rok 2000?
* Jaké je příjmení vašeho oblíbeného učitele na střední škole?
* Jak se jmenuje vysoká škola, na kterou jste se přihlásili, ale nezúčastnili jste se jí?
* Jak se jmenuje místo, kde jste uspořádali svou první svatební hostinu?
* Jaké je prostřední jméno tvého otce?
* Jaké je vaše oblíbené jídlo?
* Jaké je jméno a příjmení babičky z matčiny strany?
* Jaké je prostřední jméno tvé matky?
* Jaký je narozeninový měsíc a rok vašeho nejstaršího sourozence? (např. listopad 1985)
* Jaké je prostřední jméno vašeho nejstaršího sourozence?
* Jaké je jméno a příjmení vašeho dědečka z otcovy a tvého dědečka z otcovy je?
* Jaké je prostřední jméno vašeho nejmladšího sourozence?
* Do jaké školy jsi chodil v šesté třídě?
* Jaké bylo jméno a příjmení vašeho nejlepšího přítele z dětství?
* Jaké bylo jméno a příjmení vašeho prvního významného druhého?
* Jaké bylo příjmení tvého oblíbeného učitele na základní škole?
* Jaká byla vaše první automobilová a motorka?
* Jak se jmenovala první škola, kterou jste navštěvoval?
* Jak se jmenovala nemocnice, ve které jste se narodil?
* Jak se jmenovala ulice vašeho prvního dětského domova?
* Jak se jmenoval tvůj hrdina z dětství?
* Jak se jmenovalo tvé oblíbené plyšové zvíře?
* Jak se jmenoval tvůj první mazlíček?
* Jaká byla vaše dětská přezdívka?
* Jaký byl váš oblíbený sport na střední škole?
* Jaká byla vaše první práce?
* Jaké byly poslední čtyři číslice vašeho dětského telefonního čísla?
* Když jsi byl mladý, čím jsi chtěl být, až vyrosteš?
* Kdo je nejslavnější osoba, kterou jste kdy potkal?

Všechny předdefinované bezpečnostní otázky jsou přeloženy a lokalizovány do úplné sady jazyků Office 365 na základě národního prostředí prohlížeče uživatele.

### <a name="custom-security-questions"></a>Vlastní bezpečnostní otázky

Vlastní bezpečnostní otázky nejsou lokalizovány. Všechny vlastní otázky jsou zobrazeny ve stejném jazyce, jako jsou zadány v uživatelském rozhraní pro správu, i když je národní prostředí prohlížeče uživatele odlišné. Pokud potřebujete lokalizované otázky, měli byste použít předdefinované otázky.

Maximální délka vlastní bezpečnostní otázky je 200 znaků.

### <a name="security-question-requirements"></a>Požadavky na bezpečnostní otázky

* Minimální limit znaků odpovědi je tři znaky.
* Maximální limit znaků odpovědi je 40 znaků.
* Uživatelé nemohou odpovědět na stejnou otázku více než jednou.
* Uživatelé nemohou poskytnout stejnou odpověď na více než jednu otázku.
* Libovolnou znakovou sadu lze použít k definování otázek a odpovědí, včetně znaků Unicode.
* Počet definovaných otázek musí být větší nebo roven počtu otázek, které byly nutné k registraci.

## <a name="email-address"></a>E-mailová adresa

E-mailová adresa je k dispozici **jenom v samoobslužné mašle Azure AD**.

Společnost Microsoft doporučuje použití e-mailového účtu, který by nevyžadoval přístup k heslu Azure AD uživatele.

## <a name="microsoft-authenticator-app"></a>Aplikace Microsoft Authenticator

Aplikace Microsoft Authenticator poskytuje další úroveň zabezpečení pracovního nebo školního účtu Azure AD nebo vašeho účtu Microsoft.

Aplikace Microsoft Authenticator je k dispozici pro [Android](https://go.microsoft.com/fwlink/?linkid=866594), [iOS](https://go.microsoft.com/fwlink/?linkid=866594) a [Windows Phone](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6).

> [!NOTE]
> Uživatelé nebudou mít možnost zaregistrovat svou mobilní aplikaci při registraci pro samoobslužné resetování hesla. Místo toho mohou uživatelé [https://aka.ms/mfasetup](https://aka.ms/mfasetup) zaregistrovat svou mobilní aplikaci v náhledu registrace bezpečnostních údajů na webu nebo v jeho náhledu [https://aka.ms/setupsecurityinfo](https://aka.ms/setupsecurityinfo).
>

### <a name="notification-through-mobile-app"></a>Oznámení prostřednictvím mobilní aplikace

Aplikace Microsoft Authenticator může zabránit neoprávněnému přístupu k účtům a zastavit podvodné transakce odesláním oznámení do vašeho smartphonu nebo tabletu. Uživatelé zobrazení oznámení a pokud je legitimní, vyberte Ověřit. V opačném případě mohou vybrat možnost Odepřít.

> [!WARNING]
> Pro samoobslužné resetování hesla, pokud je pro resetování vyžadována pouze jedna metoda, je ověřovací kód jedinou možností, kterou mají uživatelé k **dispozici, aby zajistili nejvyšší úroveň zabezpečení**.
>
> Pokud jsou požadovány dvě metody, uživatelé budou moci resetovat pomocí **buď** oznámení **nebo** ověřovací kód kromě jiných povolených metod.
>

Pokud povolíte použití oznámení prostřednictvím mobilní aplikace i ověřovacího kódu z mobilní aplikace, uživatelé, kteří zaregistrují aplikaci Microsoft Authenticator pomocí oznámení, mohou k ověření své identity použít oznámení i kód.

> [!NOTE]
> Pokud má vaše organizace zaměstnance, kteří pracují v Číně nebo cestují do Číny, metoda **Oznámení prostřednictvím mobilní aplikace** na zařízeních se systémem **Android** v této zemi nefunguje. Pro tyto uživatele by měly být k dispozici alternativní metody.

### <a name="verification-code-from-mobile-app"></a>Ověřovací kód z mobilní aplikace

Aplikaci Microsoft Authenticator nebo jiné aplikace třetích stran lze použít jako softwarový token pro generování ověřovacího kódu OATH. Po zadání uživatelského jména a hesla zadáte kód poskytnutý aplikací na přihlašovací obrazovku. Ověřovací kód poskytuje druhý způsob ověřování.

> [!WARNING]
> Pro samoobslužné resetování hesla, pokud je pro resetování ověřovacího kódu vyžadována pouze jedna metoda, je jedinou možností, kterou mají uživatelé k **dispozici, aby zajistili nejvyšší úroveň zabezpečení**.
>

Uživatelé mohou mít kombinaci až pěti hardwarových tokenů OATH nebo ověřovacích aplikací, jako je například aplikace Microsoft Authenticator nakonfigurovaná pro použití kdykoli.

## <a name="oath-hardware-tokens-public-preview"></a>Hardwarové tokeny OATH (public preview)

OATH je otevřený standard, který určuje způsob generování jednorázových kódů hesel (OTP). Azure AD bude podporovat použití oath-totp SHA-1 tokeny 30 sekund nebo 60 sekund odrůdy. Zákazníci mohou získat tyto tokeny od dodavatele podle svého výběru. Tajné klíče jsou omezeny na 128 znaků, které nemusí být kompatibilní se všemi tokeny. Tajný klíč může obsahovat pouze znaky *a-z* nebo *A-Z* a číslice *1-7*a musí být zakódovány v Base32.

![Nahrání tokenů OATH do listu tokenů MFA OATH](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

Hardwarové tokeny OATH jsou podporovány jako součást veřejné verze Preview. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

Jakmile jsou tokeny získány, musí být odeslány ve formátu souboru csv oddělených čárkami, včetně hlavního čísla, sériového čísla, tajného klíče, časového intervalu, výrobce a modelu, jak je znázorněno v následujícím příkladu:

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,1234567abcdef1234567abcdef,60,Contoso,HardwareKey
```

> [!NOTE]
> Ujistěte se, že jste do souboru CSV zahrnuli řádek záhlaví.

Po správném formátování jako soubor CSV se pak správce může přihlásit na portál Azure, přejít na**tokeny**Azure **Active Directory** > **Security** > **MFA** > OATH a nahrát výsledný soubor CSV.

V závislosti na velikosti souboru CSV může zpracování trvat několik minut. Kliknutím na tlačítko **Aktualizovat** získáte aktuální stav. Pokud jsou v souboru nějaké chyby, budete mít možnost stáhnout soubor CSV se seznamem chyb, které můžete vyřešit. Názvy polí ve staženém souboru CSV se liší od nahrané verze.

Po vyřešení chyb může správce aktivovat každý klíč kliknutím na **aktivovat** token a zadáním OTP zobrazeného na tokenu.

Uživatelé mohou mít kombinaci až pěti hardwarových tokenů OATH nebo ověřovacích aplikací, jako je například aplikace Microsoft Authenticator nakonfigurovaná pro použití kdykoli.

## <a name="phone-options"></a>Možnosti telefonu

### <a name="mobile-phone"></a>Mobilní telefon

Uživatelům s mobilními telefony jsou k dispozici dvě možnosti.

Pokud uživatelé nechtějí, aby jejich číslo mobilního telefonu bylo v adresáři viditelné, ale přesto ho chtějí použít k resetování hesla, správci by ho neměli v adresáři vyplňovat. Uživatelé by měli naplnit svůj atribut **Ověřovací telefon** prostřednictvím [registračního portálu pro obnovení hesla](https://aka.ms/ssprsetup). Správci mohou tyto informace zobrazit v profilu uživatele, ale jinde se nepublikují.

Aby telefonní čísla fungovala správně, musí být ve formátu *+CountryCode PhoneNumber*, například +1 4255551234.

> [!NOTE]
> Mezi kódem země a telefonním číslem musí být mezera.
>
> Resetování hesla nepodporuje telefonní rozšíření. I ve formátu +1 4255551234X12345 jsou rozšíření před voláním odebrána.

Společnost Microsoft nezaručuje konzistentní sms nebo hlasové vícefaktorové ověřování rychlé doručení stejným číslem. V zájmu našich uživatelů může společnost Microsoft kdykoli přidat nebo odebrat krátké kódy, protože provedeme úpravy trasy za účelem zlepšení doručování SMS. Společnost Microsoft nepodporuje krátké kódy pro země nebo oblasti kromě Spojených států a Kanady.

#### <a name="text-message"></a>Textová zpráva

Na číslo mobilního telefonu obsahující ověřovací kód je odeslána SMS. Chcete-li pokračovat, zadejte ověřovací kód uvedený v přihlašovacím rozhraní.

#### <a name="phone-call"></a>Telefonát

Na telefonní číslo, které zadáte, se provádí automatický hlasový hovor. Přijměte hovor a stisknutím tlačítka # na klávesnici telefonu ověřte

> [!IMPORTANT]
> Od března 2019 nebudou možnosti telefonního hovoru dostupné uživatelům vícefaktorové mfa a spr pr ve volných a zkušebních klientech Azure AD. Sms zprávy nejsou touto změnou ovlivněny. Telefonní hovor bude i nadále k dispozici uživatelům v placených klientech Azure AD. Tato změna má vliv pouze na bezplatné nebo zkušební klienty Azure AD.

### <a name="office-phone"></a>Telefon do kanceláře

Na telefonní číslo, které zadáte, se provádí automatický hlasový hovor. Přijměte hovor a stisknutím tlačítka # na klávesnici telefonu ověřte.

Aby telefonní čísla fungovala správně, musí být ve formátu *+CountryCode PhoneNumber*, například +1 4255551234.

Atribut office phone spravuje správce.

> [!IMPORTANT]
> Od března 2019 nebudou možnosti telefonního hovoru dostupné uživatelům vícefaktorové mfa a spr pr ve volných a zkušebních klientech Azure AD. Sms zprávy nejsou touto změnou ovlivněny. Telefonní hovor bude i nadále k dispozici uživatelům v placených klientech Azure AD. Tato změna má vliv pouze na bezplatné nebo zkušební klienty Azure AD.

> [!NOTE]
> Mezi kódem země a telefonním číslem musí být mezera.
>
> Resetování hesla nepodporuje telefonní rozšíření. I ve formátu +1 4255551234X12345 jsou rozšíření před voláním odebrána.

### <a name="troubleshooting-phone-options"></a>Řešení potíží s možnostmi telefonu

Běžné problémy související s metodami ověřování pomocí telefonního čísla:

* Blokované ID volajícího na jednom zařízení
   * Poradce při potížích se zařízením
* Špatné telefonní číslo, nesprávný kód země, domácí telefonní číslo versus pracovní telefonní číslo
   * Poradce při potížích s objektem uživatele a nakonfigurovanými metodami ověřování. Ujistěte se, že jsou registrována správná telefonní čísla.
* Byl zadán nesprávný KÓD PIN.
   * Zkontrolujte, zda uživatel použil správný kód PIN registrovaný na serveru Azure MFA Server.
* Přesměrování hovoru do hlasové schránky
   * Ujistěte se, že uživatel má telefon zapnutý a že služba je k dispozici v jejich oblasti nebo použijte alternativní metodu.
* Uživatel je blokovaný
   * Pokud správce odblokuje uživatele na webu Azure Portal.
* SMS není objednaná v zařízení
   * Nechte uživatele změnit metody nebo aktivovat SMS na zařízení.
* Vadné telekomunikační služby (nebyl zjištěn žádný vstup telefonu, chybějící problémy s tóny DTMF, blokované ID volajícího na více zařízeních nebo blokované SMS na více zařízeních)
   * Společnost Microsoft používá k směrování telefonních hovorů a SMS zpráv pro ověřování více poskytovatelů telekomunikačních služeb. Pokud se zobrazí některý z výše uvedených problémů mají uživatel pokusí použít metodu alespoň 5 krát během 5 minut a mají informace o tomto uživateli k dispozici při kontaktování podpory společnosti Microsoft.

## <a name="app-passwords"></a>Hesla aplikací

Některé aplikace, které nejsou v prohlížeči, nepodporují vícefaktorové ověřování, pokud byl uživatelpovolen pro vícefaktorové ověřování a pokouší se používat aplikace, které nejsou v prohlížeči, nemohou se ověřit. Heslo aplikace umožňuje uživatelům pokračovat v ověřování

Pokud vynucujete vícefaktorové ověřování prostřednictvím zásad podmíněného přístupu a nikoli prostřednictvím vícefaktorového ověřování pro jednotlivé uživatele, nemůžete vytvářet hesla aplikací. Aplikace, které používají zásady podmíněného přístupu k řízení přístupu, nepotřebují hesla aplikací.

Pokud vaše organizace je federována pro přihlašující služby s Azure AD a budete používat Azure MFA, pak si uvědomte následující podrobnosti:

* Heslo aplikace je ověřeno službou Azure AD a proto obchází federaci. Federace se používá pouze při nastavování hesel aplikací. Pro federované uživatele (SSO) jsou hesla uložena v id organizace. Pokud uživatel opustí společnost, tyto informace musí tok na ID organizace pomocí DirSync. Synchronizace účtu zazakázat nebo odstranit může trvat až tři hodiny, což zpozdí zakázání a odstranění hesel aplikací ve službě Azure AD.
* Místní nastavení služby Access Control klienta není dodrženo heslem aplikace.
* Pro hesla aplikací není k dispozici žádná funkce protokolování a auditování místního ověřování.
* Některé pokročilé architektonické návrhy mohou vyžadovat použití kombinace uživatelského jména organizace a hesel a hesel aplikací při použití dvoustupňového ověření s klienty v závislosti na tom, kde se ověřují. Pro klienty, kteří se ověřují podle místní infrastruktury, byste použili uživatelské jméno a heslo organizace. Pro klienty, kteří se ověřují proti Azure AD, byste použili heslo aplikace.
* Ve výchozím nastavení uživatelé nemohou vytvářet hesla aplikací. Pokud potřebujete uživatelům povolit vytváření hesel aplikací, vyberte možnost **Povolit uživatelům vytvářet hesla aplikací pro přihlášení k aplikacím, které nejsou součástí prohlížeče,** v nastavení služby.

## <a name="next-steps"></a>Další kroky

[Povolení samoobslužného resetování hesla pro vaši organizaci](quickstart-sspr.md)

[Povolení azure vícefaktorového ověřování pro vaši organizaci](howto-mfa-getstarted.md)

[Povolení kombinované registrace v tenantovi](howto-registration-mfa-sspr-combined.md)

[Dokumentace konfigurace metody ověřování koncového uživatele](https://aka.ms/securityinfoguide)
