---
title: Charakteristiky interakce více klientů – Azure AD | Dokumenty společnosti Microsoft
description: Principy klientů Služby Azure Active Directory jako plně nezávislých organizací
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
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878115"
---
# <a name="understand-how-multiple-azure-active-directory-organizations-interact"></a>Zjistěte, jak spolupracuje více organizací Služby Azure Active Directory

Ve službě Azure Active Directory (Azure AD) je každý klient plně nezávislá organizace: partner, který je logicky nezávislý na ostatních organizacích Azure AD, které spravujete. Tato nezávislost mezi organizacemi zahrnuje nezávislost zdrojů, administrativní nezávislost a nezávislost synchronizace. Neexistuje žádný vztah nadřazený podřízený mezi organizacemi.

## <a name="resource-independence"></a>Nezávislost zdrojů

* Pokud vytvoříte nebo odstraníte prostředek Azure AD v jedné organizaci, nemá žádný vliv na jakýkoli prostředek v jiné organizaci, s částečnou výjimkou externích uživatelů.
* Pokud zaregistrujete jeden z názvů domén u jedné organizace, nemůže být použit jinou organizací.

## <a name="administrative-independence"></a>Správní nezávislost

Pokud neadministrativní uživatel organizace Contoso vytvoří testovací organizaci Test, pak:

* Ve výchozím nastavení je uživatel, který vytvoří organizaci, přidán jako externí uživatel v této nové organizaci a přiřazena role globálního správce v této organizaci.
* Správci organizace Contoso nemají žádná přímá oprávnění správce k organizaci Test, pokud jim správce testu výslovně neudělí tato oprávnění. Správci společnosti Contoso však mohou řídit přístup k organizaci Test, pokud řídí uživatelský účet, který vytvořil test.
* Pokud přidáte nebo odeberete roli Azure AD pro uživatele v jedné organizaci, změna nemá vliv na role, které je uživatel přiřazen v jiné organizaci Azure AD.

## <a name="synchronization-independence"></a>Nezávislost synchronizace

Každou organizaci Azure AD můžete nakonfigurovat nezávisle tak, aby se data synchronizovala z jedné instance:

* Nástroj Azure AD Connect pro synchronizaci dat s jednou doménovou strukturu služby AD.
* Konektor Azure Active Directory pro Forefront Identity Manager, k synchronizaci dat s jedním nebo více místních doménových struktur a nebo mimo Zdroje dat Azure AD.

## <a name="add-an-azure-ad-organization"></a>Přidání organizace Azure AD

Pokud chcete přidat organizaci Azure AD na webu Azure Portal, přihlaste se na [portál Azure](https://portal.azure.com) pomocí účtu, který je globálním správcem Azure AD, a vyberte **Nový**.

> [!NOTE]
> Na rozdíl od jiných prostředků Azure, vaše organizace Azure AD nejsou podřízené prostředky předplatného Azure. Pokud vaše předplatné Azure zruší nebo vypršela platnost, můžete stále přistupovat k datům organizace Azure AD pomocí Azure PowerShellu, rozhraní Microsoft Graph API nebo Centra pro správu Microsoft u 365. K organizaci můžete také [přidružit jiné předplatné](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Další kroky

Důležité informace o licencování Azure AD a doporučené postupy najdete v tématu [Co je licencování Služby Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
