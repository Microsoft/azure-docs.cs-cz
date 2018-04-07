---
title: Privileged Identity Management pro prostředky Azure – povolit správu předplatné | Microsoft Docs
description: Zjistěte, jak globální správci můžou spravovat předplatná v klientovi.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/27/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 4c6ae3da34fe5157314b8ea422591f7ecbd2a667
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="enable-subscription-management"></a>Povolení správy předplatného

Jako globální správce adresáře nemusí mít výchozí přístup ke všem prostředkům předplatného ve vašem klientovi. Tento článek se popisují kroky, abyste měli přístup na všechny odběry v klientovi a doporučený postup pro zbývající splňovat všechny ovládací prvky zabezpečení, které vaše organizace vyžaduje po přijetí přístup.

## <a name="who-can-enable-management-of-subscriptions-in-my-directory"></a>Kdo může povolit správu odběry ve adresáře Moje?

Každý uživatel přiřazený k roli globálního správce musí použijte následující postup k povolení správy předplatného. Jakmile povolíte správu předplatného pro sami, můžete přidat další globální správce, které může být nutné také přístup k prostředkům. Neexistuje žádné nastavení adresář, který umožňuje přístup pro všechny členy rolí globálního správce.

## <a name="log-on-to-the-azure-portal"></a>Přihlaste se k portálu Azure

Začněte tím, že přihlášení na portálu Azure pomocí účtu, který je členem role Globální správce oprávněné nebo active. Pokud účet, je vhodné globální správce, je nutné nejprve aktivovat roli, než budete pokračovat k dalšímu kroku.

## <a name="access-the-azure-ad-admin-center"></a>Přístup k centru pro správu Azure AD

Teď, když jste se přihlásili do portálu Azure jako globální správce můžete upravit nastavení, která zajistí přístup k předplatným Azure. Přejděte do centra pro správu Azure AD, vyhledejte a vyberte na kartě Vlastnosti v levém navigačním panelu.

![](media/azure-pim-resource-rbac/aad_properties.png)

V seznamu vlastností přepněte možnost s názvem "globálního správce může spravovat předplatná Azure" na "Ano".

![](media/azure-pim-resource-rbac/aad_properties_save.png)

## <a name="navigate-to-azure-ad-pim"></a>Přejděte na Azure AD PIM

Tato možnost povolena je váš účet automaticky přidán k roli "Správce uživatelského přístupu" pro každé předplatné prostředek v klientovi. Z tohoto místa přejděte na Azure AD PIM a vyberte prostředky Azure v části Správa levé navigaci.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resources.png)

## <a name="manage-and-discover-resources"></a>Spravovat a zjišťovat prostředky

Pokud vaše organizace už používá Azure AD PIM k ochraně správci ve službě Azure Active Directory zobrazí se seznam předplatných, až se načte v okně.

![](media/azure-pim-resource-rbac/aadpim_manage_azure_resource_some_there.png)

> [!NOTE]
> Pokud nevidíte všechny prostředky, zkontrolujte:
>- Vaše role Globální správce nevyprší platnost. 
>- Má vaše organizace předplatné Azure

![](media/azure-pim-resource-rbac/aadpim_rbac_empty_resource_list.png)

## <a name="configure-assignments"></a>Konfigurace přiřazení

Zvolte předplatné ze seznamu a přiřaďte sami (nebo skupiny, jejichž jste členem) jako oprávněné vlastníka prostředku. 
[Další informace o přiřazení rolí](pim-resource-roles-assign-roles.md).

Tento postup opakujte pro každý prostředek, než budete pokračovat k dalšímu kroku.

## <a name="clean-up-standing-access"></a>Vyčištění stálý přístup

Teď, když máte nárok přiřazení pro důležité odběry ve vaší organizaci, můžete vyčistit stálý přístup tím, že zakážete možnost ve vlastnostech adresáře:

![](media/azure-pim-resource-rbac/aad_properties_no.png)

## <a name="next-steps"></a>Další postup

[Zjišťovat prostředky](pim-resource-roles-discover-resources.md)

[Konfigurace nastavení role](pim-resource-roles-configure-role-settings.md)








