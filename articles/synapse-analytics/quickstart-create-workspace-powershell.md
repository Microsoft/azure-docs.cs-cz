---
title: 'Rychlý Start: vytvoření pracovního prostoru synapse pomocí Azure PowerShell'
description: Pomocí Azure PowerShell podle kroků v této příručce vytvořte pracovní prostor Azure synapse.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: overview
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a19d1f26616697e15ae4c39a63c44bdaf83f78f9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675763"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>Rychlý Start: vytvoření pracovního prostoru Azure synapse s využitím Azure PowerShell

Azure PowerShell je sada rutin pro správu prostředků Azure přímo z PowerShellu. Můžete ho používat ve svém prohlížeči prostřednictvím služby Azure Cloud Shell. Můžete ho také nainstalovat na macOS, Linux nebo Windows.

V tomto rychlém startu se naučíte vytvořit pracovní prostor synapse pomocí Azure PowerShell.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- [Účet úložiště Azure Data Lake Storage Gen2](../storage/common/storage-account-create.md)

    > [!IMPORTANT]
    > Pracovní prostor Azure synapse musí být schopný číst a zapisovat na vybraný ADLS Gen2 účet. Pro libovolný účet úložiště, který propojíte jako primární účet úložiště, musíte povolit **hierarchický obor názvů** při vytváření účtu úložiště, jak je popsáno v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md?tabs=azure-powershell#create-a-storage-account).

Pokud se rozhodnete použít Cloud Shell, přečtěte si téma [přehled Azure Cloud Shell](../cloud-shell/overview.md) , kde najdete další informace.

### <a name="install-the-azure-powershell-module-locally"></a>Místní instalace modulu Azure PowerShell

Pokud se rozhodnete použít prostředí PowerShell místně, Tento článek vyžaduje, abyste nainstalovali modul AZ PowerShell a připojili se k účtu Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Další informace o instalaci modulu AZ PowerShell najdete v tématu [Install Azure PowerShell](/powershell/azure/install-az-ps).

Další informace o ověřování pomocí Azure PowerShell najdete v tématu věnovaném [přihlášení pomocí Azure PowerShell](/powershell/azure/authenticate-azureps).

### <a name="install-the-azure-synapse-powershell-module"></a>Instalace modulu PowerShellu pro Azure synapse

> [!IMPORTANT]
> I když je modul PowerShell **AZ. synapse** ve verzi Preview, musíte ho nainstalovat samostatně pomocí `Install-Module` rutiny. Jakmile bude tento powershellový modul obecně dostupný, stane se součástí budoucích verzí modulu Az PowerShellu a bude ve výchozím nastavení dostupný v rámci Azure Cloud Shellu.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Vytvoření pracovního prostoru Azure synapse pomocí Azure PowerShell

1. Definujte potřebné proměnné prostředí pro vytváření prostředků pro pracovní prostor Azure synapse.

   |        Název proměnné        |                                                 Popis                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | název_účtu_úložiště          | Název existujícího účtu úložiště ADLS Gen2.                                                           |
   | StorageAccountResourceGroup | Název existující skupiny prostředků účtu úložiště ADLS Gen2                                             |
   | FileShareName               | Název existujícího systému souborů úložiště.                                                                  |
   | SynapseResourceGroup        | Vyberte nový název skupiny prostředků Azure synapse.                                                    |
   | Oblast                      | Vyberte jednu z [oblastí Azure](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
   | SynapseWorkspaceName        | Vyberte jedinečný název pro nový pracovní prostor Azure synapse.                                                  |
   | SqlUser                     | Vyberte hodnotu pro nové uživatelské jméno.                                                                          |
   | SqlPassword                 | Vyberte zabezpečené heslo.                                                                                   |
   | IP adresa klienta                    | Veřejná IP adresa systému, ze kterého spouštíte PowerShell                                             |
   |                             |                                                                                                             |

1. Vytvořte skupinu prostředků jako kontejner pro váš pracovní prostor Azure synapse:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Vytvořte pracovní prostor Azure synapse:

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. Získání webové a vývojářské adresy URL pro pracovní prostor Azure synapse:

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. Vytvořte pravidlo brány firewall, které umožní přístup k pracovnímu prostoru Azure synapse z počítače:

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. Otevřete adresu URL webu v pracovním prostoru Azure synapse, která je uložená v proměnné prostředí `WorkspaceWeb` pro přístup k vašemu pracovnímu prostoru:

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Web Azure synapse Workspace](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete odstranit pracovní prostor Azure synapse, postupujte podle následujících kroků.

> [!WARNING]
> Při odstranění pracovního prostoru Azure synapse se odstraní analytické moduly a data uložená v databázi obsažených fondů SQL a metadat pracovního prostoru. Již nebude možné se připojit k koncovým bodům SQL nebo Apache Spark. Všechny artefakty kódu se odstraní (dotazy, poznámkové bloky, definice úloh a kanály). Odstranění pracovního prostoru nebude **mít vliv** na data v Data Lake Store Gen2, která jsou propojená s pracovním prostorem.

Pokud pracovní prostor Azure synapse vytvořený v tomto článku není potřeba, můžete ho odstranit spuštěním následujícího příkladu.

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>Další kroky

V dalším kroku můžete [vytvořit fondy SQL](quickstart-create-sql-pool-studio.md) nebo [vytvořit fondy Apache Spark](quickstart-create-apache-spark-pool-studio.md) , abyste mohli začít analyzovat a prozkoumat data.