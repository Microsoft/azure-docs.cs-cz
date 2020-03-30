---
title: Kopírování dat v cloudu pomocí PowerShellu
description: Tento skript Prostředí PowerShell zkopíruje data z jednoho umístění v úložišti objektů blob Azure do jiného umístění ve stejném úložišti objektů blob.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 2e289f30ef1c1883c38884eb563a41bccc841329
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79462647"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Použití PowerShellu k vytvoření kanálu datové továrny ke kopírování dat v cloudu

Tento ukázkový skript Prostředí PowerShell vytvoří kanál v Azure Data Factory, který kopíruje data z jednoho umístění do jiného umístění v úložišti objektů blob Azure.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Požadavky
* **Účet Azure Storage**. Úložiště objektů blob použijete jako úložiště dat pro **zdroj** i **jímku**. Pokud nemáte účet úložiště Azure, přečtěte si při jeho vytvoření [viz Vytvoření účtu úložiště.](../../storage/common/storage-account-create.md) 
* Vytvořte **kontejner objektů blob** ve službě Blob Storage, v tomto kontejneru vytvořte vstupní **složku** a uložte do ní nějaké soubory. Nástroje, jako je [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/), můžete použít k připojení k úložišti objektů blob v Azure, k vytvoření kontejneru objektů blob, nahrání vstupního souboru a ověření výstupního souboru.

## <a name="sample-script"></a>Ukázkový skript

> [!IMPORTANT]
> Tento skript vytvoří soubory JSON, které definují entity Data Factory (propojená služba, datová sada a kanál) na pevném disku v c:\ Složky.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


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
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Vytvoří propojenou službu v datové továrně. Propojená služba propojuje úložiště dat nebo výpočetní prostředky s továrnou na data. |
| [Sada-AzDataFactoryV2Sada dat](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Vytvoří datovou sadu v datové továrně. Datová sada představuje vstup a výstup pro aktivitu v kanálu. |
| [Kanál Set-AzDataFactoryV2](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Vytvoří kanál v datové továrně. Kanál obsahuje jednu nebo více aktivit, které provádějí určitou operaci. V tomto kanálu zkopíruje aktivitu kopírování data z jednoho umístění do jiného umístění v úložišti objektů blob Azure. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Vytvoří spuštění pro kanál. Jinými slovy, spustí potrubí. |
| [Spustit get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Získá podrobnosti o spuštění aktivity (aktivita spustit) v kanálu.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další kroky

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázky skriptu Azure Data Factory PowerShell unavují [ukázky prostředí Azure Data Factory.](../samples-powershell.md)
