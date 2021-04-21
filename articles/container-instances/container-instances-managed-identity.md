---
title: Povolit spravovanou identitu ve skupině kontejnerů
description: Naučte se, jak v Azure Container Instances povolit spravovanou identitu, která se dá ověřit u jiných služeb Azure.
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: f8f3c646487d86f4e1bce13ccbf28992b8b1497a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763980"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Použití spravovaných identit se službou Azure Container Instances

Pomocí [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) spustíte kód v Azure Container Instances, který komunikuje s ostatními službami Azure – bez zachování tajných kódů nebo přihlašovacích údajů v kódu. Tato funkce poskytuje Azure Container Instances nasazení s automaticky spravovanou identitou v Azure Active Directory.

V tomto článku se dozvíte víc o spravovaných identitách v Azure Container Instances a:

> [!div class="checklist"]
> * Povolení uživatelsky přiřazené identity nebo identity přiřazené systémem ve skupině kontejnerů
> * Udělení identity přístupu k trezoru klíčů Azure
> * Použití spravované identity pro přístup k trezoru klíčů ze spuštěného kontejneru

Přizpůsobte si příklady a povolte a používejte identity v Azure Container Instances pro přístup k dalším službám Azure. Tyto příklady jsou interaktivní. Nicméně v praxi by image kontejneru spouštěly kód pro přístup ke službám Azure.
 
> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit. V současné době se spravované identity v Azure Container Instances podporují jenom s kontejnery Linux a ještě nejsou s kontejnery Windows.

## <a name="why-use-a-managed-identity"></a>Proč používat spravovanou identitu?

