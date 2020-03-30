---
title: Spravovaná identita v úloze ACR
description: Povolte spravovanou identitu pro prostředky Azure v úloze registru kontejnerů Azure, abyste této úloze umožnili přístup k dalším prostředkům Azure, včetně dalších privátních registrů kontejnerů.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111763"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Použití identity spravované Azure v úlohách ACR 

Povolte [spravovanou identitu pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) v [úloze ACR](container-registry-tasks-overview.md), aby úkol měl přístup k dalším prostředkům Azure, aniž by bylo nutné zajišťovat nebo spravovat přihlašovací údaje. Pomocí spravované identity můžete například povolit krok úlohy k vyhledání nebo nabízení inicií kontejnerů do jiného registru.

V tomto článku se dozvíte, jak pomocí rozhraní příkazového příkazu Azure povolit uživatelem přiřazenou nebo systémem přiřazenou spravovanou identitu na úlohě ACR. Můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Pokud jej chcete používat místně, je vyžadována verze 2.0.68 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli-install].

Pro účely ilustrace příklad příkazy v tomto článku použít [az acr úkol vytvořit][az-acr-task-create] základní úlohu sestavení bitové kopie, která umožňuje spravovanou identitu. Ukázkové scénáře pro přístup k zabezpečeným prostředkům z úkolu ACR pomocí spravované identity naleznete v tématu:

* [Ověřování napříč registry](container-registry-tasks-cross-registry-authentication.md)
* [Přístup k externím prostředkům s tajnými klíči uloženými v úložišti klíčů Azure](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Proč používat spravovanou identitu?

Spravovaná identita pro prostředky Azure poskytuje vybrané služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory. Úlohu ACR můžete nakonfigurovat se spravovanou identitou tak, aby úloha měla přístup k dalším zabezpečeným prostředkům Azure, aniž by byla v krocích úkolů předávána pověření.

Spravované identity jsou dvou typů:

* *Uživatelem přiřazené identity*, které můžete přiřadit k více prostředkům a zachovat tak dlouho, jak chcete. Identity přiřazené uživatelem jsou aktuálně ve verzi Preview.

* *Systémem přiřazená identita*, která je jedinečná pro určitý prostředek, například úlohu ACR, a trvá po dobu životnosti tohoto prostředku.

V úloze ACR můžete povolit jeden nebo oba typy identity. Udělte identitě přístup k jinému prostředku, stejně jako všechny objekty zabezpečení. Při spuštění úlohy používá identitu pro přístup k prostředku v libovolné kroky úlohy, které vyžadují přístup.

## <a name="steps-to-use-a-managed-identity"></a>Postup použití spravované identity

Chcete-li použít spravovanou identitu s úlohou ACR, postupujte podle těchto kroků vysoké úrovně.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Volitelné) Vytvoření identity přiřazené uživateli

Pokud máte v plánu použít identitu přiřazenou uživateli, použijte existující identitu nebo vytvořte identitu pomocí rozhraní příkazového příkazového příkazu Azure nebo jiných nástrojů Azure. Použijte například příkaz [az identity create.][az-identity-create] 

Pokud plánujete použít pouze systémově přiřazenou identitu, tento krok přeskočte. Při vytváření úlohy ACR vytvoříte systémově přiřazenou identitu.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Povolení identity na úkolu ACR

Při vytváření úlohy ACR volitelně povolte identitu přiřazenou uživatelem, systémově přiřazenou identitu nebo obojí. Například předat `--assign-identity` parametr při spuštění příkazu [az acr vytvoření úlohy][az-acr-task-create] v rozhraní příkazu Azure CLI.

Chcete-li povolit systémově `--assign-identity` přiřazenou identitu, přejděte bez hodnoty nebo `assign-identity [system]`. Následující příklad příkazu vytvoří úlohu Linuxu z veřejného `hello-world` úložiště GitHub, které vytvoří bitovou kopii a povolí spravovanou identitu přiřazenou systémem:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Chcete-li povolit identitu `--assign-identity` přiřazenou uživateli, předavte s hodnotou *ID prostředku* identity. Následující příkaz příkladu vytvoří úlohu Linuxu z veřejného `hello-world` úložiště GitHub, které vytvoří bitovou kopii a povolí spravovanou identitu přiřazenou uživateli:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

ID prostředku identity můžete získat spuštěním příkazu [az identity show.][az-identity-show] ID prostředku pro ID *myUserAssignedIdentity* ve skupině prostředků *myResourceGroup* je formuláře: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Udělení oprávnění k identitě pro přístup k dalším prostředkům Azure

V závislosti na požadavcích úlohy udělte oprávnění k identitě pro přístup k dalším prostředkům Azure. Příklady obsahují:

* Přiřaďte spravovanou identitu roli s vyžádat, nabízená a vyžádat nebo jiná oprávnění do registru cílového kontejneru v Azure. Úplný seznam rolí registru najdete v tématu [Role a oprávnění registru kontejnerů Azure](container-registry-roles.md). 
* Přiřaďte spravovanou identitu roli ke čtení tajných kódů v trezoru klíčů Azure.

Pomocí [azure cli](../role-based-access-control/role-assignments-cli.md) nebo jiných nástrojů Azure ke správě přístupu k prostředkům na základě rolí. Spusťte například příkaz [vytvořit přiřazení role az][az-role-assignment-create] a přiřaďte roli identity prostředku. 

Následující příklad přiřadí spravovanou identitu oprávnění k vyžádat z registru kontejneru. Příkaz určuje *ID jistiny* identity úlohy a *ID prostředku* cílového registru.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Volitelné) Přidání pověření k úkolu

Pokud váš úkol potřebuje pověření k vytahování nebo nabízení obrázků do jiného vlastního registru nebo k přístupu k jiným prostředkům, přidejte k úloze pověření. Spusťte [příkaz add úlohy az acr][az-acr-task-credential-add] pro `--use-identity` přidání pověření a předavažte parametr, který označuje, že identita může přistupovat k pověřením. 

Chcete-li například přidat pověření pro systémem přiřazenou identitu *targetregistry*k ověření `use-identity [system]`pomocí registru registru kontejneru Azure , předají :

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Chcete-li přidat pověření pro uživatelem přiřazenou identitu k `use-identity` ověření pomocí *registru targetregistry*, předavte hodnotu *ID klienta* identity. Například:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

ID klienta identity můžete získat spuštěním příkazu [az identity show.][az-identity-show] ID klienta je identifikátor GUID formuláře `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`.

### <a name="5-run-the-task"></a>5. Spuštění úlohy

Po konfiguraci úlohy se spravovanou identitou spusťte úlohu. Chcete-li například otestovat jednu z úloh vytvořených v tomto článku, ručně ji aktivujte pomocí příkazu [az acr task run.][az-acr-task-run] Pokud jste nakonfigurovali další automatické aktivační události úlohy, úloha se spustí při automatickém spuštění.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak povolit a používat uživatelem přiřazenou nebo systémem přiřazenou spravovanou identitu na úkolu ACR. Scénáře pro přístup k zabezpečeným prostředkům z úkolu ACR pomocí spravované identity naleznete v tématu:

* [Ověřování napříč registry](container-registry-tasks-cross-registry-authentication.md)
* [Přístup k externím prostředkům s tajnými klíči uloženými v úložišti klíčů Azure](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
