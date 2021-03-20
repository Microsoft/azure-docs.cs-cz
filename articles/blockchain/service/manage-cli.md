---
title: Správa služby Azure blockchain pomocí Azure CLI
description: Jak spravovat službu Azure blockchain pomocí Azure CLI
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 36b012c486c0c7d3303a81998e88f1605999c899
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87170856"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Správa služby Azure blockchain pomocí Azure CLI

Kromě Azure Portal můžete použít rozhraní příkazového řádku Azure ke správě členů blockchain a uzlů transakcí pro službu Azure blockchain.

## <a name="launch-azure-cloud-shell"></a>Spuštění služby Azure Cloud Shell

Azure Cloud Shell je bezplatné interaktivní prostředí, které můžete použít k provedení kroků v tomto článku. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem.

Pokud chcete otevřít Cloud Shell, vyberte položku **Vyzkoušet** v pravém horním rohu bloku kódu. Cloud Shell můžete spustit také na samostatné kartě prohlížeče tak, že přejdete na [https://shell.azure.com/bash](https://shell.azure.com/bash) . Zkopírujte bloky kódu výběrem možnosti **Kopírovat**, vložte je do služby Cloud Shell a potom je spusťte stisknutím klávesy Enter.

Pokud dáváte přednost instalaci a používání rozhraní příkazového řádku místně, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="prepare-your-environment"></a>Příprava prostředí

1. Přihlaste se.

    Pokud používáte místní instalaci rozhraní příkazového řádku, přihlaste se pomocí příkazu [az login](/cli/azure/reference-index#az-login).

    ```azurecli
    az login
    ```

    Dokončete proces ověřování podle kroků zobrazených v terminálu.

1. Nainstalujte rozšíření Azure CLI.

    Když pracujete s odkazy na rozšíření rozhraní příkazového řádku Azure (Azure CLI), musíte nejdřív rozšíření nainstalovat.  Rozšíření rozhraní příkazového řádku Azure poskytují přístup k experimentálním a předběžným příkazům, které ještě nebyly dodány jako součást základního rozhraní příkazového řádku.  Další informace o rozšířeních, včetně aktualizace a odinstalace, najdete v tématu [Využití rozšíření v Azure CLI](/cli/azure/azure-cli-extensions-overview).

    Nainstalujte [rozšíření pro službu Azure blockchain](/cli/azure/ext/blockchain/blockchain) spuštěním následujícího příkazu:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Vytvořit člena blockchain

Příklad [vytvoří člena blockchain](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) ve službě Azure blockchain, která spouští protokol hlavní knihy kvora v nové konsorcium.

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure blockchain. |
| **Jméno** | Jedinečný název, který identifikuje svého člena blockchain služby Azure blockchain. Název se používá pro adresu veřejného koncového bodu. Například, `myblockchainmember.blockchain.azure.com`. |
| **oblasti** | Oblast Azure, ve které je vytvořen člen blockchain. Například, `eastus`. Vyberte umístění co nejblíže vašim uživatelům nebo vašim dalším aplikacím Azure. Funkce nemusí být k dispozici v některých oblastech. |
| **heslo** | Heslo pro výchozí uzel transakce člena Při připojování k výchozímu koncovému bodu transakčního uzlu blockchain člena použijte heslo pro základní ověřování. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslo a 1 speciální znak, který není znak # (#), procenta (%), čárka (,), hvězdička (*), znaková uvozovka ( \` ), dvojité uvozovky ("), jednoduché uvozovky ('), spojovníky (-) a semicolumn (;)|
| **protokol** | Protokol blockchain V současné době je protokol *kvora* podporován. |
| **konsorci** | Název konsorcia, která se má připojit nebo vytvořit Další informace o konsorciích najdete v článku [konsorcium služby Azure blockchain](consortium.md). |
| **Konsorcium-Správa-účet-heslo** | Heslo účtu konsorcia se označuje také jako heslo k členskému účtu. Heslo pro členský účet slouží k šifrování privátního klíče pro účet Ethereem, který je vytvořen pro vašeho člena. Použijete členský účet a heslo členského účtu pro správu konsorcia. |
| **skladové** | Typ vrstvy *Standard* nebo *Basic*. Využijte *základní* vrstvu pro vývoj, testování a kontrolu konceptů. Použijte úroveň *Standard* pro nasazení produkčních úrovní. Úroveň *Standard* byste měli použít také v případě, že používáte blockchain data Manager nebo odesíláte velký objem privátních transakcí. Změna cenové úrovně mezi základními a standardními po vytvoření členů není podporována. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Změna hesel členů blockchain nebo pravidel brány firewall

Příklad [aktualizuje heslo člena blockchain](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update), heslo pro správu konsorcia a pravidlo brány firewall.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure blockchain. |
| **Jméno** | Název, který identifikuje člena služby Azure blockchain. |
| **heslo** | Heslo pro výchozí uzel transakce člena Při připojování k výchozímu koncovému bodu transakčního uzlu blockchain člena použijte heslo pro základní ověřování. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslo a 1 speciální znak, který není znak # (#), procenta (%), čárka (,), hvězdička (*), znaková uvozovka ( \` ), dvojité uvozovky ("), jednoduché uvozovky ('), spojovníky (-) a semicolumn (;)|
| **Konsorcium-Správa-účet-heslo** | Heslo účtu konsorcia se označuje také jako heslo k členskému účtu. Heslo pro členský účet slouží k šifrování privátního klíče pro účet Ethereem, který je vytvořen pro vašeho člena. Použijete členský účet a heslo členského účtu pro správu konsorcia. |
| **Brána firewall – pravidla** | Počáteční a koncová IP adresa pro seznam povolených IP adres. |

## <a name="create-transaction-node"></a>Vytvořit uzel transakce

[Vytvoří uzel transakce](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create) v existujícím členu blockchain. Přidáním uzlů transakce můžete zvýšit izolaci zabezpečení a distribuovat zatížení. Například můžete mít koncový bod uzlu transakce pro různé klientské aplikace.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, kde se vytvářejí prostředky služby Azure blockchain. |
| **oblasti** | Oblast Azure člena blockchain |
| **název členu** | Název, který identifikuje člena služby Azure blockchain. |
| **heslo** | Heslo pro uzel transakce Při připojování k veřejnému koncovému bodu uzlu transakce použijte heslo pro základní ověřování. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslo a 1 speciální znak, který není znak # (#), procenta (%), čárka (,), hvězdička (*), znaková uvozovka ( \` ), dvojité uvozovky ("), jednoduché uvozovky ('), spojovníky (-) a semicolumn (;)|
| **Jméno** | Název uzlu transakce. |

## <a name="change-transaction-node-password"></a>Změnit heslo uzlu transakce

Příklad [aktualizuje heslo uzlu transakce](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update) .

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, ve které existují prostředky služby Azure blockchain. |
| **název členu** | Název, který identifikuje člena služby Azure blockchain. |
| **heslo** | Heslo pro uzel transakce Při připojování k veřejnému koncovému bodu uzlu transakce použijte heslo pro základní ověřování. Heslo musí splňovat tři z následujících čtyř požadavků: délka musí být v rozmezí 12 & 72 znaků, 1 malé písmeno, 1 velké písmeno, 1 číslo a 1 speciální znak, který není znak # (#), procenta (%), čárka (,), hvězdička (*), znaková uvozovka ( \` ), dvojité uvozovky ("), jednoduché uvozovky ('), spojovníky (-) a semicolumn (;)|
| **Jméno** | Název uzlu transakce. |

## <a name="list-api-keys"></a>Vypsat klíče rozhraní API

Klíče rozhraní API je možné použít pro přístup k uzlům podobně jako uživatelské jméno a heslo. K dispozici jsou dva klíče rozhraní API pro podporu rotace klíče. K [výpisu klíčů rozhraní API](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key)použijte následující příkaz.

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, ve které existují prostředky služby Azure blockchain. |
| **Jméno** | Název členu blockchain služby Azure blockchain |

## <a name="regenerate-api-keys"></a>Znovu vygenerovat klíče rozhraní API

K [opětovnému vygenerování klíčů rozhraní API](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key)použijte následující příkaz.

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, ve které existují prostředky služby Azure blockchain. |
| **Jméno** | Název členu blockchain služby Azure blockchain. |
| **Klíče** | Nahraďte \<keyValue\> buď klíč1, key2, nebo obojím. |

## <a name="delete-a-transaction-node"></a>Odstraní uzel transakce.

Příklad [odstraní uzel transakcí členů blockchain](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Parametr | Popis |
|---------|-------------|
| **Skupina prostředků** | Název skupiny prostředků, ve které existují prostředky služby Azure blockchain. |
| **název členu** | Název členu blockchain služby Azure blockchain, který obsahuje také název transakčního uzlu, který se má odstranit. |
| **Jméno** | Název transakčního uzlu, který se má odstranit |

## <a name="delete-a-blockchain-member"></a>Odstranění člena blockchain

Příklad [odstraní člen blockchain](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

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

**Příklad:**

Udělit přístup k uzlu pro uživatele Azure AD k blockchain **členu**:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Příklad:**

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

**Příklad:**

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
