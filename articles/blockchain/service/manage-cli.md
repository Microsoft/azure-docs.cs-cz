---
title: Správa služby Azure blockchain pomocí Azure CLI
description: Jak spravovat službu Azure blockchain pomocí Azure CLI
ms.date: 11/22/2019
ms.topic: how-to
ms.reviewer: janders
ms.openlocfilehash: 4b41bc44c9efbf71621fcfba06e668f42caa0f8c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076941"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Správa služby Azure blockchain pomocí Azure CLI

Kromě Azure Portal můžete použít rozhraní příkazového řádku Azure ke správě členů blockchain a uzlů transakcí pro službu Azure blockchain.

Ujistěte se, že máte nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a přihlásili jste se k účtu Azure v nástroji `az login` .

V následujících příkladech nahraďte příklady `<parameter names>` vlastními hodnotami.

## <a name="create-blockchain-member"></a>Vytvořit člena blockchain

Příklad vytvoří člena blockchain ve službě Azure blockchain, která spouští protokol hlavní knihy kvora v nové konsorcium.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{ "location":"<myBlockchainLocation>", "properties": {"password":"<myStrongPassword>", "protocol":"Quorum","consortium":"<myConsortiumName>", "consortiumManagementAccountPassword":"<myConsortiumManagementAccountPassword>", "firewallRules":[{"ruleName":"<myRuleName>","startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}, "sku":{"name":"<skuName>"}}'
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure blockchain. |
| **Jméno** | Jedinečný název, který identifikuje svého člena blockchain služby Azure blockchain. Název se používá pro adresu veřejného koncového bodu. Například, `myblockchainmember.blockchain.azure.com`. |
| **oblasti** | Oblast Azure, ve které je vytvořen člen blockchain. Například, `eastus`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure. |
| **heslo** | Heslo k členskému účtu. Heslo pro členský účet se používá k ověření veřejného koncového bodu člena blockchain pomocí základního ověřování. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslo a 1 speciální znak, který není znak # (#), procenta (%), čárka (,), hvězdička (*), znaková uvozovka ( \` ), dvojité uvozovky ("), jednoduché uvozovky ('), spojovníky (-) a semicolumn (;)|
| **protokol** | Verze Public Preview podporuje kvorum. |
| **konsorci** | Název konsorcia, která se má připojit nebo vytvořit |
| **consortiumManagementAccountPassword** | Heslo pro správu konsorcia. Heslo se používá pro připojení k konsorciu. |
| **ruleName** | Název pravidla povoleného rozsahu IP adres. Volitelný parametr pro pravidla brány firewall.|
| **startIpAddress** | Začátek rozsahu IP adres pro seznam povolených. Volitelný parametr pro pravidla brány firewall. |
| **endIpAddress** | Konec rozsahu IP adres pro seznam povolených. Volitelný parametr pro pravidla brány firewall. |
| **skuName** | Typ vrstvy Pro Basic použijte S0 for Standard a B0. |

## <a name="change-blockchain-member-password"></a>Změnit heslo člena blockchain

Příklad změní heslo člena blockchain.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure blockchain. |
| **Jméno** | Název, který identifikuje člena služby Azure blockchain. |
| **heslo** | Heslo k členskému účtu. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslo a 1 speciální znak, který není znak čísla (#), procenta (%), čárka (,), hvězdička (*), znaková uvozovka ( \` ), dvojité uvozovky ("), jednoduché uvozovky ('), pomlčky (-) a středník (;) |

## <a name="create-transaction-node"></a>Vytvořit uzel transakce

Vytvoří uzel transakce v existujícím členu blockchain. Přidáním uzlů transakce můžete zvýšit izolaci zabezpečení a distribuovat zatížení. Například můžete mít koncový bod uzlu transakce pro různé klientské aplikace.

```azurecli
az resource create \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --is-full-object \
                     --properties '{"location":"<myRegion>", "properties":{"password":"<myStrongPassword>", "firewallRules":[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]}}'
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure blockchain. |
| **Jméno** | Název členu blockchain služby Azure blockchain, který obsahuje také název nového transakčního uzlu. |
| **oblasti** | Oblast Azure, ve které je vytvořen člen blockchain. Například, `eastus`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure. |
| **heslo** | Heslo uzlu transakce. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslo a 1 speciální znak, který není znak čísla (#), procenta (%), čárka (,), hvězdička (*), znaková uvozovka ( \` ), dvojité uvozovky ("), jednoduché uvozovky ('), pomlčky (-) a středník (;) |
| **ruleName** | Název pravidla povoleného rozsahu IP adres. Volitelný parametr pro pravidla brány firewall. |
| **startIpAddress** | Začátek rozsahu IP adres pro seznam povolených. Volitelný parametr pro pravidla brány firewall. |
| **endIpAddress** | Konec rozsahu IP adres pro seznam povolených. Volitelný parametr pro pravidla brány firewall.|

## <a name="change-transaction-node-password"></a>Změnit heslo uzlu transakce

Příklad změní heslo uzlu transakce.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.password='<myStrongPassword>'
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, ve které existují prostředky služby Azure blockchain. |
| **Jméno** | Název členu blockchain služby Azure blockchain, který obsahuje také název nového transakčního uzlu. |
| **heslo** | Heslo uzlu transakce. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslo a 1 speciální znak, který není znak čísla (#), procenta (%), čárka (,), hvězdička (*), znaková uvozovka ( \` ), dvojité uvozovky ("), jednoduché uvozovky ('), pomlčky (-) a středník (;) |

## <a name="change-consortium-management-account-password"></a>Změnit heslo účtu správy konsorcia

Účet správy konsorcia se používá pro správu členství v konsorciu. Každý člen je jednoznačně identifikovaný účtem správy konsorcia a heslo tohoto účtu můžete změnit pomocí následujícího příkazu.

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.consortiumManagementAccountPassword='<myConsortiumManagementAccountPassword>' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure blockchain. |
| **Jméno** | Název, který identifikuje člena služby Azure blockchain. |
| **consortiumManagementAccountPassword** | Heslo účtu správy konsorcia. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslo a 1 speciální znak, který není znak čísla (#), procenta (%), čárka (,), hvězdička (*), znaková uvozovka ( \` ), dvojité uvozovky ("), jednoduché uvozovky ('), pomlčky (-) a středník (;) |
  
## <a name="update-firewall-rules"></a>Aktualizovat pravidla brány firewall

```azurecli
az resource update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers \
                     --set properties.firewallRules='[{"ruleName":"<myRuleName>", "startIpAddress":"<myStartIpAddress>", "endIpAddress":"<myEndIpAddress>"}]' \
                     --remove properties.consortiumManagementAccountAddress
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, ve které existují prostředky služby Azure blockchain. |
| **Jméno** | Název členu blockchain služby Azure blockchain. |
| **ruleName** | Název pravidla povoleného rozsahu IP adres. Volitelný parametr pro pravidla brány firewall.|
| **startIpAddress** | Začátek rozsahu IP adres pro seznam povolených. Volitelný parametr pro pravidla brány firewall.|
| **endIpAddress** | Konec rozsahu IP adres pro seznam povolených. Volitelný parametr pro pravidla brány firewall.|

## <a name="list-api-keys"></a>Vypsat klíče rozhraní API

Klíče rozhraní API je možné použít pro přístup k uzlům podobně jako uživatelské jméno a heslo. K dispozici jsou dva klíče rozhraní API pro podporu rotace klíče. K výpisu klíčů rozhraní API použijte následující příkaz.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, ve které existují prostředky služby Azure blockchain. |
| **Jméno** | Název členu blockchain služby Azure blockchain, který obsahuje také název nového transakčního uzlu. |

## <a name="regenerate-api-keys"></a>Znovu vygenerovat klíče rozhraní API

K opětovnému vygenerování klíčů rozhraní API použijte následující příkaz.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "regenerateApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers \
                            --request-body '{"keyName":"<keyValue>"}'
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, ve které existují prostředky služby Azure blockchain. |
| **Jméno** | Název členu blockchain služby Azure blockchain, který obsahuje také název nového transakčního uzlu. |
| **Klíče** | Nahraďte \<keyValue\> buď klíč1 nebo key2. |

## <a name="delete-a-transaction-node"></a>Odstraní uzel transakce.

Příklad odstraní uzel transakcí členů blockchain.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, ve které existují prostředky služby Azure blockchain. |
| **Jméno** | Název členu blockchain služby Azure blockchain, který obsahuje také název transakčního uzlu, který se má odstranit. |

## <a name="delete-a-blockchain-member"></a>Odstranění člena blockchain

Příklad odstraní člen blockchain.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, ve které existují prostředky služby Azure blockchain. |
| **Jméno** | Název člena blockchain služby Azure blockchain, který se má odstranit |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Udělení přístupu pro uživatele Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametr | Popis |
|---------|-------------|
| **role** | Název role Azure AD. |
| **zmocněn** | ID uživatele Azure AD. Například `user@contoso.com`. |
| **oboru** | Rozsah přiřazení role Může to být buď člen blockchain nebo uzel transakce. |

**Případě**

Udělit přístup k uzlu pro uživatele Azure AD k blockchain **členu**:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Případě**

Udělit přístup k uzlu pro uživatele Azure AD na **uzel blockchain transakce**:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Udělení přístupu k uzlu pro roli skupiny nebo aplikace Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parametr | Popis |
|---------|-------------|
| **role** | Název role Azure AD. |
| **nabyvatel – ID objektu** | ID skupiny nebo ID aplikace služby Azure AD. |
| **oboru** | Rozsah přiřazení role Může to být buď člen blockchain nebo uzel transakce. |

**Případě**

Udělení přístupu k uzlu pro **roli aplikace**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Odebrat přístup k uzlu Azure AD

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametr | Popis |
|---------|-------------|
| **role** | Název role Azure AD. |
| **zmocněn** | ID uživatele Azure AD. Například `user@contoso.com`. |
| **oboru** | Rozsah přiřazení role Může to být buď člen blockchain nebo uzel transakce. |

## <a name="next-steps"></a>Další kroky

Naučte se [Konfigurovat uzly transakcí služby Azure blockchain pomocí Azure Portal](configure-transaction-nodes.md).
