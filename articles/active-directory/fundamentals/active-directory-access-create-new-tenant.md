---
title: Rychlý Start k přístupu a vytvoření nového tenanta – Azure Active Directory | Microsoft Docs
description: Pokyny, jak najít Azure Active Directory a jak vytvořit nového tenanta pro vaši organizaci.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8310b6fc33f1d03e1ccc9ccddfcd7b9e6c35d37
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473369"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Rychlý Start: vytvoření nového tenanta v Azure Active Directory
Pomocí portálu Azure Active Directory (Azure AD) můžete provádět všechny úlohy správy, včetně vytvoření nového tenanta pro vaši organizaci. 

V tomto rychlém startu se dozvíte, jak se dostat na web Azure Portal a do služby Azure Active Directory a jak vytvořit základního tenanta pro vaši organizaci.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="create-a-new-tenant-for-your-organization"></a>Vytvoření nového tenanta pro vaši organizaci
Po přihlášení k webu Azure Portal můžete vytvořit nového tenanta pro svou organizaci. Váš nový tenant reprezentuje vaši organizaci a pomáhá vám spravovat konkrétní instanci cloudových služeb společnosti Microsoft pro vaše interní a externí uživatele.

### <a name="to-create-a-new-tenant"></a>Vytvoření nového tenanta

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) vaší organizace pomocí účtu globálního správce.

1. V nabídce Azure Portal vyberte **vytvořit prostředek**.  

    ![Azure Active Directory vytvořit stránku prostředků](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Vyberte **Identita**a pak vyberte **Azure Active Directory**.

    Zobrazí se stránka **Vytvořit adresář**.

    ![Stránka pro vytvoření služby Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  Na stránce **Vytvořit adresář** zadejte následující informace:
    
    - Do pole _Název organizace_ zadejte **Contoso**.

    - Do pole _Počáteční název domény_ zadejte **Contoso**.

    - V poli _Země nebo oblast_ ponechte možnost **USA**.

1. Vyberte **Create** (Vytvořit).

Váš nový tenant se vytvoří s doménu contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud tuto aplikaci nebudete používat, můžete tenanta odstranit provedením následujících kroků:

- Vyberte **Azure Active Directory** a pak na stránce **Contoso - přehled** vyberte **Odstranit adresář**.

    Tím odstraníte tenanta a k němu přidružené informace.

    ![Stránka s přehledem se zvýrazněným tlačítkem odstranit adresář](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Další kroky
- Změňte nebo přidejte další názvy domén, viz [Přidání vlastního názvu domény do Azure Active Directory](add-custom-domain.md)

- Přidejte uživatele, viz [Přidání nebo odstranění nového uživatele](add-users-azure-active-directory.md)

- Přidejte skupiny a členy, viz [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- Přečtěte si o [přístupu na základě rolí pomocí Privileged Identity Management](../../role-based-access-control/pim-azure-resource.md) a [podmíněného přístupu](../../role-based-access-control/conditional-access-azure-management.md) , které vám pomůžou spravovat přístup k aplikacím a prostředkům vaší organizace.

- Přečtěte si o službě Azure AD, včetně [základních licenčních informací, terminologie a souvisejících funkcích](active-directory-whatis.md).
