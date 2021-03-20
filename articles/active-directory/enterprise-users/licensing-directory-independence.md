---
title: Charakteristiky interakce s více klienty – Azure AD | Microsoft Docs
description: Porozumění nezávislosti mezi daty vašich Azure Active Directorych organizací
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: overview
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ce3e4c6a7708fba15560564577c9b01722c8aec
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96548048"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Pochopení způsobu interakce více Azure Active Directorych organizací

V Azure Active Directory (Azure AD) je každá organizace Azure AD plně nezávislá: partner, který je logicky nezávislý na ostatních organizacích Azure AD, které spravujete. Tato nezávislost mezi organizacemi zahrnuje nezávislost prostředků, nezávislost správy a nezávislost synchronizace. Mezi organizacemi neexistuje žádný vztah nadřazený-podřízený.

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
