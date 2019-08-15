---
title: Použití Azure PowerShell s Azure Storage | Microsoft Docs
description: Naučte se používat rutiny Azure PowerShell pro Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ac210a03f8b1a0a5f7fff07cbc68b4cd6bc98632
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69016344"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Použití Azure Powershell s Azure Storage

Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. V případě Azure Storage tyto rutiny spadají do dvou kategorií – rovina ovládacího prvku a rovina dat. Rutiny řídicí roviny se používají ke správě účtu úložiště – k vytváření účtů úložiště, nastavení vlastností, odstraňování účtů úložiště, otočení přístupových klíčů atd. Rutiny roviny dat se používají ke správě dat uložených *v* účtu úložiště. Například nahrávání objektů blob, vytváření sdílených složek a přidávání zpráv do fronty.

Tento článek s návody popisuje běžné operace pomocí rutin roviny správy ke správě účtů úložiště. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Výpis účtů úložiště
> * Získat odkaz na existující účet úložiště
> * vytvořit účet úložiště
> * Nastavení vlastností účtu úložiště
> * Načtení a opětovné vygenerování přístupových klíčů
> * Ochrana přístupu k účtu úložiště
> * Povolit Analýza úložiště

Tento článek obsahuje odkazy na několik dalších článků prostředí PowerShell pro úložiště, jako je například povolení a přístup k Analýza úložiště, použití rutin pro rovinu dat a přístup k nezávislým cloudům Azure, jako jsou například čínské cloudy, německé cloudy a státní správa. Cloudu.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Toto cvičení vyžaduje Azure PowerShell modul AZ verze 0,7 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

Pro toto cvičení můžete zadat příkazy do běžného okna prostředí PowerShell, nebo můžete použít [Integrované skriptovací prostředí (ISE) prostředí Windows PowerShell](/powershell/scripting/getting-started/fundamental/windows-powershell-integrated-scripting-environment--ise-) a zadat příkazy do editoru, otestovat jeden nebo více příkazů najednou při přechodu na 4.6. Můžete zvýraznit řádky, které chcete spustit, a klikněte na spustit vybrané a stačí spustit tyto příkazy.

Další informace o účtech úložiště najdete v tématu [Úvod do úložiště](storage-introduction.md) a [o účtech Azure Storage](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Výpis účtů úložiště v předplatném

Spuštěním rutiny [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) načtěte seznam účtů úložiště v aktuálním předplatném.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Získat odkaz na účet úložiště

Dál potřebujete odkaz na účet úložiště. Můžete buď vytvořit nový účet úložiště, nebo získat odkaz na existující účet úložiště. V následující části jsou uvedeny obě metody.

### <a name="use-an-existing-storage-account"></a>Použít existující účet úložiště

Pokud chcete načíst existující účet úložiště, potřebujete název skupiny prostředků a název účtu úložiště. Nastavte proměnné pro tato dvě pole a pak použijte rutinu [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) .

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Nyní máte $storageAccount, který odkazuje na existující účet úložiště.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Následující skript ukazuje, jak vytvořit účet úložiště pro obecné účely pomocí [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Po vytvoření účtu načtěte jeho kontext, který se dá použít v dalších příkazech namísto zadání ověřování u každého volání.

```powershell
# Get list of locations and select one.
Get-AzLocation | select Location
$location = "eastus"

# Create a new resource group.
$resourceGroup = "teststoragerg"
New-AzResourceGroup -Name $resourceGroup -Location $location

# Set the name of the storage account and the SKU name.
$storageAccountName = "testpshstorage"
$skuName = "Standard_LRS"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName $skuName

# Retrieve the context.
$ctx = $storageAccount.Context
```

Skript používá následující rutiny PowerShellu:

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) --načte seznam platných umístění. Příklad používá `eastus` pro umístění.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) --vytvoří novou skupinu prostředků. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují vaše prostředky Azure. Byl volán `teststoragerg`náš.

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) --vytvoří účet úložiště. V příkladu se `testpshstorage`používá.

Název SKU označuje typ replikace pro účet úložiště, například LRS (místně redundantní úložiště). Další informace o replikaci najdete v tématu [Azure Storage replikace](storage-redundancy.md).

> [!IMPORTANT]
> Název vašeho účtu úložiště musí být v rámci Azure jedinečný a musí být malými písmeny. Zásady vytváření názvů a omezení najdete v tématu pojmenování [kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)a odkazování na ně.
>

