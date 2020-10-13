---
title: Přenosy artefaktů
description: Přenos kolekcí imagí nebo jiných artefaktů z jednoho registru kontejneru do jiného registru vytvořením kanálu přenosu pomocí účtů úložiště Azure
ms.topic: article
ms.date: 10/07/2020
ms.custom: ''
ms.openlocfilehash: fd2cee972ef173853572b871bc80b92b28c505cd
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932596"
---
# <a name="transfer-artifacts-to-another-registry"></a>Přenos artefaktů do jiného registru

Tento článek ukazuje, jak přenést kolekce imagí nebo jiné artefakty registru z jednoho registru služby Azure Container Registry do jiného registru. Zdrojové a cílové Registry můžou být ve stejných nebo různých předplatných, klientech Active Directory, cloudech Azure nebo fyzicky odpojených cloudech. 

Chcete-li přenést artefakty, vytvořte *kanál přenosu* , který replikuje artefakty mezi dvěma Registry pomocí [úložiště objektů BLOB](../storage/blobs/storage-blobs-introduction.md):

* Artefakty ze zdrojového registru se exportují do objektu BLOB ve zdrojovém účtu úložiště. 
* Objekt BLOB se zkopíruje ze zdrojového účtu úložiště do cílového účtu úložiště.
* Objekt BLOB v cílovém účtu úložiště se naimportuje jako artefakty do cílového registru. Kanál importu můžete nastavit tak, aby se aktivoval při každé aktualizaci objektu BLOB artefaktu v cílovém úložišti.

Přenos je ideální pro kopírování obsahu mezi dvěma Registry kontejneru Azure v fyzicky odpojených cloudech, které jsou vynásobené účty úložiště v každém cloudu. Pokud místo toho chcete kopírovat image z registrů kontejnerů v připojených cloudech, včetně Docker Hub a dalších dodavatelů cloudu, doporučuje se [Import obrázků](container-registry-import-images.md) .

V tomto článku použijete Azure Resource Manager nasazení šablon k vytvoření a spuštění kanálu přenosu. Azure CLI se používá ke zřízení přidružených prostředků, například tajných klíčů úložiště. Doporučuje se Azure CLI verze 2.2.0 nebo novější. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku (CLI), přečtěte si téma [Instalace Azure CLI][azure-cli].

Tato funkce je k dispozici na úrovni služby Registry kontejneru **Premium** . Informace o úrovních a omezeních služby registru najdete v tématu [Azure Container Registry úrovně](container-registry-skus.md).

> [!IMPORTANT]
> Tato funkce je aktuálně ve verzi Preview. Verze Preview vám zpřístupňujeme pod podmínkou, že budete souhlasit s [dodatečnými podmínkami použití][terms-of-use]. Některé aspekty této funkce se můžou před zveřejněním změnit.

## <a name="prerequisites"></a>Požadavky

* **Registry kontejnerů** – potřebujete existující zdrojový registr s artefakty pro přenos a cílový registr. Přenos ACR je určený pro pohyb mezi fyzicky odpojenými cloudy. Pro účely testování můžou být zdrojové a cílové Registry ve stejném nebo jiném předplatném Azure, tenantovi služby Active Directory nebo cloudu. 

   Pokud potřebujete vytvořit registr, přečtěte si téma [rychlý Start: Vytvoření privátního registru kontejnerů pomocí Azure CLI](container-registry-get-started-azure-cli.md). 
* **Účty úložiště** – vytvoření zdrojového a cílového účtu úložiště v předplatném a umístění dle vašeho výběru. Pro účely testování můžete použít stejné předplatné nebo odběry jako zdrojové a cílové Registry. V případě scénářů mezi cloudy obvykle vytvoříte samostatný účet úložiště v každém cloudu. 

  V případě potřeby vytvořte účty úložiště pomocí rozhraní příkazového [řádku Azure CLI](../storage/common/storage-account-create.md?tabs=azure-cli) nebo jiných nástrojů. 

  Vytvořte kontejner objektů BLOB pro přenos artefaktů do každého účtu. Vytvořte například kontejner s názvem *Transfer*. Dva nebo více kanálů přenosu může sdílet stejný účet úložiště, ale měl by používat jiné obory kontejneru úložiště.
