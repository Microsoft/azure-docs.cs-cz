---
title: Správa služby Azure Blockchain pomocí azure cli
description: Jak spravovat službu Azure Blockchain pomocí azure cli
ms.date: 11/22/2019
ms.topic: article
ms.reviewer: janders
ms.openlocfilehash: ac75be644877905c1517395c1c789b1ea16fd49c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455581"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Správa služby Azure Blockchain pomocí azure cli

Kromě portálu Azure můžete azure cli používat ke správě členů blockchainu a transakčních uzlů pro vaši službu Azure Blockchain.

Ujistěte se, že jste nainstalovali nejnovější [azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli) a `az login`přihlášeni k účtu Azure s .

V následujících příkladech `<parameter names>` nahraďte příklad vlastními hodnotami.

## <a name="create-blockchain-member"></a>Vytvořte člena blockchainu

Příklad vytvoří člena blockchainu ve službě Azure Blockchain Service, který spouští protokol hlavní knihy kvora v novém konsorciu.

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
| **skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure Blockchain Service. |
| **Jméno** | Jedinečný název, který identifikuje vašeho člena blockchainové služby Azure Blockchain Service. Název se používá pro adresu veřejného koncového bodu. Například, `myblockchainmember.blockchain.azure.com`. |
| **Umístění** | Oblast Azure, kde se vytvoří člen blockchainu. Například, `eastus`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure. |
| **heslo** | Heslo členského účtu. Heslo členského účtu se používá k ověření veřejného koncového bodu člena blockchainu pomocí základního ověřování. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být mezi 12 & 72 znaků, 1 malý znak, 1 velký znak, 1 číslo a 1 speciální znak, který\`není znak čísla (#), percent(%), čárka(,), hvězda(*), zpětová citace( ), dvojité uvozovky(), jednoduché uvozovky('), pomlčka (-) a semicolumn(;)|
| **Protokol** | Veřejná verze preview podporuje kvorum. |
| **Konsorcium** | Název konsorcia připojit nebo vytvořit. |
| **konsorciumManagementAccountPassword** | Heslo pro správu konsorcia. Heslo se používá pro připojení ke konsorciu. |
| **název_pravidla** | Název pravidla pro zařazení rozsahu IP adres na seznam povolených adres. Volitelný parametr pro pravidla brány firewall.|
| **startIpAddress** | Začátek rozsahu IP adres pro whitelisting. Volitelný parametr pro pravidla brány firewall. |
| **endIpAddress** | Konec rozsahu IP adres pro seznam povolených adres. Volitelný parametr pro pravidla brány firewall. |
| **skuName** | Typ úrovně. Použijte S0 pro standard a B0 pro základní. |

## <a name="change-blockchain-member-password"></a>Změna hesla člena blockchainu

Příklad změní heslo člena blockchainu.

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
| **skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure Blockchain Service. |
| **Jméno** | Název, který identifikuje vašeho člena služby Azure Blockchain Service. |
| **heslo** | Heslo členského účtu. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být mezi 12 & 72 znaků, 1 malý znak, 1 velký znak, 1 číslo a 1 speciální znak, který\`není znak čísla (#), percent(%), čárka(,), hvězda(*), zpět ovál( ), dvojité uvozovky(), jednoduché uvozovky('), pomlčka (-) a středník(;). |

## <a name="create-transaction-node"></a>Vytvořit uzel transakce

Vytvořte transakční uzel uvnitř existujícího člena blockchainu. Přidáním uzlů transakcí můžete zvýšit izolaci zabezpečení a distribuovat zatížení. Můžete mít například koncový bod uzlu transakce pro různé klientské aplikace.

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
| **skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure Blockchain Service. |
| **Jméno** | Název člena blockchainslužby Azure Blockchain, který také obsahuje název nového uzlu transakce. |
| **Umístění** | Oblast Azure, kde se vytvoří člen blockchainu. Například, `eastus`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure. |
| **heslo** | Heslo uzlu transakce. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být mezi 12 & 72 znaků, 1 malý znak, 1 velký znak, 1 číslo a 1 speciální znak, který\`není znak čísla (#), percent(%), čárka(,), hvězda(*), zpět ovál( ), dvojité uvozovky(), jednoduché uvozovky('), pomlčka (-) a středník(;). |
| **název_pravidla** | Název pravidla pro zařazení rozsahu IP adres na seznam povolených adres. Volitelný parametr pro pravidla brány firewall. |
| **startIpAddress** | Začátek rozsahu IP adres pro whitelisting. Volitelný parametr pro pravidla brány firewall. |
| **endIpAddress** | Konec rozsahu IP adres pro seznam povolených adres. Volitelný parametr pro pravidla brány firewall.|

## <a name="change-transaction-node-password"></a>Změna hesla uzlu transakce

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
| **skupina prostředků** | Název skupiny prostředků, kde existují prostředky služby Azure Blockchain Service. |
| **Jméno** | Název člena blockchainslužby Azure Blockchain, který také obsahuje název nového uzlu transakce. |
| **heslo** | Heslo uzlu transakce. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být mezi 12 & 72 znaků, 1 malý znak, 1 velký znak, 1 číslo a 1 speciální znak, který\`není znak čísla (#), percent(%), čárka(,), hvězda(*), zpět ovál( ), dvojité uvozovky(), jednoduché uvozovky('), pomlčka (-) a středník(;). |

## <a name="change-consortium-management-account-password"></a>Změna hesla účtu správy konsorcia

Účet pro správu konsorcia se používá pro správu členství v konsorciu. Každý člen je jednoznačně identifikován klientským účtem konsorcia a můžete změnit heslo tohoto účtu pomocí následujícího příkazu.

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
| **skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure Blockchain Service. |
| **Jméno** | Název, který identifikuje vašeho člena služby Azure Blockchain Service. |
| **konsorciumManagementAccountPassword** | Heslo účtu pro správu konsorcia. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být mezi 12 & 72 znaků, 1 malý znak, 1 velký znak, 1 číslo a 1 speciální znak, který\`není znak čísla (#), percent(%), čárka(,), hvězda(*), zpět ovál( ), dvojité uvozovky(), jednoduché uvozovky('), pomlčka (-) a středník(;). |
  
## <a name="update-firewall-rules"></a>Aktualizace pravidel brány firewall

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
| **skupina prostředků** | Název skupiny prostředků, kde existují prostředky služby Azure Blockchain Service. |
| **Jméno** | Název člena blockchainové služby Azure Blockchain Service. |
| **název_pravidla** | Název pravidla pro zařazení rozsahu IP adres na seznam povolených adres. Volitelný parametr pro pravidla brány firewall.|
| **startIpAddress** | Začátek rozsahu IP adres pro whitelisting. Volitelný parametr pro pravidla brány firewall.|
| **endIpAddress** | Konec rozsahu IP adres pro seznam povolených adres. Volitelný parametr pro pravidla brány firewall.|

## <a name="list-api-keys"></a>Seznam klíčů rozhraní API

Klíče rozhraní API lze použít pro přístup k uzlu podobný uživatelskému jménu a heslu. Existují dva klíče rozhraní API pro podporu střídání klíčů. Pomocí následujícího příkazu zobrazíte seznam klíčů rozhraní API.

```azurecli
az resource invoke-action \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>/transactionNodes/<myTransactionNode> \
                            --action "listApiKeys" \
                            --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Popis |
