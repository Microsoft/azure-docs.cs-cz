---
title: Integrovat protokoly z Azure Key Vault pomocí služby Event Hubs | Microsoft Docs
description: Kurz, který poskytuje nezbytných kroků k zpřístupnit protokoly Key Vault server SIEM s využitím protokolu integrace se službou Azure
services: security
author: barclayn
manager: MBaldwin
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 02/16/2018
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 42c30a825e44c289c42d0fb0a40a442c4ac950d7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Kurz pro Azure integrace protokolu: proces Azure Key Vault událostí pomocí služby Event Hubs

Integrace se službou protokolu Azure slouží k načtení protokolované události a zpřístupněte je váš systém zabezpečení informací a událostí správy (SIEM). Tento kurz ukazuje příklad použití Azure integrace protokolu ke zpracování protokoly, které jsou získány prostřednictvím Azure Event Hubs.

>[!IMPORTANT]
>Upřednostňovanou metodou pro integraci Azure protokoly se pomocí svého dodavatele SIEM Azure monitorování konektoru a následující tyto [pokyny](../monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs.md). Ale pokud dodavatele SIEM neposkytuje konektor k monitorování Azure, bude pravděpodobně možné použít protokol integrace se službou Azure jako dočasné řešení (je-li vašeho systému SIEM podporován protokol integrace se službou Azure) dokud takové connector je k dispozici.

 
Tento kurz použijte k se seznámit s jak integrace se službou protokolu Azure a Event Hubs spolupracují podle následujících kroků příklad a pochopíte, jak každý krok podporuje řešení. Potom můžete využít, co jste se naučili tady vytvořit vlastní postup pro podporu jedinečným požadavkům vaší společnosti.

>[!WARNING]
Kroky a příkazy v tomto kurzu nejsou určeny k kopírovat a vkládat data. Jsou jenom příklady. Nepoužívejte příkazy prostředí PowerShell "tak, jak je ve vašem prostředí za provozu. Je nutné přizpůsobit podle vašeho prostředí jedinečné.


Tento kurz vás provede procesem vezme zaprotokolované do centra událostí Azure Key Vault činnosti a zpřístupnit ho jako soubory JSON k vašemu systému SIEM. Potom můžete nakonfigurovat systém SIEM ke zpracování souborů JSON.

>[!NOTE]
>Většina kroky v tomto kurzu je konfigurace trezorů klíčů, účty úložiště a event hubs. Konkrétní postup integrace se službou Azure protokolu je na konci tohoto kurzu. Neprovádějte kroky v produkčním prostředí. Ty jsou určené pro prostředí laboratoře. Je nutné přizpůsobit kroky před jejich používání v produkčním prostředí.

Informace uvedené na této cestě vám pomůže pochopit důvodech každý krok. Odkazy na další články poskytují další podrobnosti na určité témata.

Další informace o službách, které uvádí tento kurz najdete v tématu: 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Integrace protokolů Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Počáteční nastavení

Před provedením kroků v tomto článku, budete potřebovat následující:

1. Předplatné Azure a účet v tomto předplatném s právy správce. Pokud nemáte předplatné, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/).
 
2. Systém s přístupem k Internetu, který splňuje požadavky na instalaci protokolu integrace se službou Azure. V systému může být v cloudové službě nebo hostovaný místně.

