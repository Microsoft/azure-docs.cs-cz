---
title: Spravovaná identita v úloze ACR
description: Povolte spravovanou identitu pro prostředky Azure v úloze Azure Container Registry, abyste umožnili úloze přístup k dalším prostředkům Azure, včetně dalších privátních registrů kontejnerů.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77111763"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Použití identity spravované v Azure v ACR úlohách 

Povolte [spravovanou identitu pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) v [úloze ACR](container-registry-tasks-overview.md), aby tato úloha mohla získat přístup k jiným prostředkům Azure, aniž by bylo potřeba zadávat nebo spravovat přihlašovací údaje. Pomocí spravované identity můžete například povolit, aby krok úlohy vyčetl nebo načetl image kontejneru do jiného registru.

V tomto článku se dozvíte, jak pomocí rozhraní příkazového řádku Azure povolit spravovanou identitu přiřazenou uživatelem nebo systémem v rámci úlohy ACR. Můžete použít Azure Cloud Shell nebo místní instalaci rozhraní příkazového řádku Azure CLI. Pokud ho chcete používat místně, je potřeba verze 2.0.68 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

V ukázkových příkazech v tomto článku se používají příklady příkazů [AZ ACR Task Create][az-acr-task-create] k vytvoření základní úlohy sestavení image, která umožňuje spravovanou identitu. Ukázkové scénáře přístupu k zabezpečeným prostředkům z úlohy ACR pomocí spravované identity najdete v těchto tématech:

* [Ověřování napříč registry](container-registry-tasks-cross-registry-authentication.md)
* [Přístup k externím prostředkům s tajnými kódy uloženými v Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Proč používat spravovanou identitu?

Spravovaná identita pro prostředky Azure poskytuje vybrané služby Azure s automaticky spravovanou identitou v Azure Active Directory. Pomocí spravované identity můžete nakonfigurovat úlohu ACR, aby tato úloha mohla přistupovat k jiným zabezpečeným prostředkům Azure bez předání přihlašovacích údajů v krocích úkolu.

Spravované identity mají dva typy:

* *Uživatelsky přiřazené identity*, které můžete přiřadit k více prostředkům a uchovávat tak dlouho, dokud budete chtít. Uživatelsky přiřazené identity jsou momentálně ve verzi Preview.

* *Identita přiřazená systémem*, která je jedinečná pro konkrétní prostředek, jako je například úloha ACR a která trvá po dobu života daného prostředku.

V úloze ACR můžete povolit jeden nebo oba typy identity. Udělte identitě přístup k jinému prostředku, stejně jako jakémukoli objektu zabezpečení. Když se úloha spustí, použije identitu pro přístup k prostředku v libovolném kroku úlohy, které vyžadují přístup.

## <a name="steps-to-use-a-managed-identity"></a>Postup použití spravované identity

Podle těchto kroků vysoké úrovně použijte spravovanou identitu s ACR úlohou.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (volitelné) vytvořte uživatelem přiřazenou identitu.

Pokud plánujete použít uživatelem přiřazenou identitu, použijte existující identitu nebo vytvořte identitu pomocí rozhraní příkazového řádku Azure CLI nebo dalších nástrojů Azure. Použijte například příkaz [AZ identity Create][az-identity-create] . 

Pokud máte v úmyslu používat jenom identitu přiřazenou systémem, tento krok přeskočte. Při vytváření úkolu ACR vytvoříte identitu přiřazenou systémem.

### <a name="2-enable-identity-on-an-acr-task"></a>2. povolení identity v úloze ACR

Když vytvoříte úlohu ACR, můžete volitelně povolit uživatelsky přiřazenou identitu, identitu přiřazenou systémem nebo obojí. Například parametr předejte `--assign-identity` při spuštění příkazu [AZ ACR Task Create][az-acr-task-create] v Azure CLI.

Pokud chcete povolit identitu přiřazenou systémem, předejte `--assign-identity` bez hodnoty nebo `assign-identity [system]` . Následující příklad příkazu vytvoří úlohu pro Linux z veřejného úložiště GitHub, které vytvoří `hello-world` bitovou kopii a povolí spravovanou identitu přiřazenou systémem:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Pokud chcete povolit uživatelsky přiřazenou identitu, předejte jí `--assign-identity` hodnotu *ID prostředku* identity. Následující příklad příkazu vytvoří úlohu pro Linux z veřejného úložiště GitHub, které vytvoří `hello-world` image a umožní uživateli přiřazenou spravovanou identitu:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

ID prostředku identity můžete získat spuštěním příkazu [AZ identity show][az-identity-show] . ID prostředku pro ID *myUserAssignedIdentity* ve skupině prostředků *myResourceGroup* má formu: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. udělte identitám oprávnění k přístupu k dalším prostředkům Azure.

V závislosti na požadavcích úlohy udělte oprávnění identitě přístup k dalším prostředkům Azure. Příklady:

* Přiřaďte spravované identitě roli s oprávněním Pull, push a pull nebo jinými oprávněními k cílovému registru kontejneru v Azure. Úplný seznam rolí registru najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md). 
* Přiřaďte roli spravované identity pro čtení tajných klíčů v trezoru klíčů Azure.

Ke správě přístupu na základě rolí k prostředkům použijte rozhraní příkazového [řádku Azure CLI](../role-based-access-control/role-assignments-cli.md) nebo jiné nástroje Azure. Například spuštěním příkazu [AZ role Assignment Create][az-role-assignment-create] přiřaďte identitu roli prostředku. 

Následující příklad přiřadí spravované identitě oprávnění k vyžádání z registru kontejneru. Příkaz určuje *ID objektu zabezpečení* identity úlohy a *ID prostředku* cílového registru.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (volitelné) přidejte k úloze přihlašovací údaje.

Pokud úloha potřebuje přihlašovací údaje pro vyžádání nebo vložení imagí do jiného vlastního registru nebo pro přístup k jiným prostředkům, přidejte k úloze přihlašovací údaje. Spuštěním příkazu [AZ ACR Task Credential Add][az-acr-task-credential-add] přidejte přihlašovací údaje a předejte parametr, `--use-identity` aby označoval, že identita má přístup k přihlašovacím údajům. 

Pokud například chcete přidat přihlašovací údaje pro identitu přiřazenou systémem k ověřování pomocí služby Azure Container Registry *targetregistry*, předejte `use-identity [system]` :

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Chcete-li přidat pověření pro uživatelem přiřazenou identitu pro ověření pomocí registru *targetregistry*, předejte `use-identity` hodnotu *ID klienta* identity. Například:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

ID klienta identity můžete získat spuštěním příkazu [AZ identity show][az-identity-show] . ID klienta je identifikátor GUID formuláře `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .

### <a name="5-run-the-task"></a>5. Spusťte úlohu.

Po nakonfigurování úlohy se spravovanou identitou spusťte úlohu. Chcete-li například otestovat jednu z úloh vytvořených v tomto článku, ručně ji aktivujte pomocí příkazu [AZ ACR Task Run][az-acr-task-run] . Pokud jste nakonfigurovali další, automatizované triggery úloh, úloha se spustí, když se automaticky aktivuje.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak v úloze ACR povolit a použít spravovanou identitu přiřazenou uživatelem nebo systémem. Příklady scénářů přístupu k zabezpečeným prostředkům z ACR úlohy pomocí spravované identity najdete v těchto tématech:

* [Ověřování napříč registry](container-registry-tasks-cross-registry-authentication.md)
* [Přístup k externím prostředkům s tajnými kódy uloženými v Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