|---------|-------------|
| **skupina prostředků** | Název skupiny prostředků, kde existují prostředky služby Azure Blockchain Service. |
| **Jméno** | Název člena blockchainslužby Azure Blockchain, který také obsahuje název nového uzlu transakce. |

## <a name="regenerate-api-keys"></a>Obnovení klíčů rozhraní API

Pomocí následujícího příkazu regenerovat klíče rozhraní API.

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
| **skupina prostředků** | Název skupiny prostředků, kde existují prostředky služby Azure Blockchain Service. |
| **Jméno** | Název člena blockchainslužby Azure Blockchain, který také obsahuje název nového uzlu transakce. |
| **Název_klíče** | \<Nahraďte\> keyValue buď key1 nebo key2. |

## <a name="delete-a-transaction-node"></a>Odstranění uzlu transakce

Příklad odstraní uzel transakce člena blockchainu.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>/transactionNodes/<myTransactionNode> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Popis |
|---------|-------------|
| **skupina prostředků** | Název skupiny prostředků, kde existují prostředky služby Azure Blockchain Service. |
| **Jméno** | Název člena blockchainslužby Azure Blockchain, který také obsahuje název uzlu transakce, který má být odstraněn. |

## <a name="delete-a-blockchain-member"></a>Odstranění člena blockchainu

Příklad odstraní člena blockchainu.

```azurecli
az resource delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Popis |
|---------|-------------|
| **skupina prostředků** | Název skupiny prostředků, kde existují prostředky služby Azure Blockchain Service. |
| **Jméno** | Název člena blockchainové služby Azure Blockchain, který má být odstraněn. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Udělení přístupu uživateli Azure AD

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametr | Popis |
|---------|-------------|
| **Roli** | Název role Azure AD. |
| **postupník** | ID uživatele Služby Azure AD. Například `user@contoso.com`. |
| **Rozsah** | Rozsah přiřazení role. Může být členem blockchainu nebo transakčním uzlem. |

**Příklad:**

Udělit přístup k uzlu pro uživatele Azure AD **pro člena**blockchainu :

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Příklad:**

Udělit uživateli Azure AD přístup k **uzlu transakce**blockchain :

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Udělit přístup uzlu pro skupinu Azure AD nebo roli aplikace

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Parametr | Popis |
|---------|-------------|
| **Roli** | Název role Azure AD. |
| **assignee-object-id** | ID skupiny Azure AD nebo ID aplikace. |
| **Rozsah** | Rozsah přiřazení role. Může být členem blockchainu nebo transakčním uzlem. |

**Příklad:**

Udělit přístup uzlu pro **roli aplikace**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Odebrání přístupu k uzlu Azure AD

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametr | Popis |
|---------|-------------|
| **Roli** | Název role Azure AD. |
| **postupník** | ID uživatele Služby Azure AD. Například `user@contoso.com`. |
| **Rozsah** | Rozsah přiřazení role. Může být členem blockchainu nebo transakčním uzlem. |

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nakonfigurovat uzly transakcí služby Azure Blockchain service pomocí portálu Azure](configure-transaction-nodes.md).
