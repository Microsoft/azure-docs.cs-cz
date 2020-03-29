---
title: Povolení spravované identity ve skupině kontejnerů
description: Zjistěte, jak povolit spravovanou identitu v instanci kontejneru Azure, které se můžou ověřovat pomocí jiných služeb Azure.
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 003055d5021dd8ad7c3bab6d2900298ffd13b222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76901926"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Jak používat spravované identity s instancemi kontejneru Azure

Ke spouštění kódu v instanci azure kontejnerů, které interagují s jinými službami Azure, použijte [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) – bez údržby tajných kódů nebo přihlašovacích údajů v kódu. Tato funkce poskytuje nasazení instancí kontejnerů Azure s automaticky spravovanou identitou ve službě Azure Active Directory.

V tomto článku se dozvíte další informace o spravovaných identitách v instancích kontejnerů Azure a:

> [!div class="checklist"]
> * Povolení identity přiřazené uživatelem nebo systémově přiřazené ve skupině kontejnerů
> * Udělení přístupu k identitě trezoru klíčů Azure
> * Použití spravované identity pro přístup k trezoru klíčů ze spuštěného kontejneru

Přizpůsobte příklady pro povolení a použití identit v Azure Container Instances pro přístup k dalším službám Azure. Tyto příklady jsou interaktivní. V praxi by však vaše image kontejneru spouštět kód pro přístup ke službám Azure.

> [!NOTE]
> V současné době nelze použít spravovanou identitu ve skupině kontejnerů nasazené do virtuální sítě.

## <a name="why-use-a-managed-identity"></a>Proč používat spravovanou identitu?

