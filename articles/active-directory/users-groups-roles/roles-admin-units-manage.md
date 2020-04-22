---
title: Přidání a odebrání jednotek pro správu (preview) – Azure Active Directory | Dokumenty společnosti Microsoft
description: Pomocí jednotek pro správu můžete omezit rozsah oprávnění role ve službě Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a4a0dfaeda18b3f68ddc3c7cc7333b8c994d174
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684919"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Správa administrativních jednotek ve službě Azure Active Directory

Pro podrobnější řízení správy ve službě Azure Active Directory (Azure AD) můžete přiřadit uživatele k roli Azure AD s oborem, který je omezený na jednu nebo více jednotek pro správu (AU).

## <a name="get-started"></a>Začínáme

1. Chcete-li spustit dotazy z následujících pokynů v [aplikaci Graph Explorer](https://aka.ms/ge), postupujte takto:

    a. Na webu Azure Portal přejděte na Azure AD. V seznamu aplikací vyberte **Průzkumník a**pak vyberte **Udělit souhlas správce s aplikací Graph Explorer**.

    ![Snímek obrazovky s odkazem na "Udělit souhlas správce"](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. V Průzkumníkovi grafů vyberte **beta** verzi.

    ![Snímek obrazovky s vybranou beta verzí](./media/roles-admin-units-manage/select-beta-version.png)

1. Použijte verzi preview Azure AD PowerShellu.

## <a name="add-an-administrative-unit"></a>Přidání administrativní jednotky

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Na webu Azure Portal přejděte na Azure AD a v levém podokně vyberte **jednotky pro správu**.

    ![Snímek obrazovky s odkazem Pro správu (Preview) ve službě Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Vyberte **Přidat** a zadejte název administrativní jednotky. Volitelně můžete přidat popis správní jednotky.

    ![Snímek obrazovky s tlačítkem Přidat a textového pole pro zadání názvu administrativní jednotky](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Vyberte **Přidat,** chcete-li dokončit administrativní jednotku.

### <a name="use-powershell"></a>Použití prostředí PowerShell

Nainstalujte Azure AD PowerShell (preview) před pokusem o spuštění následujících příkazů:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

Podle potřeby můžete upravit hodnoty, které jsou uzavřeny v uvozovkách.

### <a name="use-microsoft-graph"></a>Použití microsoft graphu

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Odebrání administrativní jednotky

Ve službě Azure AD můžete odebrat administrativní jednotku, kterou už nepotřebujete jako jednotku oboru pro role pro správu.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. Na webu Azure Portal přejděte na azure **ad** > **jednotky pro správu**. 
1. Vyberte administrativní jednotku, která má být odstraněna, a pak vyberte **odstranit**. 
1. Chcete-li potvrdit, že chcete odstranit administrativní jednotku, vyberte **možnost Ano**. Administrativní jednotka bude odstraněna.

![Snímek obrazovky s administrativní jednotkou Odstranit a potvrzovacího okna](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Použití prostředí PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Můžete upravit hodnoty, které jsou uzavřeny v uvozovkách, jak je požadováno pro konkrétní prostředí.

### <a name="use-the-graph-api"></a>Použití rozhraní API pro grafy

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Další kroky

* [Správa uživatelů ve správní jednotce](roles-admin-units-add-manage-users.md)
* [Správa skupin ve správní jednotce](roles-admin-units-add-manage-groups.md)
