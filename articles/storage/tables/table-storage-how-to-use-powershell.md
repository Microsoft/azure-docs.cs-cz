---
title: Provádění operací Azure Table Storage pomocí PowerShellu | Microsoft Docs
description: Naučte se spouštět běžné úlohy, jako je vytváření, dotazování a odstraňování dat z účtu služby Azure Table Storage pomocí PowerShellu.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a7502d48095fe18b983c1971d5145f51e766c95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93306904"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Provádění operací úložiště Azure Table pomocí Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table Storage je NoSQL úložiště dat, které můžete použít k ukládání a dotazování rozsáhlých sad strukturovaných, nerelačních dat. Hlavními komponentami služby jsou tabulky, entity a vlastnosti. Tabulka je kolekce entit. Entita je sada vlastností. Každá entita může mít až 252 vlastností, které jsou všechny páry název-hodnota. V tomto článku se předpokládá, že už jste obeznámeni s koncepty služby Azure Table Storage. Podrobné informace najdete v tématu [Princip datového modelu služby Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) a [Začínáme s úložištěm Azure Table pomocí rozhraní .NET](../../cosmos-db/tutorial-develop-table-dotnet.md).

Tento článek s návody popisuje běžné operace úložišť tabulek Azure. Získáte informace o těchto tématech: 

> [!div class="checklist"]
> * Vytvoření tabulky
> * Načtení tabulky
> * Přidat entity tabulky
> * Dotazování tabulky
> * Odstranit entity tabulky
> * Odstranění tabulky

V tomto článku se dozvíte, jak vytvořit nový účet Azure Storage v nové skupině prostředků, abyste ho mohli po skončení snadno odebrat. Pokud místo toho chcete použít existující účet úložiště, můžete to udělat.

Příklady vyžadují AZ PowerShell modules `Az.Storage (1.1.0 or greater)` a `Az.Resources (1.2.0 or greater)` . V okně PowerShellu spusťte příkaz `Get-Module -ListAvailable Az*` a vyhledejte verzi. Pokud se nic nezobrazí nebo potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Použití této funkce Azure z PowerShellu vyžaduje, abyste `Az` modul nainstalovali. Aktuální verze nástroje `AzTable` není kompatibilní s starším modulem AzureRM.
> V případě potřeby použijte [nejnovější pokyny k instalaci pro instalaci AZ Module](/powershell/azure/install-az-ps) .

Po instalaci nebo aktualizaci Azure PowerShell je nutné nainstalovat modul **AzTable**, který obsahuje příkazy pro správu entit. Pokud chcete tento modul nainstalovat, spusťte PowerShell jako správce a použijte příkaz **install-Module** .

> [!IMPORTANT]
> V případě důvodů kompatibility s názvem modulu stále publikujete stejný modul pod starým názvem `AzureRmStorageTables` v Galerie prostředí PowerShell. Tento dokument bude odkazovat pouze na nový název.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se ke svému předplatnému Azure pomocí příkazu `Add-AzAccount` a postupujte podle pokynů na obrazovce.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Načíst seznam umístění

Pokud nevíte, jaké umístění máte použít, můžete vypsat všechna dostupná umístění. Po zobrazení seznamu vyhledejte umístění, které chcete použít. V těchto příkladech se používá **eastus**. Uloží tuto hodnotu do **umístění** proměnné pro budoucí použití.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . 

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Uložte název skupiny prostředků do proměnné pro budoucí použití. V tomto příkladu se vytvoří skupina prostředků s názvem *pshtablesrg* v oblasti *eastus* .

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

Vytvořte standardní účet úložiště pro obecné účely s místně redundantním úložištěm (LRS) pomocí [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Nezapomeňte zadat jedinečný název účtu úložiště. Potom Získejte kontext reprezentující účet úložiště. Když pracujete na účtu úložiště, můžete místo opakovaného poskytování přihlašovacích údajů odkazovat na kontext.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Vytvoření nové tabulky

Chcete-li vytvořit tabulku, použijte rutinu [New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) . V tomto příkladu je volána tabulka `pshtesttable` .

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Načte seznam tabulek v účtu úložiště.

Načte seznam tabulek v účtu úložiště pomocí [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Načtení odkazu na určitou tabulku

K provádění operací v tabulce potřebujete odkaz na konkrétní tabulku. Získejte odkaz pomocí [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Vlastnost referenčního cloudu pro konkrétní tabulku

> [!IMPORTANT]
> Při práci s modulem PowerShellu pro **AzTable** je použití cloudové části povinné. Chcete-li získat odkaz na tento objekt, zavolejte příkaz **Get-AzStorageTable** . Tento příkaz také vytvoří tabulku, pokud ještě neexistuje.

K provádění operací s tabulkou pomocí **AzTable** potřebujete odkaz na vlastnost cloudu konkrétní tabulky.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Odstranění tabulky

Chcete-li odstranit tabulku, použijte [příkaz Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Tato rutina odebere tabulku včetně všech svých dat.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste na začátku tohoto postupu vytvořili novou skupinu prostředků a účet úložiště, můžete odebrat všechny prostředky, které jste v tomto cvičení vytvořili, odebráním skupiny prostředků. Tento příkaz odstraní všechny prostředky obsažené v rámci skupiny a také vlastní skupinu prostředků.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto článku s postupem jste se dozvěděli o běžných operacích služby Azure Table Storage pomocí prostředí PowerShell, včetně postupu: 

> [!div class="checklist"]
> * Vytvoření tabulky
> * Načtení tabulky
> * Přidat entity tabulky
> * Dotazování tabulky
> * Odstranit entity tabulky
> * Odstranění tabulky

Další informace najdete v následujících článcích.

* [Rutiny PowerShellu pro úložiště](/powershell/module/az.storage#storage)

* [Práce s tabulkami Azure z PowerShellu – AzureRmStorageTable/AzTable PS modul v 2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.