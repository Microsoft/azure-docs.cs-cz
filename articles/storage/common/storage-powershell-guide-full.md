---
title: Použití Azure PowerShell s Azure Storage | Microsoft Docs
description: Další informace o použití rutin prostředí Azure PowerShell pro Azure Storage.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.assetid: f4704f58-abc6-4f89-8b6d-1b1659746f5a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: rogarana
ms.openlocfilehash: 951b69877718c5da3c165c24c297906a1ad9a976
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652497"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Použití Azure Powershell s Azure Storage

Prostředí Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku prostředí PowerShell nebo ve skriptech. Pro Azure Storage tyto rutiny spadají do dvou kategorií – řídicí a datové roviny. Rutiny rovině řízení se používají ke správě účtu úložiště – k vytvoření účtů úložiště, nastavte vlastnosti, odstranit účty úložiště, otočit přístupové klíče a tak dále. Rutiny roviny data se používají ke správě dat uložených *v* účet úložiště. Například odeslání objekty BLOB, vytváření sdílených složek a přidání zprávy do fronty.

Tento článek popisuje běžné operace pomocí rutiny roviny správy ke správě účtů úložiště. Získáte informace o těchto tématech: 

> [!div class="checklist"]
> * Seznam účtů úložiště
> * Získat odkaz na existující účet úložiště
> * vytvořit účet úložiště 
> * Nastavit vlastnosti účtu úložiště
> * Načtení a opět vytvořit přístupové klíče
> * Chránit přístup k účtu úložiště 
> * Povolit analytika úložiště

Tento článek obsahuje odkazy na několik dalších článků prostředí PowerShell pro úložiště, například povolení a přístup k Storage Analytics, jak používat rutiny roviny data a jak získat přístup k Azure nezávislé cloudy, například cloudové Čína, němčina cloudu a Government Cloud.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Tento postup vyžaduje prostředí Azure PowerShell verze modulu 4.4 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). 

Pro toto cvičení, můžete zadat příkazy do regulární okno Powershellu, nebo můžete použít [Windows PowerShell Integrované skriptovací prostředí (ISE)](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) a zadejte příkazy do editoru a potom otestovat jeden nebo více příkazů v čase jako projít příklady. Zvýraznit řádky, které chcete spustit a klikněte na tlačítko spustit vybrané těsně spuštění těchto příkazů.

Další informace o účtech úložiště najdete v tématu [Úvod do Storage](storage-introduction.md) a [účty Azure storage](storage-create-storage-account.md).

## <a name="log-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k předplatnému Azure pomocí příkazu `Connect-AzureRmAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzureRmAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Seznam účtů úložiště v předplatném

Spustit [Get AzureRMStorageAccount](/powershell/module/azurerm.resources/get-azurermstorageaccount) rutiny načíst seznam účtů úložiště v aktuálním předplatném. 

```powershell
Get-AzureRMStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Získat odkaz na účet úložiště

Dále je nutné zadat odkaz na účet úložiště. Můžete buď vytvořit nový účet úložiště nebo získat odkaz na existující účet úložiště. Následující část popisuje obě metody. 

### <a name="use-an-existing-storage-account"></a>Použít existující účet úložiště 

Pokud chcete načíst existující účet úložiště, musíte název skupiny prostředků a název účtu úložiště. Nastavení proměnných pro tyto dvě pole, pak používat [Get AzureRmStorageAccount](/powershell/module/azurerm.storage/Get-AzureRmStorageAccount) rutiny. 

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
```

Nyní máte $storageAccount, který ukazuje na stávající účet úložiště.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště 

