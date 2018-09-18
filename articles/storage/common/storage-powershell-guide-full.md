---
title: Použití Azure Powershellu s Azure Storage | Dokumentace Microsoftu
description: Další informace o použití rutin prostředí Azure PowerShell pro službu Azure Storage.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 06/13/2018
ms.author: rogarana
ms.component: common
ms.openlocfilehash: c20ffcca17f8b0521bac3b6131e963bbecbdc6e1
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736950"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Použití Azure Powershell s Azure Storage

Prostředí Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku Powershellu nebo ve skriptech. Pro službu Azure Storage tyto rutiny spadají do dvou kategorií – rovina řízení a rovinou dat. Rutiny roviny řízení slouží ke správě účtu úložiště – k vytváření účtů úložiště, nastavit vlastnosti, odstraňování účtů úložiště, otočit přístupové klíče a tak dále. Rutiny roviny dat se používají ke správě dat uložených *v* účtu úložiště. Například nahrání objektů BLOB, vytváření sdílených složek a přidání zprávy do fronty.

Tento článek popisuje běžné operace pomocí rutiny roviny správy ke správě účtů úložiště. Získáte informace o těchto tématech: 

> [!div class="checklist"]
> * Seznam účtů úložiště
> * Získání odkazu na existující účet úložiště
> * vytvořit účet úložiště 
> * Nastavit vlastnosti účtu úložiště
> * Načtení a opětovné vygenerování přístupových klíčů
> * Ochrana přístupu k účtu úložiště 
> * Povolení analýzy úložiště

Tento článek obsahuje odkazy na několik dalších článků Powershellu pro úložiště, jako je například jak povolit a přístup k Storage Analytics, jak používat rutiny roviny dat a jak získat přístup k Azure nezávislé cloudů, například China Cloud, německého cloudu a státní správu Cloud.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

