---
title: Provádění operací úložiště azure table pomocí PowerShellu | Dokumenty společnosti Microsoft
description: Zjistěte, jak spouštět běžné úlohy, jako je vytváření, dotazování, odstranění dat z účtu úložiště tabulky Azure pomocí PowerShellu.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.openlocfilehash: f1846fae4cbf473df688a2b184c307d72ab2f8d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721468"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Provádění operací úložiště tabulek Azure s Azure PowerShellem 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table storage je úložiště dat NoSQL, které můžete použít k ukládání a dotazování na obrovské sady strukturovaných, nerelačních dat. Hlavními součástmi služby jsou tabulky, entity a vlastnosti. Tabulka je kolekce entit. Entita je sada vlastností. Každá entita může mít až 252 vlastností, což jsou všechny dvojice název-hodnota. Tento článek předpokládá, že jste již obeznámeni s koncepty služby Azure Table Storage Service. Podrobné informace najdete [v tématu Principy datového modelu služby Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) a [Začínáme s úložištěm Azure Table pomocí rozhraní .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md).

Tento článek s postupem popisuje běžné operace úložiště Azure Table. Získáte informace o těchto tématech: 

> [!div class="checklist"]
> * Vytvoření tabulky
> * Načtení tabulky
> * Přidání entit tabulky
> * Dotaz na tabulku
> * Odstranění entit tabulky
> * Odstranění tabulky

Tento článek s návody ukazuje, jak vytvořit nový účet Azure Storage v nové skupině prostředků, abyste ho po dokončení mohli snadno odebrat. Pokud byste raději používali existující účet úložiště, můžete to udělat místo toho.

Příklady vyžadují moduly `Az.Storage (1.1.0 or greater)` Az `Az.Resources (1.2.0 or greater)`PowerShell a . V okně Prostředí PowerShell spusťte `Get-Module -ListAvailable Az*` a vyhledejte verzi. Pokud se nic nezobrazuje nebo potřebujete upgradovat, přečtěte si informace [o instalaci modulu Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Pomocí této funkce Azure z PowerShellu vyžaduje, abyste `Az` měli nainstalovaný modul. Aktuální verze `AzTable` aplikace není kompatibilní se starším modulem AzureRM.
> V případě potřeby [dodržujte nejnovější pokyny k instalaci modulu Az.](/powershell/azure/install-az-ps)

Po instalaci nebo aktualizaci prostředí Azure PowerShell je nutné nainstalovat modul **AzTable**, který obsahuje příkazy pro správu entit. Chcete-li nainstalovat tento modul, spusťte prostředí PowerShell jako správce a použijte příkaz **Instalovat modul.**

> [!IMPORTANT]
> Z důvodů kompatibility názvů modulů stále publikujeme stejný modul pod starým názvem `AzureRmStorageTables` v Galerii prostředí PowerShell. Tento dokument bude odkazovat pouze na nový název.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se ke svému předplatnému Azure pomocí příkazu `Add-AzAccount` a postupujte podle pokynů na obrazovce.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Načíst seznam umístění

Pokud nevíte, jaké umístění máte použít, můžete vypsat všechna dostupná umístění. Po zobrazení seznamu vyhledejte umístění, které chcete použít. Tyto příklady používají **eastus**. Uložte tuto hodnotu do **umístění** proměnné pro budoucí použití.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) 

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Uložte název skupiny prostředků do proměnné pro budoucí použití. V tomto příkladu je vytvořena skupina prostředků s názvem *pshtablesrg* v oblasti *eastus.*

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Vytvoření účtu úložiště

Vytvořte standardní účet úložiště pro obecné účely s místně redundantním úložištěm (LRS) pomocí [účtu New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Nezapomeňte zadat jedinečný název účtu úložiště. Dále získat kontext, který představuje účet úložiště. Při jednání s účtem úložiště můžete odkazovat na kontext namísto opakovaného poskytování přihlašovacích údajů.

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

Chcete-li vytvořit tabulku, použijte rutinu [New-AzStorageTable.](/powershell/module/az.storage/New-AzStorageTable) V tomto příkladu se `pshtesttable`tabulka nazývá .

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Načtení seznamu tabulek v účtu úložiště

Načíst seznam tabulek v účtu úložiště pomocí [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Načtení odkazu na konkrétní tabulku

Chcete-li provádět operace v tabulce, potřebujete odkaz na konkrétní tabulku. Získat odkaz pomocí [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Vlastnost Reference CloudTable určité tabulky

> [!IMPORTANT]
> Použití CloudTable je povinné při práci s **modulem AzTable** PowerShell. Volání **get-AzTableTable** příkaz získat odkaz na tento objekt. Tento příkaz také vytvoří tabulku, pokud ještě neexistuje.

Chcete-li provádět operace v tabulce pomocí **AzTable**, potřebujete odkaz na vlastnost CloudTable určité tabulky.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Odstranění tabulky

Chcete-li tabulku odstranit, použijte [použít příkaz Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Tato rutina odebere tabulku, včetně všech jejích dat.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste na začátku tohoto postupu vytvořili novou skupinu prostředků a účet úložiště, můžete odebrat všechny prostředky, které jste v tomto cvičení vytvořili odebráním skupiny prostředků. Tento příkaz odstraní všechny prostředky obsažené ve skupině, stejně jako samotné skupiny prostředků.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto článku s návody jste se dozvěděli o běžných operacích úložiště Azure Table s Prostředím PowerShell, včetně toho, jak: 

> [!div class="checklist"]
> * Vytvoření tabulky
> * Načtení tabulky
> * Přidání entit tabulky
> * Dotaz na tabulku
> * Odstranění entit tabulky
> * Odstranění tabulky

Další informace naleznete v následujících článcích

* [Rutiny PowerShellu pro úložiště](/powershell/module/az.storage#storage)

* [Práce s tabulkami Azure z PowerShellu – AzureRmStorageTable/AzTable PS Module v2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