Teď máte k dispozici nový účet úložiště a odkaz na něj.

## <a name="manage-the-storage-account"></a>Správa účtu úložiště

Teď, když máte odkaz na nový účet úložiště nebo existující účet úložiště, v následující části jsou uvedené některé příkazy, které můžete použít ke správě svého účtu úložiště.

### <a name="storage-account-properties"></a>Vlastnosti účtu úložiště

Pokud chcete změnit nastavení pro účet úložiště, použijte [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount). I když nemůžete změnit umístění účtu úložiště nebo skupinu prostředků, ve které se nachází, můžete změnit mnoho dalších vlastností. V následující části jsou uvedené některé vlastnosti, které můžete změnit pomocí PowerShellu.

* **Vlastní doména** přiřazená k účtu úložiště.

* **Značky** přiřazené k účtu úložiště Značky se často používají ke kategorizaci prostředků pro účely fakturace.

* **SKU** je nastavení replikace pro účet úložiště, například LRS pro místně redundantní úložiště. Například můžete změnit z úrovně Standard\_LRS na standardní\_GRS nebo standard\_RAGRS. Všimněte si, že nemůžete změnit úrovně\_standard\_ZRS,\_Standard GZRS, standard\_RAGZRS nebo Premium LRS na jiné SKU nebo změnit jiné SKU na tyto.

* **Úroveň přístupu** pro účty BLOB Storage. Hodnota pro úroveň přístupu je nastavená na **horkou** nebo **studenou**a umožňuje vám snížit náklady tím, že vyberete úroveň přístupu, která se zarovnává s tím, jak používáte účet úložiště. Další informace najdete v tématu [horké, studené a archivní úrovně úložiště](../blobs/storage-blob-storage-tiers.md).

* Povoluje jenom přenosy protokolu HTTPS.

### <a name="manage-the-access-keys"></a>Správa přístupových klíčů

Účet Azure Storage obsahuje dva klíče účtu. K načtení klíčů použijte [příkaz Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). Tento příklad načte první klíč. K načtení druhé použijte `Value[1]` `Value[0]`místo.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

K opětovnému vygenerování klíče použijte [New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey).

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Chcete-li znovu vygenerovat druhý klíč `key2` , použijte jako název klíče `key1`místo.

Znovu vygenerujte jeden z vašich klíčů a potom ho znovu načtěte, abyste viděli novou hodnotu.

