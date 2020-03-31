---
title: Co je Azure Active Directory Identity Protection?
description: Zjišťování, náprava, zkoumání a analýza rizik pomocí azure ad identity protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 03/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2b1d9748b243dcc2104ce7b8e0e8735a7b7276f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79497672"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Co je Azure Active Directory Identity Protection?

Ochrana identity je nástroj, který organizacím umožňuje provádět tři klíčové úkoly:

- Automatizujte zjišťování a nápravu rizik založených na identitě.
- Prozkoumejte rizika pomocí dat na portálu.
- Exportujte data detekce rizik do nástrojů třetích stran pro další analýzu.

Ochrana identity využívá poznatky, které Microsoft získal ze své pozice v organizacích s Azure AD, spotřebitelského prostoru s účty Microsoft a hraní her s konzolí Xbox k ochraně vašich uživatelů. Společnost Microsoft analyzuje 6,5 bilionu signálů denně, aby identifikovala a ochránila zákazníky před hrozbami.

Signály generované a přiváděné do ochrany identity mohou být dále vloženy do nástrojů, jako je podmíněný přístup, aby se rozhodovaly o přístupu, nebo vráceny nástroji pro správu informací o zabezpečení a událostí (SIEM) pro další šetření na základě vynucených politik.

## <a name="why-is-automation-important"></a>Proč je automatizace důležitá?

Alex Weinert, který vede tým microsoftu Identity Security and Protection, ve svém [příspěvku na blogu v říjnu 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843) vysvětluje, proč je automatizace při řešení objemu událostí tak důležitá:

> Každý den naše systémy strojového učení a heuristické systémy poskytují skóre rizik a 18 miliard pokusů o přihlášení pro více než 800 milionů odlišných účtů, z nichž 300 milionů jsou zřetelně prováděny protivníky (subjekty jako: kriminální aktéři, hackeři).
>
> Minulý rok jsem v Ignite mluvil o 3 nejlepších útocích na naše identifikační systémy. Zde je nedávný objem těchto útoků
>   
>   - **Replay porušení**: 4.6BN útoky zjištěné v květnu 2018
>   - **Heslo sprej**: 350k v dubnu 2018
>   - **Phishing**: To je těžké přesně kvantifikovat, ale v březnu 2018 jsme viděli 23M rizikové události, z nichž mnohé jsou spojeny s phish

## <a name="risk-detection-and-remediation"></a>Detekce rizik a náprava

Ochrana identity identifikuje rizika v následujících klasifikacích:

| Typ detekce rizik | Popis |
| --- | --- |
| Atypické cestování | Přihlaste se z atypického umístění na základě nedávných přihlášení uživatele. |
| Anonymní IP adresa | Přihlaste se z anonymní IP adresy (například: Tor browser, anonymizer VNU). |
| Neznámé přihlašovací vlastnosti | Přihlaste se pomocí vlastností, které jsme u daného uživatele v poslední době neviděli. |
| Ip adresa propojená s malwarem | Přihlášení z IP adresy propojené s malwarem |
| Uniklá pověření | Toto zjištění rizik a informací znamená, že došlo k úniku platných přihlašovacích údajů uživatele. |
| Azure AD threat intelligence | Interní a externí zdroje analýzy hrozeb společnosti Microsoft identifikovaly známý vzorec útoku |

Podrobnější informace o těchto rizicích a jak/kdy jsou vypočtena, naleznete v článku [Co je riziko](concept-identity-protection-risks.md).

Signály rizikmohou vyvolat nápravné úsilí, jako je například vyžadování, aby uživatelé: provedli azure multifaktorové ověřování, resetovali heslo pomocí samoobslužného resetování hesla nebo blokovali, dokud správce neprovede akci.

## <a name="risk-investigation"></a>Vyšetřování rizik

Správci mohou zkontrolovat zjišťování a v případě potřeby na nich provést ruční akci. Existují tři klíčové sestavy, které správci používají pro vyšetřování v identity protection:

- Rizikoví uživatelé
- Riziková přihlášení
- Detekce rizik

Další informace naleznete v článku [Jak: Prozkoumat riziko](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Export údajů o rizicích

Data z identity protection lze exportovat do jiných nástrojů pro archivaci a další šetření a spoluvztah. Rozhraní API založená na microsoft graphu umožňují organizacím shromažďovat tato data pro další zpracování v nástroji, jako je jejich SIEM. Informace o přístupu k rozhraní API ochrany identity najdete v článku [Začínáme s Azure Active Directory Identity Protection a Microsoft Graph](howto-identity-protection-graph-api.md)

Informace o integraci informací o ochraně identity s Azure Sentinelem najdete v článku [Connect data from Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Oprávnění

Ochrana identity vyžaduje, aby uživatelé byli pro přístup čtečkou zabezpečení, operátorem zabezpečení, správcem zabezpečení, globální čtečkou nebo globálním správcem.

| Role | Může dělat | Nelze provést |
| --- | --- | --- |
| Globální správce | Plný přístup k ochraně identity |   |
| Správce zabezpečení | Plný přístup k ochraně identity | Resetování hesla pro uživatele |
| Bezpečnostní operátor | Zobrazit všechny sestavy identity protection a okno Přehled <br><br> Zamítněte riziko uživatele, potvrďte bezpečné přihlášení, potvrďte kompromis | Konfigurace nebo změna zásad <br><br> Resetování hesla pro uživatele <br><br> Konfigurace upozornění |
| Čtenář zabezpečení | Zobrazit všechny sestavy identity protection a okno Přehled | Konfigurace nebo změna zásad <br><br> Resetování hesla pro uživatele <br><br> Konfigurace upozornění <br><br> Posvězte názor na detekce |

Správci podmíněného přístupu mohou také vytvářet zásady, které zvažují riziko přihlášení jako podmínku, další informace naleznete v článku [Podmíněný přístup: Podmínky](../conditional-access/concept-conditional-access-conditions.md#sign-in-risk).

## <a name="license-requirements"></a>Licenční požadavky

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Schopnost | Podrobnosti | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Free |
| --- | --- | --- | --- | --- |
| Rizikové politiky | Zásady rizika uživatelů (prostřednictvím ochrany identity) | Ano | Ne | Ne |
| Rizikové politiky | Zásady rizika přihlášení (prostřednictvím ochrany identity nebo podmíněného přístupu) | Ano | Ne | Ne |
| Sestavy zabezpečení | Přehled | Ano | Ne | Ne |
| Sestavy zabezpečení | Rizikoví uživatelé | Full access | Omezené informace | Omezené informace |
| Sestavy zabezpečení | Riziková přihlášení | Full access | Omezené informace | Omezené informace |
| Sestavy zabezpečení | Detekce rizik | Full access | Omezené informace | Ne |
| Oznámení | Uživatelé v ohrožení zjištěné výstrahy | Ano | Ne | Ne |
| Oznámení | Týdenní digest | Ano | Ne | Ne |
| | Zásady registrace vícefaktorové registrace | Ano | Ne | Ne |

## <a name="next-steps"></a>Další kroky

- [Přehled zabezpečení](concept-identity-protection-security-overview.md)

- [Co je riziko](concept-identity-protection-risks.md)

- [Dostupné zásady ke zmírnění rizik](concept-identity-protection-policies.md)
