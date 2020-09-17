---
title: Co je Azure Active Directory Identity Protection?
description: Zjišťování, náprava, zkoumání a analýza rizik pomocí Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 08/24/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.custom: contperfq1
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8b19b8e928a220efa8a445ff033582e0f13c6de
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707726"
---
# <a name="what-is-identity-protection"></a>Co je ochrana identit?

Identity Protection je nástroj, který organizacím umožňuje provádět tři klíčové úlohy:

- Automatizujte detekci a nápravu rizik na základě identity.
- Prozkoumejte rizika pomocí dat na portálu.
- Exportujte data o detekci rizik do nástrojů jiných výrobců pro další analýzu.

Služba Identity Protection používá studijní materiály, které Microsoft získal od svého umístění v organizacích s Azure AD, uživatelským prostorem s účty Microsoft a na hraní her s Xbox k ochraně vašich uživatelů. Microsoft analyzuje 6 500 000 000 000 signálů za den k identifikaci a ochraně zákazníků před hrozbami.

Signály vygenerované a dodané do Identity Protection je možné dále dodávat do nástrojů, jako je podmíněný přístup, pro rozhodování o přístupu nebo za účelem dalšího šetření v nástroji SIEM (Security Information and Event Management), který je založený na vynucované zásadách pro vaši organizaci.

## <a name="why-is-automation-important"></a>Proč je automatizace důležitá?

V jeho [blogovém příspěvku v říjnu od 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) Alex Weinert, který vede tým Microsoftu o zabezpečení a ochranu identity od Microsoftu, vysvětluje, proč je automatizace při práci s objemem událostí tak důležitá:

> Náš strojové učení a heuristické systémy poskytují hodnocení rizik pro 18 000 000 000 pokusů o přihlášení pro více než 800 000 000 jedinečných účtů 300 000 000, které discernibly provádí nežádoucí osoby (entity jako: trestní aktéri, hackeři).
>
> V Ignite minulý rok jsem na konci prvních 3 útoků na naše systémy identit. Toto je poslední objem těchto útoků.
>   
>   - Opětovné **přehrání porušení**: 4.6 BN útoky zjištěné v květnu 2018
>   - **Sprej hesla**: 350K v dubnu 2018
>   - **Phishing**: těžko se naprosto kvantifikovat, ale v březnu 2018 jsme 23M rizikové události, mnoho z nich je v relaci phishing.

## <a name="risk-detection-and-remediation"></a>Detekce rizik a náprava

Identity Protection identifikuje rizika v následujících klasifikacích:

| Typ detekce rizika | Description |
| --- | --- |
| Neobvyklá cesta | Přihlaste se z neobvyklých míst na základě nedávných přihlášení uživatele. |
| Anonymní IP adresa | Přihlaste se z anonymní IP adresy (například: Prohlížeè Browser, Anonymizer VPN). |
| Neznámé vlastnosti přihlášení | Přihlaste se pomocí vlastností, které pro daného uživatele nevidíme nedávno. |
| Propojená IP adresa pro malware | Přihlaste se přes propojenou IP adresu malwaru. |
| Nevrácená pověření | Toto zjišťování rizik indikuje, že byly Nevráceny platné přihlašovací údaje uživatele. |
| Sprej hesla | Označuje, že více uživatelských jmen se při útoku pomocí běžných hesel v rámci sjednoceného hrubou silou nepoužívá. |
| Analýza hrozeb v Azure AD | Zdroje analýzy interních a externích hrozeb Microsoftu identifikovaly známý vzor útoku. |

Další podrobnosti o těchto rizicích a o tom, jak se počítají, najdete v článku [co je to riziko](concept-identity-protection-risks.md).

Rizikové signály můžou aktivovat úsilí k nápravě, jako je třeba vyžadování uživatelů: provádět Azure Multi-Factor Authentication, resetovat heslo pomocí samoobslužného resetování hesla nebo zablokovat, dokud správce neprovede akci.

## <a name="risk-investigation"></a>Vyšetřování rizik

