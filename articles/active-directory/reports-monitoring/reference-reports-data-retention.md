---
title: Jak dlouho jsou data sestav služby Azure AD Store? | Dokumentace Microsoftu
description: Přečtěte si, jak dlouho Azure ukládá různé typy dat generování sestav.
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
ms.date: 11/05/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc5f902d75084d649f211d589e53041f1eb37f0e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012710"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Jak dlouho jsou data sestav služby Azure AD Store?


V tomto článku se dozvíte o zásadách uchovávání dat pro různé sestavy aktivit v Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Kdy Azure AD začne shromažďovat data?

| Edice Azure AD | Začátek shromažďování |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Když se přihlásíte k předplatnému |
| Azure AD Free| Při prvním otevření okna [Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) nebo použití [rozhraní API pro vytváření sestav](./overview-reports.md)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Kdy jsou data aktivity k dispozici v Azure Portal?

- **Okamžitě** – Pokud jste již pracovali se sestavami v Azure Portal.
- **Do 2 hodin** – Pokud jste neaktivovali vytváření sestav v Azure Portal.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Jak brzy můžu zobrazit data o aktivitách po získání licence na prémii?

Pokud již máte data o aktivitách s bezplatnou licencí, můžete ji okamžitě zobrazit při upgradu. Pokud nemáte žádná data, bude trvat jeden nebo dva dny, než se data zobrazí v sestavách po upgradu na licenci Premium.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Kdy Azure AD začne shromažďovat data bezpečnostních signálů?  

Pro bezpečnostní signály se proces shromažďování spustí, když se přihlásíte k používání **centra ochrany identit**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Jak dlouho Azure AD ukládá data?

**Sestavy aktivit**    

| Sestava                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Protokoly auditu             | 7 dní        | 30 dní             | 30 dní             |
| Přihlášení               | 7 dní        | 30 dní             | 30 dní             |
| Využití Azure AD MFA        | 30 dní       | 30 dní             | 30 dní             |

Data aktivity auditu a přihlašování můžete uchovávat déle než výchozí doba uchování, která je uvedená výše, směrováním do účtu služby Azure Storage pomocí Azure Monitor. Další informace najdete v tématu [archivace protokolů služby Azure AD do účtu služby Azure Storage](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Signály zabezpečení**

| Sestava         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Ohrožení uživatelé  | 7 dní        | 30 dní             | 90 dnů             |
| Riziková přihlášení | 7 dní        | 30 dní             | 90 dnů             |

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Můžu zobrazit data z posledního měsíce po získání licence Azure AD Premium?

**Ne**, nemůžete. Azure ukládá až sedm dní dat o aktivitách pro bezplatnou verzi. To znamená, že když přepnete ze bezplatné verze na verzi Premium, zobrazí se vám jenom 7 dní dat.

---