---
title: 'Rychlý start: Přístup ke službě Azure Active Directory a vytvoření nového tenanta | Microsoft Docs'
description: Rychlý start s informace o tom, jak najít Azure Active Directory a jak vytvořit nového tenanta pro vaši organizaci.
services: active-directory
author: eross-msft
manager: mtillman
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: lizross
custom: it-pro
ms.openlocfilehash: 8ef68c8afcf61a1a11c341a679443071aece9812
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46362999"
---
# <a name="quickstart-access-azure-active-directory-to-create-a-new-tenant"></a>Rychlý start: Přístup ke službě Azure Active Directory a vytvoření nového tenanta
Pomocí portálu Azure Active Directory (Azure AD) můžete provádět všechny úlohy správy, včetně vytvoření nového tenanta pro vaši organizaci. 

V tomto rychlém startu se dozvíte, jak se dostat na web Azure Portal a do služby Azure Active Directory a jak vytvořit základního tenanta pro vaši organizaci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Než začnete, budete muset:

- Ověřit, že vaše organizace má platnou licenci Azure AD.

- Ověřit, že jste globální správce.

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com/) vaší organizace pomocí účtu globálního správce.

![Snímek obrazovky webu Azure Portal](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

## <a name="create-a-new-tenant-for-your-organization"></a>Vytvoření nového tenanta pro vaši organizaci
Po přihlášení k webu Azure Portal můžete vytvořit nového tenanta pro svou organizaci. Váš nový tenant reprezentuje vaši organizaci a pomáhá vám spravovat konkrétní instanci cloudových služeb společnosti Microsoft pro vaše interní a externí uživatele.

### <a name="to-create-a-new-tenant"></a>Vytvoření nového tenanta
1. Vyberte **Azure Active Directory**, vyberte **Vytvořit prostředky**, vyberte **Identita** a pak vyberte **Azure Active Directory**.

    Zobrazí se stránka **Vytvořit adresář**.

    ![Stránka pro vytvoření služby Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

2.  Na stránce **Vytvořit adresář** zadejte následující informace:
    
    - Do pole _Název organizace_ zadejte **Contoso**.

    - Do pole _Počáteční název domény_ zadejte **Contoso**.

    - V poli _Země nebo oblast_ ponechte možnost **USA**.

3. Vyberte **Vytvořit**.

Váš nový tenant se vytvoří s doménu contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud tuto aplikaci nebudete používat, můžete tenanta odstranit provedením následujících kroků:

- Vyberte **Azure Active Directory** a pak na stránce **Contoso - přehled** vyberte **Odstranit adresář**.

    Tím odstraníte tenanta a k němu přidružené informace.

    ![Stránka Vytvořit adresář](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Další kroky
- Změňte nebo přidejte další názvy domén, viz [Přidání vlastního názvu domény do Azure Active Directory](add-custom-domain.md)

- Přidejte uživatele, viz [Přidání nebo odstranění nového uživatele](add-users-azure-active-directory.md)

- Přidejte skupiny a členy, viz [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- Přečtěte si informace o tom, jak vám [přístup na základě role pomocí služby Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md) a [podmíněný přístup](../../role-based-access-control/conditional-access-azure-management.md) může pomoci spravovat přístup k aplikacím a prostředkům vaší organizace.
