---
title: Co je Azure Active Directory Identity Protection?
description: Zjišťování, náprava, zkoumání a analýza rizik pomocí Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e928d67ba7102df3d342e77705ea895f9230ff3
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887657"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Co je Azure Active Directory Identity Protection?

Identity Protection je nástroj, který organizacím umožňuje provádět tři klíčové úlohy:

- Automatizujte detekci a nápravu rizik na základě identity.
- Prozkoumejte rizika pomocí dat na portálu.
- Exportujte data o detekci rizik do nástrojů jiných výrobců pro další analýzu.

Služba Identity Protection používá studijní materiály, které Microsoft získal od svého umístění v organizacích s Azure AD, uživatelským prostorem s účty Microsoft a na hraní her s Xbox k ochraně vašich uživatelů. Microsoft analyzuje 6 500 000 000 000 signálů za den k identifikaci a ochraně zákazníků před hrozbami.

Signály vygenerované a dodané do Identity Protection je možné dále dodávat do nástrojů, jako je podmíněný přístup, pro rozhodování o přístupu nebo za účelem dalšího šetření pomocí nástroje SIEM (Security Information and Event Management) v závislosti na vaší organizaci. vynutily zásady.

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

| Typ detekce rizika | Popis |
| --- | --- |
| Neobvyklé cestování | Přihlaste se z neobvyklých míst na základě nedávných přihlášení uživatele. |
| Anonymní IP adresa | Přihlaste se z anonymní IP adresy (například: Prohlížeè Browser, Anonymizer VPN). |
| Neznámou vlastnost přihlášení | Přihlaste se pomocí vlastností, které pro daného uživatele nevidíme nedávno. |
| Propojená IP adresa pro malware | Přihlášení z IP adresy propojené s malwarem |
| Nevrácená pověření | Toto zjišťování rizik indikuje, že došlo k úniku platných přihlašovacích údajů uživatele. |
| Analýza hrozeb v Azure AD | Zdroje analýzy interních a externích hrozeb Microsoftu identifikovaly známý vzor útoku. |

Další podrobnosti o těchto rizicích a o tom, jak se počítají, najdete v článku [co je to riziko](concept-identity-protection-risks.md).

Rizikové signály můžou aktivovat úsilí k nápravě, jako je třeba vyžadování uživatelů: provádět Azure Multi-Factor Authentication, resetovat heslo pomocí samoobslužného resetování hesla nebo zablokovat, dokud správce neprovede akci.

## <a name="risk-investigation"></a>Vyšetřování rizik

Správci mohou kontrolovat zjišťování a v případě potřeby provádět ruční akce. Existují tři základní sestavy, které správci používají k vyšetřování v ochraně identity:

- Rizikové uživatele
- Riziková přihlášení
- Detekce rizik

Další informace najdete v článku [Postupy: šetření rizik](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Export rizikových dat

Data z ochrany identity je možné exportovat do jiných nástrojů pro archivaci a další šetření a korelace. Rozhraní API založená na Microsoft Graph umožňují organizacím shromažďovat tato data pro další zpracování v nástroji, jako jsou jejich SIEM. Informace o tom, jak získat přístup k rozhraní Identity Protection API, najdete v článku [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](howto-identity-protection-graph-api.md)

Informace o integraci informací o identitě s Azure Sentinel najdete v článku [připojení dat z Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Oprávnění

Pro přístup k aplikaci Identity Protection je potřeba, aby uživatelé byli čtenářem zabezpečení, bezpečnostním operátorem, správcem zabezpečení, globálním čtenářem nebo globálním správcem.

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Schopnost | Podrobnosti | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Free |
| --- | --- | --- | --- | --- |
| Rizikové zásady | Zásady rizik uživatelů (prostřednictvím Identity Protection) | Ano | Ne | Ne |
| Rizikové zásady | Zásady rizik přihlašování (přes Identity Protection nebo podmíněný přístup) | Ano | Ne | Ne |
| Zprávy o zabezpečení | Přehled | Ano | Ne | Ne |
| Zprávy o zabezpečení | Rizikové uživatele | Úplný přístup | Omezené informace | Omezené informace |
| Zprávy o zabezpečení | Riziková přihlášení | Úplný přístup | Omezené informace | Omezené informace |
| Zprávy o zabezpečení | Detekce rizik | Úplný přístup | Omezené informace | Ne |
| Oznámení | Uživatelé s výstrahami zjištěnými riziky | Ano | Ne | Ne |
| Oznámení | Týdenní výtah | Ano | Ne | Ne |
| | Zásady registrace MFA | Ano | Ne | Ne |

## <a name="next-steps"></a>Další kroky

- [Přehled zabezpečení](concept-identity-protection-security-overview.md)

- [Co je riziko](concept-identity-protection-risks.md)

- [Dostupné zásady pro zmírnění rizik](concept-identity-protection-policies.md)
