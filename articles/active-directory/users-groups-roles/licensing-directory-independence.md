---
title: Charakteristiky interakce s více klienty – Azure AD | Microsoft Docs
description: Spravujte klienty Azure Active tenant tím, že porozumíte svým klientům jako plně nezávislé prostředky.
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
ms.openlocfilehash: 0ce791ee3536b9ab07605787209e59b7e5d60126
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74026292"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Pochopení způsobu interakce více Azure Active Directory tenantů

V Azure Active Directory (Azure AD) je každý tenant plně nezávislý prostředek: partner, který je logicky nezávislý na ostatních klientech, které spravujete. Mezi klienty neexistuje žádný vztah nadřazený-podřízený. Tato nezávislost mezi klienty zahrnuje nezávislost prostředků, nezávislost správy a nezávislost synchronizace.

## <a name="resource-independence"></a>Nezávislost prostředků
* Pokud vytvoříte nebo odstraníte prostředek v jednom tenantovi, nemá žádný vliv na žádný prostředek v jiném tenantovi s částečnou výjimkou externích uživatelů. 
* Pokud použijete jeden z názvů domén s jedním klientem, nedá se použít s žádným jiným klientem.

## <a name="administrative-independence"></a>Nezávislost správy
Pokud uživatel bez oprávnění správce klienta contoso vytvoří testovacího tenanta, pak:

* Ve výchozím nastavení se uživatel, který vytvoří tenanta, přidá jako externí uživatel v tomto novém tenantovi a přiřadí roli globálního správce v tomto tenantovi.
* Správci klienta contoso nemají žádná přímá oprávnění pro správu pro tenanta test, a to jenom v případě, že mu správce testu tyto oprávnění výslovně neudělí. Správci společnosti Contoso ale můžou řídit přístup k tenantovi test, pokud ovládají uživatelský účet, který vytvořil test.
* Pokud přidáte nebo odeberete roli správce pro uživatele v jednom tenantovi, změna nemá vliv na role správce, které má uživatel v jiném tenantovi.

## <a name="synchronization-independence"></a>Nezávislost synchronizace
Každý tenant služby Azure AD můžete nakonfigurovat nezávisle na tom, aby se data synchronizovaná z jedné instance jednoho z nich:

* Nástroj Azure AD Connect, který slouží k synchronizaci dat s jednou doménovou strukturou služby AD.
* Konektor Azure Active tenant pro Forefront Identity Manager, který slouží k synchronizaci dat s jednou nebo více místními doménovými strukturami nebo zdroji dat mimo Azure AD.

## <a name="add-an-azure-ad-tenant"></a>Přidat tenanta Azure AD
Pokud chcete do Azure Portal přidat tenanta Azure AD, přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu, který je globálním správcem služby Azure AD, a na levé straně vyberte **Nový**.

> [!NOTE]
> Na rozdíl od jiných prostředků Azure nejsou vaši klienti podřízenými prostředky předplatného Azure. Pokud se vaše předplatné Azure zruší nebo vypršela jeho platnost, můžete k datům tenanta přistupovat pomocí Azure PowerShell, Graph API Azure nebo centra pro správu Microsoft 365. K tenantovi taky můžete [přidružit jiné předplatné](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Další kroky
Obecný přehled problémů s licencováním Azure AD a osvědčenými postupy najdete v tématu [co je licencování Azure Active tenant](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
