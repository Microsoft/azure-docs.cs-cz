---
title: Spravovat uživatelská data v Azure Security Center | Microsoft Docs
description: " Naučte se spravovat data uživatelů v Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 40299b2ff9a20792cf25828051d2f937f3f1e9da
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201282"
---
# <a name="manage-user-data-in-azure-security-center"></a>Správa uživatelských dat v Azure Security Center
Tento článek poskytuje informace o tom, jak můžete spravovat data uživatelů v Azure Security Center. Správa uživatelských dat zahrnuje možnost přístupu, odstranění a exportu dat.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Uživatel Security Center přiřazený k roli Čtenář, vlastník, přispěvatel nebo účet má přístup k zákaznickým datům v nástroji. Další informace o rolích čtenářů, vlastníků a přispěvatelů najdete v tématu [předdefinované role pro řízení přístupu na základě role v Azure](../role-based-access-control/built-in-roles.md) . Další informace o roli správce účtu najdete v tématu [Správci předplatného Azure](../billing/billing-add-change-azure-subscription-administrator.md) .

## <a name="searching-for-and-identifying-personal-data"></a>Hledání a identifikace osobních údajů
Uživatel Security Center může pomocí Azure Portal zobrazit jejich osobní údaje. Security Center ukládá pouze podrobnosti o kontaktech zabezpečení, jako jsou e-mailové adresy a telefonní čísla. Další informace najdete [v tématu Zadání podrobností o kontaktu zabezpečení v Azure Security Center](security-center-provide-security-contact-details.md) .

V Azure Portal může uživatel zobrazit povolené konfigurace IP adres pomocí funkce přístup k virtuálnímu počítači s přístupem k virtuálnímu počítači Security Center. Další informace najdete v tématu [Správa přístupu k virtuálním počítačům pomocí metody Just-in-Time](security-center-just-in-time.md).

V Azure Portal může uživatel zobrazit výstrahy zabezpečení, které poskytuje Security Center včetně IP adres a podrobností útočníka. Další informace najdete [v tématu Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) .

## <a name="classifying-personal-data"></a>Klasifikace osobních údajů
Nemusíte klasifikovat osobní údaje, které byly nalezeny ve funkci kontaktu zabezpečení Security Center. Uložená data jsou e-mailová adresa (nebo několik e-mailových adres) a telefonní číslo. [Kontaktní údaje](security-center-provide-security-contact-details.md) se ověřují pomocí Security Center.

Nemusíte klasifikovat IP adresy a čísla portů uložená funkcí pro funkci [Just-Time v čase](security-center-just-in-time.md) Security Center.

Jenom uživatel, který má přiřazenou roli správce, může klasifikovat osobní údaje [zobrazením výstrah](security-center-managing-and-responding-alerts.md) v Security Center.

## <a name="securing-and-controlling-access-to-personal-data"></a>Zabezpečení a řízení přístupu k osobním datům
Security Center uživatel, kterému byla přiřazena role čtenářů, vlastník, přispěvatel nebo účet, může získat přístup k [datům kontaktů zabezpečení](security-center-provide-security-contact-details.md).

Security Center uživatel, kterému byla přiřazena role čtenářů, vlastník, přispěvatel nebo účet, může přistupovat ke svým zásadám [podle času](security-center-just-in-time.md) .

Uživatel Security Center přiřazený k roli Čtenář, vlastník, přispěvatel nebo správce účtu, může zobrazit jejich [výstrahy](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Aktualizace osobních údajů
Uživatel Security Center přiřazený k roli vlastníka, přispěvatele nebo správce účtu může aktualizovat [data kontaktů zabezpečení](security-center-provide-security-contact-details.md) prostřednictvím Azure Portal.

Uživatel Security Center přiřazený k roli vlastníka, přispěvatele nebo správce účtu může aktualizovat zásady pro [právě v čase](security-center-just-in-time.md).

Správce účtu nemůže upravovat incidenty výstrah. [Incident výstrahy](security-center-managing-and-responding-alerts.md) se považuje za data zabezpečení a je jen pro čtení.

## <a name="deleting-personal-data"></a>Odstraňování osobních údajů
Uživatel Security Center přiřazený k roli vlastníka, přispěvatele nebo správce účtu může odstranit [data kontaktů zabezpečení](security-center-provide-security-contact-details.md) prostřednictvím Azure Portal.

Uživatel Security Center přiřazený k roli vlastníka, přispěvatele nebo správce účtu může pomocí Azure Portal odstranit [zásady pro jediný čas](security-center-just-in-time.md) .

Uživatel Security Center nemůže odstranit incidenty výstrahy. V důsledku potřeb zabezpečení se [incident výstrahy](security-center-managing-and-responding-alerts.md) považuje za data určená jen pro čtení.

## <a name="exporting-personal-data"></a>Exportování osobních údajů
Uživatel Security Center přiřazený k roli Čtenář, vlastník, přispěvatel nebo účet může exportovat [data kontaktů zabezpečení](security-center-provide-security-contact-details.md) podle těchto údajů:

- Probíhá kopírování z Azure Portal
- Spouští se volání služby Azure REST API, získat HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Uživatel, který má přiřazenou roli správce účtu Security Center, může exportovat [zásady podle času](security-center-just-in-time.md) , které obsahují IP adresy:

- Probíhá kopírování z Azure Portal
- Spouští se volání služby Azure REST API, získat HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Správce účtu může exportovat podrobnosti výstrahy podle těchto údajů:

- Probíhá kopírování z Azure Portal
- Spouští se volání služby Azure REST API, získat HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Další informace najdete v tématu [získání výstrah zabezpečení (získání kolekce)](https://msdn.microsoft.com/library/mt704050.aspx) .

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Omezení používání osobních údajů pro profilaci nebo marketing bez souhlasu
Uživatel Security Center se může rozhodnout, že se má odhlásit a odstranit [data kontaktů zabezpečení](security-center-provide-security-contact-details.md).

[Data v čase](security-center-just-in-time.md) se považují za neidentifikovatelná data, která se uchovávají po dobu 30 dnů.

[Data výstrah](security-center-managing-and-responding-alerts.md) se považují za bezpečnostní data a uchovávají se po dobu dvou let.

## <a name="auditing-and-reporting"></a>Auditování a vytváření sestav
Protokoly auditu pro kontakt se zabezpečením, tentokrát v čase a aktualizace výstrah se udržují v [protokolech aktivit Azure](../azure-monitor/platform/activity-logs-overview.md).

## <a name="next-steps"></a>Další kroky
Další informace o správě uživatelských dat najdete v tématu [Správa uživatelských dat nalezených v Azure Security Center šetření](security-center-investigation-user-data.md).
