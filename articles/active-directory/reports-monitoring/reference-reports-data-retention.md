---
title: Zásady uchování sestav Azure Active Directory | Dokumentace Microsoftu
description: Zásady uchovávání informací na data sestavy ve službě Azure Active Directory
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
ms.component: report-monitor
ms.date: 05/10/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 68028fd1ba116251860e5c370e9e9ce61fd314bb
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42060066"
---
# <a name="azure-active-directory-report-retention-policies"></a>Zásady uchování sestav Azure Active Directory


Tento článek obsahuje odpovědi na nejčastější dotazy ve spojení s uchováním dat pro různé aktivity sestavy ve službě Azure Active Directory. 

### <a name="q-how-can-you-get-the-collection-of-activity-data-started"></a>Otázka: jak lze získat shromažďování dat aktivity spuštění?

**ODPOVĚĎ:**

| Edice Azure AD | Počáteční kolekce |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Když se zaregistrujete k předplatnému |
| Azure AD Free | Při prvním otevření [okno Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) nebo použijte [rozhraní API pro generování sestav](https://aka.ms/aadreports)  |

---
### <a name="q-when-is-your-activity-data-available-in-the-azure-portal"></a>Otázka: kdy je vaše data aktivit k dispozici na webu Azure Portal?

**ODPOVĚĎ:**

- **Okamžitě** – Pokud již pracujete se sestavami na webu Azure Portal.
- **Do 2 hodin** – Pokud jste nezapnuli generování sestav na webu Azure Portal.

---

### <a name="q-how-can-you-get-the-collection-of-security-signals-started"></a>Dotaz: jak lze získat kolekce signálů zabezpečení začít?  

**Odpověď:** pro zabezpečení signály, proces shromažďování začíná můžete vyjádřit výslovný souhlas používat Centrum Identity Protection. 


---

### <a name="q-for-how-long-is-the-collected-data-stored"></a>Otázka: jak dlouho se shromážděná data uloženy?

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
