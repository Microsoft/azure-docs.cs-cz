---
title: Charakteristiky interakce s více klienty – Azure AD | Microsoft Docs
description: Principy Azure Active Directorych tenantů jako zcela nezávislých organizací
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 04/07/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175d9ce7db1657e0e654f46adaf8a8d8ef28c25e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878115"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Pochopení způsobu interakce více Azure Active Directorych organizací

V Azure Active Directory (Azure AD) je každý tenant plně nezávislá organizace: partner, který je logicky nezávislý na ostatních organizacích Azure AD, které spravujete. Tato nezávislost mezi organizacemi zahrnuje nezávislost prostředků, nezávislost správy a nezávislost synchronizace. Mezi organizacemi neexistuje žádný vztah nadřazený-podřízený.

## <a name="resource-independence"></a>Nezávislost prostředků

* Pokud vytvoříte nebo odstraníte prostředek služby Azure AD v jedné organizaci, nemá žádný vliv na žádný prostředek v jiné organizaci s částečnou výjimkou externích uživatelů.
* Pokud zaregistrujete jeden z názvů domén s jednou organizací, nemůže ho použít žádná jiná organizace.

## <a name="administrative-independence"></a>Nezávislost správy

Pokud uživatel, který není správcem organizace contoso, vytvoří testovací organizaci, pak:

* Ve výchozím nastavení se uživatel, který vytvoří organizaci, přidá jako externí uživatel v této nové organizaci a přiřadí roli globálního správce v této organizaci.
* Správci organizace contoso nemají žádná přímá administrativní oprávnění k organizaci "test", pokud správce testu tyto oprávnění výslovně neudělí. Správci společnosti Contoso ale můžou řídit přístup k organizaci test, pokud ovládají uživatelský účet, který vytvořil test.
* Pokud přidáte nebo odeberete roli Azure AD pro uživatele v jedné organizaci, změna nebude mít vliv na role, které je uživatel přiřazený v jakékoli jiné organizaci Azure AD.

## <a name="synchronization-independence"></a>Nezávislost synchronizace

Každou organizaci Azure AD můžete nakonfigurovat nezávisle a získat tak data synchronizovaná z jedné instance:

* Nástroj Azure AD Connect, který slouží k synchronizaci dat s jednou doménovou strukturou služby AD.
* Konektor Azure Active Directory pro Forefront Identity Manager, který slouží k synchronizaci dat s jednou nebo více místními doménovými strukturami nebo zdroji dat mimo Azure AD.

## <a name="add-an-azure-ad-organization"></a>Přidat organizaci Azure AD

Pokud chcete do Azure Portal přidat organizaci Azure AD, přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu, který je globálním správcem služby Azure AD, a vyberte **Nový**.

> [!NOTE]
> Na rozdíl od jiných prostředků Azure nejsou organizace Azure AD podřízenými prostředky předplatného Azure. Pokud se vaše předplatné Azure zruší nebo vypršela jeho platnost, můžete k datům organizace Azure AD přistupovat pomocí Azure PowerShell, rozhraní Microsoft Graph API nebo centra pro správu Microsoft 365. K organizaci taky můžete [přidružit jiné předplatné](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Další kroky

Informace o licencování a osvědčených postupech služby Azure AD najdete v tématu [co je Azure Active Directory licencování?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
