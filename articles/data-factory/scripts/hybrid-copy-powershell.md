---
title: 'Skript prostředí PowerShell: kopírování dat z místních do Azure pomocí služby Data Factory | Dokumentace Microsoftu'
description: Tento skript Powershellu kopíruje data z místní databáze systému SQL Server do jiné služby Azure Blob Storage.
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
ms.openlocfilehash: 3d73f6dc06ccd9aa8b3e81754b66e81b3e8252fc
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43125460"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Použití Powershellu k vytvoření kanálu datové továrny ke zkopírování dat z místních do Azure

Tento ukázkový skript Powershellu vytvoří kanál ve službě Azure Data Factory, který kopíruje data z místní databáze systému SQL Server do služby Azure Blob Storage.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Požadavky

- **SQL Server**. Použijete místní databázi systému SQL Server jako **zdroj** úložiště dat v této ukázce.
- **Účet služby Azure Storage**. Používání úložiště objektů blob v Azure jako **cíl/jímka** úložiště dat v této ukázce. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md), kde najdete kroky pro jeho vytvoření.
- **Modul runtime integrace v místním prostředí**. Stáhněte soubor MSI ze služby [centra stahování](https://www.microsoft.com/download/details.aspx?id=39717) a jeho spuštěním nainstalujte místní prostředí integration runtime na vašem počítači.  

### <a name="create-sample-database-in-sql-server"></a>Vytvoření ukázkové databáze na SQL serveru
1. V místní databázi systému SQL Server, vytvořte tabulku s názvem **emp** pomocí následujícího skriptu SQL: 

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

2. Vložte nějaká ukázková data do tabulky:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Ukázkový skript

> [!IMPORTANT]
> Tento skript vytvoří soubory JSON, které definují entity služby Data Factory (propojené služby, datové sady a kanál) na váš pevný disk do složky c:\.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete k odebrání skupiny prostředků a všechny prostředky, které s ním spojená následující příkaz:

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
| [New-AzureRmDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedserviceencryptedcredential) | Šifruje přihlašovací údaje v propojené službě a vytvoří novou definici propojené služby s šifrované přihlašovací údaje. 
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Vytvoření propojené služby ve službě data factory. Propojená služba propojuje úložiště dat nebo výpočetní výkon s datovou továrnou. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Vytvoří datovou sadu ve službě data factory. Datová sada představuje vstup/výstup pro aktivitu v kanálu. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2pipeline) | Ve službě data factory vytvoří kanál. Kanál obsahuje jednu nebo víc aktivit, které provádí určité operace. U tohoto kanálu aktivita kopírování kopíruje data z jednoho umístění do jiného umístění ve službě Azure Blob Storage. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipeline) | Vytvoří spuštění kanálu. Jinými slovy spouští kanál. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Načte podrobnosti o spuštění aktivit (spuštění aktivit) v kanálu. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty Azure Powershellu objekt pro vytváření dat najdete v [ukázky Azure Powershellu objekt pro vytváření dat](../samples-powershell.md).
