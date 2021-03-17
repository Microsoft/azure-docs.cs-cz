---
title: Bezpečné nasazení šablony s tokenem SAS
description: K nasazení prostředků do Azure můžete použít šablonu Azure Resource Manager chráněnou tokenem SAS. Zobrazuje Azure PowerShell a Azure CLI.
ms.topic: conceptual
ms.date: 08/25/2020
ms.openlocfilehash: 8b35e82da8ebca98ec9fe1fb7441612bf61fb142
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88855661"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Nasazení privátní šablony ARM pomocí tokenu SAS

Když je šablona Azure Resource Manager (šablona ARM) umístěná v účtu úložiště, můžete omezit přístup k šabloně, abyste ji předešli veřejně vystavení. Přístup k zabezpečené šabloně získáte tak, že pro šablonu vytvoříte token sdíleného přístupového podpisu (SAS) a tento token během nasazování zadáte. Tento článek vysvětluje, jak pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure nasadit šablonu s tokenem SAS.

> [!IMPORTANT]
> Místo zabezpečení šablony pomocí tokenu SAS zvažte použití [specifikací šablony](template-specs.md). Pomocí specifikací šablon můžete sdílet šablony s ostatními uživateli ve vaší organizaci a spravovat přístup k šablonám prostřednictvím Azure RBAC.

## <a name="create-storage-account-with-secured-container"></a>Vytvořit účet úložiště s zabezpečeným kontejnerem

Následující skript vytvoří účet úložiště a kontejner s povoleným veřejným přístupem.

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

## <a name="upload-template-to-storage-account"></a>Odeslat šablonu do účtu úložiště

Nyní jste připraveni odeslat šablonu do účtu úložiště. Zadejte cestu k šabloně, kterou chcete použít.

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

Pokud chcete v účtu úložiště nasadit soukromou šablonu, vygenerujte token SAS a zahrňte ho do identifikátoru URI pro šablonu. Nastavte čas vypršení platnosti, abyste měli dostatek času na dokončení nasazení.

> [!IMPORTANT]
> Objekt BLOB obsahující šablonu je přístupný pouze pro vlastníka účtu. Pokud však vytvoříte token SAS pro objekt blob, je objekt BLOB přístupný komukoli s tímto identifikátorem URI. Pokud identifikátor URI zachytí jiný uživatel, bude mít tento uživatel k šabloně přístup. Token SAS je dobrým způsobem, jak omezit přístup k vašim šablonám, neměli byste ale obsahovat citlivá data, jako jsou hesla přímo v šabloně.
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

Následující příklad funguje s prostředím bash v Cloud Shell. Jiná prostředí můžou vyžadovat jinou syntaxi, aby se vytvořil čas vypršení platnosti tokenu SAS.

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

Příklad použití tokenu SAS s propojenými šablonami najdete v tématu [použití propojených šablon s Azure Resource Manager](linked-templates.md).


## <a name="next-steps"></a>Další kroky
* Úvod k nasazení šablon najdete v tématu [nasazení prostředků pomocí šablon ARM a Azure PowerShell](deploy-powershell.md).
* Informace o definování parametrů v šabloně najdete v tématu [vytváření šablon](template-syntax.md#parameters).
