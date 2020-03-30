---
title: Rychlý start – přístup & vytvořit nového klienta – Azure AD
description: Pokyny, jak najít Azure Active Directory a jak vytvořit nového klienta pro vaši organizaci.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f29d103ce1be426fb0b5c462cc1d831fefe87b6
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80049998"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Úvodní příručka: Vytvoření nového klienta ve službě Azure Active Directory
Pomocí portálu Azure Active Directory (Azure AD) můžete provádět všechny úlohy správy, včetně vytvoření nového tenanta pro vaši organizaci. 

V tomto rychlém startu se dozvíte, jak se dostat na web Azure Portal a do služby Azure Active Directory a jak vytvořit základního tenanta pro vaši organizaci.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="create-a-new-tenant-for-your-organization"></a>Vytvoření nového tenanta pro vaši organizaci
Po přihlášení k webu Azure Portal můžete vytvořit nového tenanta pro svou organizaci. Váš nový tenant reprezentuje vaši organizaci a pomáhá vám spravovat konkrétní instanci cloudových služeb společnosti Microsoft pro vaše interní a externí uživatele.

### <a name="to-create-a-new-tenant"></a>Vytvoření nového tenanta

1. Přihlaste se na [portál Azure](https://portal.azure.com/)vaší organizace .

1. V nabídce webu Azure Portal vyberte **Vytvořit prostředek**.  

    ![Stránka Vytvoření nástroje Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-portal.png)

1. Vyberte **Identita**a pak vyberte **Azure Active Directory**.

    Zobrazí se stránka **Vytvořit adresář**.

    ![Stránka pro vytvoření služby Azure Active Directory](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)

1.  Na stránce **Vytvořit adresář** zadejte následující informace:
    
    - Do pole _Název organizace_ zadejte **Contoso**.

    - Do pole _Počáteční název domény_ zadejte **Contoso**.

    - V poli _Země nebo oblast_ ponechte možnost **USA**.

1. Vyberte **Vytvořit**.

Váš nový tenant se vytvoří s doménu contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud nebudete nadále používat tuto aplikaci, můžete klienta odstranit pomocí následujících kroků:

- Ujistěte se, že jste přihlášeni k adresáři, který chcete odstranit prostřednictvím filtru **služby Directory + předplatného** na webu Azure Portal, a v případě potřeby přepnutí do cílového adresáře.
- Vyberte **Azure Active Directory** a pak na stránce **Contoso - přehled** vyberte **Odstranit adresář**.

    Tím odstraníte tenanta a k němu přidružené informace.

    ![Stránka Přehled se zvýrazněným tlačítkem Odstranit adresář](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)

## <a name="next-steps"></a>Další kroky
- Změňte nebo přidejte další názvy domén, viz [Přidání vlastního názvu domény do Azure Active Directory](add-custom-domain.md)

- Přidejte uživatele, viz [Přidání nebo odstranění nového uživatele](add-users-azure-active-directory.md)

- Přidejte skupiny a členy, viz [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- Informace o [přístupu založeném na rolích pomocí správy privilegovaných identit](../../role-based-access-control/pim-azure-resource.md) a [podmíněného přístupu](../../role-based-access-control/conditional-access-azure-management.md) ke správě přístupu k aplikacím a prostředkům vaší organizace.

- Přečtěte si o službě Azure AD, včetně [základních licenčních informací, terminologie a souvisejících funkcích](active-directory-whatis.md).
