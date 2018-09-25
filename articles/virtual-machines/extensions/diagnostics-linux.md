---
title: Azure výpočetních služeb – rozšíření Linux Diagnostic | Dokumentace Microsoftu
description: Jak nakonfigurovat Azure Linux diagnostické rozšíření (LAD) ke shromažďování metrik a protokolování událostí z virtuálních počítačů s Linuxem v Azure.
services: virtual-machines-linux
author: abhijeetgaiha
manager: sankalpsoni
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/09/2017
ms.author: agaiha
ms.openlocfilehash: ac09754876d52798add58d9e0752d776ca29f247
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46994798"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Použití diagnostického rozšíření Linuxu pro monitorování metrik a protokolů

Tento dokument popisuje verzi 3.0 a novějších verzí rozšíření diagnostiky Linuxu.

> [!IMPORTANT]
> Informace o verzi 2.3 a starší, naleznete v tématu [tento dokument](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Úvod

Diagnostické rozšíření Linux pomáhá uživateli monitorování stavu virtuálního počítače s Linuxem, běžící v Microsoft Azure. Má následující možnosti:

* Shromažďuje metriky výkonu systému z virtuálního počítače a ukládá je do určité tabulky v účtu úložiště určený.
* Načte protokolu událostí z protokolu syslog a ukládá je do určité tabulky v účtu úložiště určený.
* Umožňuje uživatelům přizpůsobení, jež jsou shromážděna a nahráli data metriky.
* Umožňuje uživatelům přizpůsobit syslog zařízení a úrovně závažnosti události, které se shromažďují a nahrát.
* Umožňuje uživatelům odeslat zadané soubory protokolu do tabulky úložiště určené.
* Podporuje odesílání metrik a protokolů událostí do libovolného EventHub koncových bodů a ve formátu JSON objektů BLOB v účtu úložiště určený.

Toto rozšíření funguje s obou modelů nasazení Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalaci rozšíření ve virtuálním počítači

Toto rozšíření můžete povolit pomocí rutin Azure Powershellu, rozhraní příkazového řádku Azure skriptů nebo šablony nasazení Azure. Další informace najdete v tématu [rozšíření funkce](features-linux.md).

Pokud chcete povolit nebo nakonfigurovat LAD 3.0 nelze použít na webu Azure portal. Místo toho nainstaluje a nakonfiguruje verze 2.3. Azure portal grafů a výstrah pracovat s daty z obě verze rozšíření.

Tyto pokyny k instalaci a [ukázky ke stažení konfigurace](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) konfigurace LAD 3.0 na:

* zaznamenání a uložení stejné metriky, jako byly poskytnuty LAD 2.3;
* zachycení užitečné sadu souborů systémové metriky, Novinky ve verzi 3.0 LAD;
* zachycení shromažďování syslogu výchozí umožněné LAD 2.3;
* Povolte Azure portal ještě neznáte, grafů a upozornění na metriky virtuálního počítače.

Ke stažení konfigurace je uvedené jenom jako příklad; upravte jej podle svých potřeb.

### <a name="prerequisites"></a>Požadavky

* **Agent Azure Linux verze 2.2.0 nebo novější**. Většina Image z Galerie virtuálních počítačů Azure s Linuxem obsahují verzi 2.2.7 nebo novější. Spustit `/usr/sbin/waagent -version` k potvrzení verze nainstalovaná na virtuálním počítači. Pokud virtuální počítač běží starší verze agenta hosta, postupujte podle [tyto pokyny](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) ji aktualizovat.
* **Azure CLI**. [Nastavení rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) prostředí na svém počítači.
* Příkaz wget, pokud ještě nemáte ho: Spusťte `sudo apt-get install wget`.
* Stávající předplatné Azure a účet úložiště v rámci něj chcete uložit data.
* Seznam podporovaných Linuxových distribucích se na https://github.com/Azure/azure-linux-extensions/tree/master/Diagnostic#supported-linux-distributions

### <a name="sample-installation"></a>Ukázka instalace

Zadejte správné parametry na první tři řádky a potom spusťte tento skript jako kořenový adresář:

```bash
# Set your Azure VM diagnostic parameters correctly below
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

Adresa URL v ukázkové konfiguraci a její obsah se můžou změnit. Stáhnout kopii souboru JSON nastavení portálu a přizpůsobit vašim potřebám. Všechny šablony nebo automatizaci, které vytvoříte by měl použít vlastní kopii, nikoli stahování pokaždé, když tuto adresu URL.

### <a name="updating-the-extension-settings"></a>Aktualizuje se nastavení rozšíření

Po změně nastavení Protected nebo Public, nasadíte je do virtuálního počítače spuštěním ten samý příkaz. Pokud v nastavení nic nezměnilo, aktualizovaným nastavením odesílají do rozšíření. LAD znovu načte konfiguraci a restartuje samotný.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migrace z předchozích verzí rozšíření

Nejnovější verze tohoto rozšíření je **3.0**. **Všechny starší verze (2.x) jsou zastaralé a může se publikování nebo po 31. července 2018**.

> [!IMPORTANT]
> Toto rozšíření přináší změny způsobující chyby v konfiguraci rozšíření. Jeden takový změny ke zlepšení zabezpečení rozšíření; v důsledku toho zpětnou kompatibilitu s 2.x nelze provést údržbu. Vydavatel rozšíření pro toto rozšíření se také liší od vydavatele pro verze 2.x.
>
> Migrace z 2.x do této nové verzi rozšíření, je nutné odinstalovat stará rozšíření (v rámci původní název vydavatele) a potom nainstalovat verzi 3 rozšíření.

Doporučení:

* Instalace rozšíření vedlejší verze aktualizace automatického upgradu povolené.
  * V modelu nasazení classic virtuální počítače určete "3.*" jako verze při instalaci rozšíření prostřednictvím Azure XPLAT CLI nebo Powershellu.
  * Při nasazení Azure Resource Manageru model virtuálních počítačů, patří ""verzi autoUpgradeMinorVersion": true" v nasazení šablony virtuálního počítače.
* Pomocí nového nebo jiného účtu úložiště pro LAD 3.0. Nekompatibility několik malých mezi LAD 2.3 a LAD 3.0, které usnadňují sdílení problémových účet:
  * LAD 3.0 uloží události procesu syslog tabulky s jiným názvem.
  * Pro řetězce counterSpecifier `builtin` metriky se liší v LAD 3.0.

## <a name="protected-settings"></a>Chráněné nastavení

Tato sada konfiguračních informací obsahuje citlivé informace, které by měly být chráněné z veřejné zobrazení, například přihlašovací údaje úložiště. Tato nastavení jsou předány a uloženy v zašifrované podobě rozšíření.

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

Název | Hodnota
---- | -----
storageAccountName | Název účtu úložiště, ve kterém se data zapisují rozšíření.
storageAccountEndPoint | (volitelné) Koncový bod identifikace cloud, ve kterém se účet úložiště existuje. Pokud toto nastavení chybí, výchozí hodnota LAD veřejného cloudu Azure `https://core.windows.net`. Pokud chcete použít účet úložiště v Azure Germany, Azure Government nebo Azure China, nastavte tuto hodnotu odpovídajícím způsobem.
storageAccountSasToken | [Token SAS účtu](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) služby objektů Blob a tabulek (`ss='bt'`), platí pro kontejnery a objekty (`srt='co'`), která uděluje přidat, vytvářet, seznamu, aktualizovat a oprávnění k zápisu (`sp='acluw'`). Proveďte *není* zahrnují nejlepší otazník (?).
mdsdHttpProxy | (volitelné) Informace o proxy serveru HTTP potřebná k povolení rozšíření pro připojení k zadanému účtu úložiště a koncový bod.
sinksConfig | (volitelné) Podrobnosti o alternativní cíle, na které se dodávají metrik a událostí. Konkrétní podrobnosti o jednotlivých datová jímka nepodporuje rozšíření jsou popsané v následující části.


> [!NOTE]
> Při zavádění rozšíření s šablonou nasazení Azure, účet úložiště a SAS token musí vytvořit předem a poté předány do šablony. Nelze nasadit virtuální počítač, účet úložiště a konfiguraci rozšíření v jediné šabloně. Vytváří se token SAS v rámci šablony se momentálně nepodporuje.

Můžete snadno vytvořit požadovaný token SAS prostřednictvím webu Azure portal.

1. Vyberte účet úložiště pro obecné účely, do kterého má být rozšíření pro zápis
1. Vyberte "Sdílený přístupový podpis" v části Nastavení levé nabídky
1. Ujistěte se, jak je uvedeno výše příslušné části
1. Klikněte na tlačítko "Generovat SAS".

![image](./media/diagnostics-linux/make_sas.png)

Zkopírujte vygenerovaný SAS do pole storageAccountSasToken; Odeberte úvodní otazník ("?").

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

Tento volitelný oddíl definuje další cíle, na které rozšíření odešle informace, které shromažďuje. Pole "sink" obsahuje objekt pro každý další datová jímka. Atribut "type" Určuje další atributy objektu.

Element | Hodnota
------- | -----
jméno | Řetězec se používá k odkazování na tuto jímku jinde v konfiguraci rozšíření.
type | Typ jímky je definována. Určuje jiné hodnoty v instance tohoto typu (pokud existuje).

Diagnostické rozšíření Linux verze 3.0 podporuje dva typy jímky: EventHub a JsonBlob.

#### <a name="the-eventhub-sink"></a>Centra událostí jímky

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

Položka "sasURL" obsahuje úplnou adresu URL, včetně tokenu SAS pro Centrum událostí, ke kterému by se měly zveřejňovat data. LAD vyžaduje SAS pojmenování zásadu, která umožňuje odesílání deklarací identity. Příklad:

* Vytvořte obor názvů služby Event Hubs, volá se `contosohub`
* Vytvoření centra událostí v oboru názvů, které volá `syslogmsgs`
* Vytvoření zásady sdíleného přístupu v Centru událostí s názvem `writer` , který umožňuje odeslání deklarací identity

Pokud jste vytvořili SAS funkční až do půlnoci času UTC na 1. ledna 2018, může být hodnota sasURL:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Další informace o generování tokenů SAS pro službu Event Hubs najdete v tématu [tato webová stránka](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>JsonBlob jímky

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Data směrována do jímky JsonBlob se ukládají v objektech BLOB v úložišti Azure. Každá instance LAD vytvoří objekt blob každou hodinu pro každý název jímky. Každý objekt blob se vždy obsahuje syntakticky správný pole JSON objektu. Atomicky jsou přidávány nové položky na pole. Objekty BLOB jsou uloženy v kontejneru se stejným názvem jako jímku. Azure storage pravidla pro názvy kontejneru objektů blob se vztahují na názvy JsonBlob jímky: mezi 3 až 63 malé alfanumerické znaky ASCII nebo spojovníky.

## <a name="public-settings"></a>Nastavení veřejné

Tato struktura obsahuje různé bloky nastavení, které řídí informace shromážděné funkcí rozšíření. Každé nastavení je volitelné. Pokud zadáte `ladCfg`, musíte zadat také `StorageAccount`.

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
StorageAccount | Název účtu úložiště, ve kterém se data zapisují rozšíření. Musí být stejný název, jako je zadán v [nastavení chráněné](#protected-settings).
mdsdHttpProxy | (volitelné) Stejně jako v [nastavení chráněné](#protected-settings). Hodnota veřejného přepsán privátní hodnotu, pokud nastavení. Nastavení proxy serveru, které obsahují tajného kódu, jako jsou hesla, v umístit [nastavení chráněné](#protected-settings).

Zbývající prvky jsou popsány podrobně v následující části.

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

Tento volitelný struktura ovládací prvky shromažďování metrik a protokolů pro doručování ve službě Azure metrik a dalších dat jímky. Musíte zadat buď `performanceCounters` nebo `syslogEvents` nebo obojí. Je nutné zadat `metrics` struktury.

Element | Hodnota
------- | -----
eventVolume | (volitelné) Určuje počet oddílů v rámci tabulky úložiště. Musí být jedna z `"Large"`, `"Medium"`, nebo `"Small"`. Pokud není zadán, výchozí hodnota je `"Medium"`.
sampleRateInSeconds | (volitelné) Výchozí interval mezi shromažďování metrik nezpracované (neagregovaným). Nejmenší podporované vzorkovací frekvence je 15 sekund. Pokud není zadán, výchozí hodnota je `15`.

#### <a name="metrics"></a>Průzkumníku metrik

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
resourceId | Nastavit ID prostředku Azure Resource Manageru virtuálního počítače nebo škálovací virtuálních počítačů, ke které patří virtuální počítač. Toto nastavení musí být také zadána, pokud libovolné jímky JsonBlob se používá v konfiguraci.
Hodnota scheduledTransferPeriod | Frekvence, ve kterém jsou agregovaná metrika počítaný a přenést do Azure metriky, vyjádřené jako interval času je 8601. Minimální doba přenosu je 60 sekund, tedy PT1M. Musíte zadat aspoň jedna hodnota scheduledTransferPeriod.

Ukázky z metrik uvedených v části čítače výkonu shromažďovaných každých 15 sekund nebo v ukázce ohodnotit explicitně definované čítače. Pokud se zobrazí několik hodnota scheduledTransferPeriod frekvence (jako v příkladu), každá agregace je vypočítán nezávisle na sobě.

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

V této volitelné části kontrolou shromažďování metrik. Nezpracovaná vzorky se agregují pro každou [hodnota scheduledTransferPeriod](#metrics) k vytvoření těchto hodnot:

* střední hodnotu
* minimum
* maximum
* shromážděné poslední hodnota
* počet nezpracovaných ukázky, které slouží k výpočtu agregace

Element | Hodnota
------- | -----
jímky | (volitelné) Čárkou oddělený seznam názvů jímky, do které LAD odešle agregovaná metrika výsledky. Všechna agregovaná metrika se publikují do jednotlivých uvedených jímky. Zobrazit [sinksConfig](#sinksconfig). Příklad: `"EHsink1, myjsonsink"`.
type | Identifikuje skutečné zprostředkovatele metriky.
Třída | Spolu s informací, že čítač"identifikuje konkrétní metriky v rámci oboru názvů poskytovatele.
Čítač | Spolu s "třída" identifikuje konkrétní metriky v rámci oboru názvů poskytovatele.
counterSpecifier | Identifikuje konkrétní metriky v rámci oboru názvů Azure metriky.
condition | (volitelné) Vybere konkrétní instanci objektu, ke kterému metriku použije nebo vybere agregaci za všechny instance daného objektu. Další informace najdete v tématu [ `builtin` definice metrik](#metrics-supported-by-builtin).
sampleRate | JE 8601 interval, který nastaví četnost, kdy se shromažďují nezpracovaná ukázek pro tuto metriku. Pokud není nastavený, intervalem sběru hodnot nastavená hodnota [sampleRateInSeconds](#ladcfg). Nejkratší podporované vzorkovací frekvence je 15 sekund (PT15S).
jednotka | By měla být jedna z těchto řetězců: "Count", "Bajtů", "Seconds", "Procenta", "CountPerSecond", "BytesPerSecond", "Milisekund". Definuje jednotku pro metriku. Spotřebitelé dat shromážděných očekávat, že hodnoty shromážděná data tak, aby odpovídala této jednotky. LAD ignoruje toto pole.
displayName | Popisek (v jazyce určeném v nastavení národního prostředí přidružené) připojené k těmto datům v Azure metriky. LAD ignoruje toto pole.

CounterSpecifier je libovolný identifikátor. Příjemci metrik, jako je Azure portal grafů a výstrah funkce, použijte counterSpecifier jako "klíče", který identifikuje metriku nebo instance metriky. Pro `builtin` metriky, doporučujeme použít counterSpecifier hodnoty, které začínají `/builtin/`. Pokud shromažďujete konkrétní instanci metriky, doporučujeme, že se že připojíte k hodnotě counterSpecifier identifikátor instance. Několik příkladů:

* `/builtin/Processor/PercentIdleTime` -Nečinnost průměrovaný napříč všechny virtuální procesory
* `/builtin/Disk/FreeSpace(/mnt)` -Volného místa pro /mnt systému souborů
* `/builtin/Disk/FreeSpace` -Volné místo průměrovaný napříč všechny připojené systémy souborů

LAD ani na webu Azure portal očekává, že hodnota counterSpecifier tak, aby odpovídaly libovolný vzor. Buďte konzistentní v způsobu vytvoření counterSpecifier hodnoty.

Pokud zadáte `performanceCounters`, LAD vždy zapisuje data do tabulky ve službě Azure storage. Můžete mít stejné data zapsaná do objektů BLOB JSON a/nebo Event Hubs, ale už se nedá vypnout ukládání dat do tabulky. Všechny instance diagnostického rozšíření používat stejný název účtu úložiště a koncový bod přidat jejich metriky a protokoly do stejné tabulky. Pokud příliš mnoho virtuálních počítačů jsou zápisu do stejného oddílu tabulky, Azure můžete omezit zápisy do tohoto oddílu. Nastavení eventVolume způsobí, že položky šíření mezi 1 (malé), 10 (střední), nebo 100 různých oddílů (dlouhodobé používání). "Střední" je obvykle dostatečná Ujistěte se, že přenos není omezený. Funkce Azure metriky na webu Azure Portal používá data v této tabulce, k vytvoření grafů nebo aktivovat upozornění. Název tabulky je zřetězení těchto řetězců:

* `WADMetrics`
* Hodnota "scheduledTransferPeriod" pro agregované hodnoty uložené v tabulce
* `P10DV2S`
* Datum ve formě "RRRRMMDD", která změní každých 10 dnů

Mezi příklady patří `WADMetricsPT1HP10DV2S20170410` a `WADMetricsPT1MP10DV2S20170609`.

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

V této volitelné části kontrolou shromažďování protokolů událostí z protokolu syslog. Při vynechání části nejsou vůbec zaznamenané události procesu syslog.

Kolekce syslogEventConfiguration má jeden záznam pro každou protokolovací mechanismus syslog, které vás zajímají. Pokud minSeverity "NONE" je pro konkrétní zařízení nebo pokud tohoto zařízení není v elementu vůbec, jsou zachyceny žádné události z tohoto zařízení.

Element | Hodnota
------- | -----
jímky | Čárkou oddělený seznam názvů jímky, do kterých se jednotlivé protokolu události se publikují. Všechny události protokolu odpovídající omezení syslogEventConfiguration se publikují do jednotlivých uvedených jímky. Příklad: "EHforsyslog"
%{facilityname/ | Název zařízení syslog (například "protokolu\_uživatele" nebo "protokolu\_LOCAL0"). Najdete v části "zařízení" [syslog man stránky](http://man7.org/linux/man-pages/man3/syslog.3.html) úplný seznam.
minSeverity | Úroveň závažnosti syslog (například "protokolu\_ERR" nebo "protokolu\_informace"). Najdete v části "úrovní" [syslog man stránky](http://man7.org/linux/man-pages/man3/syslog.3.html) úplný seznam. Rozšíření jsou zaznamenané události odeslané do zařízení, na zadané úrovni nebo vyšší.

Pokud zadáte `syslogEvents`, LAD vždy zapisuje data do tabulky ve službě Azure storage. Můžete mít stejné data zapsaná do objektů BLOB JSON a/nebo Event Hubs, ale už se nedá vypnout ukládání dat do tabulky. Dělení chování pro tuto tabulku je stejné jako nastavení popsané pro `performanceCounters`. Název tabulky je zřetězení těchto řetězců:

* `LinuxSyslog`
* Datum ve formě "RRRRMMDD", která změní každých 10 dnů

Mezi příklady patří `LinuxSyslog20170410` a `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

V této volitelné části řídí spuštění libovolného [OMI](https://github.com/Microsoft/omi) dotazy.

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
Obor názvů | (volitelné) OMI obor názvů, ve kterém by měl provést dotaz. Pokud tento parametr zadán, výchozí hodnota je "kořenový/scx", implementovaných [poskytovatelé Cross-platform System Center](http://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
query | OMI dotazu, který má být proveden.
tabulka | (volitelné) V tabulce Azure storage, v účtu úložiště určený (viz [nastavení chráněné](#protected-settings)).
frequency | (volitelné) Počet sekund mezi provádění dotazu. Výchozí hodnota je 300 (5 minut); minimální hodnota je 15 sekund.
jímky | (volitelné) Čárkou oddělený seznam názvů další jímky, ke kterým by se měly zveřejňovat výsledky metriky nezpracovaná ukázková. Žádné agregace nezpracovaných ukázek se počítá podle rozšíření nebo metriky Azure.

Buď "table" nebo "jímky" nebo obojí, musí být zadán.

### <a name="filelogs"></a>fileLogs

Ovládací prvky sběr souborů protokolů. LAD zachycuje nové řádky textu, jako jsou zapsány do souboru a zapisuje je do řádky tabulky a/nebo žádné zadaný jímky (JsonBlob nebo centra událostí).

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
soubor | Úplný název cesty souboru protokolu určeného k viděli a zachytit. Cesta musí název jednoho souboru; nemůže název adresáře nebo obsahovat zástupné znaky.
tabulka | (volitelné) Tabulky Azure storage v účtu úložiště určený (jak je uvedeno v chráněné konfigurace), do kterého se zapisují nové řádky z "konec" soubor.
jímky | (volitelné) Čárkou oddělený seznam názvy další jímek pro odeslání protokolu řádků.

Buď "table" nebo "jímky" nebo obojí, musí být zadán.

## <a name="metrics-supported-by-the-builtin-provider"></a>Podporované poskytovatelem builtin metriky

Zprostředkovatel metriky builtin je zdroj metriky zajímá nejvíce, široké škály uživatelů. Tyto metriky se dělí do pěti obecných tříd:

* Procesor
* Memory (Paměť)
* Síť
* systém souborů
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>předdefinované metriky pro třídu procesoru

Třída procesoru metrik poskytuje informace o využití procesoru na virtuálním počítači. Při agregování procenta, výsledkem je průměr mezi všechny procesory. Ve virtuálním počítači dva virtuální procesory Pokud byl jeden virtuální procesor zaneprázdněný 100 % a druhý byl nečinný, 100 % by ohlášené PercentIdleTime 50. Pokud se každý virtuální procesor zaneprázdněný stejnou dobu 50 %, by také oznámený výsledek 50. Na virtuálním počítači čtyři virtuální procesory s jeden 100 virtuálních procesorů % zaneprázdněný a ostatní nečinnosti bude ohlášené PercentIdleTime 75.

Čítač | Význam
------- | -------
PercentIdleTime | Procentuální hodnota času během časového období agregace, procesory prováděla jádra nečinné smyčky
percentProcessorTime | Procentuální hodnota času provádění jiných než nečinných vláken
PercentIOWaitTime | Procento doby čekání na dokončení vstupně-výstupní operací
PercentInterruptTime | Procentuální hodnota času provádění hardwaru a softwaru přerušení a DPC (odložených volání procedur)
PercentUserTime | Z jiných než nečinných čas období agregace procentuální podíl doby strávené v uživatelském více s normální prioritou
PercentNiceTime | Jiných než nečinných dobu trvání procento sníženou (dobré) důležitostí
PercentPrivilegedTime | Jiných než nečinných dobu trvání procento v privilegovaném režimu režimu (jádra)

První čtyři čítače by měl součet, kterou se 100 %. Poslední tři čítače také součet na 100 %; jejich součet PercentProcessorTime PercentIOWaitTime a PercentInterruptTime rozdělit.

Chcete-li získat jednu metriku agregovaný pro všechny procesory, nastavte `"condition": "IsAggregate=TRUE"`. Získat metriky pro konkrétní procesor, jako je například druhý logický procesor virtuálního počítače s čtyři virtuální procesory nastavit `"condition": "Name=\\"1\\""`. Logický procesor čísla jsou v rozsahu `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>předdefinované metriky pro třídu paměti

Třída paměti metrik poskytuje informace o využití paměti, stránkování a výměna.

Čítač | Význam
------- | -------
AvailableMemory | Dostupná fyzická paměť v MiB
PercentAvailableMemory | Dostupná fyzická paměť v procentech celkové paměti
UsedMemory | Použijte fyzické paměti (MiB)
PercentUsedMemory | Fyzické paměti používané jako procento celkové paměti
PagesPerSec | Celkový počet stránkování (čtení a zápis)
PagesReadPerSec | Čtení stránek ze záložní úložiště (odkládacího souboru, soubor programu, mapovaný soubor, atd.)
PagesWrittenPerSec | Stránek zapsaných do záložního úložiště (odkládacího souboru, mapovaný soubor, atd.)
AvailableSwap | Nepoužité odkládacího prostoru (MiB)
PercentAvailableSwap | Nepoužité velikosti odkládacího souboru jako procentuální hodnotu celkové velikosti odkládacího souboru
UsedSwap | Použití odkládacího prostoru (MiB)
PercentUsedSwap | Používané místo odkládacího souboru jako procentuální hodnotu celkové velikosti odkládacího souboru

Tato třída metrik obsahuje jenom jednu instanci. Atribut "podmínku" nemá žádná užitečná nastavení a by měl být vynechán.

### <a name="builtin-metrics-for-the-network-class"></a>předdefinované metriky pro třídu sítě

Třída sítě metrik poskytuje informace o síťové aktivity na jednotlivých síťových rozhraních od spuštění počítače. LAD nevystavuje metriky šířky pásma, která se dá načíst z metrik hostitele.

Čítač | Význam
------- | -------
BytesTransmitted | Celkový počet bajtů odeslaných od spuštění
BytesReceived | Celkový počet bajtů přijatých od spuštění
BytesTotal | Celkový počet bajtů odeslaných nebo přijatých od spuštění
PacketsTransmitted | Celkový počet paketů odeslaných od spuštění
PacketsReceived | Celkový počet paketů přijatých od spuštění
TotalRxErrors | Počet chyb příjmu od spuštění
TotalTxErrors | Počet chyb přenosu od spuštění
TotalCollisions | Počet kolizí hlášených síťové porty od spuštění

 I když se instance této třídy, nepodporuje LAD zachytávající síťové metriky agregované napříč všechna síťová zařízení. Chcete-li získat metriky pro určité rozhraní, jako je například eth0, nastavte `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>předdefinované metriky pro třídy systému souborů

Třída systému souborů metrik poskytuje informace o využití systému souborů. Absolutní a procentuální hodnoty jsou hlášeny jako by se zobrazí jako běžný uživatel (nikoli kořenový).

Čítač | Význam
------- | -------
FreeSpace | Volného místa na disku v bajtech
UsedSpace | Využité místo na disku v bajtech
PercentFreeSpace | Procento volného místa
PercentUsedSpace | Procento využitého prostoru
PercentFreeInodes | Procento nevyužitých uzlů Inode
PercentUsedInodes | Procento přidělených (používaných) uzlů Inode sčítat přes všechny systémy souborů
BytesReadPerSecond | Přečtené bajty za sekundu
BytesWrittenPerSecond | Bajtů zapsaných za sekundu
BytesPerSecond | Bajty čteným nebo zapsaným za sekundu
ReadsPerSecond | Operace čtení za sekundu
WritesPerSecond | Operace zápisu za sekundu
TransfersPerSecond | Operace čtení nebo zápisu za sekundu

Agregované hodnoty napříč všechny systémy souborů je možné získat nastavení `"condition": "IsAggregate=True"`. Hodnoty pro konkrétní připojeného souboru systému, jako například "/ mnt", můžete získat tak, že nastavíte `"condition": 'Name="/mnt"'`.

### <a name="builtin-metrics-for-the-disk-class"></a>předdefinované metriky pro třídu disku

Třída disku metrik poskytuje informace o využití disku zařízení. Tyto statistiky vztahují na celé jednotky. Pokud existuje více systémy souborů na zařízení, čítače pro toto zařízení jsou efektivní, agreguje pro všechny z nich.

Čítač | Význam
------- | -------
ReadsPerSecond | Operace čtení za sekundu
WritesPerSecond | Operace zápisu za sekundu
TransfersPerSecond | Celkový počet operací za sekundu
AverageReadTime | Průměrný počet sekund na operace čtení
AverageWriteTime | Průměrný počet sekund na operace zápisu
AverageTransferTime | Průměrný počet sekund na operaci
AverageDiskQueueLength | Průměrný počet zařazených do fronty diskových operací
ReadBytesPerSecond | Počet bajtů přečtených za sekundu
WriteBytesPerSecond | Počet bajtů zapsaných za sekundu
BytesPerSecond | Počet bajtů čteným nebo zapsaným za sekundu

Agregované hodnoty na všech discích je možné získat nastavení `"condition": "IsAggregate=True"`. Chcete-li získat informace pro konkrétní zařízení (třeba/dev/sdf1), nastavte `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalace a konfigurace LAD 3.0 prostřednictvím rozhraní příkazového řádku

Za předpokladu, že jsou chráněný nastavení v souboru PrivateConfig.json a informace o veřejné konfigurace je v PublicConfig.json, spusťte tento příkaz:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

Tento příkaz předpokládá, že používáte režimu Azure Resource Manageru (arm) rozhraní příkazového řádku Azure. Konfigurace pro nasazení classic LAD model virtuálních počítačů (ASM), přepněte do režimu "asm" (`azure config mode asm`) a vynechat název skupiny prostředků v příkazu. Další informace najdete v tématu [– multiplatformního rozhraní příkazového řádku dokumentaci](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Příklad LAD 3.0 konfigurace

Podle předchozí definice, tady je ukázka konfigurace rozšíření LAD 3.0 vysvětlení. Tuto ukázku použít pro váš případ, měli byste použít vlastní název účtu úložiště, token SAS účtu a tokeny EventHubs SAS.

### <a name="privateconfigjson"></a>PrivateConfig.json

Nastavení privátní konfigurace:

* Účet úložiště
* odpovídající token SAS účtu
* několik jímky (JsonBlob nebo EventHubs s tokeny SAS)

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

Nastavení veřejného způsobit LAD na:

* Nahrát procento času procesoru a použitého místa disku metriky `WADMetrics*` tabulky
* Odeslat zprávy z protokolu syslog zařízení "user" a závažnost "informace" `LinuxSyslog*` tabulky
* Nahrání nezpracovaných výsledků dotazu (OMI) (PercentProcessorTime a PercentIdleTime) do pojmenované `LinuxCPU` tabulky
* Nahrát připojený řádků v souboru `/var/log/myladtestlog` k `MyLadTestLog` tabulky

V obou případech se data také nahrávají do:

* Azure Blob storage (název kontejneru je definovaný v jímce JsonBlob)
* Koncový bod EventHubs (jak je uvedeno v jímce EventHubs)

```json
{
  "StorageAccount": "yourdiagstgacct",
  "sampleRateInSeconds": 15,
  "ladCfg": {
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

`resourceId` v konfiguraci musí odpovídat, že virtuální počítač nebo virtuální počítač škálovací sady.

* Platforma Azure metriky grafů a výstrah ví resourceId virtuálních počítačů právě pracujete. Očekává, že ji vyhledat potřebná data pro váš virtuální počítač pomocí ID prostředku key vyhledávání.
* Pokud používáte automatické škálování v Azure, musí odpovídat ID prostředku v konfiguraci automatického škálování resourceId používané LAD.
* ID prostředku je integrovaná do názvy JsonBlobs autorem LAD.

## <a name="view-your-data"></a>Umožňuje zobrazit vaše data

Pomocí webu Azure portal zobrazit údaje o výkonu nebo nastavit výstrahy:

![image](./media/diagnostics-linux/graph_metrics.png)

`performanceCounters` Data jsou vždy uloženy v tabulce Azure Storage. Rozhraní API pro Azure Storage jsou k dispozici pro mnoho jazyky a platformy.

Data odeslaná do jímky JsonBlob je uložená v objektech BLOB v účtu úložiště s názvem v [nastavení chráněné](#protected-settings). Můžete využívat data objektů blob s využitím libovolné rozhraní API Azure Blob Storage.

Kromě toho můžete použít tyto nástroje uživatelského rozhraní pro přístup k datům ve službě Azure Storage:

* Průzkumníka serveru Visual Studio.
* [Microsoft Azure Storage Exploreru](https://azurestorageexplorer.codeplex.com/ "Průzkumníka služby Azure Storage").

Tento snímek relace Microsoft Azure Storage Explorer zobrazí vygenerovaný tabulky služby Azure Storage a jeho kontejnerech správně nakonfigurovaná rozšíření LAD 3.0 na testovacím virtuálním počítači. Na obrázku se neshoduje přesně [ukázkové konfiguraci LAD 3.0](#an-example-lad-30-configuration).

![image](./media/diagnostics-linux/stg_explorer.png)

Viz příslušná [EventHubs dokumentaci](../../event-hubs/event-hubs-what-is-event-hubs.md) se naučíte využívat zprávy publikované do koncového bodu EventHubs.

## <a name="next-steps"></a>Další postup

* Vytváření upozornění metrik v [Azure Monitor](../../monitoring-and-diagnostics/insights-alerts-portal.md) pro můžete shromažďovat metriky.
* Vytvoření [grafy monitorování](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) pro metriky.
* Zjistěte, jak [vytvořit škálovací sadu virtuálních počítačů](../linux/tutorial-create-vmss.md) ovládání automatického škálování pomocí metrik.
