---
title: E-mailová oznámení v PIM-Azure Active Directory | Microsoft Docs
description: Popisuje e-mailová oznámení v Azure AD Privileged Identity Management (PIM).
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
ms.date: 01/05/2019
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee5f2edbae28276f8485ae774a5b1c52e1af2fd1
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756385"
---
# <a name="email-notifications-in-pim"></a>E-mailová oznámení v PIM

Privileged Identity Management (PIM) vám umožní zjistit, kdy se v organizaci Azure Active Directory (Azure AD) vyskytují důležité události, například když je přiřazena nebo aktivovaná role. Privileged Identity Management vás informují o tom, že vám pošle e-mailová oznámení a další účastníky. Tyto e-maily můžou zahrnovat také odkazy na relevantní úkoly, jako je aktivace nebo obnova role. Tento článek popisuje, co tyto e-maily vypadají jako při jejich posílání a kdo je přijímá.

## <a name="sender-email-address-and-subject-line"></a>E-mailová adresa odesílatele a řádek předmětu

E-maily odeslané z Privileged Identity Management pro role prostředků Azure AD a Azure mají následující e-mailovou adresu odesílatele:

- E-mailová adresa: **Azure-inreply \@microsoft. com**
- Zobrazovaný název: Microsoft Azure

Tyto e-maily obsahují předponu **PIM** v řádku předmětu. Tady je příklad:

- PIM: Alain Charon byl trvale přiřazen k roli čtečky zálohování.

## <a name="notifications-for-azure-ad-roles"></a>Oznámení pro role Azure AD

Privileged Identity Management posílá e-maily, pokud dojde k následujícím událostem pro role Azure AD:

- Když je aktivace privilegované role čeká na schválení
- Po dokončení žádosti o aktivaci privilegované role
- Když je povolená Azure AD Privileged Identity Management

Kdo obdrží tyto e-maily pro role Azure AD, závisí na vaší roli, události a nastavení oznámení:

| Uživatel | Aktivace role čeká na schválení. | Žádost o aktivaci role je dokončená. | PIM je povolený. |
| --- | --- | --- | --- |
| Správce privilegovaných rolí</br>(Aktivováno/způsobilo) | Ano</br>(pouze pokud nejsou zadáni žádní explicitní schvalovatelé) | Ano* | Ano |
| Správce zabezpečení</br>(Aktivováno/způsobilo) | Ne | Ano* | Ano |
| Globální správce</br>(Aktivováno/způsobilo) | Ne | Ano* | Ano |

\*, pokud je [nastavení **oznámení** ](pim-how-to-change-default-settings.md#notifications) nastaveno na hodnotu **Povolit**.

Níže vidíte příklad e-mailu, který se pošle, když uživatel aktivuje roli Azure AD pro fiktivní organizaci Contoso.

![Nový e-mailová Privileged Identity Management pro role Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>E-mail s Privileged Identity Management hodnotou Digest pro role Azure AD

Týdenní Privileged Identity Management souhrnný e-mail pro role Azure AD se pošle správcům privilegovaných rolí, správcům zabezpečení a globálním správcům, kteří povolili Privileged Identity Management. Tento týdenní e-mail poskytuje snímek Privileged Identity Management aktivity pro týden a také přiřazení privilegovaných rolí. Je k dispozici pouze pro klienty ve veřejném cloudu. Tady je příklad e-mailu:

![E-mail s Privileged Identity Management hodnotou Digest pro role Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

E-mail obsahuje čtyři dlaždice:

| Podobě | Popis |
| --- | --- |
| **Aktivované uživatelé** | Počet, kolikrát uživatelé aktivovali svou oprávněnou roli v rámci tenanta. |
| **Uživatelé, kteří udělali trvalé** | Počet, kolikrát se uživatelé s oprávněným přiřazením udělali jako trvalé |
| **Přiřazení rolí v Privileged Identity Management** | Počet, kolikrát se uživatelům přiřadí oprávněná role v Privileged Identity Management. |
| **Přiřazení rolí mimo PIM** | Počet, kolikrát se uživatelům přiřadí trvalá role mimo Privileged Identity Management (uvnitř Azure AD). |

V části **Přehled hlavních rolí** najdete seznam pěti rolí ve vašem tenantovi na základě celkového počtu trvalých a oprávněných správců pro jednotlivé role. Odkaz **Přijmout akci** otevře [Průvodce PIM](pim-security-wizard.md) , kde můžete převést trvalé správce na oprávněné správce v dávkách.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM – e-maily pro role prostředků Azure

Privileged Identity Management posílá e-maily vlastníkům a správcům přístupu uživatelů při výskytu následujících událostí pro role prostředků Azure:

- Když přiřazení role čeká na schválení
- Při přiřazení role
- Když brzo vyprší platnost role
- Když je role oprávněná k rozšiřování
- Při obnovování role koncovým uživatelem
- Po dokončení žádosti o aktivaci role

Privileged Identity Management odesílá e-maily koncovým uživatelům, když dojde k těmto událostem pro role prostředků Azure:

- Když je role přiřazena uživateli
- Po vypršení platnosti role uživatele
- Když je rozšířená role uživatele
- Po dokončení žádosti o aktivaci role uživatele

Níže vidíte příklad e-mailu, který se pošle, když se uživateli přiřadí role prostředku Azure pro fiktivní organizaci Contoso.

![Nový e-mailová Privileged Identity Management pro role prostředků Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Další kroky

- [Konfigurace nastavení role Azure AD v Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Schválení nebo zamítnutí žádostí o role Azure AD v Privileged Identity Management](azure-ad-pim-approval-workflow.md)
