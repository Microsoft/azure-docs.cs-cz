---
title: Výpočetní - rozšíření diagnostiky Linux | Microsoft Docs
description: Postup konfigurace Azure Linux diagnostiky rozšíření (LAD) pro shromažďování metrik a protokolování událostí z virtuálních počítačů Linux běží v Azure.
services: virtual-machines-linux
author: abhijeetgaiha
manager: sankalpsoni
ms.service: virtual-machines-linux
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 05/09/2017
ms.author: agaiha
ms.openlocfilehash: 8ffa9823000efbb101be73397cd0025f9933cecd
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34652640"
---
# <a name="use-linux-diagnostic-extension-to-monitor-metrics-and-logs"></a>Použití rozšíření diagnostiky Linux ke sledování metrik a protokoly

Tento dokument popisuje verze 3.0 a novějších rozšíření diagnostiky Linux.

> [!IMPORTANT]
> Informace o 2.3 a starší verze najdete v tématu [tento dokument](../linux/classic/diagnostic-extension-v2.md).

## <a name="introduction"></a>Úvod

Rozšíření diagnostiky Linux pomáhá uživatele monitorování stavu virtuálního počítače s Linuxem systémem Microsoft Azure. Má následující funkce:

* Shromažďuje metriky výkonu systému z virtuálního počítače a ukládá je do určité tabulky v účtu úložiště určený.
* Načte události protokolu z syslog a ukládá je do určité tabulky v účtu úložiště určený.
* Umožňuje uživatelům umožnit přizpůsobení metriky dat, které jsou shromažďovány a nahrát.
* Umožňuje uživatelům umožnit přizpůsobení syslog zařízení a úrovně závažnosti události, které jsou shromažďovány a nahrát.
* Umožňuje uživatelům odesílat zadané soubory protokolu do tabulky určené úložiště.
* Podporuje odesílání metriky a protokolu událostí na libovolný EventHub koncových bodů a formátu JSON BLOB v účtu úložiště určený.

Toto rozšíření pracuje s obou modelech nasazení Azure.

## <a name="installing-the-extension-in-your-vm"></a>Instalaci rozšíření ve vašem virtuálním počítači

Toto rozšíření můžete povolit pomocí rutin prostředí Azure PowerShell, rozhraní příkazového řádku Azure skripty nebo šablony nasazení Azure. Další informace najdete v tématu [rozšíření funkce](features-linux.md).

Povolit nebo konfigurovat LAD 3.0 nelze použít na portálu Azure. Místo toho nainstaluje a nakonfiguruje verze 2.3. Výstrahy a portálu Azure grafy pracovat s daty z obě verze rozšíření.

Tyto pokyny k instalaci a [ke stažení ukázkové konfiguraci](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json) konfigurace LAD 3.0 na:

* zaznamenání a uložení metriku stejné jako byly poskytované LAD 2.3;
* zaznamenat užitečné sadu metriky systému souborů, nový LAD 3.0;
* zachycení výchozí kolekci syslog ve LAD 2.3; povolené
* Povolte Azure portálu prostředí pro vytváření grafů a výstrahy na metriky virtuálního počítače.

Zaváděná konfigurace je jenom jako příklad; upravte tak, aby vyhovovaly potřebám vaší vlastní.

### <a name="prerequisites"></a>Požadavky

* **Azure Linux Agent verze 2.2.0 nebo novější**. Většina virtuálních počítačů Linux Azure Galerie Image zahrnují verze 2.2.7 nebo novější. Spustit `/usr/sbin/waagent -version` chcete ověřit verzi nainstalovaný na Virtuálním počítači. Pokud virtuální počítač běží starší verze agenta hosta, postupujte podle [tyto pokyny](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent) jej aktualizovat.
* **Azure CLI**. [Nastavení Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) prostředí na váš počítač.
* Příkaz wget, pokud ji nemáte: Spusťte `sudo apt-get install wget`.
* Stávající předplatné Azure a v něm k uložení dat stávající účet úložiště.

### <a name="sample-installation"></a>Ukázka instalace

Zadejte správné parametry na první tři řádky, a poté spusťte tento skript jako kořenového adresáře:

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

Adresa URL pro ukázkové konfiguraci a její obsah se mohou změnit. Stáhněte si kopii souboru JSON nastavení portálu a přizpůsobit pro své potřeby. Všechny šablony nebo automatizace, které vytvoříte měli používat vlastní kopie místo stahování pokaždé, když tuto adresu URL.

