---
title: Konfigurace vlastních rolí Azure AD v Privileged Identity Management (PIM) | Microsoft Docs
description: Jak nakonfigurovat vlastní role Azure AD v Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc7e52a2ef92120da60f085c0afebeb8af714203
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947356"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Konfigurace vlastních rolí Azure AD v Privileged Identity Management

Správce privilegovaných rolí může změnit nastavení role, která platí pro uživatele, když aktivují jejich přiřazení k vlastní roli a dalším správcům aplikací, kteří přiřazují vlastní role.

## <a name="open-role-settings"></a>Otevřít nastavení role

Pomocí těchto kroků otevřete nastavení role Azure AD.

1. Přihlaste se k [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) v Azure Portal pomocí uživatelského účtu, který je přiřazený k roli správce privilegované role.
1. Vyberte **vlastní role Azure AD (Preview)** .

    ![Výběrem vlastní role Azure AD ve verzi Preview zobrazíte oprávněná přiřazení rolí.](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Výběrem **Nastavení** otevřete stránku **Nastavení** . Vyberte roli pro nastavení, které chcete konfigurovat.
1. Výběrem možnosti **Upravit** otevřete stránku **Nastavení rolí** .

    ![Otevření vlastní role Azure AD pro úpravu nastavení](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Nastavení role

Existuje několik nastavení, která můžete konfigurovat.

### <a name="assignment-duration"></a>Doba trvání přiřazení

Při konfiguraci nastavení role můžete vybrat ze dvou možností trvání přiřazení pro každý typ přiřazení (způsobilý nebo aktivní). Tyto možnosti se stanou výchozím maximálním trváním, když je člen přiřazen k roli v Privileged Identity Management.

Můžete vybrat jednu z těchto možností s nárokem na trvání přiřazení.

- **Povolení trvalého oprávněného přiřazení**: Správci můžou přiřadit trvalé oprávněné členství.
- **Platnost oprávněného přiřazení vyprší po**: Správci mohou vyžadovat, aby u všech oprávněných přiřazení bylo zadané počáteční a koncové datum.

Můžete také zvolit jednu z těchto možností trvání *aktivních* přiřazení:

- **Povoluje trvalé aktivní přiřazení**: Správci můžou přiřadit trvalé aktivní členství.
- **Po vypršení platnosti aktivního přiřazení**: Správci mohou vyžadovat, aby u všech aktivních přiřazení bylo zadané počáteční a koncové datum.

### <a name="require-azure-multi-factor-authentication"></a>Vyžadovat Azure Multi-Factor Authentication

Privileged Identity Management poskytuje volitelnou vynucování Azure Multi-Factor Authentication pro dva různé scénáře.

- **Vyžadovat službu Multi-Factor Authentication u aktivního přiřazení**

  Pokud chcete členům přiřadit jenom krátkou dobu trvání (jeden den, například), může být příliš pomalý a vyžadovat, aby přiřazená členové požádali o aktivaci. V tomto scénáři Privileged Identity Management nemůžou vymáhat ověřování službou Multi-Factor Authentication, když uživatel aktivuje přiřazení role, protože už jsou v roli aktivní od okamžiku, kdy jsou přiřazeni. Aby bylo zajištěno, že správce naplní přiřazení, považuje je za to, že jsou vysloví, vyberte možnost **vyžadovat vícefaktorové ověřování v aktivním poli přiřazení** .

- **Vyžadovat vícefaktorové ověřování při aktivaci**

  Aby bylo možné provést registraci do služby Azure Multi-Factor Authentication, můžete vyžadovat, aby se oprávněným uživatelům přidělila role. Tento proces zajišťuje, že uživatel žádající o aktivaci je tím, kdo říkají, že má rozumnou jistotu. Vynucování této možnosti chrání důležité role v situacích, kdy mohlo dojít k ohrožení uživatelského účtu. Pro vyžadování oprávněného člena pro spuštění služby Azure Multi-Factor Authentication před aktivací zaškrtněte políčko **vyžadovat vícefaktorové ověřování při aktivaci** .

Další informace naleznete v tématu [Multi-Factor Authentication a Privileged Identity Management](pim-how-to-require-mfa.md).

### <a name="activation-maximum-duration"></a>Maximální doba trvání aktivace

Pomocí posuvníku **maximální doby trvání aktivace** můžete nastavit maximální dobu v hodinách, po kterou role zůstane aktivní, než vyprší její platnost. Tato hodnota může být v rozmezí od 1 do 24 hodin.

### <a name="require-justification"></a>Vyžadovat odůvodnění

Můžete požadovat, aby členové zadali odůvodnění aktivní přiřazení nebo když se aktivují. Chcete-li vyžadovat odůvodnění, zaškrtněte políčko **vyžadovat odůvodnění při aktivním přiřazení** nebo políčko **vyžadovat odůvodnění při aktivaci** .

### <a name="require-approval-to-activate"></a>K aktivaci vyžadovat schválení

Pokud chcete pro aktivaci role vyžadovat schválení, postupujte podle těchto kroků.

1. Zaškrtněte políčko **vyžadovat schválení k aktivaci** .
1. Vyberte **Vybrat schvalovatelé** a otevřete seznam **Vybrat člena nebo skupinu** .

    ![Otevření vlastní role Azure AD pro úpravu nastavení](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Vyberte aspoň jednoho člena nebo skupinu a potom klikněte na **Vybrat**. Musíte vybrat aspoň jednoho schvalovatele. Neexistují žádní výchozí schvalovatelé. Vaše výběry se zobrazí v seznamu vybraných schvalovatelů.
1. Po zadání nastavení role uložte změny výběrem možnosti **aktualizovat** .

## <a name="next-steps"></a>Další postup

- [Aktivace vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Přiřazení vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Odebrání nebo aktualizace přiřazení vlastní role Azure AD](azure-ad-custom-roles-update-remove.md)
- [Definice rolí v Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
