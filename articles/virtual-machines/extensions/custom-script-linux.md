---
title: Spuštění rozšíření vlastních skriptů na virtuálních počítačích se systémem Linux v Azure
description: Automatizace úloh konfigurace virtuálních počítačů se systémem Linux pomocí rozšíření vlastních skriptů v2
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: mimckitt
ms.openlocfilehash: 3c3dac8c8798b9c56b746a2e4e232f43ef967ebe
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91960299"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Použití rozšíření vlastních skriptů Azure verze 2 s linuxovými virtuálními počítači
Rozšíření vlastních skriptů verze 2 stáhne a spustí skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakoukoli jinou úlohu konfigurace nebo správy. Můžete stáhnout skripty z Azure Storage nebo jiného přístupného internetového umístění, nebo je můžete poskytnout modulu runtime rozšíření. 

Rozšíření vlastních skriptů se integruje s Azure Resource Manager šablonami. Můžete ho také spustit pomocí rozhraní příkazového řádku Azure CLI, PowerShellu nebo Azure Virtual Machines REST API.

Tento článek podrobně popisuje, jak použít rozšíření vlastních skriptů z Azure CLI a jak spustit rozšíření pomocí šablony Azure Resource Manager. Tento článek také popisuje postup řešení potíží pro systémy Linux.


Existují dvě rozšíření vlastních skriptů pro Linux:
* Verze 1 – Microsoft. OSTCExtensions. CustomScriptForLinux
* Verze 2 – Microsoft. Azure. Extensions. CustomScript

Pokud chcete místo toho použít novou verzi 2, přepněte prosím nová a existující nasazení. Nová verze představuje funkční náhradu starší verze. Proto při migraci není potřeba měnit konfiguraci rozšíření, ale stačí změnit jeho název a verzi.


### <a name="operating-system"></a>Operační systém

Rozšíření vlastních skriptů pro Linux se spustí v operačním systému rozšiřujícího rozšíření podporovaného rozšíření. Další informace najdete v tomto [článku](../linux/endorsed-distros.md).

### <a name="script-location"></a>Umístění skriptu

K přístupu k úložišti objektů BLOB v Azure můžete použít rozšíření pro použití přihlašovacích údajů služby Azure Blob Storage. Případně umístění skriptu může být libovolná tam, kde může virtuální počítač směrovat na tento koncový bod, jako je GitHub, interní souborový server atd.

