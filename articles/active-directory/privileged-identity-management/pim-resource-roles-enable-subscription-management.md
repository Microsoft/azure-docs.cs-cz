---
title: Privileged Identity Management pro prostředky Azure – povolení správy předplatného | Dokumentace Microsoftu
description: Zjistěte, jak globální správci můžou spravovat předplatná v tenantovi.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: protection
ms.date: 03/27/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: 6aeb82ff1feb3521f3a09dc1b28186754568bb27
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37442979"
---
# <a name="enable-subscription-management-in-your-tenant"></a>Povolení správy předplatného ve vašem tenantovi

Jako globální správce adresáře nemusí mít výchozí přístup ke všem prostředkům předplatné ve vašem tenantovi. Tento článek popisuje kroky, abyste měli přístup pro všechna předplatná v tenantovi. Také poskytuje doporučený postup pro zbývající splňovat všechny kontrolní mechanismy zabezpečení, které vaše organizace vyžaduje, jakmile se zobrazí přístup.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Kdo může povolit správu předplatných v mém adresáři?

Každý uživatel přiřazený k roli globálního správce musí podle následujících pokynů k povolení správy předplatného. Po povolení správy předplatných sami, můžete přidat další globální správce, kteří může být nutné prostředků i přístup. Neexistuje žádné nastavení adresáře, který umožňuje přístup pro všechny členy rolí globálního správce.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k webu Azure portal pomocí účtu, který je členem role Globální správce oprávněné nebo aktivní. Pokud je účet globálního správce, musíte nejprve aktivovat roli před přechodem k dalšímu kroku.

## <a name="access-the-azure-active-directory-admin-center"></a>Přístup k centru pro správu Azure Active Directory

Teď, když jste přihlášeni k webu Azure portal jako globální správce, můžete upravit nastavení, které poskytují přístup k předplatným Azure. Přejděte do centra pro správu Azure Active Directory (Azure AD) a vyberte **vlastnosti**.

![Snímek obrazovky s Azure AD, centra pro správu pomocí vlastnosti zvýrazněné](media/azure-pim-resource-rbac/aad_properties.png)

V seznamu vlastností v části **globální správce může spravovat předplatná Azure**vyberte **Ano**.

![Snímek obrazovky vlastností stránky s přepínací tlačítko, nastavte na Ano](media/azure-pim-resource-rbac/aad_properties_save.png)

Teď váš účet je automaticky přidán do role správce přístupu uživatelů pro každý prostředek předplatného v tenantovi.

## <a name="browse-to-azure-ad-pim"></a>Přejděte do Azure AD PIM

 Odtud přejdete na Azure AD Privileged Identity Management (PIM). V části **spravovat**vyberte **prostředky Azure**.

![Snímek obrazovky z PIM, se zvýrazněným prostředků Azure](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Správa a zjišťování prostředků

Pokud vaše organizace už používá Azure AD PIM k ochraně správců ve službě Azure AD, zobrazí se seznam předplatných, při načtení okna.

![Snímek obrazovky z PIM, se seznam předplatných, které jsou zobrazeny v okně](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Pokud se nezobrazí žádné prostředky, ověřte, že:
>- Nevyprší platnost vaší role globálního správce. 
>- Vaše organizace má předplatné Azure.

![Snímek obrazovky z PIM, se seznamem prázdný prostředků](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Konfigurace přiřazení

Vyberte předplatné, ze seznamu a přiřadit sami sebe (nebo skupinu, ve které jsou členy) jako oprávnění vlastníka prostředku. 
[Další informace o přiřazení rolí](pim-resource-roles-assign-roles.md).

Tento postup opakujte pro každý prostředek, než budete pokračovat k dalšímu kroku.

## <a name="clean-up-standing-access"></a>Odstraňte stávající přístup

Teď, když máte oprávněnými přiřazeními pro důležité předplatná ve vaší organizaci, můžete vyčistit neměli přímý přístup tím, že zakážete možnost ve vlastnosti adresáře.

![Snímek obrazovky vlastností stránky s přepnout nastavená na Ne](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Další postup

[Zjišťování prostředků](pim-resource-roles-discover-resources.md)

[Konfigurace nastavení role](pim-resource-roles-configure-role-settings.md)








