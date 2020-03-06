---
title: Sestavy přístupu a využití pro Azure MFA – Azure Active Directory
description: V této části se dozvíte, jak používat Multi-Factor Authentication sestav funkcí Azure.
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
ms.openlocfilehash: 52d9f7a0b2a7cebefdb5ade8e16417043c5c83d3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78378073"
---
# <a name="reports-in-azure-multi-factor-authentication"></a>Sestavy v Azure Multi-Factor Authentication

Azure Multi-Factor Authentication poskytuje několik sestav, které můžete používat vy a vaše organizace přístupné prostřednictvím Azure Portal. V následující tabulce jsou uvedeny dostupné sestavy:

| Sestava | Umístění | Popis |
|:--- |:--- |:--- |
| Historie blokování uživatelů | Azure AD > Security > MFA > blokování nebo odblokování uživatelů | Zobrazuje historii požadavků na blokování nebo odblokování uživatelů. |
| Používání a výstrahy týkající se podvodů | Přihlášení > Azure AD | Poskytuje informace o celkovém využití, souhrnu uživatelů a podrobnostech uživatelů. a také historii výstrah podvodů odeslaných během zadaného rozsahu dat. |
| Využití pro místní součásti | Zpráva o aktivitě > > MFA služby Azure AD > Security | Poskytuje informace o celkovém využití MFA prostřednictvím rozšíření NPS, ADFS a MFA serveru. |
| Historie přeskočených uživatelů | Zabezpečení Azure AD > > MFA > jednorázové přihlášení | Poskytuje historii žádostí o obejít Multi-Factor Authentication pro uživatele. |
| Stav serveru | Azure AD > Security > MFA > Server – stav | Zobrazuje stav serverů Multi-Factor Authentication přidružených k vašemu účtu. |

## <a name="view-mfa-reports"></a>Zobrazit sestavy MFA

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Na levé straně vyberte **Azure Active Directory** > **Security** > **MFA**.
3. Vyberte sestavu, kterou chcete zobrazit.

   ![Zpráva o stavu serveru MFA Server v Azure Portal](./media/howto-mfa-reporting/report.png)

## <a name="azure-ad-sign-ins-report"></a>Sestava přihlášení k Azure AD

