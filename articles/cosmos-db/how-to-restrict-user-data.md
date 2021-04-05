---
title: Omezení přístupu uživatelů k datovým operacím pouze pomocí Azure Cosmos DB
description: Přečtěte si, jak omezit přístup k operacím s daty jenom pomocí Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 61e8ee2cbcd1252704b2d1e220fcfaaff17c2f28
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93085985"
---
# <a name="restrict-user-access-to-data-operations-in-azure-cosmos-db"></a>Omezení přístupu uživatelů k operacím s daty v Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

V Azure Cosmos DB existují dva způsoby, jak ověřit interakce s databázovou službou:

- použití identity Azure Active Directory při interakci s Azure Portal,
- použití [klíčů](database-security.md#primary-keys) Azure Cosmos DB nebo [tokenů prostředků](secure-access-to-data.md#resource-tokens) při vydávání volání z rozhraní API a sad SDK.

Každá metoda ověřování poskytuje přístup k různým sadám operací s nějakým překrytím:

:::image type="content" source="./media/how-to-restrict-user-data/operations.png" alt-text="Rozdělení operací na typ ověřování" border="false":::

V některých scénářích můžete chtít, aby někteří uživatelé vaší organizace mohli provádět operace s daty (které jsou požadavky CRUD a dotazy). To je obvykle případ pro vývojáře, kteří nepotřebují vytvářet ani odstraňovat prostředky, nebo měnit zřízenou propustnost kontejnerů, na kterých pracují.

Přístup můžete omezit použitím následujících kroků:
1. Vytvoření vlastní role Azure Active Directory pro uživatele, kterým chcete omezit přístup. Vlastní role služby Active Directory by měla mít jemně odstupňovanou úroveň přístupu k operacím s využitím [podrobných akcí](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)Azure Cosmos DB.
1. Nepovoluje se spouštění nedatových operací s klíči. To můžete dosáhnout omezením těchto operací na Azure Resource Manager volání.

Další části tohoto článku ukazují, jak provést tyto kroky.

> [!NOTE]
> Aby bylo možné spustit příkazy v dalších částech, je nutné nainstalovat Azure PowerShell modulu 3.0.0 nebo novějším a také [roli vlastníka Azure](../role-based-access-control/built-in-roles.md#owner) v předplatném, které chcete upravit.

V skriptech PowerShell v dalších částech nahraďte následující zástupné symboly hodnotami, které jsou specifické pro vaše prostředí:
- `$MySubscriptionId` – ID předplatného, které obsahuje účet Azure Cosmos, u kterého chcete omezit oprávnění. Příklad: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName` – Skupina prostředků obsahující účet Azure Cosmos. Příklad: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName` – Název vašeho účtu Azure Cosmos. Příklad: `mycosmosdbsaccount`.
- `$MyUserName` – Přihlášení ( username@domain ) uživatele, pro kterého chcete omezit přístup. Příklad: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Vyberte své předplatné Azure.

Příkazy Azure PowerShell vyžadují přihlášení a výběr předplatného, ve kterém se příkazy spouštějí:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Vytvoření vlastní role Azure Active Directory

Následující skript vytvoří přiřazení role Azure Active Directory s přístupem pouze ke klíčům pro účty Azure Cosmos. Role je založená na [vlastních rolích Azure](../role-based-access-control/custom-roles.md) a [podrobných akcích pro Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Tyto role a akce jsou součástí `Microsoft.DocumentDB` oboru názvů Azure Active Directory.

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

1. Spuštěním následujících příkazů vytvořte přiřazení role a přiřaďte ho uživateli:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Zakázat provádění operací bez dat

Následující příkazy odeberou možnost používat klíče k těmto akcím:
- vytváření, úpravy a odstraňování prostředků
- Aktualizujte nastavení kontejneru (včetně zásad indexování, propustnosti atd.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Další kroky

- Další informace o [řízení přístupu na základě role Cosmos DB](role-based-access-control.md)
- Přehled [zabezpečeného přístupu k datům v Cosmos DB](secure-access-to-data.md)