> [!NOTE]
> Před obnovením klíče pro produkční účet úložiště byste měli provést pečlivé plánování. Opětovné generování jednoho nebo obou klíčů zruší platnost přístupu pro žádnou aplikaci pomocí klíče, který se znovu vygeneroval. Další informace najdete v části [Přístupové klíče](storage-account-manage.md#access-keys).


### <a name="delete-a-storage-account"></a>Odstranění účtu úložiště

Pokud chcete odstranit účet úložiště, použijte [Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Při odstranění účtu úložiště se odstraní také všechny prostředky uložené v účtu. Pokud omylem odstraníte účet, zavolejte podporu hned a otevřete lístek pro obnovení účtu úložiště. Obnovení vašich dat není zaručené, ale někdy funguje. Nevytvářejte nový účet úložiště se stejným názvem jako starý, dokud se nevyřeší lístek podpory.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Ochrana účtu úložiště pomocí virtuální sítě a bran firewall

Ve výchozím nastavení jsou všechny účty úložiště dostupné ze všech sítí, které mají přístup k Internetu. Můžete ale nakonfigurovat Síťová pravidla tak, aby povolovala přístup k účtu úložiště jenom aplikacím z konkrétních virtuálních sítí. Další informace najdete v tématu [konfigurace Azure Storage bran firewall a virtuálních sítí](storage-network-security.md).

Tento článek ukazuje, jak spravovat tato nastavení pomocí následujících rutin PowerShellu:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Remove-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Použití služby Storage Analytics  

[Analýza úložiště Azure](storage-analytics.md) sestávají z [Analýza úložiště metrik](/rest/api/storageservices/about-storage-analytics-metrics) a [protokolování analýza úložiště](/rest/api/storageservices/about-storage-analytics-logging).

**Metriky analýza úložiště** se používají ke shromažďování metrik pro účty služby Azure Storage, které můžete použít k monitorování stavu účtu úložiště. Metriky lze povolit pro objekty blob, soubory, tabulky a fronty.

**Analýza úložiště protokolování** probíhá na straně serveru a umožňuje zaznamenávat podrobnosti o úspěšných i neúspěšných požadavcích na váš účet úložiště. Tyto protokoly umožňují zobrazit podrobné informace o operacích čtení, zápisu a odstraňování na vašich tabulkách, frontách a objektech blob a také důvody pro neúspěšné požadavky. Pro soubory Azure není protokolování k dispozici.

Monitorování můžete nakonfigurovat pomocí [Azure Portal](https://portal.azure.com), PowerShellu nebo programově pomocí klientské knihovny pro úložiště.

> [!NOTE]
> Můžete povolit minutové analýzy pomocí prostředí PowerShell. Tato možnost není k dispozici na portálu.
>

* Informace o tom, jak povolit a zobrazit data metrik úložiště pomocí PowerShellu, najdete v tématu metriky služby [Storage Analytics](storage-analytics-metrics.md).

* Informace o tom, jak povolit a načíst data protokolování úložiště pomocí PowerShellu, najdete v tématu [Postup povolení protokolování úložiště pomocí prostředí PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) a [hledání dat protokolu protokolování úložiště](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Podrobné informace o použití metrik úložiště a protokolování úložiště k řešení problémů s úložištěm najdete v tématu [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Správa dat v účtu úložiště

Teď, když jste se seznámili s tím, jak spravovat účet úložiště pomocí PowerShellu, můžete pomocí následujících článků zjistit, jak získat přístup k datovým objektům v účtu úložiště.

* [Jak spravovat objekty BLOB pomocí PowerShellu](../blobs/storage-how-to-use-blobs-powershell.md)
* [Jak spravovat soubory pomocí PowerShellu](../files/storage-how-to-use-files-powershell.md)
* [Správa front pomocí PowerShellu](../queues/storage-powershell-how-to-use-queues.md)
* [Provádění operací Azure Table Storage pomocí PowerShellu](../../storage/tables/table-storage-how-to-use-powershell.md)

Azure Cosmos DB rozhraní API pro tabulky poskytuje prémiové funkce pro úložiště tabulek, jako je globální distribuce klíč, čtení a zápisy s nízkou latencí, automatické sekundární indexování a vyhrazená propustnost.

* Další informace najdete v tématu [Azure Cosmos DB rozhraní API pro tabulky](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Nezávislá cloudová nasazení Azure

Většina lidí používá ke globálnímu nasazení Azure veřejný cloud Azure. Existují také některá nezávislá nasazení Microsoft Azure z důvodů svrchovanosti a tak dále. Tato nezávislá nasazení se označují jako "prostředí". K dispozici jsou tato prostředí:

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure Čína 21Vianet Cloud provozovaný společností 21Vianet v Číně](http://www.windowsazure.cn/)
* [Cloud pro Azure němčina](../../germany/germany-welcome.md)

Informace o tom, jak získat přístup k těmto cloudům a jejich úložišti pomocí PowerShellu, najdete v tématu [Správa úložiště v nezávislých cloudech Azure pomocí PowerShellu](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste pro toto cvičení vytvořili novou skupinu prostředků a účet úložiště, může yous odebrat všechny prostředky, které jste vytvořili, odebráním skupiny prostředků. Tím se odstraní také všechny prostředky, které skupina obsahuje. V takovém případě se odebere účet úložiště, který je vytvořený, a skupinu prostředků.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Další postup

Tento článek s návody popisuje běžné operace pomocí rutin roviny správy ke správě účtů úložiště. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Výpis účtů úložiště
> * Získat odkaz na existující účet úložiště
> * vytvořit účet úložiště
> * Nastavení vlastností účtu úložiště
> * Načtení a opětovné vygenerování přístupových klíčů
> * Ochrana přístupu k účtu úložiště
> * Povolit Analýza úložiště

V tomto článku najdete také odkazy na několik dalších článků, jako je třeba Správa datových objektů, povolení Analýza úložiště a přístup k nezávislým cloudům Azure, jako jsou například čínské cloudy, německé cloudy a státní Cloud. Tady je několik dalších souvisejících článků a prostředků pro referenci:

* [Rutiny PowerShellu pro řízení Azure Storage roviny](/powershell/module/az.storage/)
* [Azure Storage rutiny PowerShellu pro rovinu dat](/powershell/module/azure.storage/)
* [Reference k prostředí Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)
