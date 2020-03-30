---
title: Ověřování pomocí spravované identity
description: Poskytněte přístup k bitovým kopiím v registru privátního kontejneru pomocí spravované identity Azure přiřazené uživatelem nebo systémem.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 9b8bed78629d3a9739ec00772ad5c8216a04c122
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456492"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Použití spravované identity Azure k ověření v registru kontejnerů Azure 

Pomocí [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) ověřte do registru kontejnerů Azure z jiného prostředku Azure, aniž byste museli zajišťovat nebo spravovat přihlašovací údaje registru. Můžete například nastavit uživatelem přiřazenou nebo systémem přiřazenou spravovanou identitu na virtuálním počítači s Linuxem pro přístup k ikopiím kontejnerů z registru kontejnerů stejně snadno jako veřejný registr.

V tomto článku se dozvíte více o spravovaných identitách a o těchto postupech:

> [!div class="checklist"]
> * Povolení uživatelem přiřazené nebo systémově přiřazené identity na virtuálním počítači Azure
> * Udělení přístupu k identitě registru kontejnerů Azure
> * Použití spravované identity pro přístup k registru a vyžádat bitovou kopii kontejneru 

Chcete-li vytvořit prostředky Azure, tento článek vyžaduje spuštění Azure CLI verze 2.0.55 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

Chcete-li nastavit registr kontejnerů a vysunout do něj image kontejneru, musíte mít také Docker nainstalován místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru v libovolném systému [macOS][docker-mac], [Windows][docker-windows] nebo [Linux][docker-linux].

## <a name="why-use-a-managed-identity"></a>Proč používat spravovanou identitu?

Spravovaná identita pro prostředky Azure poskytuje služby Azure s automaticky spravovanou identitou ve službě Azure Active Directory (Azure AD). Můžete nakonfigurovat [určité prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), včetně virtuálních počítačů, se spravovanou identitou. Potom použijte identitu pro přístup k jiným prostředkům Azure, bez předávání přihlašovacích údajů v kódu nebo skriptech.

Spravované identity jsou dvou typů:

* *Uživatelem přiřazené identity*, které můžete přiřadit k více prostředkům a zachovat tak dlouho, jak budete chtít. Identity přiřazené uživatelem jsou aktuálně ve verzi Preview.

* *Systémová identita*, která je jedinečná pro konkrétní prostředek, jako je jeden virtuální počítač, a trvá po dobu životnosti tohoto prostředku.

Po nastavení prostředku Azure se spravovanou identitou přiznejte identitu přístup, který chcete k jinému prostředku, stejně jako jakýkoli objekt zabezpečení. Například přiřaďte spravovanou identitu roli s vyžádat, push and pull nebo jiná oprávnění k privátnímu registru v Azure. (Úplný seznam rolí registru najdete v tématu [Role a oprávnění registru kontejnerů Azure](container-registry-roles.md).) Identitu můžete udělit k jednomu nebo více prostředkům.

Potom použijte identitu k ověření na libovolnou [službu, která podporuje ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), bez pověření ve vašem kódu. Chcete-li použít identitu pro přístup k registru kontejnerů Azure z virtuálního počítače, ověřte se pomocí Správce prostředků Azure. V závislosti na scénáři zvolte způsob ověření pomocí spravované identity:

* [Získání přístupového tokenu Azure AD](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programově pomocí volání HTTP nebo REST

* Použití sad [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Přihlaste se k Azure CLI nebo PowerShellu](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) pomocí identity. 

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Pokud ještě nemáte registr kontejnerů Azure, vytvořte registr a převezte do něj ukázkovou image kontejneru. Postup najdete [v tématu Úvodní příručka: Vytvoření registru privátního kontejneru pomocí azure cli](container-registry-get-started-azure-cli.md).

Tento článek předpokládá, `aci-helloworld:v1` že máte image kontejneru uloženou v registru. Příklady používají název registru *myContainerRegistry*. V pozdějších krocích je nahraďte vlastními názvy registru a obrázků.

## <a name="create-a-docker-enabled-vm"></a>Vytvoření virtuálního virtuálního zařízení s podporou Dockeru

Vytvořte virtuální stroj Ubuntu s podporou Dockeru. Taky je potřeba nainstalovat [azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) na virtuální můličce. Pokud už máte virtuální počítač Azure, přeskočte tento krok k vytvoření virtuálního počítače.

Nasazení výchozího virtuálního počítače Ubuntu Azure s [vytvořením az vm][az-vm-create]. Následující příklad vytvoří virtuální hod s názvem *myDockerVM* v existující skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po dokončení příkazu, poznamenejte si `publicIpAddress` zobrazení v příkazovém příkazu Azure. Tato adresa slouží k připojení SSH k virtuálnímu virtuálnímu provozu.

### <a name="install-docker-on-the-vm"></a>Instalace Dockeru na virtuální ms

Po spuštění virtuálního virtuálního provozu vytvořte připojení SSH k virtuálnímu virtuálnímu jemu. Nahraďte *publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače.

```bash
ssh azureuser@publicIpAddress
```

Spusťte následující příkaz pro instalaci Dockeru na virtuální počítač:

```bash
sudo apt install docker.io -y
```

Po instalaci spusťte následující příkaz a ověřte, že Docker na virtuálním počítači funguje správně:

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

Postupujte podle kroků v [části Instalace nastavení příkazového příkazu Azure s aptem](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) k instalaci příkazového příkazového příkazu Azure do virtuálního počítače Ubuntu. V tomto článku se ujistěte, že nainstalujete verzi 2.0.55 nebo novější.

Ukončete relaci SSH.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Příklad 1: Přístup s identitou přiřazenou uživatelem

### <a name="create-an-identity"></a>Vytvoření identity

Vytvořte identitu ve vašem předplatném pomocí příkazu [az identity create.](/cli/azure/identity?view=azure-cli-latest#az-identity-create) Můžete použít stejnou skupinu prostředků, kterou jste použili dříve k vytvoření registru kontejneru nebo virtuálního počítače, nebo jiného.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Chcete-li nakonfigurovat identitu v následujících krocích, použijte příkaz [az identity show][az-identity-show] k uložení ID prostředku identity a ID instančního objektu do proměnných.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Protože při pozdějším přihlášení k zaepisování k zaepisování z virtuálního počítače potřebujete ID identity, zobrazte hodnotu:

```bash
echo $userID
```

ID je ve tvaru:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Konfigurace virtuálního počítače s identitou

Následující příkaz [přiřazení identity virtuálního počítače az][az-vm-identity-assign] nakonfiguruje virtuální počítač Dockeru s identitou přiřazenou uživatelem:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udělit přístup k identitě registru kontejneru

Nyní nakonfigurujte identitu pro přístup k registru kontejneru. Nejprve použijte příkaz [az acr show][az-acr-show] k získání ID prostředku registru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Pomocí příkazu [az role create][az-role-assignment-create] přiřaďte roli AcrPull registru. Tato role poskytuje [oprávnění k vyžádat](container-registry-roles.md) registr. Chcete-li poskytnout oprávnění k vyžádat i vyžádat, přiřaďte roli ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Použití identity pro přístup k registru

SSH do virtuálního počítače Dockeru, který je nakonfigurovaný s identitou. Spusťte následující příkazy Azure CLI pomocí azure cli nainstalované na virtuálním počítači.

Nejprve se ověřte na Azure CLI s [az přihlášení][az-login], pomocí identity, kterou jste nakonfigurovali na virtuálním počítači. Pro `<userID>`, nahradit ID identity, které jste získali v předchozím kroku. 

```azurecli
az login --identity --username <userID>
```

Potom ověřte do registru s [az acr přihlášení][az-acr-login]. Při použití tohoto příkazu rozhraní příkazového příkazu používá `az login` token služby Active Directory vytvořený při spuštění k bezproblémovéověření relace pomocí registru kontejneru. (V závislosti na nastavení virtuálního počítače možná budete muset spustit `sudo`tento příkaz a příkazy dockeru s .)

```azurecli
az acr login --name myContainerRegistry
```

Měl bys `Login succeeded` vidět zprávu. Poté můžete `docker` spouštět příkazy bez zadání pověření. Například spuštění [množiny pro vytažení][docker-pull] `aci-helloworld:v1` bitové kopie a zadání názvu přihlašovacího serveru registru. Název přihlašovacího serveru se skládá z názvu registru kontejneru (všechna malá písmena) následovaná `.azurecr.io` například - například `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Příklad 2: Přístup se systémem přiřazenou identitou

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Konfigurace virtuálního počítače pomocí identity spravované systémem

Následující příkaz [přiřazení identity virtuálního][az-vm-identity-assign] počítače az nakonfiguruje virtuální počítač Dockeru se systémem přiřazenou identitou:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Pomocí příkazu [az vm show][az-vm-show] nastavte proměnnou na hodnotu (ID instančního objektu) identity virtuálního soudu, která se použije v pozdějších `principalId` krocích.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udělit přístup k identitě registru kontejneru

Nyní nakonfigurujte identitu pro přístup k registru kontejneru. Nejprve použijte příkaz [az acr show][az-acr-show] k získání ID prostředku registru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Pomocí příkazu [az role create][az-role-assignment-create] přiřaďte roli AcrPull identitě. Tato role poskytuje [oprávnění k vyžádat](container-registry-roles.md) registr. Chcete-li poskytnout oprávnění k vyžádat i vyžádat, přiřaďte roli ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Použití identity pro přístup k registru

SSH do virtuálního počítače Dockeru, který je nakonfigurovaný s identitou. Spusťte následující příkazy Azure CLI pomocí azure cli nainstalované na virtuálním počítači.

Nejprve ověřte Azure CLI s [az přihlášení][az-login], pomocí systémově přiřazené identity na virtuálním počítači.

```azurecli
az login --identity
```

Potom ověřte do registru s [az acr přihlášení][az-acr-login]. Při použití tohoto příkazu rozhraní příkazového příkazu používá `az login` token služby Active Directory vytvořený při spuštění k bezproblémovéověření relace pomocí registru kontejneru. (V závislosti na nastavení virtuálního počítače možná budete muset spustit `sudo`tento příkaz a příkazy dockeru s .)

```azurecli
az acr login --name myContainerRegistry
```

Měl bys `Login succeeded` vidět zprávu. Poté můžete `docker` spouštět příkazy bez zadání pověření. Například spuštění [množiny pro vytažení][docker-pull] `aci-helloworld:v1` bitové kopie a zadání názvu přihlašovacího serveru registru. Název přihlašovacího serveru se skládá z názvu registru kontejneru (všechna malá písmena) následovaná `.azurecr.io` například - například `mycontainerregistry.azurecr.io`.

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o používání spravovaných identit s Azure Container Registry a jak:

> [!div class="checklist"]
> * Povolení uživatelem přiřazené nebo systémově přiřazené identity ve virtuálním počítači Azure
> * Udělení přístupu k identitě registru kontejnerů Azure
> * Použití spravované identity pro přístup k registru a vyžádat bitovou kopii kontejneru

* Přečtěte si další informace o [spravovaných identitách pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/).


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
