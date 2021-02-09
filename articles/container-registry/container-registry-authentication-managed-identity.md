---
title: Ověřování pomocí spravované identity
description: Poskytněte přístup k obrázkům v soukromém registru kontejneru pomocí uživatelsky přiřazené spravované identity Azure, která je přiřazená uživatelem nebo systémem.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 68564cc5743b1deb43bf39f897c239dc683c334c
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987751"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Použití spravované identity Azure k ověření ve službě Azure Container Registry 

Použijte [spravovanou identitu pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) pro ověření ve službě Azure Container Registry z jiného prostředku Azure, aniž byste museli poskytovat nebo spravovat přihlašovací údaje registru. Například na virtuálním počítači Linux nastavte uživatelsky přiřazenou nebo systémově přiřazenou spravovanou identitu pro přístup k imagím kontejneru z registru kontejnerů, a to jednoduše při použití veřejného registru.

V tomto článku se dozvíte víc o spravovaných identitách a o tom, jak:

> [!div class="checklist"]
> * Povolení identity přiřazené uživateli nebo systému přiřazené systémem na virtuálním počítači Azure
> * Udělení identity přístupu ke službě Azure Container Registry
> * Použití spravované identity pro přístup k registru a stažení Image kontejneru 

Tento článek vyžaduje, abyste spustili Azure CLI verze 2.0.55 nebo novější, abyste mohli vytvořit prostředky Azure. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][azure-cli].

K nastavení registru kontejneru a vložení image kontejneru do něj je také nutné nainstalovat Docker místně. Docker nabízí balíčky pro snadnou konfiguraci Dockeru v libovolném systému [macOS][docker-mac], [Windows][docker-windows] nebo [Linux][docker-linux].

## <a name="why-use-a-managed-identity"></a>Proč používat spravovanou identitu?

Spravovaná identita pro prostředky Azure poskytuje služby Azure s automaticky spravovanou identitou v Azure Active Directory (Azure AD). Pomocí spravované identity můžete nakonfigurovat [určité prostředky Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), včetně virtuálních počítačů. Pak použijte identitu pro přístup k dalším prostředkům Azure bez předání přihlašovacích údajů v kódu nebo skriptech.

Spravované identity mají dva typy:

* *Uživatelsky přiřazené identity*, které můžete přiřadit k více prostředkům a uchovávat tak dlouho, dokud budete chtít. Uživatelsky přiřazené identity jsou momentálně ve verzi Preview.

* *Identita spravovaná systémem*, která je jedinečná pro konkrétní prostředek, jako je jeden virtuální počítač a který trvá po dobu životnosti daného prostředku.

Po nastavení prostředku Azure pomocí spravované identity Udělte identitě požadovaný přístup k jinému prostředku, stejně jako jakýkoli objekt zabezpečení. Přiřaďte například roli spravovaná identita s oprávněním Pull, push a pull nebo jinými oprávněními k privátnímu registru v Azure. (Úplný seznam rolí registru najdete v tématu [Azure Container Registry role a oprávnění](container-registry-roles.md).) Jednomu nebo více prostředkům můžete přidělit přístup k identitě.

Pak použijte identitu k ověření pro libovolnou [službu, která podporuje ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), a to bez jakýchkoli přihlašovacích údajů ve vašem kódu. Pokud chcete používat identitu pro přístup ke službě Azure Container Registry z virtuálního počítače, ověříte pomocí Azure Resource Manager. V závislosti na vašem scénáři vyberte způsob ověřování pomocí spravované identity.

* Programové [získání přístupového tokenu Azure AD](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) pomocí volání http nebo REST

* Použití [sad Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Přihlaste se k Azure CLI nebo PowerShellu](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) s identitou. 

## <a name="create-a-container-registry"></a>Vytvoření registru kontejneru

Pokud ještě nemáte službu Azure Container Registry, vytvořte registr a nahrajte do něj ukázkovou image kontejneru. Postup najdete v tématu [rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md).

V tomto článku se předpokládá, že máte `aci-helloworld:v1` Image kontejneru uloženou v registru. V příkladech se používá název registru pro *myContainerRegistry*. Nahraďte vlastními názvy registru a imagí v pozdějších krocích.

