---
title: Sestavy o přístupech a použití pro Azure MFA – Azure Active Directory
description: Popisuje způsob použití funkce ověřování Azure Multi-Factor Authentication – sestavy.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9cf0b848e551d3c0e7d7275af9eb2c8e970dbd9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67113398"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Sestavy v Azure Multi-Factor Authentication

Azure Multi-Factor Authentication nabízí několik sestav, které mohou využívat vás a vaši organizaci, která je přístupná prostřednictvím webu Azure portal. V následující tabulce jsou uvedeny dostupné sestavy:

| Sestava | Location | Popis |
|:--- |:--- |:--- |
| Historie blokovaného uživatele | Azure AD > MFA Server > blokování a odblokování uživatelů | Zobrazuje historie žádostí o blokování nebo odblokování uživatelů. |
| Využití a odhalování výstrahy | Azure AD > přihlášení | Poskytuje informace o celkové využití: uživatelský souhrn a podrobnosti o uživateli; také historie upozornění na podvod odeslaných během období zadán. |
| Využití pro místní komponenty | Azure AD > MFA Server > Sestava aktivit | Pomocí rozšíření serveru NPS, AD FS, poskytuje informace o celkové využití pro MFA a MFA serveru. |
| Historie přeskočených uživatelů | Azure AD > MFA Server > jednorázové přihlášení | Poskytuje historii žádostí o obejít ověřování Multi-Factor Authentication pro uživatele. |
| Stav serveru | Azure AD > MFA Server > Stav serveru | Zobrazí stav služby Multi-Factor Authentication Server spojenou s vaším účtem. |

