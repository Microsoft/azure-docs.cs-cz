---
title: Azure COMPUTE – diagnostické rozšíření Linux 3,0
description: Jak nakonfigurovat diagnostické rozšíření Azure Linux (LAD) 3,0 pro shromažďování metrik a protokolování událostí z virtuálních počítačů se systémem Linux spuštěných v Azure.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.openlocfilehash: d063aec3b093f00640d909a6ce3c2cde6d2d2420
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102547420"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>Monitorování metrik a protokolů pomocí diagnostického rozšíření Linux 3,0

Tento dokument popisuje verzi 3,0 a novější diagnostické rozšíření pro Linux.

> [!IMPORTANT]
> Informace o verzi 2,3 a starší najdete v [tomto dokumentu](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2).

## <a name="introduction"></a>Úvod

Diagnostické rozšíření pro Linux pomáhá uživateli monitorovat stav virtuálního počítače se systémem Linux běžícího na Microsoft Azure. Má následující možnosti:

* Shromažďuje z virtuálního počítače metriky výkonu systému a ukládá je do konkrétní tabulky v určeném účtu úložiště.
* Načte události protokolu z syslog a uloží je do konkrétní tabulky v určeném účtu úložiště.
* Umožňuje uživatelům přizpůsobit metriky dat, které se shromáždí a nahrají.
* Umožňuje uživatelům přizpůsobit zařízení syslog a úrovně závažnosti shromažďovaných a odesílaných událostí.
* Umožňuje uživatelům odeslat zadané soubory protokolu do určené tabulky úložiště.
* Podporuje odesílání metrik a protokolování událostí do libovolných koncových bodů EventHub a objektů BLOB ve formátu JSON v určeném účtu úložiště.

Toto rozšíření funguje v obou modelech nasazení Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalace rozšíření na virtuálním počítači

Toto rozšíření můžete povolit pomocí rutin Azure PowerShell, skriptů Azure CLI, šablon ARM nebo Azure Portal. Další informace najdete v tématu [funkce rozšíření](features-linux.md).

