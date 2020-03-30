---
title: Bezpečné nasazení šablony pomocí tokenu SAS
description: Nasazení prostředků do Azure pomocí šablony Azure Resource Manageru, která je chráněná tokenem SAS. Zobrazuje Azure PowerShell a Azure CLI.
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 42eaae316d4fd0575102323933f849a3058228a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156391"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Nasazení soukromé šablony ARM pomocí tokenu SAS

Když je vaše šablona Správce prostředků Azure (ARM) umístěna v účtu úložiště, můžete omezit přístup k šabloně, abyste se vyhnuli jeho veřejnému vystavení. Přístup k zabezpečené šabloně vytvořením tokenu sdíleného přístupového podpisu (SAS) pro šablonu a poskytnutím tohoto tokenu během nasazení. Tento článek vysvětluje, jak použít Azure PowerShell nebo Azure CLI k nasazení šablony s tokenem SAS.

## <a name="create-storage-account-with-secured-container"></a>Vytvoření účtu úložiště se zabezpečeným kontejnerem

Následující skript vytvoří účet úložiště a kontejner s vypnutým veřejným přístupem.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Nahrát šablonu do účtu úložiště

Nyní jste připraveni nahrát šablonu do účtu úložiště. Zadejte cestu k šabloně, kterou chcete použít.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Poskytnout token SAS během nasazení

Chcete-li nasadit soukromou šablonu v účtu úložiště, vygenerujte token SAS a zahrňte jej do identifikátoru URI pro šablonu. Nastavte dobu vypršení platnosti, aby byl dostatek času na dokončení nasazení.

> [!IMPORTANT]
> Objekt blob obsahující šablonu je přístupný pouze vlastníkovi účtu. Však při vytvoření tokenu SAS pro objekt blob, objekt blob je přístupný všem uživatelům s tímto URI. Pokud jiný uživatel zachytí identifikátor URI, bude mít k šabloně přístup. Token SAS je dobrý způsob, jak omezit přístup k šablonám, ale neměli byste zahrnout citlivá data, jako jsou hesla přímo do šablony.
>

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Příklad použití tokenu SAS s propojenými šablonami najdete v tématu [Použití propojených šablon ve Správci prostředků Azure](linked-templates.md).


## <a name="next-steps"></a>Další kroky
* Úvod k nasazení šablon najdete v tématu [Nasazení prostředků pomocí šablon ARM a Azure PowerShellu](deploy-powershell.md).
* Chcete-li definovat parametry v šabloně, [přečtěte si](template-syntax.md#parameters)informace o vytváření šablon .