Správci mohou kontrolovat zjišťování a v případě potřeby provádět ruční akce. Existují tři základní sestavy, které správci používají k vyšetřování v ochraně identity:

- Rizikoví uživatelé
- Riziková přihlášení
- Detekce rizik

Další informace najdete v článku [Postupy: šetření rizik](howto-identity-protection-investigate-risk.md).

### <a name="risk-levels"></a>Úrovně rizika

Identity Protection kategorizuje riziko do tří úrovní: nízká, střední a vysoká. 

Zatímco společnost Microsoft neposkytuje konkrétní informace o tom, jakým způsobem je riziko vypočítáno, říkáme, že každá úroveň přináší větší jistotu, že se uživateli nebo přihlášení naruší zabezpečení. Například něco jako jedna instance neznámého přihlašovacího oprávnění pro uživatele nemusí být ohroženo jako nevrácená pověření pro jiného uživatele.

## <a name="exporting-risk-data"></a>Export rizikových dat

Data z ochrany identity je možné exportovat do jiných nástrojů pro archivaci a další zkoumání a korelaci. Rozhraní API založená na Microsoft Graph umožňují organizacím shromažďovat tato data pro další zpracování v nástroji, jako jsou jejich SIEM. Informace o tom, jak získat přístup k rozhraní Identity Protection API, najdete v článku [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](howto-identity-protection-graph-api.md)

Informace o integraci informací o identitě s Azure Sentinel najdete v článku [připojení dat z Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Oprávnění

Pro přístup k aplikaci Identity Protection je potřeba, aby uživatelé byli čtenářem zabezpečení, bezpečnostním operátorem, správcem zabezpečení, globálním čtenářem nebo globálním správcem.

| Role | Může | Nejde |
| --- | --- | --- |
| Globální správce | Úplný přístup k Identity Protection |   |
| Správce zabezpečení | Úplný přístup k Identity Protection | Resetování hesla pro uživatele |
| Operátor zabezpečení | Zobrazit všechny sestavy Identity Protection a okno s přehledem <br><br> Zavřít riziko uživatele, potvrdit bezpečné přihlášení, potvrdit ohrožení | Konfigurace nebo změna zásad <br><br> Resetování hesla pro uživatele <br><br> Konfigurace upozornění |
| Čtenář zabezpečení | Zobrazit všechny sestavy Identity Protection a okno s přehledem | Konfigurace nebo změna zásad <br><br> Resetování hesla pro uživatele <br><br> Konfigurace upozornění <br><br> Sdělte nám svůj názor na detekce |

V současné době role operátora zabezpečení nemá přístup k sestavě rizikových přihlášení.

Správci podmíněného přístupu můžou taky vytvářet zásady, které přistupují k riziku přihlašování jako podmínka. Další informace najdete v článku [podmíněný přístup: podmínky](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk).

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Schopnost | Podrobnosti  | Aplikace Azure AD Free/Microsoft 365 | Azure AD Premium P1|Azure AD Premium P2 |
| --- | --- | --- | --- | --- |
| Rizikové zásady | Zásady rizik uživatelů (prostřednictvím Identity Protection)  | No | No |Yes | 
| Rizikové zásady | Zásady rizik přihlašování (přes Identity Protection nebo podmíněný přístup)  | No |  No |Yes |
| Sestavy zabezpečení | Přehled |  No | No |Yes |
| Sestavy zabezpečení | Rizikoví uživatelé  | Omezené informace | Omezené informace | Full access|
| Sestavy zabezpečení | Riziková přihlášení  | Omezené informace | Omezené informace | Full access|
| Sestavy zabezpečení | Detekce rizik   | No | Omezené informace| Full access|
| Oznámení | Uživatelé s výstrahami zjištěnými riziky  | No | No |Yes |
| Oznámení | Týdenní výtah| No | No | Yes | 
| | Zásady registrace MFA | No | No | Ano |

## <a name="next-steps"></a>Další kroky

- [Přehled zabezpečení](concept-identity-protection-security-overview.md)

- [Co je riziko](concept-identity-protection-risks.md)

- [Dostupné zásady pro zmírnění rizik](concept-identity-protection-policies.md)
