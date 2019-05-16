---
title: Přidání názvů rychlý start zásady pro skupiny Office 365 – Azure Active Directory | Dokumentace Microsoftu
description: Tento článek vysvětluje, jak ve službě Azure Active Directory přidat nové uživatele nebo odstranit existující uživatele.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: quickstart
ms.date: 04/24/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0e600204479bc54a590df6bf1bbcd634eaac7fc
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65605639"
---
# <a name="quickstart-naming-policy-for-groups-in-azure-active-directory"></a>Rychlý start: Pojmenování zásady pro skupiny ve službě Azure Active Directory

V tomto rychlém startu nastavíte ve svém tenantovi Azure Active Directory (Azure AD) zásady pojmenování pro skupiny Office 365 vytvořené uživatelem, které vám pomůžou řadit a prohledávat skupiny ve vašem tenantovi. Zásady pojmenování můžete použít například k následujícím účelům:

* Informování o funkci skupiny, členství, geografické oblasti a uživateli, který skupinu vytvořil.
* Pomoc s uspořádáním skupin do kategorií v adresáři.
* Blokování použití určitých slov v názvech a aliasech skupin.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="configure-the-group-naming-policy-for-a-tenant-using-azure-portal-preview"></a>Konfigurace skupiny zásad pojmenování pro tenanta pomocí webu Azure portal (preview)

1. Přihlaste se k [centrum pro správu Azure AD](https://aad.portal.azure.com) pomocí uživatelského účtu správce.
1. Vyberte **skupiny**a pak vyberte **zásad pojmenování** otevřete stránku zásad pojmenování.

    ![Otevřete stránku zásad pojmenování v Centru pro správu](./media/groups-naming-policy/policy-preview.png)

### <a name="view-or-edit-the-prefix-suffix-naming-policy"></a>Umožňuje zobrazit nebo upravit zásady vytváření názvů předponu příponu

1. Na **zásad pojmenování** stránce **pojmenování zásady skupiny**.
1. Můžete zobrazit nebo upravit aktuální předpona nebo přípona jednotlivě pojmenování zásady tak, že vyberete atributy nebo řetězce, které chcete vynutit jako součást zásady pojmenování.
1. Prefix nebo sufix odebrat ze seznamu, vyberte, jakou předponu nebo příponu a potom vyberte **odstranit**. Více položek lze odstranit ve stejnou dobu.
1. Vyberte **Uložit** změny zásad projeví.

### <a name="view-or-edit-the-custom-blocked-words"></a>Umožňuje zobrazit nebo upravit vlastní blokované slova

1. Na **zásad pojmenování** stránce **blokované slova**.

    ![Upravit a uložit seznam blokovaných slova pro pojmenování zásady](./media/groups-naming-policy/blockedwords-preview.png)

1. Umožňuje zobrazit nebo upravit tak, že vyberete aktuální seznam blokovaných zadejte vlastní slova **Stáhnout**.
1. Nahrajte nový seznam blokovaných zadejte vlastní slova tak, že vyberete ikonu souboru.
1. Vyberte **Uložit** změny zásad projeví.

A to je vše. Nastavili jste zásady pojmenování a přidali jste vlastní blokovaná slova.

## <a name="clean-up-resources"></a>Vyčištění prostředků

### <a name="remove-the-naming-policy-using-azure-portal-preview"></a>Odebrat zásady pojmenování pomocí webu Azure portal (preview)

1. Na **zásad pojmenování** stránce **odstranit zásadu**.
1. Jakmile potvrdíte odstranění, zásady pojmenování Odebereme, včetně všech předpon přípon pojmenování zásady a jakékoli vlastní blokované slova.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste zjistili, jak nastavit zásady pojmenování pro vaši organizaci Azure AD na webu Azure portal.

Přejděte k dalšímu článku pro další informace, včetně rutin Powershellu pro pojmenování zásady, technická omezení, přidání seznamu blokovaných zadejte vlastní slova, a prostředí koncového uživatele v aplikacích Office 365.
> [!div class="nextstepaction"]
> [Pojmenování zásady prostředí PowerShell](groups-naming-policy.md)