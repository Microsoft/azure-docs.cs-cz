---
title: Rychlý Start – přístup & vytvoření nového tenanta – Azure AD
description: Pokyny, jak najít Azure Active Directory a jak vytvořit nového tenanta pro vaši organizaci.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: ajburnle
ms.custom: it-pro, seodec18, fasttrack-edit
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeea88d8c21ba754fbeadbb24891126b639616c7
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437226"
---
# <a name="quickstart-create-a-new-tenant-in-azure-active-directory"></a>Rychlý Start: vytvoření nového tenanta v Azure Active Directory
Pomocí portálu Azure Active Directory (Azure AD) můžete provádět všechny úlohy správy, včetně vytvoření nového tenanta pro vaši organizaci. 

V tomto rychlém startu se dozvíte, jak se dostat na web Azure Portal a do služby Azure Active Directory a jak vytvořit základního tenanta pro vaši organizaci.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).

## <a name="create-a-new-tenant-for-your-organization"></a>Vytvoření nového tenanta pro vaši organizaci
Po přihlášení k webu Azure Portal můžete vytvořit nového tenanta pro svou organizaci. Váš nový tenant reprezentuje vaši organizaci a pomáhá vám spravovat konkrétní instanci cloudových služeb společnosti Microsoft pro vaše interní a externí uživatele.

### <a name="to-create-a-new-tenant"></a>Vytvoření nového tenanta

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)vaší organizace.

1. V nabídce Azure Portal vyberte možnost **Azure Active Directory**.  

    <kbd>![Azure Active Directory – Stránka s přehledem – vytvoření tenanta](media/active-directory-access-create-new-tenant/azure-ad-portal.png)</kbd>  

1. Vyberte **vytvořit tenanta**.

1. Na kartě základy vyberte typ tenanta, který chcete vytvořit, buď **Azure Active Directory** , nebo **Azure Active Directory (B2C)**.

1. Vyberte **Další: Konfigurace** , která se má přesunout na kartu konfigurace.

    <kbd>![Azure Active Directory – vytvoření stránky tenanta – karta konfigurace ](media/active-directory-access-create-new-tenant/azure-ad-create-new-tenant.png)</kbd>

1.  Na kartě Konfigurace zadejte následující informace:
    
    - Do pole **název organizace** zadejte _organizace contoso_ .

    - Do pole **počáteční název domény** zadejte _Contosoorg_ .

    - V poli _Země nebo oblast_ ponechte možnost **USA**.

1. Vyberte **Další: zkontrolovat + vytvořit**. Zkontrolujte informace, které jste zadali, a pokud jsou informace správné, vyberte **vytvořit**.

    <kbd>![Azure Active Directory – kontrola a vytvoření stránky tenanta](media/active-directory-access-create-new-tenant/azure-ad-review.png)</kbd>

Váš nový tenant se vytvoří s doménu contoso.onmicrosoft.com.

## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud nebudete tuto aplikaci nadále používat, můžete klienta odstranit pomocí následujících kroků:

- Ujistěte se, že jste přihlášeni k adresáři, který chcete odstranit, pomocí filtru **Directory + Subscription** v Azure Portal a v případě potřeby přepnete do cílového adresáře.
- Vyberte **Azure Active Directory** a pak na stránce **Contoso - přehled** vyberte **Odstranit adresář**.

    Tím odstraníte tenanta a k němu přidružené informace.

    <kbd>![Stránka s přehledem se zvýrazněným tlačítkem odstranit adresář](media/active-directory-access-create-new-tenant/azure-ad-delete-new-tenant.png)</kbd>

## <a name="next-steps"></a>Další kroky
- Změňte nebo přidejte další názvy domén, viz [Přidání vlastního názvu domény do Azure Active Directory](add-custom-domain.md)

- Přidejte uživatele, viz [Přidání nebo odstranění nového uživatele](add-users-azure-active-directory.md)

- Přidejte skupiny a členy, viz [Vytvoření základní skupiny a přidání členů](active-directory-groups-create-azure-portal.md)

- Přečtěte si o [přístupu na základě rolí pomocí Privileged Identity Management](../../role-based-access-control/best-practices.md) a [podmíněného přístupu](../../role-based-access-control/conditional-access-azure-management.md) , které vám pomůžou spravovat přístup k aplikacím a prostředkům vaší organizace.

- Přečtěte si o službě Azure AD, včetně [základních licenčních informací, terminologie a souvisejících funkcích](active-directory-whatis.md).