## <a name="create-a-docker-enabled-vm"></a>Vytvoření virtuálního počítače s podporou Docker

Vytvořte virtuální počítač s Ubuntu s podporou Docker. Na virtuálním počítači je také potřeba nainstalovat rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) . Pokud už máte virtuální počítač Azure, přeskočte tento krok a vytvořte virtuální počítač.

Nasaďte výchozí Ubuntu virtuální počítač Azure pomocí [AZ VM Create][az-vm-create]. Následující příklad vytvoří virtuální počítač s názvem *myDockerVM* v existující skupině prostředků s názvem *myResourceGroup*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Vytvoření virtuálního počítače trvá několik minut. Po dokončení příkazu si poznamenejte `publicIpAddress` zobrazené v Azure CLI. Tato adresa slouží k vytvoření připojení SSH k virtuálnímu počítači.

### <a name="install-docker-on-the-vm"></a>Instalace Docker na virtuálním počítači

Po spuštění virtuálního počítače vytvořte připojení SSH k virtuálnímu počítači. Nahraďte *publicIpAddress* veřejnou IP adresou vašeho virtuálního počítače.

```bash
ssh azureuser@publicIpAddress
```

Spusťte následující příkaz pro instalaci Docker na virtuální počítač:

```bash
sudo apt update
sudo apt install docker.io -y
```

Po instalaci spusťte následující příkaz, který ověří, jestli je na virtuálním počítači správně spuštěný Docker:

```bash
sudo docker run -it mcr.microsoft.com/hello-world
```

Výstup:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalace Azure CLI

Podle postupu v části [instalace Azure CLI pomocí apt](/cli/azure/install-azure-cli-apt) nainstalujte rozhraní příkazového řádku Azure do svého virtuálního počítače s Ubuntu. V tomto článku se ujistěte, že instalujete verzi 2.0.55 nebo novější.

Ukončete relaci SSH.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Příklad 1: přístup s identitou přiřazenou uživatelem

### <a name="create-an-identity"></a>Vytvoření identity