## <a name="view-mfa-reports"></a>Zobrazení sestav vícefaktorového ověřování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **MFA Server**.
3. Vyberte sestavu, kterou chcete zobrazit.

   ![Sestava stavu serveru MFA Server na webu Azure Portal](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Sestava přihlašování Azure AD

S **sestavě aktivit přihlašování** v [webu Azure portal](https://portal.azure.com), můžete získat informace, které potřebujete ke zjištění stavu vašeho prostředí.

Sestava přihlášení vám může poskytnout informace o použití spravovaných aplikací a přihlašovací aktivity uživatelů, který obsahuje informace o používání služby Multi-Factor authentication (MFA). Data vícefaktorového ověřování poskytují přehled o fungování vícefaktorového ověřování ve vaší organizaci. Poskytují odpovědi na následující otázky:

- Použilo se při přihlášení vícefaktorové ověřování?
- Jak uživatel dokončil vícefaktorové ověřování?
- Proč se uživateli nepodařilo dokončit vícefaktorové ověřování?
- Kolik uživatelů ovlivňuje vícefaktorové ověřování?
- Kolik uživatelů nemůže dokončit vícefaktorové ověřování?
- S jakými běžnými problémy se koncoví uživatelé vícefaktorového ověřování setkávají?

Tato data jsou k dispozici prostřednictvím [webu Azure portal](https://portal.azure.com) a [API pro vytváření sestav](../reports-monitoring/concept-reporting-api.md).

![Sestava přihlašování Azure AD na webu Azure Portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Struktura sestavy přihlášení

Sestavy aktivit přihlašování pro vícefaktorové ověřování poskytují přístup k následujícím informacím:

**Vyžaduje se MFA:** Určuje, zda se vyžaduje pro přihlášení vícefaktorové ověřování, nebo ne. Vícefaktorové ověřování můžete vyžadovat vícefaktorové ověřování jednotlivých uživatelů, podmíněný přístup nebo z jiných důvodů. Možné hodnoty jsou **Ano** nebo **ne**.

**Výsledek vícefaktorového ověřování:** Další informace o tom, jestli splnění nebo odepření vícefaktorového ověřování:

- Pokud bylo vícefaktorové ověřování splněno, v tomto sloupci se zobrazí informace o způsobu splnění vícefaktorového ověřování.
   - Azure Multi-Factor Authentication
      - dokončeno v cloudu
      - vypršela platnost kvůli zásadám nakonfigurovaným na tenantovi
      - zobrazena výzva k registraci
      - splněno deklarací identity v tokenu
      - splněno deklarací identity poskytnutou externím poskytovatelem
      - splněno silným ověřením
      - přeskočeno, protože tok byl spuštěn tokem přihlášení zprostředkovatele ve Windows
      - přeskočeno kvůli heslu aplikace
      - přeskočeno kvůli umístění
      - přeskočeno kvůli registrovanému zařízení
      - přeskočeno kvůli zapamatovanému zařízení
      - úspěšně dokončeno
   - Přesměrováno k externímu poskytovateli vícefaktorového ověřování

- Pokud bylo vícefaktorové ověřování odepřeno, v tomto sloupci se zobrazí důvod odepření.
   - Služba Azure Multi-Factor Authentication byla odepřena
      - probíhá ověřování
      - duplicitní pokus o ověření
      - příliš mnohokrát byl zadán nesprávný kód
      - neplatné ověření
      - neplatný ověřovací kód z mobilní aplikace
      - chybná konfigurace
      - telefonní hovor byl přesměrován do hlasové pošty
      - telefonní číslo má neplatný formát
      - chyba služby
      - nepodařilo se spojit s telefonem uživatele
      - nepodařilo se odeslat oznámení mobilní aplikace do zařízení
      - nepodařilo se odeslat oznámení mobilní aplikace
      - uživatel odmítl ověřování
      - uživatel nereagoval na oznámení mobilní aplikace
      - uživatel nemá zaregistrované žádné metody ověřování
      - uživatel zadal nesprávný kód
      - uživatel zadal nesprávný kód PIN
      - uživatel zavěsil před dokončením ověřování
      - uživatel je blokován
      - uživatel nezadal ověřovací kód
      - uživatel nenalezen
      - ověřovací kód již byl použitý

**Metoda Vícefaktorového ověřování:** Metoda ověřování, uživatel dokončil vícefaktorové ověřování. Možné hodnoty:

- Textová zpráva
- Oznámení mobilní aplikace
- Telefonní hovor (telefon pro ověření)
- Ověřovací kód z mobilní aplikace
- Telefonní hovor (telefon do kanceláře)
- Telefonní hovor (telefon pro alternativní ověření)

**Podrobnosti o Vícefaktorovém ověřování:** Zkrácená verze telefonního čísla, například: + X XXXXXXXX64.

**Podmíněný přístup** najít informace o vytváření zásad podmíněného přístupu, které měla vliv na pokus o přihlášení, včetně:

- Název zásady
- Udělit řízení
- Ovládací prvky relací
- Výsledek

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Prostředí PowerShell vytvářet zprávy o uživatelé zaregistrovaný pro MFA

Nejprve zkontrolujte, že máte [modulu MSOnline Powershellu V1](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) nainstalované.

Identifikujte uživatele, kterým jste se zaregistrovali pro vícefaktorové ověřování pomocí Powershellu, který následuje.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifikujte uživatele, kteří se ještě nezaregistrovali pro vícefaktorové ověřování pomocí Powershellu, který následuje.

```Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

## <a name="possible-results-in-activity-reports"></a>Možné výsledky při sestavy aktivit

V následující tabulce slouží k řešení potíží s ověřování službou Multi-Factor Authentication pomocí stažené verze sestava aktivit ověřování službou Multi-Factor Authentication. Nezobrazí se přímo na webu Azure portal.

| Výsledek volání | Popis | Obecný popis |
| --- | --- | --- |
| SUCCESS_WITH_PIN | Zadán kód PIN. | Uživatel zadal kód PIN.  Pokud bylo ověřování úspěšné, zadali správný kód PIN.  Pokud je ověřování odepřeno, pak se zadal nesprávný kód PIN nebo uživatel je nastaven na standardní režim. |
| SUCCESS_NO_PIN | Pouze # zadali | Uživatel je nastavený na režim kódu PIN a ověřování byl odepřen, to znamená uživatel nezadali kód PIN a pouze #.  Pokud uživatel je nastavena na standardní režim a ověření úspěšně, to znamená, že uživatel zadal jenom #, která je správnou věc udělat ve standardním režimu. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Nebyla stisknuta klávesa po vstupu | Uživatel neodeslal žádné číslic systému DTMF, protože nebyl zadán #.  Další číslice zadané neodešlou až do # označující konce vstupu. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Žádný telefonní vstup – vypršel časový limit | Volání odpověděl, ale neexistuje žádná odpověď.  To obvykle znamená, že volání se prodlouží hlasová pošta. |
| SUCCESS_PIN_EXPIRED | Kód PIN vypršel a nebyl změněn. | Platnost PIN kód uživatele se zobrazí výzva ho změnit, ale změna kódu PIN se nedokončil úspěšně. |
| SUCCESS_USED_CACHE | Použita mezipaměť. | Ověření bylo úspěšné bez hovoru služby Multi-Factor Authentication, protože došlo k předchozí úspěšné ověření pro stejné uživatelské jméno v časovém rámci nakonfigurovanou mezipaměť. |
| SUCCESS_BYPASSED_AUTH | Jednorázové přihlášení | Ověření bylo úspěšné. pomocí jednorázového přihlášení zahájené pro daného uživatele.  Obejít uživatele historie v sestavě najdete další podrobnosti na jednorázového přihlášení. |
| SUCCESS_USED_IP_BASED_CACHE | Použita mezipaměť na základě IP adresy | Ověření bylo úspěšné bez ověření službou Multi-Factor Authentication volání od předchozí úspěšné ověření pro stejné uživatelské jméno, typ ověřování, název aplikace a IP, došlo k chybě v časovém rámci nakonfigurovanou mezipaměť. |
| SUCCESS_USED_APP_BASED_CACHE | Použita mezipaměť na základě aplikace | Ověření bylo úspěšné bez ověření službou Multi-Factor Authentication volání od předchozí úspěšné ověření pro stejné uživatelské jméno, typ ověřování a název aplikace v časovém rámci nakonfigurovanou mezipaměť. |
| SUCCESS_INVALID_INPUT | Neplatný telefonní vstup | Odpověď odesílanou z telefonu není platný.  Může to být z jiného počítače, fax nebo modem nebo pro uživatele zadali * jako součást svých kódech PIN. |
| SUCCESS_USER_BLOCKED | Uživatel je blokován | Telefonní číslo uživatele je blokován.  Blokované číslo, nepůjdou uživatelem v průběhu ověřovacího hovoru nebo správce pomocí webu Azure portal. <br> POZNÁMKA:  Blokované číslo je také byproduct z upozornění podvod. |
| SUCCESS_SMS_AUTHENTICATED | Textová zpráva byla ověřena | Pro obousměrný zkušební zprávu, uživatel správně odpověděl s jejich jednorázovým heslem (OTP) nebo jednorázové heslo + kód PIN. |
| SUCCESS_SMS_SENT | Textová zpráva byla odeslána | Textová zpráva byla úspěšně odeslána textová zpráva obsahující jednorázové heslo (OTP).  Uživatel zadá OTP nebo OTP + kód PIN v aplikaci, aby bylo možné ověření dokončit. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobilní aplikace byla ověřena. | Uživatel úspěšně ověření přes mobilní aplikaci. |
| SUCCESS_OATH_CODE_PENDING | Čekání na vyřízení kódu OATH | Uživatel se zobrazí výzva k zadání kódu OATH, ale neodpověděl. |
| SUCCESS_OATH_CODE_VERIFIED | Kód OATH byl ověřen. | Uživatel zadá platný kód OATH po zobrazení výzvy. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Kód záložní OATH byl ověřen. | Uživatel byl odepřen ověřování s využitím primární metodu ověřování službou Multi-Factor Authentication a pak k dispozici platný kód OATH jako záložní metodu. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Nouzové bezpečnostní otázky byly zodpovězeny. | Uživatel ověřování s využitím primární metodu ověřování službou Multi-Factor Authentication byla zamítnuta a potom zodpovědět své bezpečnostní otázky pro záložní přihlašování správně. |
| FAILED_PHONE_BUSY | Ověřování již probíhá | Ověřování službou Multi-Factor Authentication již zpracovává ověřování pro tohoto uživatele.  To je často způsobeno klientů RADIUS, které odesílají několik požadavků na ověření během stejné přihlašování. |
| CONFIG_ISSUE | Telefon není dostupný. | Došlo k pokusu o volání, ale buď nebylo možné umístit nebo nebylo přijato.  Jedná se o obsazena, rychlé obsazena (odpojeno), tri tóny (číslo už ve službě service), vypršel časový limit při vyzvánění atd. |
| FAILED_INVALID_PHONENUMBER | Neplatný formát telefonního čísla | Telefonní číslo má neplatný formát.  Telefonní čísla musí být číselná hodnota a musí obsahovat 10 číslic pro kód země + 1 (USA a Kanada). |
| FAILED_USER_HUNGUP_ON_US | Uživatel zavěsil telefon. | Uživatel odpověděl na telefonu, ale pak zavěšení bez stisknutí tlačítka. |
| FAILED_INVALID_EXTENSION | Neplatná linka | Toto rozšíření obsahuje neplatné znaky.  Pouze číslice, čárky, *, a #.  Předponu @ mohou být využity také. |
| FAILED_FRAUD_CODE_ENTERED | Zadán kód podvodu | Uživatel, určují pro nahlášení podvodu při volání, což vede k odepření ověření a zablokování telefonní číslo.| 
| FAILED_SERVER_ERROR | Nelze vytočit hovor | Nebylo možné volání služby Multi-Factor Authentication. |
| FAILED_SMS_NOT_SENT | Textovou zprávu nebylo možné odeslat. | Textovou zprávu nelze odeslat.  Ověřování je odepřen. |
| FAILED_SMS_OTP_INCORRECT | Textová zpráva tokenu otp není platná | Uživatel zadal nesprávný jednorázovým heslem (OTP) z textové zprávy, který dostanou.  Ověřování je odepřen. |
| FAILED_SMS_OTP_PIN_INCORRECT | Text zpráva tokenu OTP + kód PIN je nesprávný | Uživatel zadal nesprávný jednorázovým heslem (OTP) a/nebo nesprávná PIN kód uživatele.  Ověřování je odepřen. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Překročena pokusů ověřování jednorázovým HESLEM maximální Text zprávy | Uživatel překročil maximální počet pokusů o jednorázové heslo (OTP). |
| FAILED_PHONE_APP_DENIED | Mobilní aplikace – zamítnuto | Uživatel zakáže ověřování v mobilní aplikaci tlačítko Odepřít. |
| FAILED_PHONE_APP_INVALID_PIN | Mobilní aplikace – neplatný kód PIN | Při ověřování v mobilní aplikaci zadal uživatel neplatný kód PIN. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Mobilní aplikace kód PIN nebyl změněn. | Uživateli nebyla úspěšně dokončena požadovaná změna PIN kódu v mobilní aplikaci. |
| FAILED_FRAUD_REPORTED | Byl nahlášen podvod. | Uživatel ohlásil podvod v mobilní aplikaci. |
| FAILED_PHONE_APP_NO_RESPONSE | Mobilní aplikace bez odpovědi | Uživatel nereagovala na žádost o ověření mobilní aplikace. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Mobilní aplikace – všechna zařízení blokována | Zařízení s mobilní aplikací pro tohoto uživatele se už reakce na oznámení a byla zablokována. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Oznámení mobilní aplikace se nezdařilo | Při pokusu o odeslání oznámení do mobilní aplikace na zařízení uživatele došlo k chybě. |
| FAILED_PHONE_APP_INVALID_RESULT | Neplatný výsledek mobilní aplikace | Mobilní aplikace vrátil neplatný výsledek. |
| FAILED_OATH_CODE_INCORRECT | Kód OATH je nesprávný | Uživatel zadal nesprávný kód OATH.  Ověřování je odepřen. |
| FAILED_OATH_CODE_PIN_INCORRECT | Kód OATH + PIN nesprávný | Uživatel zadal nesprávný kód OATH a/nebo nesprávná PIN kód uživatele.  Ověřování je odepřen. |
| FAILED_OATH_CODE_DUPLICATE | Duplikátní kód OATH. | Uživatel zadal kód OATH, který byl dříve používán.  Ověřování je odepřen. |
| FAILED_OATH_CODE_OLD | Kód OATH je zastaralý. | Uživatel zadal kód OATH, který předchází kódu OATH, který byl dříve používán.  Ověřování je odepřen. |
| FAILED_OATH_TOKEN_TIMEOUT | Časový limit výsledku kódu OATH | Uživatel trvá příliš dlouho k zadání kódu OATH a pokus o ověření službou Multi-Factor Authentication již vypršel. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Časový limit výsledku bezpečnostní otázky | Uživatel trvalo příliš dlouho zadejte odpověď na bezpečnostní otázky a pokus o ověření službou Multi-Factor Authentication již vypršel. |
| FAILED_AUTH_RESULT_TIMEOUT | Časový limit výsledku ověřování | Uživatel trvalo příliš dlouho pro dokončení pokus o ověření službou Multi-Factor Authentication. |
| FAILED_AUTHENTICATION_THROTTLED | Ověření omezení | Pokus o ověření službou Multi-Factor Authentication byla omezena systémem služby. |

## <a name="next-steps"></a>Další postup

* [Pro uživatele](../user-help/multi-factor-authentication-end-user.md)
* [Pokud chcete nasadit](concept-mfa-whichversion.md)