Pomocí spravované identity ve spuštěném kontejneru proveďte ověření u libovolné [služby, která podporuje ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) bez nutnosti spravovat přihlašovací údaje v kódu kontejneru. Pro služby, které nepodporují ověřování AD, můžete tajné klíče ukládat do trezoru klíčů Azure a pomocí spravované identity získat přístup k trezoru klíčů a načíst přihlašovací údaje. Další informace o použití spravované identity najdete v tématu [co jsou spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="enable-a-managed-identity"></a>Povolení spravované identity

 Když vytváříte skupinu kontejnerů, povolte jednu nebo více spravovaných identit nastavením vlastnosti [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) . Spravované identity můžete povolit nebo aktualizovat i po spuštění skupiny kontejnerů – akce způsobí restartování skupiny kontejnerů. Pokud chcete nastavit identity pro novou nebo existující skupinu kontejnerů, použijte Azure CLI, šablonu Správce prostředků, soubor YAML nebo jiný nástroj Azure. 

Azure Container Instances podporuje oba typy spravovaných identit Azure: přiřazeno uživatelem a systémem. Ve skupině kontejnerů můžete povolit identitu přiřazenou systémem, jednu nebo více uživatelsky přiřazených identit nebo oba typy identit. Pokud neznáte spravované identity prostředků Azure, podívejte se na [Přehled](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="use-a-managed-identity"></a>Použití spravované identity

Aby bylo možné používat spravovanou identitu, musí být identitě udělen přístup k jednomu nebo několika prostředkům služby Azure (jako je webová aplikace, Trezor klíčů nebo účet úložiště) v předplatném. Použití spravované identity v běžícím kontejneru se podobá použití identity na virtuálním počítači Azure. Podívejte se na pokyny k VIRTUÁLNÍm počítačům pro použití [tokenu](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure POWERSHELL nebo Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)nebo [sad Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

### <a name="limitations"></a>Omezení

* V tuto chvíli nemůžete použít spravovanou identitu ve skupině kontejnerů nasazené do virtuální sítě.
* Spravovanou identitu nemůžete použít k načtení obrázku z Azure Container Registry při vytváření skupiny kontejnerů. Identita je k dispozici pouze v rámci běžícího kontejneru.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Tento článek vyžaduje verzi rozhraní příkazového řádku Azure 2.0.49 nebo novější. Pokud používáte Azure Cloud Shell, nejnovější verze je už nainstalovaná.

## <a name="create-an-azure-key-vault"></a>Vytvoření trezoru klíčů Azure

Příklady v tomto článku používají spravovanou identitu v Azure Container Instances pro přístup k tajnému kódu trezoru klíčů Azure. 

Nejprve pomocí následujícího příkazu [az group create](/cli/azure/group#az_group_create)vytvořte skupinu prostředků s názvem *myResourceGroup* v umístění *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Pomocí příkazu [AZ datatrezor Create](/cli/azure/keyvault#az_keyvault_create) vytvořte Trezor klíčů. Nezapomeňte zadat jedinečný název trezoru klíčů. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Pomocí příkazu [AZ Key trezor tajné sady](/cli/azure/keyvault/secret#az_keyvault_secret_set) uložte ukázkový tajný klíč do trezoru klíčů:

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Pokračujte následujícími příklady pro přístup k trezoru klíčů s použitím spravované identity přiřazené uživatelem nebo systémem v Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Příklad 1: použití identity přiřazené uživatelem pro přístup k trezoru klíčů Azure

### <a name="create-an-identity"></a>Vytvoření identity

Nejdřív v předplatném vytvořte identitu pomocí příkazu [AZ identity Create](/cli/azure/identity#az_identity_create) . Můžete použít stejnou skupinu prostředků, která se používá k vytvoření trezoru klíčů, nebo použít jinou.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Pokud chcete použít identitu v následujících krocích, pomocí příkazu [AZ identity show](/cli/azure/identity#az_identity_show) uložte ID instančního objektu identity a ID prostředku do proměnných.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query id --output tsv)
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Udělení přístupu k trezoru klíčů uživatelem přiřazeným identitám

Pokud chcete nastavit zásady přístupu pro Trezor klíčů, spusťte následující příkaz [AZ Key trezor set-Policy](/cli/azure/keyvault) . Následující příklad umožňuje uživateli přiřazenou identitu k získání tajných kódů z trezoru klíčů:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>Povolení uživatelsky přiřazené identity ve skupině kontejnerů

Spuštěním následujícího příkazu [AZ Container Create](/cli/azure/container#az_container_create) vytvořte instanci kontejneru založenou na `azure-cli` imagi Microsoftu. Tento příklad poskytuje skupinu s jedním kontejnerem, kterou můžete použít interaktivně, aby bylo možné spustit Azure CLI pro přístup k dalším službám Azure. V této části se používá jenom základní operační systém. Příklad použití rozhraní příkazového řádku Azure v kontejneru najdete v tématu [Povolení identity přiřazené systémem ve skupině kontejnerů](#enable-system-assigned-identity-on-a-container-group). 

`--assign-identity`Parametr předá uživatelem přiřazenou spravovanou identitu do skupiny. Dlouho běžící příkaz udržuje kontejner spuštěný. Tento příklad používá stejnou skupinu prostředků, která se používá k vytvoření trezoru klíčů, ale můžete zadat jiný.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Během několika sekund byste měli dostat odpověď z rozhraní příkazového řádku Azure oznamující, že nasazení bylo dokončeno. Pomocí příkazu [AZ Container show](/cli/azure/container#az_container_show) ověřte jeho stav.

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

`identity`Oddíl ve výstupu vypadá podobně jako v následujícím příkladu, který zobrazuje identitu nastavenou ve skupině kontejnerů. V `principalID` části `userAssignedIdentities` je instanční objekt identity, kterou jste vytvořili v Azure Active Directory:

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

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Použití uživatelsky přiřazené identity k získání tajného klíče z trezoru klíčů

Nyní můžete použít spravovanou identitu v rámci spuštěné instance kontejneru pro přístup k trezoru klíčů. Nejdřív spusťte prostředí bash v kontejneru:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Spusťte následující příkazy v prostředí bash v kontejneru. Pokud chcete získat přístupový token pro použití Azure Active Directory k ověření v trezoru klíčů, spusťte následující příkaz:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Výstup:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Pro uložení přístupového tokenu do proměnné pro použití v dalších příkazech k ověření spusťte následující příkaz:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Nyní pomocí přístupového tokenu ověřte v trezoru klíčů a přečtěte si tajný klíč. Nezapomeňte nahradit název trezoru klíčů v adrese URL (*https: \/ /mykeyvault.Vault.Azure.NET/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Odpověď vypadá podobně jako v následujícím příkladu, který zobrazuje tajný klíč. V kódu byste tento výstup mohli analyzovat a získat tak tajný klíč. Pak použijte tajný klíč v následné operaci pro přístup k jinému prostředku Azure.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Příklad 2: použití identity přiřazené systémem pro přístup k trezoru klíčů Azure

### <a name="enable-system-assigned-identity-on-a-container-group"></a>Povolení identity přiřazené systémem ve skupině kontejnerů

Spuštěním následujícího příkazu [AZ Container Create](/cli/azure/container#az_container_create) vytvořte instanci kontejneru založenou na `azure-cli` imagi Microsoftu. Tento příklad poskytuje skupinu s jedním kontejnerem, kterou můžete použít interaktivně, aby bylo možné spustit Azure CLI pro přístup k dalším službám Azure. 

`--assign-identity`Parametr bez další hodnoty umožňuje pro skupinu spravovanou identitu přiřazenou systémem. Identita je vymezena pro skupinu prostředků ve skupině kontejnerů. Dlouho běžící příkaz udržuje kontejner spuštěný. Tento příklad používá stejnou skupinu prostředků, která se používá k vytvoření trezoru klíčů, který je v oboru identity.

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

Během několika sekund byste měli dostat odpověď z rozhraní příkazového řádku Azure oznamující, že nasazení bylo dokončeno. Pomocí příkazu [AZ Container show](/cli/azure/container#az_container_show) ověřte jeho stav.

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

`identity`Oddíl ve výstupu vypadá podobně jako v následujícím příkladu, který ukazuje, že se v Azure Active Directory vytvoří identita přiřazená systémem:

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

Nastavte proměnnou na hodnotu `principalId` (ID instančního objektu) identity, která se použije v pozdějších krocích.

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Udělení přístupu k trezoru klíčů skupině kontejnerů

Pokud chcete nastavit zásady přístupu pro Trezor klíčů, spusťte následující příkaz [AZ Key trezor set-Policy](/cli/azure/keyvault) . Následující příklad umožňuje, aby identita spravovaná systémem získala tajné kódy z trezoru klíčů:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Použití identity skupiny kontejnerů k získání tajného klíče z trezoru klíčů

Nyní můžete použít spravovanou identitu pro přístup k trezoru klíčů v rámci spuštěné instance kontejneru. Nejdřív spusťte prostředí bash v kontejneru:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Spusťte následující příkazy v prostředí bash v kontejneru. Nejdřív se přihlaste k Azure CLI pomocí spravované identity:

```bash
az login --identity
```

Z běžícího kontejneru načtěte tajný klíč z trezoru klíčů:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

Načte se hodnota tajného klíče:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Povolení spravované identity pomocí šablony Správce prostředků

Chcete-li povolit spravovanou identitu ve skupině kontejnerů pomocí [šablony Správce prostředků](container-instances-multi-container-group.md), nastavte `identity` vlastnost `Microsoft.ContainerInstance/containerGroups` objektu `ContainerGroupIdentity` objektem. Následující fragmenty kódu ukazují `identity` vlastnost nakonfigurovanou pro různé scénáře. Viz [odkaz na šablonu správce prostředků](/azure/templates/microsoft.containerinstance/containergroups). Zadejte minimálně hodnotu `apiVersion` `2018-10-01` .

### <a name="user-assigned-identity"></a>Identita přiřazená uživatelem

Identita přiřazená uživatelem je ID prostředku ve formátu:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Můžete povolit jednu nebo více uživatelsky přiřazených identit.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Identita přiřazená systémem

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Identity přiřazené systémem a uživatelem

Ve skupině kontejnerů můžete povolit identitu přiřazenou systémem i jednu nebo více uživatelsky přiřazených identit.

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

Pokud chcete ve skupině kontejnerů nasazenou pomocí [souboru YAML](container-instances-multi-container-yaml.md)povolit spravovanou identitu, zahrňte následující YAML.
Zadejte minimálně hodnotu `apiVersion` `2018-10-01` .

### <a name="user-assigned-identity"></a>Identita přiřazená uživatelem

Identita přiřazená uživatelem je ID prostředku ve formátu. 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Můžete povolit jednu nebo více uživatelsky přiřazených identit.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Identita přiřazená systémem

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Identity přiřazené systémem a uživatelem

Ve skupině kontejnerů můžete povolit identitu přiřazenou systémem i jednu nebo více uživatelsky přiřazených identit.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste se seznámili se spravovanými identitami v Azure Container Instances a postupy:

> [!div class="checklist"]
> * Povolení uživatelsky přiřazené identity nebo identity přiřazené systémem ve skupině kontejnerů
> * Udělení identity přístupu k trezoru klíčů Azure
> * Použití spravované identity pro přístup k trezoru klíčů ze spuštěného kontejneru

* Přečtěte si další informace o [spravovaných identitách pro prostředky Azure](../active-directory/managed-identities-azure-resources/index.yml).

* Příklad použití spravované identity pro přístup k trezoru klíčů z Azure Container Instances najdete v tématu [Azure SDK SDK](https://medium.com/@samkreter/c98911206328) .