Pomocí spravované identity ve spuštěném kontejneru se můžete ověřit na libovolné [službě, která podporuje ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) bez správy přihlašovacích údajů v kódu kontejneru. U služeb, které nepodporují ověřování ve službě AD, můžete ukládat tajné klíče do trezoru klíčů Azure a použít spravovanou identitu pro přístup k trezoru klíčů k načtení přihlašovacích údajů. Další informace o používání spravované identity najdete v tématu [Co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit. V současné době spravované identity v instanci kontejnerů Azure jsou podporovány jenom s linuxovými kontejnery a ještě ne s kontejnery Windows.
>  

### <a name="enable-a-managed-identity"></a>Povolení spravované identity

 V Azure Container Instances spravované identity pro prostředky Azure jsou podporované od REST API verze 2018-10-01 a odpovídající sady SDK a nástroje. Při vytváření skupiny kontejnerů povolte jednu nebo více spravovaných identit nastavením [vlastnosti ContainerGroupIdentity.](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) Spravované identity můžete také povolit nebo aktualizovat po spuštění skupiny kontejnerů – buď akce způsobí restartování skupiny kontejnerů. Chcete-li nastavit identity na nové nebo existující skupiny kontejnerů, použijte Azure CLI, šablonu Správce prostředků nebo soubor YAML. 

Azure Container Instances podporuje oba typy spravovaných identit Azure: uživatelem přiřazené a přiřazené k systému. Ve skupině kontejnerů můžete povolit systémem přiřazenou identitu, jednu nebo více identit přiřazených uživatelem nebo oba typy identit. 

* Uživatelem **přiřazená spravovaná** identita se vytvoří jako samostatný prostředek Azure v tenantu Azure AD, který je důvěryhodný pro použití předplatného. Po vytvoření identity se identita dá přiřadit k jednomu nebo více prostředkům Azure (v instancích Azure Container Instance nebo jiných službách Azure). Životní cyklus uživatelem přiřazené identity je spravován odděleně od životního cyklu skupin kontejnerů nebo jiných prostředků služby, ke kterým je přiřazena. Toto chování je užitečné zejména v Azure Container Instances. Vzhledem k tomu, že identita přesahuje životnost skupiny kontejnerů, můžete ji znovu použít spolu s dalšími standardními nastaveními, aby nasazení skupiny kontejnerů byla vysoce opakovatelná.

* **Systémem přiřazená spravovaná** identita je povolena přímo ve skupině kontejnerů v instanci kontejneru Azure. Když je povolená, Azure vytvoří identitu pro skupinu v tenantovi Azure AD, který je důvěryhodný podle předplatného instance. Po vytvoření identity jsou pověření zřízena v každém kontejneru ve skupině kontejnerů. Životní cyklus systémově přiřazené identity je přímo vázán na skupinu kontejnerů, ve které je povolena. Když se odstraní skupina, Azure automaticky vyčistí přihlašovací údaje a identitu ve službě Azure AD.

### <a name="use-a-managed-identity"></a>Použití spravované identity

Aby bylo možné používat spravovanou identitu, musí být identitě nejprve udělen přístup k jednomu nebo více prostředkům služby Azure (například k webové aplikaci, trezoru klíčů nebo účtu úložiště) v předplatném. Přístup k prostředkům Azure z spuštěného kontejneru, váš kód musí získat *přístupový token* z koncového bodu Azure AD. Potom váš kód odešle přístupový token na volání služby, která podporuje ověřování Azure AD. 

Použití spravované identity ve spuštěném kontejneru je v podstatě stejné jako použití identity ve virtuálním počítači Azure. Přečtěte si pokyny k virtuálnímu počítači pro použití [tokenu](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell nebo Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)nebo [Sady Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, tento článek vyžaduje, abyste spouštěli Azure CLI verze 2.0.49 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Vytvoření trezoru klíčů Azure

Příklady v tomto článku používají spravovanou identitu v instanci kontejneru Azure pro přístup k tajnému klíči trezoru Azure. 

Nejprve pomocí následujícího příkazu [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create)vytvořte skupinu prostředků s názvem *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

K vytvoření trezoru klíčů použijte příkaz [az keyvault](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) create. Nezapomeňte zadat jedinečný název trezoru klíčů. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Uložit ukázkový tajný klíč do trezoru klíčů pomocí příkazu [az keyvault secret set:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Pokračujte v následujících příkladech pro přístup k trezoru klíčů pomocí spravované identity přiřazené uživatelem nebo systémem v instanci kontejneru Azure.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Příklad 1: Použití identity přiřazené uživateli pro přístup k trezoru klíčů Azure

### <a name="create-an-identity"></a>Vytvoření identity

Nejprve vytvořte identitu ve vašem předplatném pomocí příkazu [az identity create.](/cli/azure/identity?view=azure-cli-latest#az-identity-create) Můžete použít stejnou skupinu prostředků, která se používá k vytvoření trezoru klíčů, nebo použít jinou skupinu.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Chcete-li použít identitu v následujících krocích, použijte příkaz [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) k uložení ID instancí instancí identity a ID prostředku v proměnných.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Povolení identity přiřazené uživateli ve skupině kontejnerů

Spusťte následující příkaz [az kontejner vytvořit](/cli/azure/container?view=azure-cli-latest#az-container-create) vytvořit instanci kontejneru na základě image společnosti `azure-cli` Microsoft. Tento příklad poskytuje skupinu s jedním kontejnerem, kterou můžete interaktivně použít ke spuštění příkazového příkazového příkazu Azure pro přístup k dalším službám Azure. V této části se používá pouze základní operační systém Ubuntu. 

Parametr `--assign-identity` předá skupině spravovanou identitu přiřazenou uživatelem. Příkaz pro dlouhotrvající spuštění udržuje kontejner spuštěn. Tento příklad používá stejnou skupinu prostředků, která slouží k vytvoření trezoru klíčů, ale můžete zadat jinou skupinu.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Během několika sekund byste měli dostat odpověď z rozhraní příkazového řádku Azure oznamující, že nasazení bylo dokončeno. Zkontrolujte jeho stav pomocí příkazu [az container show.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

Oddíl `identity` ve výstupu vypadá podobně jako následující, zobrazující identitu je nastavena ve skupině kontejnerů. Pod `principalID` `userAssignedIdentities` je instanční objekt identity, kterou jste vytvořili ve službě Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Udělit uživateli přiřazený přístup k identitě trezoru klíčů

Spusťte následující příkaz [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) a nastavte zásady přístupu v trezoru klíčů. Následující příklad umožňuje uživateli přiřazenou identitu získat tajné klíče z trezoru klíčů:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Použití identity přiřazené uživateli k získání tajného klíče z trezoru klíčů

Nyní můžete použít spravovanou identitu v rámci spuštěné instance kontejneru pro přístup k trezoru klíčů. První spuštění bash shell v kontejneru:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Spusťte následující příkazy v prostředí bash v kontejneru. Pokud chcete získat přístupový token pro ověřování v trezoru klíčů pomocí služby Azure Active Directory, spusťte následující příkaz:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Výstup:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Chcete-li ukládat přístupový token do proměnné pro použití v následujících příkazech k ověření, spusťte následující příkaz:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Nyní použijte přístupový token k ověření trezoru klíčů a čtení tajného klíče. Nezapomeňte nahradit název trezoru klíčů v adrese URL (*https://mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Odpověď vypadá podobně jako následující a zobrazuje tajemství. V kódu by analyzovat tento výstup k získání tajného klíče. Potom použijte tajný klíč v následné operaci pro přístup k jinému prostředku Azure.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Příklad 2: Použití systémem přiřazené identity pro přístup k trezoru klíčů Azure

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Povolení systémově přiřazené identity ve skupině kontejnerů

Spusťte následující příkaz [az kontejner vytvořit](/cli/azure/container?view=azure-cli-latest#az-container-create) vytvořit instanci kontejneru na základě image společnosti `azure-cli` Microsoft. Tento příklad poskytuje skupinu s jedním kontejnerem, kterou můžete interaktivně použít ke spuštění příkazového příkazového příkazu Azure pro přístup k dalším službám Azure. 

Parametr `--assign-identity` bez další hodnoty umožňuje systémem přiřazenou spravovanou identitu ve skupině. Identita je vymezena na skupinu prostředků skupiny kontejnerů. Příkaz pro dlouhotrvající spuštění udržuje kontejner spuštěn. Tento příklad používá stejnou skupinu prostředků, která slouží k vytvoření trezoru klíčů, ale můžete zadat jinou skupinu.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Během několika sekund byste měli dostat odpověď z rozhraní příkazového řádku Azure oznamující, že nasazení bylo dokončeno. Zkontrolujte jeho stav pomocí příkazu [az container show.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

Oddíl `identity` ve výstupu vypadá podobně jako následující a ukazuje, že se ve službě Azure Active Directory vytvoří systémově přiřazená identita:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Nastavte proměnnou na `principalId` hodnotu (ID instančního objektu) identity, která se má použít v pozdějších krocích.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Udělit skupině kontejnerů přístup k trezoru klíčů

Spusťte následující příkaz [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) a nastavte zásady přístupu v trezoru klíčů. Následující příklad umožňuje identity spravované systémem získat tajné klíče z trezoru klíčů:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Použití identity skupiny kontejnerů k získání tajného klíče z trezoru klíčů

Nyní můžete použít spravovanou identitu pro přístup k trezoru klíčů v rámci spuštěné instance kontejneru. První spuštění bash shell v kontejneru:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Spusťte následující příkazy v prostředí bash v kontejneru. Nejprve se přihlaste k azure cli pomocí spravované identity:

```bash
az login --identity
```

Ze spuštěného kontejneru načtěte tajný klíč z trezoru klíčů:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

Hodnota tajného klíče je načtena:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Povolení spravované identity pomocí šablony Správce prostředků

Chcete-li povolit spravovanou identitu ve skupině `identity` kontejnerů `Microsoft.ContainerInstance/containerGroups` pomocí šablony `ContainerGroupIdentity` [Správce prostředků](container-instances-multi-container-group.md), nastavte vlastnost objektu pomocí objektu. Následující výstřižky `identity` zobrazují vlastnost nakonfigurovanou pro různé scénáře. Viz [odkaz na šablonu Správce prostředků](/azure/templates/microsoft.containerinstance/containergroups). Zadejte `apiVersion` minimum `2018-10-01`.

### <a name="user-assigned-identity"></a>Identita přiřazená uživatelem

Identita přiřazená uživatelem je ID prostředku formuláře:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Můžete povolit jednu nebo více identit přiřazených uživatelem.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Systémem přiřazená identita

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Identity přiřazené systémem a uživateli

Ve skupině kontejnerů můžete povolit systémově přiřazenou identitu i jednu nebo více identit přiřazených uživatelem.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Povolení spravované identity pomocí souboru YAML

Chcete-li povolit spravovanou identitu ve skupině kontejnerů nasazené pomocí [souboru YAML](container-instances-multi-container-yaml.md), zahrňte následující yaml.
Zadejte `apiVersion` minimum `2018-10-01`.

### <a name="user-assigned-identity"></a>Identita přiřazená uživatelem

Identita přiřazená uživatelem je ID prostředku formuláře. 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Můžete povolit jednu nebo více identit přiřazených uživatelem.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Systémem přiřazená identita

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Identity přiřazené systémem a uživateli

Ve skupině kontejnerů můžete povolit systémově přiřazenou identitu i jednu nebo více identit přiřazených uživatelem.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli o spravovaných identitách v Azure Container Instances a o tom, jak:

> [!div class="checklist"]
> * Povolení identity přiřazené uživatelem nebo systémově přiřazené ve skupině kontejnerů
> * Udělení přístupu k identitě trezoru klíčů Azure
> * Použití spravované identity pro přístup k trezoru klíčů ze spuštěného kontejneru

* Přečtěte si další informace o [spravovaných identitách pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/).

* Podívejte se na [příklad Azure Go SDK příklad](https://medium.com/@samkreter/c98911206328) použití spravované identity pro přístup k trezoru klíčů z Azure Container Instances.
