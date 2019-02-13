---
title: Integrace protokolů ze služby Azure Key Vault pomocí Event Hubs | Dokumentace Microsoftu
description: Kurz, který obsahuje kroky potřebné ke zpřístupnění protokoly Key Vault do SIEM pomocí integrace protokolů Azure
services: security
author: barclayn
manager: barbkess
editor: TomShinder
ms.assetid: ''
ms.service: security
ms.topic: article
ms.date: 01/14/2019
ms.author: Barclayn
ms.custom: AzLog
ms.openlocfilehash: 21a1cd6d0326c834a05681ffe98555ea52858e6e
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56106558"
---
# <a name="azure-log-integration-tutorial-process-azure-key-vault-events-by-using-event-hubs"></a>Kurz integrace protokolů Azure: Zpracování událostí služby Azure Key Vault pomocí Event Hubs

>[!IMPORTANT]
> Funkce integrace protokolů Azure se přestanou používat podle 06/01/2019. Soubory ke stažení AzLog byly deaktivovány 27. června 2018. Pokyny, jak postupovat přesun vpřed revizi, příspěvek [použití Azure monitoru k integraci s nástroji SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Integrace protokolů Azure slouží k načtení protokolovaných událostí a zpřístupněte je do vašeho systému pro správu (SIEM) událostí zabezpečení. Tento kurz ukazuje příklad použití Azure Log Integration pro zpracování protokoly získané prostřednictvím služby Azure Event Hubs.

Je upřednostňovanou metodou pro integraci protokolů Azure pomocí Azure Monitor konektor SIEM dodavatele a podle těchto [pokyny](../azure-monitor/platform/stream-monitoring-data-event-hubs.md). Nicméně pokud dodavatele vašeho systému SIEM neposkytuje konektor k monitorování Azure, bude pravděpodobně možné používat Azure Log Integration jako dočasné řešení (Pokud vašeho systému SIEM je podporovaný službou Azure Log Integration) dokud tyto connector je k dispozici.

 
V tomto kurzu použijte k získání seznámí s tím, jak integrace protokolů Azure Event Hubs spolu fungují a podle následujících kroků příklad a vysvětlení, jak každý krok podporuje řešení. Pak můžete využít, co jste se naučili v tomto poli k vytvoření vlastní kroky pro podporu jedinečným požadavkům vaší společnosti.

>[!WARNING]
Kroky a příkazy v tomto kurzu nejsou určeny k zkopírovat a vložit. Jsou to pouze jako příklady. Nepoužívejte příkazy prostředí PowerShell "tak jak jsou" v živém prostředí. Je nutné přizpůsobit podle vašeho prostředí jedinečné.


Tento kurz vás provede procesem odběr služby Azure Key Vault aktivit do centra událostí a jejich zpřístupnění jako soubory JSON do vašeho systému SIEM. Potom můžete nakonfigurovat vašeho systému SIEM ke zpracování souborů JSON.

>[!NOTE]
>Většina kroků v tomto kurzu se týkají konfigurace trezorů klíčů, účty úložiště a centra událostí. Konkrétní kroky integrace protokolů Azure se na konci tohoto kurzu. Nebudete provádět tyto kroky v produkčním prostředí. Ty jsou určené pro prostředí laboratoře. Je třeba upravit kroky před jejich použitím v produkčním prostředí.

Informace uvedené na cestě vám pomůže pochopit důvodech každého kroku. Odkazy na další články poskytují další podrobnosti na určitá témata.

Další informace o službách, které uvádí v tomto kurzu naleznete v tématu: 

- [Azure Key Vault](../key-vault/key-vault-whatis.md)
- [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Integrace protokolů Azure](security-azure-log-integration-overview.md)


## <a name="initial-setup"></a>Počáteční nastavení

Před dokončením kroků v tomto článku, budete potřebovat následující:

1. Předplatné Azure a účet pro toto předplatné s oprávněním správce. Pokud předplatné nemáte, můžete vytvořit [bezplatný účet](https://azure.microsoft.com/free/).
 
1. Systém s přístupem k Internetu, který splňuje požadavky na instalaci integrace protokolů Azure. Systém může být v cloudové službě nebo na místně hostovaném.

1. Integrace s Azure Log nainstalované. K jeho instalaci:

   a. Použití vzdálené plochy pro připojení k systému uvedené v kroku 2.   
   b. Zkopírujte instalační program pro integraci protokolů Azure do systému. c. Spusťte instalační program a přijměte licenční podmínky pro Software společnosti Microsoft.

1. Pokud vám poskytne informace o telemetrii, nechte toto políčko zaškrtnuté. Pokud místo toho by není odeslat informace o využití do Microsoftu, zrušte zaškrtnutí políčka.

   Další informace o integraci protokolů Azure a jak ji nainstalovat, naleznete v tématu [integrace protokolů Azure s protokolování diagnostiky Azure a předávání událostí Windows](security-azure-log-integration-get-started.md).

1. Nejnovější verze prostředí PowerShell.

   Pokud máte nainstalovaný Windows Server 2016, pak je mít minimálně PowerShell 5.0. Pokud používáte jiná verze systému Windows Server, bude pravděpodobně starší verzi prostředí PowerShell nainstalovaný. Můžete zkontrolovat verzi tak, že zadáte ```get-host``` v okně Powershellu. Pokud nemáte nainstalovaný PowerShell 5.0, můžete si [si ho stáhnout](https://www.microsoft.com/download/details.aspx?id=50395).

   Až budete mít alespoň Powershellu 5.0, můžete přejít k instalaci nejnovější verze:

   a. V okně Powershellu, zadejte ```Install-Module Azure``` příkazu. Dokončete instalaci.    
   b. Zadejte ```Install-Module AzureRM``` příkazu. Dokončete instalaci.

   Další informace najdete v tématu [instalace Azure Powershellu](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-4.0.0).


## <a name="create-supporting-infrastructure-elements"></a>Vytvořit prvky podpůrné infrastruktury

1. Otevřete okno Powershellu se zvýšenými oprávněními a přejděte na **C:\Program Files\Microsoft Azure Log Integration**.
1. Importujte rutiny AzLog spuštěním skriptu LoadAzLogModule.ps1. Zadejte `.\LoadAzLogModule.ps1` příkazu. (Všimněte si, že ". \" v tomto příkazu.) Mělo by se vám zobrazit přibližně toto:</br>

   ![Seznam načtené moduly](./media/security-azure-log-integration-keyvault-eventhub/loaded-modules.png)

1. Zadejte `Connect-AzureRmAccount` příkazu. V okně přihlášení zadejte přihlašovací údaje pro předplatné, které budete používat pro účely tohoto kurzu.

   >[!NOTE]
   >Pokud je to poprvé, budete se přihlášení k Azure z tohoto počítače, zobrazí se zpráva o povolení Microsoft ke shromažďování dat o využití prostředí PowerShell. Doporučujeme, abyste povolili tímto shromažďováním dat, protože se používá ke zlepšení prostředí Azure PowerShell.

1. Po úspěšném ověření jste přihlášeni a zobrazit informace na následujícím snímku obrazovky. Poznamenejte si ID předplatného a název odběru, protože jejich dokončení dalších krocích budete potřebovat.

   ![Okno prostředí PowerShell](./media/security-azure-log-integration-keyvault-eventhub/login-azurermaccount.png)
1. Vytvoření proměnné k ukládání hodnot, které se později použijí. Zadejte všechny následující příkazy prostředí PowerShell. Můžete potřebovat a upravte hodnoty tak, aby odpovídaly vašemu prostředí.
    - ```$subscriptionName = 'Visual Studio Ultimate with MSDN'``` (Název vašeho odběru může lišit. Zobrazí se jako součást výstupního předchozím příkazem.)
    - ```$location = 'West US'``` (Tato proměnná se používá k předání umístění, kde by měl být vytvořen prostředky. Můžete změnit tuto proměnnou na libovolné místo podle vašeho výběru.)
    - ```$random = Get-Random```
    - ``` $name = 'azlogtest' + $random``` (Název může obsahovat cokoli, ale měl by obsahovat pouze malá písmena a číslice.)
    - ``` $storageName = $name``` (Tato proměnná se používá pro název účtu úložiště.)
    - ```$rgname = $name ``` (Tato proměnná se používá pro název skupiny prostředků.)
    - ``` $eventHubNameSpaceName = $name``` (To je název oboru názvů centra událostí).
1. Určete předplatné, můžete pracovat s:
    
    ```Select-AzureRmSubscription -SubscriptionName $subscriptionName```
1. Vytvořte skupinu prostředků:
    
    ```$rg = New-AzureRmResourceGroup -Name $rgname -Location $location```
    
   Pokud zadáte `$rg` v tomto okamžiku byste měli vidět výstup podobný tomuto snímku obrazovky:

   ![Výstup po vytvoření skupiny prostředků](./media/security-azure-log-integration-keyvault-eventhub/create-rg.png)
1. Vytvoření účtu úložiště, který se použije ke sledování informací o stavu:
    
    ```$storage = New-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename -Location $location -SkuName Standard_LRS```
1. Vytvořte obor názvů centra událostí. Vyžaduje se k vytvoření centra událostí.
    
    ```$eventHubNameSpace = New-AzureRmEventHubNamespace -ResourceGroupName $rgname -NamespaceName $eventHubnamespaceName -Location $location```
1. Získejte ID pravidla, která se použije u poskytovatele insights:
    
    ```$sbruleid = $eventHubNameSpace.Id +'/authorizationrules/RootManageSharedAccessKey' ```
1. Získání všech umístěních Azure je to možné a přidejte název proměnné, která lze použít v pozdějším kroku:
    
    a. ```$locationObjects = Get-AzureRMLocation```    
    b. ```$locations = @('global') + $locationobjects.location```
    
    Pokud zadáte `$locations` v tomto okamžiku se zobrazí názvy umístění bez dalších informací vrácený Get-AzureRmLocation.
1. Vytvoření profilu protokolu Azure Resource Manageru: 
    
    ```Add-AzureRmLogProfile -Name $name -ServiceBusRuleId $sbruleid -Locations $locations```
    
    Další informace o profilu protokolů Azure najdete v části [přehled protokolu aktivit Azure](../azure-monitor/platform/activity-logs-overview.md).

> [!NOTE]
> Při pokusu o vytvoření profilu protokolu, může se zobrazit chybová zpráva. Potom můžete zkontrolovat v dokumentaci pro Get-AzureRmLogProfile a Remove-AzureRmLogProfile. Pokud spustíte Get AzureRmLogProfile, se zobrazí informace o profilu protokolu. Můžete odstranit stávající profilu protokolu zadáním ```Remove-AzureRmLogProfile -name 'Log Profile Name' ``` příkazu.
>
>![Chyba profilu Resource Manageru](./media/security-azure-log-integration-keyvault-eventhub/rm-profile-error.png)

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

1. Vytvoření služby key vault:

   ```$kv = New-AzureRmKeyVault -VaultName $name -ResourceGroupName $rgname -Location $location ```

1. Konfigurace protokolování pro key vault:

   ```Set-AzureRmDiagnosticSetting -ResourceId $kv.ResourceId -ServiceBusRuleId $sbruleid -Enabled $true ```

## <a name="generate-log-activity"></a>Vygenerování protokolu aktivit

Požadavky nutné k odeslání do služby Key Vault k vygenerování protokolu aktivit. Akce, jako jsou generování klíče, tajné kódy, ukládání nebo čtení tajných kódů ze služby Key Vault vytvoří záznamy protokolu.

1. Zobrazení aktuálního úložiště klíčů:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Generovat nový **key2**:
    
   ```New-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname -KeyName key2```
1. Znovu zobrazit klíče a Všimněte si, že **key2** obsahuje jiné hodnoty:
    
   ```Get-AzureRmStorageAccountKey -Name $storagename -ResourceGroupName $rgname  | ft -a```
1. Nastavení a čtení tajného klíče ke generování dalších protokolů položky:
    
   a. ```Set-AzureKeyVaultSecret -VaultName $name -Name TestSecret -SecretValue (ConvertTo-SecureString -String 'Hi There!' -AsPlainText -Force)``` b. ```(Get-AzureKeyVaultSecret -VaultName $name -Name TestSecret).SecretValueText```

   ![Vrátí tajného kódu](./media/security-azure-log-integration-keyvault-eventhub/keyvaultsecret.png)


## <a name="configure-azure-log-integration"></a>Konfigurace integrace protokolů Azure

Teď, když jste nakonfigurovali všechny požadované prvky mít protokolování do centra událostí služby Key Vault, budete muset nakonfigurovat integraci protokolů Azure:

1. ```$storage = Get-AzureRmStorageAccount -ResourceGroupName $rgname -Name $storagename```
1. ```$eventHubKey = Get-AzureRmEventHubNamespaceKey -ResourceGroupName $rgname -NamespaceName $eventHubNamespace.name -AuthorizationRuleName RootManageSharedAccessKey```
1. ```$storagekeys = Get-AzureRmStorageAccountKey -ResourceGroupName $rgname -Name $storagename```
1. ``` $storagekey = $storagekeys[0].Value```

Spusťte příkaz AzLog pro každé Centrum událostí:

1. ```$eventhubs = Get-AzureRmEventHub -ResourceGroupName $rgname -NamespaceName $eventHubNamespaceName```
1. ```$eventhubs.Name | %{Add-AzLogEventSource -Name $sub' - '$_ -StorageAccount $storage.StorageAccountName -StorageKey $storageKey -EventHubConnectionString $eventHubKey.PrimaryConnectionString -EventHubName $_}```

Po minutě vypršet z posledních dvou příkazů měli byste vidět generovaných souborů JSON. Ověřte, že při sledování adresáři **C:\users\AzLog\EventHubJson**.

## <a name="next-steps"></a>Další postup

- [Integrace protokolů Azure – nejčastější dotazy](security-azure-log-integration-faq.md)
- [Začínáme s integrací protokolů Azure](security-azure-log-integration-get-started.md)
- [Integrace protokolů z prostředků Azure do systémů SIEM](security-azure-log-integration-overview.md)
