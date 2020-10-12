---
title: Konfigurace vlastní role Azure AD – Privileged Identity Management (PIM)
description: Jak nakonfigurovat vlastní role Azure AD v Privileged Identity Management (PIM)
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/06/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8745482ed551108044bbe5e2b2f191ad46d7a441
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91538443"
---
# <a name="configure-azure-ad-custom-roles-in-privileged-identity-management"></a>Konfigurace vlastních rolí Azure AD v Privileged Identity Management

Správce privilegovaných rolí může změnit nastavení role, která platí pro uživatele, když aktivují jejich přiřazení k vlastní roli a dalším správcům aplikací, kteří přiřazují vlastní role.

> [!NOTE]
> Vlastní role Azure AD nejsou integrovány s předdefinovanými rolemi adresáře ve verzi Preview. Jakmile je funkce všeobecně dostupná, bude se Správa rolí konat v prostředí integrovaných rolí. Pokud se zobrazí následující nápis, tyto role by se měly spravovat [v prostředí integrovaných rolí](pim-how-to-activate-role.md) a tento článek neplatí:
>
> [![Vyberte Azure AD > Privileged Identity Management](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

## <a name="open-role-settings"></a>Otevřít nastavení role

Pomocí těchto kroků otevřete nastavení role Azure AD.

1. Přihlaste se k [Privileged Identity Management](https://portal.azure.com/?Microsoft_AAD_IAM_enableCustomRoleManagement=true&Microsoft_AAD_IAM_enableCustomRoleAssignment=true&feature.rbacv2roles=true&feature.rbacv2=true&Microsoft_AAD_RegisteredApps=demo#blade/Microsoft_Azure_PIMCommon/CommonMenuBlade/quickStart) v Azure Portal pomocí uživatelského účtu, který je přiřazený k roli správce privilegované role.
1. Vyberte **vlastní role Azure AD (Preview)**.

    ![Výběrem vlastní role Azure AD ve verzi Preview zobrazíte oprávněná přiřazení rolí.](./media/azure-ad-custom-roles-configure/settings-list.png)

1. Výběrem **Nastavení** otevřete stránku **Nastavení** . Vyberte roli pro nastavení, které chcete konfigurovat.
1. Výběrem možnosti **Upravit** otevřete stránku **Nastavení rolí** .

    ![Snímek obrazovky zobrazující stránku podrobností nastavení role se zvolenou akcí upravit](./media/azure-ad-custom-roles-configure/edit-settings.png)

## <a name="role-settings"></a>Nastavení role

Existuje několik nastavení, která můžete konfigurovat.

### <a name="assignment-duration"></a>Doba trvání přiřazení

Při konfiguraci nastavení role můžete vybrat ze dvou možností trvání přiřazení pro každý typ přiřazení (způsobilý nebo aktivní). Tyto možnosti se stanou výchozím maximálním trváním, když je člen přiřazen k roli v Privileged Identity Management.

Můžete vybrat jednu z těchto možností s *nárokem* na trvání přiřazení.

- **Povolení trvalého oprávněného přiřazení**: Správci můžou přiřadit trvalé oprávněné členství.
- **Vypršení platnosti oprávněného přiřazení**: Správci můžou vyžadovat, aby u všech oprávněných přiřazení bylo zadané počáteční a koncové datum.

Můžete také zvolit jednu z těchto možností trvání *aktivních* přiřazení:

- **Povolení trvalého aktivního přiřazení**: Správci můžou přiřadit trvalé aktivní členství.
- **Ukončit aktivní přiřazení po**: Správci můžou vyžadovat, aby u všech aktivních přiřazení bylo zadané počáteční a koncové datum.

### <a name="require-azure-multi-factor-authentication"></a>Vyžadování služby Azure Multi-Factor Authentication

Privileged Identity Management poskytuje volitelnou vynucování Multi-Factor Authentication Azure pro dva různé scénáře.

- **Vyžadovat Multi-Factor Authentication aktivnímu přiřazení**

  Pokud chcete členům přiřadit jenom krátkou dobu trvání (jeden den, například), může být příliš pomalý a vyžadovat, aby přiřazená členové požádali o aktivaci. V tomto scénáři Privileged Identity Management nemůžou vymáhat ověřování službou Multi-Factor Authentication, když uživatel aktivuje přiřazení role, protože už jsou v roli aktivní od okamžiku, kdy jsou přiřazeni. Aby se zajistilo, že správce naplní přiřazení, považuje je za to, že jsou vydávají, vyberte pole **vyžadovat Multi-Factor Authentication v aktivním přiřazení** .

- **Vyžadovat Multi-Factor Authentication při aktivaci**

  Můžete vyžadovat, aby se oprávnění uživatelé přiřazení k roli zaregistrovali v Azure Multi-Factor Authentication předtím, než se můžou aktivovat. Tento proces zajišťuje, že uživatel žádající o aktivaci je tím, kdo říkají, že má rozumnou jistotu. Vynucování této možnosti chrání důležité role v situacích, kdy mohlo dojít k ohrožení uživatelského účtu. Pro vyžadování oprávněného člena pro spuštění služby Azure Multi-Factor Authentication před aktivací zaškrtněte políčko **vyžadovat Multi-Factor Authentication na aktivaci** .

Další informace naleznete v tématu [Multi-Factor Authentication a Privileged Identity Management](pim-how-to-require-mfa.md).

### <a name="activation-maximum-duration"></a>Maximální doba trvání aktivace

Pomocí posuvníku **maximální doby trvání aktivace** můžete nastavit maximální dobu v hodinách, po kterou role zůstane aktivní, než vyprší její platnost. Tato hodnota může být v rozmezí od 1 do 24 hodin.

### <a name="require-justification"></a>Vyžadovat odůvodnění

Můžete požadovat, aby členové zadali odůvodnění aktivní přiřazení nebo když se aktivují. Chcete-li vyžadovat odůvodnění, zaškrtněte políčko **vyžadovat odůvodnění při aktivním přiřazení** nebo políčko **vyžadovat odůvodnění při aktivaci** .

### <a name="require-approval-to-activate"></a>Vyžadovat schválení pro aktivaci

Pokud chcete pro aktivaci role vyžadovat schválení, postupujte podle těchto kroků.

1. Zaškrtněte políčko **vyžadovat schválení k aktivaci** .
1. Vyberte **Vybrat schvalovatelé** a otevřete seznam **Vybrat člena nebo skupinu** .

    ![Otevření vlastní role Azure AD pro úpravu nastavení](./media/azure-ad-custom-roles-configure/select-approvers.png)

1. Vyberte aspoň jednoho člena nebo skupinu a potom klikněte na **Vybrat**. Musíte vybrat aspoň jednoho schvalovatele. Neexistují žádní výchozí schvalovatelé. Vaše výběry se zobrazí v seznamu vybraných schvalovatelů.
1. Po zadání nastavení role uložte změny výběrem možnosti **aktualizovat** .

## <a name="next-steps"></a>Další kroky

- [Aktivace vlastní role Azure AD](azure-ad-custom-roles-activate.md)
- [Přiřazení vlastní role Azure AD](azure-ad-custom-roles-assign.md)
- [Odebrání nebo aktualizace přiřazení vlastní role Azure AD](azure-ad-custom-roles-update-remove.md)
- [Definice rolí v Azure AD](../users-groups-roles/directory-assign-admin-roles.md)
