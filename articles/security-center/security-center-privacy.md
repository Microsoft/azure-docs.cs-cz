---
title: Spravovat uživatelská data v Azure Security Center | Microsoft Docs
description: Naučte se spravovat data uživatelů v Azure Security Center. Správa uživatelských dat zahrnuje možnost přístupu, odstranění a exportu dat.
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
ms.openlocfilehash: bf715d872fab421de30ebcb146a1981a7d008738
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80585972"
---
# <a name="manage-user-data-in-azure-security-center"></a>Správa uživatelských dat ve službě Azure Security Center
Tento článek poskytuje informace o tom, jak můžete spravovat data uživatelů v Azure Security Center. Správa uživatelských dat zahrnuje možnost přístupu, odstranění a exportu dat.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Uživatel Security Center přiřazený k roli Čtenář, vlastník, přispěvatel nebo účet má přístup k zákaznickým datům v nástroji. Další informace o roli správce účtu najdete v tématu [předdefinované role pro řízení přístupu na základě role v Azure](../role-based-access-control/built-in-roles.md) , kde se dozvíte víc o rolích čtenářů, vlastníků a přispěvatelů. Viz [Správce předplatného Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Vyhledávání a identifikace osobních údajů
Uživatel Security Center může pomocí Azure Portal zobrazit jejich osobní údaje. Security Center ukládá pouze podrobnosti o kontaktech zabezpečení, jako jsou e-mailové adresy a telefonní čísla. Další informace najdete v tématu [zadání podrobností o kontaktu zabezpečení v Azure Security Center](security-center-provide-security-contact-details.md).

V Azure Portal může uživatel zobrazit povolené konfigurace IP pomocí funkce přístup k virtuálnímu počítači za běhu Security Center. Další informace najdete v tématu [Správa přístupu k virtuálnímu počítači pomocí za běhu](security-center-just-in-time.md).

V Azure Portal může uživatel zobrazit výstrahy zabezpečení, které poskytuje Security Center včetně IP adres a podrobností útočníka. Další informace najdete v tématu [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Klasifikace osobních údajů
Nemusíte klasifikovat osobní údaje, které se našly ve funkci kontaktování zabezpečení Security Center. Uložená data jsou e-mailová adresa (nebo několik e-mailových adres) a telefonní číslo. [Kontaktní údaje](security-center-provide-security-contact-details.md) se ověřují pomocí Security Center.

Nemusíte klasifikovat IP adresy a čísla portů uložená funkcí [za](security-center-just-in-time.md) běhu Security Center.

Jenom uživatel, který má přiřazenou roli správce, může klasifikovat osobní údaje [zobrazením výstrah](security-center-managing-and-responding-alerts.md) v Security Center.

## <a name="securing-and-controlling-access-to-personal-data"></a>Zabezpečení a řízení přístupu k osobním datům
Security Center uživatel, kterému byla přiřazena role čtenářů, vlastník, přispěvatel nebo účet, může získat přístup k [datům kontaktů zabezpečení](security-center-provide-security-contact-details.md).

Security Center uživatel, kterému byla přiřazena role čtenářů, vlastník, přispěvatel nebo účet, může přistupovat ke svým zásadám [za](security-center-just-in-time.md) běhu.

Uživatel Security Center přiřazený k roli Čtenář, vlastník, přispěvatel nebo správce účtu, může zobrazit jejich [výstrahy](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Aktualizace osobních dat
Uživatel Security Center přiřazený k roli vlastníka, přispěvatele nebo správce účtu může aktualizovat [data kontaktů zabezpečení](security-center-provide-security-contact-details.md) prostřednictvím Azure Portal.

Uživatel Security Center přiřazený k roli vlastníka, přispěvatele nebo správce účtu může aktualizovat zásady za [běhu](security-center-just-in-time.md).

Správce účtu nemůže upravovat incidenty výstrah. [Incident výstrahy](security-center-managing-and-responding-alerts.md) se považuje za data zabezpečení a je jen pro čtení.

## <a name="deleting-personal-data"></a>Odstranění osobních dat
Uživatel Security Center přiřazený k roli vlastníka, přispěvatele nebo správce účtu může odstranit [data kontaktů zabezpečení](security-center-provide-security-contact-details.md) prostřednictvím Azure Portal.

Uživatel Security Center přiřazený k roli vlastníka, přispěvatele nebo správce účtu může pomocí Azure Portal odstranit [zásady za běhu](security-center-just-in-time.md) .

Security Center uživatel nemůže odstranit incidenty výstrahy. Z bezpečnostních důvodů se [incident výstrahy](security-center-managing-and-responding-alerts.md) považuje za data jen pro čtení.

## <a name="exporting-personal-data"></a>Export osobních dat
Uživatel Security Center přiřazený k roli Čtenář, vlastník, přispěvatel nebo účet může exportovat [data kontaktů zabezpečení](security-center-provide-security-contact-details.md) podle těchto údajů:

- Kopírování z Azure Portal
- Spouští se volání služby Azure REST API, získat HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Security Center uživatel, kterému je přiřazena role správce účtu, může exportovat [zásady za běhu](security-center-just-in-time.md) , které obsahují IP adresy:

- Kopírování z Azure Portal
- Spouští se volání služby Azure REST API, získat HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Správce účtu může exportovat podrobnosti výstrahy podle těchto údajů:

- Kopírování z Azure Portal
- Spouští se volání služby Azure REST API, získat HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Další informace najdete v tématu [získání výstrah zabezpečení (získání kolekce)](https://msdn.microsoft.com/library/mt704050.aspx).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Omezení používání osobních údajů pro profilaci nebo marketing bez souhlasu
Uživatel Security Center se může rozhodnout, že se má odhlásit a odstranit [data kontaktů zabezpečení](security-center-provide-security-contact-details.md).

[Data za běhu](security-center-just-in-time.md) se považují za neidentifikovatelná data, která se uchovávají po dobu 30 dnů.

[Data výstrah](security-center-managing-and-responding-alerts.md) se považují za bezpečnostní data a uchovávají se po dobu dvou let.

## <a name="auditing-and-reporting"></a>Auditování a vytváření sestav
Protokoly auditu v [protokolech aktivit Azure](../azure-monitor/platform/platform-logs-overview.md)se uchovávají v protokolech auditování zabezpečení, za běhu a výstrahy.