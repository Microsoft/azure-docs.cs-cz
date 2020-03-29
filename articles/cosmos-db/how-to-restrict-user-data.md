---
title: Omezení přístupu uživatelů k datovým operacím jenom pomocí Azure Cosmos DB
description: Zjistěte, jak omezit přístup k datovým operacím jenom pomocí Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980371"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Omezení přístupu uživatelů jenom na datové operace

V Azure Cosmos DB existují dva způsoby ověření vašich interakcí s databázovou službou:
- používání identity Služby Azure Active Directory při interakci s portálem Azure,
- pomocí [klíčů](secure-access-to-data.md#master-keys) Azure Cosmos DB nebo [tokenů prostředků](secure-access-to-data.md#resource-tokens) při vydávání volání z api a sad SDK.

Každá metoda ověřování poskytuje přístup k různým množinám operací s určitým i překrývatím: ![Rozdělení operací podle typu ověřování](./media/how-to-restrict-user-data/operations.png)

V některých případech můžete chtít omezit některé uživatele vaší organizace provádět operace dat (to je crud požadavky a dotazy) pouze. To je obvykle případ pro vývojáře, kteří nepotřebují vytvářet nebo odstraňovat prostředky nebo změnit zřízenou propustnost kontejnerů, na kterých pracují.

Přístup můžete omezit použitím následujících kroků:
1. Vytvoření vlastní role služby Azure Active Directory pro uživatele, kterým chcete omezit přístup. Vlastní role služby Active Directory by měla mít jemně odstupňovanou úroveň přístupu k operacím pomocí [podrobných akcí služby](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)Azure Cosmos DB .
1. Nepovolování provádění nedatových operací pomocí klíčů. Toho lze dosáhnout omezením těchto operací pouze na volání Azure Resource Manager.

Následující části tohoto článku ukazují, jak provést tyto kroky.

> [!NOTE]
> Chcete-li provést příkazy v dalších částech, je potřeba nainstalovat Modul Azure PowerShell 3.0.0 nebo novější, stejně jako [role vlastníka Azure](../role-based-access-control/built-in-roles.md#owner) na předplatné, které se pokoušíte upravit.

Ve skriptech prostředí PowerShell v následujících částech nahraďte následující zástupné symboly hodnotami specifickými pro vaše prostředí:
- `$MySubscriptionId`- ID předplatného, které obsahuje účet Azure Cosmos, kde chcete omezit oprávnění. Například: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName`- Skupina prostředků obsahující účet Azure Cosmos. Například: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName`- Název vašeho účtu Azure Cosmos. Například: `mycosmosdbsaccount`.
- `$MyUserName`- Přihlášeníusername@domain( ) uživatele, pro kterého chcete omezit přístup. Například: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Vyberte své předplatné Azure.

Příkazy Azure PowerShellu vyžadují přihlášení a výběr předplatného pro spuštění příkazů:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Vytvoření vlastní role služby Azure Active Directory

Následující skript vytvoří přiřazení role Azure Active Directory s přístupem "Key Only" pro účty Azure Cosmos. Role je založená na [vlastních rolích pro prostředky Azure](../role-based-access-control/custom-roles.md) a [podrobné akce pro Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Tyto role a akce `Microsoft.DocumentDB` jsou součástí oboru názvů služby Azure Active Directory.

1. Nejprve vytvořte dokument JSON s názvem `AzureCosmosKeyOnlyAccess.json` s následujícím obsahem:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Chcete-li vytvořit přiřazení role a přiřadit jej uživateli, spusťte následující příkazy:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Zakázat provádění nedatových operací

Následující příkazy odeberou možnost použití klíčů k následujícím možnostem:
- vytváření, úpravy nebo odstraňování prostředků
- nastavení kontejneru (včetně zásad indexování, propustnosti atd.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Další kroky

- Další informace o [řízení přístupu cosmos DB založené na rolích](role-based-access-control.md)
- Získejte přehled o [zabezpečeném přístupu k datům v Cosmos DB](secure-access-to-data.md)
