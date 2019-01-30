---
title: Postup vytvoření role registrace pro Azure Stack
description: Jak vytvořit vlastní roli, abyste se vyhnuli použití globálního správce pro registraci.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 01/10/2019
ms.openlocfilehash: 80caa470675a78a9c2e3d4c055333719f54fe64a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247880"
---
# <a name="create-a-registration-role-for-azure-stack"></a>Umožňuje vytvořit roli registrace pro Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Pro scénáře, kde nechcete v rámci předplatného Azure udělit oprávnění vlastníka můžete vytvořit vlastní roli přiřadit oprávnění pro uživatelský účet pro registraci služby Azure Stack.

> [!WARNING]
> Toto není součást stavu zabezpečení. Použijte ho ve scénářích místo, kam chcete omezení kvůli zamezení náhodným změnám k předplatnému Azure. Pokud je uživatel delegované oprávnění pro tuto vlastní roli, má uživatel práva upravovat oprávnění a zvýšení oprávnění. Přiřadíte pouze uživatele, kterému důvěřujete vlastní roli.

Při registraci služby Azure Stack, vyžaduje registraci účtu následující oprávnění služby Azure Active Directory a oprávnění pro předplatné Azure:

* **Oprávnění k registraci aplikace ve vašem tenantovi Azure Active Directory:** Správci mají oprávnění k registraci aplikace. Oprávnění pro uživatele je globální nastavení pro všechny uživatele v tenantovi. Zobrazení nebo změna nastavení viz [vytvořit aplikace a instančního objektu, který má přístup k prostředkům Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

    *Uživatele můžou registrovat aplikace* nastavení musí být nastavené na **Ano** můžete povolit účet uživatele k registraci Azure Stack. Pokud je nastavení registrace aplikací nastaveno **ne**, nelze použít uživatelský účet a musí používat účet globálního správce registrace Azure Stack.

* **Sada dostatečná oprávnění pro předplatné Azure:** Uživatelé ve skupině vlastníků mají dostatečná oprávnění. Pro jiné účty můžete přiřadit oprávnění nastavit pomocí přiřazení vlastní role, jak je uvedeno v následující části.

## <a name="create-a-custom-role-using-powershell"></a>Vytvoření vlastní role pomocí prostředí PowerShell

Chcete-li vytvořit vlastní roli, musíte mít `Microsoft.Authorization/roleDefinitions/write` oprávnění ve všech `AssignableScopes`, jako například [vlastníka](../role-based-access-control/built-in-roles.md#owner) nebo [správce uživatelských přístupů](../role-based-access-control/built-in-roles.md#user-access-administrator). Pomocí následující šablony JSON pro zjednodušení definuje vlastní roli. Šablona vytvoří vlastní roli, která umožňuje požadovaná pro čtení a zápis pro registraci Azure Stack.

1. Vytvořte soubor JSON. Například  `C:\CustomRoles\registrationrole.json`
2. Přidejte do souboru následující kód JSON. <SubscriptionID> nahraďte ID vašeho předplatného Azure.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. V prostředí PowerShell připojení k Azure pomocí Azure Resource Manageru. Po zobrazení výzvy ověřování, například pomocí účtu s dostatečnými oprávněními [vlastníka](../role-based-access-control/built-in-roles.md#owner) nebo [správce uživatelských přístupů](../role-based-access-control/built-in-roles.md#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Chcete-li přidat roli do předplatného, použijte **New-AzureRmRoleDefinition** určení soubor šablony JSON.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Přiřazení uživatele k roli registrace

Po vytvoření vlastní role registrace přiřazení rolí uživatelů, registrace Azure Stack.

1. Přihlaste se pomocí účtu s dostatečná oprávnění u předplatného Azure k delegování práv – například [vlastníka](../role-based-access-control/built-in-roles.md#owner) nebo [správce uživatelských přístupů](../role-based-access-control/built-in-roles.md#user-access-administrator) .
2. V **předplatná**vyberte **řízení přístupu (IAM) > Přidat přiřazení role**.
3. V **Role**, zvolte vlastní roli, které jste vytvořili *role registrace Azure Stack*.
4. Vyberte uživatele, které chcete přiřadit k roli.
5. Vyberte **Uložit** vybranými uživateli přiřadit roli.

    ![Vyberte možnost uživatelé přiřazení k roli](media/azure-stack-registration-role/assign-role.png)

Další informace o používání vlastních rolí, najdete v části [správě přístupu pomocí RBAC a webu Azure portal](../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Další postup

[Registrace Azure Stack s Azure](azure-stack-registration.md)
