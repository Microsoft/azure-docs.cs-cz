---
title: E-mailová oznámení v PIM – Azure | Dokumentace Microsoftu
description: Popisuje e-mailových oznámení v Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: pim
ms.date: 07/24/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: 1a1635e0ba16bdc0fa6dc90aa0e1a417f43e04ac
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43190172"
---
# <a name="email-notifications-in-pim"></a>E-mailových oznámení v PIM

Po události kláves se generují v Azure AD Privileged Identity Management (PIM), e-mailová oznámení se odesílají na příslušné správce nebo uživatele. Například PIM odešle e-mailů pro následující události:

- Pokud je aktivace privilegovaných rolí čekající na schválení
- Při schválení žádosti aktivace privilegovaných rolí
- Když se aktivuje privilegovanou roli
- Když se přiřazení privilegovaných rolí
- Pokud je povolená Azure AD PIM

Cena začíná na konci. července 2018, budou mít e-mailová oznámení odesílat prostřednictvím PIM novou e-mailovou adresu odesílatele a nové vizuální návrh. Tato aktualizace ovlivní obě PIM pro Azure AD a PIM pro prostředky Azure. Všechny události, které dříve aktivovaly e-mailové oznámení bude nadále rozešle e-mail. Některé e-mailů bude aktualizovaný obsah poskytování více cílových informací.

## <a name="sender-email-address"></a>E-mailová adresa odesílatele

E-mailová oznámení od na konci. července 2018 se mají následující adresu:

- E-mailová adresa:  **azure-noreply@microsoft.com**
- Zobrazovaný název: Microsoft Azure

Dříve musely e-mailová oznámení na následující adresu:

- E-mailová adresa:  **azureadnotifications@microsoft.com**
- Zobrazovaný název: Služba oznámení Microsoft Azure AD

## <a name="email-subject-line"></a>Řádek předmětu e-mailu

Spouští se na konci z července 2018 se e-mailová oznámení pro Azure AD a budou mít role prostředků Azure **PIM** předponu v řádku předmětu. Tady je příklad:

- PIM: Alain Charon byl trvale přiřadit role Čtenář zálohování.

## <a name="pim-emails-for-azure-ad-roles"></a>E-mailů PIM pro role Azure AD

Spouští se na konci. července 2018, e-mailová oznámení PIM pro role Azure AD mají aktualizovaný návrh. Následuje ukázkový e-mail, který se odešle, když uživatel aktivuje privilegovaných rolí pro fiktivní organizace Contoso.

![Nový e-mail PIM pro role Azure AD](./media/pim-email-notifications/email-directory-new.png)

Dříve když uživatel aktivovat privilegovanou roli, e-mailu vypadal následující.

![Starý e-mail PIM pro role Azure AD](./media/pim-email-notifications/email-directory-old.png)

## <a name="pim-emails-for-azure-resource-roles"></a>E-mailů PIM pro role prostředků Azure

Spouští se na konci. července 2018, e-mailová oznámení PIM pro role prostředků Azure mají aktualizovaný návrh. Následuje ukázkový e-mail, který se odešle, když je uživatel přiřazenou privilegovanou roli pro fiktivní organizace Contoso.

![Nový e-mail PIM pro role prostředků Azure](./media/pim-email-notifications/email-resources-new.png)

Dříve Pokud má uživatel přiřazenou privilegovanou roli, e-mailu vypadal následující.

![Starý e-mail PIM pro role prostředků Azure](./media/pim-email-notifications/email-resources-old.png)

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role adresáře Azure AD v PIM](pim-how-to-change-default-settings.md)
- [Schválit nebo zamítnout žádosti pro role adresáře Azure AD v PIM](azure-ad-pim-approval-workflow.md)
