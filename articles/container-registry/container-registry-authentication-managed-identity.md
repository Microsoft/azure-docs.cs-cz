---
title: Ověřování služby Azure Container Registry pomocí spravované identity
description: Poskytují přístup k obrázkům v váš privátní registr kontejnerů s využitím uživatelsky přiřazené nebo systém přiřadil spravované Azure identity.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 01/16/2019
ms.author: danlep
ms.openlocfilehash: 46965842831673d25d636e0cfbfa3d0097b82f99
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54857345"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Použití spravované identity k ověření do služby Azure container registry v Azure 

Použití [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) k ověření do služby Azure container registry z jiného prostředku Azure, aniž by museli poskytnout nebo spravovat přihlašovací údaje registru. Například nastavte uživatelsky přiřazené nebo systém přiřadil spravovaná identita na virtuální počítač s Linuxem do imagí kontejneru přístup z vašeho registru kontejneru, stejně snadno, jako používáte veřejného registru.

Pro účely tohoto článku se dozvíte další informace o spravovaných identit a jak:

> [!div class="checklist"]
> * Povolit uživateli přiřazena nebo systém přiřadil identitu na Virtuálním počítači Azure
> * Udělit přístup identit do služby Azure container registry
> * Použití spravované identity pro přístup k registru a stáhněte si image kontejneru 

Vytváření prostředků Azure, tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.55 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

Nastavení registru kontejneru a nahrání image kontejneru do ní, musí také mít Docker nainstalovaný místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru v libovolném systému [macOS][docker-mac], [Windows][docker-windows] nebo [Linux][docker-linux].

## <a name="why-use-a-managed-identity"></a>Proč používat spravovanou identitu?

Spravovaná identita pro prostředky Azure poskytuje služby Azure se automaticky spravovanou identitu ve službě Azure Active Directory (Azure AD). Můžete nakonfigurovat [určité prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-msi.md), včetně virtual machines, s využitím spravované identity. Potom použijte identitu a přístup jiných prostředků služby Azure bez předávání přihlašovacích údajů v kódu nebo skriptech.

Spravované identity jsou dvou typů:

* *Uživatelsky přiřazené identity*, který lze přiřadit k více prostředkům a až vaše chcete zachovat. Uživatelsky přiřazené identity jsou aktuálně ve verzi preview.

* A *identity spravované systému*, které jsou jedinečné pro konkrétní prostředek, jako je jeden virtuální počítač a má platnost po dobu životnosti prostředku.

Po nastavení prostředku Azure s využitím spravované identity poskytují identitu přístup, které chcete do jiného prostředku, stejně jako libovolný objekt zabezpečení. Například přiřadíte spravovanou identitu role se stejnými o přijetí změn, vložení a o přijetí změn nebo jiná oprávnění privátního registru, v Azure. (Úplný seznam rolí registru najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md).) Můžete poskytnout přístup identity na jeden nebo více prostředků.

Potom použijte identitu ověřování do libovolných [služba, která podporuje ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication), bez jakékoli přihlašovací údaje ve vašem kódu. Používat identitu pro přístup k registru kontejneru Azure z virtuálního počítače, ověřování pomocí Azure Resource Manageru. Volba způsobu ověření pomocí spravované identity, v závislosti na vašem scénáři:

* [Získání přístupového tokenu Azure AD](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programově pomocí protokolu HTTP nebo ZBÝVAJÍCÍ volání

* Použití [sady Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Přihlaste se k Azure CLI nebo Powershellu](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) s identitou. 

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Pokud ještě nemáte služby Azure container registry, vytvořte registr a push ukázka image kontejneru do ní. Pokyny najdete v tématu [rychlý start: Vytvořit privátní registr pomocí rozhraní příkazového řádku Azure](container-registry-get-started-azure-cli.md).

Tento článek předpokládá, že máte `aci-helloworld:v1` image kontejneru uložena v registru. V příkladech se používá název registru *myContainerRegistry*. Nahraďte vlastním registru a názvy imagí v dalších krocích.

## <a name="create-a-docker-enabled-vm"></a>Vytvoření virtuálního počítače s podporou Dockeru

Vytvoření virtuálního počítače s Ubuntu podporou Dockeru. Budete také muset nainstalovat [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) na virtuálním počítači. Pokud již máte virtuální počítač Azure, přeskočte tento krok k vytvoření virtuálního počítače.

Nasadit výchozí virtuální počítač Azure s Ubuntu s [az vm vytvořit][az-vm-create]. Následující příklad vytvoří virtuální počítač s názvem *myDockerVM* v existující skupinu prostředků s názvem *myResourceGroup*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po dokončení příkazu si poznamenejte `publicIpAddress` zobrazí rozhraní příkazového řádku Azure. Tuto adresu používejte k vytvoření připojení SSH k virtuálnímu počítači.

### <a name="install-docker-on-the-vm"></a>Nainstalovat Docker na virtuálním počítači

Po spuštění virtuálního počítače vytvořte připojení SSH k virtuálnímu počítači. Nahraďte *publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače.

```bash
ssh azureuser@publicIpAddress
```

Spusťte následující příkaz k instalaci Dockeru ve virtuálním počítači:

```bash
sudo apt install docker.io -y
```

Po dokončení instalace spusťte následující příkaz k ověření, že je správně spuštěna Dockeru ve virtuálním počítači:

```bash
sudo docker run -it hello-world
```

Výstup:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

Postupujte podle kroků v [instalace Azure CLI pomocí apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) instalace rozhraní příkazového řádku Azure ve vašem virtuálním počítači se systémem Ubuntu. Pro účely tohoto článku, ujistěte se, že instalujete verzi 2.0.55 nebo novější.

Ukončete relaci SSH.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Příklad 1: Přístup k pomocí uživatelsky přiřazené identity

### <a name="create-an-identity"></a>Vytvoření identity

Vytvoříte identitu, která v předplatném pomocí [vytvoření az identity](/cli/azure/identity?view=azure-cli-latest#az-identity-create) příkazu. Můžete použít stejnou skupinu prostředků, které jste použili k vytvoření registru kontejnerů nebo virtuálních počítačů nebo na jiném.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Ke konfiguraci identity v následujících krocích, použijte [az identity zobrazit] [ az-identity-show] příkazu pro uložení identity prostředku, ID a ID instančního objektu v proměnné.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Protože při přihlášení k rozhraní příkazového řádku z vašeho virtuálního počítače potřebujete ID identity v pozdějším kroku, zobrazit hodnotu:

```bash
echo $userID
```

ID je ve formátu:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Konfigurace virtuálního počítače s identitou

Následující [az vm identity přiřadit] [ az-vm-identity-assign] příkaz nakonfiguruje virtuální počítač Docker s využitím uživatelsky přiřazené identity:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udělit přístup identit do služby container registry

Teď nakonfigurujte identitu pro přístup k registru kontejneru. Nejprve pomocí [az acr show] [ az-acr-show] příkazu Získejte ID prostředku registru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Použití [vytvořit přiřazení role az] [ az-role-assignment-create] příkazu přiřazení AcrPull role do registru. Tato role poskytuje [o přijetí změn oprávnění](container-registry-roles.md) do registru. Můžete zadat i o přijetí změn a oprávnění, přiřaďte roli ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Použít identitu pro přístup k registru

Připojte přes SSH k virtuálnímu počítači Dockeru, který má nakonfigurovanou identitu. Spuštěním následujících příkazů rozhraní příkazového řádku Azure, pomocí rozhraní příkazového řádku Azure, který je nainstalovaný na virtuálním počítači.

Nejprve, přihlaste se pomocí rozhraní příkazového řádku Azure [az login][az-login], pomocí identity nakonfigurovaný na virtuálním počítači. Pro <userID>, nahraďte ID identity, který jste získali v předchozím kroku. 

```azurecli
az login --identity --username <userID>
```

Potom se přihlaste do registru příkazem [az acr login][az-acr-login]. Při použití tohoto příkazu rozhraní příkazového řádku používá token služby Active Directory vytvoří, když jste spustili `az login` bez problémů ověřování relace pomocí registru kontejnerů. (V závislosti na nastavení Virtuálního počítače, možná budete muset spustit tento příkaz a příkazy dockeru s `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Měli byste vidět `Login succeeded` zprávy. Pak můžete spustit `docker` příkazy bez zadání pověření. Například spusťte [operace docker pull] [ docker-pull] pro akce pull `aci-helloworld:v1` bitové kopie, zadáte název přihlašovacího serveru vašeho registru. Název přihlašovacího serveru se skládá z za nímž následuje název registru kontejneru (všechna písmena malá) `.azurecr.io` – například `mycontainerregistry.azurecr.io`.

```docker
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Příklad 2: Přístup s identitou systém přiřadil

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Konfigurace virtuálního počítače s identitou systému spravované

Následující [az vm identity přiřadit] [ az-vm-identity-assign] příkaz nakonfiguruje virtuální počítač Docker s identitou systém přiřadil:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Použít [az vm show] [ az-vm-show] příkazu nastavte proměnnou na hodnotu `principalId` (ID instančního objektu) identity Virtuálního počítače pro použití v dalších krocích.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udělit přístup identit do služby container registry

Teď nakonfigurujte identitu pro přístup k registru kontejneru. Nejprve pomocí [az acr show] [ az-acr-show] příkazu Získejte ID prostředku registru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Použití [vytvořit přiřazení role az] [ az-role-assignment-create] příkazu přiřazení AcrPull role identitě. Tato role poskytuje [o přijetí změn oprávnění](container-registry-roles.md) do registru. Můžete zadat i o přijetí změn a oprávnění, přiřaďte roli ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Použít identitu pro přístup k registru

Připojte přes SSH k virtuálnímu počítači Dockeru, který má nakonfigurovanou identitu. Spuštěním následujících příkazů rozhraní příkazového řádku Azure, pomocí rozhraní příkazového řádku Azure, který je nainstalovaný na virtuálním počítači.

Nejprve, přihlaste se pomocí rozhraní příkazového řádku Azure [az login][az-login], pomocí identity přiřazené systém na virtuálním počítači.

```azurecli
az login --identity
```

Potom se přihlaste do registru příkazem [az acr login][az-acr-login]. Při použití tohoto příkazu rozhraní příkazového řádku používá token služby Active Directory vytvoří, když jste spustili `az login` bez problémů ověřování relace pomocí registru kontejnerů. (V závislosti na nastavení Virtuálního počítače, možná budete muset spustit tento příkaz a příkazy dockeru s `sudo`.)

```azurecli
az acr login --name myContainerRegistry
```

Měli byste vidět `Login succeeded` zprávy. Pak můžete spustit `docker` příkazy bez zadání pověření. Například spusťte [operace docker pull] [ docker-pull] pro akce pull `aci-helloworld:v1` bitové kopie, zadáte název přihlašovacího serveru vašeho registru. Název přihlašovacího serveru se skládá z za nímž následuje název registru kontejneru (všechna písmena malá) `.azurecr.io` – například `mycontainerregistry.azurecr.io`.

```docker
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o použití spravovaných identit s Azure Container Registry a jak:

> [!div class="checklist"]
> * Povolit uživateli přiřazena nebo systém přiřadil identitu ve Virtuálním počítači Azure
> * Udělit přístup identit do služby Azure container registry
> * Použití spravované identity pro přístup k registru a stáhněte si image kontejneru

* Další informace o [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/).


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli