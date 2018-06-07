---
title: Spravovat uživatelská data v Azure Security Center | Microsoft Docs
description: " Naučte se spravovat uživatelská data v Azure Security Center. "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: 2495bae5c63cdafe049ec39f71ab53106c5f2df7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654991"
---
# <a name="manage-user-data-in-azure-security-center"></a>Spravovat uživatelská data v Azure Security Center
Tento článek obsahuje informace o tom, jak můžete spravovat data uživatele v Azure Security Center. Správa uživatelských dat zahrnuje schopnost přístup, odstraňte nebo exportovat data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Security Center uživatel přiřazenou roli čtečky, vlastník, Přispěvatel nebo správce účtu může přistupovat k datům zákazníka v rámci nástroje. V tématu [předdefinované role pro řízení přístupu Azure na základě rolí](../role-based-access-control/built-in-roles.md) Další informace o rolích čtečky, vlastník a Přispěvatel. V tématu [správci předplatného Azure](../billing/billing-add-change-azure-subscription-administrator.md) Další informace o roli správce účtu.

## <a name="searching-for-and-identifying-personal-data"></a>Hledání a identifikaci osobní data
Uživatel Security Center můžete zobrazit jejich osobních údajů prostřednictvím portálu Azure. Security Center ukládá jenom zabezpečení kontaktní údaje, jako je například e-mailových adres a telefonních čísel. V tématu [zadejte kontaktní údaje zabezpečení v Azure Security Center](security-center-provide-security-contact-details.md) Další informace.

Na portálu Azure můžete zobrazit uživatele povolené konfigurace protokolu IP pomocí Security Center pouze ve funkci přístup čas virtuálních počítačů. Další informace najdete v tématu [Správa přístupu k virtuálním počítačům pomocí metody Just-in-Time](security-center-just-in-time.md).

Na portálu Azure může uživatel zobrazit výstrahy zabezpečení poskytované Security Center, včetně adres IP a útočník podrobnosti. V tématu [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) Další informace.

## <a name="classifying-personal-data"></a>Klasifikace osobní data
Není nutné ke klasifikaci osobní data uvedená v kontaktní funkce zabezpečení Security Center. Data uložená je e-mailovou adresu (nebo více e-mailové adresy) a telefonní číslo. [Obraťte se na data](security-center-provide-security-contact-details.md) se ověří pomocí služby Security Center.

Není potřeba klasifikovat IP adresy a portu čísla uložit pomocí služby Security Center [jenom na dobu](security-center-just-in-time.md) funkce.

Pouze uživatel přiřazenou roli správce můžete klasifikovat osobní data podle [zobrazení výstrah](security-center-managing-and-responding-alerts.md) ve službě Security Center.

## <a name="securing-and-controlling-access-to-personal-data"></a>Zabezpečení a řízení přístupu k osobním údajům
Security Center uživatel přiřazenou roli čtečky, vlastník, Přispěvatel nebo účet správce, můžete přístup [kontaktní údaje zabezpečení](security-center-provide-security-contact-details.md).

Security Center uživatel přiřazenou roli čtečky, vlastník, Přispěvatel nebo správce účtu může přístup svých [jenom na dobu](security-center-just-in-time.md) zásady.

Security Center uživatel přiřazenou roli čtečky, vlastník, Přispěvatel nebo správce účtu může zobrazovat jejich [výstrahy](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Aktualizace osobních dat
Security Center uživatel přiřazenou roli vlastník, Přispěvatel, nebo můžete aktualizovat účet správce [zabezpečení kontaktní údaje](security-center-provide-security-contact-details.md) prostřednictvím portálu Azure.

Security Center uživatel přiřazenou roli vlastník, Přispěvatel, nebo můžete aktualizovat účet správce jejich [jenom v zásadách čas](security-center-just-in-time.md).

Účet správce nelze upravit výstrah incidentů. [Výstrah incidentů](security-center-managing-and-responding-alerts.md) považuje za zabezpečení dat a je jen pro čtení.

## <a name="deleting-personal-data"></a>Odstranění osobní data
Security Center uživatel přiřazenou roli vlastník, Přispěvatel, nebo můžete odstranit správce účtu [zabezpečení kontaktní údaje](security-center-provide-security-contact-details.md) prostřednictvím portálu Azure.

Security Center uživatel přiřazenou roli vlastník, Přispěvatel, nebo můžete odstranit správce účtu [jenom v zásadách čas](security-center-just-in-time.md) prostřednictvím portálu Azure.

Security Center uživatele nelze odstranit výstrah incidentů. Z důvodu potřeby zabezpečení [výstrah incidentů](security-center-managing-and-responding-alerts.md) se považuje za čtení pouze data.

## <a name="exporting-personal-data"></a>Export osobní data
Security Center uživatel přiřazenou roli čtečky, vlastník, Přispěvatel nebo účet správce může exportovat [zabezpečení kontaktní údaje](security-center-provide-security-contact-details.md) podle:

- Provedení kopii z portálu Azure
- Provádění volání rozhraní REST API Azure GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
```

Security Center uživatel přiřazenou roli správce účtu služby můžete exportovat [jenom v zásadách čas](security-center-just-in-time.md) obsahující IP adres podle:

- Provedení kopii z portálu Azure
- Provádění volání rozhraní REST API Azure GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
```

Účet správce, můžete exportovat podrobnosti výstrahy podle:

- Provedení kopii z portálu Azure
- Provádění volání rozhraní REST API Azure GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
```

V tématu [získat výstrahy zabezpečení (získání kolekce)](https://msdn.microsoft.com/library/mt704050.aspx) Další informace.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Omezení použití osobních údajů pro profilace nebo marketingové bez souhlasu
Security Center uživatele můžete se rozhodnout chodit odstraněním jejich [kontaktní údaje zabezpečení](security-center-provide-security-contact-details.md).

[Právě v časových dat](security-center-just-in-time.md) považuje za-osobní údaje a se uchovávají po dobu 30 dnů.

[Data výstrahy](security-center-managing-and-responding-alerts.md) považuje za zabezpečení dat a se uchovávají po dobu dvou roků.

## <a name="auditing-and-reporting"></a>Auditování a vytváření sestav
Protokoly zabezpečení kontaktu auditu jenom na dobu a upozornit aktualizace jsou zachována ve [protokoly aktivity Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

## <a name="next-steps"></a>Další postup
Další informace o správě uživatelských dat najdete v tématu [správu dat uživatele v Azure Security Center šetření najít](security-center-investigation-user-data.md).