Následující skript je ukázkou, jak vytvořit účet úložiště pro obecné účely pomocí [AzureRmStorageAccount nový](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Po vytvoření účtu načíst jeho kontext, který může být použit v následné příkazy místo zadání ověřování s každou volání.

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

*   [Get-AzureRmLocation](/powershell/module/azurerm.storage/Get-AzureRmLocation) – načte seznam platných umístění. Tento příklad používá `eastus` pro umístění.

*   [Nový-AzureRmResourceGroup](/powershell/module/azurerm.resources/New-AzureRmResourceGroup) – vytvoří novou skupinu prostředků. Skupina prostředků je logický kontejner, do kterého jsou nasadit a spravovat vašich prostředků Azure. Náš nazývá `teststoragerg`. 

*   [Nové AzureRmStorageAccount](/powershell/module/azurerm.resources/New-AzureRmStorageAcccount) – vytvoří účet skutečné úložiště. V příkladu je `testpshstorage`.

Název SKU označuje typ replikace pro účet úložiště, jako je například LRS (Locally Redundant Storage). Další informace o replikaci, najdete v části [replikace Azure Storage](storage-redundancy.md).

> [!IMPORTANT]
> Název účtu úložiště musí být jedinečný v rámci Azure a musí být psaný malými písmeny. Zásady vytváření názvů a omezení najdete v tématu [pojmenování a odkazování na kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata).
> 

Nyní máte nový účet úložiště a na něj odkaz. 

## <a name="manage-the-storage-account"></a>Správa účtu úložiště

Teď, když máte odkaz na nový účet úložiště nebo existující účet úložiště, následující část popisuje některé příkazy, které můžete použít ke správě vašeho účtu úložiště.

### <a name="storage-account-properties"></a>Vlastnosti účtu úložiště

Chcete-li změnit nastavení pro účet úložiště, použijte [Set AzureRmStorageAccount](/powershell/module/azurerm.resources/Set-AzureRmStorageAccount). Při umístění účtu úložiště nebo skupině prostředků, ve kterém se nachází nelze změnit, můžete změnit mnoho dalších vlastností. Následuje seznam některých vlastností, které můžete změnit pomocí prostředí PowerShell.

* **Vlastní domény** přiřazená k účtu úložiště.

* **Značky** přiřazená k účtu úložiště. Značky se často používají k kategorizovat prostředky pro účely fakturace.

* **SKU** je nastavení replikace pro účet úložiště, jako je například LRS pro místně redundantní úložiště. Například můžete změnit z Standard\_LRS standardního\_GRS nebo Standard\_RAGRS. Všimněte si, že nemůžete změnit Standard\_ZRS nebo Premium\_LRS do jiných SKU, nebo změňte tyto další jednotky SKU.

* **Úroveň přístupu** pro účty úložiště Blob. Hodnota pro úroveň přístupu je nastavena na **aktivní** nebo **nástrojů**, a umožňuje vaše náklady minimalizovat tak, že vyberete úroveň přístupu, která zarovnaná s jak používat účet úložiště. Další informace najdete v tématu [za provozu, ochladí a archivaci vrstvy úložiště](../blobs/storage-blob-storage-tiers.md).

* Povolte jenom přenos HTTPS. 

### <a name="manage-the-access-keys"></a>Správa přístupových klíčů

Účet úložiště Azure obsahuje dva klíče účtu. K načtení klíčů, použijte [Get-AzureRmStorageAccountKey](/powershell/module/AzureRM.Storage/Get-AzureRmStorageAccountKey). Tento příklad načte první klíč. Chcete-li načíst jiného, použijte `Value[1]` místo `Value[0]`.

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

Obnovit jednu z klíče a potom ho znovu zobrazíte novou hodnotu načíst.

> [!NOTE] 
> Měli byste provést pečlivé plánování před znovu vygenerovat klíč pro účet úložiště produkční. Opakované generování klíčů k jedné nebo obou zruší platnost přístupu pro každou aplikaci pomocí klíče, který se znovu vygeneroval. Další informace najdete v tématu [opětovné vygenerování přístupových klíčů úložiště](storage-create-storage-account.md#regenerate-storage-access-keys).


### <a name="delete-a-storage-account"></a>Odstranění účtu úložiště 

Pokud chcete odstranit účet úložiště, použijte [AzureRmStorageAccount odebrat](/powershell/module/azurerm.storage/Remove-AzureRmStorageAccount).

```powershell
Remove-AzureRmStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Pokud odstraníte účet úložiště, všechny prostředky uložené v účtu budou také odstraněny. Pokud omylem odstraníte účet, obraťte se na podporu okamžitě a otevřít lístek k obnovení účtu úložiště. Obnovení dat není zaručeno, ale někdy funguje. Nevytvářejte nový účet úložiště se stejným názvem jako ten starý, dokud nebude vyřešen lístku podpory. 
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Chránit váš účet úložiště pomocí virtuální sítě a brány firewall

Ve výchozím nastavení všechny účty úložiště jsou dostupné všechny sítě, který má přístup k Internetu. Ale můžete nakonfigurovat pravidla pro sítě a Povolit jenom aplikace z konkrétní virtuální sítě pro přístup k účtu úložiště. Další informace najdete v tématu [brány firewall nakonfigurovat úložiště Azure a virtuální sítě](storage-network-security.md). 

Článek ukazuje, jak spravovat tyto nastavení pomocí následující rutiny prostředí PowerShell:
* [Add-AzureRmStorageAccountNetworkRule](/powershell/module/AzureRM.Storage/Add-AzureRmStorageAccountNetworkRule)
* [Update-AzureRmStorageAccountNetworkRuleSet](/powershell/module/azurerm.storage/update-azurermstorageaccountnetworkruleset)
* [Remove-AzureRmStorageAccountNetworkRule](/powershell/module/azurerm.storage/remove-azurermstorage-account-networkrule)

## <a name="use-storage-analytics"></a>Použít analytika úložiště  

[Azure Storage Analytics](storage-analytics.md) se skládá z [Storage Analytics Metrics](/rest/api/storageservices/about-storage-analytics-metrics) a [Storage Analytics protokolování](/rest/api/storageservices/about-storage-analytics-logging). 

**Úložiště Analytics metriky** se používá ke shromažďování metrik pro vaše účty úložiště Azure, které můžete použít k monitorování stavu účtu úložiště. Metriky lze povolit pro objekty BLOB, soubory, tabulky a fronty.

**Protokolování Analytics úložiště** se stane, serverový a umožní vám si zaznamenejte podrobnosti pro úspěšné i neúspěšné požadavky na účtu úložiště. Tyto protokoly umožňují najdete v části Podrobnosti o čtení, zápisu a odstraňování operace u tabulky, fronty a objekty BLOB, jakož i důvody pro chybné žádosti. Protokolování není k dispozici pro soubory Azure.

Můžete nakonfigurovat monitorování pomocí [portál Azure](https://portal.azure.com), prostředí PowerShell nebo programově pomocí klientské knihovny pro úložiště. 

> [!NOTE]
> Můžete povolit minutu analytics pomocí prostředí PowerShell. Tato funkce není k dispozici na portálu.
>

* Informace o povolení a zobrazovat data metriky úložiště pomocí prostředí PowerShell najdete v tématu [povolení Azure Storage metriky a zobrazení údajů metrik](storage-enable-and-view-metrics.md#how-to-enable-metrics-using-powershell).

* Informace o povolení a načtení dat protokolování úložiště pomocí prostředí PowerShell najdete v tématu [povolení protokolování úložiště pomocí prostředí PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#how-to-enable-storage-logging-using-powershell) a [hledání data protokolu protokolování úložiště](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data#finding-your-storage-logging-log-data).

* Podrobné informace o použití úložiště metrik a protokolování úložiště k řešení problémů s úložištěm najdete v tématu [monitorování, diagnostikování a řešení potíží s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Správa dat v účtu úložiště

Teď, když chápete, jak spravovat váš účet úložiště pomocí prostředí PowerShell, můžete pomocí následujících článků se dozvíte, jak získat přístup k objektům dat v účtu úložiště.

* [Jak spravovat objekty BLOB pomocí prostředí PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Správa souborů pomocí prostředí PowerShell](../files/storage-how-to-use-files-powershell.md)
* [Jak spravovat fronty pomocí prostředí PowerShell](../queues/storage-powershell-how-to-use-queues.md)
* [Provedení operace úložiště Azure Table pomocí prostředí PowerShell](../../storage/tables/table-storage-how-to-use-powershell.md)

Rozhraní API tabulky DB Cosmos Azure poskytuje prémiových funkcí pro úložiště table, jako jsou to globální distribuční, nízká latence čtení a zápisy, automatické sekundární indexování a vyhrazenou propustností. 

* Další informace najdete v tématu [rozhraní API služby Azure Cosmos DB tabulky](../../cosmos-db/table-introduction.md). 
* Informace o tom, jak pomocí prostředí PowerShell k provádění operací rozhraní API služby Azure Cosmos DB tabulky, najdete v části [provést Azure Cosmos DB tabulky operace rozhraní API pomocí prostředí PowerShell](../../cosmos-db/table-powershell.md).

## <a name="independent-cloud-deployments-of-azure"></a>Nezávislé cloudové nasazení Azure

Většina lidí používá veřejném cloudu Azure pro jejich globální nasazení Azure. Existují také některé nezávislé nasazení Microsoft Azure z důvodů suverenity a tak dále. Tato nezávislé nasazení jsou označovány jako "prostředí." Toto jsou k dispozici prostředí:

* [Cloud vlády Azure](https://azure.microsoft.com/features/gov/)
* [Čína cloudu Azure provozované v Číně společností 21Vianet](http://www.windowsazure.cn/)
* [Němčině cloudu Azure](../../germany/germany-welcome.md)

Informace o tom, jak získat přístup k tyto cloudy a jejich úložiště pomocí prostředí PowerShell najdete v tématu [správu úložiště v Azure nezávislé cloudy pomocí prostředí PowerShell](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste vytvořili novou skupinu prostředků a účet úložiště pro toto cvičení, můžete odebrat yous všech prostředků, které jste vytvořili odebráním skupině prostředků. Tím se odstraní také všechny prostředky, které skupina obsahuje. V takovém případě odebere účtu úložiště vytvořeném a skupině prostředků, sám sebe.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Další postup

Tento článek popisuje běžné operace pomocí rutiny roviny správy ke správě účtů úložiště. Naučili jste se tyto postupy: 

> [!div class="checklist"]
> * Seznam účtů úložiště
> * Získat odkaz na existující účet úložiště
> * vytvořit účet úložiště 
> * Nastavit vlastnosti účtu úložiště
> * Načtení a opět vytvořit přístupové klíče
> * Chránit přístup k účtu úložiště 
> * Povolit analytika úložiště

Tento článek k dispozici také odkazy na několik dalších článků, například jak spravovat datové objekty, jak povolit analytika úložiště a informace o přístupu k Azure nezávislé cloudy, například cloudové Čína, němčina cloudu a Cloud vlády. Tady jsou některé další související články a prostředky pro referenci:

* [Rutiny řízení prostředí PowerShell roviny Azure úložiště](/powershell/module/AzureRM.Storage/)
* [Azure rutiny prostředí PowerShell roviny úložiště dat](/powershell/module/azure.storage/)
* [Referenční informace prostředí PowerShell systému Windows](https://msdn.microsoft.com/library/ms714469.aspx)