### <a name="internet-connectivity"></a>Připojení k Internetu
Pokud potřebujete stáhnout skript externě, jako je GitHub nebo Azure Storage, pak je potřeba otevřít další porty skupiny zabezpečení sítě brány firewall/sítě. Pokud je například skript umístěný v Azure Storage, můžete přístup pomocí značek služeb Azure NSG pro [úložiště](../../virtual-network/network-security-groups-overview.md#service-tags)zpřístupnit.

Pokud je váš skript na místním serveru, můžete přesto potřebovat další porty skupiny zabezpečení brány firewall/sítě.

### <a name="tips-and-tricks"></a>Tipy a triky
* Selhání tohoto rozšíření nejčastěji způsobují chyby syntaxe ve skriptu. Otestujte, že se skript spustí bez chyb, a implementujte do skriptu dodatečné protokolování, které vám pomůže snadněji zjistit, kde došlo k selhání.
* Pište skripty, které jsou idempotentní, takže když se omylem spustí více než jednou, nezpůsobí to změny systému.
* Zajistěte, aby skripty při spuštění nevyžadovaly vstup uživatele.
* Pro spuštění skriptu je povolených 90 minut, což bude mít za následek neúspěšné zřízení rozšíření.
* Neumísťujte do skriptu restartování, což způsobí problémy s dalšími nainstalovanými rozšířeními a po restartování po restartování nebude rozšíření pokračovat. 
* Nedoporučujeme spouštět skript, který způsobí zastavení nebo aktualizaci agenta virtuálního počítače. To může mít příponu ve stavu přechodu a vést k vypršení časového limitu.
* Pokud máte skript, který způsobí restartování, nainstalujte aplikace a spusťte skripty atd. Restartování byste měli naplánovat pomocí úlohy cron nebo pomocí nástrojů, jako je DSC nebo Puppet rozšíření.
* Rozšíření spustí pouze jeden skript, pokud chcete spustit skript při každém spuštění, můžete použít [bitovou kopii Cloud-init](../linux/using-cloud-init.md)  a použít [skripty na spouštěcí](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modul. Případně můžete použít skript k vytvoření jednotky systémové služby.
* Pro virtuální počítač můžete použít jenom jednu verzi rozšíření. Aby bylo možné spustit druhý vlastní skript, je nutné odebrat rozšíření vlastních skriptů a znovu ho znovu použít s aktualizovaným skriptem. 
* Pokud chcete naplánovat, kdy se skript spustí, měli byste použít rozšíření k vytvoření úlohy cron. 
* Když je skript spuštěný, na webu Azure Portal nebo v rozhraní příkazového řádku se rozšíření zobrazí pouze v přechodném stavu. Pokud chcete častěji aktualizovat stav spuštěného skriptu, budete muset vytvořit vlastní řešení.
* Rozšíření vlastních skriptů nepodporují nativně proxy servery, ale můžete použít nástroj pro přenos souborů, který podporuje proxy servery ve vašem skriptu, jako je například *kudrlinkou*. 
* Mějte na paměti, že nevýchozí umístění adresářů, na kterých se můžou skripty nebo příkazy spoléhat, mají logiku k tomuto zpracování.
*  Při nasazování vlastního skriptu do produkčních VMSS instancí je navrženo nasazení prostřednictvím šablony JSON a uložení účtu úložiště skriptu, kde máte kontrolu nad tokenem SAS. 


## <a name="extension-schema"></a>Schéma rozšíření

Konfigurace rozšíření vlastních skriptů určuje například umístění skriptu a příkaz, který má být spuštěn. Tuto konfiguraci můžete uložit do konfiguračních souborů, zadat ji na příkazovém řádku nebo ji zadat v šabloně Azure Resource Manager. 

Citlivá data můžete ukládat do chráněné konfigurace, která je zašifrovaná a v rámci virtuálního počítače se šifruje jenom. Chráněná konfigurace je užitečná, když příkaz pro spuštění zahrnuje tajné klíče, jako je třeba heslo.

Tyto položky by měly být považovány za citlivá data a specifikována v konfiguraci nastavení chráněného rozšíření. Data nastavení chráněná rozšířením virtuálního počítače Azure jsou šifrovaná a v cílovém virtuálním počítači se dešifrují jenom.

```json
{
  "name": "config-app",
  "type": "Extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":123456789          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."],
       "managedIdentity" : "<managed-identity-identifier>"
    }
  }
}
```

>[!NOTE]
> vlastnost managedIdentity se **nesmí** používat ve spojení s vlastnostmi StorageAccountName nebo storageAccountKey.

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota/příklad | Typ dat | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| vydavatel | Microsoft. Compute. Extensions | řetězec |
| typ | CustomScript | řetězec |
| typeHandlerVersion | 2.1 | int |
| Identifikátory URI (např.) | `https://github.com/MyProject/Archive/MyPythonScript.py` | array |
| commandToExecute (např.) | Python MyPythonScript.py \<my-param1> | řetězec |
| script | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo = | řetězec |
| skipDos2Unix (např.) | false (nepravda) | boolean |
| časové razítko (např.) | 123456789 | 32-bitové celé číslo |
| storageAccountName (např.) | examplestorageacct | řetězec |
| storageAccountKey (např.) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | řetězec |
| managedIdentity (např.) | {} nebo {"clientId": "31b403aa-C364-4240-a7ff-d85fb6cd7232"} nebo {"objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b"} | objekt JSON |

### <a name="property-value-details"></a>Podrobnosti hodnoty vlastnosti
* `apiVersion`: Nejaktuálnější apiVersion se dá najít pomocí [Průzkumník prostředků](https://resources.azure.com/) nebo ze služby Azure CLI pomocí následujícího příkazu. `az provider list -o json`
* `skipDos2Unix`: (volitelné, logická hodnota) přeskočit dos2unix konverze adres URL souborů založených na skriptech nebo skriptu.
* `timestamp` (volitelné, 32 celé číslo) Toto pole použijte pouze k aktivaci opakovaného spuštění skriptu změnou hodnoty tohoto pole.  Je přijatelné libovolné celočíselné hodnoty; musí se lišit jenom od předchozí hodnoty.
* `commandToExecute`: (**vyžaduje** se, pokud skript není nastaven, řetězec) skript vstupního bodu, který se má spustit. Místo toho použijte toto pole, pokud váš příkaz obsahuje tajné klíče jako hesla.
* `script`: (**požadováno** , pokud commandToExecute není nastaven, String) skript kódovaný jako Base64 (a volitelně gzip'ed), který spustil/bin/sh.
* `fileUris`: (volitelné, pole řetězců) adresy URL pro soubory, které mají být staženy.
* `storageAccountName`: (volitelné, řetězec) název účtu úložiště. Pokud zadáte přihlašovací údaje úložiště, `fileUris` musí být všechny adresy URL pro objekty blob Azure.
* `storageAccountKey`: (volitelné, String) přístupový klíč účtu úložiště
* `managedIdentity`: (volitelné, objekt JSON) [spravovaná identita](../../active-directory/managed-identities-azure-resources/overview.md) pro stahování souborů
  * `clientId`: (volitelné, String) ID klienta spravované identity
  * `objectId`: (volitelné, String) ID objektu spravované identity


V rámci veřejného nebo chráněného nastavení lze nastavit následující hodnoty, rozšíření bude odmítat všechny konfigurace, kde jsou níže uvedené hodnoty nastaveny v nastavení veřejné i chráněné.
* `commandToExecute`
* `script`
* `fileUris`

Použití veřejného nastavení může být užitečné pro ladění, ale důrazně doporučujeme použít chráněná nastavení.

Veřejné nastavení se odesílá ve formě prostého textu do virtuálního počítače, ve kterém se skript spustí.  Chráněná nastavení se šifrují pomocí klíče, který je známý jenom pro Azure a virtuální počítač. Nastavení se uloží do virtuálního počítače, protože se poslala, tj. Pokud byla nastavení zašifrovaná, uloží se na virtuálním počítači jako šifrované. Certifikát použitý k dešifrování šifrovaných hodnot je uložený ve virtuálním počítači a slouží k dešifrování nastavení (v případě potřeby) za běhu.

#### <a name="property-skipdos2unix"></a>Vlastnost: skipDos2Unix

Výchozí hodnota je false, což znamená, že **je** proveden dos2unix převod.

Předchozí verze CustomScript, Microsoft. OSTCExtensions. CustomScriptForLinux, by automaticky převedla soubory DOS na soubory systému UNIX pomocí překladu `\r\n` na `\n` . Tento překlad stále existuje a je ve výchozím nastavení zapnutý. Tento převod se použije na všechny soubory stažené z identifikátorů URI nebo nastavení skriptu na základě některého z následujících kritérií.

* Pokud je rozšíření jedním z `.sh` , `.txt` , `.py` nebo `.pl` bude převedeno. Nastavení skriptu bude vždy odpovídat těmto kritériím, protože se předpokládá, že se jedná o skript spouštěný pomocí/bin/sh a je uložen jako script.sh na virtuálním počítači.
* Pokud soubor začíná na `#!` .

Konverzi dos2unix lze přeskočit nastavením skipDos2Unix na hodnotu true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Vlastnost: skript

CustomScript podporuje provádění skriptu definovaného uživatelem. Nastavení skriptu pro kombinování commandToExecute a identifikátorů URI do jediného nastavení. Místo toho, abyste museli nastavovat soubor ke stažení z úložiště Azure Storage nebo GitHubu, můžete skript jednoduše zakódovat jako nastavení. Skript lze použít k nahrazení commandToExecute a identifikátorů URI.

Skript **musí** být kódovaný v kódování Base64.  Skript může být **volitelně** gzip'ed. Nastavení skriptu lze použít ve veřejném nebo chráněném nastavení. Maximální velikost dat parametru skriptu je 256 KB. Pokud skript tuto velikost překročí, nebude proveden.

Například s ohledem na následující skript uložený do souboru/script.sh/.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Správné nastavení skriptu CustomScript se vytvoří tak, že se vytvoří výstup následujícího příkazu.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Skript může být volitelně gzip'ed k dalšímu snížení velikosti (ve většině případů). (CustomScript automaticky detekuje použití komprese GZip.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript používá k provedení skriptu následující algoritmus.

 1. vyhodnotit délku hodnoty skriptu nepřekračuje 256 KB.
 1. Base64 dekódovat hodnotu skriptu
 1. _pokus o_ gunzip hodnoty dekódovaného kódu base64
 1. zápis Dekódovatelné (případně dekomprimovaná) hodnoty na disk (/var/lib/waagent/Custom-Script/#/script.sh)
 1. Spusťte skript pomocí skriptu _/bin/sh-c/var/lib/waagent/Custom-Script/#/script.sh.

####  <a name="property-managedidentity"></a>Vlastnost: managedIdentity
> [!NOTE]
> Tato vlastnost **musí** být určena pouze v chráněných nastaveních.

CustomScript (verze 2,1 a vyšší) podporuje [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md) pro stahování souborů z adres URL, které jsou k dispozici v nastavení "identifikátory URI". Umožňuje CustomScript získat přístup k Azure Storage privátním objektům blob nebo kontejnerům bez toho, aby uživatel musel předávat tajné kódy, jako jsou tokeny SAS nebo klíče účtu úložiště.

Aby bylo možné tuto funkci používat, musí uživatel přidat identitu přiřazenou [systémem](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-system-assigned-identity) nebo [uživatelem přiřazenou](../../app-service/overview-managed-identity.md?tabs=dotnet#add-a-user-assigned-identity) k virtuálnímu počítači nebo VMSS, kde se očekává spuštění CustomScript, a [udělit spravované identitě přístup k kontejneru Azure Storage nebo objektu BLOB](../../active-directory/managed-identities-azure-resources/tutorial-vm-windows-access-storage.md#grant-access).

Pokud chcete použít identitu přiřazenou systémem na cílovém virtuálním počítači nebo VMSS, nastavte pole managedidentity na prázdný objekt JSON. 

> Příklad:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : {}
> }
> ```

Pokud chcete v cílovém virtuálním počítači/VMSS použít identitu přiřazenou uživatelem, nakonfigurujte pole managedidentity s ID klienta nebo ID objektu spravované identity.

> Příklady:
>
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "clientId": "31b403aa-c364-4240-a7ff-d85fb6cd7232" }
> }
> ```
> ```json
> {
>   "fileUris": ["https://mystorage.blob.core.windows.net/privatecontainer/script1.sh"],
>   "commandToExecute": "sh script1.sh",
>   "managedIdentity" : { "objectId": "12dd289c-0583-46e5-b9b4-115d5c19ef4b" }
> }
> ```

> [!NOTE]
> vlastnost managedIdentity se **nesmí** používat ve spojení s vlastnostmi StorageAccountName nebo storageAccountKey.

## <a name="template-deployment"></a>Nasazení šablon
Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Schéma JSON popsané v předchozí části lze použít v šabloně Azure Resource Manager ke spuštění rozšíření vlastních skriptů během nasazování Azure Resource Manager šablony. Ukázkovou šablonu, která obsahuje rozšíření vlastních skriptů, najdete tady: [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2019-03-01",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.1",
    "autoUpgradeMinorVersion": true,
    "settings": {
      },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <param2>",
      "fileUris": ["https://github.com/MyProject/Archive/hello.sh"
      ]  
    }
  }
}
```

>[!NOTE]
>U těchto názvů vlastností se rozlišují velká a malá písmena. Aby nedocházelo k potížím s nasazením, použijte názvy, jak je znázorněno zde.

## <a name="azure-cli"></a>Azure CLI
Pokud ke spuštění rozšíření vlastních skriptů používáte Azure CLI, vytvořte konfigurační soubor nebo soubory. Minimálně musíte mít ' commandToExecute '.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Volitelně můžete zadat nastavení v příkazu jako řetězec ve formátu JSON. To umožňuje zadání konfigurace během provádění a bez samostatného konfiguračního souboru.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Příklady Azure CLI

#### <a name="public-configuration-with-script-file"></a>Veřejná konfigurace se souborem skriptu

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Příkaz Azure CLI:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Veřejná konfigurace bez souboru skriptu

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Příkaz Azure CLI:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Veřejné a chráněné konfigurační soubory

Pomocí veřejného konfiguračního souboru určíte identifikátor URI souboru skriptu. Pomocí chráněného konfiguračního souboru určíte příkaz, který se má spustit.

Soubor veřejné konfigurace:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Chráněný konfigurační soubor:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Příkaz Azure CLI:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \ 
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Řešení potíží
Když se rozšíření vlastních skriptů spustí, skript se vytvoří nebo stáhne do adresáře, který je podobný následujícímu příkladu. Výstup příkazu je také uložen do tohoto adresáře v `stdout` souborech a `stderr` .

```bash
/var/lib/waagent/custom-script/download/0/
```

Pokud chcete řešit potíže, nejdřív zkontrolujte protokol agenta pro Linux, zkontrolujte, jestli je rozšíření spuštěné, zkontrolujte:

```bash
/var/log/waagent.log 
```

Měli byste Hledat spuštění rozšíření, bude vypadat přibližně takto:

```output
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```

Poznámka:
1. Povolit je při spuštění příkazu.
2. Stažení se týká stahování balíčku rozšíření CustomScript z Azure, nikoli souborů skriptu zadaných v identifikátorech URI.


Rozšíření skriptu Azure vytvoří protokol, který můžete najít tady:

```bash
/var/log/azure/custom-script/handler.log
```

Měli byste hledat konkrétní spuštění, bude vypadat přibližně takto:

```output
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=start
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=pre-check
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="comparing seqnum" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="seqnum saved" path=mrseq
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="reading configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="read configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating json schema"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="json schema valid"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsing configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="parsed configuration json"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validating configuration logically"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="validated configuration"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="creating output directory" path=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="created output directory"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 files=1
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download start"
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 file=0 event="download complete" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executing protected commandToExecute" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event="executed command" output=/var/lib/waagent/custom-script/download/0
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=enabled
time=2018-04-26T17:47:23Z version=v2.0.6/git@1008306-clean operation=enable seq=0 event=end
```

Tady můžete vidět:
* Tento protokol se spouští příkazem Enable.
* Nastavení předaná rozšíření
* Rozšíření stahuje soubor a jeho výsledek.
* Příkaz, který se spouští, a výsledek.

Můžete také načíst stav spuštění rozšíření vlastních skriptů včetně skutečných argumentů předávaných pomocí `commandToExecute` Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Výstup bude vypadat jako následující text:

```output
[
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/customscript",
    "resourceGroup": "rgname",
    "settings": {
      "commandToExecute": "sh script.sh > ",
      "fileUris": [
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh",
        "https://catalogartifact.azureedge.net/publicartifacts/scripts/script.sh"
      ]
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "2.0",
    "virtualMachineExtensionType": "CustomScript"
  },
  {
    "autoUpgradeMinorVersion": true,
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionid/resourceGroups/rgname/providers/Microsoft.Compute/virtualMachines/vmname/extensions/OmsAgentForLinux",
    "instanceView": null,
    "location": "eastus",
    "name": "OmsAgentForLinux",
    "protectedSettings": null,
    "provisioningState": "Succeeded",
    "publisher": "Microsoft.EnterpriseCloud.Monitoring",
    "resourceGroup": "rgname",
    "settings": {
      "workspaceId": "workspaceid"
    },
    "tags": null,
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "typeHandlerVersion": "1.0",
    "virtualMachineExtensionType": "OmsAgentForLinux"
  }
]
```

## <a name="next-steps"></a>Další kroky
Chcete-li zobrazit kód, aktuální problémy a verze, přečtěte si téma [vlastní-Script-rozšíření – úložiště Linux](https://github.com/Azure/custom-script-extension-linux).