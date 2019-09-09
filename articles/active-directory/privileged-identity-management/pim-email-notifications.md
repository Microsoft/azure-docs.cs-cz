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
ms.openlocfilehash: f0db4828ef7a6d3392ce53b2f2f95b0f7f014d65
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804539"
---
# <a name="email-notifications-in-pim"></a>E-mailová oznámení v PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) vám umožní zjistit, kdy dochází k důležitým událostem, například při přiřazení nebo aktivaci role. PIM vás informuje o tom, že vám pošle e-mailová oznámení a další účastníky. Tyto e-maily můžou zahrnovat také odkazy na relevantní úkoly, jako je aktivace nebo obnova role. Tento článek popisuje, co tyto e-maily vypadají jako při jejich posílání a kdo je přijímá.

## <a name="sender-email-address-and-subject-line"></a>E-mailová adresa odesílatele a řádek předmětu

E-maily odeslané z PIM pro role prostředků Azure AD a Azure mají následující e-mailovou adresu odesílatele:

- E-mailová adresa: **Azure\@-Microsoft.com Reply**
- Zobrazovaný název: Microsoft Azure

Tyto e-maily obsahují předponu **PIM** v řádku předmětu. Tady je příklad:

- PIM: Alain Charon se trvale přiřadila k roli čtečky zálohování.

## <a name="pim-emails-for-azure-ad-roles"></a>PIM e-maily pro role Azure AD

PIM posílá e-maily, když dojde k následujícím událostem pro role Azure AD:

- Když je aktivace privilegované role čeká na schválení
- Po dokončení žádosti o aktivaci privilegované role
- Když je povolená služba Azure AD PIM

Kdo obdrží tyto e-maily pro role Azure AD, závisí na vaší roli, události a nastavení oznámení:

| Uživatel | Aktivace role čeká na schválení. | Žádost o aktivaci role je dokončená. | PIM je povolený. |
| --- | --- | --- | --- |
| Správce privilegované role</br>(Aktivováno/způsobilo) | Ano</br>(pouze pokud nejsou zadáni žádní explicitní schvalovatelé) | Ano* | Ano |
| Správce zabezpečení</br>(Aktivováno/způsobilo) | Ne | Ano* | Ano |
| Globální správce</br>(Aktivováno/způsobilo) | Ne | Ano* | Ano |

\*Pokud je [nastavení **oznámení** ](pim-how-to-change-default-settings.md#notifications) nastaveno na **Povolit**.

Níže vidíte příklad e-mailu, který se pošle, když uživatel aktivuje roli Azure AD pro fiktivní organizaci Contoso.

![Nový e-mail PIM pro role Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Týdenní e-mail PIM Digest pro role Azure AD

Týdenní souhrnný e-mail PIM pro role Azure AD se pošle správcům privilegovaných rolí, správcům zabezpečení a globálním správcům s povoleným PIM. Tento týdenní e-mail poskytuje snímek aktivit PIM pro týden i přiřazení privilegovaných rolí. Je k dispozici pouze pro klienty ve veřejném cloudu. Tady je příklad e-mailu:

![Týdenní e-mail PIM Digest pro role Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

E-mail obsahuje čtyři dlaždice:

| Dlaždice | Popis |
| --- | --- |
| **Aktivované uživatelé** | Počet, kolikrát uživatelé aktivovali svou oprávněnou roli v rámci tenanta. |
| **Uživatelé, kteří udělali trvalé** | Počet, kolikrát se uživatelé s oprávněným přiřazením udělali jako trvalé |
| **Přiřazení rolí v PIM** | Počet, kolikrát se uživatelům přiřadí oprávněná role v rámci PIM. |
| **Přiřazení rolí mimo PIM** | Počet, kolikrát se uživatelům přiřadí trvalá role mimo PIM (v rámci Azure AD). |

V části **Přehled hlavních rolí** najdete seznam pěti rolí ve vašem tenantovi na základě celkového počtu trvalých a oprávněných správců pro jednotlivé role. Odkaz **Přijmout akci** otevře [Průvodce PIM](pim-security-wizard.md) , kde můžete převést trvalé správce na oprávněné správce v dávkách.

## <a name="pim-emails-for-azure-resource-roles"></a>PIM – e-maily pro role prostředků Azure

PIM posílá e-maily vlastníkům a správcům přístupu uživatelů, pokud se pro role prostředků Azure vyskytnou tyto události:

- Když přiřazení role čeká na schválení
- Při přiřazení role
- Když brzo vyprší platnost role
- Když je role oprávněná k rozšiřování
- Při obnovování role koncovým uživatelem
- Po dokončení žádosti o aktivaci role

PIM odesílá e-maily koncovým uživatelům, když dojde k těmto událostem pro role prostředků Azure:

- Když je role přiřazena uživateli
- Po vypršení platnosti role uživatele
- Když je rozšířená role uživatele
- Po dokončení žádosti o aktivaci role uživatele

Níže vidíte příklad e-mailu, který se pošle, když se uživateli přiřadí role prostředku Azure pro fiktivní organizaci Contoso.

![Nový e-mail PIM pro role prostředků Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role Azure AD v PIM](pim-how-to-change-default-settings.md)
- [Schválení nebo zamítnutí žádostí o role Azure AD v PIM](azure-ad-pim-approval-workflow.md)
