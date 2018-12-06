---
title: Správa dat uživatele ve službě Azure Security Center | Dokumentace Microsoftu
description: " Další informace o správě dat uživatele ve službě Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: rkarlin
ms.openlocfilehash: 8417b342dc032f46fddd6c57b82c402cc6c1a10d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967721"
---
# <a name="manage-user-data-in-azure-security-center"></a>Správa dat uživatele ve službě Azure Security Center
Tento článek obsahuje informace o tom, jak můžete spravovat uživatelská data v Azure Security Center. Správa uživatelských dat zahrnuje schopnost přistupovat k, odstranit nebo exportovat data.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Security Center uživatel přiřazenou roli Čtenář, vlastník, Přispěvatel nebo správce účtu může přístup k zákaznickým datům v rámci nástroje. Zobrazit [předdefinované role pro řízení přístupu na základě rolí Azure](../role-based-access-control/built-in-roles.md) Další informace o rolích Čtenář, vlastníka a Přispěvatel. Zobrazit [správci předplatného Azure](../billing/billing-add-change-azure-subscription-administrator.md) získat další informace o roli správce účtu.

## <a name="searching-for-and-identifying-personal-data"></a>Hledání a identifikovat osobní údaje
Security Center uživatel mohl zobrazit jejich osobních údajů na webu Azure portal. Security Center se uchovávají pouze podrobností o kontaktu zabezpečení jako jsou e-mailových adres a telefonních čísel. Zobrazit [poskytnutí podrobností kontaktů zabezpečení ve službě Azure Security Center](security-center-provide-security-contact-details.md) Další informace.

Na webu Azure Portal může uživatel zobrazit povolená konfigurace IP adresy pomocí metody just-in čas virtuálního počítače přístup k funkci služby Security Center. Další informace najdete v tématu [Správa přístupu k virtuálním počítačům pomocí metody Just-in-Time](security-center-just-in-time.md).

Na webu Azure Portal může uživatel zobrazit výstrahy zabezpečení poskytne Security Center, včetně IP adresy a podrobnosti o útočníka. Zobrazit [Správa a zpracování výstrah zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md) Další informace.

## <a name="classifying-personal-data"></a>Klasifikace osobních údajů
Nemusíte klasifikace osobních údajů v funkce kontaktu zabezpečení Security Center. Data uložená se e-mailovou adresu (nebo více e-mailové adresy) a telefonní číslo. [Kontaktní údaje](security-center-provide-security-contact-details.md) ověření pomocí služby Security Center.

Není potřeba klasifikovat IP adresy a portu čísel uloženy službou Security Center [právě včas](security-center-just-in-time.md) funkce.

Pouze uživatel přiřazenou roli správce můžete klasifikace osobních údajů pomocí [zobrazení výstrah](security-center-managing-and-responding-alerts.md) ve službě Security Center.

## <a name="securing-and-controlling-access-to-personal-data"></a>Zabezpečení a řízení přístupu k osobním údajům
Security Center uživatel přiřazenou roli Čtenář, vlastník, Přispěvatel nebo správce účtu může přistupovat k [kontaktní údaje zabezpečení](security-center-provide-security-contact-details.md).

Security Center uživatel přiřazenou roli Čtenář, vlastník, Přispěvatel nebo správce účtu může přistupovat k jejich [právě včas](security-center-just-in-time.md) zásady.

Security Center uživatel přiřazenou roli Čtenář, vlastník, Přispěvatel nebo správce účtu může zobrazovat jejich [výstrahy](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Aktualizace osobních údajů
Security Center uživatel přiřazenou roli vlastník, Přispěvatel, nebo správce účtu může aktualizovat [kontaktní údaje zabezpečení](security-center-provide-security-contact-details.md) prostřednictvím webu Azure portal.

Security Center uživatel přiřazenou roli vlastník, Přispěvatel, nebo správce účtu může aktualizovat jejich [jenom v zásadách čas](security-center-just-in-time.md).

Správce účtu nelze upravit výstrah incidentů. [Výstrah incidentů](security-center-managing-and-responding-alerts.md) je považován za zabezpečení dat a je jen pro čtení.

## <a name="deleting-personal-data"></a>Odstraňování osobních údajů
Security Center uživatel přiřazenou roli vlastník, Přispěvatel, nebo účet správce může odstranit [kontaktní údaje zabezpečení](security-center-provide-security-contact-details.md) prostřednictvím webu Azure portal.

Security Center uživatel přiřazenou roli vlastník, Přispěvatel, nebo účet správce může odstranit [jenom v zásadách čas](security-center-just-in-time.md) prostřednictvím webu Azure portal.

Security Center uživatele nelze odstranit upozornění incidenty. Z důvodu potřeby zabezpečení [výstrah incidentů](security-center-managing-and-responding-alerts.md) se považuje za čtení pouze data.

## <a name="exporting-personal-data"></a>Exportování osobních údajů
Security Center uživatel přiřazenou roli Čtenář, vlastník, Přispěvatel nebo účet správce může exportovat [kontaktní údaje zabezpečení](security-center-provide-security-contact-details.md) podle:

- Při kopírování z portálu Azure portal
- Provádění volání rozhraní Azure REST API, GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
```

Security Center uživateli přiřadit roli správce účtu můžete exportovat [jenom v zásadách čas](security-center-just-in-time.md) obsahující IP adres podle:

- Při kopírování z portálu Azure portal
- Provádění volání rozhraní Azure REST API, GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
```

Účet správce může exportovat podrobných informacích výstrahy podle:

- Při kopírování z portálu Azure portal
- Provádění volání rozhraní Azure REST API, GET HTTP:
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
```

Zobrazit [výstrahy zabezpečení získat (GET kolekce)](https://msdn.microsoft.com/library/mt704050.aspx) Další informace.

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Omezení použití osobních údajů pro profilaci nebo marketingové bez souhlasu
Security Center uživatel může zvolit odhlásit tak, že odstraníte jejich [kontaktní údaje zabezpečení](security-center-provide-security-contact-details.md).

[Právě v datech časové](security-center-just-in-time.md) je považován za podnikově identifikovatelných dat a se uchovávají po dobu 30 dnů.

[Data výstrahy pro](security-center-managing-and-responding-alerts.md) je považován za zabezpečení dat a se uchovávají po dobu dvou let.

## <a name="auditing-and-reporting"></a>Auditování a vytváření sestav
Auditovat protokoly kontaktu zabezpečení za běhu a výstrahy aktualizace jsou zachována ve [protokolů aktivit Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

## <a name="next-steps"></a>Další postup
Další informace o správě uživatelských dat, naleznete v tématu [spravovat uživatelská data v Azure Security Center šetření](security-center-investigation-user-data.md).
