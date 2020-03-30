---
title: Jak dlouho azure ad ukládat data vytváření sestav? | Dokumentace Microsoftu
description: Zjistěte, jak dlouho Azure ukládá různé typy dat sestav.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54636600c208f8f5df9fa2e25460c63dd9f46e85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239549"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Jak dlouho azure ad ukládat data vytváření sestav?


V tomto článku se dozvíte o zásadách uchovávání dat pro různé sestavy aktivit ve službě Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Kdy azure ad začít shromažďovat data?

| Azure AD Edition | Začátek kolekce |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Když si zaregistrujete předplatné |
| Azure AD Free| Při prvním otevření [okna služby Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) nebo při použití [rozhraní API pro vytváření sestav](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Kdy jsou data o aktivitách dostupná na webu Azure Portal?

- **Okamžitě** – Pokud jste už pracovali se sestavami na webu Azure Portal.
- **Do 2 hodin** – pokud jste nezapnuli vytváření sestav na webu Azure Portal.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Za jak dlouho se mi po získání prémiové licence zobrazí údaje o aktivitách?

Pokud již máte údaje o aktivitách s bezplatnou licencí, můžete je okamžitě vidět při upgradu. Pokud nemáte žádná data, bude trvat jeden nebo dva dny, než se data zobrazí v přehledech po upgradu na prémiovou licenci.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Můžu vidět data z minulého měsíce po získání licence Azure AD premium?

Pokud jste nedávno přešli na prémiovou verzi (včetně zkušební verze), můžete zpočátku zobrazit data až 7 dní. Když se data hromadí, můžete zobrazit data za posledních 30 dní.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Kdy azure ad začít shromažďovat data signálu zabezpečení?  

U zabezpečovacích signálů se proces shromažďování spustí, když se přihlásíte k používání **Centra ochrany identity**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Jak dlouho Azure AD ukládat data?

**Sestavy aktivit**    

| Sestava                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Protokoly auditu             | 7 dní        | 30 dní             | 30 dní             |
| Přihlášení               | 7 dní        | 30 dní             | 30 dní             |
| Využití Azure MFA        | 30 dní       | 30 dní             | 30 dní             |

Data o aktivitách auditu a přihlášení můžete uchovávat déle, než je výchozí doba uchovávání uvedená výše, a to tak, že je pomocí Azure Monitoru přejdete na účet úložiště Azure. Další informace najdete [v tématu archivace protokolů Azure AD na účet úložiště Azure](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Bezpečnostní signály**

| Sestava         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Ohrožení uživatelé  | 7 dní        | 30 dní             | 90 dnů             |
| Riziková přihlášení | 7 dní        | 30 dní             | 90 dnů             |

---
