---
title: Používání Azure PowerShellu s Azure Storage | Dokumenty společnosti Microsoft
description: Přečtěte si, jak používat rutiny Azure PowerShellu pro Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/16/2018
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: d2404ee58f5f44fbe5625f267e6d1c504d0bd237
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75465093"
---
# <a name="using-azure-powershell-with-azure-storage"></a>Použití Azure Powershell s Azure Storage

Azure PowerShell se používá k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. Pro Azure Storage tyto rutiny spadají do dvou kategorií – rovina ovládacího prvku a rovina dat. Rutiny roviny ovládacího prvku se používají ke správě účtu úložiště – k vytvoření účtů úložiště, nastavení vlastností, odstranění účtů úložiště, otočení přístupových klíčů a tak dále. Rutiny roviny dat se používají ke správě dat uložených *v* účtu úložiště. Například nahrávání objektů BLOB, vytváření sdílených složek a přidávání zpráv do fronty.

Tento článek s návody popisuje běžné operace pomocí rutin roviny správy pro správu účtů úložiště. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Seznam účtů úložiště
> * Získání odkazu na existující účet úložiště
> * vytvořit účet úložiště
> * Nastavení vlastností účtu úložiště
> * Načtení a obnovení přístupových klíčů
> * Ochrana přístupu k účtu úložiště
> * Povolení analýzy úložiště

Tento článek obsahuje odkazy na několik dalších článků Prostředí PowerShell pro úložiště, jako je například povolení a přístup k storage Analytics, jak používat rutiny roviny dat a jak získat přístup k nezávislým cloudům Azure, jako je China Cloud, Německý cloud a Správa. Cloud.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Toto cvičení vyžaduje modul Azure PowerShell Az verze 0.7 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

Pro toto cvičení můžete zadat příkazy do běžného okna prostředí PowerShell nebo můžete použít [prostředí ISE (Windows PowerShell Integrated Scripting Environment)](/powershell/scripting/components/ise/exploring-the-windows-powershell-ise) a zadat příkazy do editoru a potom otestovat jeden nebo více příkazů najednou při procházení příkladů. Můžete zvýraznit řádky, které chcete spustit, a kliknutím na Spustit vybrané tyto příkazy spustit.

