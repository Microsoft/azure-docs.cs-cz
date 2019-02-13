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
ms.subservice: pim
ms.date: 01/05/2019
ms.author: rolyon
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f4eab44a55574f84f29488e78cd60522d7a52eb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56171192"
---
# <a name="email-notifications-in-pim"></a>E-mailových oznámení v PIM

Azure AD Privileged Identity Management (PIM) vám umožňuje vědět, pokud důležité dojde k událostem, například když je role přiřazení nebo aktivaci. PIM zachová vaši informovanost odesláním hodnoty hash že můžete a ostatní účastníci e-mailová oznámení. Tyto e-maily může také zahrnovat odkazy na příslušné úlohy, tyto aktivace nebo obnovení role. Tento článek popisuje vypadat těchto e-mailů, kdy se odesílají a je obdrží.

## <a name="sender-email-address-and-subject-line"></a>Odesílatel e-mailové adresy a předmětu řádku

E-mailů odeslaných z PIM pro Azure AD a mají následující e-mailovou adresu odesílatele role prostředků Azure:

- E-mailová adresa:  **azure-noreply@microsoft.com**
- Zobrazovaný název: Microsoft Azure

Zahrnout tyto e-mailů **PIM** předponu v řádku předmětu. Tady je příklad:

- PIM: Alain Charon byl trvale přiřadit role Čtenář zálohování

## <a name="pim-emails-for-azure-ad-roles"></a>E-mailů PIM pro role Azure AD

PIM odešle e-mailů, když dojde k následujícím událostem pro role Azure AD:

- Pokud je aktivace privilegovaných rolí čekající na schválení
- Po dokončení požadavek aktivace privilegovaných rolí
- Pokud je povolená Azure AD PIM

Kdo přijímá tyto e-maily pro role Azure AD závisí na vaší roli, události a oznámení nastavení:

| Uživatel | Aktivace role čeká na schválení | Dokončit žádost o aktivaci role | Je povolené PIM |
| --- | --- | --- | --- |
| Správce privilegované role</br>(Aktivovat/vhodné) | Ano</br>(pouze pokud jsou zadány žádné explicitní schvalovatelům) | Ano* | Ano |
| Správce zabezpečení</br>(Aktivovat/vhodné) | Ne | Ano* | Ano |
| Globální správce</br>(Aktivovat/vhodné) | Ne | Ano* | Ano |

\* Pokud [ **oznámení** nastavení](pim-how-to-change-default-settings.md#notifications) je nastavena na **povolit**.

Následuje ukázkový e-mail, který se odešle, když uživatel aktivuje roli Azure AD pro fiktivní organizace Contoso.

![Nový e-mail PIM pro role Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-pim-digest-email-for-azure-ad-roles"></a>Týdenní e-mail digest PIM pro role Azure AD

Týdenní souhrn e-mail PIM pro role Azure AD posílá správce privilegovaných rolí, správci zabezpečení a globální správci, které jste povolili PIM. Tento týdenní e-mail obsahuje snímek aktivity PIM pro týden, stejně jako přiřazení privilegovaných rolí. Je dostupná jenom pro klienty ve veřejném cloudu. Tady je příklad e-mailu:

![Týdenní e-mail digest PIM pro role Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

E-mailu obsahuje čtyři dlaždice:

| Dlaždice | Popis |
| --- | --- |
| **Uživatelé aktivovat** | Počet pokusů uživatele aktivována jejich oprávněné role v tenantovi. |
| **Uživatelé, které jsou provedeny trvalé** | Počet uživatelů s oprávněného přiřazení se provádí trvalé. |
| **Přiřazení rolí v PIM** | Počet pokusů, které uživatelé jsou přiřazení oprávněné role v PIM. |
| **Přiřazení rolí mimo PIM** | Počet pokusů, které uživatelé jsou přiřazeni roli trvalé mimo PIM (v Azure AD). |

**Přehled hlavní role** části jsou uvedené nahoře pět rolí ve vašem tenantovi závislosti na celkovém počtu oprávněných a trvalých správců pro jednotlivé role. **Provést akci** odkaz otevře [PIM průvodce](pim-security-wizard.md) kde trvalí správci můžete převést na oprávněné správce v dávkách.

## <a name="pim-emails-for-azure-resource-roles"></a>E-mailů PIM pro role prostředků Azure

PIM odešle e-mailů vlastníci a správci přístupu uživatelů pro role prostředků Azure dojde k následujícím událostem:

- Při přiřazení role čeká na schválení
- Když se přiřazení role
- Když role je brzo vyprší
- Když má nárok na rozšíření role
- Když role obnovuje koncovým uživatelem
- Po dokončení žádost o aktivaci role

PIM odešle e-mailů koncovým uživatelům, když dojde k následujícím událostem pro role prostředků Azure:

- Když role je přiřazená uživateli
- Pokud je vypršel role uživatele
- Když je rozšířeno role uživatele
- Po dokončení žádost o aktivaci role uživatele

Následuje ukázkový e-mail, který se odešle, když uživatel má přiřazenou roli prostředků Azure pro fiktivní organizace Contoso.

![Nový e-mail PIM pro role prostředků Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Další postup

- [Konfigurace nastavení role adresáře Azure AD v PIM](pim-how-to-change-default-settings.md)
- [Schválit nebo zamítnout žádosti pro role adresáře Azure AD v PIM](azure-ad-pim-approval-workflow.md)