* **Trezory klíčů** – trezory klíčů jsou potřeba k ukládání tajných klíčů tokenů SAS používaných pro přístup ke zdrojovému a cílovému účtu úložiště. Vytvořte zdrojové a cílové trezory klíčů ve stejném předplatném Azure nebo předplatném jako zdrojové a cílové Registry. V případě demonstračních účelů šablony a příkazy používané v tomto článku předpokládají, že se zdrojové a cílové trezory klíčů nacházejí ve stejné skupině prostředků jako zdrojové a cílové Registry v uvedeném pořadí. Použití běžných skupin prostředků se nevyžaduje, ale zjednodušuje šablony a příkazy používané v tomto článku.

   V případě potřeby vytvořte trezory klíčů pomocí rozhraní příkazového [řádku Azure CLI](../key-vault/secrets/quick-create-cli.md) nebo jiných nástrojů.

* **Proměnné prostředí** – například příkazy v tomto článku, nastavte následující proměnné prostředí pro zdrojové a cílové prostředí. Všechny příklady jsou formátovány pro prostředí bash shell.
  ```console
  SOURCE_RG="<source-resource-group>"
  TARGET_RG="<target-resource-group>"
  SOURCE_KV="<source-key-vault>"
  TARGET_KV="<target-key-vault>"
  SOURCE_SA="<source-storage-account>"
  TARGET_SA="<target-storage-account>"
  ```

## <a name="scenario-overview"></a>Přehled scénáře

Pro přenos obrázků mezi registry vytvoříte následující tři prostředky kanálu. Všechny jsou vytvářeny pomocí operací PUT. Tyto prostředky pracují na vašich *zdrojových* a *cílových* registrech a účtech úložiště. 

Ověřování úložiště používá tokeny SAS spravované jako tajné klíče v trezorech klíčů. Kanály využívají spravované identity ke čtení tajných klíčů v trezorech.