Pomocí **sestavy aktivity přihlášení** v [Azure Portal](https://portal.azure.com)můžete získat informace, které potřebujete k určení toho, jak vaše prostředí dělá.

Sestava přihlášení vám může poskytnout informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů, které obsahují informace o využití vícefaktorového ověřování (MFA). Data vícefaktorového ověřování poskytují přehled o fungování vícefaktorového ověřování ve vaší organizaci. Poskytují odpovědi na následující otázky:

- Použilo se při přihlášení vícefaktorové ověřování?
- Jak uživatel dokončil vícefaktorové ověřování?
- Proč se uživateli nepodařilo dokončit vícefaktorové ověřování?
- Kolik uživatelů ovlivňuje vícefaktorové ověřování?
- Kolik uživatelů nemůže dokončit vícefaktorové ověřování?
- S jakými běžnými problémy se koncoví uživatelé vícefaktorového ověřování setkávají?

Tato data jsou k dispozici prostřednictvím [Azure Portal](https://portal.azure.com) a [rozhraní API pro vytváření sestav](../reports-monitoring/concept-reporting-api.md).

![Sestava přihlášení k Azure AD v Azure Portal](./media/howto-mfa-reporting/sign-in-report.png)

### <a name="sign-ins-report-structure"></a>Struktura sestav přihlášení

Sestavy aktivit přihlašování pro vícefaktorové ověřování poskytují přístup k následujícím informacím:

**Vyžadování vícefaktorového ověřování:** Uvádí, jestli se pro přihlašování vyžaduje vícefaktorové ověřování, nebo ne. Vícefaktorové ověřování se může vyžadovat v důsledku MFA, podmíněného přístupu pro jednotlivé uživatele nebo z jiných důvodů. Možné hodnoty jsou **Ano** nebo **ne**.

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

**Metoda vícefaktorového ověřování:** Metoda ověřování, kterou uživatel dokončil vícefaktorové ověřování. Možné hodnoty:

- Textová zpráva
- Oznámení mobilní aplikace
- Telefonní hovor (telefon pro ověření)
- Ověřovací kód z mobilní aplikace
- Telefonní hovor (telefon do kanceláře)
- Telefonní hovor (telefon pro alternativní ověření)

**Podrobnosti o vícefaktorovém ověřování:** Zkrácená verze telefonního čísla, například: +X XXXXXXXX64.

**Podmíněný přístup** Vyhledejte informace o zásadách podmíněného přístupu, které ovlivnily pokus o přihlášení, včetně:

- Název zásady
- Udělit ovládací prvky
- Ovládací prvky relace
- Výsledek

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Vytváření sestav PowerShellu u uživatelů registrovaných pro MFA

Nejdřív se ujistěte, že máte nainstalovaný [modul PowerShellu MSOnline v1](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-1.0) .

Identifikujte uživatele, kteří se zaregistrovali pro MFA, pomocí PowerShellu, který následuje.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName```

Identifikujte uživatele, kteří se nezaregistrovali pro MFA pomocí PowerShellu, který následuje.

```Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName```

Identifikujte zaregistrované uživatele a výstupní metody. 

```PowerShell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="possible-results-in-activity-reports"></a>Možné výsledky v sestavách aktivit

Následující tabulka může sloužit k řešení vícefaktorového ověřování pomocí stažené verze sestavy aktivita aplikace Multi-Factor Authentication. Nebudou se zobrazovat přímo v Azure Portal.

| Výsledek volání | Popis | Obecný popis |
| --- | --- | --- |
| SUCCESS_WITH_PIN | Zadán kód PIN. | Uživatel zadal kód PIN.  Pokud ověření proběhlo úspěšně, zadali jste správný PIN kód.  Pokud je ověřování odepřeno, zadali jste nesprávný kód PIN nebo je uživatel nastaven na standardní režim. |
| SUCCESS_NO_PIN | Pouze zadání # | Pokud je uživatel nastavený na režim připnutí a ověřování je odepřeno, znamená to, že uživatel nezadal PIN kód a zadali jste jenom #.  Pokud je uživatel nastavený na standardní režim a ověřování je úspěšné, znamená to, že uživatel zadal jenom #, což je správné nastavení v režimu Standard. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | Po vstupu nebyla stisknuta klávesa #. | Uživatel neodeslal žádné číslice DTMF, protože nebyl zadán znak #.  Jiné zadané číslice nejsou odesílány, pokud není zadáno #, což znamená dokončení záznamu. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Žádný telefonní vstup – vypršel časový limit. | Volání bylo zodpovězeno, ale nedošlo k žádné odezvě.  To obvykle znamená, že volání bylo převzato pomocí hlasové pošty. |
| SUCCESS_PIN_EXPIRED | Kód PIN vypršel a nebyl změněn. | Platnost PIN kódu uživatele vypršela a zobrazila se jim výzva k jeho změně, ale změna kódu PIN nebyla úspěšně dokončena. |
| SUCCESS_USED_CACHE | Použita mezipaměť. | Ověřování bylo úspěšné bez Multi-Factor Authentication volání, protože v rámci nakonfigurovaného časového období mezipaměti došlo k předchozímu úspěšnému ověření pro stejné uživatelské jméno. |
| SUCCESS_BYPASSED_AUTH | Jednorázové přihlášení | Ověřování bylo úspěšné pomocí jednorázového přihlášení iniciované pro uživatele.  Další informace o obejití najdete v sestavě historie uživatelů. |
| SUCCESS_USED_IP_BASED_CACHE | Použitá mezipaměť založená na protokolu IP | Ověřování bylo úspěšné bez Multi-Factor Authentication volání od předchozího úspěšného ověření pro stejné uživatelské jméno, typ ověřování, název aplikace a IP adresa v rámci nakonfigurovaného časového období mezipaměti. |
| SUCCESS_USED_APP_BASED_CACHE | Použitá mezipaměť na základě aplikace | Ověřování bylo úspěšné bez Multi-Factor Authentication volání od předchozího úspěšného ověření pro stejné uživatelské jméno, typ ověřování a název aplikace v rámci nakonfigurovaného časového období mezipaměti. |
| SUCCESS_INVALID_INPUT | Neplatný telefonní vstup. | Odpověď odesílaná z telefonu není platná.  Může to být z faxového zařízení nebo modemu nebo uživatel zadal * jako součást svého PIN kódu. |
| SUCCESS_USER_BLOCKED | Uživatel je blokován | Telefonní číslo uživatele je blokované.  Blokované číslo může být iniciováno uživatelem během volání ověřování nebo správcem pomocí Azure Portal. <br> Poznámka: blokované číslo je také byproduct výstrahy na podvod. |
| SUCCESS_SMS_AUTHENTICATED | Textová zpráva byla ověřena. | Pro obousměrnou zkušební zprávu uživatel správně odpověděl s použitím jednorázového hesla nebo hesla + PIN. |
| SUCCESS_SMS_SENT | Textová zpráva byla odeslána. | Textová zpráva obsahující jednorázové heslo (jednorázové heslo) byla pro textovou zprávu úspěšně odeslána.  Uživatel zadá do aplikace jednorázové heslo nebo jednorázové heslo a kód PIN, aby se ověřování dokončilo. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobilní aplikace byla ověřena. | Uživatel se úspěšně ověřil přes mobilní aplikaci. |
| SUCCESS_OATH_CODE_PENDING | Čekání na vyřízení kódu OATH. | Uživateli se zobrazila výzva k zadání kódu OATH, ale neodpověděl. |
| SUCCESS_OATH_CODE_VERIFIED | Kód OATH byl ověřen. | Po zobrazení výzvy uživatel zadal platný kód OATH. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Záložní kód OATH byl ověřen. | Uživateli bylo odepřeno ověřování pomocí své primární metody Multi-Factor Authentication a pak zadali platný kód OATH pro použití náhradní. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Záložní bezpečnostní otázky byly zodpovězeny. | Uživateli bylo odepřeno ověřování pomocí své primární metody Multi-Factor Authentication a poté byly správně zodpovězeny své bezpečnostní otázky. |
| FAILED_PHONE_BUSY | Ověřování již probíhá | Multi-Factor Authentication již zpracovává ověřování pro tohoto uživatele.  To je často způsobeno klienty RADIUS, kteří odesílají více žádostí o ověření během stejného přihlašování. |
| CONFIG_ISSUE | Telefon je nedostupný | Došlo k pokusu o volání, ale buď nebylo možné umístit, nebo nebylo zodpovězeno.  To zahrnuje zaneprázdněný signál, rychlý zaneprázdněn signál (odpojeno), Tri-tónů (číslo již v provozu), časový limit při cyklickém vyzvánění atd. |
| FAILED_INVALID_PHONENUMBER | Neplatný formát telefonního čísla. | Telefonní číslo má neplatný formát.  Telefonní čísla musí být číselná a musí obsahovat 10 číslic pro kód země + 1 (USA & Kanada). |
| FAILED_USER_HUNGUP_ON_US | Uživatel zavěsil telefon. | Uživatel odpověděl na telefon, ale pak se zablokoval bez stisknutí žádného tlačítka. |
| FAILED_INVALID_EXTENSION | Neplatná linka | Přípona obsahuje neplatné znaky.  Jsou povoleny pouze číslice, čárky, * a #.  Může být také použita předpona @. |
| FAILED_FRAUD_CODE_ENTERED | Zadán kód podvodu | Uživatel se rozhodl nahlásit podvod během volání, což vedlo k odepřenému ověření a zablokovanému telefonnímu číslu.| 
| FAILED_SERVER_ERROR | Nelze uskutečnit hovor. | Služba Multi-Factor Authentication nedokázala umístit volání. |
| FAILED_SMS_NOT_SENT | Textovou zprávu nebylo možné odeslat. | Textovou zprávu nelze odeslat.  Ověřování bylo odepřeno. |
| FAILED_SMS_OTP_INCORRECT | Textová zpráva tokenu OTP není platná. | Uživatel zadal neplatné jednorázové heslo (JEDNORÁZOVé heslo) z textové zprávy, kterou obdržel.  Ověřování bylo odepřeno. |
| FAILED_SMS_OTP_PIN_INCORRECT | Textová zpráva tokenu OTP + kód PIN nejsou platné. | Uživatel zadal nesprávné jednorázové heslo (jednorázové heslo) a/nebo nesprávný PIN kód uživatele.  Ověřování bylo odepřeno. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Překročení maximálního počtu textových zpráv pro JEDNORÁZOVé přihlášení | Uživatel překročil maximální počet pokusů o jednorázové heslo (jednorázového hesla). |
| FAILED_PHONE_APP_DENIED | Mobilní aplikace byla odmítnuta. | Uživatel odepřel ověřování v mobilní aplikaci stisknutím tlačítka odepřít. |
| FAILED_PHONE_APP_INVALID_PIN | Neplatný kód PIN mobilní aplikace. | Uživatel zadal neplatný kód PIN při ověřování v mobilní aplikaci. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | Kód PIN mobilní aplikace nebyl změněn. | Uživatel neúspěšně dokončil požadovanou změnu PIN kódu v mobilní aplikaci. |
| FAILED_FRAUD_REPORTED | Zasláno upozornění na podvod | Uživatel ohlásil podvod v mobilní aplikaci. |
| FAILED_PHONE_APP_NO_RESPONSE | Mobilní aplikace bez odpovědi. | Uživatel neodpověděl na žádost o ověření mobilní aplikace. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Všechna zařízení mobilní aplikace jsou zablokována. | Zařízení mobilní aplikace pro tohoto uživatele již nereagují na oznámení a byla zablokována. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Oznámení mobilní aplikace se nezdařilo. | Při pokusu o odeslání oznámení do mobilní aplikace na zařízení uživatele došlo k chybě. |
| FAILED_PHONE_APP_INVALID_RESULT | Neplatný výsledek mobilní aplikace. | Mobilní aplikace vrátila neplatný výsledek. |
| FAILED_OATH_CODE_INCORRECT | Kód OATH není správný. | Uživatel zadal nesprávný kód OATH.  Ověřování bylo odepřeno. |
| FAILED_OATH_CODE_PIN_INCORRECT | Kód OATH + kód PIN je nesprávný | Uživatel zadal nesprávný kód OATH nebo nesprávný kód PIN uživatele.  Ověřování bylo odepřeno. |
| FAILED_OATH_CODE_DUPLICATE | Duplikátní kód OATH. | Uživatel zadal kód OATH, který byl dříve použit.  Ověřování bylo odepřeno. |
| FAILED_OATH_CODE_OLD | Kód OATH je zastaralý. | Uživatel zadal kód OATH, který předchází dříve použitý kód OATH.  Ověřování bylo odepřeno. |
| FAILED_OATH_TOKEN_TIMEOUT | Časový limit výsledku kódu OATH | Uživatel trval zadání kódu OATH příliš dlouho a Multi-Factor Authentication pokus již vypršel. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Časový limit výsledku bezpečnostních otázek | Uživatel trval příliš dlouho, aby zadával odpověď na bezpečnostní otázky a Multi-Factor Authentication pokusu již vypršel časový limit. |
| FAILED_AUTH_RESULT_TIMEOUT | Časový limit výsledku ověřování | Dokončení Multi-Factor Authentication pokusu uživatele trvalo příliš dlouho. |
| FAILED_AUTHENTICATION_THROTTLED | Ověřování bylo omezené. | Pokus o Multi-Factor Authentication byl službou omezen. |

## <a name="next-steps"></a>Další kroky

* [Vytváření sestav využití SSPR a MFA a přehledy](howto-authentication-methods-usage-insights.md)
* [Pro uživatele](../user-help/multi-factor-authentication-end-user.md)
* [Místo nasazení](concept-mfa-whichversion.md)