Pomocí příkazu [AZ identity Create](/cli/azure/identit#az-identity-create) vytvořte v předplatném identitu. Stejnou skupinu prostředků, kterou jste použili dříve, můžete použít k vytvoření registru kontejneru nebo virtuálního počítače nebo jiného.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Pokud chcete nakonfigurovat identitu v následujících krocích, pomocí příkazu [AZ identity show][az-identity-show] uložte ID prostředku identity a ID instančního objektu do proměnných.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Vzhledem k tomu, že v pozdějším kroku potřebujete ID identity, když se přihlásíte k rozhraní příkazového řádku z virtuálního počítače, zobrazí se hodnota:

```bash
echo $userID
```

ID má formát:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Konfigurace virtuálního počítače s identitou

Následující příkaz [AZ VM identity Assign][az-vm-identity-assign] NAkonfiguruje virtuální počítač Docker s uživatelem přiřazenou identitou:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udělení přístupu identit do registru kontejneru

Nyní nakonfigurujte identitu pro přístup k registru kontejneru. Nejprve pomocí příkazu [AZ ACR show][az-acr-show] Získejte ID prostředku registru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

K přiřazení role AcrPull do registru použijte příkaz [AZ role Assignment Create][az-role-assignment-create] . Tato role poskytuje [oprávnění ke stažení](container-registry-roles.md) do registru. Pokud chcete poskytnout oprávnění ke stažení i pro nabízená oznámení, přiřaďte roli ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Použití identity pro přístup k registru

Připojte se přes SSH k virtuálnímu počítači Docker, který je nakonfigurovaný s identitou. Spusťte následující příkazy rozhraní příkazového řádku Azure a pomocí rozhraní příkazového řádku Azure nainstalovaného na virtuálním počítači.

Nejdřív pomocí identity, kterou jste nakonfigurovali na VIRTUÁLNÍm počítači, proveďte ověření v Azure CLI pomocí [AZ Login][az-login]. `<userID>`V případě nahraďte ID identity, kterou jste získali v předchozím kroku. 

```azurecli
az login --identity --username <userID>
```

Pak proveďte ověření v registru pomocí [AZ ACR Login][az-acr-login]. Při použití tohoto příkazu rozhraní příkazového řádku používá token služby Active Directory, který byl vytvořen při `az login` bezproblémovém ověřování relace pomocí registru kontejnerů. (V závislosti na nastavení virtuálního počítače možná budete muset spustit tento příkaz a příkazy Docker s `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Měla by se zobrazit `Login succeeded` zpráva. Pak můžete spustit `docker` příkazy bez zadání přihlašovacích údajů. Například spusťte [Docker Pull][docker-pull] pro načtení `aci-helloworld:v1` obrázku a zadáním názvu přihlašovacího serveru vašeho registru. Název přihlašovacího serveru se skládá z názvu registru kontejneru (všechna malá písmena) následovaných `.azurecr.io` například `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Příklad 2: přístup s identitou přiřazenou systémem

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Konfigurace virtuálního počítače pomocí identity spravované systémem

Následující příkaz [AZ VM identity Assign][az-vm-identity-assign] NAkonfiguruje virtuální počítač Docker s identitou přiřazenou systémem:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Pomocí příkazu [AZ VM show][az-vm-show] nastavte proměnnou na hodnotu `principalId` (ID instančního objektu) identity virtuálního počítače, která se má použít v pozdějších krocích.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udělení přístupu identit do registru kontejneru

Nyní nakonfigurujte identitu pro přístup k registru kontejneru. Nejprve pomocí příkazu [AZ ACR show][az-acr-show] Získejte ID prostředku registru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

K přiřazení role AcrPull k identitě použijte příkaz [AZ role Assignment Create][az-role-assignment-create] . Tato role poskytuje [oprávnění ke stažení](container-registry-roles.md) do registru. Pokud chcete poskytnout oprávnění ke stažení i pro nabízená oznámení, přiřaďte roli ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Použití identity pro přístup k registru

Připojte se přes SSH k virtuálnímu počítači Docker, který je nakonfigurovaný s identitou. Spusťte následující příkazy rozhraní příkazového řádku Azure a pomocí rozhraní příkazového řádku Azure nainstalovaného na virtuálním počítači.

Nejdřív ověřte Azure CLI pomocí [AZ Login][az-login]a na virtuálním počítači použijte identitu přiřazenou systémem.

```azurecli
az login --identity
```

Pak proveďte ověření v registru pomocí [AZ ACR Login][az-acr-login]. Při použití tohoto příkazu rozhraní příkazového řádku používá token služby Active Directory, který byl vytvořen při `az login` bezproblémovém ověřování relace pomocí registru kontejnerů. (V závislosti na nastavení virtuálního počítače možná budete muset spustit tento příkaz a příkazy Docker s `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Měla by se zobrazit `Login succeeded` zpráva. Pak můžete spustit `docker` příkazy bez zadání přihlašovacích údajů. Například spusťte [Docker Pull][docker-pull] pro načtení `aci-helloworld:v1` obrázku a zadáním názvu přihlašovacího serveru vašeho registru. Název přihlašovacího serveru se skládá z názvu registru kontejneru (všechna malá písmena) následovaných `.azurecr.io` například `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```
> [!NOTE]
> Identity spravované služby přiřazené systémem se dají použít k interakci s záznamů ACR a App Service můžou používat identity spravované služby přiřazené systémem. Nemůžete je ale kombinovat, protože App Service nemůže použít MSI ke komunikaci s ACR. Jediným způsobem je povolit správcům ACR a používat uživatelské jméno a heslo správce.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili s používáním spravovaných identit s Azure Container Registry a postupy:

> [!div class="checklist"]
> * Povolení uživatelsky přiřazené identity nebo identity přiřazené systémem ve virtuálním počítači Azure
> * Udělení identity přístupu ke službě Azure Container Registry
> * Použití spravované identity pro přístup k registru a stažení Image kontejneru

* Přečtěte si další informace o [spravovaných identitách pro prostředky Azure](../active-directory/managed-identities-azure-resources/index.yml).


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
