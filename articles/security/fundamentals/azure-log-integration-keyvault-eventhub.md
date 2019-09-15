---
title: Integrace protokolů z Azure Key Vault pomocí Event Hubs | Microsoft Docs
description: Kurz, který poskytuje kroky potřebné k zpřístupnění Key Vaultch protokolů pro SIEM pomocí Azure Log Integration
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.date: 05/28/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 15223c59f270dc562e521697186cfaf7f30073b9
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2019
ms.locfileid: "71004117"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Azure Log Integration kurz: Zpracování událostí Azure Key Vault pomocí Event Hubs

>[!IMPORTANT]
> Funkce integrace protokolu Azure bude zastaralá od 06/15/2019. Stahování AzLog bylo zakázáno 27. června 2018. Další informace o tom, co dělat, najdete v příspěvku [pomocí Azure monitoru pro integraci s nástroji Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) . 

K načtení protokolovaných událostí a jejich zpřístupnění pro systém SIEM (Security Information and Event Management) můžete použít Azure Log Integration. V tomto kurzu se dozvíte, jak můžete Azure Log Integration použít ke zpracování protokolů získaných prostřednictvím Azure Event Hubs.

Upřednostňovanou metodou pro integraci protokolů Azure je použití konektoru Azure Monitor dodavatele SIEM a podle těchto [pokynů](../../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Pokud však dodavatel SIEM neposkytuje konektor pro Azure Monitor, můžete Azure Log Integration použít jako dočasné řešení (Pokud SIEM podporuje Azure Log Integration), dokud nebude takový konektor k dispozici.

 
V tomto kurzu se seznámíte s tím, jak Azure Log Integration a Event Hubs vzájemně spolupracují, podle uvedených ukázkových kroků a porozumění tomu, jak každý krok podporuje řešení. Pak můžete v takovém případě zjistit, co jste se naučili, a vytvořit vlastní kroky pro podporu jedinečných požadavků vaší společnosti.

> [!WARNING]
> Kroky a příkazy v tomto kurzu nejsou určeny ke zkopírování a vložení. Jedná se pouze o příklady. Nepoužívejte Příkazy PowerShellu "tak, jak jsou" v živém prostředí. Je nutné je přizpůsobit na základě jedinečného prostředí.


Tento kurz vás provede procesem převzetí Azure Key Vault aktivity do centra událostí a jejich zpřístupnění jako soubory JSON pro systém SIEM. Pak můžete nakonfigurovat systém SIEM tak, aby zpracovával soubory JSON.

>[!NOTE]
>Většina kroků v tomto kurzu zahrnuje konfiguraci trezorů klíčů, účtů úložiště a Center událostí. Konkrétní kroky Azure Log Integration najdete na konci tohoto kurzu. Tyto kroky neprovádějte v produkčním prostředí. Jsou určené jenom pro testovací prostředí. Než budete tyto kroky používat v produkčním prostředí, je nutné je přizpůsobit.

Informace, které jsou k dispozici, vám pomohou pochopit důvody za jednotlivými kroky. Odkazy na jiné články vám poskytnou více podrobností pro určitá témata.

Další informace o službách, které tento kurz uvádí, najdete v těchto tématech: 

- [Azure Key Vault](/azure/key-vault/key-vault-overview)
- [Azure Event Hubs](/azure/event-hubs/event-hubs-what-is-event-hubs)
- [Azure Log Integration](azure-log-integration-overview.md)


## <a name="initial-setup"></a>Počáteční nastavení

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Než budete moct dokončit kroky v tomto článku, budete potřebovat následující:

* Předplatné a účet Azure v tomto předplatném s právy správce. Pokud předplatné nemáte, můžete si vytvořit [bezplatný účet](https://azure.microsoft.com/free/).
 
* Systém s přístupem k Internetu, který splňuje požadavky pro instalaci Azure Log Integration. Systém může být v cloudové službě nebo hostovaný místně.

* Azure Log Integration nainstalován. Instalace:

   a. Pomocí vzdálené plochy se připojte k systému, který je uvedený v kroku 2.   
   b. Zkopírujte instalační program Azure Log Integration do systému. c. Spusťte instalační program a přijměte licenční podmínky pro software společnosti Microsoft.

* Pokud zadáte informace o telemetrie, ponechte políčko zaškrtnuté. Pokud nechcete odesílat informace o používání společnosti Microsoft, zrušte zaškrtnutí políčka.

   Další informace o Azure Log Integration a způsobu jejich instalace najdete v tématu [Azure log Integration s Azure Diagnostics protokolování a předávání událostí systému Windows](azure-log-integration-get-started.md).

* Nejnovější verze prostředí PowerShell.

   Pokud máte nainstalovaný Windows Server 2016, budete mít aspoň PowerShell 5,0. Pokud používáte jinou verzi Windows serveru, je možné, že máte nainstalovanou starší verzi PowerShellu. Verzi můžete ověřit zadáním ```get-host``` v okně PowerShellu. Pokud nemáte nainstalované prostředí PowerShell 5,0, můžete [si ho stáhnout](https://www.microsoft.com/download/details.aspx?id=50395).

   Až budete mít aspoň PowerShell 5,0, můžete pokračovat v instalaci nejnovější verze podle pokynů v části [instalace Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="create-supporting-infrastructure-elements"></a>Vytváření podpůrných prvků infrastruktury

1. Otevřete okno PowerShellu se zvýšenými oprávněními a do složky **C:\Program Files\Microsoft Azure log Integration**.
1. Naimportujte rutiny AzLog spuštěním skriptu LoadAzLogModule. ps1. `.\LoadAzLogModule.ps1` Zadejte příkaz. (Všimněte si ". \" v tomto příkazu.) Mělo by se vám zobrazit přibližně toto:</br>

   ![Seznam načtených modulů](./media/azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. `Connect-AzAccount` Zadejte příkaz. V okně přihlášení zadejte informace o přihlašovacích údajích k předplatnému, které budete používat pro tento kurz.

   >[!NOTE]
   >Pokud se z tohoto počítače poprvé přihlašujete do Azure, zobrazí se zpráva, že Microsoft umožňuje shromažďovat data o využití PowerShellu. Tuto kolekci dat doporučujeme povolit, protože se bude používat ke zlepšení Azure PowerShell.

1. Po úspěšném ověření budete přihlášení. Poznamenejte si ID předplatného a název předplatného, protože je budete potřebovat k dokončení dalších kroků.

1. Vytvořte proměnné pro uložení hodnot, které budou použity později. Zadejte všechny následující řádky prostředí PowerShell. Možná budete muset upravit hodnoty tak, aby odpovídaly vašemu prostředí.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'```(Název vašeho předplatného může být jiný. Můžete ji zobrazit jako součást výstupu předchozího příkazu.)
    - ```$location = 'West US'```(Tato proměnná se použije k předání umístění, kde se prostředky mají vytvořit. Tuto proměnnou můžete změnit na libovolné místo, kde si zvolíte.)
    - ```$random = Get-Random```
    - ```$name = 'azlogtest' + $random```(Název může být cokoli, ale měl by obsahovat jenom malá písmena a číslice.)
    - ```$storageName = $name```(Tato proměnná se použije pro název účtu úložiště.)
    - ```$rgname = $name```(Tato proměnná se použije pro název skupiny prostředků.)
    - ```$eventHubNameSpaceName = $name```(Jedná se o název oboru názvů centra událostí.)
1. Zadejte předplatné, se kterým budete pracovat:
    
    ```Select-AzSubscription -SubscriptionName $subscriptionName```
1. Vytvořte skupinu prostředků:
    
    ```$rg = New-AzResourceGroup -Name $rgname -Location $location```
    
   Pokud v tuto `$rg` chvíli zadáte, měl by se zobrazit výstup podobný tomuto snímku obrazovky:

   ![Výstup po vytvoření skupiny prostředků](./media/azure-log-integration-keyvault-eventhub/create-rg.png)
1. Vytvořte účet úložiště, který bude sloužit k udržení přehledu o stavu:
    
    ```$storage = New-AzStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Vytvořte obor názvů centra událostí. To je nutné k vytvoření centra událostí.
    
    ```$eventHubNameSpace = New-AzEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Získejte ID pravidla, které se bude používat se zprostředkovatelem Insights:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey'```
1. Získejte všechna možná umístění Azure a přidejte názvy do proměnné, kterou můžete použít v pozdějším kroku:
    
    a. ```$locationObjects = Get-AzLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Pokud v tomto `$locations` okamžiku zadáte, zobrazí se názvy umístění bez dalších informací vrácených funkcí get-AzLocation.
1. Vytvořit profil Azure Resource Managerho protokolu: 
    
    ```Add-AzLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Další informace o profilu protokolu Azure najdete v tématu [Přehled protokolu aktivit Azure](../../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Při pokusu o vytvoření profilu protokolu se může zobrazit chybová zpráva. Pak si můžete projít dokumentaci pro Get-AzLogProfile a Remove-AzLogProfile. Pokud spustíte rutinu Get-AzLogProfile, zobrazí se informace o profilu protokolu. Stávající profil protokolu můžete odstranit zadáním ```Remove-AzLogProfile -name 'Log Profile Name'``` příkazu.
>
>![Chyba profilu Správce prostředků](./media/azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

1. Vytvořte Trezor klíčů:

   ```$kv = New-AzKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location```

1. Konfigurace protokolování pro Trezor klíčů:

   ```Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true```

## <a name="generate-log-activity"></a>Aktivita generování protokolu

Aby bylo možné vygenerovat aktivitu protokolu, je nutné odeslat žádosti Key Vault. Akce jako generování klíčů, ukládání tajných kódů nebo čtení tajných kódů z Key Vault vytvoří položky protokolu.

1. Zobrazit aktuální klíče úložiště:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Vygenerujte nový **key2**:
    
   ```New-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Znovu zobrazte klíče a podívejte se, že **key2** má jinou hodnotu:
    
   ```Get-AzStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Nastavení a čtení tajného klíče pro generování dalších položek protokolu:
    
   a. ```Set-AzKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Vrácený tajný kód](./media/azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Konfigurace Azure Log Integration

Teď, když jste nakonfigurovali všechny požadované prvky, aby Key Vault protokolování do centra událostí, je nutné nakonfigurovat Azure Log Integration:

1. ```$storage = Get-AzStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ```$storagekey = $storagekeys[0].Value```

Pro každé centrum událostí spusťte příkaz AzLog:

1. ```$eventhubs = Get-AzEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Po minutě nebo spuštění posledních dvou příkazů by se měly zobrazit generované soubory JSON. Můžete to ověřit tím, že budete sledovat adresář **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Další postup

- [Nejčastější dotazy k Azure Log Integration](azure-log-integration-faq.md)
- [Začínáme s Azure Log Integration](azure-log-integration-get-started.md)
- [Integrace protokolů z prostředků Azure do systémů SIEM](azure-log-integration-overview.md)
