---
title: Zásady uchování sestav Azure Active Directory | Dokumentace Microsoftu
description: Zásady uchovávání informací na data sestavy ve službě Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 932290c3b42b0ea55725fbc17de6b81886fe1217
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54822514"
---
# <a name="azure-active-directory-report-retention-policies"></a>Zásady uchování sestav Azure Active Directory

V tomto článku se dozvíte o zásad uchovávání dat pro různé aktivity sestavy ve službě Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Pokud Azure AD spusťte shromažďování dat?

| Azure AD Edition | Počáteční kolekce |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Když se zaregistrujete k předplatnému |
| Azure AD Free <br /> Azure AD Basic | Při prvním otevření [okno Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) nebo použijte [rozhraní API pro generování sestav](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Když se data aktivit k dispozici na webu Azure Portal

- **Okamžitě** – Pokud již pracujete se sestavami na webu Azure Portal.
- **Do 2 hodin** – Pokud jste nezapnuli generování sestav na webu Azure Portal.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Jak rychle můžete zobrazit data aktivity po získání licence premium?

Pokud již máte data aktivity s bezplatnou licencí, pak uvidíte ho okamžitě při upgradu. Pokud nemáte k dispozici žádná data, bude to trvat jeden nebo dva dny pro daná data zobrazit v sestavách po upgradu na licenci premium.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Můžete zobrazit data poslední měsíc po získání licenci Azure AD premium?

Pokud nedávno přešli na verzi premium (včetně zkušební verze), uvidíte data až na 7 dní původně. Když se data hromadí, se zobrazí data za posledních 30 dnů.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Pokud Azure AD začít signál shromažďování dat o zabezpečení?  

Pro zabezpečení signály, proces shromažďování začíná můžete vyjádřit výslovný souhlas pro použití **centrum Identity Protection**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Jak dlouho Azure AD ukládat data?

**Sestavy aktivit**    

| Sestava                 | Azure AD Free | Azure AD Basic | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--            | :--                 | :--                 |
| Audit adresáře        | 7 dní        |  7 dní        | 30 dní             | 30 dní             |
| Přihlašovací aktivita       | neuvedeno           |  neuvedeno           | 30 dní             | 30 dní             |
| Použití Azure MFA        | 30 dní       |  30 dní       | 30 dní             | 30 dní             |

Můžete zachovat data aktivit auditu a přihlaste se po dobu delší než výchozí dobu uchování uvedených výše přesměrováním do účtu služby Azure storage pomocí Azure monitoru. Další informace najdete v tématu [archiv služby Azure AD se zaznamená do účtu služby Azure storage](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Signály zabezpečení**

| Sestava         | Azure AD Free | Azure AD Basic | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--            | :--                 | :--                 |
| Ohrožení uživatelé  | 7 dní        | 7 dní         | 30 dní             | 90 dnů             |
| Riziková přihlášení | 7 dní        | 7 dní         |  30 dní            | 90 dnů             |

---