>[!NOTE]
>Některé komponenty rozšíření virtuálního počítače diagnostiky se také dodávají v [rozšíření Log Analytics VM](./oms-linux.md). Z důvodu této architektury mohou konflikty nastat, pokud jsou obě rozšíření vytvořena ve stejné šabloně ARM. Aby se tyto konflikty při instalaci nezobrazovaly, použijte [ `dependsOn` direktivu](../../azure-resource-manager/templates/define-resource-dependency.md#dependson) , abyste zajistili, že se rozšíření nainstalují sekvenčně. Rozšíření lze instalovat v libovolném pořadí.

Tyto pokyny k instalaci a [Ukázková konfigurace ke stažení](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) nakonfigurují lad 3,0 na:

* Zachyťte a uložte stejné metriky, které poskytla služba LAD 2,3;
* zachytit užitečnou sadu metrik systému souborů, která je novinkou LAD 3,0;
* zachytit výchozí kolekci syslog povolenou LAD 2,3;
* Umožněte Azure Portal prostředí pro vytváření grafů a upozorňování na metriky virtuálních počítačů.

Konfigurace ke stažení je pouze příklad. upravte ji tak, aby vyhovovala vašim potřebám.

### <a name="supported-linux-distributions"></a>Podporované distribuce systému Linux

Diagnostické rozšíření pro Linux podporuje následující distribuce a verze. Seznam distribucí a verzí se týká jenom imagí dodavatele systému Linux schváleného systémem. Image BYOL a BYOS třetích stran, jako jsou zařízení, se obecně nepodporují pro diagnostické rozšíření Linux.

Distribuce, která obsahuje pouze hlavní verze, například Debian 7, je podporována také pro všechny podverze. Pokud je zadána konkrétní podverze, je podporována pouze tato konkrétní verze. Pokud je připojen znak "+", jsou podporovány menší verze, které jsou větší nebo rovny zadané verzi.

Podporované distribuce a verze:

- Ubuntu 18,04, 16,04, 14,04
- CentOS 7, 6.5 +
- Oracle Linux 7, 6.4 +
- OpenSUSE 13.1 +
- SUSE Linux Enterprise Server 12
- Debian 9, 8, 7
- RHEL 7, 6.7 +

### <a name="prerequisites"></a>Požadavky

* **Agent Azure Linux verze 2.2.0 nebo novější**. Většina imagí z Galerie virtuálních počítačů Azure pro Linux zahrnuje verzi 2.2.7 nebo novější. Spusťte `/usr/sbin/waagent -version` a potvrďte verzi nainstalovanou na virtuálním počítači. Pokud na virtuálním počítači běží starší verze agenta hosta, aktualizujte ho podle [těchto pokynů](./update-linux-agent.md) .
* Rozhraní příkazového **řádku Azure** Nastavte na svém počítači prostředí [Azure CLI](/cli/azure/install-azure-cli) .
* Příkaz wget, pokud ho ještě nemáte: Spusťte `sudo apt-get install wget` .
* Existující předplatné Azure a existující účet úložiště pro obecné účely, ve kterém se budou ukládat data.  Účty úložiště pro obecné účely podporují úložiště tabulek, které je povinné.  Účet Blob Storage nebude fungovat.
* Python 2

### <a name="python-requirement"></a>Požadavek Pythonu

Diagnostické rozšíření pro Linux vyžaduje Python 2. Pokud váš virtuální počítač používá distribuce, který ve výchozím nastavení neobsahuje Python 2, musíte ho nainstalovat. Následující vzorové příkazy instalují Python 2 v různých distribuce.    

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SUSE `zypper install -y python2`

Spustitelný soubor python2 musí mít alias na *Python*. Následuje jedna metoda, kterou můžete použít k nastavení tohoto aliasu:

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
> U některé z ukázek Vyplňte správné hodnoty proměnných v první části před spuštěním. 

Ukázková konfigurace stažená v těchto příkladech shromažďuje sadu standardních dat a odesílá je do úložiště tabulek. Adresa URL pro ukázkovou konfiguraci a její obsah se může změnit. Ve většině případů byste si měli stáhnout kopii souboru JSON s nastavením portálu a přizpůsobit ho vašim potřebám, potom budete mít všechny šablony nebo automatizace, které vytvoříte, místo stažení této adresy URL použili vlastní verzi konfiguračního souboru.

#### <a name="azure-cli-sample"></a>Ukázka Azure CLI

```azurecli
# Set your Azure VM diagnostic variables correctly below
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-for-installing-lad-30-extension-on-the-virtual-machine-scale-set-instance"></a>Ukázka Azure CLI pro instalaci rozšíření LAD 3,0 na instanci sady škálování virtuálního počítače

```azurecli
#Set your Azure VMSS diagnostic variables correctly below
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Should login to Azure first before anything else
az login

# Select the subscription containing the storage account
az account set --subscription <your_azure_subscription_id>

# Download the sample Public settings. (You could also use curl or any web browser)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VMSS resource ID. Replace storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token)
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally tell Azure to install and enable the extension
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

# If you have your own customized public settings, you can inline those rather than using the template above: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>Aktualizace nastavení rozšíření

Po změně chráněných nebo veřejných nastavení je můžete nasadit do virtuálního počítače spuštěním stejného příkazu. Pokud se v nastavení změní nějaké změny, do rozšíření se pošle aktualizované nastavení. LAD znovu načte konfiguraci a sám se restartuje.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migrace z předchozích verzí rozšíření

Nejnovější verze rozšíření je **3,0**. **Všechny staré verze (2. x) jsou zastaralé a mohou být publikovány od 31. července 2018 nebo po ní**.

> [!IMPORTANT]
> Toto rozšíření přináší zásadní změny v konfiguraci rozšíření. Tato změna byla provedena za účelem zlepšení zabezpečení rozšíření. v důsledku toho nelze zachovat zpětnou kompatibilitu s 2. x. Také Vydavatel rozšíření pro toto rozšíření je jiný než Vydavatel pro verze 2. x.
>
> Chcete-li provést migraci z 2. x na tuto novou verzi rozšíření, je nutné odinstalovat starou příponu (pod starým názvem vydavatele) a pak nainstalovat verzi 3 rozšíření.

Doporučit

* Nainstalujte rozšíření s povoleným automatickým upgradem dílčí verze.
  * V případě virtuálních počítačů modelu nasazení Classic zadejte jako verzi hodnotu 3. *, pokud chcete rozšíření instalovat prostřednictvím Azure XPLAT CLI nebo PowerShellu.
  * V Azure Resource Manager virtuálních počítačů modelu nasazení přidejte do šablony nasazení virtuálního počítače "" autoUpgradeMinorVersion ": true.
* Pro LAD 3,0 použijte nový nebo jiný účet úložiště. Mezi LAD 2,3 a LAD 3,0 dochází k několika malým nekompatibilitám, které sdílejí účet komplikované:
  * LAD 3,0 ukládá události syslog do tabulky s jiným názvem.
  * Řetězce counterSpecifier pro `builtin` metriky se liší v LAD 3,0.

## <a name="protected-settings"></a>Chráněná nastavení

Tato sada informací o konfiguraci obsahuje citlivé informace, které by měly být chráněny před veřejným zobrazením, například přihlašovací údaje úložiště. Tato nastavení se přenášejí do a ukládají rozšíření v šifrované podobě.

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
storageAccountEndPoint | volitelné Koncový bod identifikující Cloud, ve kterém existuje účet úložiště. Pokud toto nastavení chybí, LAD se výchozí nastavení pro veřejný cloud Azure, `https://core.windows.net` . Pokud chcete použít účet úložiště v Azure Německo, Azure Government nebo Azure Čína, nastavte tuto hodnotu odpovídajícím způsobem.
storageAccountSasToken | [Token SAS účtu](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) pro služby BLOB a Table Services ( `ss='bt'` ), který se vztahuje na kontejnery a objekty () `srt='co'` , což uděluje oprávnění k přidávání, vytváření, výpisům, aktualizaci a zápisu ( `sp='acluw'` ). Nezahrnujte *úvodní* otazník (?).
mdsdHttpProxy | volitelné Informace o proxy serveru HTTP potřebné k povolení rozšíření pro připojení k zadanému účtu úložiště a koncovému bodu
sinksConfig | volitelné Podrobnosti o alternativních umístěních, na které se dají doručovat metriky a události Konkrétní podrobnosti o jednotlivých datových jímkach podporovaných rozšířením jsou uvedené v následujících oddílech.

K získání tokenu SAS v rámci šablony Správce prostředků použijte funkci **listAccountSas** . Příklad šablony naleznete v tématu [Příklad funkce list](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Požadovaný token SAS můžete snadno vytvořit prostřednictvím Azure Portal.

1. Vyberte účet úložiště pro obecné účely, na který chcete rozšíření zapisovat.
1. V části nastavení v nabídce vlevo vyberte Shared Access Signature (sdílený přístupový podpis).
1. Proveďte příslušné oddíly, jak je popsáno výše.
1. Klikněte na tlačítko generovat SAS.

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="Snímek obrazovky se stránkou sdíleného přístupového podpisu s generovat S A S.":::

Zkopírujte vygenerované SAS do pole storageAccountSasToken; Odeberte úvodní otazník (?).

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

Tento volitelný oddíl definuje další cíle, do kterých rozšíření odesílá informace, které shromažďuje. Pole "jímka" obsahuje objekt pro každou další datovou jímku. Atribut Type určuje ostatní atributy v objektu.

Prvek | Hodnota
------- | -----
name | Řetězec, který se používá k odkazování na tuto jímku na jiné místo v konfiguraci rozšíření.
typ | Typ definované jímky. Určuje další hodnoty (pokud existují) v instancích tohoto typu.

Diagnostické rozšíření pro Linux verze 3,0 podporuje dva typy jímky: EventHub a JsonBlob.

#### <a name="the-eventhub-sink"></a>Jímka EventHub

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

Položka "sasURL" obsahuje úplnou adresu URL, včetně tokenu SAS, pro centrum událostí, do kterého se mají data publikovat. LAD vyžaduje, aby pojmenování SAS vyžadovalo zásadu, která povoluje nárok na odeslání. Příklad:

* Vytvořte obor názvů Event Hubs s názvem. `contosohub`
* Vytvořte centrum událostí v oboru názvů s názvem. `syslogmsgs`
* Vytvořte zásadu sdíleného přístupu v centru událostí s názvem `writer` , která umožňuje odeslat deklaraci identity.

Pokud jste vytvořili SAS vhodným až do půlnoci UTC od 1. ledna 2018, může být sasURL hodnota:

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Další informace o generování a načítání informací o tokenech SAS pro Event Hubs naleznete na [této webové stránce](/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="the-jsonblob-sink"></a>Jímka JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Data směrované do jímky JsonBlob se ukládají v objektech blob ve službě Azure Storage. Každá instance LAD vytváří objekt BLOB každou hodinu pro každý název jímky. Každý objekt BLOB vždy obsahuje syntakticky platné pole JSON objektu. Nové položky jsou atomicky přidány do pole. Objekty blob jsou uloženy v kontejneru se stejným názvem, jako má jímka. Pravidla úložiště Azure pro názvy kontejnerů objektů BLOB se vztahují na názvy jímky JsonBlob: mezi 3 a 63 malými alfanumerickými znaky ASCII nebo pomlčkami.

## <a name="public-settings"></a>Veřejné nastavení

Tato struktura obsahuje různé bloky nastavení, které řídí informace shromažďované rozšířením. Každé nastavení je volitelné. Pokud zadáte `ladCfg` , je nutné zadat také `StorageAccount` .

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
StorageAccount | Název účtu úložiště, ve kterém se má rozšíření zapsat data Musí se jednat o stejný název, jako je zadaný v [Nastavení Protected](#protected-settings).
mdsdHttpProxy | volitelné Stejné jako v [chráněných nastaveních](#protected-settings). Veřejná hodnota je přepsána soukromou hodnotou, pokud je nastavena. V [chráněných nastaveních](#protected-settings)umístěte nastavení proxy serveru, jako je třeba heslo.

Zbývající prvky jsou podrobně popsány v následujících oddílech.

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

Tato volitelná struktura ovládá shromažďování metrik a protokolů pro doručování do služby Azure metrik a dalších datových jímka. Je nutné zadat buď `performanceCounters` nebo `syslogEvents` nebo obojí. Je nutné zadat `metrics` strukturu.

Prvek | Hodnota
------- | -----
eventVolume | volitelné Určuje počet oddílů vytvořených v rámci tabulky úložiště. Musí být jedna z `"Large"` , `"Medium"` nebo `"Small"` . Pokud není zadaný, použije se výchozí hodnota `"Medium"` .
sampleRateInSeconds | volitelné Výchozí interval mezi kolekcemi nezpracovaných (neagregovaných) metrik. Nejmenší podporovaná vzorkovací frekvence je 15 sekund. Pokud není zadaný, použije se výchozí hodnota `15` .

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
resourceId | ID prostředku Azure Resource Manager virtuálního počítače nebo sady škálování virtuálního počítače, do které virtuální počítač patří. Toto nastavení musí být zadáno také v případě, že se v konfiguraci používá jakákoli jímka JsonBlob.
scheduledTransferPeriod | Frekvence, s jakou se mají vypočítat agregované metriky a jejich přenos do metrik Azure, vyjádřené jako časový interval 8601. Nejmenší perioda přenosu je 60 sekund, tj. PT1M. Je nutné zadat alespoň jeden scheduledTransferPeriod.

Ukázky metrik zadané v části čítače výkonu se shromažďují každých 15 sekund nebo vzorkovací frekvence, která je explicitně definovaná pro čítač. Pokud se zobrazí více scheduledTransferPeriod frekvencí (jako v příkladu), každá agregace je vypočítána nezávisle.

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

Tento volitelný oddíl řídí kolekci metrik. Nezpracované vzorky jsou agregované pro každý [scheduledTransferPeriod](#metrics) , aby se vytvořily tyto hodnoty:

* mean
* Minimum
* Maximum
* Poslední shromážděná hodnota
* počet nezpracovaných vzorků používaných k výpočtu agregace

Prvek | Hodnota
------- | -----
jímky | volitelné Čárkami oddělený seznam názvů umyvadel, na které LAD odesílá agregované výsledky metriky. Všechny agregované metriky jsou publikovány v každé uvedené jímky. Viz [sinksConfig](#sinksconfig). Příklad: `"EHsink1, myjsonsink"`.
typ | Určuje skutečného poskytovatele metriky.
class | Společně s "čítač" identifikuje konkrétní metriku v oboru názvů poskytovatele.
counter | Společně s "Class" identifikuje konkrétní metriku v oboru názvů poskytovatele.
counterSpecifier | Identifikuje konkrétní metriku v oboru názvů metrik Azure.
pomocné | volitelné Vybere konkrétní instanci objektu, na kterou metrika aplikuje, nebo vybere agregaci napříč všemi instancemi daného objektu. Další informace najdete v tématu `builtin` definice metrik.
sampleRate | JE 8601 interval, který nastavuje rychlost shromažďování nezpracovaných vzorků pro tuto metriku. Pokud není nastaven, interval shromažďování je nastaven hodnotou [sampleRateInSeconds](#ladcfg). Nejkratší podporovaná vzorkovací frekvence je 15 sekund (PT15S).
unit | Mělo by se jednat o jeden z těchto řetězců: "Count", "bytes", "Seconds", "PERCENT", "CountPerSecond", "BytesPerSecond", "milisekund". Definuje jednotku pro metriku. Spotřebitelé shromážděných dat očekávají, že hodnoty shromážděných dat odpovídají této jednotce. LAD ignoruje toto pole.
displayName | Popisek (v jazyce určeném pomocí přidruženého nastavení národního prostředí), který se má připojit k těmto datům v Azure metrik. LAD ignoruje toto pole.

CounterSpecifier je libovolný identifikátor. Příjemci metrik, jako je například funkce Azure Portaling a upozorňování, používají counterSpecifier jako klíč, který identifikuje metriku nebo instanci metriky. Pro `builtin` metriky doporučujeme používat counterSpecifier hodnoty, které začínají na `/builtin/` . Pokud shromažďujete konkrétní instanci metriky, doporučujeme připojit identifikátor instance k hodnotě counterSpecifier. Několik příkladů:

* `/builtin/Processor/PercentIdleTime` -Průměrná doba nečinnosti napříč všemi vCPU
* `/builtin/Disk/FreeSpace(/mnt)` – Volné místo pro systém souborů/mnt
* `/builtin/Disk/FreeSpace` – Průměrné místo v rámci všech připojených systémů souborů

LAD ani Azure Portal neočekává, že counterSpecifier hodnota odpovídá jakémukoli vzoru. Být konzistentní při sestavování hodnot counterSpecifier.

Když zadáte `performanceCounters` , lad vždy zapisuje data do tabulky ve službě Azure Storage. Můžete mít stejná data zapsaná do objektů BLOB JSON nebo Event Hubs, ale nemůžete zakázat ukládání dat do tabulky. Všechny instance diagnostického rozšíření nakonfigurovaného tak, aby používaly stejný název a koncový bod účtu úložiště, přidají ke stejné tabulce své metriky a protokoly. Pokud je do stejného oddílu tabulky zapisováno příliš mnoho virtuálních počítačů, může Azure omezit zápisy do tohoto oddílu. Nastavení eventVolume způsobí, že položky budou rozloženy mezi 1 (malá), 10 (střední) nebo 100 (velké) různé oddíly. Obvykle je "střední" dostačující, aby se zajistilo, že provoz není omezený. Funkce metrik Azure v Azure Portal používá data v této tabulce k tvorbě grafů nebo k aktivaci výstrah. Název tabulky je zřetězení těchto řetězců:

* `WADMetrics`
* "ScheduledTransferPeriod" pro agregované hodnoty uložené v tabulce
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

Tento volitelný oddíl řídí shromažďování událostí protokolu z syslog. Pokud je oddíl vynechán, události syslog nejsou zachyceny vůbec.

Kolekce syslogEventConfiguration má jednu položku pro každé zařízení syslog, které vás zajímá. Pokud minSeverity je "NONE" pro konkrétní zařízení, nebo pokud se toto zařízení nezobrazí v prvku vůbec, nebudou zachyceny žádné události z tohoto zařízení.

Prvek | Hodnota
------- | -----
jímky | Čárkami oddělený seznam názvů umyvadel, na které se jednotlivé události protokolu publikují. Všechny události protokolu, které odpovídají omezením v syslogEventConfiguration, se publikují do každé uvedené jímky. Příklad: "EHforsyslog"
facilityName | Název zařízení syslog (například "PROTOKOLovat \_ uživatele" nebo "log \_ local0"). Úplný seznam najdete v části "zařízení" na [stránce zachycení služby SYSLOG](http://man7.org/linux/man-pages/man3/syslog.3.html) .
minSeverity | Úroveň závažnosti syslog (například LOG \_ Err nebo log \_ info) Úplný seznam najdete v části "úroveň" [stránky zachycení služby SYSLOG](http://man7.org/linux/man-pages/man3/syslog.3.html) . Rozšíření zachytí události odesílané do zařízení na zadané úrovni nebo nad ní.

Když zadáte `syslogEvents` , lad vždy zapisuje data do tabulky ve službě Azure Storage. Můžete mít stejná data zapsaná do objektů BLOB JSON nebo Event Hubs, ale nemůžete zakázat ukládání dat do tabulky. Chování dělení této tabulky je stejné, jak je popsáno v `performanceCounters` . Název tabulky je zřetězení těchto řetězců:

* `LinuxSyslog`
* Datum ve formátu "RRRRMMDD", které se mění každých 10 dní

Příklady zahrnují `LinuxSyslog20170410` a `LinuxSyslog20170609` .

### <a name="perfcfg"></a>perfCfg

Tento volitelný oddíl řídí provádění libovolných dotazů [OMI](https://github.com/Microsoft/omi) .

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
namespace | volitelné Obor názvů OMI, ve kterém má být dotaz proveden. Je-li tento parametr zadán, je použita výchozí hodnota "root/SCX", kterou implementuje [poskytovatelé služeb System Center pro různé platformy](https://github.com/Microsoft/SCXcore).
query | Dotaz OMI, který se má spustit.
tabulka | volitelné Tabulka úložiště Azure v určeném účtu úložiště (viz [Nastavení chráněná](#protected-settings)).
frequency | volitelné Počet sekund mezi provedením dotazu. Výchozí hodnota je 300 (5 minut); minimální hodnota je 15 sekund.
jímky | volitelné Čárkami oddělený seznam názvů dalších umyvadel, na které by měly být publikovány nezpracované ukázkové výsledky metriky. Žádná agregace těchto nezpracovaných vzorků se počítá rozšířením nebo metrikami Azure.

Je třeba zadat buď Table, nebo "jímky", nebo obojí.

### <a name="filelogs"></a>Protokoly

Řídí zachycení souborů protokolu. LAD zachycuje nové textové řádky při zápisu do souboru a zapisuje je do řádků tabulky nebo do všech zadaných umyvadel (JsonBlob nebo EventHub).

> [!NOTE]
> Protokoly LAD jsou zachyceny podkomponentou s názvem `omsagent` . Aby bylo možné shromažďovat protokoly souborů, musíte zajistit, aby `omsagent` měl uživatel oprávnění ke čtení pro zadané soubory, a také oprávnění ke spouštění na všech adresářích v cestě k tomuto souboru. Tuto kontrolu můžete spustit `sudo su omsagent -c 'cat /path/to/file'` po instalaci lad.

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
 – soubor | Úplná cesta k souboru protokolu, který má být sledován a zachycen. Cesta musí pojmenovat jeden soubor. nemůže obsahovat název adresáře ani zástupné znaky. Uživatelský účet omsagent musí mít k této cestě k souboru přístup pro čtení.
tabulka | volitelné Tabulka úložiště Azure v určeném účtu úložiště (jak je uvedeno v chráněných konfiguracích), do kterého se zapisují nové řádky z "koncového" souboru.
jímky | volitelné Čárkami oddělený seznam názvů dalších umyvadel, na které se odesílají řádky protokolu.

Je třeba zadat buď Table, nebo "jímky", nebo obojí.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metriky podporované zprostředkovatelem Builtin

Předdefinovaná zprostředkovatel metriky je zdrojem metrik, které jsou zajímavé pro širokou škálu uživatelů. Tyto metriky spadají do pěti širších tříd:

* Procesor
* Memory (Paměť)
* Síť
* Filesystem
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>předdefinované metriky pro třídu procesoru

Třída procesoru metrik nabízí informace o využití procesoru ve virtuálním počítači. Při agregaci procent je výsledkem průměr ve všech procesorech. V případě vCPU virtuálního počítače, pokud byl jeden vCPU 100% zaneprázdněný a druhý byl 100% nečinný, nahlášený PercentIdleTime by byl 50. Pokud by každý vCPU byl 50% zaneprázdněný pro stejné období, nahlášený výsledek by byl také 50. Ve vCPUm virtuálním počítači, který má zaneprázdněný vCPU 100% a jiné nečinné, nahlášený PercentIdleTime by byl 75.

counter | Význam
------- | -------
PercentIdleTime | Procento času během okna agregace, které procesory prováděly nečinný cyklus jádra
PercentProcessorTime | Procento času spuštění vlákna, které není nečinné
PercentIOWaitTime | Procento času čekání na dokončení vstupně-výstupních operací
PercentInterruptTime | Procento času provádění hardwarových a softwarových přerušení a DPC (odložená volání procedur)
PercentUserTime | Doba nečinnosti v průběhu okna agregace, procento času stráveného uživatelem s normální prioritou
PercentNiceTime | Nečinný čas, procento strávené za sníženou prioritou (Nice)
PercentPrivilegedTime | Nečinný čas, procento strávené v privilegovaném režimu (kernel)

První čtyři čítače by měly být v součtu 100%. Poslední tři čítače jsou také celkem 100%; rozdělují součet hodnot PercentProcessorTime, PercentIOWaitTime a PercentInterruptTime.

Pro získání jedné metriky agregované napříč všemi procesory nastavte `"condition": "IsAggregate=TRUE"` . Pokud chcete získat metriku pro konkrétní procesor, jako je druhý logický procesor se čtyřmi vCPU virtuálními počítači, nastavte `"condition": "Name=\\"1\\""` . Čísla logických procesorů jsou v rozsahu `[0..n-1]` .

### <a name="builtin-metrics-for-the-memory-class"></a>předdefinované metriky pro třídu paměti

Třída Memory metriky poskytuje informace o využití paměti, stránkování a prohození.

counter | Význam
------- | -------
AvailableMemory | Dostupná fyzická paměť v databázi MiB
PercentAvailableMemory | Dostupná fyzická paměť jako procento z celkové paměti
UsedMemory | Využití fyzické paměti (MiB)
PercentUsedMemory | Použít fyzickou paměť jako procento z celkové paměti
PagesPerSec | Celkový počet stránkování (čtení a zápis)
PagesReadPerSec | Stránky čtené ze záložního úložiště (odkládací soubor, soubor programu, mapovaný soubor atd.)
PagesWrittenPerSec | Stránky zapsané do záložního úložiště (odkládací soubor, mapovaný soubor atd.)
AvailableSwap | Nevyužité místo odkládacího souboru (MiB)
PercentAvailableSwap | Nevyužité místo odkládacího souboru jako procento z celkového zahození
UsedSwap | Odkládací místo (MiB) v aplikaci
PercentUsedSwap | Použít místo odkládacího souboru jako procento z celkového zahození

Tato třída metrik má pouze jednu instanci. Atribut Condition nemá žádná užitečná nastavení a měla by být vynechána.

### <a name="builtin-metrics-for-the-network-class"></a>předdefinované metriky pro třídu sítě

Třída Network metriky poskytuje informace o aktivitě sítě v jednotlivých síťových rozhraních od spuštění. LAD nevystavuje metriky šířky pásma, které se dají načíst z metrik hostitelů.

counter | Význam
------- | -------
BytesTransmitted | Celkový počet odeslaných bajtů od spuštění
BytesReceived | Celkový počet přijatých bajtů od spuštění
BytesTotal | Celkový počet odeslaných nebo přijatých bajtů od spuštění
PacketsTransmitted | Celkový počet odeslaných paketů od spuštění
PacketsReceived | Celkový počet přijatých paketů od spuštění
TotalRxErrors | Počet chyb přijetí od spuštění
TotalTxErrors | Počet chyb při odesílání od spuštění
TotalCollisions | Počet kolizí hlášených síťovými porty od spuštění

 I když je tato třída instance, LAD nepodporuje zachycení síťových metrik agregovaných napříč všemi síťovými zařízeními. Chcete-li získat metriky pro určité rozhraní, například eth0, nastavte `"condition": "InstanceID=\\"eth0\\""` .

### <a name="builtin-metrics-for-the-filesystem-class"></a>předdefinované metriky pro třídu FileSystem

Třída FileSystem metrik poskytuje informace o využití systému souborů. Absolutní a procentuální hodnoty jsou hlášeny tak, jak by se zobrazily běžnému uživateli (ne root).

counter | Význam
------- | -------
FreeSpace | Volné místo na disku v bajtech
UsedSpace | Využité místo na disku v bajtech
PercentFreeSpace | Procento volného místa
PercentUsedSpace | Procento využitého místa
PercentFreeInodes | Procento nepoužívaných uzlů inode
PercentUsedInodes | Procentuální podíl přiděleného (používaného) uzlů inode ve všech systémech souborů
BytesReadPerSecond | Přečtené bajty za sekundu
BytesWrittenPerSecond | Zapsané bajty za sekundu
BytesPerSecond | Přečtené nebo zapsané bajty za sekundu
ReadsPerSecond | Operace čtení za sekundu
WritesPerSecond | Operace zápisu za sekundu
TransfersPerSecond | Operace čtení nebo zápisu za sekundu

Agregované hodnoty napříč všemi systémy souborů lze získat nastavením `"condition": "IsAggregate=True"` . Hodnoty pro konkrétní připojený systém souborů, jako je například "/mnt", lze získat nastavením `"condition": 'Name="/mnt"'` . 

> [!NOTE]
> Pokud místo JSON použijete Azure Portal, je ve správném formuláři pole podmínka název = '/mnt '.

### <a name="builtin-metrics-for-the-disk-class"></a>předdefinované metriky pro třídu disku

Disková třída metrik nabízí informace o využití diskového zařízení. Tyto statistiky se vztahují na celou jednotku. V případě, že je v zařízení více systémů souborů, jsou čítače pro toto zařízení efektivně agregované napříč všemi.

counter | Význam
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

Agregované hodnoty ve všech discích lze získat nastavením `"condition": "IsAggregate=True"` . Chcete-li získat informace pro konkrétní zařízení (například/dev/sdf1), nastavte `"condition": "Name=\\"/dev/sdf1\\""` .

## <a name="installing-and-configuring-lad-30"></a>Instalace a konfigurace LAD 3,0

### <a name="azure-cli"></a>Azure CLI

Za předpokladu, že vaše chráněná nastavení jsou v souboru ProtectedSettings.jsna a informace o vaší veřejné konfiguraci jsou PublicSettings.jsna, spusťte tento příkaz:

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

Příkaz předpokládá, že používáte režim správy prostředků Azure Azure CLI. Pokud chcete nakonfigurovat LAD pro virtuální počítače s modelem nasazení Classic, přepněte do režimu ASM ( `azure config mode asm` ) a vynechejte název skupiny prostředků v příkazu. Další informace najdete v dokumentaci k rozhraní příkazového [řádku pro více platforem](/cli/azure/authenticate-azure-cli).

### <a name="powershell"></a>PowerShell

Za předpokladu, že vaše chráněná nastavení jsou v `$protectedSettings` proměnné a informace o vaší veřejné konfiguraci jsou v `$publicSettings` proměnné, spusťte tento příkaz:

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="an-example-lad-30-configuration"></a>Příklad konfigurace LAD 3,0

V závislosti na předchozích definicích najdete ukázkovou konfiguraci rozšíření LAD 3,0 s některými vysvětleními. Pokud chcete tuto ukázku použít pro váš případ, měli byste použít vlastní název účtu úložiště, token SAS účtu a EventHubs tokeny SAS.

> [!NOTE]
> V závislosti na tom, jestli k instalaci LAD použijete rozhraní příkazového řádku Azure CLI nebo PowerShell, se metoda pro zajištění veřejného a chráněného nastavení bude lišit. Pokud používáte rozhraní příkazového řádku Azure CLI, uložte následující nastavení, abyste ProtectedSettings.jsv a PublicSettings.jsna použití s výše uvedeným vzorovým příkazem. Pokud používáte PowerShell, uložte nastavení do `$protectedSettings` a `$publicSettings` spuštěním `$protectedSettings = '{ ... }'` .

### <a name="protected-settings"></a>Chráněná nastavení

Tato chráněná nastavení se konfigurují:

* účet úložiště
* token SAS odpovídajícího účtu
* několik umyvadel (JsonBlob nebo EventHubs s tokeny SAS)

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

Tato veřejná nastavení způsobí, že LAD:

* Odeslat metriku procenta – čas procesoru a využité místo na disku pro `WADMetrics*` tabulku
* Nahrajte do tabulky zprávy ze zařízení syslog "User" a závažnost "info". `LinuxSyslog*`
* Nahrání nezpracovaných výsledků dotazu OMI (PercentProcessorTime a PercentIdleTime) do pojmenované `LinuxCPU` tabulky
* Nahrát připojené řádky do souboru `/var/log/myladtestlog` do `MyLadTestLog` tabulky

V každém případě se data nahrají taky do:

* Úložiště objektů BLOB v Azure (název kontejneru je definovaný v jímky JsonBlob)
* Koncový bod EventHubs (zadaný v jímky EventHubs)

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

* Grafy metrik a výstrahy na platformě Azure ví o resourceId virtuálního počítače, na kterém právě pracujete. Očekává, že se data pro virtuální počítač vyhledají pomocí vyhledávacího klíče resourceId.
* Pokud používáte automatické škálování Azure, musí resourceId v konfiguraci automatického škálování odpovídat ID resourceId, které používá LAD.
* ResourceId je integrováno do názvů JsonBlobs zapsaných pomocí LAD.

## <a name="view-your-data"></a>Zobrazení dat

Pomocí Azure Portal zobrazte data výkonu nebo nastavte výstrahy:

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="Snímek obrazovky zobrazuje Azure Portal s využitým místem na disku u vybrané metriky a výsledným grafem.":::

`performanceCounters`Data jsou vždy uložena v Azure Storage tabulce. Rozhraní API pro Azure Storage jsou k dispozici pro mnoho jazyků a platforem.

Data odesílaná do jímky JsonBlob se ukládají v objektech blob v účtu úložiště s názvem v části [chráněná nastavení](#protected-settings). Data objektů blob můžete využívat pomocí libovolných rozhraní API Azure Blob Storage.

Kromě toho můžete použít tyto nástroje uživatelského rozhraní pro přístup k datům v Azure Storage:

* Průzkumník serveru sady Visual Studio.
* [Snímek obrazovky ukazuje kontejnery a tabulky v Průzkumník služby Azure Storage.](https://azurestorageexplorer.codeplex.com/ "Průzkumník služby Azure Storage").

Tento snímek relace Průzkumník služby Microsoft Azure Storage zobrazuje vygenerované Azure Storage tabulky a kontejnery ze správně nakonfigurovaného rozšíření LAD 3,0 na testovacím virtuálním počítači. Obrázek se přesně neshoduje s [ukázkovou konfigurací LAD 3,0](#an-example-lad-30-configuration).

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="Snímek obrazovky ukazuje Průzkumník služby Azure Storage.":::


V příslušné [dokumentaci k EventHubs](../../event-hubs/event-hubs-about.md) se dozvíte, jak využívat zprávy publikované do koncového bodu EventHubs.

## <a name="next-steps"></a>Další kroky

* Vytvořte výstrahy metriky v [Azure monitor](../../azure-monitor/alerts/alerts-classic-portal.md) pro metriky, které shromažďujete.
* Vytvořte [grafy monitorování](../../azure-monitor/data-platform.md) pro vaše metriky.
* Naučte se [vytvořit sadu škálování virtuálního počítače](../linux/tutorial-create-vmss.md) pomocí vašich metrik k řízení automatického škálování.