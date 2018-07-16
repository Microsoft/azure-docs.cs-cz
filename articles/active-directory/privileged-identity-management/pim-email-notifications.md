---
title: E-mailová oznámení v Azure AD PIM | Dokumentace Microsoftu
description: Popisuje e-mailových oznámení v Azure AD Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: protection
ms.date: 07/14/2018
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.openlocfilehash: 6c329554b5854f113fb216f874fa5a918110f9c5
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39059778"
---
# <a name="email-notifications-in-azure-ad-pim"></a>E-mailových oznámení v Azure AD PIM

Po události kláves se generují v Azure AD Privileged Identity Management (PIM), e-mailová oznámení se odesílají na příslušné správce nebo uživatele. Například PIM odešle e-mailů pro následující události:

- Pokud je aktivace privilegovaných rolí čekající na schválení
- Při schválení žádosti aktivace privilegovaných rolí
- Když se aktivuje privilegovanou roli
- Když se přiřazení privilegovaných rolí
- Pokud je povolená Azure AD PIM

Cena začíná na konci. července 2018, budou mít e-mailová oznámení odesílat prostřednictvím PIM novou e-mailovou adresu odesílatele a nové vizuální návrh. Tato aktualizace ovlivní obě PIM pro Azure AD a PIM pro prostředky Azure. Všechny události, které dříve aktivovaly e-mailové oznámení bude nadále rozešle e-mail. Tato aktualizace je výhradně vizuální změny pomocí funkce nebyly provedeny žádné změny.

## <a name="sender-email-address"></a>E-mailová adresa odesílatele

E-mailová oznámení od na konci. července 2018 se mají následující adresu:

- E-mailová adresa:  **azure-noreply@microsoft.com**
- Zobrazovaný název: Microsoft Azure

Dříve musely e-mailová oznámení na následující adresu:

- E-mailová adresa:  **azureadnotifications@microsoft.com**
- Zobrazovaný název: Služba oznámení Microsoft Azure AD

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

- [Jak spravovat nastavení aktivace rolí v Azure AD PIM](pim-how-to-change-default-settings.md)
- [Schválení v Azure AD PIM](azure-ad-pim-approval-workflow.md)
