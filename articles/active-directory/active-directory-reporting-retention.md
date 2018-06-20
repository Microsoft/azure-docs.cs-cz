---
title: Zásady uchování sestav Azure Active Directory | Microsoft Docs
description: Zásady uchovávání informací na data sestavy ve vašem Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: compliance-reports
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8edce7d1a633148cf361deec0c3d09ef4c16ac9b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225174"
---
# <a name="azure-active-directory-report-retention-policies"></a>Zásady uchování sestav Azure Active Directory


Tento článek obsahuje odpovědi na nejčastější dotazy ve spojení s uchovávání dat pro sestavy jinou aktivitu v Azure Active Directory. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>Otázka: jak můžete získat shromažďování dat aktivity spustit?

**ODPOVĚĎ:**

| Edice Azure AD | Počáteční kolekce |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Když se zaregistrujete k předplatnému |
| Azure AD Free | Při prvním spuštění [okno Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) nebo použít [reporting rozhraní API](https://aka.ms/aadreports)  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>Otázka: Pokud je vaše data aktivity k dispozici na portálu Azure?

**ODPOVĚĎ:**

- **Okamžitě** – Pokud již pracujete s sestav na portálu Azure.
- **V rámci 2 hodiny** – Pokud jste nezapnuli vytváření sestav v portálu Azure.

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>Otázka: jak můžete získat kolekce signálů zabezpečení spustit?  

**Odpověď:** signálů zabezpečení, se proces shromažďování spustí, když jste přihlásit k centru pro ochranu Identity použít. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>Otázka: jak dlouho shromážděná data ukládají?

**ODPOVĚĎ:**

**Sestavy aktivit**    

| Sestava                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Audit adresáře        | 7 dní        | 30 dní             | 30 dní             |
| Přihlašovací aktivita       | neuvedeno           | 30 dní             | 30 dní             |
| Použití Azure MFA        | 30 dní       | 30 dní             | 30 dní             |

**Signály zabezpečení**

| Sestava         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Ohrožení uživatelé  | 7 dní        | 30 dní             | 90 dnů             |
| Riziková přihlášení | 7 dní        | 30 dní             | 90 dnů             |

---