### <a name="updating-the-extension-settings"></a>Aktualizuje se nastavení rozšíření

Po změně chráněné nebo nastavení veřejných, nasaďte do virtuálního počítače tak, že spuštění stejného příkazu. Pokud nic změnit v nastavení, rozšíření jsou odesílány aktualizovaným nastavením. LAD znovu načte konfiguraci a restartuje sám sebe.

### <a name="migration-from-previous-versions-of-the-extension"></a>Migrace z předchozích verzí rozšíření

Nejnovější verze rozšíření je **3.0**. **Všechny starší verze (2.x) jsou zastaralé a může být publikování na nebo po 31. července 2018**.

> [!IMPORTANT]
> Toto rozšíření zavádí nejnovější změny v konfiguraci rozšíření. Pro zlepšení zabezpečení rozšíření; provedla jeden taková změna v důsledku toho zpětné kompatibility s 2.x nebylo možné udržovat. Navíc se liší od vydavatele pro verze 2.x vydavatel rozšíření pro tuto příponu.
>
> Při migraci z 2.x do této nové verzi rozšíření, je nutné odinstalovat původní rozšíření (pod původním názvem vydavatele) a potom nainstalovat verzi 3 rozšíření.

Doporučení:

* Nainstalujte rozšíření automatické podverze upgradu povolena.
  * V modelu nasazení classic virtuální počítače zadejte '3.*, jako je verze při instalaci rozšíření prostřednictvím příkazového řádku Azure XPLAT nebo Powershellu.
  * Při nasazení Azure Resource Manager modelu virtuálních počítačů, zahrnují ' "autoUpgradeMinorVersion": true, v nasazení šablony virtuálního počítače.
* Použijte nový nebo jiný účet úložiště pro LAD 3.0. Existuje několik malých nekompatibility mezi LAD 2.3 a LAD 3.0, které usnadňují sdílení problémových účet:
  * LAD 3.0 ukládá události procesu syslog v tabulce s jiným názvem.
  * CounterSpecifier řetězce pro `builtin` metriky rozdílnou LAD 3.0.

## <a name="protected-settings"></a>Chráněné nastavení

Tato sada informace o konfiguraci obsahuje citlivé informace, které by měly být chráněné z veřejného zobrazení, například přihlašovací údaje úložiště. Tato nastavení jsou předávány a uložená rozšíření v šifrovaném tvaru.

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
storageAccountName | Název účtu úložiště, ve kterém je rozšíření zapisovat data.
storageAccountEndPoint | (volitelné) Koncový bod identifikace cloudu, ve kterém existuje účet úložiště. Chybí-li toto nastavení, LAD výchozí veřejného cloudu Azure `https://core.windows.net`. Pokud chcete používat účet úložiště v Azure v Německu, Azure Government nebo Azure China, tato hodnota odpovídajícím způsobem nastavte.
storageAccountSasToken | [Tokenu SAS účtu](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/) pro služby objektů Blob a tabulky (`ss='bt'`), platí pro kontejnery a objekty (`srt='co'`), která uděluje přidat, vytvářet, seznam, aktualizovat a oprávnění k zápisu (`sp='acluw'`). Proveďte *není* zahrnují na úvodní otazník (?).
mdsdHttpProxy | (volitelné) Informace o proxy serveru HTTP potřebnými k povolení rozšíření pro připojení k zadaný účet úložiště a koncového bodu.
sinksConfig | (volitelné) Podrobnosti o alternativní umístění, do kterých mohou být zajišťovány metriky a události. Konkrétní podrobnosti o každém jímku dat nepodporuje rozšíření jsou popsané v následujících částech.


> [!NOTE]
> Při nasazování rozšíření pomocí šablony Azure nasazení, SAS token a účet úložiště musí být vytvořen předem a následně předán do šablony. Nelze nasadit virtuální počítač, účet úložiště a konfiguraci rozšíření v jediné šabloně. Vytvoření tokenu SAS v rámci šablony není aktuálně podporován.

Můžete snadno vytvořit požadovaný token SAS prostřednictvím portálu Azure.

1. Vyberte účet úložiště pro obecné účely, u kterého chcete rozšíření pro zápis
1. Vyberte "Sdílený přístupový podpis" z nastavení součástí v levé nabídce
1. Ujistěte se, jak se popisuje výš příslušné části
1. Klikněte na tlačítko "Generovat SAS".

