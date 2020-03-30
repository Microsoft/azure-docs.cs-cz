---
title: Sestavy přístupu a využití pro Azure MFA – Azure Active Directory
description: To popisuje, jak používat azure multi-factor ověřování funkce – sestavy.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/30/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b47e6bac031a5f2cffc8734fee976fbf8dadc666
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129085"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Sestavy v Azure vícefaktorové ověřování

Azure Multi-Factor Authentication poskytuje několik sestav, které můžete použít vy a vaše organizace přístupné prostřednictvím portálu Azure. V následující tabulce jsou uvedeny dostupné sestavy:

| Sestava | Umístění | Popis |
|:--- |:--- |:--- |
| Historie blokovaných uživatelů | Azure AD > zabezpečení > > blokovat a odblokovat uživatele | Zobrazuje historii žádostí o blokování nebo odblokování uživatelů. |
| Upozornění na používání a podvody | Azure AD > přihlášení | Obsahuje informace o celkovém využití, souhrnu uživatelů a podrobnostech o uživateli. a také historii záznamů o podvodech předložených během uvedeného časového období. |
| Použití místních komponent | Azure AD > sestava > zabezpečení > > aktivity | Poskytuje informace o celkovém využití vícefaktorového přístupu prostřednictvím rozšíření nps, služby ADFS a serveru MFA. |
| Vynechání historie uživatelů | Azure AD > zabezpečení > > jednorázové houf | Poskytuje historii požadavků na obejití vícefaktorového ověřování pro uživatele. |
| Stav serveru | Azure AD > zabezpečení > stav > serveru > | Zobrazuje stav vícefaktorových ověřovacích serverů přidružených k vašemu účtu. |

