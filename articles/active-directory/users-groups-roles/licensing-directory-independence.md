---
title: Vlastnosti služby Azure Active Directory tenanta interakce | Dokumentace Microsoftu
description: Pochopením vašich klientů jako plně nezávislý prostředky spravovat klienty tenanta Azure Active
services: active-tenant
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: a1022bcc3c81ef22d1ba9f6c4905e1bb4c515fa5
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150372"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Vysvětlení, jak více tenantů Azure Active Directory komunikovat

Ve službě Azure Active Directory (Azure AD), každý tenant je plně nezávislý prostředek: partnerské zařízení, která je logicky nezávislý na ostatních tenantů, která spravujete. Není žádný vztah nadřízenosti a podřízenosti mezi tenanty. Tato nezávislost mezi tenanty zahrnuje nezávislost prostředků, nezávislost správy a nezávislost synchronizace.

## <a name="resource-independence"></a>Nezávislost prostředků
* Pokud vytvoříte nebo odstranění prostředku v jednoho tenanta, nemá žádný vliv na prostředky v jiném tenantovi částečnou výjimku, externích uživatelů. 
* Pokud používáte některou názvy domén s jednoho tenanta, nelze použít s žádným jiným tenantem.

## <a name="administrative-independence"></a>Nezávislost správy
Pokud uživatel bez oprávnění správce tenanta "Contoso" vytvoří testovacího tenanta "Test", pak:

* Ve výchozím nastavení uživatel vytvářející tenanta přidán jako externí uživatel do tohoto nového tenanta a přiřazenou roli globálního správce v tomto tenantovi.
* Správci tenanta "Contoso" nemají žádná přímá oprávnění pro tenanta "Test", správu, pokud "Test" konkrétně jim správce udělí oprávnění. Však administrators "Contoso" můžou řídit přístup k tenantovi "Test", jakým se řídí uživatelský účet, který vytvoří "Test".
* Pokud můžete přidat nebo odebrat roli správce u uživatele do jednoho tenanta, tato změna nemá vliv rolí správce, které má uživatel v jiném tenantovi.

## <a name="synchronization-independence"></a>Nezávislost synchronizace
Můžete nakonfigurovat nezávisle tak, aby se data synchronizovala z jedné instance buď každého tenanta služby Azure AD:

* Nástroj Azure AD Connect pro synchronizaci dat s jednou doménovou strukturou služby AD.
* Azure Active tenanta konektor pro produkt Forefront Identity Manager, k synchronizaci dat s jedním nebo více místními doménovými strukturami, a/nebo zdroje dat mimo Azure AD.

## <a name="add-an-azure-ad-tenant"></a>Přidání tenanta Azure AD
Přidání tenanta služby Azure AD na webu Azure Portal, přihlaste se k [na webu Azure portal](https://portal.azure.com) pomocí účtu, který je globální správce Azure AD a na levé straně, vyberte **nový**.

> [!NOTE]
> Na rozdíl od jiných prostředků služby Azure vašim klientům nejsou podřízenými prostředky předplatného služby Azure. Pokud vaše předplatné Azure zruší nebo vypršela platnost, budete k němu přístup data tenanta pomocí Azure Powershellu, Azure Graph API nebo Centrum pro správu Office 365. Můžete také [přidružit jiné předplatné tenanta](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Další postup
Široký přehled o problémy s licencí Azure AD a doporučené postupy, najdete v části [co je Azure Active tenanta licencování?](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
