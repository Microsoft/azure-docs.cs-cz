---
title: Správa uživatelských dat v Azure Security Center | Dokumenty společnosti Microsoft
description: Zjistěte, jak spravovat uživatelská data v Azure Security Center. Správa uživatelských dat zahrnuje možnost přístupu k datům, jejich odstranění nebo exportu.
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
ms.openlocfilehash: 6edea1d0de53e2dc9f764de26209dc1f3110556e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978605"
---
# <a name="manage-user-data-in-azure-security-center"></a>Správa uživatelských dat ve službě Azure Security Center
Tento článek obsahuje informace o tom, jak můžete spravovat uživatelská data v Azure Security Center. Správa uživatelských dat zahrnuje možnost přístupu k datům, jejich odstranění nebo exportu.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Uživatel Centra zabezpečení, který má přiřazenou roli čtenáře, vlastníka, přispěvatele nebo správce účtu, má v nástroji přístup k zákaznickým datům. Další informace o roli správce účtu najdete [v tématu předdefinované role pro řízení přístupu na základě rolí Azure,](../role-based-access-control/built-in-roles.md) kde se dozvíte víc o rolích Reader, Owner a Contributor. Viz [Správci předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Vyhledávání a identifikace osobních údajů
Uživatel Centra zabezpečení může zobrazit svá osobní data prostřednictvím portálu Azure. Security Center ukládá pouze bezpečnostní kontaktní údaje, jako jsou e-mailové adresy a telefonní čísla. Další informace najdete [v tématu Poskytování podrobností o kontaktu se zabezpečením v Centru zabezpečení Azure](security-center-provide-security-contact-details.md).

Na webu Azure Portal může uživatel zobrazit povolené konfigurace IP adres pomocí funkce přístupu k virtuálním počítačům centra zabezpečení v čase. Další informace najdete [v tématu Správa přístupu k virtuálním strojům pomocí just-in-time](security-center-just-in-time.md).

Na webu Azure Portal může uživatel zobrazit výstrahy zabezpečení poskytované Centrem zabezpečení, včetně IP adres a podrobností o útočníkovi. Další informace najdete [v tématu Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Klasifikace osobních údajů
Není nutné klasifikovat osobní údaje, které se nacházejí v bezpečnostní kontaktní funkci centra zabezpečení. Uložená data jsou e-mailová adresa (nebo více e-mailových adres) a telefonní číslo. [Kontaktní data](security-center-provide-security-contact-details.md) jsou ověřena centrem zabezpečení.

Není nutné klasifikovat IP adresy a čísla portů uložené funkcí [just-in-time](security-center-just-in-time.md) centra zabezpečení.

Pouze uživatel přiřazený roli správce může klasifikovat osobní údaje [zobrazením výstrah](security-center-managing-and-responding-alerts.md) v Centru zabezpečení.

## <a name="securing-and-controlling-access-to-personal-data"></a>Zabezpečení a kontrola přístupu k osobním údajům
Uživatel Centra zabezpečení, který má přiřazenou roli čtečky, vlastníka, přispěvatele nebo správce účtu, může přistupovat k [datům bezpečnostních kontaktů](security-center-provide-security-contact-details.md).

Uživatel Centra zabezpečení, který přiřadil roli čtenáře, vlastníka, přispěvatele nebo správce účtu, může přistupovat ke svým [zásadám just-in-time.](security-center-just-in-time.md)

Uživatel Centra zabezpečení, který přiřadil roli čtenáře, vlastníka, přispěvatele nebo správce účtu, může zobrazit jejich [výstrahy](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Aktualizace osobních dat
Uživatel Centra zabezpečení, který přiřadil roli vlastníka, přispěvatele nebo správce účtu, může aktualizovat [data kontaktu zabezpečení](security-center-provide-security-contact-details.md) prostřednictvím portálu Azure.

Uživatel Centra zabezpečení, který má přiřazenou roli vlastníka, přispěvatele nebo správce účtu, může aktualizovat své [zásady "just-in-time"](security-center-just-in-time.md).

Správce účtu nemůže upravovat incidenty výstrah. [Výstražný incident](security-center-managing-and-responding-alerts.md) je považován za bezpečnostní data a je jen pro čtení.

## <a name="deleting-personal-data"></a>Odstranění osobních dat
Uživatel Centra zabezpečení, který přiřadil roli vlastníka, přispěvatele nebo správce účtu, může odstranit [data kontaktu zabezpečení](security-center-provide-security-contact-details.md) prostřednictvím portálu Azure.

Uživatel Centra zabezpečení, který přiřadil roli vlastníka, přispěvatele nebo správce účtu, může odstranit [zásady just-in-time](security-center-just-in-time.md) prostřednictvím portálu Azure.

Uživatel centra zabezpečení nemůže odstranit incidenty výstrah. Z bezpečnostních důvodů je [incident výstrahy](security-center-managing-and-responding-alerts.md) považován za data jen pro čtení.

## <a name="exporting-personal-data"></a>Export osobních dat
Uživatel Centra zabezpečení, který přiřadil roli čtenáře, vlastníka, přispěvatele nebo správce účtu, může exportovat [kontaktní data zabezpečení:](security-center-provide-security-contact-details.md)

- Kopírování z webu Azure Portal
- Spuštění volání rozhraní API Azure REST, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Uživatel Centra zabezpečení, který má přiřazenou roli správce účtu, může exportovat [zásady just-in-time](security-center-just-in-time.md) obsahující IP adresy pomocí:

- Kopírování z webu Azure Portal
- Spuštění volání rozhraní API Azure REST, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Správce účtu může exportovat podrobnosti výstrahy takto:

- Kopírování z webu Azure Portal
- Spuštění volání rozhraní API Azure REST, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Další informace naleznete v [tématu Získání výstrah zabezpečení (kolekce GET).](https://msdn.microsoft.com/library/mt704050.aspx)

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Omezení používání osobních údajů pro profilování nebo marketing bez souhlasu
Uživatel centra zabezpečení se může rozhodnout, že se odhlásí, a to odstraněním svých [kontaktních údajů zabezpečení](security-center-provide-security-contact-details.md).

[Údaje just-in-time](security-center-just-in-time.md) jsou považovány za neidentifikovatelné údaje a uchovávají se po dobu 30 dnů.

[Výstražná data](security-center-managing-and-responding-alerts.md) jsou považována za bezpečnostní data a uchovávají se po dobu dvou let.

## <a name="auditing-and-reporting"></a>Audit a podávání zpráv
Protokoly auditu kontaktu zabezpečení, aktualizace just-in-time a výstrahy jsou udržovány v [protokolech aktivit Azure](../azure-monitor/platform/platform-logs-overview.md).

## <a name="next-steps"></a>Další kroky
Další informace o správě uživatelských dat najdete [v tématu Správa uživatelských dat nalezených ve vyšetřovacím šetření Centra zabezpečení Azure](security-center-investigation-user-data.md).