## <a name="view-mfa-reports"></a>Zobrazit sestavy Vícefaktorové finanční ho dohledu

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **Security** > **MFA**.
3. Vyberte sestavu, kterou chcete zobrazit.

   ![Sestava stavu serveru MFA serveru na webu Azure Portal](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Sestava přihlášení Azure AD

Pomocí **sestavy aktivit přihlášení** na [webu Azure Portal](https://portal.azure.com)můžete získat informace, které potřebujete k určení, jak se daří vašemu prostředí.

Sestava přihlášení vám může poskytnout informace o využití spravovaných aplikací a aktivitách přihlášení uživatelů, které zahrnují informace o využití vícefaktorového ověřování (MFA). Data vícefaktorového ověřování poskytují přehled o fungování vícefaktorového ověřování ve vaší organizaci. Poskytují odpovědi na následující otázky:

- Použilo se při přihlášení vícefaktorové ověřování?
- Jak uživatel dokončil vícefaktorové ověřování?
- Proč se uživateli nepodařilo dokončit vícefaktorové ověřování?
- Kolik uživatelů ovlivňuje vícefaktorové ověřování?
- Kolik uživatelů nemůže dokončit vícefaktorové ověřování?
- S jakými běžnými problémy se koncoví uživatelé vícefaktorového ověřování setkávají?

Tato data jsou dostupná prostřednictvím [portálu Azure portal](https://portal.azure.com) a [rozhraní API pro vytváření sestav](../reports-monitoring/concept-reporting-api.md).

![Sestava přihlášení Azure AD na webu Azure Portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Struktura sestavy přihlášení

Sestavy aktivit přihlašování pro vícefaktorové ověřování poskytují přístup k následujícím informacím:

**Vyžadování vícefaktorového ověřování:** Uvádí, jestli se pro přihlašování vyžaduje vícefaktorové ověřování, nebo ne. MFA může být vyžadováno z důvodu vícefaktorové ověřování na uživatele, podmíněného přístupu nebo z jiných důvodů. Možné hodnoty jsou **Ano** nebo **Ne**.

**Výsledek vícefaktorového ověřování:** Další informace o splnění nebo odepření vícefaktorového ověřování:

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

**Metoda vícefaktorového ověřování:** Metoda ověřování, kterou uživatel dokončil vícefaktorové ověřování. Možné hodnoty zahrnují:

- Textová zpráva
- Oznámení mobilní aplikace
- Telefonní hovor (telefon pro ověření)
- Ověřovací kód z mobilní aplikace
- Telefonní hovor (telefon do kanceláře)
- Telefonní hovor (telefon pro alternativní ověření)

**Podrobnosti o vícefaktorovém ověřování:** Zkrácená verze telefonního čísla, například: +X XXXXXXXX64.

**Podmíněný přístup** Vyhledejte informace o zásadách podmíněného přístupu, které ovlivnily pokus o přihlášení, včetně:

- Název zásad
- Udělit ovládací prvky
- Ovládací prvky relace
- Výsledek

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Vykazování prostředí PowerShell u uživatelů registrovaných pro vícefaktorové a finanční zdroje

Nejprve se ujistěte, že máte nainstalovaný [modul Prostředí PowerShell MSOnline V1.](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0)

Identifikujte uživatele, kteří se zaregistrovali pro vícefaktorové hodnocení pomocí následujícího prostředí PowerShell. Tato sada příkazů vylučuje zakázané uživatele, protože tyto účty nelze ověřit proti Azure AD.

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods -ne $null -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

Identifikujte uživatele, kteří se nezaregistrovali pro vícefaktorové hodnocení pomocí následujícího prostředí PowerShell. Tato sada příkazů vylučuje zakázané uživatele, protože tyto účty nelze ověřit proti Azure AD.

```Get-MsolUser -All | Where-Object {$.StrongAuthenticationMethods.Count -eq 0 -and $.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName```

Identifikujte registrované uživatele a výstupní metody. 

```PowerShell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>Možné výsledky v sestavách aktivit

Následující tabulka může být použita k řešení potíží s vícefaktorovým ověřováním pomocí stažené verze sestavy aktivitvícefaktorového ověřování. Nezobrazí se přímo na webu Azure Portal.

| Výsledek hovoru | Popis | Široký popis |
| --- | --- | --- |
| SUCCESS_WITH_PIN | Zadaný PIN kód | Uživatel zadal PIN. Pokud se ověření podařilo, zadali správný PIN kód. Pokud je ověřování odmítnuto, zadali nesprávný kód PIN nebo je uživatel nastaven na standardní režim. |
| SUCCESS_NO_PIN | Pouze # Zadáno | Pokud je uživatel nastaven na režim PIN a ověřování je odepřeno, znamená to, že uživatel nezadal svůj PIN a pouze zadal #.  Pokud je uživatel nastaven na standardní režim a ověřování proběhne úspěšně, znamená to, že uživatel pouze zadal # což je správná věc ve standardním režimu. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Není stisknuto po vstupu | Uživatel neodeslal žádné číslice DTMF, protože # nebyl zadán. Ostatní zadané číslice nejsou odeslány, pokud není zadáno # označující dokončení položky. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Žádný vstup do telefonu – časový výtok | Hovor byl přijat, ale žádná odpověď nebyla. To obvykle znamená, že hovor byl vyzvednut hlasovou schránkou. |
| SUCCESS_PIN_EXPIRED | Platnost kódu PIN vypršela a nebyla změněna. | Platnost pinu uživatele vypršela a byli vyzváni ke změně, ale změna kódu PIN nebyla úspěšně dokončena. |
| SUCCESS_USED_CACHE | Použitá mezipaměť | Ověření proběhlo bez volání vícefaktorového ověřování, protože předchozí úspěšné ověření stejného uživatelského jména proběhlo v rámci nakonfigurovaného časového rámce mezipaměti. |
| SUCCESS_BYPASSED_AUTH | Obejít Auth | Ověření proběhlo úspěšně pomocí jednorázového obejití pro uživatele. Další podrobnosti o obtoku naleznete v přehledu vynechání historie uživatelů. |
| SUCCESS_USED_IP_BASED_CACHE | Použitá mezipaměť založená na protokolu IP | Ověření proběhlo bez volání vícefaktorového ověřování, protože předchozí úspěšné ověření stejného uživatelského jména, typu ověřování, názvu aplikace a protokolu IP došlo v rámci nakonfigurovaného časového rámce mezipaměti. |
| SUCCESS_USED_APP_BASED_CACHE | Použitá mezipaměť založená na aplikacích | Ověření proběhlo bez volání vícefaktorového ověřování od předchozího úspěšného ověření stejného uživatelského jména, typu ověřování a názvu aplikace v rámci nakonfigurovaného časového rámce mezipaměti. |
| SUCCESS_INVALID_INPUT | Neplatný vstup telefonu | Odpověď odeslaná z telefonu není platná. Může pojednávat z faxového zařízení nebo modemu nebo uživatel mohl zadat * jako součást svého kódu PIN. |
| SUCCESS_USER_BLOCKED | Uživatel je blokován. | Telefonní číslo uživatele je blokováno. Blokované číslo může iniciovat uživatel během volání ověřování nebo správce pomocí portálu Azure. <br> Poznámka: Blokované číslo je také vedlejším produktem upozornění podvodu. |
| SUCCESS_SMS_AUTHENTICATED | Textová zpráva ověřena | U obousměrné testovací zprávy uživatel správně odpověděl svým jednorázovým heskódem (OTP) nebo OTP + PIN. |
| SUCCESS_SMS_SENT | Odeslaná textová zpráva | V případě textové zprávy byla úspěšně odeslána textová zpráva obsahující jednorázový přístupový kód (OTP). Uživatel zadá do aplikace OTP nebo OTP + PIN pro dokončení ověřování. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Ověřená mobilní aplikace | Uživatel byl úspěšně ověřen prostřednictvím mobilní aplikace. |
| SUCCESS_OATH_CODE_PENDING | Kód OATH čeká na vyřízení | Uživatel byl vyzván k zadání kódu OATH, ale neodpověděl. |
| SUCCESS_OATH_CODE_VERIFIED | Ověřený kód OATH | Uživatel zadal platný kód OATH po zobrazení výzvy. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Záložní kód OATH ověřen | Uživateli bylo odepřeno ověřování pomocí primární metody vícefaktorového ověřování a poté byl poskytnut platný kód OATH pro záložní. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Odpovědi na záložní bezpečnostní otázky | Uživateli bylo odepřeno ověřování pomocí primární metody vícefaktorového ověřování a poté správně odpověděl na své bezpečnostní otázky pro záložní. |
| FAILED_PHONE_BUSY | Auth již probíhá | Vícefaktorové ověřování již zpracovává ověřování pro tohoto uživatele. To je často způsobeno klienty RADIUS, kteří odesílají více požadavků na ověření během stejného přihlášení. |
| CONFIG_ISSUE | Telefon nedostupný | Hovor byl pokus, ale buď nelze umístit nebo nebyl a nebyl zodpovězen. To zahrnuje obsazovací signál, signál rychlého zaneprázdnění (odpojený), třítónové tóny (číslo, které již není v provozu), časový režim při zvonění atd. |
| FAILED_INVALID_PHONENUMBER | Neplatný formát telefonního čísla | Telefonní číslo má neplatný formát. Telefonní čísla musí být číselná a musí být 10 číslic pro kód země +1 (Spojené státy & Kanady). |
| FAILED_USER_HUNGUP_ON_US | Uživatel zavěsil telefon | Uživatel zvedl telefon, ale pak zavěsil bez stisknutí tlačítek. |
| FAILED_INVALID_EXTENSION | Neplatné rozšíření | Rozšíření obsahuje neplatné znaky. Povoleny jsou pouze číslice, čárky, *a #. @prefix může být také použit. |
| FAILED_FRAUD_CODE_ENTERED | Zadaný kód podvodu | Uživatel se rozhodl nahlásit podvod během hovoru, což vedlo k odepření ověření a blokovanému telefonnímu číslu.| 
| FAILED_SERVER_ERROR | Nelze uskutečnit hovor. | Služba Vícefaktorového ověřování nemohla volat. |
| FAILED_SMS_NOT_SENT | Textovou zprávu nelze odeslat. | Textovou zprávu nelze odeslat. Ověřování je odmítnuto. |
| FAILED_SMS_OTP_INCORRECT | Textová zpráva OTP nesprávná | Uživatel zadal z přijaté textové zprávy nesprávný jednorázový přístupový kód (OTP). Ověřování je odmítnuto. |
| FAILED_SMS_OTP_PIN_INCORRECT | Textová zpráva OTP + PIN nesprávná | Uživatel zadal nesprávný jednorázový přístupový kód (OTP) a/nebo nesprávný uživatelský PIN kód. Ověřování je odmítnuto. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Překročenmaximální počet pokusů o převod ois. | Uživatel překročil maximální počet pokusů o jednorázový přístupový kód (OTP). |
| FAILED_PHONE_APP_DENIED | Mobilní aplikace byla zamítnuta. | Uživatel odmítl ověřování v mobilní aplikaci stisknutím tlačítka Odepřít. |
| FAILED_PHONE_APP_INVALID_PIN | Neplatný PIN kód mobilní aplikace | Uživatel zadal při ověřování v mobilní aplikaci neplatný kód PIN. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Pin kódu mobilní aplikace se nezměnil | Uživatel v mobilní aplikaci úspěšně neprovedl požadovanou změnu kódu PIN. |
| FAILED_FRAUD_REPORTED | Nahlášené podvody | Uživatel nahlásil podvod v mobilní aplikaci. |
| FAILED_PHONE_APP_NO_RESPONSE | Mobilní aplikace bez odpovědi | Uživatel neodpověděl na požadavek na ověření mobilní aplikace. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Mobilní aplikace Všechna blokovaná zařízení | Zařízení mobilních aplikací pro tohoto uživatele již nereagují na oznámení a byla zablokována. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Oznámení mobilní aplikace se nezdařilo. | Při pokusu o odeslání oznámení do mobilní aplikace na zařízení uživatele došlo k chybě. |
| FAILED_PHONE_APP_INVALID_RESULT | Neplatný výsledek mobilní aplikace | Mobilní aplikace vrátila neplatný výsledek. |
| FAILED_OATH_CODE_INCORRECT | Nesprávný kód OATH | Uživatel zadal nesprávný kód OATH.  Ověřování je odmítnuto. |
| FAILED_OATH_CODE_PIN_INCORRECT | Kód OATH + PIN nesprávný | Uživatel zadal nesprávný kód OATH a/nebo nesprávný uživatelský PIN.  Ověřování je odmítnuto. |
| FAILED_OATH_CODE_DUPLICATE | Duplicitní kód OATH | Uživatel zadal dříve použitý kód OATH.  Ověřování je odmítnuto. |
| FAILED_OATH_CODE_OLD | Kód OATH je zastaralý | Uživatel zadal kód OATH, který předchází kódu OATH, který byl dříve používán.  Ověřování je odmítnuto. |
| FAILED_OATH_TOKEN_TIMEOUT | Časový opovad výsledku kódu OATH | Uživateli trvalo příliš dlouho, než zadá kód OATH a pokus o vícefaktorové ověřování již překročil časový čas. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Časový opojení výsledku bezpečnostních otázek | Uživateli trvalo příliš dlouho, než zadal odpověď na bezpečnostní otázky a pokus o vícefaktorové ověřování již překročil časový čas. |
| FAILED_AUTH_RESULT_TIMEOUT | Časový výtok výsledku auth | Dokončení pokusu o vícefaktorové ověřování uživateli trvalo příliš dlouho. |
| FAILED_AUTHENTICATION_THROTTLED | Ověřování omezené | Služba omezila pokus o vícefaktorové ověřování. |

## <a name="next-steps"></a>Další kroky

* [Vykazování využití služby SSPR a mfa a přehledů](howto-authentication-methods-usage-insights.md)
* [Pro uživatele](../user-help/multi-factor-authentication-end-user.md)
* [Kam nasadit](concept-mfa-whichversion.md)