![Bitové kopie](./media/diagnostics-linux/make_sas.png)

Zkopírujte vygenerovaný SAS do pole storageAccountSasToken; odebrat úvodní otazník ("?").

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

Tento volitelný oddíl definuje další cílových míst, na které rozšíření odešle informace, které shromáždí. Pole "podřízený" obsahuje objekt pro každý další data jímky. Atributu "typ" Určuje atributy v objektu.

Element | Hodnota
------- | -----
jméno | Řetězec se používá k odkazování na tento podřízený jinde v konfiguraci rozšíření.
type | Typ jímky definovaný. Určuje ostatní hodnoty (pokud existuje) v instance tohoto typu.

Verze 3.0 rozšíření diagnostiky Linux podporuje dva typy podřízený: EventHub a JsonBlob.

#### <a name="the-eventhub-sink"></a>Podřízený EventHub

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

Položka "sasURL" obsahuje úplnou adresu URL, včetně tokenu SAS pro centra událostí, ke které je nutné ji publikovat data. LAD vyžaduje SAS pojmenování zásadu, která umožňuje odesílání deklarace identity. Příklad:

* Vytvořit obor názvů Event Hubs s názvem `contosohub`
* Vytvoření centra událostí v oboru názvů názvem `syslogmsgs`
* Vytvoření zásady sdíleného přístupu v Centru událostí s názvem `writer` umožňující odesílat deklarace identity

Pokud jste vytvořili SAS funkční až půlnoci času UTC na 1. ledna 2018, hodnota sasURL může být:

