---
title: E-mailová oznámení v PIM – Azure Active Directory | Dokumenty společnosti Microsoft
description: Popisuje e-mailová oznámení v privilegované správě identit (PIM) služby Azure AD.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 04/21/2020
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 201abd24bc4056337f1ffecd2dabd002ae352c74
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81866423"
---
# <a name="email-notifications-in-pim"></a>E-mailová oznámení v PIM

Privilegovaná správa identit (PIM) vás informuje o tom, kdy dojde k důležitým událostem v organizaci Azure Active Directory (Azure AD), například když je role přiřazená nebo aktivovaná. Správa privilegovaných identit vás informuje tím, že vám a ostatním účastníkům pošle e-mailová oznámení. Tyto e-maily mohou také obsahovat odkazy na příslušné úkoly, například aktivaci nebo obnovení role. Tento článek popisuje, jak tyto e-maily vypadají, kdy jsou odeslány a kdo je přijímá.

## <a name="sender-email-address-and-subject-line"></a>E-mailová adresa odesílatele a řádek předmětu

E-maily odeslané ze správy privilegovaných identit pro role prostředků Azure AD i Azure mají následující e-mailovou adresu odesílatele:

- E-mailová adresa: **azure-noreply\@microsoft.com**
- Zobrazovaný název: Microsoft Azure

Tyto e-maily obsahují předponu **PIM** v řádku předmětu. Tady je příklad:

- PIM: Alain Charon byl trvale přiřazen role Backup Reader

## <a name="notifications-for-azure-ad-roles"></a>Oznámení pro role Azure AD

Správa privilegovaných identit odesílá e-maily, když dojde k následujícím událostem pro role Azure AD:

- Pokud aktivace privilegované role čeká na schválení
- Po dokončení žádosti o aktivaci privilegované role
- Když je povolena správa privilegovaných identit Azure AD

Kdo obdrží tyto e-maily pro role Azure AD závisí na vaší roli, události a nastavení oznámení:

| Uživatel | Aktivace role čeká na schválení. | Požadavek na aktivaci role je dokončen. | PIM je povolena |
| --- | --- | --- | --- |
| Správce privilegovaných rolí</br>(Aktivováno/způsobilé) | Ano</br>(pouze v případě, že nejsou zadáni žádní explicitní schvalovatelé) | Ano* | Ano |
| Správce zabezpečení</br>(Aktivováno/způsobilé) | Ne | Ano* | Ano |
| Globální správce</br>(Aktivováno/způsobilé) | Ne | Ano* | Ano |

\*Pokud je nastavení [ **Oznámení** ](pim-how-to-change-default-settings.md#notifications) nastaveno na **Povolit**.

Následující text ukazuje ukázkový e-mail, který se odesílá, když uživatel aktivuje roli Azure AD pro fiktivní organizaci Contoso.

![Nový e-mail správy privilegovaných identit pro role Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Týdenní správa privilegovaných identit digest e-mail pro role Azure AD

Týdenní souhrnný e-mail správy privilegovaných identit pro role Azure AD se odesílá správcům privilegovaných rolí, správcům zabezpečení a globálním správcům, kteří povolili správu privilegovaných identit. Tento týdenní e-mail poskytuje snímek aktivit správy privilegovaných identit pro tento týden, stejně jako přiřazení privilegovaných rolí. Je k dispozici jenom pro klienty ve veřejném cloudu. Zde je příklad e-mailu:

![Týdenní správa privilegovaných identit digest e-mail pro role Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

E-mail obsahuje čtyři dlaždice:

| dlaždici | Popis |
| --- | --- |
| **Uživatelé aktivováni** | Počet, kolikrát uživatelé aktivovali svou způsobilou roli uvnitř klienta. |
| **Uživatelé se trvalé** | Počet trvalých nastavení uživatelů se způsobilým přiřazením. |
| **Přiřazení rolí ve správě privilegovaných identit** | Počet, kolikrát je uživatelům přiřazena způsobilá role v rámci správy privilegovaných identit. |
| **Přiřazení rolí mimo PIM** | Počet, kolikrát uživatelům jsou přiřazeny trvalé role mimo privilegované správy identit (uvnitř Azure AD). |

**Přehled hlavních rolí** v části uvádí prvních pět rolí ve vašem tenantovi na základě celkového počtu stálých a způsobilých správců pro každou roli. Odkaz **Přijmout akci** otevře průvodce [PIM,](pim-security-wizard.md) kde můžete převést trvalé správce na oprávněné správce v dávkách.

## <a name="email-timing-for-activation-approvals"></a>Časování e-mailu pro schválení aktivace

Když uživatelé aktivují svou roli a nastavení role vyžaduje schválení, schvalovatelé obdrží tři e-maily pro každé schválení:

- Žádost o schválení nebo zamítnutí žádosti o aktivaci uživatele (odeslaná schvalovacím strojem žádosti)
- Požadavek uživatele je schválen (odeslán o schválení žádosti)
- Role uživatele je aktivována (odeslána službou Privileged Identity Management)

První dva e-maily odeslané strojem pro schválení žádosti mohou být zpožděny. V současné době trvá 90% e-mailů tři až deset minut, ale pro 1% zákazníky to může být mnohem delší, až patnáct minut.

Pokud je žádost o schválení schválena na webu Azure Portal před odesláním prvního e-mailu, první e-mail se už neaktivuje a ostatní schvalovatelé nebudou e-mailem upozorněni na žádost o schválení. Může se zdát, že nedostali e-mail, ale je to očekávané chování.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM e-maily pro role prostředků Azure

Správa privilegovaných identit odesílá e-maily vlastníkům a správcům přístupu uživatelů, když dojde k následujícím událostem pro role prostředků Azure:

- Pokud přiřazení role čeká na schválení
- Když je role přiřazena
- Když brzy vyprší platnost role
- Je-li role způsobilá k prodloužení
- Při obnově role koncovým uživatelem
- Po dokončení žádosti o aktivaci role

Správa privilegovaných identit odesílá e-maily koncovým uživatelům, když dojde k následujícím událostem pro role prostředků Azure:

- Když je uživateli přiřazena role
- Po vypršení platnosti role uživatele
- Když je role uživatele rozšířena
- Po dokončení žádosti o aktivaci role uživatele

Následující text ukazuje ukázkový e-mail, který se odesílá, když je uživateli přiřazena role prostředku Azure pro fiktivní organizaci Contoso.

![Nový e-mail správy privilegovaných identit pro role prostředků Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role Azure AD ve správě privilegovaných identit](pim-how-to-change-default-settings.md)
- [Schválení nebo zamítnutí požadavků na role Azure AD ve správě privilegovaných identit](azure-ad-pim-approval-workflow.md)
