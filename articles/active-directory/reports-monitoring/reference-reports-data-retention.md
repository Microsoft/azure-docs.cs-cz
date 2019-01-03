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
ms.openlocfilehash: 6188b141ec1a514d999f290366cd9ffbafc1d96c
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2018
ms.locfileid: "53753128"
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