```url
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

Další informace o generování tokeny SAS pro Event Hubs naleznete v tématu [této webové stránce](../../event-hubs/event-hubs-authentication-and-security-model-overview.md).

#### <a name="the-jsonblob-sink"></a>Podřízený JsonBlob

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

Data směrované podřízený JsonBlob se ukládají do objektů BLOB v úložišti Azure. Každá instance LAD vytvoří každou hodinu pro každý název podřízený objekt blob. Každý objekt blob vždy obsahuje syntakticky pole JSON objektu. Do pole jsou atomicky přidány nové položky. Objekty BLOB jsou uloženy v kontejneru se stejným názvem jako jímky. Úložiště Azure pravidla pro názvy kontejneru objektů blob platí pro názvy JsonBlob jímky: mezi 3 a 63 malé alfanumerické znaky ASCII nebo pomlčky.

## <a name="public-settings"></a>Nastavení veřejné

Tato struktura obsahuje různé bloky nastavení, které řídí údaje shromážděné pomocí rozšíření. Každé nastavení je volitelné. Pokud zadáte `ladCfg`, musíte zadat také `StorageAccount`.

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
StorageAccount | Název účtu úložiště, ve kterém je rozšíření zapisovat data. Musí být stejný jako název, jako jsou zadány v [chráněné nastavení](#protected-settings).
mdsdHttpProxy | (volitelné) Stejné jako v [chráněné nastavení](#protected-settings). Hodnota veřejného je přepsat privátní hodnotou, pokud nastavení. Nastavení proxy serveru, které obsahují tajného klíče, jako jsou hesla, v umístit [chráněné nastavení](#protected-settings).

Zbývající prvky jsou podrobně popsané v v následujících částech.

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

Ovládací prvky této volitelné struktura jímky shromažďování metrik a protokoly pro doručení ke službě Azure metriky a další data. Je nutné zadat buď `performanceCounters` nebo `syslogEvents` nebo obojí. Je nutné zadat `metrics` struktura.

Element | Hodnota
------- | -----
eventVolume | (volitelné) Určuje počet oddílů vytvořit v tabulce úložiště. Musí mít jednu z `"Large"`, `"Medium"`, nebo `"Small"`. Pokud není zadáno, výchozí hodnota je `"Medium"`.
sampleRateInSeconds | (volitelné) Výchozí interval mezi kolekce nezpracovaná (neagregovaným) metrik. Nejmenší podporované vzorkovací frekvence je 15 sekund. Pokud není zadáno, výchozí hodnota je `15`.

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
resourceId | Nastavit ID prostředku Azure Resource Manager virtuálního počítače nebo virtuálního počítače měřítka, ke které patří virtuální počítač. Toto nastavení musí být zadaná také pokud žádné podřízený JsonBlob se používá v konfiguraci.
scheduledTransferPeriod | Frekvence, kdy jsou agregovaná metrika počítaný a přenést do Azure metriky, vyjádřené jako interval času je 8601. V nejmenší období je 60 sekund, který je PT1M. Je nutné zadat alespoň jeden scheduledTransferPeriod.

Ukázky metrik zadané v části čítače výkonu se shromažďují každých 15 sekund nebo v ukázce míra explicitně definované čítače. Pokud se zobrazí více scheduledTransferPeriod frekvence (jako v příkladu), každé agregace se počítá samostatně.

#### <a name="performancecounters"></a>čítače výkonu

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

Tento volitelný oddíl pod kontrolou shromažďování metrik. Nezpracovaná ukázky jsou agregována pro každou [scheduledTransferPeriod](#metrics) k vytvoření tyto hodnoty:

* střední
* minimum
* maximum
* shromážděné poslední hodnota
* počet nezpracovaných ukázky slouží k výpočtu agregace

Element | Hodnota
------- | -----
jímky | (volitelné) Seznam názvů jímky, do které LAD zasílá agregovat metriky výsledky oddělených čárkami. Všechna agregovaná metrika se publikují do jednotlivých uvedených jímky. V tématu [sinksConfig](#sinksconfig). Příklad: `"EHsink1, myjsonsink"`.
type | Identifikuje skutečné zprostředkovatele metriky.
Třída | Společně s "čítač" jsou uvedeny konkrétní metriky v rámci oboru názvů poskytovatele.
Čítač | Společně s "class" jsou uvedeny konkrétní metriky v rámci oboru názvů poskytovatele.
counterSpecifier | Identifikuje určité metriky v rámci oboru názvů metrik Azure.
podmínka | (volitelné) Vybere konkrétní instanci objektu do které metriku platí nebo vybere agregace napříč všemi instancemi tohoto objektu. Další informace najdete v tématu [ `builtin` definice metrik](#metrics-supported-by-builtin).
sampleRate | Interval 8601, která nastavuje rychlost, jakou jsou shromažďovány nezpracovaná ukázky pro tato metrika je. Pokud není nastavena, je kolekce interval nastaven hodnotou [sampleRateInSeconds](#ladcfg). Nejkratší podporované vzorkovací frekvence je 15 sekund (PT15S).
jednotka | By měl být jeden z těchto řetězců: "Count", "Bajtů", "Seconds", "Procenta", "CountPerSecond", "BytesPerSecond", "Milisekundu". Definuje jednotky pro metriku. Příjemci shromážděná data očekávat shromážděná data hodnoty tak, aby odpovídaly tuto jednotku. LAD ignoruje toto pole.
displayName | Popisek (v jazyce určeném nastavením přidružené národní prostředí) být připojené k těmto datům v Azure metriky. LAD ignoruje toto pole.

CounterSpecifier je libovolný identifikátor. Příjemci metrik, Azure portálu grafů, jako a výstrahy funkce, použijte counterSpecifier jako "klíč", který identifikuje metriky nebo instanci metriky. Pro `builtin` metriky, doporučujeme použít counterSpecifier hodnoty, které začínají `/builtin/`. Pokud shromažďujete konkrétní instanci metriky, doporučujeme, abyste že na hodnotu counterSpecifier připojíte identifikátor instance. Několik příkladů:

* `/builtin/Processor/PercentIdleTime` -Doba nečinnosti, po průměrem všech Vcpu
* `/builtin/Disk/FreeSpace(/mnt)` -Volného místa pro systém souborů /mnt
* `/builtin/Disk/FreeSpace` -Volného místa průměrem všech připojené systémy

LAD ani portálu Azure očekává, že hodnota counterSpecifier odpovídat žádnému vzoru. Být konzistentní v tom, jak vytvořit counterSpecifier hodnoty.

Pokud zadáte `performanceCounters`, LAD vždy zapisuje data do tabulky v úložišti Azure. Může mít stejné dat zapsaných na objekty BLOB JSON nebo Event Hubs, ale nelze zakázat ukládání dat do tabulky. Všechny instance rozšíření diagnostiky nakonfigurovaný tak, aby používají stejný název účtu úložiště a koncový bod přidejte protokoly a metriky do stejné tabulky. Pokud jsou moc velký počet virtuálních počítačů zápis do stejného oddílu tabulky, Azure může omezit zápisy do tohoto oddílu. Nastavení eventVolume způsobí, že položky šíření mezi 1 (malé), 10 (střední), nebo 100 různých oddílů (velká). Obvykle je dostačující Ujistěte se, že není omezen provoz "Střední". K vytvoření grafy nebo výstrahy aktivovat funkci Azure metriky na portálu Azure používá data v této tabulce. Název tabulky je zřetězení tyto řetězce:

* `WADMetrics`
* "ScheduledTransferPeriod" pro agregované hodnoty, které jsou uložené v tabulce
* `P10DV2S`
* Datum ve formátu "RRRRMMDD", který změní každých 10 dní

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

Tento volitelný oddíl ovládací prvky kolekce protokolu událostí z syslog. Pokud je vynechán části, nejsou vůbec zachytit události procesu syslog.

Kolekce syslogEventConfiguration má jeden záznam pro každý mechanismus syslog, které vás zajímají. Pokud minSeverity "Žádný" je pro konkrétní zařízení, nebo pokud tohoto zařízení nezobrazí v elementu vůbec, jsou zachyceny žádné události z tohoto zařízení.

Element | Hodnota
------- | -----
jímky | Seznam názvů jímky, na které jsou publikovány jednotlivých protokolu událostí oddělených čárkami. Všechny události protokolu odpovídající omezení v syslogEventConfiguration se publikují do jednotlivých uvedených jímky. Příklad: "EHforsyslog"
facilityName | Název zařízení syslog (například "protokolu\_uživatele" nebo "protokolu\_LOCAL0"). Najdete v části "zařízení" [syslog man stránky](http://man7.org/linux/man-pages/man3/syslog.3.html) pro úplný seznam.
minSeverity | Úroveň závažnosti syslog (například "protokolu\_chyba" nebo "protokolu\_informace"). Najdete v části "úroveň" [syslog man stránky](http://man7.org/linux/man-pages/man3/syslog.3.html) pro úplný seznam. Přípona zachytí událostí odeslaných do zařízení nebo vyšší úrovni zadané.

Pokud zadáte `syslogEvents`, LAD vždy zapisuje data do tabulky v úložišti Azure. Může mít stejné dat zapsaných na objekty BLOB JSON nebo Event Hubs, ale nelze zakázat ukládání dat do tabulky. Rozdělení chování pro tuto tabulku je stejný jako popsané pro `performanceCounters`. Název tabulky je zřetězení tyto řetězce:

* `LinuxSyslog`
* Datum ve formátu "RRRRMMDD", který změní každých 10 dní

Mezi příklady patří `LinuxSyslog20170410` a `LinuxSyslog20170609`.

### <a name="perfcfg"></a>perfCfg

Spuštění libovolné ovládací prvky této volitelné části [OMI](https://github.com/Microsoft/omi) dotazy.

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
obor názvů | (volitelné) OMI obor názvů, ve kterém se má provést dotaz. Pokud tento parametr nezadáte, výchozí hodnota je "kořenový/scx", implementované [System Center a platformy zprostředkovatelé](http://scx.codeplex.com/wikipage?title=xplatproviders&referringTitle=Documentation).
query | OMI dotaz, který má být proveden.
tabulka | (volitelné) Tabulky úložiště Azure, v účtu úložiště určený (viz [chráněné nastavení](#protected-settings)).
frequency | (volitelné) Počet sekund mezi provádění dotazu. Výchozí hodnota je 300 (5 minut); minimální hodnota je 15 sekund.
jímky | (volitelné) Seznam názvy další jímky, na které je nutné ji publikovat metriky výsledky nezpracovaná ukázkový textový soubor s oddělovači. Žádné agregace ukázek nezpracovaná se počítá rozšíření nebo metrik Azure.

Musí být zadán buď "tabulka" nebo "jímky" nebo obojí.

### <a name="filelogs"></a>fileLogs

Řídí zaznamenávání souborů protokolu. LAD zaznamená nové řádky textu, jako jsou zapsané do souboru a zapíše na řádky tabulky a všechny zadané jímky (JsonBlob nebo EventHub).

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
soubor | Úplná cesta souboru protokolu bude sledovaná a zachycení. Název musí být názvu cesty jeden soubor; nemůže název adresáře nebo obsahovat zástupné znaky.
tabulka | (volitelné) Tabulka úložiště Azure v účtu úložiště určený (jako je zadaný v konfiguraci chráněného), do které se zapisují nové řádky "zadní" část souboru.
jímky | (volitelné) Seznam dalších jímky do protokolu linky, které odeslaných názvy oddělených čárkami.

Musí být zadán buď "tabulka" nebo "jímky" nebo obojí.

## <a name="metrics-supported-by-the-builtin-provider"></a>Zprostředkovatel předdefinované metriky

Zprostředkovatel metriky builtin je zdroj metriky nejvíce zajímavé pro širokou škálu uživatele. Tyto metriky spadají do pěti široký třídy:

* Procesor
* Memory (Paměť)
* Síť
* Systém souborů
* Disk

### <a name="builtin-metrics-for-the-processor-class"></a>předdefinované metriky pro procesor – třída

Třída procesoru metrik poskytuje informace o využití procesoru ve virtuálním počítači. Při agregování procenta, výsledkem je průměr mezi všechny procesory. Do virtuálního počítače, dva-virtuální procesory Pokud jeden virtuální procesor bylo 100 % zaneprázdněn a dalších 100 % nečinnosti, bude hlášené PercentIdleTime 50. Pokud každý virtuální procesor byl 50 % zaneprázdněn stejnou dobu, by také hlášené výsledek 50. Ve virtuálním počítači virtuální čtyřmi procesory s jeden 100 virtuálních procesorů % zaneprázdněn a ostatní nečinnosti bude hlášené PercentIdleTime 75.

Čítač | Význam
------- | -------
PercentIdleTime | Procento času během časového období agregace, procesory byly provádění jádra nečinné smyčky
percentProcessorTime | Procento doby provádění jiných než nečinných vláken
PercentIOWaitTime | Procento doby čekání na dokončení operací vstupně-výstupní operace
PercentInterruptTime | Procento doby provádění hardware a software přerušení a DPC (odložených volání procedur)
PercentUserTime | Jiných než nečinných dobu, během časového období agregace procento času stráveného v uživatele více se střední důležitostí
PercentNiceTime | Jiných než nečinných času stráveného procento snížený (dobrý) důležitostí
PercentPrivilegedTime | Jiných než nečinných času stráveného procento v privilegovaném režimu režimu (jádra)

První čtyři čítače by měl součet, kterou se 100 %. Poslední tři čítače také součet na 100 %; jejich rozdělit součet PercentProcessorTime, PercentIOWaitTime a PercentInterruptTime.

Chcete-li získat jeden metrika agregovat do všech procesorů, nastavte `"condition": "IsAggregate=TRUE"`. Chcete-li získat metriky pro specifické procesory, jako je například druhý logický procesor virtuálního počítače virtuální čtyřmi procesory, nastavte `"condition": "Name=\\"1\\""`. Logický procesor čísla jsou v rozsahu `[0..n-1]`.

### <a name="builtin-metrics-for-the-memory-class"></a>předdefinované metriky pro třídu paměti

Třída paměti metrik poskytuje informace o využití paměti, stránkování a vzájemná záměna.

Čítač | Význam
------- | -------
AvailableMemory | Dostupná fyzická paměť v MiB
PercentAvailableMemory | Dostupná fyzická paměť v procentech celkové paměti
UsedMemory | Použití fyzické paměti (MiB)
PercentUsedMemory | Fyzické paměti používané jako procento celkové paměti
PagesPerSec | Celkový počet stránkování (čtení a zápis)
PagesReadPerSec | Čtení stránek ze záložního úložiště (odkládacího souboru, soubor programu, mapovaný soubor, atd.)
PagesWrittenPerSec | Stránek zapsaných k záložnímu úložišti (odkládací soubor, mapovaný soubor, atd.)
AvailableSwap | Nepoužívané odkládacího prostoru (MiB)
PercentAvailableSwap | Nepoužívané odkládacího souboru jako procentuální hodnotu celkové velikosti odkládacího souboru
UsedSwap | Využití odkládacího souboru (MiB)
PercentUsedSwap | Používané místo odkládacího souboru jako procentuální hodnotu celkové velikosti odkládacího souboru

Tato třída metrik obsahuje pouze jednu instanci. Atribut "podmínky" neexistuje užitečné nastavení a by měl být vynechán.

### <a name="builtin-metrics-for-the-network-class"></a>předdefinované metriky pro sítě – třída

Třída sítě metrik poskytuje informace o síťové aktivity na rozhraní jednotlivým síťovým od spuštění počítače. LAD nevystavuje metriky šířky pásma, které je možné načíst z hostitele metriky.

Čítač | Význam
------- | -------
BytesTransmitted | Celkový počet bajtů odeslaných od spuštění
BytesReceived | Celkový počet bajtů přijatých od spuštění
BytesTotal | Celkový počet bajtů odesílané nebo přijímané od spuštění počítače
PacketsTransmitted | Celkový počet paketů odeslaných od spuštění
PacketsReceived | Celkový počet paketů přijatých od spuštění
TotalRxErrors | Počet chyb, obdrží od spuštění počítače
TotalTxErrors | Počet chyb přenosu od spuštění počítače
TotalCollisions | Počet kolizí hlášené síťové porty od spuštění počítače

 I když tato třída je instance, nepodporuje LAD zaznamenávání metriky sítě s agregovat přes všechna síťová zařízení. Chcete-li získat metriky pro určité rozhraní, jako je například eth0, nastavte `"condition": "InstanceID=\\"eth0\\""`.

### <a name="builtin-metrics-for-the-filesystem-class"></a>předdefinované metriky pro třídy systému souborů

Třída Filesystem metrik poskytuje informace o použití systému souborů. Absolutní a procentuální hodnoty jsou hlášeny, jako by se zobrazit běžného uživatele (není uživatel root).

Čítač | Význam
------- | -------
FreeSpace | Dostupné místo na disku v bajtech
UsedSpace | Využité místo na disku v bajtech
PercentFreeSpace | Procento volného místa
PercentUsedSpace | Procento využitého prostoru
PercentFreeInodes | Procentuální hodnota nepoužívané uzlů Inode
PercentUsedInodes | Procentuální hodnota přidělené (používán) uzlů Inode sčítají mezi všechny systémy
BytesReadPerSecond | Načíst počet bajtů za sekundu
BytesWrittenPerSecond | Bajtů zapsaných za sekundu
BytesPerSecond | Bajtů číst nebo zapisovat za sekundu
ReadsPerSecond | Operace čtení za sekundu
WritesPerSecond | Zápis operací za sekundu
TransfersPerSecond | Operace čtení nebo zápisu za sekundu

Agregované hodnoty mezi všechny systémy souborů je možné získat nastavení `"condition": "IsAggregate=True"`. Hodnoty pro konkrétní připojeného souboru systém, například "/ mnt", je možné získat nastavení `"condition": 'Name="/mnt"'`.

### <a name="builtin-metrics-for-the-disk-class"></a>předdefinované metriky pro třídu disku

Třída disku metrik poskytuje informace o využití disku zařízení. Tyto statistické údaje se vztahují na celou jednotku. Pokud existují více systémy souborů na zařízení, čítačů pro toto zařízení se prakticky agregovat přes všechny z nich.

Čítač | Význam
------- | -------
ReadsPerSecond | Operace čtení za sekundu
WritesPerSecond | Zápis operací za sekundu
TransfersPerSecond | Celkový počet operací za sekundu
AverageReadTime | Průměrný počet sekund na operace čtení
AverageWriteTime | Průměrný počet sekund na operace zápisu
AverageTransferTime | Průměrný počet sekund na operaci
AverageDiskQueueLength | Průměrný počet operací zařazených do fronty disku
ReadBytesPerSecond | Počet bajtů přečtených za sekundu
WriteBytesPerSecond | Počet bajtů zapsaných za sekundu
BytesPerSecond | Počet bajtů přečtených nebo zapsaných za sekundu

Agregované hodnoty všech disků je možné získat nastavení `"condition": "IsAggregate=True"`. Chcete-li získat informace pro konkrétní zařízení (například/dev/sdf1), nastavte `"condition": "Name=\\"/dev/sdf1\\""`.

## <a name="installing-and-configuring-lad-30-via-cli"></a>Instalace a konfigurace LAD 3.0 prostřednictvím rozhraní příkazového řádku

Za předpokladu, že jsou chráněný nastavení v souboru PrivateConfig.json a veřejné konfigurační informace v PublicConfig.json, spusťte tento příkaz:

```azurecli
az vm extension set *resource_group_name* *vm_name* LinuxDiagnostic Microsoft.Azure.Diagnostics '3.*' --private-config-path PrivateConfig.json --public-config-path PublicConfig.json
```

Příkaz předpokládá, že používáte správu prostředků Azure režim rozhraní příkazového řádku Azure (arm). Konfigurace pro nasazení classic LAD modelu virtuálních počítačů (ASM), přepněte do režimu "asm" (`azure config mode asm`) a název skupiny prostředků v příkazu vynechat. Další informace najdete v tématu [dokumentaci rozhraní příkazového řádku a platformy](https://docs.microsoft.com/azure/xplat-cli-connect).

## <a name="an-example-lad-30-configuration"></a>Příklad LAD 3.0 konfigurace

Podle definice předchozí, zde je ukázka konfigurace rozšíření LAD 3.0 s vysvětlení. Tuto ukázku použít pro váš případ, měli byste použít vlastní název účtu úložiště, token SAS účtu a tokeny EventHubs SAS.

### <a name="privateconfigjson"></a>PrivateConfig.json

Tato nastavení privátní konfigurace:

* účet úložiště
* odpovídající tokenu SAS účtu
* několik jímky (JsonBlob nebo EventHubs se tokeny SAS)

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

Tato nastavení veřejných způsobit LAD na:

* Nahrát procentuální hodnoty času procesoru a použitého místa disku metrik `WADMetrics*` tabulky
* Odeslání zprávy z syslog budovy "user" a závažnost "informace" k `LinuxSyslog*` tabulky
* Nahrát nezpracovaná OMI výsledků dotazu (PercentProcessorTime a PercentIdleTime) na zadaný `LinuxCPU` tabulky
* Nahrát připojením řádků v souboru `/var/log/myladtestlog` k `MyLadTestLog` tabulky

Data jsou v každém případě také odeslána a:

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

`resourceId` v konfiguraci se musí shodovat, virtuální počítač nebo virtuální počítač měřítka nastavit.

* Metriky platformy Azure grafů a výstrah zná resourceId pracujete virtuálního počítače. Se očekává, že chcete najít data pro virtuální počítač pomocí resourceId klíč vyhledávání.
* Pokud používáte Azure škálování, resourceId v konfiguraci automatického škálování se musí shodovat resourceId používané LAD.
* ResourceId je integrovaná do názvy JsonBlobs zapsaných správcem LAD.

## <a name="view-your-data"></a>Zobrazení dat

Pomocí portálu Azure do zobrazení dat výkonu nebo nastavit výstrahy:

![Bitové kopie](./media/diagnostics-linux/graph_metrics.png)

`performanceCounters` Dat byly vždy uloženy v tabulce Azure Storage. Rozhraní API Azure úložiště jsou k dispozici pro mnoho jazyky a platformy.

Data odeslaná jímky JsonBlob se ukládají do objektů BLOB v účtu úložiště s názvem v [chráněné nastavení](#protected-settings). Můžete využívat data objektů blob s využitím jakékoli rozhraní API úložiště objektů Blob Azure.

Kromě toho můžete tyto nástroje uživatelského rozhraní pro přístup k datům ve službě Azure Storage:

* Průzkumníka serveru Visual Studio.
* [Microsoft Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/ "Azure Storage Explorer").

Tento snímek relaci Microsoft Azure Storage Explorer zobrazuje generovaného tabulky služby Azure Storage a kontejnery z správně nakonfigurována rozšíření LAD 3.0 na testovací virtuální počítač. Obrázek neodpovídá přesně [Ukázková konfigurace LAD 3.0](#an-example-lad-30-configuration).

![Bitové kopie](./media/diagnostics-linux/stg_explorer.png)

V tématu odpovídajícího [EventHubs dokumentace](../../event-hubs/event-hubs-what-is-event-hubs.md) se dozvíte, jak využívat zpráv publikovaných do EventHubs koncový bod.

## <a name="next-steps"></a>Další postup

* Vytvoření metriky výstrahy v [Azure monitorování](../../monitoring-and-diagnostics/insights-alerts-portal.md) podle metrik, které shromažďujete.
* Vytvoření [tabulek sledování](../../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md) pro vaše metriky.
* Zjistěte, jak [vytvořit škálovací sadu virtuálních počítačů](../linux/tutorial-create-vmss.md) pomocí vaše metriky pro řízení automatické škálování.