V tomto cvičení vyžaduje modul Azure PowerShell verze 4.4 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Pro toto cvičení, můžete zadat příkazy do regulární okno prostředí PowerShell, nebo můžete použít [Windows Powershellu integrovaném skriptovacím prostředí (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) a zadáním příkazů do editoru a pak test jeden nebo více příkazů v době jako Projděte si příklady. Můžete zvýraznit řádky, které chcete spustit a klikněte na tlačítko spustit vybrané jenom spuštění těchto příkazů.

Další informace o účtech úložiště najdete v tématu [seznámení se službou Storage](storage-introduction.md) a [účty Azure storage](storage-create-storage-account.md).

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Seznam účtů úložiště v rámci předplatného

Spustit [Get-AzureRMStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount) rutina pro načtení seznamu účtů úložiště v rámci aktuálního předplatného. 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Získejte odkaz na účet úložiště

Dále je třeba odkaz na účet úložiště. Můžete vytvořit nový účet úložiště nebo získání odkazu na existující účet úložiště. Následující části zobrazuje obě metody. 

### <a name="use-an-existing-storage-account"></a>Použít existující účet úložiště 

Pokud chcete načíst existující účet úložiště, budete potřebovat název skupiny prostředků a název účtu úložiště. Nastavte proměnné pro tyto dvě pole a pak použít [Get-AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount) rutiny. 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

Nyní máte $storageAccount, která odkazuje na existující účet úložiště.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště 

Tento skript ukazuje, jak vytvořit účet úložiště pro obecné účely pomocí [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Po vytvoření účtu načíst jeho kontext, který lze použít v následné příkazy místo určení ověřování se každé volání.

```powershell
# Get list of locations and select one.
Get-AzureRmLocation | select Location 
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzureRmResourceGroup -Name $resourceGroup -Location $location 

# Set the name of the storage account and the SKU name. 
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"
    
# Create the storage account.
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context. 
$ctx = $storageAccount.Context
```

Skript používá následující rutiny prostředí PowerShell: 

*   [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) – načte seznam platných umístění. V příkladu `eastus` pro umístění.

*   [Nový-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) – vytvoří novou skupinu prostředků. Skupina prostředků je logický kontejner, do které se nasazují a spravují prostředky Azure. Náš nazývá `teststoragerg`. 

*   [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) – vytvoří účet úložiště. V příkladu `testpshstorage`.

Název skladové položky určuje typ replikace účtu úložiště, jako je například LRS (místně redundantní úložiště). Další informace o replikaci najdete v tématu [replikace Azure Storage](storage-redundancy.md).

> [!IMPORTANT]
> Název účtu úložiště musí být v rámci Azure jedinečný a musí obsahovat malá písmena. Zásady vytváření názvů a omezení, najdete v části [pojmenování a odkazování na kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
> 

Nyní máte nový účet úložiště a na ni odkaz. 

## <a name="manage-the-storage-account"></a>Správa účtu úložiště

Teď, když máte odkaz na nový účet úložiště nebo existující účet úložiště, následující část popisuje některé příkazy, které můžete použít ke správě vašeho účtu úložiště.

### <a name="storage-account-properties"></a>Vlastnosti účtu úložiště

Chcete-li změnit nastavení pro účet úložiště, použijte [Set-AzureRmStorageAccount](/powershell/module/azurerm.storage/set-azurermstorageaccount). Při umístění účtu úložiště nebo skupinu prostředků, ve kterém se nachází nelze změnit, můžete změnit mnoho dalších vlastností. Následuje seznam některé vlastnosti, které můžete změnit pomocí prostředí PowerShell.

* **Vlastní doménu** přiřazená k účtu úložiště.

* **Značky** přiřazená k účtu úložiště. Značky se často používají k kategorizovat prostředky pro účely fakturace.

* **SKU** je nastavení replikace pro účet úložiště, jako je například LRS pro místně redundantní úložiště. Například můžete změnit úroveň ze Standard\_LRS standardu\_GRS nebo Standard\_RAGRS. Všimněte si, že nelze změnit Standard\_ZRS nebo Premium\_LRS pro ostatní SKU, nebo změňte na ty ostatní SKU.

* **Úroveň přístupu** pro účty Blob storage. Hodnota pro úroveň přístupu nastavená na **horké** nebo **cool**, a umožňuje minimalizovat náklady tak, že vyberete úroveň přístupu, který odpovídá použití účtu úložiště. Další informace najdete v tématu [horké, studené a archivní úroveň úložiště](../blobs/storage-blob-storage-tiers.md).

* Povolte pouze provoz protokolu HTTPS. 

### <a name="manage-the-access-keys"></a>Správa přístupových klíčů

Účet služby Azure Storage se dodává s dva klíče účtu. Pokud chcete načíst klíče, použijte [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey). Tento příklad načte první klíč. Chcete-li načíst druhé, použijte `Value[1]` místo `Value[0]`.

```powershell
$storageAccountKey = `
    (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Chcete-li znovu vygenerovat klíč, použijte [New-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/New-AzureRmStorageAccountKey). 

```powershell
New-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1 
```

Chcete-li obnovit jiné klíč, použijte `key2` jako název klíče místo `key1`.

Znovu generovat jeden ze svých klíčů a potom ho znovu a podívejte se na novou hodnotu načíst.

> [!NOTE] 
> Měli byste provést pečlivé plánování před obnovuje se klíč pro účet úložiště produkčního prostředí. Znova se generuje jeden nebo oba klíče skončí platnost přístupu pro každou aplikaci pomocí klíče, který se znovu vygeneroval. Další informace najdete v tématu [přístupové klíče](storage-account-manage.md#access-keys).


### <a name="delete-a-storage-account"></a>Odstranění účtu úložiště 

Chcete-li odstranit účet úložiště, použijte [Remove-AzureRmStorageAccount](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount).

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Když odstraníte účet úložiště, všechny prostředky uložené v účtu budou také odstraněny. Pokud omylem odstraníte účet, obraťte se na podporu okamžitě a vytvořit lístek obnovit účet úložiště. Není zaručeno, že obnovení vašich dat, ale někdy funguje. Nevytvářejte nový účet úložiště se stejným názvem jako měl ten starý, dokud nebude vyřešen lístek podpory. 
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Chránit svůj účet úložiště pomocí virtuálních sítí a bran firewall

Všechny účty úložiště jsou standardně přístupné pro všechny sítě, který má přístup k Internetu. Ale můžete nakonfigurovat pravidla síti povolit jenom aplikace z konkrétní virtuálních sítí pro přístup k účtu úložiště. Další informace najdete v tématu [virtuálních sítí a bran firewall nakonfigurovat Storage Azure](storage-network-security.md). 

Tento článek ukazuje, jak spravovat tato nastavení pomocí následujících rutin Powershellu:
* [Add-AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remove-AzureRmStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/azurerm.storage/remove-azurermstorageaccountnetworkrule?view=azurermps-6.8.1)

## <a name="use-storage-analytics"></a>Použití storage analytics  

[Azure Storage Analytics](storage-analytics.md) se skládá z [metrikách Storage Analytics](/rest/api/storageservices/about-storage-analytics-metrics) a [Storage Analytics protokolování](/rest/api/storageservices/about-storage-analytics-logging). 

**Metrikách Storage Analytics** se používá ke shromažďování metrik pro účty úložiště Azure, které vám umožní monitorovat stav účtu úložiště. Metriky lze povolit pro objekty BLOB, soubory, tabulky a fronty.

**Storage Analytics protokolování** dochází na straně serveru a umožňuje zaznamenávat podrobnosti o úspěšných i neúspěšných požadavků na účet úložiště. V těchto protokolech můžete zobrazit podrobnosti o čtení, zápisu a odstranění operace tabulky, fronty a objekty BLOB, jakož i důvody neúspěšných požadavků. Protokolování není k dispozici pro soubory Azure.

Můžete nakonfigurovat monitorování pomocí [webu Azure portal](https://portal.azure.com), PowerShell, nebo programově pomocí klientskou knihovnu pro úložiště. 

> [!NOTE]
> Můžete povolit minutové analytics pomocí Powershellu. Tato funkce není k dispozici na portálu.
>

* Informace o povolení a zobrazení dat metrik úložiště pomocí prostředí PowerShell najdete v tématu [povolení služby Azure Storage metriky a zobrazení dat metrik](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell).

* Zjistěte, jak povolit a načtení dat protokolování úložiště pomocí prostředí PowerShell, najdete v článku [povolení protokolování úložiště pomocí prostředí PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell) a [hledání dat protokolování úložiště protokolů](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data).

* Podrobné informace o použití úložiště metrik a protokolování úložiště k řešení problémů s úložištěm, naleznete v tématu [monitorování, diagnostikování a řešení potíží s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Správa dat v účtu úložiště

Teď, když chápete, jak spravovat svůj účet úložiště pomocí prostředí PowerShell, můžete použít v následujících článcích se dozvíte, jak získat přístup k datové objekty v účtu úložiště.

* [Správa objektů BLOB pomocí Powershellu](../blobs/storage-how-to-use-blobs-powershell.md)
* [Jak spravovat soubory pomocí Powershellu](../files/storage-how-to-use-files-powershell.md)
* [Správa fronty pomocí Powershellu](../queues/storage-powershell-how-to-use-queues.md)
* [Provádění operací Azure Table storage pomocí Powershellu](../../storage/tables/table-storage-how-to-use-powershell.md)

Table API služby Azure Cosmos DB nabízí prémiové funkce pro table storage, jako je například globální distribuce na klíč, zajištění nízké latence čtení a zápisy, automatického sekundárního indexování a vyhrazenou propustností. 

* Další informace najdete v tématu [Azure Cosmos DB Table API](../../cosmos-db/table-introduction.md). 

## <a name="independent-cloud-deployments-of-azure"></a>Nasazení nezávislé cloudů Azure

Většina lidí pomocí veřejného cloudu Azure pro globální nasazení v Azure. Existují také některá nezávislé nasazení Microsoft Azure z důvodů suverenity a tak dále. Tato nezávislé nasazení jsou označovány jako "prostředí". Toto jsou k dispozici prostředí:

* [Cloud Azure Government](https://azure.microsoft.com/features/gov/)
* [Cloud Azure China, provozovaný společností 21Vianet v Číně](http://www.windowsazure.cn/)
* [Německého cloudu Azure](../../germany/germany-welcome.md)

Informace o tom, jak přistupovat k tyto cloudy a jejich úložiště pomocí Powershellu najdete v tématu [Správa úložiště v Azure pomocí Powershellu nezávislé cloudech](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili novou skupinu prostředků a účet úložiště pro toto cvičení, můžete odebrat yous všechny prostředky, které jste vytvořili odstraněním skupiny prostředků. Tím se odstraní také všechny prostředky, které skupina obsahuje. V takovém případě odebere účet úložiště, který jste vytvořili a samotnou skupinu prostředků.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Další postup

Tento článek popisuje běžné operace pomocí rutiny roviny správy ke správě účtů úložiště. Naučili jste se tyto postupy: 

> [!div class="checklist"]
> * Seznam účtů úložiště
> * Získání odkazu na existující účet úložiště
> * vytvořit účet úložiště 
> * Nastavit vlastnosti účtu úložiště
> * Načtení a opětovné vygenerování přístupových klíčů
> * Ochrana přístupu k účtu úložiště 
> * Povolení analýzy úložiště

Tento článek také poskytuje odkazy na několik dalších článků, jako je například Správa datových objektů, jak povolit analytika úložiště a jak získat přístup k Azure nezávislé cloudů, například China Cloud, německého cloudu a cloudu pro státní správu. Tady jsou některé další související články a zdroje pro referenci:

* [Rutiny Powershellu rovina řízení pro Azure Storage](/powershell/module/AzureRM.Storage/)
* [Rutiny Powershellu roviny dat pro Azure Storage](/powershell/module/azure.storage/)
* [Referenční informace k Windows Powershellu](https://msdn.microsoft.com/library/ms714469.aspx)
