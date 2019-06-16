---
title: Správa služby Azure Blockchain s použitím rozhraní příkazového řádku Azure
description: Jak vytvořit a spravovat službu Azure Blockchain v Azure CLI
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: seal
manager: femila
ms.openlocfilehash: d078ca181b2eed4b80d4f12f1c03b42f4e242194
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65154440"
---
# <a name="manage-azure-blockchain-service-with-azure-cli"></a>Správa služby Azure Blockchain pomocí Azure CLI

Kromě webu Azure portal můžete použít rozhraní příkazového řádku Azure k rychlému vytváření a správě blockchain členy a transakce uzly pro vaši službu Azure Blockchain.

Ujistěte se, že máte nainstalovanou nejnovější verzi [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) a jste přihlášení k účtu Azure pomocí `az login`.

V následujících příkladech nahraďte příklad `<parameter names>` vlastními hodnotami.

## <a name="create-blockchain-member"></a>Vytvořit člena blockchain

Příklad vytvoří ve službě Azure Blockchain Service, na kterém běží hlavní knihy protokol kvora v nové consortium blockchain člena.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --is-full-object --properties "{ \"location\": \"<myBlockchainLocation>\", \"properties\": {\"password\": \"<myStrongPassword>\", \"protocol\": \"Quorum\", \"consortium\": \"<myConsortiumName>\", \"consortiumManagementAccountPassword\": \"<myConsortiumManagementAccountPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] }, \"sku\": { \"name\": \"<skuName>\" } }"
```

| Parametr | Popis |
|---------|-------------|
| **resource-group** | Název skupiny prostředků, ve kterém jsou vytvořeny prostředky služeb Azure Blockchain. |
| **name** | Jedinečný název, který identifikuje vaši službu Azure Blockchain blockchain člena. Název se používá pro adresu veřejný koncový bod. Například, `myblockchainmember.blockchain.azure.com`. |
| **location** | Oblasti Azure, ve kterém je vytvořena blockchain člena. Například, `eastus`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure. |
| **Heslo** | Heslo účtu člena. Člen heslo účtu se používá k ověření na člen blockchain veřejný koncový bod pomocí základního ověřování. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí od 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslici a 1 speciální znak, který ne číslo znaku, procentech (%), čárkou (,), star(*), zadní uvozovky (\`), dvojité quote("), jeden uvozovky ('), pomlčky a semicolumn(;)|
| **Protokol** | Ve verzi Public preview podporuje kvora. |
| **consortium** | Název consortium připojit nebo vytvořit. |
| **consortiumManagementAccountPassword** | Heslo pro správu consortium. Heslo se používá pro připojení k konsorcium. |
| **RuleName** | Název pravidla pro rozsah IP adres na seznam povolených. Volitelný parametr pro pravidla brány firewall.|
| **startIpAddress** | Počáteční rozsah IP adres pro přidávání na seznam povolených. Volitelný parametr pro pravidla brány firewall. |
| **endIpAddress** | Koncový rozsah IP adres pro přidávání na seznam povolených. Volitelný parametr pro pravidla brány firewall. |
| **skuName** | Typ vrstvy. Použijte S0 Standard a B0 Basic. |

## <a name="change-blockchain-member-password"></a>Změna hesla člen blockchain

Příklad změní heslo blockchain člena.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.password="<myStrongPassword>" --remove properties.consortiumManagementAccountAddress
```
| Parametr | Popis |
|---------|-------------|
| **resource-group** | Název skupiny prostředků, ve kterém jsou vytvořeny prostředky služeb Azure Blockchain. |
| **name** | Název, který identifikuje vaši službu Azure Blockchain člena. |
| **Heslo** | Heslo účtu člena. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí od 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslici a 1 speciální znak, který ne číslo znaku, procentech (%), čárkou (,), star(*), zadní uvozovky (\`), dvojité quote("), jeden uvozovky ('), pomlčky a semicolon(;). |


## <a name="create-transaction-node"></a>Vytvořit uzel transakce

Vytvořte transakci uzel do existujícího člena blockchain. Přidáním uzlů transakce můžete zvýšit zabezpečení, izolace a distribuci zatížení. Například můžete mít koncový bod transakce uzel pro různé klientské aplikace.

```azurecli
az resource create --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --is-full-object --properties "{ \"location\": \"<myRegion>\", \"properties\": { \"password\": \"<myStrongPassword>\", \"firewallRules\": [ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ] } }"
```

| Parametr | Popis |
|---------|-------------|
| **resource-group** | Název skupiny prostředků, ve kterém jsou vytvořeny prostředky služeb Azure Blockchain. |
| **name** | Název člena blockchain služba Blockchain v Azure, která zahrnuje i nový název uzlu transakce. |
| **location** | Oblasti Azure, ve kterém je vytvořena blockchain člena. Například, `eastus`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure. |
| **Heslo** | Uzel heslo transakce. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí od 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslici a 1 speciální znak, který ne číslo znaku, procentech (%), čárkou (,), star(*), zadní uvozovky (\`), dvojité quote("), jeden uvozovky ('), pomlčky a semicolon(;). |
| **RuleName** | Název pravidla pro rozsah IP adres na seznam povolených. Volitelný parametr pro pravidla brány firewall. |
| **startIpAddress** | Počáteční rozsah IP adres pro přidávání na seznam povolených. Volitelný parametr pro pravidla brány firewall. |
| **endIpAddress** | Koncový rozsah IP adres pro přidávání na seznam povolených. Volitelný parametr pro pravidla brány firewall.|

## <a name="change-transaction-node-password"></a>Změna hesla uzel transakce

Příklad změní heslo uzel transakce.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers  --set properties.password="<myStrongPassword>"
```

| Parametr | Popis |
|---------|-------------|
| **resource-group** | Pokud existují prostředky služeb Azure Blockchain název skupiny prostředků. |
| **name** | Název člena blockchain služba Blockchain v Azure, která zahrnuje i nový název uzlu transakce. |
| **Heslo** | Uzel heslo transakce. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí od 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslici a 1 speciální znak, který ne číslo znaku, procentech (%), čárkou (,), star(*), zadní uvozovky (\`), dvojité quote("), jeden uvozovky ('), pomlčky a semicolon(;). |

## <a name="change-consortium-management-account-password"></a>Heslo účtu consortium správy změn

Účet správy consortium se používá pro správu členství consortium. Každý člen je jedinečně identifikovaný účet správy consortium a můžete změnit heslo tohoto účtu pomocí následujícího příkazu.

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.consortiumManagementAccountPassword="<myConsortiumManagementAccountPassword>" --remove properties.consortiumManagementAccountAddress
```

| Parametr | Popis |
|---------|-------------|
| **resource-group** | Název skupiny prostředků, ve kterém jsou vytvořeny prostředky služeb Azure Blockchain. |
| **name** | Název, který identifikuje vaši službu Azure Blockchain člena. |
| **consortiumManagementAccountPassword** | Heslo účtu správy consortium. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí od 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslici a 1 speciální znak, který ne číslo znaku, procentech (%), čárkou (,), star(*), zadní uvozovky (\`), dvojité quote("), jeden uvozovky ('), pomlčky a semicolon(;). |
  
## <a name="update-firewall-rules"></a>Aktualizovat pravidla brány firewall

```azurecli
az resource update --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers --set properties.firewallRules="[ { \"ruleName\": \"<myRuleName>\", \"startIpAddress\": \"<myStartIpAddress>\", \"endIpAddress\": \"<myEndIpAddress>\" } ]" --remove properties.consortiumManagementAccountAddress
```

| Parametr | Popis |
|---------|-------------|
| **resource-group** | Pokud existují prostředky služeb Azure Blockchain název skupiny prostředků. |
| **name** | Název člena blockchain služba Blockchain v Azure. |
| **RuleName** | Název pravidla pro rozsah IP adres na seznam povolených. Volitelný parametr pro pravidla brány firewall.|
| **startIpAddress** | Počáteční rozsah IP adres pro přidávání na seznam povolených. Volitelný parametr pro pravidla brány firewall.|
| **endIpAddress** | Koncový rozsah IP adres pro přidávání na seznam povolených. Volitelný parametr pro pravidla brány firewall.|

## <a name="list-api-keys"></a>Klíče rozhraní API seznamu

Klíče rozhraní API můžete použít pro přístup k uzlu, podobně jako uživatelské jméno a heslo. Existují dva klíče rozhraní API pro podporu obměny klíče. Použijte následující příkaz pro výpis klíčů rozhraní API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "listApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Popis |
|---------|-------------|
| **resource-group** | Pokud existují prostředky služeb Azure Blockchain název skupiny prostředků. |
| **name** | Název člena blockchain služba Blockchain v Azure, která zahrnuje i nový název uzlu transakce. |

## <a name="regenerate-api-keys"></a>Znovu vygenerovat klíče rozhraní API

Použijte následující příkaz se znovu vygenerovat klíče rozhraní API.

```azurecli
az resource invoke-action --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --action "regenerateApiKeys" --resource-type Microsoft.Blockchain/blockchainMembers --request-body '{"keyName":"<keyValue>"}'
```


| Parametr | Popis |
|---------|-------------|
| **resource-group** | Pokud existují prostředky služeb Azure Blockchain název skupiny prostředků. |
| **name** | Název člena blockchain služba Blockchain v Azure, která zahrnuje i nový název uzlu transakce. |
| **keyName** | Nahraďte \<párů klíč-hodnota\> s key1 a key2. |

## <a name="delete-a-transaction-node"></a>Odstranit uzel transakce

Příklad odstraní uzel blockchain člen transakce.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName>/transactionNodes/<myTransactionNode> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Popis |
|---------|-------------|
| **resource-group** | Pokud existují prostředky služeb Azure Blockchain název skupiny prostředků. |
| **name** | Název člena blockchain služba Blockchain v Azure, která zahrnuje i nový název uzlu transakce má být odstraněn. |

## <a name="delete-a-blockchain-member"></a>Odstraní člena blockchain

Příklad odstraní blockchain člena.

```azurecli
az resource delete --resource-group <myResourceGroup> --name <myMemberName> --resource-type Microsoft.Blockchain/blockchainMembers
```

| Parametr | Popis |
|---------|-------------|
| **resource-group** | Pokud existují prostředky služeb Azure Blockchain název skupiny prostředků. |
| **name** | Název člena služba Blockchain v Azure blockchain odstranit. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Udělení přístupu pro uživatele Azure AD

```azurecli
az role assignment create --role <role> --assignee <assignee> --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Parametr | Popis |
|---------|-------------|
| **Role** | Název role Azure AD. |
| **assignee** | Azure AD ID uživatele. Například `user@contoso.com`. |
| **Obor** | Rozsah přiřazení role. Můžete být členem blockchain transakce uzlu. |

**Příklad:**

Udělit přístup k uzlu pro uživatele Azure AD pro blockchain **člen**:

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Příklad:**

Udělit přístup k uzlu pro uživatele Azure AD pro blockchain **transakce uzel**:

```azurecli
az role assignment create \
  --role "MyRole" \
  --assignee user@contoso.com \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Udělit přístup k uzlu pro Azure AD skupinu nebo aplikaci role

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id>
```
| Parametr | Popis |
|---------|-------------|
| **Role** | Název role Azure AD. |
| **assignee-object-id** | ID skupiny Azure AD nebo aplikací ID. |
| **Obor** | Rozsah přiřazení role. Můžete být členem blockchain transakce uzlu. |

**Příklad:**

Udělit přístup k uzlu pro **aplikační role**

```azurecli
az role assignment create \
  --role "myRole" \
  --assignee-object-id 22222222-2222-2222-2222-222222222222 \
  --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Odebrat přístup k uzlu služby Azure AD

```azurecli
az role assignment delete --role <myRole> --assignee <assignee> --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Parametr | Popis |
|---------|-------------|
| **Role** | Název role Azure AD. |
| **assignee** | Azure AD ID uživatele. Například `user@contoso.com`. |
| **Obor** | Rozsah přiřazení role. Můžete být členem blockchain transakce uzlu. |

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace uzlů transakce služba Blockchain v Azure pomocí webu Azure portal](configure-transaction-nodes.md)
