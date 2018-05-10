---
title: Vlastnosti služby Azure Active Directory klienta interakce | Microsoft Docs
description: Seznámení s klienty jako plně nezávislý prostředky spravovat klienty klienta Azure Active
services: active-tenant
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.component: users-groups-roles
ms.date: 10/10/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017;it-pro
ms.reviewer: piotrci
ms.openlocfilehash: 81edc75f84c1dcb4f7b94878c472569d392175b1
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Pochopit, jak více klientů služby Azure Active Directory pracovat

V Azure Active Directory (Azure AD), každý klient je plně nezávislý prostředek: sdílené, které je logicky nezávislý ostatních klientů, které spravujete. Není žádný vztah nadřazený podřízený mezi klienty. Tato nezávislost mezi klienty zahrnuje nezávislost prostředků, nezávislost správy a nezávislost synchronizace.

## <a name="resource-independence"></a>Nezávislost prostředků
* Pokud vytvoříte nebo odstranění prostředku v jednoho klienta, nemá žádný vliv na prostředky v jiném klientovi s částečnou výjimku, která externí uživatele. 
* Pokud použijete jeden názvů domén pomocí jednoho klienta, nelze použít s žádným jiným klientem.

## <a name="administrative-independence"></a>Nezávislost správy
Pokud uživatele bez oprávnění správce klienta "Contoso" vytvoří testovacím klientem "Test", pak:

* Ve výchozím nastavení je uživatel, který vytvoří klienta přidány jako externí uživatel v tohoto nového klienta a přiřazenou roli globálního správce v něm.
* Správci klienta "Contoso" mít žádné přímé oprávnění správce klienta "Test", pokud správce "Test" explicitně neudělí těchto oprávnění. Však správci "Contoso" můžou řídit přístup k klienta "Test", pokud jejich ovládání uživatelského účtu, který vytvořili Test.
* Pokud můžete přidat nebo odebrat roli správce u uživatele v jednoho klienta, tato změna nemá vliv role správce, které má uživatel v jiného klienta.

## <a name="synchronization-independence"></a>Nezávislost synchronizace
Můžete nakonfigurovat každý klient Azure AD nezávisle tak, aby se data synchronizovala z jediné instance buď:

* Nástroj Azure AD Connect pro synchronizaci dat s jednou doménovou strukturou AD.
* Azure Active klient konektor pro nástroj Forefront Identity Manager k synchronizaci dat s jedním nebo více místními doménovými strukturami nebo zdroje dat mimo Azure AD.

## <a name="add-an-azure-ad-tenant"></a>Přidat klienta Azure AD
Pokud chcete přidat klient služby Azure AD na portálu Azure, přihlaste se k [portálu Azure](https://portal.azure.com) pomocí účtu, který je globální správce Azure AD a na levé straně, vyberte **nový**.

> [!NOTE]
> Na rozdíl od jiných prostředků Azure klienti nejsou podřízené prostředky předplatného Azure. Pokud vaše předplatné Azure je zrušená nebo vypršela platnost, můžete pořád přístup k datům klienta pomocí prostředí Azure PowerShell, Azure Graph API nebo Centrum pro správu Office 365. Můžete také [přidružit jiné předplatné klienta](active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Další postup
Komplexní přehled o problémy s licencí Azure AD a osvědčené postupy, najdete v části [co je Azure Active klienta licencování?](active-directory-licensing-whatis-azure-portal.md).
