---
title: Konfigurace vývojového prostředí pro skripty pro nasazení v šablonách | Microsoft Docs
description: Nakonfigurujte vývojové prostředí pro skripty nasazení v šablonách Azure Resource Manager (šablony ARM).
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: 13dc072e31f0d27768de8d9a62ea942d55460713
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936392"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>Konfigurace vývojového prostředí pro skripty pro nasazení v šablonách ARM

Naučte se vytvářet vývojové prostředí pro vývoj a testování skriptů nasazení pomocí Image skriptu nasazení. Můžete buď vytvořit službu [Azure Container instance](../../container-instances/container-instances-overview.md) , nebo použít [Docker](https://docs.docker.com/get-docker/). Jak je popsáno v tomto článku.

## <a name="prerequisites"></a>Požadavky

Pokud nemáte skript nasazení, můžete vytvořit soubor _hello.ps1_ s následujícím obsahem:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

## <a name="use-azure-container-instance"></a>Použití instance kontejneru Azure

Chcete-li vytvářet skripty v počítači, je třeba vytvořit účet úložiště a připojit účet úložiště k instanci kontejneru. Takže můžete nahrát svůj skript do účtu úložiště a spustit skript na instanci kontejneru.

> [!NOTE]
> Účet úložiště, který vytvoříte pro otestování skriptu, není stejný účet úložiště, který služba skriptu nasazení používá ke spuštění skriptu. Služba skriptu nasazení při každém spuštění vytvoří jedinečný název jako sdílená složka.

### <a name="create-an-azure-container-instance"></a>Vytvoření instance kontejneru Azure

Následující šablona Azure Resource Manager (šablona ARM) vytvoří instanci kontejneru a sdílenou složku a poté připojí sdílenou složku k imagi kontejneru.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "deploymentScript",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('projectName'), 'store')]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```

Výchozí hodnota pro cestu pro připojení je `deploymentScript` . Toto je cesta v instanci kontejneru, kde je připojena ke sdílené složce.

Výchozí image kontejneru zadaná v šabloně je `mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3` . Podívejte se na seznam [podporovaných verzí Azure PowerShell](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Podívejte se na seznam [podporovaných verzí rozhraní příkazového řádku Azure CLI](https://mcr.microsoft.com/v2/azure-cli/tags/list).

  >[!IMPORTANT]
  > Skript nasazení používá dostupné image CLI z Microsoft Container Registry (MCR). K certifikaci bitové kopie rozhraní příkazového řádku pro skript nasazení trvá zhruba jeden měsíc. Nepoužívejte verze rozhraní příkazového řádku, které byly vydány do 30 dnů. Data vydání imagí najdete v [poznámkách k verzi Azure CLI](/cli/azure/release-notes-azure-cli?view=azure-cli-latest&preserve-view=true). Pokud se používá Nepodporovaná verze, zobrazí se chybová zpráva s podporovanými verzemi.

Šablona pozastaví instanci kontejneru 1800 sekund. Máte 30 minut, než se instance kontejneru dostane do stavu terminálu a relace skončí.

Nasazení šablony:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-azResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-deployment-script"></a>Nahrání skriptu nasazení

Nahrajte do účtu úložiště skript nasazení. Následuje příklad PowerShellu:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Soubor můžete také nahrát pomocí Azure Portal a Azure CLI.

### <a name="test-the-deployment-script"></a>Test skriptu nasazení

1. Z Azure Portal otevřete skupinu prostředků, do které jste nasadili instanci kontejneru a účet úložiště.
1. Otevřete skupinu kontejnerů. Výchozím názvem skupiny kontejnerů je název **projektu s připojenou příponou** . Je vidět, že instance kontejneru je ve stavu **spuštěno** .
1. V nabídce vlevo vyberte **kontejnery** . Zobrazí se instance kontejneru. Název instance kontejneru je název projektu s připojeným **kontejnerem** .

    ![instance kontejneru nasazení připojit ke skriptu](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Vyberte **připojit** a pak vyberte **připojit**. Pokud se nemůžete připojit k instanci kontejneru, restartujte skupinu kontejnerů a zkuste to znovu.
1. V podokně konzoly spusťte následující příkazy:

    ```console
    cd deploymentScript
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    Výstupem je **Hello Jan dole**.

    ![test instance kontejneru skriptu nasazení](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

1. Pokud použijete image kontejneru AZ CLI, spusťte tento kód:

   ```console
   cd /mnt/azscripts/azscriptinput
   ls
   ./userscript.sh
   ```

## <a name="use-docker"></a>Použít Docker

Jako vývojové prostředí skriptu nasazení můžete použít předem nakonfigurovanou image kontejneru Docker. Pokud chcete nainstalovat Docker, přečtěte si téma [získání Docker](https://docs.docker.com/get-docker/).
Je také nutné nakonfigurovat sdílení souborů pro připojení adresáře, který obsahuje skripty nasazení do kontejneru Docker.

1. Načíst image kontejneru skriptu nasazení do místního počítače:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    V příkladu se používá verze PowerShellu 4.3.0.

    Načtení image CLI z Microsoft Container Registry (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    V tomto příkladu je použita verze rozhraní příkazového řádku 2.0.80. Skript nasazení používá výchozí image kontejnerů rozhraní příkazového řádku, které najdete [tady](https://hub.docker.com/_/microsoft-azure-cli).

1. Spusťte image Docker lokálně.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Nahraďte **&lt; písmeno hostitelského ovladače>** a **&lt; název adresáře hostitele>** existující složkou na sdílené jednotce. Namapuje složku do složky _/data_ v kontejneru. Například pro mapování _D:\docker_:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **–** znamená, že udržuje image kontejneru aktivní.

    Příklad rozhraní příkazového řádku:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Následující snímek obrazovky ukazuje, jak spustit skript prostředí PowerShell s tím, že na sdílené jednotce máte soubor _helloworld.ps1_ .

    ![Správce prostředků skript nasazení skriptu Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Po úspěšném otestování skriptu ho můžete použít jako skript nasazení v šablonách.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat skripty pro nasazení. Návod k procházení skriptu nasazení:

> [!div class="nextstepaction"]
> [Kurz: použití skriptů pro nasazení v šablonách ARM](./template-tutorial-deployment-script.md)
