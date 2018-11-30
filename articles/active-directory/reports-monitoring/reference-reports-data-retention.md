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
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: abd64b7d2fa7930f5b6177c7ac037840da34dc18
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52333577"
---
# <a name="azure-active-directory-report-retention-policies"></a>Zásady uchování sestav Azure Active Directory

V tomto článku se dozvíte o zásad uchovávání dat pro různé aktivity sestavy ve službě Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Pokud Azure AD spusťte shromažďování dat?

| Edice Azure AD | Počáteční kolekce |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Když se zaregistrujete k předplatnému |
| Azure AD Free <br /> Azure AD Basic | Při prvním otevření [okno Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) nebo použijte [rozhraní API pro generování sestav](https://aka.ms/aadreports)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Když se data aktivit k dispozici na webu Azure Portal

- **Okamžitě** – Pokud již pracujete se sestavami na webu Azure Portal.
- **Do 2 hodin** – Pokud jste nezapnuli generování sestav na webu Azure Portal.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Pokud Azure AD začít signál shromažďování dat o zabezpečení?  

Pro zabezpečení signály, proces shromažďování začíná můžete vyjádřit výslovný souhlas pro použití **centrum Identity Protection**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Jak dlouho Azure AD ukládat data?

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