3. [Integrace se službou Azure protokolu](https://www.microsoft.com/download/details.aspx?id=53324) nainstalována. K její instalaci:

   a. Pomocí vzdálené plochy pro připojení k systému uveden v kroku 2.   
   b. Zkopírujte instalační službu Azure protokolu integrace do systému. Můžete [stáhnout instalační soubory](https://www.microsoft.com/download/details.aspx?id=53324).   
   c. Spusťte instalační program a přijměte licenční podmínky pro Software společnosti Microsoft.   
   d. Pokud bude poskytovat informace telemetrie, nechte zaškrtnuté políčko. Pokud místo by není odeslat informace o využití do Microsoftu, zrušte zaškrtnutí políčka.
   
   Další informace o protokolu integrace se službou Azure a jak ji nainstalovat, najdete v části [integrace protokolu Azure s Azure Diagnostics protokolování a předávání událostí systému Windows](security-azure-log-integration-get-started.md).

4. Nejnovější verze prostředí PowerShell.
 
   Pokud máte nainstalovaný Windows Server 2016, pak máte alespoň PowerShell 5.0. Pokud používáte jinou verzi systému Windows Server, bude pravděpodobně starší verzi prostředí PowerShell nainstalovaný. Verze můžete zkontrolovat tak, že zadáte ```get-host``` v okně prostředí PowerShell. Pokud nemáte nainstalovaný PowerShell 5.0, můžete [stažení](https://www.microsoft.com/download/details.aspx?id=50395).

   Až budete mít alespoň PowerShell 5.0, můžete přejít k instalaci nejnovější verze:
   
   a. V okně prostředí PowerShell, zadejte ```Install-Module Azure``` příkaz. Kroky instalace.    
   b. Zadejte ```Install-Module AzureRM``` příkaz. Kroky instalace.

   Další informace najdete v tématu [nainstalovat Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Vytváření podpůrné prvků infrastruktury

1. Otevřete okno prostředí PowerShell se zvýšenými oprávněními a přejděte na **C:\Program Files\Microsoft Azure protokolu integrace**.
2. Importujte rutiny AzLog spuštěním skriptu LoadAzLogModule.ps1. Zadejte `.\LoadAzLogModule.ps1` příkaz. (Všimněte si ". \" v tomto příkazu.) Mělo by se vám zobrazit přibližně toto:</br>

   ![Seznam načtených modulů](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

3. Zadejte `Connect-AzureRmAccount` příkaz. V okně přihlášení zadejte přihlašovací údaje pro předplatné, které budete používat pro účely tohoto kurzu.

   >[!NOTE]
   >Pokud je poprvé, který jste protokolování Azure z tohoto počítače, zobrazí se zpráva o povolení Microsoft ke shromažďování dat o použití prostředí PowerShell. Doporučujeme, abyste povolili tuto kolekci dat, protože se použije ke zlepšení prostředí Azure PowerShell.

4. Po úspěšném ověření jste přihlášeni a přečtěte si informace na následujícím snímku obrazovky. Poznamenejte si ID předplatného a název odběru, protože je k dokončení následujících krocích budete potřebovat.

   ![Okno prostředí PowerShell](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
5. Vytváření proměnných pro uložení hodnot, které se použijí později. Zadejte, každý z následujících řádků prostředí PowerShell. Možná budete muset upravit hodnoty tak, aby odpovídaly vašemu prostředí.
    - ```$subscriptionName = ‘Visual Studio Ultimate with MSDN’``` (Název odběru mohou lišit. Zobrazí se v rámci výstupu předchozí příkaz.)
    - ```$location = 'West US'``` (Tato proměnná se použije k předání umístění, kde by měl být vytvořen prostředky. Můžete změnit tuto proměnnou na libovolné místo dle vlastního výběru.)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (Název může být nic, ale měl by obsahovat pouze malá písmena a číslice.)
    - ``` $storageName = $name``` (Tato proměnná se použije pro název účtu úložiště.)
    - ```$rgname = $name ``` (Tato proměnná se použije pro název skupiny prostředků.)
    - ``` $eventHubNameSpaceName = $name``` (To je název oboru názvů centra událostí.)
6. Určete předplatné, budete pracovat s:
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
7. Vytvořte skupinu prostředků:
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Pokud zadáte `$rg` v tomto okamžiku byste měli vidět výstup podobný na tomto snímku obrazovky:

   ![Výstup po vytvoření skupiny prostředků](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
8. Vytvořte účet úložiště, který se použije ke sledování informací o stavu:
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
9. Vytvoření oboru názvů centra událostí. To je nutné k vytváření centra událostí.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
10. Získání ID pravidlo, které budou použity s poskytovateli statistiky:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
11. Získat všechny možné Azure umístění a přidat názvy do proměnné, která lze použít v pozdějším kroku:
    
    a. ```$locationObjects = Get-AzureRMLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Pokud zadáte `$locations` v tomto okamžiku zobrazí názvy umístění bez dalších informací vrácený Get-AzureRmLocation.
12. Vytvoření profilu protokolu správce Azure Resource Manager: 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Další informace o profilu protokolů Azure najdete v tématu [přehled protokol činnosti Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md).

> [!NOTE]
> Při pokusu o vytvoření profilu protokolu, může se zobrazí chybové hlášení. Potom můžete zkontrolovat v dokumentaci pro Get-AzureRmLogProfile a AzureRmLogProfile odebrat. Pokud spustíte Get-AzureRmLogProfile, zobrazí informace o profilu protokolu. Můžete odstranit stávající profil protokolu zadáním ```Remove-AzureRmLogProfile -name 'Log Profile Name' ``` příkaz.
>
>![Správce prostředků profilu došlo k chybě](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

1. Vytvoření trezoru klíčů:

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

2. Konfigurace protokolování pro key vault:

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Generovat aktivitu protokolu

Požadavky nutné k odeslání do Key Vault pro generování aktivity protokolu. Akce, jako generování klíčů, ukládání tajné klíče, nebo čtení tajných klíčů z Key Vault vytvoří položky protokolu.

1. Zobrazí aktuální klíče k úložišti:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
2. Generovat nové **key2**:
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
3. Nezobrazovat klíče, zjistíte, že **key2** obsahuje jiné hodnoty:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
4. Nastavení a čtení tajný klíč pro generování položky další protokolu:
    
   a. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Vrátí tajné](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Konfigurace integrace protokolů Azure

Teď, když jste nakonfigurovali všechny požadované prvky mít Key Vault protokolování do centra událostí, je třeba nakonfigurovat integraci Azure protokolu:

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
2. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
3. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
4. ``` $storagekey = $storagekeys[0].Value```

Spusťte příkaz AzLog pro každé Centrum událostí:

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
2. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Za minutu z posledních dvou příkazů měli byste vidět generován soubory JSON. Je můžete potvrdit, že sledováním adresáři **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Další postup

- [Integrace Azure protokolu – nejčastější dotazy](security-azure-log-integration-faq.md)
- [Začínáme s Azure protokolu integrace](security-azure-log-integration-get-started.md)
- [Protokoly z prostředků Azure integrovat do vašeho systému SIEM systémy](security-azure-log-integration-overview.md)
