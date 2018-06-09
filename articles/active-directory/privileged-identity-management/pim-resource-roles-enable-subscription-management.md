---
title: Správa privilegovaných identit pro prostředky Azure – povolit správu předplatné | Microsoft Docs
description: Zjistěte, jak globální správci můžete spravovat odběry v klientovi.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 628ee70f7eb59673d4229441e3c4242e1ef8e0d3
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/08/2018
ms.locfileid: "35234268"
---
# <a name="enable-subscription-management-in-your-tenant"></a>Povolení správy předplatného ve vašem klientovi

Jako globální správce adresáře nemusí mít výchozí přístup ke všem prostředkům předplatného ve vašem klientovi. Tento článek popisuje kroky, abyste měli přístup na všechny odběry ve vašem klientovi. Také poskytuje doporučený postup pro zbývající splňovat všechny prvky zabezpečení, které vaše organizace vyžaduje, jakmile se zobrazí přístup.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Kdo může povolit správu odběry ve adresáře Moje?

Každý uživatel přiřazený k roli globálního správce musí použijte následující postup k povolení správy předplatného. Po povolení správy předplatného sami, můžete přidat další globální správce, kteří může být nutné prostředků také přístup. Neexistuje žádné nastavení adresář, který umožňuje přístup pro všechny členy rolí globálního správce.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k portálu Azure pomocí účtu, který je členem role Globální správce oprávněné nebo active. Pokud účet, je vhodné globální správce, musíte nejprve aktivovat roli než budete pokračovat k dalšímu kroku.

## <a name="access-the-azure-active-directory-admin-center"></a>Přístup k centru pro správu Azure Active Directory

Teď, když jste přihlášení k portálu Azure jako globální správce, můžete upravit nastavení, které poskytují přístup k předplatným služby Azure. Přejděte do centra pro správu Azure Active Directory (Azure AD) a vyberte **vlastnosti**.

![Centrum pro správu – snímek obrazovky Azure AD, se zvýrazněnou vlastnosti](media/azure-pim-resource-rbac/aad_properties.png)

V seznamu vlastností v části **globálního správce může spravovat předplatná Azure**, vyberte **Ano**.

![Snímek obrazovky vlastností stránky s přepnutí nastavenou na Ano](media/azure-pim-resource-rbac/aad_properties_save.png)

Teď váš účet je automaticky přidán do role správce přístupu uživatelů pro každé předplatné prostředek v klientovi.

## <a name="browse-to-azure-ad-pim"></a>Přejděte do Azure AD PIM

 Z tohoto místa přejděte na Azure AD Privileged Identity Management (PIM). V části **spravovat**, vyberte **prostředky Azure**.

![Snímek obrazovky z PIM s prostředky Azure zvýrazněná](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Spravovat a zjišťovat prostředky

Pokud vaše organizace už používá Azure AD PIM k ochraně správci ve službě Azure AD, zobrazí se seznam předplatných, až se načte v okně.

![Snímek obrazovky z PIM s seznamu předplatných zobrazí v okně](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Pokud nevidíte všechny prostředky, potvrďte, že:
>- Vaše role Globální správce nevyprší platnost. 
>- Vaše organizace nemá předplatné služby Azure.

![Snímek obrazovky z PIM s seznam prázdný prostředků](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Konfigurace přiřazení

Zvolte předplatné ze seznamu a přiřaďte sami (nebo skupiny, jejichž jste členem) jako oprávněné vlastníka prostředku. 
[Další informace o přiřazení rolí](pim-resource-roles-assign-roles.md).

Tento postup opakujte pro každý prostředek, než budete pokračovat k dalšímu kroku.

## <a name="clean-up-standing-access"></a>Vyčištění stálý přístup

Teď, když máte nárok přiřazení pro důležité odběry ve vaší organizaci, můžete vyčistit stálý přístup tím, že zakážete možnost ve vlastnostech adresáře.

![Snímek obrazovky vlastností stránky s přepnutí nastavena na Ne](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Další postup

[Zjišťování prostředků](pim-resource-roles-discover-resources.md)

[Konfigurace nastavení role](pim-resource-roles-configure-role-settings.md)








