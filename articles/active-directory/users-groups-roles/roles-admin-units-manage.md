---
title: Přidání a odebrání jednotek pro správu (preview) – Azure Active Directory | Dokumenty společnosti Microsoft
description: Použití jednotek pro správu k omezení rozsahu oprávnění role ve službě Azure Active Directory
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
ms.openlocfilehash: 351cbee9cbc7cc02c7f20553b6fc8c3f861541df
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428157"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Správa administrativních jednotek ve službě Azure Active Directory

Pro podrobnější řízení správy ve službě Azure Active Directory (Azure AD) můžete přiřadit uživatele k roli Azure AD s oborem omezeným na jednu nebo více jednotek pro správu (AU).

## <a name="getting-started"></a>Začínáme

1. Chcete-li spustit dotazy z následujících pokynů pomocí [aplikace Graph Explorer](https://aka.ms/ge), zkontrolujte následující:

    1. Přejděte na Azure AD na portálu a pak v aplikacích vyberte Graph Explorer a poskytnout souhlas správce Graph Explorer.

        ![Vyberte Průzkumník a na této stránce uveďte souhlas správce.](./media/roles-admin-units-manage/select-graph-explorer.png)

    1. V Průzkumníkovi grafů se ujistěte, že jste vybrali beta verzi.

        ![vyberte beta verzi před operací POST](./media/roles-admin-units-manage/select-beta-version.png)

1. Použijte verzi preview prostředí Azure AD PowerShell. Podrobné pokyny jsou zde.

## <a name="add-an-administrative-unit"></a>Přidání administrativní jednotky

### <a name="azure-portal"></a>portál Azure

1. Přejděte na portál do služby Active Directory a v levém panelu vyberte položku Administrativní jednotky.

    ![Přechod na jednotky pro správu ve službě Azure Active Directory](./media/roles-admin-units-manage/nav-to-admin-units.png)

1. Vyberte **Přidat*** a zadejte název administrativní jednotky a volitelně můžete přidat popis pro správní jednotku.

    ![Vyberte Přidat a zadejte název pro správní jednotku.](./media/roles-admin-units-manage/add-new-admin-unit.png)

1. Vyberte **Přidat,** chcete-li dokončit administrativní jednotku.

### <a name="powershell"></a>PowerShell

Nainstalujte Azure AD PowerShell (preview verze) před pokusem o provedení akce níže:

    Connect-AzureAD
    New-AzureADAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"

Výše uvedené hodnoty lze podle potřeby upravit.

### <a name="microsoft-graph"></a>Microsoft Graph

    Http Request
    POST /administrativeUnits
    Request body
    {
        "displayName": "North America Operations",
        "description": "North America Operations administration"
    }

## <a name="remove-an-administrative-unit"></a>Odebrání administrativní jednotky

Ve službě Azure Active Directory (Azure AD) můžete odebrat jednotku pro správu, kterou už nepotřebujete jako jednotku oboru pro role pro správu.

### <a name="azure-portal"></a>portál Azure

Přejděte na **Azure AD > jednotky pro správu** na portálu. Vyberte administrativní jednotku, která má být odstraněna, a pak vyberte **odstranit**. Po potvrzení **ano**bude administrativní jednotka odstraněna.

![Vyberte administrativní jednotku, která má být odstraněna.](./media/roles-admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

    $delau = Get-AzureADAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
    Remove-AzureADAdministrativeUnit -ObjectId $delau.ObjectId

Zvýrazněná část může být změněna podle potřeby pro konkrétní prostředí.

### <a name="graph-api"></a>Graph API

    HTTP request
    DELETE /administrativeUnits/{Admin id}
    Request body
    {}

## <a name="next-steps"></a>Další kroky

[Správa uživatelů ve správní jednotce](roles-admin-units-add-manage-users.md)
[Správa skupin ve správní jednotce](roles-admin-units-add-manage-groups.md)
