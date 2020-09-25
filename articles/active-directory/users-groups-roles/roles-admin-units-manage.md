---
title: Přidat a odebrat jednotky pro správu – Azure Active Directory | Microsoft Docs
description: Pomocí jednotek pro správu Omezte rozsah oprávnění role v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30b673994e20f01dde504adb438aa1b199c96d88
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264687"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Správa jednotek pro správu v Azure Active Directory

Pro přesnější administrativní řízení v Azure Active Directory (Azure AD) můžete přiřadit uživatele k roli Azure AD s oborem, který je omezený na jednu nebo více jednotek pro správu (Austrálie).

## <a name="get-started"></a>Začínáme

1. Pokud chcete spouštět dotazy z následujících pokynů pomocí [Graph Exploreru](https://aka.ms/ge), udělejte toto:

    a. V Azure Portal přejdete do služby Azure AD. V seznamu aplikace vyberte **Průzkumník grafů**a potom v **Graph Exploreru vyberte udělit souhlas správce**.

    ![Snímek obrazovky ukazující odkaz na udělení souhlasu správce](./media/roles-admin-units-manage/select-graph-explorer.png)

    b. V Průzkumníku grafů vyberte **beta** verzi.

    ![Snímek obrazovky zobrazující vybranou verzi beta](./media/roles-admin-units-manage/select-beta-version.png)

1. Použijte verzi Preview služby Azure AD PowerShell.

## <a name="add-an-administrative-unit"></a>Přidat jednotku pro správu

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. V Azure Portal otevřete službu Azure AD a v levém podokně vyberte **jednotky pro správu**.

    ![Snímek obrazovky správce unitslink ve službě Azure AD](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Vyberte **Přidat** a potom zadejte název jednotky pro správu. Volitelně můžete přidat popis jednotky pro správu.

    ![Snímek obrazovky s tlačítkem Přidat a textové pole pro zadání názvu jednotky pro správu](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Chcete-li dokončit jednotku správy, vyberte možnost **Přidat** .

### <a name="use-powershell"></a>Použití prostředí PowerShell

Než se pokusíte spustit následující příkazy, nainstalujte Azure AD PowerShell (Preview):

```powershell
Connect-AzureAD
New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

V případě potřeby můžete upravit hodnoty, které jsou uzavřeny v uvozovkách.

### <a name="use-microsoft-graph"></a>Použít Microsoft Graph

```http
Http Request
POST /administrativeUnits
Request body
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Odebrání jednotky pro správu

V Azure AD můžete odebrat jednotku pro správu, kterou už nebudete potřebovat, jako jednotku rozsahu pro administrativní role.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. V Azure Portal přejdete na **Azure AD**  >  **jednotky pro správu**Azure AD. 
1. Vyberte jednotku pro správu, kterou chcete odstranit, a pak vyberte **Odstranit**. 
1. Chcete-li potvrdit, že chcete jednotku pro správu odstranit, vyberte možnost **Ano**. Jednotka pro správu se odstraní.

![Snímek obrazovky s tlačítkem odstranit jednotku pro správu a potvrzovacím oknem](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Použití prostředí PowerShell

```powershell
$delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId
```

Hodnoty, které jsou uzavřeny v uvozovkách, můžete upravit podle potřeby konkrétního prostředí.

### <a name="use-the-graph-api"></a>Použití Graph API

```http
HTTP request
DELETE /administrativeUnits/{Admin id}
Request body
{}
```

## <a name="next-steps"></a>Další kroky

* [Správa uživatelů v jednotce pro správu](roles-admin-units-add-manage-users.md)
* [Správa skupin v jednotce pro správu](roles-admin-units-add-manage-groups.md)
