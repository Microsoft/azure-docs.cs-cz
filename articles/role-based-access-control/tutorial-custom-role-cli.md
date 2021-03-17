---
title: 'Kurz: Vytvoření vlastní role Azure pomocí Azure CLI – Azure RBAC'
description: Začínáme s vytvářením vlastní role Azure pomocí Azure CLI a řízení přístupu na základě role Azure (Azure RBAC) v tomto kurzu.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 02/20/2019
ms.author: rolyon
ms.custom: devx-track-azurecli
ms.openlocfilehash: 319bca74c8e781e5dc5022e9fb901b2edca24a80
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87485639"
---
# <a name="tutorial-create-an-azure-custom-role-using-azure-cli"></a>Kurz: Vytvoření vlastní role Azure pomocí Azure CLI

Pokud [předdefinované role Azure](built-in-roles.md) nevyhovují konkrétním potřebám vaší organizace, můžete vytvořit vlastní role. V tomto kurzu pomocí Azure CLI vytvoříte vlastní roli Čtenář lístků podpory. Vlastní role uživateli umožňuje zobrazit vše v rovině správy předplatného a také otevřít lístky podpory.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření vlastní role
> * Výpis vlastních rolí
> * Aktualizace vlastní role
> * Odstranění vlastní role

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Oprávnění k vytváření vlastních rolí, například [Vlastník](built-in-roles.md#owner) nebo [Správce přístupu uživatelů](built-in-roles.md#user-access-administrator)
- [Azure Cloud Shell](../cloud-shell/overview.md) nebo [Azure CLI](/cli/azure/install-azure-cli)

## <a name="sign-in-to-azure-cli"></a>Přihlášení k Azure CLI

Přihlaste se k [Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="create-a-custom-role"></a>Vytvoření vlastní role

Nejjednodušší způsob, jak vytvořit vlastní roli, je začít se šablonou JSON, upravit ji a pak vytvořit novou roli.

1. Projděte si seznam operací pro [poskytovatele prostředků Microsoft.Support](resource-provider-operations.md#microsoftsupport). Je užitečné znát operace, které máte k dispozici k vytváření oprávnění.

    | Operace | Popis |
    | --- | --- |
    | Microsoft.Support/register/action | Registruje poskytovatele prostředků podpory. |
    | Microsoft.Support/supportTickets/read | Umožňuje získat podrobnosti lístku podpory (včetně stavu, závažnosti, podrobností kontaktu a komunikací) nebo seznam lístků podpory pro všechna předplatná. |
    | Microsoft.Support/supportTickets/write | Umožňuje vytvořit nebo aktualizovat lístek podpory. Můžete vytvořit lístek podpory pro technické problémy, problémy s fakturací nebo kvótami nebo problémy související se správou předplatného. U stávajících lístků podpory můžete aktualizovat závažnost, podrobnosti kontaktu a komunikace. |
    
1. Vytvořte nový soubor **ReaderSupportRole.json**.

1. Otevřete soubor ReaderSupportRole.json v editoru a přidejte do něj následující JSON.

    Informace o různých vlastnostech najdete v tématu [vlastní role Azure](custom-roles.md).

    ```json
    {
      "Name": "",
      "IsCustom": true,
      "Description": "",
      "Actions": [],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId1}"
      ]
    }
    ```
    
1. Do vlastnosti `Actions` přidejte následující operace. Tyto akce umožní uživateli zobrazit vše v předplatném a vytvářet lístky podpory.

    ```
    "*/read",
    "Microsoft.Support/*"
    ```

1. Získejte ID vašeho předplatného pomocí příkazu [az account list](/cli/azure/account#az-account-list).

    ```azurecli
    az account list --output table
    ```

1. Ve vlastnosti `AssignableScopes` nahraďte `{subscriptionId1}` za ID vašeho předplatného.

    Musíte přidat explicitní ID předplatných, jinak tuto roli nebudete moct importovat do svého předplatného.

1. Změňte vlastnost `Name` na Čtenář lístků podpory a vlastnost `Description` na Zobrazení všeho v předplatném a také otevírání lístků podpory.

    Váš soubor JSON by měl vypadat následovně:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    
1. Pokud chcete vytvořit novou vlastní roli, použijte příkaz [az role definition create](/cli/azure/role/definition#az-role-definition-create) a zadejte soubor JSON s definicí role.

    ```azurecli
    az role definition create --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```

    Nová vlastní role je teď k dispozici a můžete ji přiřadit uživatelům, skupinám nebo instančním objektům stejně jako předdefinované role.

## <a name="list-custom-roles"></a>Výpis vlastních rolí

- Pokud chcete vypsat všechny vaše vlastní role, použijte příkaz [az role definition list](/cli/azure/role/definition#az-role-definition-list) s parametrem `--custom-role-only`.

    ```azurecli
    az role definition list --custom-role-only true
    ```
    
    ```Output
    [
      {
        "additionalProperties": {},
        "assignableScopes": [
          "/subscriptions/00000000-0000-0000-0000-000000000000"
        ],
        "description": "View everything in the subscription and also open support tickets.",
        "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
        "name": "22222222-2222-2222-2222-222222222222",
        "permissions": [
          {
            "actions": [
              "*/read",
              "Microsoft.Support/*",
              "Microsoft.Resources/deployments/*",
              "Microsoft.Insights/diagnosticSettings/*/read"
            ],
            "additionalProperties": {},
            "dataActions": [],
            "notActions": [],
            "notDataActions": []
          }
        ],
        "roleName": "Reader Support Tickets",
        "roleType": "CustomRole",
        "type": "Microsoft.Authorization/roleDefinitions"
      }
    ]
    ```
    
    Vlastní roli můžete zobrazit také na webu Azure Portal.

    ![snímek obrazovky webu Azure Portal s importovanou vlastní rolí](./media/tutorial-custom-role-cli/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Aktualizace vlastní role

Pokud chcete aktualizovat vlastní roli, aktualizujte soubor JSON a pak aktualizujte vlastní roli.

1. Otevřete soubor ReaderSupportRole.json.

1. Do vlastnosti `Actions` přidejte operaci `"Microsoft.Resources/deployments/*"` umožňující vytváření a správu nasazení skupiny prostředků. Nezapomeňte vložit čárku za předchozí operaci.

    Váš aktualizovaný soubor JSON by měl vypadat následovně:

    ```json
    {
      "Name": "Reader Support Tickets",
      "IsCustom": true,
      "Description": "View everything in the subscription and also open support tickets.",
      "Actions": [
        "*/read",
        "Microsoft.Support/*",
        "Microsoft.Resources/deployments/*"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
        
1. Pokud chcete aktualizovat vlastní roli, použijte příkaz [az role definition update](/cli/azure/role/definition#az-role-definition-update) a zadejte aktualizovaný soubor JSON.

    ```azurecli
    az role definition update --role-definition "~/CustomRoles/ReaderSupportRole.json"
    ```

    ```Output
    {
      "additionalProperties": {},
      "assignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ],
      "description": "View everything in the subscription and also open support tickets.",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.Authorization/roleDefinitions/22222222-2222-2222-2222-222222222222",
      "name": "22222222-2222-2222-2222-222222222222",
      "permissions": [
        {
          "actions": [
            "*/read",
            "Microsoft.Support/*",
            "Microsoft.Resources/deployments/*"
          ],
          "additionalProperties": {},
          "dataActions": [],
          "notActions": [],
          "notDataActions": []
        }
      ],
      "roleName": "Reader Support Tickets",
      "roleType": "CustomRole",
      "type": "Microsoft.Authorization/roleDefinitions"
    }
    ```
    
## <a name="delete-a-custom-role"></a>Odstranění vlastní role

- Vlastní roli odstraníte pomocí příkazu [az role definition delete](/cli/azure/role/definition#az-role-definition-delete), pro který zadáte název a ID role.

    ```azurecli
    az role definition delete --name "Reader Support Tickets"
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření nebo aktualizace vlastních rolí Azure pomocí Azure CLI](custom-roles-cli.md)
