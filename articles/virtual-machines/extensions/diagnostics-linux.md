---
title: Azure COMPUTE – diagnostické rozšíření Linux 4,0
description: Jak nakonfigurovat diagnostické rozšíření Azure Linux (LAD) 4,0 pro shromažďování metrik a protokolování událostí z virtuálních počítačů se systémem Linux spuštěných v Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 02/05/2021
ms.openlocfilehash: 2e862915bcc524db50e7e66c969b713f729c64aa
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479640"
---
# <a name="use-the-linux-diagnostic-extension-40-to-monitor-metrics-and-logs"></a>K monitorování metrik a protokolů použijte diagnostické rozšíření pro Linux 4,0.

Tento dokument popisuje nejnovější verze diagnostického rozšíření pro Linux (LAD).

> [!IMPORTANT]
> Informace o verzi 3. x najdete v tématu  [použití diagnostického rozšíření pro Linux 3,0 k monitorování metrik a protokolů](./diagnostics-linux-v3.md). Informace o verzi 2,3 a starších verzích najdete v tématu [monitorování výkonu a diagnostických dat virtuálního počítače se systémem Linux](https://docs.microsoft.com/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Úvod

diagnostické rozšíření pro Linux pomáhá uživateli monitorovat stav virtuálního počítače se systémem Linux běžícího na Microsoft Azure. Má následující možnosti:

* Shromažďuje z virtuálního počítače metriky výkonu systému a ukládá je do konkrétní tabulky v určeném účtu úložiště.
* Načte události protokolu z syslog a uloží je do konkrétní tabulky v určeném účtu úložiště.
* Umožňuje uživatelům přizpůsobit metriky dat, které se shromáždí a nahrají.
* Umožňuje uživatelům přizpůsobit zařízení syslog a úrovně závažnosti shromažďovaných a odesílaných událostí.
* Umožňuje uživatelům odeslat zadané soubory protokolu do určené tabulky úložiště.
* Podporuje odesílání metrik a protokolování událostí do libovolných koncových bodů EventHub a objektů BLOB ve formátu JSON v určeném účtu úložiště.

Toto rozšíření funguje v obou modelech nasazení Azure.

## <a name="install-the-extension-on-a-vm"></a>Instalace rozšíření na virtuální počítač

Toto rozšíření můžete povolit pomocí rutin Azure PowerShell, skriptů Azure CLI, Azure Resource Manager šablon (šablon ARM) nebo Azure Portal. Další informace najdete v tématu věnovaném [rozšířením a funkcím](features-linux.md).

>[!NOTE]
>Některé komponenty rozšíření pro diagnostické virtuální počítače Linux se také dodávají v [Log Analytics rozšíření virtuálního počítače](./oms-linux.md). Z důvodu této architektury mohou konflikty nastat, pokud jsou obě rozšíření vytvořena ve stejné šabloně ARM. 
>
>Aby nedocházelo ke konfliktům při instalaci, použijte [ `dependsOn` direktivu](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) k následné instalaci rozšíření. Rozšíření lze instalovat v libovolném pořadí.

Pomocí instalačních instrukcí a [ukázkové konfigurace ke stažení](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) nakonfigurujte lad 4,0 na:

* Zachyťte a uložte stejné metriky, které poskytují LAD verze 2,3 a 3. x.
* Odeslat metriky do Azure Monitor jímky spolu s obvyklou jímkou Azure Storage. Tato funkce je v LAD 4,0 novinkou.
* Zachytit užitečnou sadu metrik systému souborů, jako v LAD 3,0.
* Zachyťte výchozí kolekci syslog povolenou LAD 2,3.
* Umožněte Azure Portal prostředí pro vytváření grafů a upozorňování na metriky virtuálních počítačů.

Konfigurace ke stažení je pouze příklad. Upravte ji tak, aby vyhovovala vašim potřebám.

### <a name="supported-linux-distributions"></a>Podporované distribuce systému Linux

Diagnostické rozšíření pro Linux podporuje mnoho distribucí a verzí. Následující seznam distribucí a verzí platí jenom pro Image dodavatele Linux schválené systémem. Toto rozšíření obecně nepodporuje Image BYOL a BYOS třetích stran, jako jsou zařízení.

Distribuce, která obsahuje pouze hlavní verze, například Debian 7, je podporována také pro všechny podverze. Pokud je zadána konkrétní dílčí verze, je podporována pouze tato verze. Pokud je připojeno znaménko plus (+), jsou podporovány nižší verze, které jsou stejné nebo vyšší než zadaná verze.

Podporované distribuce a verze:

- Ubuntu 18,04, 16,04, 14,04
- CentOS 7, 6.5 +
- Oracle Linux 7, 6.4 +
- OpenSUSE 13.1 +
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- Red Hat Enterprise Linux (RHEL) 7, 6.7 +

### <a name="prerequisites"></a>Požadavky

* **Agent Azure Linux verze 2.2.0 nebo novější**. Většina imagí z Galerie virtuálních počítačů Azure pro Linux zahrnuje verzi 2.2.7 nebo novější. Spusťte `/usr/sbin/waagent -version` a potvrďte verzi nainstalovanou na virtuálním počítači. Pokud na virtuálním počítači běží starší verze agenta hosta, [aktualizujte agenta pro Linux](./update-linux-agent.md).
* Rozhraní příkazového **řádku Azure** Nastavte na svém počítači prostředí [Azure CLI](/cli/azure/install-azure-cli) .
* **`wget` Příkaz**. Pokud ho ještě nemáte, spusťte příkaz `sudo apt-get install wget` .
* **Předplatné Azure a účet úložiště pro obecné účely** , který ukládá data.  Účty úložiště pro obecné účely podporují úložiště tabulek, které je povinné.  Účet úložiště BLOB nebude fungovat.
* **Python 2**.

### <a name="python-requirement"></a>Požadavek Pythonu

Diagnostické rozšíření pro Linux vyžaduje Python 2. Pokud váš virtuální počítač používá distribuci, která ve výchozím nastavení neobsahuje Python 2, nainstalujte ji. 

Následující vzorové příkazy instalují Python 2 v různých distribucích:    

- Red Hat, CentOS, Oracle: `yum install -y python2`
- Ubuntu, Debian: `apt-get install -y python2`
- SUSE `zypper install -y python2`

`python2`Spustitelný soubor musí mít alias na *Python*. Tento alias můžete nastavit jedním ze způsobů:

1. Spuštěním následujícího příkazu odeberte všechny existující aliasy.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Spusťte následující příkaz pro vytvoření aliasu.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>Ukázková instalace

> [!NOTE]
> V následujících ukázkách Vyplňte správné hodnoty proměnných v první části před spuštěním kódu. 

V těchto příkladech Ukázková konfigurace shromažďuje sadu standardních dat a odesílá je do tabulkového úložiště. Adresa URL pro ukázkovou konfiguraci a její obsah se může změnit. 

Ve většině případů byste si měli stáhnout kopii souboru JSON s nastavením portálu a přizpůsobit ho podle svých potřeb. Pak použijte šablony nebo vlastní automatizaci k použití přizpůsobené verze konfiguračního souboru místo stahování z adresy URL pokaždé, když.

> [!NOTE]
> Pokud povolíte novou jímku Azure Monitor, virtuální počítače musí mít povolenou identitu přiřazenou systémem pro generování tokenů ověřování Identita spravované služby (MSI). Tato nastavení můžete přidat během vytváření virtuálního počítače nebo po něm. 
>
> Pokyny pro Azure Portal, Azure CLI, PowerShell a Azure Resource Manager najdete v tématu [Konfigurace spravovaných identit](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md). 



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

# Enable system-assigned identity on the existing VM.
az vm identity assign -g $my_resource_group -n $my_linux_vm

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
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-40-on-a-virtual-machine-scale-set-instance"></a>Ukázka Azure CLI pro instalaci LAD 4,0 na instanci sady škálování virtuálního počítače

```azurecli
# Set your Azure virtual machine scale set diagnostic variables. 
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Enable system-assigned identity on the existing virtual machine scale set.
az vmss identity assign -g $my_resource_group -n $my_linux_vmss

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
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>Ukázka PowerShellu

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Enable system-assigned identity on an existing VM
Update-AzVM -ResourceGroupName $VMresourceGroup -VM $vm -IdentityType SystemAssigned

# Get the public settings template from GitHub and update the templated values for the storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have your own customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0 
```

### <a name="update-the-extension-settings"></a>Aktualizace nastavení rozšíření

Když změníte chráněné nebo veřejné nastavení, nasaďte je do virtuálního počítače spuštěním stejného příkazu. Pokud se změní jakákoli nastavení, aktualizace se odešlou do rozšíření. LAD znovu načte konfiguraci a sám se restartuje.

### <a name="migrate-from-previous-versions-of-the-extension"></a>Migrace z předchozích verzí rozšíření

Nejnovější verze rozšíření je *4,0, která je aktuálně ve verzi Public Preview*. Starší verze 3. x jsou stále podporovány. Verze 2. x jsou ale zastaralé od 31. července 2018.

> [!IMPORTANT]
> Chcete-li provést migraci ze 3. x na nejnovější verzi rozšíření, odinstalujte starou příponu. Pak nainstalujte verzi 4, která zahrnuje aktualizovanou konfiguraci pro identitu přiřazenou systémem a jímky pro odesílání metrik do Azure Monitor jímky.

Při instalaci nového rozšíření Povolte automatické upgrady podverze:
* V případě virtuálních počítačů modelu nasazení Classic zadejte verzi, `4.*` Pokud instalujete rozšíření prostřednictvím rozhraní příkazového řádku Azure Xplat CLI nebo PowerShellu.
* Na Azure Resource Manager virtuálních počítačů modelu nasazení zahrňte `"autoUpgradeMinorVersion": true` do šablony nasazení virtuálních počítačů.

Můžete použít stejný účet úložiště, který jste použili pro LAD 3. x. 

## <a name="protected-settings"></a>Chráněná nastavení

Tato sada informací o konfiguraci obsahuje citlivé informace, které by měly být chráněny před veřejným zobrazením. Obsahuje například přihlašovací údaje úložiště. Nastavení se přenáší do a ukládají rozšíření v šifrované podobě.

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
storageAccountName | Název účtu úložiště, ve kterém rozšíření zapisuje data.
storageAccountEndPoint | Volitelné Koncový bod, který identifikuje Cloud, ve kterém existuje účet úložiště. Pokud toto nastavení chybí, služba LAD ve výchozím nastavení používá veřejný cloud Azure `https://core.windows.net` . Pokud chcete použít účet úložiště v Azure Německo, Azure Government nebo Azure Čína 21Vianet, nastavte tuto hodnotu podle potřeby.
storageAccountSasToken | [Token SAS účtu](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) pro objekty BLOB a Table Services ( `ss='bt'` ). Tento token se vztahuje na kontejnery a objekty ( `srt='co'` ). Uděluje oprávnění přidat, vytvořit, zobrazit, aktualizovat a zapsat ( `sp='acluw'` ). Nezahrnujte *úvodní* otazník (?).
mdsdHttpProxy | Volitelné Informace o proxy serveru HTTP: rozšíření se musí připojit k zadanému účtu úložiště a koncovému bodu.
sinksConfig | Volitelné Podrobnosti o alternativních umístěních, na které se dají doručovat metriky a události Následující části obsahují podrobné informace o jednotlivých datových jímkach, které rozšíření podporuje.

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

`sinksConfig`Volitelný oddíl definuje více umístění, do kterých rozšíření odesílá shromážděné informace. `"sink"`Pole obsahuje objekt pro každou další datovou jímku. `"type"`Atribut určuje ostatní atributy v objektu.

Prvek | Hodnota
------- | -----
name | Řetězec, který se používá k odkazování na tuto jímku na jiné místo v konfiguraci rozšíření.
typ | Typ definované jímky. Určuje další hodnoty (pokud existují) v instancích tohoto typu.

Diagnostické rozšíření pro Linux 4,0 podporuje dva typy jímky: `EventHub` a `JsonBlob` .

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

`"sasURL"`Položka obsahuje úplnou adresu URL, včetně tokenu SAS, pro centrum událostí, na které se mají data publikovat. LAD vyžaduje SAS pro pojmenování zásady, která povoluje deklaraci identity Send. Tady je příklad:

* Vytvořte obor názvů Event Hubs s názvem `contosohub` .
* Vytvořte centrum událostí v oboru názvů s názvem `syslogmsgs` .
* Vytvořte zásadu sdíleného přístupu v centru událostí s názvem `writer` , která umožňuje odeslat deklaraci identity.

Pokud jste vytvořili SAS, která je dobrá až do půlnoci UTC od 1. ledna 2018, `sasURL` hodnota může být podobně jako v následujícím příkladu.

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

Objekty blob jsou uloženy v kontejneru, který má stejný název jako jímka. Pravidla Azure Storage pro názvy kontejnerů objektů BLOB se vztahují na názvy `JsonBlob` umyvadel. To znamená, že názvy musí obsahovat 3 až 63 malých alfanumerických znaků ASCII nebo pomlčky.

## <a name="public-settings"></a>Veřejné nastavení

Struktura veřejných nastavení obsahuje různé bloky nastavení, které řídí informace, které rozšíření shromažďuje. Každé nastavení, s výjimkou `ladCfg` , je volitelné. Pokud v nástroji zadáte metriku nebo kolekci syslog `ladCfg` , je nutné zadat také `StorageAccount` . Je nutné zadat `sinksConfig` prvek pro povolení jímky Azure monitor pro metriky z LAD 4,0.

```json
{
    "ladCfg":  { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "sinksConfig": { ... },
    "mdsdHttpProxy" : ""
}
```

Prvek | Hodnota
------- | -----
StorageAccount | Název účtu úložiště, ve kterém rozšíření zapisuje data. Musí se jednat o název zadaný v [Nastavení Protected](#protected-settings).
mdsdHttpProxy | Volitelné Proxy server zadaný v [Nastavení chráněno](#protected-settings) Pokud je soukromá hodnota nastavená, přepíše se veřejná hodnota. Zadejte nastavení proxy serveru, které v [chráněných nastaveních](#protected-settings)obsahuje tajný klíč, jako je třeba heslo.

Následující části obsahují podrobné informace o zbývajících prvcích.

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

`ladCfg`Struktura řídí shromažďování metrik a protokolů pro doručování do služby Azure monitor metriky a do dalších datových umyvadel. Zadejte buď `performanceCounters` nebo `syslogEvents` nebo obojí. Určete také `metrics` strukturu.

Pokud nechcete povolit shromažďování dat syslog nebo metrik, zadejte prázdnou strukturu pro `ladCfg` element, jako v tomto příkladu: 

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {}
    }
```

Prvek | Hodnota
------- | -----
eventVolume | Volitelné Určuje počet oddílů vytvořených v rámci tabulky úložiště. Hodnota musí být `"Large"` , `"Medium"` nebo `"Small"` . Pokud hodnota není zadaná, výchozí hodnota je `"Medium"` .
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
resourceId | ID prostředku Azure Resource Manager virtuálního počítače nebo sady škálování virtuálního počítače, do které virtuální počítač patří. Toto nastavení zadejte také v případě, že konfigurace používá jakoukoli `JsonBlob` jímku.
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
pomocné | Volitelné Vybere instanci objektu, na kterou se metrika vztahuje. Nebo vybere agregaci napříč všemi instancemi daného objektu. 
sampleRate | Interval je 8601, který nastavuje rychlost shromažďování nezpracovaných vzorků pro tuto metriku. Pokud hodnota není nastavena, interval shromažďování je nastaven hodnotou [`sampleRateInSeconds`](#ladcfg) . Nejkratší podporovaná vzorkovací frekvence je 15 sekund (PT15S).
unit | Definuje jednotku pro metriku. By měl být jeden z těchto řetězců: `"Count"` , `"Bytes"` , `"Seconds"` , `"Percent"` , `"CountPerSecond"` , `"BytesPerSecond"` , `"Millisecond"` . Spotřebitelé shromážděných dat očekávají, že hodnoty shromážděných dat odpovídají této jednotce. LAD ignoruje toto pole.
displayName | Popisek, který se má připojit k datům v Azure Monitor metriky. Tento popisek je v jazyce určeném pomocí přidruženého nastavení národního prostředí. LAD ignoruje toto pole.

`counterSpecifier`Je libovolný identifikátor. Příjemci metrik, jako Azure Portal je například funkce pro vytváření grafů a upozorňování, slouží `counterSpecifier` jako "klíč", který identifikuje metriku nebo instanci metriky. 

Pro `builtin` metriky doporučujeme `counterSpecifier` hodnoty, které začínají na `/builtin/` . Pokud shromažďujete konkrétní instanci metriky, připojte identifikátor instance k `counterSpecifier` hodnotě. Tady je několik příkladů:

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
facilityName | Název zařízení syslog, například `"LOG\_USER"` nebo `"LOG\_LOCAL0"` . Další informace najdete v části "zařízení" na [stránce zachycení služby SYSLOG](http://man7.org/linux/man-pages/man3/syslog.3.html).
minSeverity | Úroveň závažnosti syslog, například `"LOG\_ERR"` nebo `"LOG\_INFO"` . Další informace najdete v části "úroveň" [stránky zachycení služby SYSLOG](http://man7.org/linux/man-pages/man3/syslog.3.html). Rozšíření zachytí události odesílané do zařízení na zadané úrovni nebo nad ní.

Když zadáte `syslogEvents` , lad vždy zapisuje data do tabulky v Azure Storage. Stejná data lze zapsat do objektů BLOB JSON nebo Event Hubs nebo obojího. Nemůžete ale zakázat ukládání dat do tabulky. 

Chování dělení této tabulky je stejné, jak je popsáno v `performanceCounters` . Název tabulky je zřetězení těchto řetězců:

* `LinuxSyslog`
* Datum ve formátu "RRRRMMDD", které se mění každých 10 dní

Příklady zahrnují `LinuxSyslog20170410` a `LinuxSyslog20170609` .

### <a name="sinksconfig"></a>sinksConfig

`sinksConfig`Volitelný oddíl umožňuje kromě účtu úložiště a výchozího okna metriky hostů odesílat metriky do Azure monitor jímky.

> [!NOTE]
> V této části je třeba `sinksConfig` Povolit identitu přiřazenou systémem na virtuálních počítačích nebo v sadě škálování virtuálních počítačů. Identitu přiřazenou systémem můžete povolit prostřednictvím Azure Portal, CLI, PowerShellu nebo Azure Resource Manager. Postupujte podle [podrobných pokynů](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) nebo si přečtěte předchozí ukázky instalací v tomto článku. 

```json
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
```


### <a name="filelogs"></a>Protokoly

`fileLogs`Oddíl řídí zachycení souborů protokolu. LAD zachycuje nové textové řádky při jejich zápisu do souboru. Zapisuje je do řádků tabulky nebo do všech zadaných umyvadel, například `JsonBlob` a `EventHub` .

> [!NOTE]
> `fileLogs`Jsou zachyceny dílčí komponentou lad s názvem `omsagent` . Aby bylo možné shromáždit `fileLogs` informace, ujistěte se, že `omsagent` uživatel má oprávnění ke čtení pro soubory, které zadáte. Musí mít také oprávnění EXECUTE pro všechny adresáře v cestě k tomuto souboru. Po instalaci nástroje LAD můžete kontrolovat oprávnění spuštěním `sudo su omsagent -c 'cat /path/to/file'` .

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
 – soubor | Úplný název cesty k souboru protokolu, který se má sledovat a zachytit. Název cesty je pro jeden soubor. Nemůže obsahovat název adresáře ani zástupné znaky. `omsagent`Uživatelský účet musí mít k této cestě k souboru přístup pro čtení.
tabulka | Volitelné Tabulka Azure Storage, do které se zapisují nové řádky ze "konce" souboru. Tabulka musí být v určeném účtu úložiště, jak je uvedeno v části chráněná konfigurace. 
jímky | Volitelné Čárkami oddělený seznam názvů dalších umyvadel, na které se odesílají řádky protokolu.

`"table"` `"sinks"` Musí být zadán buď nebo obojí.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metriky podporované zprostředkovatelem Builtin

> [!NOTE]
> Výchozí metriky, které LAD podporuje, jsou agregované napříč všemi systémy souborů, disky nebo názvy. Pro neagregované metriky se podívejte na podporu Azure Monitor novějších metrik jímky.

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

### <a name="builtin-metrics-for-the-memory-class"></a>předdefinované metriky pro třídu paměti

Třída Memory metriky poskytuje informace o využití paměti, stránkování a prohození.

counter | Význam
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

## <a name="install-and-configure-lad-40"></a>Instalace a konfigurace LAD 4,0

LAD 4,0 můžete nainstalovat a nakonfigurovat v rozhraní příkazového řádku Azure CLI nebo PowerShellu.

### <a name="azure-cli"></a>Azure CLI

Pokud jsou vaše chráněná nastavení v souboru *ProtectedSettings.jsna* a informace o vaší veřejné konfiguraci jsou v *PublicSettings.jsna*, spusťte tento příkaz:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 4.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Příkaz předpokládá, že používáte režim správy prostředků Azure Azure CLI. Pokud chcete nakonfigurovat LAD pro virtuální počítače modelu nasazení Classic, přepněte do režimu ASM ( `azure config mode asm` ) a vynechejte název skupiny prostředků v příkazu. 

Další informace najdete v dokumentaci k rozhraní příkazového [řádku pro více platforem](/cli/azure/authenticate-azure-cli).

### <a name="powershell"></a>PowerShell

Pokud jsou vaše chráněná nastavení v `$protectedSettings` proměnné a informace o veřejné konfiguraci jsou v `$publicSettings` proměnné, spusťte tento příkaz:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 4.0
```

## <a name="example-lad-40-configuration"></a>Příklad konfigurace LAD 4,0

V závislosti na předchozích definicích poskytuje tato část ukázkovou konfiguraci rozšíření LAD 4,0 a některé vysvětlení. Pokud chcete tuto ukázku použít pro váš případ, použijte vlastní název účtu úložiště, token SAS účtu a Event Hubs tokeny SAS.

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

* Odeslat metriku procentuální hodnoty času procesoru a využité místo na disku pro `WADMetrics*` tabulku,
* Nahrajte zprávy ze zařízení syslog `"user"` a závažnost `"info"` do `LinuxSyslog*` tabulky.
* Nahrajte připojené řádky do souboru `/var/log/myladtestlog` do `MyLadTestLog` tabulky.

V každém případě se data nahrají taky do:

* Blob Storage Azure. Název kontejneru je definovaný v `JsonBlob` jímky.
* Event Hubs koncový bod, jak je uvedeno v `EventHubs` jímky.

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
  "sinksConfig": {
    "sink": [
      {
        "name": "AzMonSink",
        "type": "AzMonSink",
        "AzureMonitor": {}
      }
    ]
  },
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

Pomocí Azure Portal zobrazte data výkonu nebo nastavte výstrahy:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Snímek obrazovky ukazuje Azure Portal. Vybralo se využité místo na disku v metrikě. Zobrazí se výsledný graf.":::

`performanceCounters`Data jsou vždy uložena v Azure Storage tabulce. Rozhraní API pro Azure Storage jsou k dispozici pro mnoho jazyků a platforem.

Data odesílaná do `JsonBlob` jímky se ukládají v objektech blob v účtu úložiště s názvem v části [chráněná nastavení](#protected-settings). Data objektu blob můžete využívat v jakémkoli rozhraní API Azure Blob Storage.

K přístupu k datům v Azure Storage můžete použít také tyto nástroje uživatelského rozhraní:

* Průzkumník serveru sady Visual Studio
* [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)

Následující snímek obrazovky Průzkumník služby Azure Storage relace zobrazuje vygenerované Azure Storage tabulky a kontejnery ze správně nakonfigurovaného rozšíření LAD 4,0 na testovacím virtuálním počítači. Obrázek se přesně neshoduje s [ukázkovou konfigurací LAD 4,0](#example-lad-40-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Snímek obrazovky ukazuje Průzkumník služby Azure Storage.":::

Další informace o tom, jak využívat zprávy publikované do Event Hubsho koncového bodu, najdete v příslušné [dokumentaci k Event Hubs](../../event-hubs/event-hubs-about.md).

## <a name="next-steps"></a>Další kroky

* V [Azure monitor](../../azure-monitor/alerts/alerts-classic-portal.md)vytvořte výstrahy pro metriky, které shromáždíte.
* [Vytvořte grafy monitorování](../../azure-monitor/data-platform.md) pro vaše metriky.
* [Vytvořte sadu škálování virtuálního počítače](../linux/tutorial-create-vmss.md) pomocí vašich metrik pro řízení automatického škálování.