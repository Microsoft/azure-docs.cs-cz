---
title: Charakteristiky interakce více klientů – Azure AD | Dokumenty společnosti Microsoft
description: Spravujte klienty klienta Azure Active tak, že své klienty porozumíte jako plně nezávislým prostředkům.
services: active-tenant
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 11/08/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4eb09ab7fa31af5edf14b113a6a88e08df2d115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77562254"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Zjistěte, jak spolupracuje více klientů Služby Azure AD

Ve službě Azure Active Directory (Azure AD) je každý klient plně nezávislý prostředek: partner, který je logicky nezávislý na ostatních klientech, které spravujete. Neexistuje žádný vztah nadřazený podřízený mezi tenanty. Tato nezávislost mezi klienty zahrnuje nezávislost prostředků, nezávislost správy a nezávislost synchronizace.

## <a name="resource-independence"></a>Nezávislost zdrojů
* Pokud vytvoříte nebo odstraníte prostředek v jednom klientovi, nemá žádný vliv na jakýkoli prostředek v jiném klientovi, s částečnou výjimkou externích uživatelů. 
* Pokud používáte jeden z názvů domén s jedním klientem, nelze jej použít s žádným jiným klientem.

## <a name="administrative-independence"></a>Správní nezávislost
Pokud uživatel klienta contoso bez oprávnění správce vytvoří testovacího klienta Test, pak:

* Ve výchozím nastavení je uživatel, který vytvoří klienta, přidán jako externí uživatel v tomto novém tenantovi a přiřazenroli globálního správce v tomto tenantovi.
* Správci klienta Contoso nemají žádná přímá oprávnění správce k tenantovi Test, pokud jim správce testu tato oprávnění výslovně neudělí. Správci společnosti Contoso však mohou řídit přístup k klientovi Test, pokud řídí uživatelský účet, který vytvořil test.
* Pokud přidáte nebo odeberete roli správce pro uživatele v jednom klientovi, změna nemá vliv na role správce, které má uživatel v jiném tenantovi.

## <a name="synchronization-independence"></a>Nezávislost synchronizace
Každý klient Azure AD můžete nakonfigurovat nezávisle tak, aby se data synchronizovala z jedné instance buď:

* Nástroj Azure AD Connect pro synchronizaci dat s jednou doménovou strukturu služby AD.
* Konektor klienta Azure Active pro Forefront Identity Manager pro synchronizaci dat s jedním nebo více místními doménovými strukturami a/nebo mimo zdroje dat Azure AD.

## <a name="add-an-azure-ad-tenant"></a>Přidání klienta Azure AD
Pokud chcete přidat klienta Azure AD na webu Azure Portal, přihlaste se na [portál Azure pomocí](https://portal.azure.com) účtu, který je globálním správcem Azure AD, a na levé straně vyberte **Nový**.

> [!NOTE]
> Na rozdíl od jiných prostředků Azure nejsou vaši klienti podřízenými prostředky předplatného Azure. Pokud vaše předplatné Azure zruší nebo vypršela platnost, můžete stále přistupovat k datům klienta pomocí Azure PowerShellu, rozhraní Microsoft Graph API nebo Centra pro správu Microsoft u 365. Můžete také [přidružit jiné předplatné s tenantem](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Další kroky
Obecný přehled problémů s licencováním Azure AD a doporučených postupů najdete v tématu [Co je licencování klienta Azure Active?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
