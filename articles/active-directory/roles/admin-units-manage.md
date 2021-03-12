---
title: Přidat a odebrat jednotky pro správu – Azure Active Directory | Microsoft Docs
description: Pomocí jednotek pro správu Omezte rozsah oprávnění role v Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0706fad1e5340625c32eab691ac3e4d58eeafc9f
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012093"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Správa jednotek pro správu v Azure Active Directory

Pro přesnější administrativní řízení v Azure Active Directory (Azure AD) můžete přiřadit uživatele k roli Azure AD s oborem, který je omezený na jednu nebo víc jednotek pro správu.

## <a name="get-started"></a>Začínáme

1. Pokud chcete spouštět dotazy z následujících pokynů pomocí [Graph Exploreru](https://aka.ms/ge), udělejte toto:

    a. V Azure Portal přejdete do služby Azure AD. 
    
    b. V seznamu aplikace vyberte **Průzkumník grafů**.
    
    c. V podokně **oprávnění** vyberte **udělit souhlas správce pro Průzkumník grafů**.

    ![Snímek obrazovky s odkazem "udělení souhlasu správce k Průzkumníku Graph".](./media/admin-units-manage/select-graph-explorer.png)


1. Použijte [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/).

## <a name="add-an-administrative-unit"></a>Přidat jednotku pro správu

Jednotku pro správu můžete přidat pomocí Azure Portal nebo PowerShellu.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. V Azure Portal přejdete do služby Azure AD. Pak v levém podokně vyberte **jednotky pro správu**.

    ![Snímek obrazovky s odkazem "administrativní jednotky" v Azure AD.](./media/admin-units-manage/nav-to-admin-units.png)

1. V horní části podokna vyberte tlačítko **Přidat** a potom do pole **název** zadejte název jednotky pro správu. Volitelně můžete přidat popis jednotky pro správu.

    ![Snímek obrazovky s tlačítkem Přidat a polem název pro zadání názvu jednotky pro správu](./media/admin-units-manage/add-new-admin-unit.png)

1. Vyberte modré tlačítko **Přidat** k finalizaci jednotky pro správu.

### <a name="use-powershell"></a>Použití prostředí PowerShell

Než se pokusíte spustit následující příkazy, nainstalujte [Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureAD/) :

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

V případě potřeby můžete upravit hodnoty, které jsou uzavřeny v uvozovkách.

### <a name="use-microsoft-graph"></a>Použít Microsoft Graph

Žádost

```http
POST /administrativeUnits
```

Text

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>Odebrání jednotky pro správu

V Azure AD můžete odebrat jednotku pro správu, kterou už nebudete potřebovat, jako jednotku rozsahu pro administrativní role.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

1. V Azure Portal otevřete **službu Azure AD** a vyberte **jednotky pro správu**. 
1. Vyberte jednotku pro správu, kterou chcete odstranit, a pak vyberte **Odstranit**. 
1. Chcete-li potvrdit, že chcete jednotku pro správu odstranit, vyberte možnost **Ano**. Jednotka pro správu se odstraní.

![Snímek obrazovky s tlačítkem pro odstranění jednotky pro správu a potvrzovacím oknem](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="use-powershell"></a>Použití prostředí PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -ObjectId $adminUnitObj.ObjectId
```

Hodnoty, které jsou uzavřeny v uvozovkách, můžete upravit podle potřeby konkrétního prostředí.

### <a name="use-the-graph-api"></a>Pomocí rozhraní Graph API

Žádost

```http
DELETE /administrativeUnits/{admin-unit-id}
```

Text

```http
{}
```

## <a name="next-steps"></a>Další kroky

* [Správa uživatelů v jednotce pro správu](admin-units-add-manage-users.md)
* [Správa skupin v jednotce pro správu](admin-units-add-manage-groups.md)
