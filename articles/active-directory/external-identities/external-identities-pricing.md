---
title: Model fakturace MAU pro externí identity Azure AD
description: Přečtěte si o modelu fakturace pro Azure AD External identity měsíčně aktivních uživatelů (MAU) pro spolupráci uživatelů typu Host (B2B) ve službě Azure AD. Přečtěte si, jak propojit tenanta Azure AD s předplatným Azure.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 09/21/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 983a803245467145a0b1161a4495e8045759e7ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92442061"
---
# <a name="billing-model-for-azure-ad-external-identities"></a>Model fakturace pro externí identity Azure AD

Ceny za externí identity Azure Active Directory (Azure AD) jsou založené na měsíčních aktivních uživatelích (MAU), což je počet jedinečných uživatelů s aktivitou ověřování v kalendářním měsíci. Tento model fakturace platí pro spolupráci uživatelů Azure AD hosta (B2B) i pro [klienty Azure AD B2C](../../active-directory-b2c/billing.md). MAU účtování pomáhá snižovat náklady tím, že nabízí bezplatnou úroveň a flexibilní a předvídatelné ceny. V tomto článku se dozvíte o fakturaci MAU a propojování tenantů Azure AD s předplatným.

> [!IMPORTANT]
> Tento článek neobsahuje informace o cenách. Nejnovější informace o fakturaci a cenách využití najdete v tématu [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="what-do-i-need-to-do"></a>Co musím udělat?

Pokud chcete využít výhod fakturace MAU, musí být váš tenant služby Azure AD propojený s předplatným Azure.

|Pokud je váš tenant:  |Musíte:  |
|---------|---------|
| Tenant Azure AD už je propojený s předplatným.     | Není potřeba dělat nic. Pokud používáte funkce externích identit ke spolupráci s uživateli typu Host, bude se vám automaticky účtovat pomocí modelu MAU.        |
| Tenant Azure AD ještě není propojený s předplatným.     | [Propojte klienta služby Azure AD s předplatným](#link-your-azure-ad-tenant-to-a-subscription) , abyste mohli aktivovat MAUou fakturaci.        |
|  |  |

## <a name="about-monthly-active-users-mau-billing"></a>O fakturaci měsíčně aktivních uživatelů (MAU)

Ve vašem tenantovi Azure AD se využití spolupráce uživatelů hosta účtuje na základě počtu jedinečných uživatelů typu host s aktivitou ověřování v kalendářním měsíci. Tento model nahrazuje model účtování poměru 1:5, který povoluje až pět uživatelů typu Host pro každou licenci Azure AD Premium ve vašem tenantovi. Pokud je váš tenant propojený s předplatným a používáte funkce externích identit ke spolupráci s uživateli typu Host, bude se vám automaticky účtovat pomocí fakturačního modelu založeného na MAU.
  
Cenová úroveň, která se vztahuje na uživatele typu Host, je založena na nejvyšší cenové úrovni přiřazené k vašemu tenantovi Azure AD. Pokud je například nejvyšší cenová úroveň ve vašem tenantovi Azure AD Premium P1, cenová úroveň Premium P1 platí také pro uživatele typu Host. Pokud se Azure AD Free nejvyšší ceny, budete vyzváni k upgradu na cenovou úroveň Premium, když se pokusíte použít prémiové funkce pro uživatele typu Host.

## <a name="link-your-azure-ad-tenant-to-a-subscription"></a>Propojení tenanta Azure AD s předplatným

Aby bylo možné správně vyúčtování a přístup k funkcím, musí být tenant služby Azure AD propojený s předplatným Azure. Pokud adresář ještě nemá předplatné, které můžete propojit, budete mít možnost ho během tohoto procesu přidat.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu Azure, kterému byla přiřazena alespoň role [Přispěvatel](../../role-based-access-control/built-in-roles.md) v rámci předplatného nebo skupiny prostředků v rámci předplatného.

2. Vyberte adresář, který chcete propojit: na panelu nástrojů Azure Portal vyberte ikonu **adresář + předplatné** a pak vyberte adresář.

    ![Vyberte ikonu adresář a předplatné.](media/external-identities-pricing/portal-mau-pick-directory.png)

3. V části **služby Azure** vyberte **Azure Active Directory**.

4. V nabídce vlevo vyberte **externí identity**.

5. V části **předplatná** vyberte **propojená předplatná**.

6. V seznamu tenant zaškrtněte políčko vedle tenanta a pak vyberte **propojit odběr**.

    ![Vyberte tenanta a propojte předplatné.](media/external-identities-pricing/linked-subscriptions.png)

7. V podokně propojit a předplatné vyberte **předplatné** a **skupinu prostředků**. Pak vyberte **Použít**.

   > [!NOTE]
   > Pokud nejsou uvedená žádná předplatná, můžete [k vašemu tenantovi přidružit předplatné](../fundamentals/active-directory-how-subscriptions-associated-directory.md). Nebo můžete přidat nové předplatné tím, že ho vyberete, **Pokud ještě nemáte předplatné, které zde můžete vytvořit**.

    ![Vyberte předplatné a skupinu prostředků.](media/external-identities-pricing/link-subscription-resource.png)

Po dokončení tohoto postupu se vaše předplatné Azure účtuje na základě přímých nebo smlouva Enterprisech detailů Azure, pokud jsou k dispozici.

## <a name="next-steps"></a>Další kroky

Nejnovější informace o cenách najdete v článku [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).