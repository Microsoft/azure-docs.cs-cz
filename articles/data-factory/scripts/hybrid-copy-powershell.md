---
title: Kopírování dat z místního prostředí do Azure pomocí PowerShellu
description: Tento skript Prostředí PowerShell zkopíruje data z místní databáze SERVERU SQL Server do jiného úložiště objektů blob Azure.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: jingwang
author: linda33wj
manager: shwang
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 10555defc4888af66bb88d19190b6543aa8ae0c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974692"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Použití PowerShellu k vytvoření kanálu datové továrny ke kopírování dat z místního prostředí do Azure

Tento ukázkový skript Prostředí PowerShell vytvoří kanál v Azure Data Factory, který kopíruje data z místní databáze SQL Serveru do úložiště objektů blob Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Požadavky

- **SQL Server**. V této ukázce se používá místní databáze serveru SQL Server jako **zdrojové** úložiště dat.
- **Účet Azure Storage**. Úložiště objektů blob Azure se používá jako úložiště dat **cílového/cílového úložiště** v této ukázce. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md), kde najdete kroky pro jeho vytvoření.
- **Za běhu integrace s vlastním hostitelem**. Stáhněte si soubor MSI ze [služby download center](https://www.microsoft.com/download/details.aspx?id=39717) a spusťte jej a nainstalujte do počítače runtime integrace s vlastním hostitelem.  

### <a name="create-sample-database-in-sql-server"></a>Vytvoření ukázkové databáze na serveru SQL Server
1. V místní databázi serveru SQL Server vytvořte tabulku s názvem **EMP** pomocí následujícího skriptu SQL:

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

2. Vložte do tabulky některá ukázková data:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Ukázkový skript

> [!IMPORTANT]
> Tento skript vytvoří soubory JSON, které definují entity Data Factory (propojená služba, datová sada a kanál) na pevném disku v c:\ Složky.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní spojené prostředky:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Chcete-li odebrat objekt pro vytváření dat ze skupiny prostředků, spusťte následující příkaz:

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Sada-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Vytvoření datové továrny |
| [New-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Šifruje pověření v propojené službě a generuje novou definici propojené služby se šifrovanými přihlašovacími údaji.
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Vytvoří propojenou službu v datové továrně. Propojená služba propojuje úložiště dat nebo výpočetní prostředky s továrnou na data. |
| [Sada-AzDataFactoryV2Sada dat](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Vytvoří datovou sadu v datové továrně. Datová sada představuje vstup a výstup pro aktivitu v kanálu. |
| [Kanál Set-AzDataFactoryV2](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Vytvoří kanál v datové továrně. Kanál obsahuje jednu nebo více aktivit, které provádí určitou operaci. V tomto kanálu zkopíruje aktivitu kopírování data z jednoho umístění do jiného umístění v úložišti objektů blob Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Vytvoří spuštění pro kanál. Jinými slovy, spustí potrubí. |
| [Spustit get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Získá podrobnosti o spuštění aktivity (aktivita spustit) v kanálu.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu Azure Data Factory PowerShell unavují [ukázky prostředí Azure Data Factory.](../samples-powershell.md)