Další informace o účtech úložiště najdete [v tématu Úvod do úložiště](storage-introduction.md) a O [účtech úložiště Azure](storage-create-storage-account.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se ke svému předplatnému Azure pomocí příkazu `Connect-AzAccount` a postupujte podle pokynů na obrazovce.

```powershell
Connect-AzAccount
```

## <a name="list-the-storage-accounts-in-the-subscription"></a>Seznam účtů úložiště v předplatném

Spusťte rutinu [Get-AzStorageAccount](/powershell/module/az.storage/Get-azStorageAccount) a načtěte seznam účtů úložiště v aktuálním předplatném.

```powershell
Get-AzStorageAccount | Select StorageAccountName, Location
```

## <a name="get-a-reference-to-a-storage-account"></a>Získání odkazu na účet úložiště

Dále potřebujete odkaz na účet úložiště. Můžete buď vytvořit nový účet úložiště, nebo získat odkaz na existující účet úložiště. Následující část ukazuje obě metody.

### <a name="use-an-existing-storage-account"></a>Použití existujícího účtu úložiště

Chcete-li načíst existující účet úložiště, potřebujete název skupiny prostředků a název účtu úložiště. Nastavte proměnné pro tato dvě pole a pak použijte rutinu [Get-AzStorageAccount.](/powershell/module/az.storage/Get-azStorageAccount)

```powershell
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"

$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName
```

Teď máte $storageAccount, který odkazuje na existující účet úložiště.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Následující skript ukazuje, jak vytvořit účet úložiště pro obecné účely pomocí [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Po vytvoření účtu načtěte jeho kontext, který lze použít v následujících příkazech, nikoli v zadání ověřování při každém volání.

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

Skript používá následující rutiny prostředí PowerShell:

*   [Get-AzLocation](/powershell/module/az.resources/get-azlocation) -- načte seznam platných umístění. Příklad používá `eastus` pro umístění.

*   [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) – vytvoří novou skupinu prostředků. Skupina prostředků je logický kontejner, do kterého se vaše prostředky Azure nasazují a spravují. Naše se `teststoragerg`jmenuje .

*   [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) -- vytvoří účet úložiště. Příklad používá `testpshstorage`.

Název skladové položky označuje typ replikace pro účet úložiště, například LRS (Místně redundantní úložiště). Další informace o replikaci najdete v [tématu Replikace úložiště Azure](storage-redundancy.md).

> [!IMPORTANT]
> Název vašeho účtu úložiště musí být jedinečný v rámci Azure a musí být malá písmena. Zásady vytváření názvů a omezení najdete v [tématu Pojmenování a odkazování kontejnerů, objektů BLOB a metadat .](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)
>

Teď máte nový účet úložiště a odkaz na něj.

## <a name="manage-the-storage-account"></a>Správa účtu úložiště

Teď, když máte odkaz na nový účet úložiště nebo existující účet úložiště, následující část zobrazuje některé příkazy, které můžete použít ke správě účtu úložiště.

### <a name="storage-account-properties"></a>Vlastnosti účtu úložiště

Chcete-li změnit nastavení účtu úložiště, použijte [set-azstorageaccount](/powershell/module/az.storage/set-azstorageaccount). Zatímco nelze změnit umístění účtu úložiště nebo skupiny prostředků, ve kterém se nachází, můžete změnit mnoho dalších vlastností. V následujícím seznamu jsou uvedeny některé vlastnosti, které můžete změnit pomocí prostředí PowerShell.

* **Vlastní doména** přiřazená k účtu úložiště.

* **Značky** přiřazené k účtu úložiště. Značky se často používají ke kategorizaci prostředků pro účely fakturace.

* Skladová **položka** je nastavení replikace pro účet úložiště, jako je například LRS pro místně redundantní úložiště. Můžete například změnit ze\_standardního\_LRS na\_standardní GRS nebo standardní RAGRS. Všimněte si, že\_nelze změnit\_standardní ZRS,\_Standardní GZRS, Standardní RAGZRS nebo Premium\_LRS na jiné sku, nebo změnit jiné skusto na tyto.

* **Úroveň přístupu** pro účty úložiště objektů Blob. Hodnota pro úroveň přístupu je nastavena na **horkou** nebo **studenou**a umožňuje minimalizovat náklady výběrem úrovně přístupu, která je v souladu s tím, jak používáte účet úložiště. Další informace naleznete v tématu [Horké, studené a archivní úrovně úložiště](../blobs/storage-blob-storage-tiers.md).

* Povolte pouze přenos HTTPS.

### <a name="manage-the-access-keys"></a>Správa přístupových klíčů

Účet Azure Storage je dodáván se dvěma klíči účtu. Chcete-li načíst klíče, použijte [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey). Tento příklad načte první klíč. Chcete-li načíst `Value[1]` druhý, `Value[0]`použijte místo .

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroup `
    -Name $storageAccountName).Value[0]
```

Chcete-li klíč znovu vygenerovat, použijte [klávesu New-AzStorageAccountKey](/powershell/module/az.Storage/New-azStorageAccountKey).

```powershell
New-AzStorageAccountKey -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -KeyName key1
```

Chcete-li znovu vygenerovat druhý klíč, použijte `key2` jako název klíče namísto `key1`.

Znovu vygenerujte jeden z klíčů a pak jej znovu načtěte, abyste viděli novou hodnotu.

> [!NOTE]
> Před obnovením klíče pro účet produkčního úložiště byste měli pečlivě plánovat. Obnovení jednoho nebo obou klíčů zruší platnost přístupu pro všechny aplikace pomocí klíče, který byl obnoven. Další informace naleznete v [tématu Správa přístupových klíčů účtu úložiště](storage-account-keys-manage.md).


### <a name="delete-a-storage-account"></a>Odstranění účtu úložiště

Chcete-li odstranit účet úložiště, použijte [příkaz Remove-AzStorageAccount](/powershell/module/az.storage/Remove-azStorageAccount).

```powershell
Remove-AzStorageAccount -ResourceGroup $resourceGroup -AccountName $storageAccountName
```

> [!IMPORTANT]
> Když odstraníte účet úložiště, odstraní se také všechny prostředky uložené v účtu. Pokud omylem odstraníte účet, okamžitě zavolejte na podporu a otevřete lístek pro obnovení účtu úložiště. Obnovení dat není zaručeno, ale někdy funguje. Nevytvářejte nový účet úložiště se stejným názvem jako starý, dokud nebude vyřešen lístek podpory.
>

### <a name="protect-your-storage-account-using-vnets-and-firewalls"></a>Ochrana účtu úložiště pomocí virtuálních sítí a firewallů

Ve výchozím nastavení jsou všechny účty úložiště přístupné ze všech sítí, které mají přístup k Internetu. Můžete však nakonfigurovat pravidla sítě tak, aby umožňovala přístup k účtu úložiště pouze aplikacím z určitých virtuálních sítí. Další informace najdete [v tématu Konfigurace bran firewall úložišť Azure a virtuálních sítí](storage-network-security.md).

Článek ukazuje, jak spravovat tato nastavení pomocí následujících rutin prostředí PowerShell:
* [Add-AzStorageAccountNetworkRule](/powershell/module/az.Storage/Add-azStorageAccountNetworkRule)
* [Update-AzStorageAccountNetworkRuleSet](/powershell/module/az.storage/update-azstorageaccountnetworkruleset)
* [Odebrat-AzStorageAccountNetworkRule](https://docs.microsoft.com/powershell/module/az.storage/remove-azstorageaccountnetworkrule)

## <a name="use-storage-analytics"></a>Použití analýzy úložiště

[Azure Storage Analytics](storage-analytics.md) se skládá z [metrik storage analytics](/rest/api/storageservices/about-storage-analytics-metrics) a [protokolování analýzy úložiště](/rest/api/storageservices/about-storage-analytics-logging).

**Metriky Storage Analytics** se používají ke shromažďování metrik pro vaše účty úložiště Azure, které můžete použít ke sledování stavu účtu úložiště. Metriky lze povolit pro objekty BLOB, soubory, tabulky a fronty.

**Protokolování služby Storage Analytics** probíhá na straně serveru a umožňuje zaznamenávat podrobnosti o úspěšných i neúspěšných požadavcích na váš účet úložiště. Tyto protokoly umožňují zobrazit podrobnosti o operacích čtení, zápisu a odstraňování proti vašim tabulkám, frontám a objektům BLOB, stejně jako důvody neúspěšných požadavků. Protokolování není k dispozici pro soubory Azure.

Monitorování můžete nakonfigurovat pomocí [portálu Azure](https://portal.azure.com), PowerShellu nebo programově pomocí knihovny klienta úložiště.

> [!NOTE]
> Pomocí prostředí PowerShell můžete povolit analýzu minut. Tato funkce není k dispozici na portálu.
>

* Informace o povolení a zobrazení dat metrik úložiště pomocí PowerShellu najdete v [tématu Metriky analýzy úložiště](storage-analytics-metrics.md).

* Informace o povolení a načtení dat protokolování úložiště pomocí PowerShellu najdete v tématu [Povolení protokolování úložiště pomocí prostředí PowerShell](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data) a [Hledání dat protokolu protokolování úložiště](/rest/api/storageservices/Enabling-Storage-Logging-and-Accessing-Log-Data).

* Podrobné informace o používání metrik úložiště a protokolování úložiště k řešení problémů s úložištěm najdete v tématu [Monitorování, diagnostika a řešení potíží s úložištěm Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="manage-the-data-in-the-storage-account"></a>Správa dat v účtu úložiště

Teď, když pochopíte, jak spravovat účet úložiště pomocí PowerShellu, můžete použít následující články, abyste zjistili, jak získat přístup k datovým objektům v účtu úložiště.

* [Správa objektů BLOB pomocí Prostředí PowerShell](../blobs/storage-how-to-use-blobs-powershell.md)
* [Jak spravovat soubory pomocí PowerShellu](../files/storage-how-to-use-files-powershell.md)
* [Správa front pomocí PowerShellu](../queues/storage-powershell-how-to-use-queues.md)
* [Provádění operací úložiště azure table pomocí PowerShellu](../../storage/tables/table-storage-how-to-use-powershell.md)

Azure Cosmos DB Table API poskytuje prémiové funkce pro úložiště tabulek, jako je globální distribuce na klíč, čtení a zápisy s nízkou latencí, automatické sekundární indexování a vyhrazená propustnost.

* Další informace naleznete v tématu [Azure Cosmos DB Table API](../../cosmos-db/table-introduction.md).

## <a name="independent-cloud-deployments-of-azure"></a>Nezávislá cloudová nasazení Azure

Většina lidí používá Azure Public Cloud pro své globální nasazení Azure. Existují také některé nezávislé nasazení Microsoft Azure z důvodu suverenity a tak dále. Tato nezávislá nasazení se označují jako "prostředí". Toto jsou dostupná prostředí:

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure China 21Vianet Cloud provozovaný společností 21Vianet v Číně](http://www.windowsazure.cn/)
* [Německý cloud Azure](../../germany/germany-welcome.md)

Informace o tom, jak získat přístup k těmto cloudům a jejich úložišti pomocí PowerShellu, najdete [v tématu Správa úložiště v cloudech nezávislých pro Azure pomocí PowerShellu](storage-powershell-independent-clouds.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste pro toto cvičení vytvořili novou skupinu prostředků a účet úložiště, můžete odebrat všechny prostředky, které jste vytvořili odebráním skupiny prostředků. Tím se odstraní také všechny prostředky, které skupina obsahuje. V takovém případě odebere vytvořený účet úložiště a samotnou skupinu prostředků.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```
## <a name="next-steps"></a>Další kroky

Tento článek s návody popisuje běžné operace pomocí rutin roviny správy pro správu účtů úložiště. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Seznam účtů úložiště
> * Získání odkazu na existující účet úložiště
> * vytvořit účet úložiště
> * Nastavení vlastností účtu úložiště
> * Načtení a obnovení přístupových klíčů
> * Ochrana přístupu k účtu úložiště
> * Povolení analýzy úložiště

Tento článek také poskytl odkazy na několik dalších článků, například jak spravovat datové objekty, jak povolit analýzu úložiště a jak získat přístup k nezávislým cloudům Azure, jako je China Cloud, German Cloud a Government Cloud. Zde jsou některé další související články a zdroje pro referenci:

* [Rutiny PowerShellu pro ovládací prvek Azure Storage](/powershell/module/az.storage/)
* [Rutiny Prostředí PowerShell pro datové roviny Azure Storage](/powershell/module/azure.storage/)
* [Referenční informace k Windows PowerShellu](/powershell/scripting/overview)
