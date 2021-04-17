---
title: Azure COMPUTE – diagnostické rozšíření Linux 3,0
description: Jak nakonfigurovat diagnostické rozšíření Azure Linux (LAD) 3,0 pro shromažďování metrik a protokolování událostí z virtuálních počítačů se systémem Linux, které jsou spuštěny v Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.openlocfilehash: fe03bbfb33f3637eecc4e68f24846c929dad5fa4
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479249"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Monitorování metrik a protokolů pomocí diagnostického rozšíření Linux 3,0

Tento dokument popisuje verzi 3,0 a novější rozšíření pro diagnostiku Linux (LAD).

> [!IMPORTANT]
> Informace o verzi 2,3 a starších verzích najdete v tématu [monitorování výkonu a diagnostických dat virtuálního počítače se systémem Linux](https://docs.microsoft.com/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Úvod

Diagnostické rozšíření pro Linux pomáhá uživateli monitorovat stav virtuálního počítače se systémem Linux, který běží na Microsoft Azure. Má následující možnosti:

* Shromažďuje z virtuálního počítače metriky výkonu systému a ukládá je do konkrétní tabulky v určeném účtu úložiště.
* Načte události protokolu z syslog a uloží je do konkrétní tabulky v určeném účtu úložiště.
* Umožňuje uživatelům přizpůsobit metriky dat, které se shromáždí a nahrají.
* Umožňuje uživatelům přizpůsobit zařízení syslog a úrovně závažnosti shromažďovaných a odesílaných událostí.
* Umožňuje uživatelům odeslat zadané soubory protokolu do určené tabulky úložiště.
* Podporuje odesílání metrik a protokolování událostí do libovolných koncových bodů Azure Event Hubs a objektů BLOB ve formátu JSON v určeném účtu úložiště.

Toto rozšíření funguje v obou modelech nasazení Azure.

## <a name="install-the-extension-on-a-vm"></a>Instalace rozšíření na virtuální počítač

Rozšíření můžete povolit pomocí rutin Azure PowerShell, skriptů Azure CLI, šablon Azure Sledování prostředků (šablon ARM) nebo Azure Portal. Další informace najdete v tématu [funkce rozšíření](features-linux.md).

>[!NOTE]
>Některé součásti rozšíření virtuálního počítače LAD jsou také dodány v [rozšíření Log Analytics VM](./oms-linux.md). Z důvodu této architektury mohou konflikty nastat, pokud jsou obě rozšíření vytvořena ve stejné šabloně ARM. 
>
>Aby nedocházelo ke konfliktům při instalaci, použijte [ `dependsOn` direktivu](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) , abyste zajistili, že se rozšíření nainstalují sekvenčně. Rozšíření lze instalovat v libovolném pořadí.

Tyto pokyny k instalaci a [Ukázková konfigurace ke stažení](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) umožňují nakonfigurovat lad 3,0 na:

* Zachyťte a uložte stejné metriky jako v LAD 2,3.
* Zachytit užitečnou sadu metrik systému souborů. Tato funkce je v LAD 3,0 novinkou.
* Zachyťte výchozí kolekci syslog, kterou povoluje LAD 2,3.
* Umožněte Azure Portal prostředí pro vytváření grafů a upozorňování na metriky virtuálních počítačů.

Konfigurace ke stažení je pouze příklad. Upravte ji tak, aby vyhovovala vašim potřebám.

### <a name="supported-linux-distributions"></a>Podporované distribuce systému Linux

LAD podporuje následující distribuce a verze. Seznam distribucí a verzí se týká jenom imagí dodavatele systému Linux schváleného systémem. Toto rozšíření obecně nepodporuje Image BYOL a BYOS třetích stran, jako jsou zařízení.

Distribuce, která obsahuje pouze hlavní verze, například Debian 7, je podporována také pro všechny podverze. Pokud je zadána dílčí verze, je podporována pouze tato verze. Pokud je připojeno znaménko plus (+), jsou podporovány nižší verze, které jsou stejné nebo vyšší než zadaná verze.

Podporované distribuce a verze:

- Ubuntu 18,04, 16,04, 14,04
- CentOS 7, 6.5 +
- Oracle Linux 7, 6.4 +
- OpenSUSE 13.1 +
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7 +

### <a name="prerequisites"></a>Požadavky

* **Agent Azure Linux verze 2.2.0 nebo novější**. Většina imagí z Galerie virtuálních počítačů Azure pro Linux zahrnuje verzi 2.2.7 nebo novější. Spusťte `/usr/sbin/waagent -version` a potvrďte verzi nainstalovanou na virtuálním počítači. Pokud na virtuálním počítači běží starší verze, [aktualizujte agenta hosta](./update-linux-agent.md).
* Rozhraní příkazového **řádku Azure** V případě potřeby nastavte na svém počítači prostředí [Azure CLI](/cli/azure/install-azure-cli) .
* **Příkaz wget** Pokud ho ještě nemáte, spusťte příkaz `sudo apt-get install wget` .
* Existující **předplatné Azure**. 
* Existující **účet úložiště pro obecné účely** pro ukládání dat. Účty úložiště pro obecné účely musí podporovat úložiště tabulek. Účet úložiště BLOB nebude fungovat.
* **Python 2**.

### <a name="python-requirement"></a>Požadavek Pythonu

Diagnostické rozšíření pro Linux vyžaduje Python 2. Pokud váš virtuální počítač používá distribuci, která ve výchozím nastavení neobsahuje Python 2, musíte ho nainstalovat. Následující vzorové příkazy instalují Python 2 v různých distribucích:   

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SUSE `zypper install -y python2`

`python2`Spustitelný soubor musí mít alias na *Python*. Tady je jedna metoda pro nastavení tohoto aliasu:

1. Spuštěním následujícího příkazu odeberte všechny existující aliasy.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Spusťte následující příkaz pro vytvoření aliasu.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Ukázková instalace

Ukázková konfigurace stažená v následujících příkladech shromažďuje sadu standardních dat a odesílá je do tabulkového úložiště. Adresa URL pro ukázkovou konfiguraci a její obsah se může změnit. 

Ve většině případů byste si měli stáhnout kopii souboru JSON s nastavením portálu a přizpůsobit ho podle svých potřeb. Pak použijte šablony nebo vlastní automatizaci k použití přizpůsobené verze konfiguračního souboru místo stahování z adresy URL pokaždé, když.

> [!NOTE]
> V následujících ukázkách Vyplňte správné hodnoty proměnných v první části před spuštěním kódu. 
#### <a name="azure-cli-sample"></a>Ukázka Azure CLI

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-to-install-lad-30-on-the-virtual-machine-scale-set-instance"></a>Ukázka Azure CLI pro instalaci LAD 3,0 na instanci sady škálování virtuálního počítače

```azurecli
#Set your Azure Virtual Machine Scale Sets diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>Ukázka PowerShellu

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="update-the-extension-settings"></a>Aktualizace nastavení rozšíření

Když změníte chráněné nebo veřejné nastavení, nasaďte je do virtuálního počítače spuštěním stejného příkazu. V případě změny nastavení se aktualizace odesílají do rozšíření. LAD znovu načte konfiguraci a sám se restartuje.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Migrace z předchozích verzí rozšíření

Nejnovější verze rozšíření je *4,0*. 

> [!IMPORTANT]
> Toto rozšíření přináší zásadní změny konfigurace. Tato změna vylepšuje zabezpečení rozšíření, takže zpětná kompatibilita se 2. x se nedá zachovat. Navíc se Vydavatel rozšíření pro toto rozšíření liší od vydavatele pro verze 2. x.
>
> Pokud chcete provést migraci z 2. x na novou verzi, nejdřív Odinstalujte starou příponu (pod starým názvem vydavatele). Pak nainstalujte verzi 3.

Doporučit

* Nainstalujte rozšíření s povoleným automatickým upgradem dílčí verze.
  * V případě virtuálních počítačů modelu nasazení Classic zadejte verzi, `3.*` Pokud instalujete rozšíření prostřednictvím rozhraní příkazového řádku Azure XPLAT CLI nebo PowerShellu.
  * Na Azure Resource Manager virtuálních počítačů modelu nasazení zahrňte `"autoUpgradeMinorVersion": true` do šablony nasazení virtuálních počítačů.
* Pro LAD 3,0 použijte nový nebo jiný účet úložiště. LAD 2,3 a LAD 3,0 mají několik malých nekompatibilit, které umožňují sdílení účtu komplikované:
  * LAD 3,0 ukládá události syslog v tabulce, která má jiný název.
  * `counterSpecifier`Řetězce pro `builtin` metriky se v lad 3,0 liší.

## <a name="protected-settings"></a>Chráněná nastavení

Tato sada informací o konfiguraci obsahuje citlivé informace, které by měly být chráněny před veřejným zobrazením. Obsahuje například přihlašovací údaje úložiště. Tato nastavení se přenášejí do a ukládají rozšíření v šifrované podobě.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name | Hodnota
---- | -----
storageAccountName | Název účtu úložiště, ve kterém se má rozšíření zapsat data
storageAccountEndPoint | Volitelné Koncový bod, který identifikuje Cloud, ve kterém existuje účet úložiště. Pokud toto nastavení chybí, LAD výchozím nastavením je veřejný cloud Azure, `https://core.windows.net` . Pokud chcete použít účet úložiště v Azure Německo, Azure Government nebo Azure Čína 21Vianet, nastavte tuto hodnotu podle potřeby.
storageAccountSasToken | [Token SAS účtu](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) pro objekty BLOB a Table Services ( `ss='bt'` ). Vztahuje se na kontejnery a objekty ( `srt='co'` ). Uděluje oprávnění přidat, vytvořit, zobrazit, aktualizovat a zapsat ( `sp='acluw'` ). Nezahrnujte *úvodní* otazník (?).
mdsdHttpProxy | Volitelné Informace o proxy serveru HTTP, které rozšíření potřebuje k připojení k zadanému účtu úložiště a koncovému bodu
sinksConfig | Volitelné Podrobnosti o alternativních umístěních, na které se dají doručovat metriky a události Následující části obsahují podrobnosti o jednotlivých datových jímkach podporovaných rozšířením.

K získání tokenu SAS v rámci šablony ARM použijte `listAccountSas` funkci. Příklad šablony naleznete v tématu [Příklad funkce list](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Požadovaný token SAS můžete vytvořit prostřednictvím Azure Portal:

1. Vyberte účet úložiště pro obecné účely, na který chcete rozšíření zapisovat.
1. V nabídce na levé straně v části **Nastavení** vyberte **podpis sdíleného přístupu**.
1. Proveďte výběr podle výše popsaného postupu.
1. Vyberte **Generovat SAS**.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Snímek obrazovky se stránkou sdíleného přístupového podpisu zobrazí tlačítko generovat S A S.":::

Zkopírujte vygenerované SAS do `storageAccountSasToken` pole. Odeberte úvodní otazník (?).

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

`sinksConfig`Volitelný oddíl definuje více umístění, do kterých rozšíření odesílá informace, které shromažďuje. `sink`Pole obsahuje objekt pro každou další datovou jímku. `type`Atribut určuje ostatní atributy v objektu.

Prvek | Hodnota
------- | -----
name | Řetězec, který odkazuje na tuto jímka jinde v konfiguraci rozšíření.
typ | Typ definované jímky. Určuje další hodnoty (pokud existují) v instancích tohoto typu.

LAD verze 3,0 podporuje dva typy jímky: `EventHub` a `JsonBlob` .

#### <a name="eventhub-sink"></a>Jímka EventHub

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

`"sasURL"`Položka obsahuje úplnou adresu URL, včetně tokenu SAS, pro centrum událostí, na které se mají data publikovat. LAD vyžaduje SAS pro pojmenování zásady, která povoluje deklaraci identity Send. 

Například:

* Vytvořte obor názvů Azure Event Hubs s názvem `contosohub` .
* Vytvořte centrum událostí v oboru názvů s názvem `syslogmsgs` .
* Vytvořte zásadu sdíleného přístupu v centru událostí, která umožňuje odeslat deklaraci identity. Pojmenujte zásadu `writer` .

Pokud je vaše SAS vhodné do půlnoci od 1. ledna 2018, hodnota sasURL může být podobný jako v tomto příkladu:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Další informace o generování a načítání informací o tokenech SAS pro Event Hubs najdete v tématu [generování tokenu SAS](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="jsonblob-sink"></a>Jímka JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Data, která se přesměrují do `JsonBlob` jímky, se ukládají v objektech blob v Azure Storage. Každá instance LAD vytváří objekt BLOB každou hodinu pro každý název jímky. Každý objekt BLOB vždy obsahuje syntakticky platné pole JSON objektů. Nové položky jsou atomicky přidány do pole. 

Objekty blob jsou uloženy v kontejneru, který má stejný název jako jímka. Pravidla Azure Storage pro názvy kontejnerů objektů BLOB se vztahují na názvy `JsonBlob` umyvadel. Název musí mít 3 až 63 malých alfanumerických znaků ASCII nebo pomlčky.

## <a name="public-settings"></a>Veřejné nastavení

Struktura veřejných nastavení obsahuje různé bloky nastavení, které řídí informace, které rozšíření shromažďuje. Každé nastavení je volitelné. Pokud zadáte `ladCfg` , je nutné zadat také `StorageAccount` .

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Prvek | Hodnota
------- | -----
StorageAccount | Název účtu úložiště, ve kterém se má rozšíření zapsat data Musí se jednat o název zadaný v [Nastavení Protected](#protected-settings).
mdsdHttpProxy | Volitelné Stejné jako v [chráněných nastaveních](#protected-settings). Veřejná hodnota je přepsána soukromou hodnotou, pokud je nastavena. V [chráněných nastaveních](#protected-settings)umístěte nastavení proxy serveru, jako je třeba heslo.

Následující části obsahují podrobnosti o zbývajících prvcích.

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

`ladCfg`Struktura je volitelná. Řídí shromažďování metrik a protokolů dodaných službě Azure Monitor metriky a dalším datovým jímkam. Je nutné zadat:

* Buď `performanceCounters` nebo `syslogEvents` obojí. 
* `metrics`Struktura.

Prvek | Hodnota
------- | -----
eventVolume | Volitelné Určuje počet oddílů vytvořených v rámci tabulky úložiště. Musí to být `"Large"` , `"Medium"` , nebo `"Small"` . Pokud hodnota není zadaná, výchozí hodnota je `"Medium"` .
sampleRateInSeconds | Volitelné Výchozí interval mezi kolekcemi nezpracovaných (neagregovaných) metrik. Nejmenší podporovaná vzorkovací frekvence je 15 sekund. Pokud hodnota není zadaná, je výchozí hodnota `15` .

#### <a name="metrics"></a>metriky

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

Prvek | Hodnota
------- | -----
resourceId | ID prostředku Azure Resource Manager virtuálního počítače nebo sady škálování, ke které patří virtuální počítač. Toto nastavení musí být zadáno také v případě, že `JsonBlob` se v konfiguraci používá nějaká jímka.
scheduledTransferPeriod | Frekvence, s níž jsou počítány agregované metriky a přeneseny do Azure Monitor metriky. Frekvence se vyjadřuje jako časový interval 8601. Nejmenší perioda přenosu je 60 sekund, tj. PT1M. Zadejte aspoň jednu `scheduledTransferPeriod` .

Ukázky metriky uvedené v `performanceCounters` oddílu se shromažďují každých 15 sekund nebo v vzorkovací frekvenci, která je pro čítač explicitně definovaná. Pokud `scheduledTransferPeriod` se zobrazí více frekvencí, jako v příkladu, je každá agregace vypočítána nezávisle.

#### <a name="performancecounters"></a>Čítače výkonu

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

`performanceCounters`Volitelný oddíl řídí kolekci metrik. Nezpracované vzorky jsou agregované pro každý [`scheduledTransferPeriod`](#metrics) , aby se vytvořily tyto hodnoty:

* Mean
* Minimum
* Maximum
* Poslední shromážděná hodnota
* Počet nezpracovaných vzorků používaných k výpočtu agregace

Prvek | Hodnota
------- | -----
jímky | Volitelné Čárkami oddělený seznam názvů umyvadel, na které LAD odesílá agregované výsledky metriky. Všechny agregované metriky jsou publikovány v každé uvedené jímky. Příklad: `"EHsink1, myjsonsink"`. Další informace najdete na webu [`sinksConfig`](#sinksconfig).
typ | Určuje skutečného poskytovatele metriky.
class | Společně s `"counter"` identifikuje konkrétní metriku v oboru názvů poskytovatele.
counter | Společně s `"class"` identifikuje konkrétní metriku v oboru názvů poskytovatele.
counterSpecifier | Určuje specifickou metriku v oboru názvů Azure Monitorch metrik.
pomocné | Volitelné Vybere konkrétní instanci objektu, na kterou se metrika vztahuje. Nebo vybírá agregaci napříč všemi instancemi daného objektu. 
sampleRate | Interval je 8601, který nastavuje rychlost shromažďování nezpracovaných vzorků pro tuto metriku. Pokud hodnota není nastavena, interval shromažďování je nastaven hodnotou [`sampleRateInSeconds`](#ladcfg) . Nejkratší podporovaná vzorkovací frekvence je 15 sekund (PT15S).
unit | Definuje jednotku pro metriku. By měl být jeden z těchto řetězců: `"Count"` , `"Bytes"` , `"Seconds"` , `"Percent"` , `"CountPerSecond"` , `"BytesPerSecond"` , `"Millisecond"` . Spotřebitelé shromážděných dat očekávají, že hodnoty shromážděných dat odpovídají této jednotce. LAD ignoruje toto pole.
displayName | Popisek, který se má připojit k datům v Azure Monitor metriky. Tento popisek je v jazyce určeném pomocí přidruženého nastavení národního prostředí. LAD ignoruje toto pole.

`counterSpecifier`Je libovolný identifikátor. Příjemci metrik, jako Azure Portal je například funkce pro vytváření grafů a upozorňování, slouží `counterSpecifier` jako "klíč", který identifikuje metriku nebo instanci metriky. 

Pro `builtin` metriky doporučujeme `counterSpecifier` hodnoty, které začínají na `/builtin/` . Pokud shromažďujete konkrétní instanci metriky, doporučujeme připojit identifikátor instance k `counterSpecifier` hodnotě. 

Tady je několik příkladů:

* `/builtin/Processor/PercentIdleTime` -Průměrná doba nečinnosti napříč všemi vCPU
* `/builtin/Disk/FreeSpace(/mnt)` – Volné místo pro `/mnt` systém souborů
* `/builtin/Disk/FreeSpace` – Průměrné místo v rámci všech připojených systémů souborů

LAD a Azure Portal neočekávají, že `counterSpecifier` hodnota odpovídá jakémukoli vzoru. Být konzistentní při sestavování `counterSpecifier` hodnot.

Když zadáte `performanceCounters` , lad vždy zapisuje data do tabulky v Azure Storage. Stejná data lze zapsat do objektů BLOB JSON nebo Event Hubs nebo obojího. Nemůžete ale zakázat ukládání dat do tabulky. 

Všechny instance LAD, které používají stejný název a koncový bod účtu úložiště, přidají ke stejné tabulce své metriky a protokoly. Pokud se do stejného oddílu tabulky zapisuje příliš mnoho virtuálních počítačů, může Azure omezit zápisy do tohoto oddílu. 

`eventVolume`Nastavení způsobí, že položky budou rozloženy mezi 1 (malý), 10 (střední) nebo 100 (velké) oddíly. Obvykle jsou střední oddíly dostačující k zamezení omezení provozu. 

Funkce metrik Azure Monitor Azure Portal používá data v této tabulce k vytváření grafů nebo k aktivaci výstrah. Název tabulky je zřetězení těchto řetězců:

* `WADMetrics`
* `"scheduledTransferPeriod"`Pro agregované hodnoty uložené v tabulce
* `P10DV2S`
* Datum ve formátu "RRRRMMDD", které se mění každých 10 dní

Příklady zahrnují `WADMetricsPT1HP10DV2S20170410` a `WADMetricsPT1MP10DV2S20170609` .

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

`syslogEvents`Volitelný oddíl řídí shromažďování událostí protokolu z syslog. Pokud je oddíl vynechán, události syslog nejsou zachyceny vůbec.

`syslogEventConfiguration`Kolekce má jednu položku pro každé zařízení syslog, které vás zajímá. Pokud `minSeverity` je `"NONE"` pro konkrétní zařízení, nebo pokud se toto zařízení v prvku vůbec neobjeví, nebudou zachyceny žádné události z tohoto zařízení.

Prvek | Hodnota
------- | -----
jímky | Čárkami oddělený seznam názvů umyvadel, na které se jednotlivé události protokolu publikují. Všechny události protokolu, které odpovídají omezením v, `syslogEventConfiguration` se publikují do každé uvedené jímky. Příklad: `"EHforsyslog"`
facilityName | Název zařízení syslog, například `"LOG_USER"` nebo `"LOG\LOCAL0"` . Další informace najdete v části "zařízení" na [stránce zachycení služby SYSLOG](http://man7.org/linux/man-pages/man3/syslog.3.html).
minSeverity | Úroveň závažnosti syslog, například `"LOG_ERR"` nebo `"LOG_INFO"` . Další informace najdete v části "úroveň" [stránky zachycení služby SYSLOG](http://man7.org/linux/man-pages/man3/syslog.3.html). Rozšíření zachytí události odesílané do zařízení na zadané úrovni nebo nad ní.

Když zadáte `syslogEvents` , lad vždy zapisuje data do tabulky v Azure Storage. Stejná data lze zapsat do objektů BLOB JSON nebo Event Hubs nebo obojího. Nemůžete ale zakázat ukládání dat do tabulky. 

Chování dělení tabulky je stejné, jak je popsáno v `performanceCounters` . Název tabulky je zřetězení těchto řetězců:

* `LinuxSyslog`
* Datum ve formátu "RRRRMMDD", které se mění každých 10 dní

Příklady zahrnují `LinuxSyslog20170410` a `LinuxSyslog20170609` .

### <a name="perfcfg"></a>perfCfg

`perfCfg`Oddíl je nepovinný. Řídí spouštění libovolných dotazů [OMI (Open Management Infrastructure)](https://github.com/Microsoft/omi) .

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

Prvek | Hodnota
------- | -----
namespace | Volitelné Obor názvů OMI, ve kterém se má dotaz spustit Je-li tento parametr zadán, je použita výchozí hodnota `"root/scx"` . Je implementována [poskytovateli pro různé platformy pro System Center](https://github.com/Microsoft/SCXcore).
query | Dotaz OMI, který se má spustit.
tabulka | Volitelné Tabulka Azure Storage v určeném účtu úložiště Další informace najdete v tématu [chráněná nastavení](#protected-settings).
frequency | Volitelné Počet sekund mezi spuštěním dotazu. Výchozí hodnota je 300 (5 minut). Minimální hodnota je 15 sekund.
jímky | Volitelné Čárkami oddělený seznam názvů více umyvadel, na které by měly být publikovány nezpracované ukázkové výsledky metriky. Žádná agregace těchto nezpracovaných vzorků není vypočítána rozšířením nebo Azure Monitormi metrikami.

`"table"` `"sinks"` Musí být zadán buď nebo obojí.

### <a name="filelogs"></a>Protokoly

`fileLogs`Oddíl řídí zachycení souborů protokolu. LAD zachycuje nové textové řádky při jejich zápisu do souboru. Zapisuje je do řádků tabulky nebo do všech zadaných umyvadel ( `JsonBlob` nebo `EventHub` ).

> [!NOTE]
> `fileLogs`Jsou zachyceny dílčí komponentou lad s názvem `omsagent` . Aby bylo možné shromáždit `fileLogs` informace, ujistěte se, že `omsagent` uživatel má oprávnění ke čtení pro soubory, které zadáte. Uživatel musí mít také oprávnění EXECUTE pro všechny adresáře v cestě k tomuto souboru. Po instalaci nástroje LAD můžete kontrolovat oprávnění spuštěním `sudo su omsagent -c 'cat /path/to/file'` .

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Prvek | Hodnota
------- | -----
 – soubor | Úplný název cesty k souboru protokolu, který se má sledovat a zachytit. Název cesty musí pojmenovat jeden soubor. Nemůže obsahovat název adresáře ani zástupné znaky. `omsagent`Uživatelský účet musí mít k této cestě k souboru přístup pro čtení.
tabulka | Volitelné Tabulka Azure Storage, do které se zapisují nové řádky ze "konce" souboru. Tabulka musí být v určeném účtu úložiště, jak je uvedeno v části chráněná konfigurace. 
jímky | Volitelné Čárkami oddělený seznam názvů dalších umyvadel, na které se odesílají řádky protokolu.

`"table"` `"sinks"` Musí být zadán buď nebo, nebo obojí.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metriky podporované zprostředkovatelem Builtin

`builtin`Zprostředkovatel metriky je zdrojem metrik, které jsou pro širokou škálu uživatelů nejzajímavější. Tyto metriky spadají do pěti širších tříd:

* Procesor
* Memory (Paměť)
* Síť
* Systém souborů
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>předdefinované metriky pro třídu procesoru

Třída procesoru metrik nabízí informace o využití procesoru ve virtuálním počítači. V případě agregace procent je výsledkem průměr ve všech procesorech. 

Pokud je v vCPUm virtuálním počítači jeden 100 vCPU zaneprázdněný a druhý je 100% nečinný, nahlášený `PercentIdleTime` je 50. Pokud 50 je každý vCPU zaneprázdněný pro stejné období, nahlášený výsledek je také 50. Pokud 100 je v vCPU virtuálním počítači vCPU a jiné nečinné, je nahlášená hodnota `PercentIdleTime` 75.

Čítač | Význam
------- | -------
PercentIdleTime | Procentuální podíl času během okna agregace, po kterém procesor spustil smyčku nečinného jádra
PercentProcessorTime | Procento času spuštění vlákna, které není nečinné
PercentIOWaitTime | Procento času čekání na dokončení vstupně-výstupních operací
PercentInterruptTime | Procento času spuštění hardwarového nebo softwarového přerušení a DPC (odložená volání procedur)
PercentUserTime | V době nečinnosti během okna agregace je procento času stráveného v uživatelském režimu s normální prioritou.
PercentNiceTime | Nečinný čas, procento strávené za sníženou prioritou (Nice)
PercentPrivilegedTime | Nečinný čas, procento strávené v privilegovaném režimu (kernel)

První čtyři čítače by měly být součtem 100 procent. Poslední tři čítače jsou také součtem 100 procent. Tyto tři čítače rozdělují součet hodnot `PercentProcessorTime` , `PercentIOWaitTime` a `PercentInterruptTime` .

Pro agregaci jedné metriky napříč všemi procesory nastavte `"condition": "IsAggregate=TRUE"` . Pokud chcete získat metriku pro konkrétní procesor, jako je druhý logický procesor se čtyřmi vCPU virtuálními počítači, nastavte `"condition": "Name=\\"1\\""` . Čísla logických procesorů jsou v rozsahu `[0..n-1]` .

### <a name="builtin-metrics-for-the-memory-class"></a>předdefinované metriky pro třídu paměti

Třída Memory metriky poskytuje informace o využití paměti, stránkování a prohození.

Čítač | Význam
------- | -------
AvailableMemory | Dostupná fyzická paměť v databázi MiB
PercentAvailableMemory | Dostupná fyzická paměť jako procento z celkové paměti
UsedMemory | Využití fyzické paměti (MiB)
PercentUsedMemory | Použít fyzickou paměť jako procento z celkové paměti
PagesPerSec | Celkový počet stránkování (čtení a zápis)
PagesReadPerSec | Stránky čtené ze záložního úložiště, například odkládací soubor, soubor programu a mapovaný soubor
PagesWrittenPerSec | Stránky zapsané do záložního úložiště, jako je například odkládací soubor a mapovaný soubor
AvailableSwap | Nevyužité místo odkládacího souboru (MiB)
PercentAvailableSwap | Nevyužité místo odkládacího souboru jako procento z celkového zahození
UsedSwap | Odkládací místo (MiB) v aplikaci
PercentUsedSwap | Použít místo odkládacího souboru jako procento z celkového zahození

Tato třída metrik má pouze jednu instanci. `"condition"`Atribut nemá žádná užitečná nastavení a měla by být vynechán.

### <a name="builtin-metrics-for-the-network-class"></a>předdefinované metriky pro třídu sítě

Třída Network metriky poskytuje informace o aktivitě sítě v jednotlivých síťových rozhraních od spuštění. 

LAD nevystavuje metriky šířky pásma. Tyto metriky můžete získat z metrik hostitele.

Čítač | Význam
------- | -------
BytesTransmitted | Celkový počet odeslaných bajtů od spuštění
BytesReceived | Celkový počet přijatých bajtů od spuštění
BytesTotal | Celkový počet odeslaných nebo přijatých bajtů od spuštění
PacketsTransmitted | Celkový počet odeslaných paketů od spuštění
PacketsReceived | Celkový počet přijatých paketů od spuštění
TotalRxErrors | Počet chyb přijetí od spuštění
TotalTxErrors | Počet chyb odeslání od spuštění
TotalCollisions | Počet kolizí hlášených síťovými porty od spuštění

I když je třída sítě instance, LAD nepodporuje zachycení síťových metrik agregovaných napříč všemi síťovými zařízeními. Chcete-li získat metriky pro určité rozhraní, například eth0, nastavte `"condition": "InstanceID=\\"eth0\\""` .

### <a name="builtin-metrics-for-the-file-system-class"></a>předdefinované metriky pro třídu systému souborů

Třída systému souborů metrik nabízí informace o využití systému souborů. Absolutní a procentuální hodnoty jsou hlášeny tak, jak by se zobrazily běžnému uživateli (ne kořenovému).

Čítač | Význam
------- | -------
FreeSpace | Volné místo na disku v bajtech
UsedSpace | Využité místo na disku v bajtech
PercentFreeSpace | Procento volného místa
PercentUsedSpace | Procento využitého místa
PercentFreeInodes | Procento nepoužívaných indexovaných uzlů (uzlů inode)
PercentUsedInodes | Procento přiděleného (používaného) uzlů inode v rámci všech systémů souborů
BytesReadPerSecond | Přečtené bajty za sekundu
BytesWrittenPerSecond | Zapsané bajty za sekundu
BytesPerSecond | Přečtené nebo zapsané bajty za sekundu
ReadsPerSecond | Operace čtení za sekundu
WritesPerSecond | Operace zápisu za sekundu
TransfersPerSecond | Operace čtení nebo zápisu za sekundu

Agregované hodnoty můžete získat napříč všemi systémy souborů nastavením `"condition": "IsAggregate=True"` . Získejte hodnoty pro určitý připojený systém souborů, jako je například `"/mnt"` , nastavením `"condition": 'Name="/mnt"'` . 

> [!NOTE]
> Pokud pracujete v Azure Portal místo JSON, formulář pole podmínka je `Name='/mnt'` .

### <a name="builtin-metrics-for-the-disk-class"></a>předdefinované metriky pro třídu disku

Disková třída metrik nabízí informace o využití diskového zařízení. Tyto statistiky se vztahují na celou jednotku. 

Pokud má zařízení více systémů souborů, jsou čítače pro toto zařízení efektivně agregované napříč všemi systémy souborů.

Čítač | Význam
------- | -------
ReadsPerSecond | Operace čtení za sekundu
WritesPerSecond | Operace zápisu za sekundu
TransfersPerSecond | Celkem operací za sekundu
AverageReadTime | Průměrný počet sekund na operaci čtení
AverageWriteTime | Průměrný počet sekund na operaci zápisu
AverageTransferTime | Průměrný počet sekund na operaci
AverageDiskQueueLength | Průměrný počet operací disku zařazených do fronty
ReadBytesPerSecond | Počet přečtených bajtů za sekundu
WriteBytesPerSecond | Počet zapsaných bajtů za sekundu
BytesPerSecond | Počet přečtených nebo zapsaných bajtů za sekundu

Agregované hodnoty můžete získat u všech disků nastavením `"condition": "IsAggregate=True"` . Chcete-li získat informace o konkrétním zařízení (například `/dev/sdf1` ), nastavte `"condition": "Name=\\"/dev/sdf1\\""` .

## <a name="install-and-configure-lad-30"></a>Instalace a konfigurace LAD 3,0

### <a name="azure-cli"></a>Azure CLI

Pokud jsou vaše chráněná nastavení v souboru *ProtectedSettings.jsna* a informace o vaší veřejné konfiguraci jsou v *PublicSettings.jsna*, spusťte následující příkaz.

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Příkaz předpokládá, že používáte režim Azure Resource Manager rozhraní příkazového řádku Azure CLI. Pokud chcete nakonfigurovat LAD pro virtuální počítače modelu nasazení Classic, přepněte do režimu ASM ( `azure config mode asm` ) a vynechejte název skupiny prostředků v příkazu. 

Další informace najdete v dokumentaci k rozhraní příkazového [řádku pro více platforem](/cli/azure/authenticate-azure-cli).

### <a name="powershell"></a>PowerShell

Pokud jsou vaše chráněná nastavení v `$protectedSettings` proměnné a informace o veřejné konfiguraci jsou v `$publicSettings` proměnné, spusťte tento příkaz:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="example-lad-30-configuration"></a>Příklad konfigurace LAD 3,0

V závislosti na předchozích definicích poskytuje tato část ukázkovou konfiguraci rozšíření LAD 3,0 a některé vysvětlení. Pokud chcete tuto ukázku použít pro váš případ, použijte vlastní název účtu úložiště, token SAS účtu a Event Hubs tokeny SAS.

> [!NOTE]
> V závislosti na tom, jestli k instalaci LAD používáte Azure CLI nebo PowerShell, se metoda pro zajištění veřejného a chráněného nastavení liší: 
>
> * Pokud používáte rozhraní příkazového řádku Azure CLI, uložte následující nastavení, abyste *ProtectedSettings.jsi na* a *PublicSettings.jsna* používání předchozího ukázkového příkazu. 
> * Pokud používáte PowerShell, uložte následující nastavení do `$protectedSettings` a a `$publicSettings` Spusťte `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Chráněná nastavení

Konfigurace chráněných nastavení:

* Účet úložiště.
* Token SAS odpovídajícího účtu.
* Několik umyvadel ( `JsonBlob` nebo `EventHub` s tokeny SAS).

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>Veřejné nastavení

Veřejné nastavení způsobí, že LAD:

* Nahrajte metriky v procentech času procesoru a využité místo na disku v `WADMetrics*` tabulce.
* Nahrajte zprávy ze zařízení syslog `"user"` a závažnost `"info"` do `LinuxSyslog*` tabulky.
* Nahrání nezpracovaných výsledků dotazu OMI ( `PercentProcessorTime` a `PercentIdleTime` ) do pojmenované `LinuxCPU` tabulky
* Nahrajte připojené řádky do souboru `/var/log/myladtestlog` do `MyLadTestLog` tabulky.

V každém případě se data nahrají taky do:

* Blob Storage Azure. Název kontejneru je definovaný v `JsonBlob` jímky.
* Koncový bod Event Hubs, jak je uvedeno v `EventHub` jímky.

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

`resourceId`V konfiguraci se musí shodovat s konfigurací virtuálního počítače nebo sady škálování virtuálních počítačů.

* Grafy metrik a výstrahy na platformě Azure ví `resourceId` o virtuálním počítači, na kterém pracujete. Očekává, že se data pro váš virtuální počítač vyhledají pomocí `resourceId` vyhledávacího klíče.
* Pokud používáte automatické škálování Azure, musí se `resourceId` v konfiguraci automatického škálování shodovat s tím `resourceId` , který lad používá.
* `resourceId`Je integrován do názvů objektů BLOB JSON zapsaných pomocí lad.

## <a name="view-your-data"></a>Zobrazení dat

Použijte Azure Portal k zobrazení dat výkonu nebo k nastavení výstrah:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Snímek obrazovky ukazuje Azure Portal. Vybralo se využité místo na disku v metrikě. Zobrazí se výsledný graf.":::

`performanceCounters`Data jsou vždy uložena v Azure Storage tabulce. Rozhraní API pro Azure Storage jsou k dispozici pro mnoho jazyků a platforem.

Data odesílaná do `JsonBlob` jímky se ukládají v objektech blob v účtu úložiště s názvem v části [chráněná nastavení](#protected-settings). Data objektů blob můžete využívat pomocí libovolných rozhraní API služby Azure Blob Storage.

K přístupu k datům v Azure Storage můžete použít také tyto nástroje uživatelského rozhraní:

* Průzkumník serveru sady Visual Studio
* [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)

Následující snímek obrazovky Průzkumník služby Azure Storage relace zobrazuje vygenerované Azure Storage tabulky a kontejnery ze správně nakonfigurovaného rozšíření LAD 3,0 na testovacím virtuálním počítači. Obrázek se přesně neshoduje s [ukázkovou konfigurací LAD 3,0](#example-lad-30-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Snímek obrazovky ukazuje Průzkumník služby Azure Storage.":::


Další informace o tom, jak využívat zprávy publikované do Event Hubsho koncového bodu, najdete v příslušné [dokumentaci k Event Hubs](../../event-hubs/event-hubs-about.md).

## <a name="next-steps"></a>Další kroky

* V [Azure monitor](../../azure-monitor/alerts/alerts-classic-portal.md)vytvořte výstrahy pro metriky, které shromáždíte.
* Vytvořte [grafy monitorování](../../azure-monitor/data-platform.md) pro vaše metriky.
* [Vytvořte sadu škálování virtuálního počítače](../linux/tutorial-create-vmss.md) pomocí vašich metrik pro řízení automatického škálování.
