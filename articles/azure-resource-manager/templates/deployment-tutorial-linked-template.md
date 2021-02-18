---
title: Kurz – nasazení propojené šablony
description: Naučte se nasadit propojenou šablonu.
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 8f2bbd327adca6eef62d5e79f422f61d460ea7a5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589268"
---
# <a name="tutorial-deploy-a-linked-template"></a>Kurz: nasazení propojené šablony

V [předchozích kurzech](./deployment-tutorial-local-template.md)jste zjistili, jak nasadit šablonu, která je uložená v místním počítači. Pro nasazení složitých řešení můžete rozdělit šablonu na mnoho šablon a tyto šablony nasadit pomocí hlavní šablony. V tomto kurzu se naučíte, jak nasadit hlavní šablonu obsahující odkaz na propojenou šablonu. Jakmile se nasadí hlavní šablona, aktivuje se nasazení propojené šablony. Naučíte se také, jak ukládat a zabezpečovat šablony pomocí tokenu SAS. Dokončení trvá přibližně **12 minut** .

## <a name="prerequisites"></a>Požadavky

Doporučujeme, abyste dokončili předchozí kurz, ale není to nutné.

## <a name="review-template"></a>Zkontrolovat šablonu

V předchozích kurzech nasadíte šablonu, která vytvoří účet úložiště, App Service plán a webovou aplikaci. Použitá šablona:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Vytvoření propojené šablony

Prostředek účtu úložiště můžete oddělit do propojené šablony:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Následující šablona je hlavní šablonou. Zvýrazněný `Microsoft.Resources/deployments` objekt ukazuje, jak zavolat propojenou šablonu. Propojená šablona nemůže být uložena jako místní soubor nebo soubor, který je k dispozici pouze v místní síti. Můžete buď zadat hodnotu identifikátoru URI propojené šablony, která obsahuje buď HTTP nebo HTTPS, nebo použít vlastnost _RelativePath_ k nasazení vzdálené propojené šablony v umístění relativně k nadřazené šabloně. Jednou z možností je umístit hlavní šablonu i propojenou šablonu do účtu úložiště.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>Uložit propojenou šablonu

Obě hlavní šablony i propojená šablona jsou uložené na GitHubu:

Následující skript prostředí PowerShell vytvoří účet úložiště, vytvoří kontejner a zkopíruje dvě šablony z úložiště GitHub do kontejneru. Tyto dvě šablony:

- Hlavní Šablona: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- Odkazovaná Šablona: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Vyberte **try-it** a otevřete Cloud Shell, vyberte **Kopírovat** pro zkopírování skriptu PowerShellu a kliknutím pravým tlačítkem na podokno prostředí vložte skript:

> [!IMPORTANT]
> Názvy účtů úložiště musí mít délku 3 až 24 znaků a musí obsahovat jenom číslice a malá písmena. Název musí být jedinečný. V šabloně je název účtu úložiště název projektu s připojeným **úložištěm** a název projektu musí být dlouhý 3 až 11 znaků. Proto název projektu musí splňovat požadavky na název účtu úložiště a má méně než 11 znaků.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Nasazení šablony

Pokud chcete v účtu úložiště nasadit šablony, vygenerujte token SAS a poskytněte ho do parametru _-QueryString_ . Nastavte čas vypršení platnosti, abyste měli dostatek času na dokončení nasazení. Objekty BLOB obsahující šablony jsou přístupné pouze pro vlastníka účtu. Když ale vytvoříte token SAS pro objekt blob, je objekt BLOB přístupný komukoli s tímto tokenem SAS. Pokud jiný uživatel zachytí identifikátor URI a token SAS, bude mít tento uživatel k šabloně přístup. Token SAS je dobrým způsobem, jak omezit přístup k vašim šablonám, neměli byste ale obsahovat citlivá data, jako jsou hesla přímo v šabloně.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> V níže uvedeném kódu Azure CLI `date` je parametr `-d` v MacOS neplatný argument. Takže uživatelé macOS k aktuálnímu času v terminálu na macOS, kterou byste měli použít, aby přidali 2 hodiny `-v+2H` .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
  --verbose
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak nasadit propojenou šablonu. V dalším kurzu se dozvíte, jak vytvořit DevOps kanál pro nasazení šablony.

> [!div class="nextstepaction"]
> [Vytvoření kanálu](./deployment-tutorial-pipeline.md)