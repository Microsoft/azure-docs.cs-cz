---
title: Kurz – nasazení propojené šablony
description: Naučte se nasadit propojenou šablonu.
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 81a8b6cd5044b31ee80f50448d6610ba6a57281e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585645"
---
# <a name="tutorial-deploy-a-linked-template"></a>Kurz: nasazení propojené šablony

V [předchozích kurzech](./deployment-tutorial-local-template.md)jste zjistili, jak nasadit šablonu, která je uložená v místním počítači. Pro nasazení složitých řešení můžete rozdělit šablonu na mnoho šablon a tyto šablony nasadit pomocí hlavní šablony. V tomto kurzu se naučíte, jak nasadit hlavní šablonu obsahující odkaz na propojenou šablonu. Jakmile se nasadí hlavní šablona, aktivuje se nasazení propojené šablony. Naučíte se také, jak uložit a zabezpečit propojenou šablonu pomocí tokenu SAS. Dokončení trvá přibližně **12 minut** .

## <a name="prerequisites"></a>Předpoklady

Doporučujeme, abyste dokončili předchozí kurz, ale není to nutné.

## <a name="review-template"></a>Zkontrolovat šablonu

V předchozích kurzech nasadíte šablonu, která vytvoří účet úložiště, App Service plán a webovou aplikaci. Použitá šablona:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Vytvoření propojené šablony

Prostředek účtu úložiště můžete oddělit do propojené šablony:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Následující šablona je hlavní šablonou. Zvýrazněný `Microsoft.Resources/deployments` objekt ukazuje, jak zavolat propojenou šablonu. Propojená šablona nemůže být uložena jako místní soubor nebo soubor, který je k dispozici pouze v místní síti. Můžete zadat jenom hodnotu identifikátoru URI, která zahrnuje buď HTTP, nebo HTTPS. Správce prostředků musí být schopné získat přístup k šabloně. Jednou z možností je umístit propojenou šablonu do účtu úložiště a použít identifikátor URI pro tuto položku. Identifikátor URI je předán šabloně pomocí parametru. Podívejte se na zvýrazněnou definici parametru.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Uložte kopii hlavní šablony do místního počítače s příponou _. JSON_ , například _azuredeploy.js_. Nemusíte ukládat kopii propojené šablony. Odkazovaná šablona se zkopíruje z úložiště GitHubu do účtu úložiště.

## <a name="store-the-linked-template"></a>Uložit propojenou šablonu

Následující skript prostředí PowerShell vytvoří účet úložiště, vytvoří kontejner a zkopíruje propojenou šablonu z úložiště GitHub do kontejneru. Kopie propojené šablony je uložená na [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Vyberte **try-it** a otevřete Cloud Shell, vyberte **Kopírovat** pro zkopírování skriptu PowerShellu a kliknutím pravým tlačítkem na podokno prostředí vložte skript:

> [!IMPORTANT]
> Názvy účtů úložiště musí mít délku 3 až 24 znaků a musí obsahovat jenom číslice a malá písmena. Název musí být jedinečný. V šabloně je název účtu úložiště název projektu s připojeným **úložištěm** a název projektu musí být dlouhý 3 až 11 znaků. Proto název projektu musí splňovat požadavky na název účtu úložiště a má méně než 11 znaků.

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

Pokud chcete v účtu úložiště nasadit soukromou šablonu, vygenerujte token SAS a zahrňte ho do identifikátoru URI pro šablonu. Nastavte čas vypršení platnosti, abyste měli dostatek času na dokončení nasazení. Objekt BLOB obsahující šablonu je přístupný pouze pro vlastníka účtu. Pokud však vytvoříte token SAS pro objekt blob, je objekt BLOB přístupný komukoli s tímto identifikátorem URI. Pokud identifikátor URI zachytí jiný uživatel, bude mít tento uživatel k šabloně přístup. Token SAS je dobrým způsobem, jak omezit přístup k vašim šablonám, neměli byste ale obsahovat citlivá data, jako jsou hesla přímo v šabloně.

Pokud jste ještě nevytvořili skupinu prostředků, přečtěte si téma [Vytvoření skupiny prostředků](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> V níže uvedeném kódu Azure CLI `date` je parametr `-d` v MacOS neplatný argument. Takže uživatelé macOS k aktuálnímu času v terminálu na macOS, kterou byste měli použít, aby přidali 2 hodiny `-v+2H` .

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

Vyčistěte prostředky, které jste nasadili, odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak nasadit propojenou šablonu. V dalším kurzu se dozvíte, jak vytvořit DevOps kanál pro nasazení šablony.

> [!div class="nextstepaction"]
> [Vytvoření kanálu](./deployment-tutorial-pipeline.md)
