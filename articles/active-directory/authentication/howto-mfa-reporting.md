---
title: Podrobnosti události přihlášení pro Azure Multi-Factor Authentication – Azure Active Directory
description: Přečtěte si, jak zobrazit přihlašovací aktivitu pro události a stavové zprávy Azure Multi-Factor Authentication.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/15/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 676b8b6fbb56536ec3a49100f5de1419ac417bb6
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91964141"
---
# <a name="use-the-sign-ins-report-to-review-azure-multi-factor-authentication-events"></a>Použití sestavy přihlášení ke kontrole událostí Azure Multi-Factor Authentication

Pokud chcete zkontrolovat a pochopit události Azure Multi-Factor Authentication, můžete použít sestavu přihlášení Azure Active Directory (Azure AD). Tato sestava zobrazuje podrobnosti o ověřování pro události, když se uživateli zobrazí výzva k zadání služby Multi-Factor Authentication, a pokud se nějaké zásady podmíněného přístupu používaly. Podrobné informace o sestavě přihlášení najdete v tématu [Přehled sestav aktivit přihlašování v Azure AD](../reports-monitoring/concept-sign-ins.md).

V tomto článku se dozvíte, jak zobrazit sestavu přihlášení k Azure AD v Azure Portal a pak modul PowerShellu pro MSOnline v1.

## <a name="view-the-azure-ad-sign-ins-report"></a>Zobrazit sestavu přihlášení k Azure AD

Sestava přihlášení poskytuje informace o použití spravovaných aplikací a aktivitách přihlašování uživatelů, které obsahují informace o využití vícefaktorového ověřování (MFA). Data vícefaktorového ověřování poskytují přehled o fungování vícefaktorového ověřování ve vaší organizaci. Umožňuje vám odpovídat na následující otázky:

- Použilo se při přihlášení vícefaktorové ověřování?
- Jak uživatel dokončil vícefaktorové ověřování?
- Proč se uživateli nepodařilo dokončit vícefaktorové ověřování?
- Kolik uživatelů ovlivňuje vícefaktorové ověřování?
- Kolik uživatelů nemůže dokončit vícefaktorové ověřování?
- S jakými běžnými problémy se koncoví uživatelé vícefaktorového ověřování setkávají?

