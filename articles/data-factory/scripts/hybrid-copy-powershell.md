---
title: 'Skript prostředí PowerShell: kopírování dat z místní do Azure pomocí služby Data Factory | Microsoft Docs'
description: Tento skript prostředí PowerShell zkopíruje data z místní databáze systému SQL Server do jiné Azure Blob Storage.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: jingwang
ms.openlocfilehash: 5c0252e42dd3dd53a0a95684553b193b983e95ee
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
ms.locfileid: "30166870"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Vytvoření kanálu data factory ke zkopírování dat z místního do Azure pomocí prostředí PowerShell

Tento ukázkový skript prostředí PowerShell vytvoří kanál v Azure Data Factory, který kopíruje data z místní databáze SQL serveru do Azure Blob Storage.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadavky

- **SQL Server**. Můžete použít místní databázi systému SQL Server jako **zdroj** úložiště dat v této ukázce.
- **Účet služby Azure Storage**. Použít úložiště objektů blob v Azure jako **cílové/jímka** úložiště dat v této ukázce. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-create-storage-account.md#create-a-storage-account), kde najdete kroky pro jeho vytvoření.
- **Hostovanou na vlastním integrace runtime**. Stažení souboru MSI z [centra stahování softwaru společnosti](https://www.microsoft.com/download/details.aspx?id=39717) a spusťte ho k instalaci modulu runtime vlastním hostováním integrace na váš počítač.  

### <a name="create-sample-database-in-sql-server"></a>Vytvoření ukázkové databáze v systému SQL Server
1. V místní databázi systému SQL Server, vytvořte tabulku s názvem **emp** pomocí následující skript SQL: 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Vložte ukázková data do tabulky:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Ukázkový skript

> [!IMPORTANT]
> Tento skript vytvoří soubory JSON, které definují entit služby Data Factory (propojené služby, datové sady a kanál) na vašem pevném disku ve složce c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkový skript můžete odebrat skupinu prostředků a všechny prostředky, které jsou s ním spojená následující příkaz:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Chcete-li odebrat objekt pro vytváření dat ze skupiny prostředků, spusťte následující příkaz: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy: 

| Příkaz | Poznámky |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Vytvoření datové továrny |
| [New-AzureRmDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedserviceencryptedcredential) | Šifruje pověření v propojené službě a generuje novou definici propojenou službu s šifrovaný přihlašovací údaj. 
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | V datové továrně vytvoří propojené služby. Propojená služba odkazuje na objekt pro vytváření dat v úložišti dat nebo výpočetní. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Vytvoří datovou sadu v datové továrně. Datové sady představuje vstupu a výstupu pro aktivitu v kanálu. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Vytvoří v objektu pro vytváření dat kanál. Kanál obsahuje jeden nebo více aktivit, které provádí určité operaci. U tohoto kanálu aktivity kopírování kopíruje data z jednoho umístění do jiného umístění v Azure Blob Storage. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | Vytvoří spustit pro kanál. Jinými slovy spouští kanálu. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Získá informace o spouštění aktivity (aktivity při spuštění) v kanálu. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu prostředí PowerShell objekt pro vytváření dat Azure lze nalézt v [ukázky Azure Data Factory PowerShell](../samples-powershell.md).