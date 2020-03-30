---
title: Azure Compute – rozšíření diagnostiky Linuxu
description: Jak nakonfigurovat Azure Linux Diagnostic Extension (LAD) shromažďovat metriky a protokolovat události z virtuálních aplikací Linux běží v Azure.
services: virtual-machines-linux
author: axayjo
manager: gwallace
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 12/13/2018
ms.author: akjosh
ms.openlocfilehash: 7a7c1af1193ba391550438229a22c4a8c116e6be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289171"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Použití diagnostického rozšíření Linuxu k monitorování metrik a protokolů

Tento dokument popisuje verzi 3.0 a novější linuxové diagnostické rozšíření.

> [!IMPORTANT]
> Informace o verzi 2.3 a starší naleznete v [tomto dokumentu](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Úvod

Linux Diagnostické rozšíření pomáhá uživateli sledovat stav virtuálního počítače s Linuxem běží na Microsoft Azure. Má následující možnosti:

* Shromažďuje metriky výkonu systému z virtuálního účtu a ukládá je v konkrétní tabulce v určeném účtu úložiště.
* Načte události protokolu ze syslogu a uloží je do určité tabulky v určeném účtu úložiště.
* Umožňuje uživatelům přizpůsobit metriky dat, které jsou shromažďovány a nahrávány.
* Umožňuje uživatelům přizpůsobit zařízení syslogu a úrovně závažnosti událostí, které jsou shromažďovány a nahrány.
* Umožňuje uživatelům nahrávat zadané soubory protokolu do určené tabulky úložiště.
* Podporuje odesílání metrik a událostí protokolu do libovolných koncových bodů EventHub a objektů BLOB ve formátu JSON v určeném účtu úložiště.

Toto rozšíření funguje s oběma modely nasazení Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalace rozšíření na virtuálním počítači

Toto rozšíření můžete povolit pomocí rutin Azure PowerShell, skriptů Azure CLI, šablon ARM nebo portálu Azure. Další informace naleznete v [tématu Funkce rozšíření](features-linux.md).

Tyto pokyny k instalaci a [konfigurace ukázkové konfigurace ke stažení](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) nakonfigurují lad 3.0 na:

* zachycovat a uchovávat stejné metriky, které poskytl LAD 2.3;
* zachytit užitečnou sadu metrik systému souborů, nové na LAD 3.0;
* zachytit výchozí kolekci syslogu povolenou ladem 2.3;
* povolit prostředí portálu Azure pro vytváření grafů a upozorňování na metriky virtuálních her.

Konfigurace ke stažení je jen příklad; upravit tak, aby vyhovovalvašim potřebám.

### <a name="prerequisites"></a>Požadavky

* **Azure Linux Agent verze 2.2.0 nebo novější**. Většina ihráz galerie Azure VM Linux obsahuje verzi 2.2.7 nebo novější. Spuštěním `/usr/sbin/waagent -version` potvrďte verzi nainstalovanou na virtuálním počítači. Pokud virtuální počítač používá starší verzi agenta hosta, aktualizujte [ho podle těchto pokynů.](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent)
* **Azure CLI**. [Nastavte prostředí Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) na vašem počítači.
* Příkaz wget, pokud jej ještě nemáte: `sudo apt-get install wget`Spustit .
* Existující předplatné Azure a existující účet úložiště v něm pro ukládání dat.
* Seznam podporovaných distribucí Linuxu je zapnutýhttps://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Instalace vzorku

Před spuštěním vyplňte správné hodnoty proměnných v první části:

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

# Finallly tell Azure to install and enable the extension
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

Ukázková konfigurace stažená v těchto příkladech shromažďuje sadu standardních dat a odesílá je do úložiště tabulky. Adresa URL ukázkové konfigurace a její obsah se může změnit. Ve většině případů byste měli stáhnout kopii souboru JSON nastavení portálu a přizpůsobit jej podle vašich potřeb, pak mít všechny šablony nebo automatizace, které vytvoříte, použijte vlastní verzi konfiguračního souboru, nikoli pokaždé, než stáhnout tuto adresu URL pokaždé.

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
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally install the extension with the settings built above
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="updating-the-extension-settings"></a>Aktualizace nastavení rozšíření

Po změně chráněného nebo veřejného nastavení je nasaďte do virtuálního počítače spuštěním stejného příkazu. Pokud se v nastavení něco změní, aktualizované nastavení se odešle do rozšíření. LAD znovu načte konfiguraci a restartuje sám.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migrace z předchozích verzí rozšíření

Nejnovější verze rozšíření je **3.0**. **Všechny staré verze (2.x) jsou zastaralé a mohou být nepublikovány 31.**

> [!IMPORTANT]
> Toto rozšíření zavádí narušující změny konfigurace rozšíření. Jedna taková změna byla provedena s cílem zlepšit bezpečnost rozšíření; v důsledku toho nelze zachovat zpětnou kompatibilitu s 2.x. Vydavatel rozšíření pro toto rozšíření se také liší od vydavatele pro verze 2.x.
>
> Chcete-li migrovat z 2.x na tuto novou verzi rozšíření, musíte odinstalovat staré rozšíření (pod starým názvem vydavatele) a nainstalovat verzi 3 rozšíření.

Doporučení:

* Nainstalujte rozšíření s povoleným automatickým upgradem dílčí verze.
  * Na klasické nasazení modelu virtuálních počítače, zadejte '3.*' jako verzi, pokud instalujete rozšíření prostřednictvím Azure XPLAT CLI nebo Powershell.
  * Na virtuálních počítačích nasazení Azure Resource Manager uvázli do šablony nasazení virtuálního počítače "autoUpgradeMinorVersion": true.
* Použijte nový/jiný účet úložiště pro LAD 3.0. Existuje několik malých nekompatibility mezi LAD 2.3 a LAD 3.0, které dělají sdílení účtu problematické:
  * LAD 3.0 ukládá události syslogu v tabulce s jiným názvem.
  * Řetězce counterSpecifier pro `builtin` metriky se liší v LAD 3.0.

## <a name="protected-settings"></a>Chráněná nastavení

Tato sada informací o konfiguraci obsahuje citlivé informace, které by měly být chráněny před veřejným zobrazením, například pověření úložiště. Tato nastavení jsou přenášena a uložena příponou v zašifrované podobě.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Name (Název) | Hodnota
---- | -----
storageAccountName | Název účtu úložiště, ve kterém jsou data zapsána rozšířením.
storageAccountEndPoint | (nepovinné) Koncový bod identifikující cloud, ve kterém existuje účet úložiště. Pokud toto nastavení chybí, LAD výchozí azure `https://core.windows.net`veřejného cloudu , . Pokud chcete použít účet úložiště v Azure Germany, Azure Government nebo Azure China, nastavte tuto hodnotu odpovídajícím způsobem.
storageAccountSasToken | [Token SAS účtu](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) pro služby`ss='bt'`blob a table`srt='co'`services ( ), který se vztahuje na kontejnery a objekty ( ), který uděluje oprávnění k přidání, vytvoření, seznamu, aktualizaci a zápisu (`sp='acluw'`). *Nezahrnejte* hlavní otazník (?).
mdsdHttpProxy | (nepovinné) Informace http proxy potřebné k povolení rozšíření pro připojení k zadanému účtu úložiště a koncovému bodu.
sinksConfig | (nepovinné) Podrobnosti o alternativních destinacích, do kterých lze doručovat metriky a události. Konkrétní podrobnosti o každé jímky dat podporované rozšíření jsou popsány v následujících částech.

Chcete-li získat token SAS v rámci šablony Správce prostředků, použijte funkci **listAccountSas.** Ukázkovou šablonu naleznete v [příkladu funkce List](../../azure-resource-manager/templates/template-functions-resource.md#list-example).

Požadovaný token SAS můžete snadno vytvořit prostřednictvím portálu Azure.

1. Vyberte účet úložiště pro obecné účely, do kterého má rozšíření psát.
1. V levé nabídce vyberte možnost "Sdílený přístupový podpis" v levé nabídce Nastavení.
1. Vytvořte příslušné oddíly, jak bylo popsáno výše
1. Klikněte na tlačítko "Generovat SAS".

![image](./media/diagnostics-linux/make_sas.png)

Zkopírujte vygenerovaný SAS do pole storageAccountSasToken. hlavní otazník ("?").

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

Tato volitelná část definuje další cíle, do kterých rozšíření odesílá informace, které shromažďuje. Pole "jímky" obsahuje objekt pro každý další jímky dat. Atribut "type" určuje ostatní atributy v objektu.

Element | Hodnota
------- | -----
jméno | Řetězec používaný k odkazování na tento jímky jinde v konfiguraci rozšíření.
type | Typ jímky, které jsou definovány. Určuje další hodnoty (pokud existují) v instancích tohoto typu.

Verze 3.0 linuxového diagnostického rozšíření podporuje dva typy jímky: EventHub a JsonBlob.

#### <a name="the-eventhub-sink"></a>Umyvadlo EventHub

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

Položka "sasURL" obsahuje úplnou adresu URL, včetně tokenu SAS pro centrum událostí, do kterého by měla být data publikována. LAD vyžaduje SAS pojmenování zásady, která umožňuje odeslat deklaraci. Příklad:

* Vytvoření oboru názvů Event Hubs s názvem`contosohub`
* Vytvoření centra událostí v oboru názvů nazývaném`syslogmsgs`
* Vytvoření zásady sdíleného přístupu `writer` v centru událostí s názvem, která umožňuje deklaraci odeslat

Pokud jste vytvořili sazi do půlnoci UTC 1.

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Další informace o generování a načítání informací o tokenech SAS pro centra událostí naleznete na [této webové stránce](https://docs.microsoft.com/rest/api/eventhub/generate-sas-token#powershell).

#### <a name="the-jsonblob-sink"></a>Umyvadlo JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Data směrovaná na jímky JsonBlob se ukládají v objektech BLOB ve službě Azure. Každá instance LAD vytvoří objekt blob každou hodinu pro každý název jímky. Každý objekt blob vždy obsahuje syntakticky platné pole JSON objektu. Nové položky jsou atomicky přidány do pole. Objekty BLOB jsou uloženy v kontejneru se stejným názvem jako jímky. Pravidla úložiště Azure pro názvy kontejnerů objektů blob platí pro názvy jímky JsonBlob: mezi 3 a 63 malá alfanumerická ascii znaky nebo pomlčky.

## <a name="public-settings"></a>Veřejná nastavení

Tato struktura obsahuje různé bloky nastavení, které řídí informace shromážděné rozšíření. Každé nastavení je volitelné. Pokud zadáte `ladCfg`, musíte `StorageAccount`také zadat .

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

Element | Hodnota
------- | -----
StorageAccount | Název účtu úložiště, ve kterém jsou data zapsána rozšířením. Musí mít stejný název, který je zadán v [nastavení Protected](#protected-settings).
mdsdHttpProxy | (nepovinné) Stejné jako v [nastavení Protected](#protected-settings). Veřejná hodnota je přepsána soukromou hodnotou, pokud je nastavena. Do [nastavení Chráněné](#protected-settings)umístěte nastavení serveru proxy, které obsahuje tajný klíč, například heslo.

Zbývající prvky jsou podrobně popsány v následujících částech.

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

Tato volitelná struktura řídí shromažďování metrik a protokolů pro doručení do služby Azure Metrics a do dalších datových propadů. Je nutné `performanceCounters` zadat `syslogEvents` buď nebo nebo obojí. Je nutné `metrics` zadat strukturu.

Element | Hodnota
------- | -----
eventVolume | (nepovinné) Určuje počet oddílů vytvořených v tabulce úložiště. Musí se `"Large"`jednat `"Medium"`o `"Small"`soubor , , nebo . Pokud není zadán, výchozí `"Medium"`hodnota je .
sampleRateInSeconds | (nepovinné) Výchozí interval mezi shromažďováním nezpracovaných (neagregovaných) metrik. Nejmenší podporovaná vzorkovací frekvence je 15 sekund. Pokud není zadán, výchozí `15`hodnota je .

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

Element | Hodnota
------- | -----
resourceId | ID prostředku Azure Resource Manager virtuálního počítače nebo škálovací sady virtuálního počítače, do které virtuální počítač patří. Toto nastavení musí být také zadáno, pokud je v konfiguraci použita jakákoli jímka JsonBlob.
scheduledTransferPeriod | Frekvence, s jakou se mají vypočítat a přenést do metrik Azure, vyjádřená jako časový interval IS 8601. Nejmenší doba přenosu je 60 sekund, to znamená PT1M. Je nutné zadat alespoň jeden scheduledTransferPeriod.

Vzorky metriky zadané v části performanceCounters jsou shromažďovány každých 15 sekund nebo na vzorkovací rychlost explicitně definované pro čítač. Pokud se zobrazí více scheduledTransferPeriod frekvence (jako v příkladu), každá agregace se vypočítá nezávisle.

#### <a name="performancecounters"></a>performanceCounters

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

Tato volitelná část řídí shromažďování metrik. Nezpracovaná vzorky jsou agregovány pro každý [scheduledTransferPeriod](#metrics) k vytvoření těchto hodnot:

* mean
* Minimum
* Maximum
* naposledy shromážděná hodnota
* počet nezpracovaných vzorků použitých k výpočtu agregátu

Element | Hodnota
------- | -----
Propadů | (nepovinné) Seznam názvů jímek oddělených čárkami, do kterého lad odesílá agregované výsledky metriky. Všechny agregované metriky jsou publikovány do každé uvedené jímky. Viz [sinksConfig](#sinksconfig). Příklad: `"EHsink1, myjsonsink"`.
type | Identifikuje skutečného zprostředkovatele metriky.
třída | Spolu s "čítač", identifikuje konkrétní metriku v rámci oboru názvů zprostředkovatele.
counter | Spolu s "class", identifikuje konkrétní metriku v rámci oboru názvů zprostředkovatele.
counterSpecifier | Identifikuje konkrétní metriku v oboru názvů Azure Metrics.
Podmínka | (nepovinné) Vybere konkrétní instanci objektu, na který se metrika vztahuje, nebo vybere agregaci ve všech instancích tohoto objektu. Další informace naleznete `builtin` v definicích metrik.
sampleRate | Interval IS 8601, který nastavuje rychlost shromažďování nezpracovaných vzorků pro tuto metriku. Pokud není nastavena, interval sběru je nastaven a hodnota [sampleRateInSeconds](#ladcfg). Nejkratší podporovaná vzorkovací frekvence je 15 sekund (PT15S).
unit | By měl být jeden z těchto řetězců: "Count", "Bajty", "Seconds", "Percent", "CountPerSecond", "BytesPerSecond", "Millisecond". Definuje jednotku pro metriku. Spotřebitelé shromážděných dat očekávají, že hodnoty shromážděných dat budou odpovídat této jednotce. LAD ignoruje toto pole.
displayName | Popisek (v jazyce určeném přidruženým nastavením národního prostředí), který se má k tato datům připojit v Azure Metrics. LAD ignoruje toto pole.

CounterSpecifier je libovolný identifikátor. Spotřebitelé metrik, jako je azure portál ugrafování a upozornění funkce, použijte counterSpecifier jako "klíč", který identifikuje metriku nebo instanci metriky. Pro `builtin` metriky doporučujeme použít hodnoty counterSpecifier `/builtin/`začínající na . Pokud shromažďujete konkrétní instanci metriky, doporučujeme připojit identifikátor instance k hodnotě counterSpecifier. Několik příkladů:

* `/builtin/Processor/PercentIdleTime`- Doba nečinnosti zprůměrovaná napříč všemi virtuálními procesory
* `/builtin/Disk/FreeSpace(/mnt)`- Volné místo pro souborový systém /mnt
* `/builtin/Disk/FreeSpace`- Volný prostor zprůměrovaný ve všech připojených souborových systémech

Lad ani portál Azure očekává, že hodnota counterSpecifier bude odpovídat jakémukoli vzoru. Buďte konzistentní v tom, jak vytvářet hodnoty counterSpecifier.

Když zadáte `performanceCounters`, LAD vždy zapisuje data do tabulky v úložišti Azure. Můžete mít stejná data zapsaná do objektů BLOB JSON nebo centra událostí, ale nelze zakázat ukládání dat do tabulky. Všechny instance diagnostického rozšíření nakonfigurovaného tak, aby používaly stejný název účtu úložiště a koncový bod, přidávají své metriky a protokoly do stejné tabulky. Pokud příliš mnoho virtuálních počítačích zapisují do stejného oddílu tabulky, Azure může omezit zápisy do tohoto oddílu. EventVolume nastavení způsobí, že položky, které mají být rozloženy do 1 (Malé), 10 (Střední) nebo 100 (Velké) různé oddíly. Obvykle je "Střední" dostatečné k tomu, aby se zajistilo, že provoz není omezen. Funkce Azure Metrics na webu Azure Portal používá data v této tabulce k vytváření grafů nebo k aktivaci výstrah. Název tabulky je zřetězení těchto řetězců:

* `WADMetrics`
* "ScheduledTransferPeriod" pro agregované hodnoty uložené v tabulce
* `P10DV2S`
* Datum ve tvaru "YYYYMMDD", které se mění každých 10 dní

Příklady `WADMetricsPT1HP10DV2S20170410` zahrnují `WADMetricsPT1MP10DV2S20170609`a .

#### <a name="syslogevents"></a>syslogUdálosti

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

Tato volitelná část řídí shromažďování událostí protokolu ze syslogu. Pokud je oddíl vynechán, události syslogu nejsou zachyceny vůbec.

Kolekce syslogEventConfiguration má jednu položku pro každé zařízení syslog u zájmů. Pokud minZávažnost je "NONE" pro určité zařízení, nebo pokud toto zařízení není v prvku vůbec, žádné události z tohoto zařízení jsou zachyceny.

Element | Hodnota
------- | -----
Propadů | Seznam názvů jímek oddělených čárkami, do kterých jsou publikovány jednotlivé události protokolu. Všechny události protokolu odpovídající omezení v syslogEventConfiguration jsou publikovány do každé uvedené jímky. Příklad: "EHforsyslog"
název zařízení | Název zařízení syslogu (například "LOG\_\_USER" nebo "LOG LOCAL0"). Úplný seznam naleznete v části "zařízení" na [stránce syslog man.](http://man7.org/linux/man-pages/man3/syslog.3.html)
minZávažnost | Úroveň závažnosti protokolu syslogu\_(například "LOG\_ERR" nebo "LOG INFO"). Úplný seznam naleznete v části "úroveň" na [stránce syslog man.](http://man7.org/linux/man-pages/man3/syslog.3.html) Rozšíření zachycuje události odeslané do zařízení na nebo nad zadanou úroveň.

Když zadáte `syslogEvents`, LAD vždy zapisuje data do tabulky v úložišti Azure. Můžete mít stejná data zapsaná do objektů BLOB JSON nebo centra událostí, ale nelze zakázat ukládání dat do tabulky. Chování dělení pro tuto tabulku je stejné `performanceCounters`jako popsané pro . Název tabulky je zřetězení těchto řetězců:

* `LinuxSyslog`
* Datum ve tvaru "YYYYMMDD", které se mění každých 10 dní

Příklady `LinuxSyslog20170410` zahrnují `LinuxSyslog20170609`a .

### <a name="perfcfg"></a>perfCfg

Tato volitelná část řídí provádění libovolných dotazů [OMI.](https://github.com/Microsoft/omi)

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

Element | Hodnota
------- | -----
namespace | (nepovinné) Obor názvů OMI, ve kterém by měl být proveden dotaz. Pokud není zadán, výchozí hodnota je "root/scx", implementovaný [zprostředkovateli mezi platformami System Center](https://github.com/Microsoft/SCXcore).
query | Dotaz OMI, který má být proveden.
tabulka | (nepovinné) Tabulka úložiště Azure v určeném účtu úložiště (viz [Chráněné nastavení](#protected-settings)).
frequency | (nepovinné) Počet sekund mezi spuštěním dotazu. Výchozí hodnota je 300 (5 minut); minimální hodnota je 15 sekund.
Propadů | (nepovinné) Seznam názvů dalších jímek oddělených čárkami, na které by měly být publikovány výsledky nezpracovaných ukázkových metrik. Žádné agregace těchto nezpracovaných vzorků se vypočítá v rozšíření nebo metriky Azure.

Musí být zadáno "tabulka" nebo "umyvadla", nebo obojí.

### <a name="filelogs"></a>fileLogs

Řídí sběr souborů protokolu. LAD zachytí nové řádky textu, jak jsou zapsány do souboru a zapíše je do řádků tabulky nebo jakékoli zadané jímky (JsonBlob nebo EventHub).

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

Element | Hodnota
------- | -----
 – soubor | Úplný název cesty souboru protokolu, který má být sledován a zachycen. Název cesty musí pojmenovat jeden soubor; nemůže pojmenovat adresář nebo obsahovat zástupné znaky.
tabulka | (nepovinné) Tabulka úložiště Azure v určeném účtu úložiště (jak je uvedeno v chráněné konfiguraci), do kterého jsou zapsány nové řádky z "ocasu" souboru.
Propadů | (nepovinné) Seznam názvů dalších jímek oddělených čárkami, do kterého byly odeslány řádky protokolu.

Musí být zadáno "tabulka" nebo "umyvadla", nebo obojí.

## <a name="metrics-supported-by-the-builtin-provider"></a>Metriky podporované integrovaným poskytovatelem

Poskytovatel integrované metriky je zdrojem metrik, které jsou pro širokou skupinu uživatelů nejzajímavější. Tyto metriky spadají do pěti širokých tříd:

* Procesor
* Memory (Paměť)
* Network (Síť)
* Filesystem
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>integrované metriky pro třídu Procesor

Procesor třída metriky poskytuje informace o využití procesoru ve virtuálním počítače. Při agregaci procent je výsledkem průměr napříč všemi procesory. V virtuálním počítače se dvěma virtuálními procesory, pokud jeden virtuální procesor byl 100 % zaneprázdněný a druhý byl 100 % nečinný, ohlášené PercentIdleTime by bylo 50. Pokud každý virtuální procesor byl 50 % zaneprázdněn za stejné období, vykázaný výsledek by také 50. Ve virtuálním počítače se čtyřmi virtuálními procesory s jedním virtuálním procesorem 100 % zaneprázdněným a ostatními nečinnými by ohlášené procento IdleTime bylo 75.

counter | Význam
------- | -------
PercentIdleTime | Procento času během okna agregace, kdy procesory prováděly nečinnosti jádra
Procento času procesoru | Procento času provádění vlákna, který není nečinný
Percentiočekací čas | Procento času čekání na dokončení operací vi
Procento přerušeníČasu | Procento času provádění hardwarových/softwarových přerušení a DPC (odložená volání procedur)
PercentUserTime | Z doby nečinnosti během okna agregace procento času stráveného v uživateli více při normální prioritě
PercentNiceTime | Z doby bez nečinnosti je procento strávené se sníženou (pěknou) prioritou
PercentPrivilegedTime | Procento strávené v privilegovaném (jádrovém) režimu, které není nečinný, je vyčerpáno v privilegovaném režimu (jádra).

První čtyři čítače by měly sčítat na 100%. Poslední tři čítače také součet 100%; rozdělují součet PercentProcessorTime, PercentIOWaitTime a PercentInterruptTime.

Chcete-li získat jednu metriku agregolivnou ve všech procesorech, nastavte `"condition": "IsAggregate=TRUE"`. Chcete-li získat metriku pro konkrétní procesor, jako je například druhý `"condition": "Name=\\"1\\""`logický procesor virtuálního počítače se čtyřmi virtuálními procesory, nastavte . Čísla logického procesoru `[0..n-1]`jsou v rozsahu .

### <a name="builtin-metrics-for-the-memory-class"></a>integrované metriky pro třídu Memory

Memory třída metriky poskytuje informace o využití paměti, stránkování a prohození.

counter | Význam
------- | -------
AvailableMemory | Dostupná fyzická paměť v MiB
PercentavailableMemory | Dostupná fyzická paměť jako procento celkové paměti
Použitá paměť | Fyzická paměť v provozu (MiB)
PercentUsedMemory | Nevyužitá fyzická paměť jako procento celkové paměti
Pagespersec | Celkový počet stránkování (čtení a zápis)
PagesReadPerSec | Stránky přečtené z podkladového úložiště (odkládací soubor, programový soubor, mapovaný soubor atd.)
PagesWrittenPerSec | Stránky zapsané do záložního úložiště (odkládací soubor, mapovaný soubor atd.)
Dostupný swap | Nevyužitý odkládací prostor (MiB)
PercentAvailableSwap | Nevyužitý odkládací prostor jako procento z celkového swapu
Použitý swap | Vyměnitmísto v provozu (MiB)
PercentUsedSwap | Vyměnitmísto v provozu jako procento z celkového swapu

Tato třída metrikmá pouze jednu instanci. Atribut "condition" nemá žádné užitečné nastavení a měl by být vynechán.

### <a name="builtin-metrics-for-the-network-class"></a>integrované metriky pro třídu Network

Síťová třída metrik poskytuje informace o síťové aktivitě na jednotlivých síťových rozhraních od spuštění. LAD nezveřejňuje metriky šířky pásma, které lze načíst z metriky hostitele.

counter | Význam
------- | -------
Přenesené bajty | Celkový počet bajtů odeslaných od spuštění
Přijaté bajty | Celkový počet bajtů přijatých od spuštění
BajtůCelkm | Celkový počet bajtů odeslaných nebo přijatých od spuštění
Pakety přenášené | Celkový počet paketů odeslaných od spuštění
Přijaté pakety | Celkový počet přijatých paketů od spuštění
Chyby TotalRx | Počet chyb příjmu od spuštění
TotalTxErrors | Počet chyb přenosu od spuštění
TotalCollisions | Počet kolizí hlášených síťovými porty od spuštění

 Přestože je tato třída instance, LAD nepodporuje zachytávání síťové metriky agregované ve všech síťových zařízeních. Chcete-li získat metriky pro konkrétní rozhraní, jako `"condition": "InstanceID=\\"eth0\\""`je například eth0, nastavte .

### <a name="builtin-metrics-for-the-filesystem-class"></a>integrované metriky pro třídu Souborový systém

Třída metrik souborového systému poskytuje informace o využití souborového systému. Absolutní a procentuální hodnoty jsou hlášeny tak, jak by se zobrazily běžnému uživateli (nikoli kořenovému adresáři).

counter | Význam
------- | -------
FreeSpace | Dostupné místo na disku v bajtech
Použitý prostor | Využité místo na disku v bajtech
PercentFreeSpace | Procento volného místa
Procento usedspace | Procento využité místo
PercentFreeInodes | Procento nevyužitých inodů
PercentUsedInodes | Procento přidělených (využívaných) inodů sečtených ve všech souborových systémech
BajtůReadPerSekunda | Čtení bajtů za sekundu
BajtůPsanýPerSecond | Bajty zapsané za sekundu
BajtyPerSekunda | Čtení nebo zápis bajtů za sekundu
Čtepersekunda | Operace čtení za sekundu
Zapisujepersekundu | Operace zápisu za sekundu
TransfersPerSecond | Čtení nebo zápis operací za sekundu

Agregované hodnoty ve všech souborových `"condition": "IsAggregate=True"`systémech lze získat nastavením . Hodnoty pro konkrétní připojený systém souborů, například "/mnt", lze získat nastavením `"condition": 'Name="/mnt"'`. 

**Poznámka:** Pokud používáte portál Azure místo JSON, správný formulář pole podmínky je Name='/mnt'

### <a name="builtin-metrics-for-the-disk-class"></a>integrované metriky pro třídu Disk

Disk třída metriky poskytuje informace o využití diskového zařízení. Tyto statistiky platí pro celou jednotku. Pokud je v zařízení více systémů souborů, čítače pro toto zařízení jsou efektivně agregovány ve všech těchto zařízeních.

counter | Význam
------- | -------
Čtepersekunda | Operace čtení za sekundu
Zapisujepersekundu | Operace zápisu za sekundu
TransfersPerSecond | Celkový počet operací za sekundu
Průměrný readtime | Průměrný počet sekund na operaci čtení
AverageWriteTime | Průměrný počet sekund na operaci zápisu
Průměrná doba přenosu | Průměrný počet sekund na operaci
Délka průměrné dobytky | Průměrný počet operací disku ve frontě
ReadBytesPerSecond | Počet přečtených bajtů za sekundu
WriteBytesPerSecond | Počet zapsaných bajtů za sekundu
BajtyPerSekunda | Počet přečtených nebo zapsaných bajtů za sekundu

Agregované hodnoty na všech discích lze získat nastavením `"condition": "IsAggregate=True"`. Chcete-li získat informace pro konkrétní zařízení (například /dev/sdf1), nastavte `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalace a konfigurace LAD 3.0 pomocí rozhraní příkazového řádku

Za předpokladu, že chráněná nastavení jsou v souboru PrivateConfig.json a vaše informace o veřejné konfiguraci jsou v souboru PublicConfig.json, spusťte tento příkaz:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

Příkaz předpokládá, že používáte režim správy prostředků Azure (rameno) v příkazovém příkazu k řešení Azure. Chcete-li nakonfigurovat virtuální počítače modelu LAD pro klasické nasazení (ASM), přepněte do režimu ASM (`azure config mode asm`) a vpříkazu vyneche název skupiny prostředků. Další informace naleznete v [dokumentaci k cli mezi platformami](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Příklad konfigurace LAD 3.0

Na základě předchozích definic, zde je ukázka LAD 3.0 rozšíření konfigurace s některými vysvětlení. Chcete-li použít tuto ukázku na váš případ, měli byste použít vlastní název účtu úložiště, token s účtem SAS a tokeny EventHubs SAS.

### <a name="privateconfigjson"></a>PrivateConfig.json

Tato soukromá nastavení konfigurují:

* účet úložiště
* odpovídající účet SAS token
* několik propadů (JsonBlob nebo EventHubs s tokeny SAS)

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

### <a name="publicconfigjson"></a>PublicConfig.json

Tato veřejná nastavení způsobují, že LAD:

* Nahrání metrik v procentech procesoru a využitém místě na `WADMetrics*` disku do tabulky
* Nahrání zpráv ze zařízení syslog "uživatel" a `LinuxSyslog*` "informace" o závažnosti do tabulky
* Nahrání nezpracovaných výsledků dotazu OMI (PercentProcessorTime a PercentIdleTime) do pojmenované `LinuxCPU` tabulky
* Nahrání připojených `/var/log/myladtestlog` řádků `MyLadTestLog` v souboru do tabulky

V každém případě jsou údaje nahrány také do:

* Úložiště objektů blob Azure (název kontejneru je definovaný v jímce JsonBlob)
* EventHubs koncový bod (jak je uvedeno v jímce EventHubs)

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

V `resourceId` konfiguraci musí odpovídat virtuálnímu počítači nebo škálovací sadě virtuálního počítače.

* Metriky platformy Azure zmapování a výstrahy zná resourceId virtuálního počítače, na kterém pracujete. Očekává, že najít data pro váš virtuální počítač pomocí resourceId vyhledávací klíč.
* Pokud používáte automatické škálování Azure, resourceId v konfiguraci automatického škálování musí odpovídat resourceId používá LAD.
* ResourceId je integrován do názvů JsonBlobs napsané LAD.

## <a name="view-your-data"></a>Zobrazení dat

Pomocí portálu Azure můžete zobrazit údaje o výkonu nebo nastavit výstrahy:

![image](./media/diagnostics-linux/graph_metrics.png)

Data `performanceCounters` se vždycky ukládají v tabulce Azure Storage. Rozhraní API azure storage jsou k dispozici pro mnoho jazyků a platforem.

Data odeslaná do jímek JsonBlob jsou uložena v objektech BLOB v účtu úložiště pojmenovaném v [nastavení Protected](#protected-settings). Data objektu blob můžete spotřebovat pomocí libovolných api úložiště objektů blob Azure.

Kromě toho můžete použít tyto nástroje uznatí pro přístup k datům ve službě Azure Storage:

* Průzkumník serveru Visual Studio.
* [Průzkumník úložiště Microsoft Azure](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Tento snímek relace Průzkumníka úložiště Microsoft Azure zobrazuje generované tabulky a kontejnery azure storage z správně nakonfigurované hodu 3.0 na testovacím virtuálním počítači. Obrázek se přesně neshoduje se [ukázkovou konfigurací LAD 3.0](#an-example-lad-30-configuration).

![image](./media/diagnostics-linux/stg_explorer.png)

Podívejte se na příslušnou [dokumentaci EventHubs,](../../event-hubs/event-hubs-what-is-event-hubs.md) kde se dozvíte, jak využívat zprávy publikované do koncového bodu EventHubs.

## <a name="next-steps"></a>Další kroky

* Ve Službě [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) můžete vytvářet upozornění na metriky, které shromažďujete.
* Vytvořte [grafy monitorování](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) pro metriky.
* Zjistěte, jak [vytvořit škálovací sadu virtuálních strojů](../linux/tutorial-create-vmss.md) pomocí metrik pro řízení automatického škálování.
