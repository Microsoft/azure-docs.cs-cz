---
title: Kopírování dat z místního prostředí do Azure pomocí PowerShellu
description: Tento skript PowerShellu zkopíruje data z databáze SQL Server do jiného Azure Blob Storage.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: jingwang
author: linda33wj
manager: shwang
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 6f0a1509a97d2d860b43146ffaf69bb241105910
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84194632"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-sql-server-to-azure"></a>Použití PowerShellu k vytvoření kanálu Data Factory pro kopírování dat z SQL Server do Azure

Tento ukázkový skript PowerShellu vytvoří v Azure Data Factory kanál, který kopíruje data z databáze SQL Server do Azure Blob Storage.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Požadavky

- **SQL Server**. V této ukázce použijete databázi SQL Server jako **zdrojové** úložiště dat.
- **Účet Azure Storage**. V této ukázce použijete úložiště objektů BLOB v Azure jako **cílové úložiště nebo** úložiště dat jímky. Pokud nemáte účet úložiště Azure, přečtěte si článek [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md), kde najdete kroky pro jeho vytvoření.
- **Integration runtime**v místním prostředí. Stáhněte soubor MSI z [webu Download Center](https://www.microsoft.com/download/details.aspx?id=39717) a spusťte ho, abyste na svém počítači nainstalovali místně hostovaný modul runtime integrace.  

### <a name="create-sample-database-in-sql-server"></a>Vytvořit ukázkovou databázi v SQL Server
1. V databázi SQL Server pomocí následujícího skriptu SQL vytvořte tabulku s názvem **EMP** :

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

2. Vložte do tabulky ukázková data:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Ukázkový skript

> [!IMPORTANT]
> Tento skript vytvoří soubory JSON, které definují Data Factory entit (propojená služba, datová sada a kanál) na pevném disku v c:\. složky.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Pokud chcete datovou továrnu ze skupiny prostředků odebrat, spusťte následující příkaz:

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy:

| Příkaz | Poznámky |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků, ve které se ukládají všechny prostředky. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Vytvoření datové továrny |
| [New-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Šifruje přihlašovací údaje v propojené službě a vygeneruje novou definici propojené služby s šifrovanými přihlašovacími údaji.
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Vytvoří propojenou službu v datové továrně. Propojená služba propojuje úložiště dat nebo výpočetní prostředky s datovou továrnou. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Vytvoří datovou sadu v datové továrně. Datová sada představuje vstup/výstup aktivity v kanálu. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Vytvoří v datové továrně kanál. Kanál obsahuje jednu nebo více aktivit, které provádějí určitou operaci. V tomto kanálu aktivita kopírování kopíruje data z jednoho umístění do jiného umístění v Azure Blob Storage. |
| [Invoke – AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Vytvoří běh pro kanál. Jinými slovy, spouští kanál. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Získá podrobnosti o spuštění aktivity (spuštění aktivity) v kanálu.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další Azure Data Factory ukázkových skriptech PowerShellu najdete v [ukázkách Azure Data Factory PowerShellu](../samples-powershell.md).
