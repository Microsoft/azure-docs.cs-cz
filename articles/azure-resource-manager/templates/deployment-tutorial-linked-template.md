---
title: Kurz – nasazení propojené šablony
description: Přečtěte si, jak nasadit propojenou šablonu.
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 70a09315b0947f41e7602e630460cb3e674a7bf8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80082241"
---
# <a name="tutorial-deploy-a-linked-template"></a>Kurz: Nasazení propojené šablony

V [předchozích kurzech](./deployment-tutorial-local-template.md)jste se naučili nasadit šablonu, která je uložena v místním počítači. Chcete-li nasadit komplexní řešení, můžete rozdělit šablonu do mnoha šablon a nasadit tyto šablony prostřednictvím hlavní šablony. V tomto kurzu se dozvíte, jak nasadit hlavní šablonu, která obsahuje odkaz na propojenou šablonu. Když se nasadí hlavní šablona, spustí nasazení propojené šablony. Můžete také zjistit, jak ukládat a zabezpečit propojené šablony pomocí tokenu SAS. To trvá asi **12 minut** na dokončení.

## <a name="prerequisites"></a>Požadavky

Doporučujeme dokončit předchozí kurz, ale není to nutné.

## <a name="review-template"></a>Šablona revize

V předchozích kurzech nasadíte šablonu, která vytvoří účet úložiště, plán služby App Service a webovou aplikaci. Použitá šablona byla:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Vytvoření propojené šablony

Prostředek účtu úložiště můžete oddělit do propojené šablony:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Následující šablona je hlavní šablona.  Zvýrazněný objekt **Microsoft.Resources/deployments** ukazuje, jak volat propojenou šablonu. Propojenou šablonu nelze uložit jako místní soubor nebo soubor, který je k dispozici pouze v místní síti. Můžete zadat pouze hodnotu IDENTIFIKÁTORU URI, která obsahuje protokol *http* nebo *https*. Správce prostředků musí mít přístup k šabloně. Jednou z možností je umístit propojenou šablonu do účtu úložiště a použít identifikátor URI pro tuto položku. Identifikátor URI je předán šabloně pomocí parametru. Podívejte se na zvýrazněnou definici parametru.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Uložte kopii hlavní šablony do místního počítače s příponou JSON, například azuredeploy.json. Není nutné ukládat kopii propojené šablony.  Propojená šablona se zkopíruje z úložiště GitHub do účtu úložiště.

## <a name="store-the-linked-template"></a>Uložení propojené šablony

Následující skript Prostředí PowerShell vytvoří účet úložiště, vytvoří kontejner a zkopíruje propojenou šablonu z úložiště GitHub do kontejneru. Kopie propojené šablony je uložena v [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Vyberte **Try-it, chcete-li** otevřít prostředí Cloud, vyberte **Kopírovat,** chcete-li zkopírovat skript prostředí PowerShell, a kliknutím pravým tlačítkem myši na podokno prostředí skript vložte:

> [!IMPORTANT]
> Názvy účtů úložiště musí mít délku 3 až 24 znaků a používat pouze čísla a malá písmena. Název musí být jedinečný. V šabloně je název účtu úložiště název projektu s připojeným "store" a název projektu musí být mezi 3 a 11 znaků. Název projektu tedy musí splňovat požadavky na název účtu úložiště a má méně než 11 znaků.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

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

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Nasazení šablony

Chcete-li nasadit soukromou šablonu v účtu úložiště, vygenerujte token SAS a zahrňte jej do identifikátoru URI pro šablonu. Nastavte dobu vypršení platnosti, aby byl dostatek času na dokončení nasazení. Objekt blob obsahující šablonu je přístupný pouze vlastníkovi účtu. Však při vytvoření tokenu SAS pro objekt blob, objekt blob je přístupný všem uživatelům s tímto URI. Pokud jiný uživatel zachytí identifikátor URI, bude mít k šabloně přístup. Token SAS je dobrý způsob, jak omezit přístup k šablonám, ale neměli byste zahrnout citlivá data, jako jsou hesla přímo do šablony.

Pokud jste skupinu prostředků nevytvořili, přečtěte si informace [o vytvoření skupiny prostředků](./deployment-tutorial-local-template.md#create-resource-group).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
  --verbose
```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky, které jste nasadili odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak nasadit propojenou šablonu. V dalším kurzu se dozvíte, jak vytvořit kanál DevOp k nasazení šablony.

> [!div class="nextstepaction"]
> [Vytvoření kanálu](./deployment-tutorial-pipeline.md)
