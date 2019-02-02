---
title: Použití spravované identity s Azure Container Instances
description: Další informace o použití spravované identity k ověření s ostatními službami Azure z Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 10/22/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: bf807a4d24b72c948707ade28f06c4eb2a54c0a0
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55657021"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Použití spravované identity s Azure Container Instances

Použití [spravovaných identit pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md) pro spuštění kódu ve službě Azure Container Instances, která interaguje s ostatními službami Azure – bez zachování všechny tajné kódy nebo přihlašovací údaje v kódu. Tato funkce poskytuje nasazení služby Azure Container Instances se automaticky spravované identity v Azure Active Directory.

V tomto článku najdete další informace o spravovaných identit ve službě Azure Container Instances a:

> [!div class="checklist"]
> * Povolit uživateli přiřazena nebo systém přiřadil identitu ve skupině kontejnerů
> * Udělit přístup identit do služby Azure Key Vault
> * Přístup ke Key Vault z spuštěný kontejner pomocí spravované identity

Příklady pro povolení a používání identit ve službě Azure Container Instances pro přístup k jiným službám Azure přizpůsobit. Tyto příklady jsou interaktivní. V praxi však by imagí kontejnerů spustit kód pro přístup ke službám Azure.

## <a name="why-use-a-managed-identity"></a>Proč používat spravovanou identitu?

Použití spravované identity v spuštěný kontejner ověřování do libovolných [služba, která podporuje ověřování Azure AD](../active-directory/managed-identities-azure-resources/services-support-msi.md#azure-services-that-support-azure-ad-authentication) bez nutnosti spravovat přihlašovací údaje ve vašem kontejneru kódu. Pro služby, které nepodporují ověřování AD můžete ukládat tajné kódy ve službě Azure Key Vault a používat spravovanou identitu pro přístup k službě Key Vault načíst přihlašovací údaje. Další informace o použití spravované identity najdete v tématu [co je spravované identity pro prostředky Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Některé aspekty této funkce se můžou před zveřejněním změnit. V současné době spravovaných identit jsou podporovány pouze na instance kontejneru systému Linux.
>  

### <a name="enable-a-managed-identity"></a>Povolit spravované identity

 Ve službě Azure Container Instances podporují spravované identity pro prostředky Azure od verze rozhraní REST API verze 2018-10-01 a odpovídající sady SDK a nástroje. Když vytvoříte skupinu kontejnerů, povolte jeden nebo více spravovaných identit nastavením [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) vlastnost. Můžete také povolit nebo po skupiny kontejnerů běží aktualizovat spravované identity obě akce způsobí, že skupina kontejnerů restartovat. Nastavení identit v kontejneru nové nebo existující skupiny, použijte Azure CLI, šablony Resource Manageru nebo soubor YAML. 

Služba Azure Container Instances podporuje oba typy spravované identity Azure: uživatelsky přiřazené a systém přiřadil. Skupiny kontejnerů můžete povolit identitu se systém přiřadil, jeden nebo více uživatelsky přiřazené identity nebo obou typů identit. 

* A **uživatelsky přiřazené** spravovaná identita je vytvořen jako samostatný prostředek Azure v tenantovi Azure AD, která je důvěryhodná pro používanému předplatnému. Po vytvoření identity identity můžete přiřadit k jedné nebo více prostředkům Azure (ve službě Azure Container Instances nebo jiných služeb Azure). Životní cyklus uživatelsky přiřazené identity spravované samostatně z životního cyklu skupiny kontejnerů nebo jiných prostředků služby, ke kterým je přiřazen. Toto chování je zvláště užitečná v Azure Container Instances. Protože identitu se rozpíná za dobu života skupiny kontejnerů, můžete znovu použít společně s další standardní nastavení nasazení skupiny kontejnerů vysoce opakovatelné.

* A **systém přiřadil** povolení identity spravované přímo pro skupinu kontejnerů ve službě Azure Container Instances. Pokud je povolena, Azure vytvoří identitu pro skupiny v tenantovi Azure AD, která je důvěryhodná pro předplatné instance. Po vytvoření identity přihlašovací údaje jsou zřízené v jednotlivých kontejnerů ve skupině kontejnerů. Životního cyklu identity systém přiřadil přímo se váže na skupinu kontejnerů, které je zapnutá. Při odstranění skupiny Azure automaticky vyčistí přihlašovací údaje a identitu ve službě Azure AD.

### <a name="use-a-managed-identity"></a>Použití spravované identity

Použití spravované identity, identita musí zpočátku být udělen přístup k jeden nebo více prostředků služby Azure (například webové aplikace, služby Key Vault nebo účtem služby Storage) v rámci předplatného. Pro přístup k prostředkům Azure z spuštěný kontejner, musí váš kód získat *přístupový token* z koncového bodu Azure AD. Pak váš kód odešle přístupový token pro volání na službu, která podporuje ověřování Azure AD. 

Použití spravované identity v spuštěný kontejner je v podstatě totéž jako identitu ve Virtuálním počítači Azure. Viz příručka virtuálního počítače pro použití [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md), nebo [sady Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a používat rozhraní příkazového řádku místně, tento článek vyžaduje použití Azure CLI verze 2.0.49 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault

V příkladech v tomto článku pomocí spravované identity ve službě Azure Container Instances přístup Azure Key Vault tajný klíč. 

Nejprve vytvořte skupinu prostředků s názvem *myResourceGroup* v *eastus* umístění následujícím [vytvořit skupiny az](/cli/azure/group?view=azure-cli-latest#az-group-create) příkaz:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Použití [az keyvault vytvořit](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) příkaz pro vytvoření služby Key Vault. Nezapomeňte zadat jedinečný název služby Key Vault. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Store ukázkový tajný kód v Key Vault pomocí [az keyvault secret sady](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) příkaz:

```azurecli-interactive
az keyvault secret set --name SampleSecret --value "Hello Container Instances!" --description ACIsecret  --vault-name mykeyvault
```

Pokračujte v následujících příkladech přístup ke Key Vault, buď pomocí uživatelsky přiřazené nebo systém přiřadil spravovanou identitu ve službě Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Příklad 1: Použít uživatelsky přiřazené identity pro přístup k Azure Key Vault

### <a name="create-an-identity"></a>Vytvoření identity

Nejprve vytvořte identitu, která v předplatném pomocí [vytvoření az identity](/cli/azure/identity?view=azure-cli-latest#az-identity-create) příkazu. Můžete použít stejnou skupinu prostředků, který se používá k vytvoření služby Key Vault, nebo použijte jiný.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACIId
```

Použít identitu v následujících krocích, použijte [az identity zobrazit](/cli/azure/identity?view=azure-cli-latest#az-identity-show) příkazu pro uložení ID instančního objektu a ID prostředku identity v proměnné.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Povolit uživatelsky přiřazené identity pro skupinu kontejnerů

Spusťte následující příkaz [az container vytvořit](/cli/azure/container?view=azure-cli-latest#az-container-create) příkaz pro vytvoření instance kontejneru založené na Ubuntu Server. V tomto příkladu obsahuje jeden kontejner skupiny, které můžete použít interaktivní přístup k jiným službám Azure. `--assign-identity` Parametr předává vaše spravovaná identita uživatelsky přiřazené ke skupině. Příkaz dlouhotrvající udržuje kontejneru. Tento příklad používá stejnou skupinu prostředků, který se používá k vytvoření služby Key Vault, ale můžete zadat jiný.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity $resourceID --command-line "tail -f /dev/null"
```

Během několika sekund byste měli dostat odpověď z rozhraní příkazového řádku Azure oznamující, že nasazení bylo dokončeno. Zkontrolujte stav s [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) příkazu.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

`identity` Část ve výstupu vypadá podobně jako na následující zobrazující identita nastavena ve skupině kontejnerů. `principalID` Pod `userAssignedIdentities` je instanční objekt služby identity, kterou jste vytvořili v Azure Active Directory:

```console
...
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
...
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Udělení přístupu uživatelsky přiřazené identity do služby Key Vault

Spusťte následující příkaz [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) příkaz pro nastavení zásad přístupu ke službě Key Vault. Následující příklad umožňuje uživatelsky přiřazené identity k získání tajné kódy z trezoru klíčů:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Získání tajného klíče ze služby Key Vault pomocí uživatelsky přiřazené identity

Teď můžete použít spravovanou identitu pro přístup k trezoru klíčů v rámci spuštěnou instanci kontejneru. V tomto příkladu nejdřív spusťte prostředí bash v kontejneru:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Spusťte následující příkazy v prostředí bash v kontejneru. Chcete-li získat přístupový token pro ověření do služby Key Vault pomocí Azure Active Directory, spusťte následující příkaz:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Výstup:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

K ukládání přístupového tokenu v proměnné pro použití v následné příkazy k ověření, spusťte následující příkaz:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Teď pomocí přístupového tokenu pro ověření do služby Key Vault a čtení tajného klíče. Nezapomeňte nahradit název trezoru klíčů v adrese URL (*https://mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Odpověď bude vypadat podobně jako následujícím zobrazující tajný kód. Ve vašem kódu bude parsovat tento výstup získat tajný kód. Potom použijte tajný kód v následné operace pro přístup k jiné prostředků Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Příklad 2: Používat systém přiřadil identitu pro přístup k Azure Key Vault

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Povolit systém přiřadil identitou pro skupinu kontejnerů

Spusťte následující příkaz [az container vytvořit](/cli/azure/container?view=azure-cli-latest#az-container-create) příkaz pro vytvoření instance kontejneru založené na Ubuntu Server. V tomto příkladu obsahuje jeden kontejner skupiny, které můžete použít interaktivní přístup k jiným službám Azure. `--assign-identity` Parametr bez dalších hodnot povoluje systém přiřadil spravovanou identitu skupiny. Příkaz dlouhotrvající udržuje kontejneru. Tento příklad používá stejnou skupinu prostředků, který se používá k vytvoření služby Key Vault, ale můžete zadat jiný.

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image microsoft/azure-cli --assign-identity --command-line "tail -f /dev/null"
```

Během několika sekund byste měli dostat odpověď z rozhraní příkazového řádku Azure oznamující, že nasazení bylo dokončeno. Zkontrolujte stav s [az container show](/cli/azure/container?view=azure-cli-latest#az-container-show) příkazu.

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer
```

`identity` Část ve výstupu bude vypadat podobně jako následujícím znázorňující, že systém přiřadil identitou je vytvořená ve službě Azure Active Directory:

```console
...
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
...
```

Nastavte proměnnou na hodnotu `principalId` (ID instančního objektu) identity pro použití v dalších krocích.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Udělení přístupu ke kontejneru skupiny pro Key Vault

Spusťte následující příkaz [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest) příkaz pro nastavení zásad přístupu ke službě Key Vault. Následující příklad umožňuje identity spravované systému získat tajné kódy z trezoru klíčů:

```azurecli-interactive
 az keyvault set-policy --name mykeyvault --resource-group myResourceGroup --object-id $spID --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Získání tajného klíče ze služby Key Vault pomocí identita skupiny kontejnerů

Teď můžete použít spravovanou identitu pro přístup k trezoru klíčů v rámci spuštěnou instanci kontejneru. V tomto příkladu nejdřív spusťte prostředí bash v kontejneru:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name mycontainer --exec-command "/bin/bash"
```

Spusťte následující příkazy v prostředí bash v kontejneru. Chcete-li získat přístupový token pro ověření do služby Key Vault pomocí Azure Active Directory, spusťte následující příkaz:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net%2F' -H Metadata:true -s
```

Výstup:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

K ukládání přístupového tokenu v proměnné pro použití v následné příkazy k ověření, spusťte následující příkaz:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Teď pomocí přístupového tokenu pro ověření do služby Key Vault a čtení tajného klíče. Nezapomeňte nahradit název trezoru klíčů v adrese URL (*https://mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Odpověď bude vypadat podobně jako následujícím zobrazující tajný kód. Ve vašem kódu bude parsovat tento výstup získat tajný kód. Potom použijte tajný kód v následné operace pro přístup k jiné prostředků Azure.

```bash
{"value":"Hello Container Instances!","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Povolit spravované identity pomocí šablony Resource Manageru

Povolit spravované identity v kontejneru pomocí skupiny [šablony Resource Manageru](container-instances-multi-container-group.md), nastavte `identity` vlastnost `Microsoft.ContainerInstance/containerGroups` objekt s `ContainerGroupIdentity` objektu. Následující fragmenty kódu zobrazit `identity` vlastnost nakonfigurovat pro různé scénáře. Zobrazit [referenčními informacemi k šablonám Resource Manageru](/azure/templates/microsoft.containerinstance/containergroups). Zadejte `apiVersion` z `2018-10-01`.

### <a name="user-assigned-identity"></a>Uživatelsky přiřazené identity

Uživatelsky přiřazené identity je ID prostředku ve tvaru:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Můžete povolit jeden nebo více uživatelsky přiřazené identity.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Systém přiřadil identity

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>System – a uživatelsky přiřazené identity

Skupiny kontejnerů můžete povolit na systém přiřadil identitu a jeden nebo více uživatelsky přiřazené identity.

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

## <a name="enable-managed-identity-using-yaml-file"></a>Povolit spravované identity pomocí souboru YAML

Povolit spravované identity ve skupině kontejnerů nasazeným v rámci [soubor YAML](container-instances-multi-container-yaml.md), zahrnují následující kód YAML.
Zadejte `apiVersion` z `2018-10-01`.

### <a name="user-assigned-identity"></a>Uživatelsky přiřazené identity

Uživatelsky přiřazené identity je ID prostředku z formuláře 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Můžete povolit jeden nebo více uživatelsky přiřazené identity.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Systém přiřadil identity

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>System – a uživatelsky přiřazené identity

Skupiny kontejnerů můžete povolit na systém přiřadil identitu a jeden nebo více uživatelsky přiřazené identity.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Další postup

V tomto článku jste se dozvěděli o spravovaných identit ve službě Azure Container Instances a jak:

> [!div class="checklist"]
> * Povolit uživateli přiřazena nebo systém přiřadil identitu ve skupině kontejnerů
> * Udělit přístup identit do služby Azure Key Vault
> * Přístup ke Key Vault z spuštěný kontejner pomocí spravované identity

* Další informace o [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/).

* Najdete v článku [Azure Go SDK příklad](https://medium.com/@samkreter/c98911206328) použití spravované identity pro přístup ke Key Vault z Azure Container Instances.