* **[ExportPipeline](#create-exportpipeline-with-resource-manager)** dlouhodobé prostředky, které obsahují informace vysoké úrovně o *zdrojovém* registru a účtu úložiště. Tyto informace zahrnují identifikátor URI kontejneru objektů BLOB zdrojového úložiště a trezor klíčů, který spravuje zdrojový token SAS. 
* **[ImportPipeline](#create-importpipeline-with-resource-manager)** -dlouhodobě dlouhodobé prostředky, které obsahují informace vysoké úrovně o *cílovém* registru a účtu úložiště. Tyto informace zahrnují cílový identifikátor URI kontejneru objektů BLOB úložiště a trezor klíčů, který spravuje cílový token SAS. Aktivační událost importu je ve výchozím nastavení povolená, takže kanál se automaticky spustí, když se objekt BLOB artefaktu naplní do cílového kontejneru úložiště. 
* **[PipelineRun](#create-pipelinerun-for-export-with-resource-manager)** – prostředek, který se používá k vyvolání prostředku ExportPipeline nebo ImportPipeline.  
  * ExportPipeline spustíte ručně vytvořením prostředku PipelineRun a zadáním artefaktů k exportu.  
  * Pokud je povolená aktivační událost importu, ImportPipeline se automaticky spustí. Můžete ho také spustit ručně pomocí PipelineRun. 
  * V současné době je možné přenést maximálně **50 artefaktů** s každým PipelineRun.

### <a name="things-to-know"></a>Co je potřeba vědět
* ExportPipeline a ImportPipeline se obvykle nacházejí v různých klientech služby Active Directory přidružených ke zdrojovému a cílovému cloudu. Tento scénář vyžaduje samostatné spravované identity a trezory klíčů pro prostředky exportu a importu. Pro účely testování je možné tyto prostředky umístit do stejného cloudu a sdílet identity.
* Ve výchozím nastavení jednotlivé šablony ExportPipeline a ImportPipeline umožňují spravované identitě přiřazené systémem přístup k tajným klíčům klíčů trezoru. Šablony ExportPipeline a ImportPipeline také podporují identitu přiřazenou uživatelem, kterou zadáte. 

## <a name="create-and-store-sas-keys"></a>Vytvoření a uložení klíčů SAS

Přenos používá tokeny sdíleného přístupového podpisu (SAS) pro přístup k účtům úložiště ve zdrojovém a cílovém prostředí. Generujte a uložte tokeny, jak je popsáno v následujících částech.  

### <a name="generate-sas-token-for-export"></a>Vygenerovat token SAS pro export

Spuštěním příkazu [AZ Storage Container Generate-SAS][az-storage-container-generate-sas] vygenerujte token SAS pro kontejner ve zdrojovém účtu úložiště, který se používá pro export artefaktu.

*Doporučená oprávnění tokenu*: číst, zapisovat, zobrazit, přidat. 

V následujícím příkladu je výstup příkazu přiřazen proměnné prostředí EXPORT_SAS, s předponou znaku "?". Aktualizujte `--expiry` hodnotu pro vaše prostředí:

```azurecli
EXPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $SOURCE_SA \
  --expiry 2021-01-01 \
  --permissions alrw \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-export"></a>Uložit token SAS pro export

Uložte token SAS ve vašem zdrojovém trezoru klíčů Azure pomocí [AZ Key trezor Secret set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrexportsas \
  --value $EXPORT_SAS \
  --vault-name $SOURCE_KV 
```

### <a name="generate-sas-token-for-import"></a>Vygenerovat token SAS pro import

Spuštěním příkazu [AZ Storage Container Generate-SAS][az-storage-container-generate-sas] vygenerujte token SAS pro kontejner v cílovém účtu úložiště, který se používá pro import artefaktů.

*Doporučená oprávnění tokenu*: číst, odstranit, seznam

V následujícím příkladu je výstup příkazu přiřazen proměnné prostředí IMPORT_SAS, s předponou znaku "?". Aktualizujte `--expiry` hodnotu pro vaše prostředí:

```azurecli
IMPORT_SAS=?$(az storage container generate-sas \
  --name transfer \
  --account-name $TARGET_SA \
  --expiry 2021-01-01 \
  --permissions dlr \
  --https-only \
  --output tsv)
```

### <a name="store-sas-token-for-import"></a>Uložit token SAS pro import

Uložte token SAS v cílovém trezoru Azure Key pomocí [AZ Key trezoru set][az-keyvault-secret-set]:

```azurecli
az keyvault secret set \
  --name acrimportsas \
  --value $IMPORT_SAS \
  --vault-name $TARGET_KV 
```

## <a name="create-exportpipeline-with-resource-manager"></a>Vytvoření ExportPipeline s využitím Správce prostředků

Vytvořte prostředek ExportPipeline pro svůj zdrojový registr kontejneru pomocí nasazení šablony Azure Resource Manager.

Zkopírujte [soubory šablon](https://github.com/Azure/acr/tree/master/docs/image-transfer/ExportPipelines) správce prostředků ExportPipeline do místní složky.

Do souboru zadejte následující hodnoty parametrů `azuredeploy.parameters.json` :

|Parametr  |Hodnota  |
|---------|---------|
|registr     | Název zdrojového registru kontejneru      |
|exportPipelineName     |  Název, který zvolíte pro kanál exportu       |
|targetUri     |  Identifikátor URI kontejneru úložiště ve vašem zdrojovém prostředí (cíl pro export kanálu).<br/>Příklad: `https://sourcestorage.blob.core.windows.net/transfer`       |
|keyVaultName     |  Název zdrojového trezoru klíčů  |
|sasTokenSecretName  | Název tajného klíče tokenu SAS ve zdrojovém trezoru klíčů <br/>Příklad: acrexportsas

### <a name="export-options"></a>Možnosti exportu

`options`Vlastnost pro kanály exportu podporuje volitelné logické hodnoty. Doporučené jsou následující hodnoty:

|Parametr  |Hodnota  |
|---------|---------|
|možnosti | OverwriteBlobs – přepsat existující cílové objekty blob<br/>ContinueOnErrors – export zbývajících artefaktů ve zdrojovém registru pokračuje, pokud dojde k chybě jednoho exportu artefaktů.

### <a name="create-the-resource"></a>Vytvoření prostředku

Spuštěním [AZ Deployment Group Create][az-deployment-group-create] vytvořte prostředek s názvem *exportPipeline* , jak je znázorněno v následujících příkladech. Ve výchozím nastavení, s první možností, šablona příkladu povoluje identitu přiřazenou systémem v prostředku ExportPipeline. 

Po druhé možnosti můžete prostředek poskytnout identitě přiřazené uživatelem. (Vytvoření identity přiřazené uživatelem se nezobrazují.)

Pomocí obou možností šablona nakonfiguruje identitu pro přístup k tokenu SAS v trezoru klíčů export. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Možnost 1: vytvoření prostředku a povolení identity přiřazené systémem

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Možnost 2: vytvoření prostředku a poskytnutí identity přiřazené uživateli

V tomto příkazu zadejte ID prostředku uživatelsky přiřazené identity jako další parametr.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Ve výstupu příkazu si poznamenejte ID prostředku ( `id` ) kanálu. Tuto hodnotu můžete uložit do proměnné prostředí pro pozdější použití spuštěním vlastnosti [AZ Deployment Group show][az-deployment-group-show]. Například:

```azurecli
EXPORT_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-importpipeline-with-resource-manager"></a>Vytvoření ImportPipeline s využitím Správce prostředků 

Vytvořte prostředek ImportPipeline v cílovém registru kontejneru pomocí nasazení šablony Azure Resource Manager. Ve výchozím nastavení je povolený automatický import kanálu, pokud účet úložiště v cílovém prostředí obsahuje objekt BLOB artefaktu.

Zkopírujte [soubory šablon](https://github.com/Azure/acr/tree/master/docs/image-transfer/ImportPipelines) správce prostředků ImportPipeline do místní složky.

Do souboru zadejte následující hodnoty parametrů `azuredeploy.parameters.json` :

Parametr  |Hodnota  |
|---------|---------|
|registr     | Název cílového registru kontejneru      |
|importPipelineName     |  Název, který zvolíte pro kanál importu       |
|sourceUri     |  Identifikátor URI kontejneru úložiště v cílovém prostředí (zdroj pro kanál importu)<br/>Příklad: `https://targetstorage.blob.core.windows.net/transfer/`|
|keyVaultName     |  Název cílového trezoru klíčů |
|sasTokenSecretName     |  Název tajného klíče tokenu SAS v cílovém trezoru klíčů<br/>Příklad: ACR importsas |

### <a name="import-options"></a>Možnosti importu

`options`Vlastnost pro kanál importu podporuje volitelné logické hodnoty. Doporučené jsou následující hodnoty:

|Parametr  |Hodnota  |
|---------|---------|
|možnosti | OverwriteTags – přepsat existující cílové značky<br/>DeleteSourceBlobOnSuccess – odstranění zdrojového objektu BLOB úložiště po úspěšném importu do cílového registru<br/>ContinueOnErrors – pokračujte v importu zbývajících artefaktů v cílovém registru, pokud dojde k chybě jednoho importu artefaktů.

### <a name="create-the-resource"></a>Vytvoření prostředku

Spuštěním [AZ Deployment Group Create][az-deployment-group-create] vytvořte prostředek s názvem *importPipeline* , jak je znázorněno v následujících příkladech. Ve výchozím nastavení, s první možností, šablona příkladu povoluje identitu přiřazenou systémem v prostředku ImportPipeline. 

Po druhé možnosti můžete prostředek poskytnout identitě přiřazené uživatelem. (Vytvoření identity přiřazené uživatelem se nezobrazují.)

Pomocí obou možností šablona nakonfiguruje identitu pro přístup k tokenu SAS v trezoru klíčů import. 

#### <a name="option-1-create-resource-and-enable-system-assigned-identity"></a>Možnost 1: vytvoření prostředku a povolení identity přiřazené systémem

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json 
```

#### <a name="option-2-create-resource-and-provide-user-assigned-identity"></a>Možnost 2: vytvoření prostředku a poskytnutí identity přiřazené uživateli

V tomto příkazu zadejte ID prostředku uživatelsky přiřazené identity jako další parametr.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --template-file azuredeploy.json \
  --name importPipeline \
  --parameters azuredeploy.parameters.json \
  --parameters userAssignedIdentity="/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

Pokud máte v úmyslu spustit import ručně, poznamenejte si ID prostředku ( `id` ) kanálu. Tuto hodnotu můžete uložit do proměnné prostředí pro pozdější použití spuštěním příkazu [AZ Deployment Group show][az-deployment-group-show] . Například:

```azurecli
IMPORT_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipeline \
  --query 'properties.outputResources[1].id' \
  --output tsv)
```

## <a name="create-pipelinerun-for-export-with-resource-manager"></a>Vytvořit PipelineRun pro export pomocí Správce prostředků 

Vytvořte prostředek PipelineRun pro svůj zdrojový registr kontejneru pomocí nasazení šablony Azure Resource Manager. Tento prostředek spustí prostředek ExportPipeline, který jste vytvořili dříve, a exportujte zadané artefakty z registru kontejneru jako objekt blob do vašeho zdrojového účtu úložiště.

Zkopírujte [soubory šablon](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Export) správce prostředků PipelineRun do místní složky.

Do souboru zadejte následující hodnoty parametrů `azuredeploy.parameters.json` :

|Parametr  |Hodnota  |
|---------|---------|
|registr     | Název zdrojového registru kontejneru      |
|pipelineRunName     |  Název, který zvolíte pro běh       |
|pipelineResourceId     |  ID prostředku kanálu exportu<br/>Příklad: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/exportPipelines/myExportPipeline`|
|targetName     |  Název, který zvolíte pro objekt BLOB artefaktů exportovaný do vašeho zdrojového účtu úložiště, například *myblob*
|artefakty | Pole zdrojových artefaktů, které se mají přenést, jako značky nebo výtahy manifestů<br/>Příklad: `[samples/hello-world:v1", "samples/nginx:v1" , "myrepository@sha256:0a2e01852872..."]` |

Pokud se znovu nasadí prostředek PipelineRun se stejnými vlastnostmi, musíte použít také vlastnost [forceUpdateTag](#redeploy-pipelinerun-resource) .

Spuštěním [AZ Deployment Group Create vytvořte][az-deployment-group-create] prostředek PipelineRun. Následující příklad pojmenuje *exportPipelineRun*nasazení.

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json
```

Pro pozdější použití uložte ID prostředku spuštění kanálu do proměnné prostředí:

```azurecli
EXPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $SOURCE_RG \
  --name exportPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)
```

Export artefaktů může trvat několik minut. Po úspěšném dokončení nasazení ověřte export artefaktem uvedením exportovaného objektu BLOB v kontejneru *přenosu* zdrojového účtu úložiště. Spusťte například příkaz [AZ Storage BLOB list][az-storage-blob-list] :

```azurecli
az storage blob list \
  --account-name $SOURCE_SA \
  --container transfer \
  --output table
```

## <a name="transfer-blob-optional"></a>Přenos objektu BLOB (volitelné) 

Použijte nástroj AzCopy nebo jiné metody k [přenosu dat objektů BLOB](../storage/common/storage-use-azcopy-blobs.md#copy-blobs-between-storage-accounts) ze zdrojového účtu úložiště do cílového účtu úložiště.

Následující [`azcopy copy`](../storage/common/storage-ref-azcopy-copy.md) příkaz například zkopíruje myblob z kontejneru *přenosu* ve zdrojovém účtu do kontejneru *přenosu* v cílovém účtu. Pokud objekt BLOB v cílovém účtu existuje, bude přepsán. Ověřování používá tokeny SAS s příslušnými oprávněními pro zdrojové a cílové kontejnery. (Kroky pro vytváření tokenů nejsou zobrazeny.)

```console
azcopy copy \
  'https://<source-storage-account-name>.blob.core.windows.net/transfer/myblob'$SOURCE_SAS \
  'https://<destination-storage-account-name>.blob.core.windows.net/transfer/myblob'$TARGET_SAS \
  --overwrite true
```

## <a name="trigger-importpipeline-resource"></a>Aktivovat prostředek ImportPipeline

Pokud jste povolili `sourceTriggerStatus` parametr ImportPipeline (výchozí hodnota), kanál se aktivuje po zkopírování objektu blob do cílového účtu úložiště. Import artefaktů může trvat několik minut. Po úspěšném dokončení importu ověřte pomocí výpisu úložišť v cílovém registru kontejneru import artefaktů. Například spusťte příkaz [AZ ACR úložištì list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

Pokud jste nepovolili `sourceTriggerStatus` parametr kanálu importu, spusťte prostředek ImportPipeline ručně, jak je znázorněno v následující části. 

## <a name="create-pipelinerun-for-import-with-resource-manager-optional"></a>Vytvořit PipelineRun pro import pomocí Správce prostředků (volitelné) 
 
Pomocí prostředku PipelineRun můžete také aktivovat ImportPipeline pro import artefaktů do cílového registru kontejneru.

Zkopírujte [soubory šablon](https://github.com/Azure/acr/tree/master/docs/image-transfer/PipelineRun/PipelineRun-Import) správce prostředků PipelineRun do místní složky.

Do souboru zadejte následující hodnoty parametrů `azuredeploy.parameters.json` :

|Parametr  |Hodnota  |
|---------|---------|
|registr     | Název cílového registru kontejneru      |
|pipelineRunName     |  Název, který zvolíte pro běh       |
|pipelineResourceId     |  ID prostředku kanálu importu.<br/>Příklad: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ContainerRegistry/registries/<sourceRegistryName>/importPipelines/myImportPipeline`       |
|sourceName     |  Název existujícího objektu BLOB pro exportované artefakty ve vašem účtu úložiště, například *myblob*

Pokud se znovu nasadí prostředek PipelineRun se stejnými vlastnostmi, musíte použít také vlastnost [forceUpdateTag](#redeploy-pipelinerun-resource) .

Spusťte příkaz [AZ Deployment Group Create][az-deployment-group-create] a spusťte prostředek.

```azurecli
az deployment group create \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --template-file azuredeploy.json \
  --parameters azuredeploy.parameters.json
```

Pro pozdější použití uložte ID prostředku spuštění kanálu do proměnné prostředí:

```azurecli
IMPORT_RUN_RES_ID=$(az deployment group show \
  --resource-group $TARGET_RG \
  --name importPipelineRun \
  --query 'properties.outputResources[0].id' \
  --output tsv)

When deployment completes successfully, verify artifact import by listing the repositories in the target container registry. For example, run [az acr repository list][az-acr-repository-list]:

```azurecli
az acr repository list --name <target-registry-name>
```

## <a name="redeploy-pipelinerun-resource"></a>Znovu nasadit prostředek PipelineRun

Pokud se znovu nasadí prostředek PipelineRun se *stejnými vlastnostmi*, je nutné využít vlastnost **forceUpdateTag** . Tato vlastnost označuje, že prostředek PipelineRun by měl být znovu vytvořen i v případě, že se konfigurace nezměnila. Ujistěte se prosím, že forceUpdateTag je pokaždé, když znovu nasadíte prostředek PipelineRun. Následující příklad znovu vytvoří PipelineRun pro export. Aktuální hodnota DateTime slouží k nastavení forceUpdateTag, čímž je zajištěno, že tato vlastnost je vždy jedinečná.

```console
CURRENT_DATETIME=`date +"%Y-%m-%d:%T"`
```

```azurecli
az deployment group create \
  --resource-group $SOURCE_RG \
  --template-file azuredeploy.json \
  --name exportPipelineRun \
  --parameters azuredeploy.parameters.json \
  --parameters forceUpdateTag=$CURRENT_DATETIME
```

## <a name="delete-pipeline-resources"></a>Odstranění prostředků kanálu

Následující příklady příkazů pomocí příkazu [AZ Resource Delete][az-resource-delete] odstraní prostředky kanálu vytvořené v tomto článku. ID prostředků byly dřív uloženy v proměnných prostředí.

```
# Delete export resources
az resource delete \
--resource-group $SOURCE_RG \
--ids $EXPORT_RES_ID $EXPORT_RUN_RES_ID \
--api-version 2019-12-01-preview

# Delete import resources
az resource delete \
--resource-group $TARGET_RG \
--ids $IMPORT_RES_ID $IMPORT_RUN_RES_ID \
--api-version 2019-12-01-preview
```

## <a name="troubleshooting"></a>Řešení potíží

* **Template deployment chyby nebo chyby**
  * Pokud se spuštění kanálu nezdařilo, podívejte se na `pipelineRunErrorMessage` vlastnost prostředku spuštění.
  * Chyby při nasazení běžných šablon najdete v tématu [řešení potíží s nasazeními šablon ARM](../azure-resource-manager/templates/template-tutorial-troubleshoot.md) .
* **Problémy s exportem nebo importem objektů BLOB úložiště**
  * Vypršela platnost tokenu SAS nebo může mít nedostatečná oprávnění pro zadaný příkaz pro export nebo import.
  * Existující objekt BLOB úložiště ve zdrojovém účtu úložiště se při několika spuštěních exportu nemusí přepsat. Ověřte, že je v běhu pro export nastavená možnost OverwriteBlob a že token SAS má dostatečná oprávnění.
  * Po úspěšném spuštění importu se nemusí odstranit objekt BLOB úložiště v cílovém účtu úložiště. Ověřte, že je v běhu importu nastavená možnost DeleteBlobOnSuccess a že token SAS má dostatečná oprávnění.
  * Objekt BLOB úložiště se nevytvořil nebo se odstranil. Potvrďte, že kontejner zadaný v běhu export nebo import existuje, nebo pokud pro ruční spuštění importu existuje zadaný objekt BLOB úložiště. 
* **Problémy AzCopy**
  * Přečtěte si téma [řešení potíží s AzCopy](../storage/common/storage-use-azcopy-configure.md#troubleshoot-issues).  
* **Problémy s přenosem artefaktů**
  * Ne všechny artefakty nebo žádné, jsou přeneseny. Potvrďte kontrolu pravopisu artefaktů při spuštění exportu a název objektu BLOB v běhůch exportu a importu. Potvrďte, že přenášíte maximálně 50 artefaktů.
  * Běh kanálu možná není dokončený. Spuštění exportu nebo importu může nějakou dobu trvat. 
  * Pro jiné problémy s kanály zadejte [ID korelace](../azure-resource-manager/templates/deployment-history.md) nasazení pro spuštění exportu nebo spusťte import do týmu Azure Container Registry.


## <a name="next-steps"></a>Další kroky

Informace o importování jediné image kontejneru do služby Azure Container Registry z veřejného registru nebo jiného privátního registru najdete v tématu [AZ ACR import][az-acr-import] Command reference.

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/



<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-login]: /cli/azure/reference-index#az-login
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-secret-show]: /cli/azure/keyvault/secret#az-keyvault-secret-show
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-storage-container-generate-sas]: /cli/azure/storage/container#az-storage-container-generate-sas
[az-storage-blob-list]: /cli/azure/storage/blob#az-storage-blob-list
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-deployment-group-delete]: /cli/azure/deployment/group#az-deployment-group-delete
[az-deployment-group-show]: /cli/azure/deployment/group#az-deployment-group-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-import]: /cli/azure/acr#az-acr-import
[az-resource-delete]: /cli/azure/resource#az-resource-delete