Chcete-li zobrazit sestavu aktivity přihlašování v [Azure Portal](https://portal.azure.com), proveďte následující kroky. Data můžete také dotazovat pomocí [rozhraní API pro vytváření sestav](../reports-monitoring/concept-reporting-api.md).

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu s oprávněními *globálního správce* .
1. Vyhledejte a vyberte **Azure Active Directory**a pak v nabídce na levé straně zvolte **Uživatelé** .
1. V části *aktivita* v nabídce na levé straně vyberte **přihlášení**.
1. Zobrazí se seznam přihlašovacích událostí, včetně stavu. Můžete vybrat událost a zobrazit další podrobnosti.

    Na kartě *Podrobnosti o ověřování* nebo *podmíněný přístup* v podrobnostech události se zobrazí stavový kód nebo tato zásada aktivovala výzvu MFA.

    [![Snímek obrazovky s příkladem sestavy přihlášení Azure Active Directory v Azure Portal](media/howto-mfa-reporting/sign-in-report-cropped.png)](media/howto-mfa-reporting/sign-in-report.png#lightbox)

Pokud je k dispozici, zobrazí se ověřování, například textová zpráva, Microsoft Authenticator oznámení aplikace nebo telefonní hovor.

V okně *Podrobnosti o ověřování* se zobrazí následující podrobnosti o události přihlášení, která ukazuje, jestli byla žádost MFA splněná nebo zamítnutá:

* Pokud bylo vícefaktorové ověřování splněno, v tomto sloupci se zobrazí informace o způsobu splnění vícefaktorového ověřování.
   * dokončeno v cloudu
   * vypršela platnost kvůli zásadám nakonfigurovaným na tenantovi
   * zobrazena výzva k registraci
   * splněno deklarací identity v tokenu
   * splněno deklarací identity poskytnutou externím poskytovatelem
   * splněno silným ověřením
   * přeskočeno, protože tok byl spuštěn tokem přihlášení zprostředkovatele ve Windows
   * přeskočeno kvůli heslu aplikace
   * přeskočeno kvůli umístění
   * přeskočeno kvůli registrovanému zařízení
   * přeskočeno kvůli zapamatovanému zařízení
   * úspěšně dokončeno

* Pokud bylo vícefaktorové ověřování odepřeno, v tomto sloupci se zobrazí důvod odepření.
   * probíhá ověřování
   * duplicitní pokus o ověření
   * příliš mnohokrát byl zadán nesprávný kód
   * neplatné ověření
   * neplatný ověřovací kód z mobilní aplikace
   * chybná konfigurace
   * telefonní hovor byl přesměrován do hlasové pošty
   * telefonní číslo má neplatný formát
   * chyba služby
   * Nepovedlo se kontaktovat telefon uživatele.
   * nepodařilo se odeslat oznámení mobilní aplikace do zařízení
   * nepodařilo se odeslat oznámení mobilní aplikace
   * uživatel odmítl ověřování
   * uživatel nereagoval na oznámení mobilní aplikace
   * uživatel nemá zaregistrované žádné metody ověřování
   * uživatel zadal nesprávný kód
   * uživatel zadal nesprávný kód PIN
   * uživatel zavěsil před dokončením ověřování
   * uživatel je blokován
   * uživatel nezadal ověřovací kód
   * uživatel nenalezen
   * ověřovací kód již byl použitý

## <a name="powershell-reporting-on-users-registered-for-mfa"></a>Vytváření sestav PowerShellu u uživatelů registrovaných pro MFA

Nejdřív se ujistěte, že máte nainstalovaný [modul PowerShellu MSOnline v1](/powershell/azure/active-directory/overview?view=azureadps-1.0) .

Identifikujte uživatele, kteří se zaregistrovali pro MFA, pomocí PowerShellu, který následuje. Tato sada příkazů nezahrnuje zakázané uživatele, protože tyto účty nelze ověřit vůči službě Azure AD:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods -ne $null -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifikujte uživatele, kteří se nezaregistrovali pro MFA pomocí PowerShellu, který následuje. Tato sada příkazů nezahrnuje zakázané uživatele, protože tyto účty nelze ověřit vůči službě Azure AD:

```powershell
Get-MsolUser -All | Where-Object {$_.StrongAuthenticationMethods.Count -eq 0 -and $_.BlockCredential -eq $False} | Select-Object -Property UserPrincipalName
```

Identifikujte zaregistrované uživatele a výstupní metody:

```powershell
Get-MsolUser -All | Select-Object @{N='UserPrincipalName';E={$_.UserPrincipalName}},

@{N='MFA Status';E={if ($_.StrongAuthenticationRequirements.State){$_.StrongAuthenticationRequirements.State} else {"Disabled"}}},

@{N='MFA Methods';E={$_.StrongAuthenticationMethods.methodtype}} | Export-Csv -Path c:\MFA_Report.csv -NoTypeInformation
```

## <a name="downloaded-activity-reports-result-codes"></a>Kódy výsledku sestav aktivit ke stažení

Následující tabulka může pomoct řešit události pomocí stažené verze sestavy aktivity z předchozích kroků portálu nebo příkazů PowerShellu. Tyto kódy výsledku se nezobrazí přímo v Azure Portal.

| Výsledek volání | Popis | Obecný popis |
| --- | --- | --- |
| SUCCESS_WITH_PIN | Zadán kód PIN | Uživatel zadal kód PIN. Pokud ověření proběhlo úspěšně, zadali jste správný PIN kód. Pokud je ověřování odepřeno, zadali jste nesprávný kód PIN nebo je uživatel nastaven na standardní režim. |
| SUCCESS_NO_PIN | Pouze zadání # | Pokud je uživatel nastavený na režim připnutí a ověřování je odepřeno, znamená to, že uživatel nezadal PIN kód a zadali jste jenom #.  Pokud je uživatel nastavený na standardní režim a ověřování je úspěšné, znamená to, že uživatel zadal jenom #, což je správné nastavení v režimu Standard. |
| SUCCESS_WITH_PIN_BUT_TIMEOUT | # Nestisknuté po zadání | Uživatel neodeslal žádné číslice DTMF, protože nebyl zadán znak #. Jiné zadané číslice nejsou odesílány, pokud není zadáno #, což znamená dokončení záznamu. |
|SUCCESS_NO_PIN_BUT_TIMEOUT | Žádný telefonní vstup – vypršel časový limit. | Volání bylo zodpovězeno, ale nedošlo k žádné odezvě. To obvykle znamená, že volání bylo převzato pomocí hlasové pošty. |
| SUCCESS_PIN_EXPIRED | KÓD PIN vypršel a nebyl změněn. | Platnost PIN kódu uživatele vypršela a zobrazila se jim výzva k jeho změně, ale změna kódu PIN nebyla úspěšně dokončena. |
| SUCCESS_USED_CACHE | Použitá mezipaměť | Ověřování bylo úspěšné bez Multi-Factor Authentication volání, protože v rámci nakonfigurovaného časového období mezipaměti došlo k předchozímu úspěšnému ověření pro stejné uživatelské jméno. |
| SUCCESS_BYPASSED_AUTH | Obcházení ověřování | Ověřování bylo úspěšné pomocí One-Time Nezahájeno pro uživatele. Další informace o obejití najdete v sestavě historie uživatelů. |
| SUCCESS_USED_IP_BASED_CACHE | Použitá mezipaměť založená na protokolu IP | Ověřování bylo úspěšné bez Multi-Factor Authentication volání od předchozího úspěšného ověření pro stejné uživatelské jméno, typ ověřování, název aplikace a IP adresa v rámci nakonfigurovaného časového období mezipaměti. |
| SUCCESS_USED_APP_BASED_CACHE | Použitá mezipaměť na základě aplikace | Ověřování bylo úspěšné bez Multi-Factor Authentication volání od předchozího úspěšného ověření pro stejné uživatelské jméno, typ ověřování a název aplikace v rámci nakonfigurovaného časového období mezipaměti. |
| SUCCESS_INVALID_INPUT | Neplatný telefonní vstup | Odpověď odesílaná z telefonu není platná. Může to být z faxového zařízení nebo modemu nebo uživatel zadal * jako součást svého PIN kódu. |
| SUCCESS_USER_BLOCKED | Uživatel je zablokován. | Telefonní číslo uživatele je blokované. Blokované číslo může být iniciováno uživatelem během volání ověřování nebo správcem pomocí Azure Portal. <br> Poznámka: blokované číslo je také byproduct výstrahy na podvod. |
| SUCCESS_SMS_AUTHENTICATED | Textová zpráva byla ověřena. | Pro obousměrnou zkušební zprávu uživatel správně odpověděl s použitím jednorázového hesla nebo hesla + PIN. |
| SUCCESS_SMS_SENT | Textová zpráva byla odeslána. | Textová zpráva obsahující jednorázové heslo (jednorázové heslo) byla pro textovou zprávu úspěšně odeslána. Uživatel zadá do aplikace jednorázové heslo nebo jednorázové heslo a kód PIN, aby se ověřování dokončilo. |
| SUCCESS_PHONE_APP_AUTHENTICATED | Mobilní aplikace ověřena | Uživatel se úspěšně ověřil přes mobilní aplikaci. |
| SUCCESS_OATH_CODE_PENDING | Nedokončený kód OATH | Uživateli se zobrazila výzva k zadání kódu OATH, ale neodpověděl. |
| SUCCESS_OATH_CODE_VERIFIED | Kód OATH byl ověřen. | Po zobrazení výzvy uživatel zadal platný kód OATH. |
| SUCCESS_FALLBACK_OATH_CODE_VERIFIED | Nouzový kód OATH byl ověřen. | Uživateli bylo odepřeno ověřování pomocí své primární metody Multi-Factor Authentication a pak zadali platný kód OATH pro použití náhradní. |
| SUCCESS_FALLBACK_SECURITY_QUESTIONS_ANSWERED | Záložní bezpečnostní otázky byly zodpovězeny | Uživateli bylo odepřeno ověřování pomocí své primární metody Multi-Factor Authentication a poté byly správně zodpovězeny své bezpečnostní otázky. |
| FAILED_PHONE_BUSY | Ověřování již probíhá. | Multi-Factor Authentication již zpracovává ověřování pro tohoto uživatele. To je často způsobeno klienty RADIUS, kteří odesílají více žádostí o ověření během stejného přihlašování. |
| CONFIG_ISSUE | Telefon nedosažitelný | Došlo k pokusu o volání, ale buď nebylo možné umístit, nebo nebylo zodpovězeno. To zahrnuje zaneprázdněný signál, rychlý zaneprázdněn signál (odpojeno), Tri-tónů (číslo již v provozu), časový limit při cyklickém vyzvánění atd. |
| FAILED_INVALID_PHONENUMBER | Neplatný formát telefonního čísla | Telefonní číslo má neplatný formát. Telefonní čísla musí být číselná a musí obsahovat 10 číslic pro kód země + 1 (USA & Kanada). |
| FAILED_USER_HUNGUP_ON_US | Uživatel přestal telefonem. | Uživatel odpověděl na telefon, ale pak se zablokoval bez stisknutí žádného tlačítka. |
| FAILED_INVALID_EXTENSION | Neplatná přípona | Přípona obsahuje neplatné znaky. Jsou povoleny pouze číslice, čárky, * a #. Může být také použita předpona @. |
| FAILED_FRAUD_CODE_ENTERED | Byl zadán podvodný kód. | Uživatel se rozhodl nahlásit podvod během volání, což vedlo k odepřenému ověření a zablokovanému telefonnímu číslu.| 
| FAILED_SERVER_ERROR | Nejde umístit volání. | Služba Multi-Factor Authentication nedokázala umístit volání. |
| FAILED_SMS_NOT_SENT | Textovou zprávu se nepodařilo odeslat. | Textovou zprávu nelze odeslat. Ověřování bylo odepřeno. |
| FAILED_SMS_OTP_INCORRECT | Textová zpráva – neplatné jednorázové heslo | Uživatel zadal neplatné jednorázové heslo (JEDNORÁZOVé heslo) z textové zprávy, kterou obdržel. Ověřování bylo odepřeno. |
| FAILED_SMS_OTP_PIN_INCORRECT | Textová zpráva – jednorázové heslo + kód PIN | Uživatel zadal nesprávné jednorázové heslo (jednorázové heslo) a/nebo nesprávný PIN kód uživatele. Ověřování bylo odepřeno. |
| FAILED_SMS_MAX_OTP_RETRY_REACHED | Překročení maximálního počtu textových zpráv pro JEDNORÁZOVé přihlášení | Uživatel překročil maximální počet pokusů o jednorázové heslo (jednorázového hesla). |
| FAILED_PHONE_APP_DENIED | Mobilní aplikace byla odepřena | Uživatel odepřel ověřování v mobilní aplikaci stisknutím tlačítka odepřít. |
| FAILED_PHONE_APP_INVALID_PIN | Mobilní aplikace – neplatný kód PIN | Uživatel zadal neplatný kód PIN při ověřování v mobilní aplikaci. |
| FAILED_PHONE_APP_PIN_NOT_CHANGED | PIN kód mobilní aplikace se nezměnil. | Uživatel neúspěšně dokončil požadovanou změnu PIN kódu v mobilní aplikaci. |
| FAILED_FRAUD_REPORTED | Oznámeno podvodům | Uživatel ohlásil podvod v mobilní aplikaci. |
| FAILED_PHONE_APP_NO_RESPONSE | Mobilní aplikace – žádná odpověď | Uživatel neodpověděl na žádost o ověření mobilní aplikace. |
| FAILED_PHONE_APP_ALL_DEVICES_BLOCKED | Mobilní aplikace – všechna zařízení blokována | Zařízení mobilní aplikace pro tohoto uživatele již nereagují na oznámení a byla zablokována. |
| FAILED_PHONE_APP_NOTIFICATION_FAILED | Oznámení mobilní aplikace selhalo. | Při pokusu o odeslání oznámení do mobilní aplikace na zařízení uživatele došlo k chybě. |
| FAILED_PHONE_APP_INVALID_RESULT | Mobilní aplikace – neplatný výsledek | Mobilní aplikace vrátila neplatný výsledek. |
| FAILED_OATH_CODE_INCORRECT | Kód OATH je nesprávný. | Uživatel zadal nesprávný kód OATH.  Ověřování bylo odepřeno. |
| FAILED_OATH_CODE_PIN_INCORRECT | Kód OATH + kód PIN je nesprávný | Uživatel zadal nesprávný kód OATH nebo nesprávný kód PIN uživatele.  Ověřování bylo odepřeno. |
| FAILED_OATH_CODE_DUPLICATE | Duplicitní kód OATH | Uživatel zadal kód OATH, který byl dříve použit.  Ověřování bylo odepřeno. |
| FAILED_OATH_CODE_OLD | Kód OATH je zastaralý. | Uživatel zadal kód OATH, který předchází dříve použitý kód OATH.  Ověřování bylo odepřeno. |
| FAILED_OATH_TOKEN_TIMEOUT | Časový limit výsledku kódu OATH | Uživatel trval zadání kódu OATH příliš dlouho a Multi-Factor Authentication pokus již vypršel. |
| FAILED_SECURITY_QUESTIONS_TIMEOUT | Časový limit výsledku bezpečnostních otázek | Uživatel trval příliš dlouho, aby zadával odpověď na bezpečnostní otázky a Multi-Factor Authentication pokusu již vypršel časový limit. |
| FAILED_AUTH_RESULT_TIMEOUT | Časový limit výsledku ověřování | Dokončení Multi-Factor Authentication pokusu uživatele trvalo příliš dlouho. |
| FAILED_AUTHENTICATION_THROTTLED | Ověřování bylo omezené. | Pokus o Multi-Factor Authentication byl službou omezen. |

## <a name="additional-mfa-reports"></a>Další sestavy MFA

K dispozici jsou následující další informace a sestavy pro události MFA, včetně těch pro MFA Server:

| Sestava | Umístění | Popis |
|:--- |:--- |:--- |
| Historie blokovaného uživatele | Azure AD > Security > MFA > blokování nebo odblokování uživatelů | Zobrazuje historii požadavků na blokování nebo odblokování uživatelů. |
| Využití pro místní součásti | Zpráva o aktivitě > > MFA služby Azure AD > Security | Poskytuje informace o celkovém využití MFA serveru prostřednictvím rozšíření NPS, ADFS a MFA serveru. |
| Historie obcházení uživatelů | Zabezpečení Azure AD > > MFA > jednorázové přihlášení | Poskytuje historii požadavků MFA serveru na obejití MFA pro uživatele. |
| Stav serveru | Azure AD > Security > MFA > Server – stav | Zobrazuje stav MFA serverů přidružených k vašemu účtu. |

## <a name="next-steps"></a>Další kroky

Tento článek poskytuje přehled sestavy aktivit přihlášení. Podrobnější informace o tom, co tato sestava obsahuje a pochopení dat, najdete v tématu [sestavy aktivit přihlašování v Azure AD](../reports-monitoring/concept-sign-ins.md).
