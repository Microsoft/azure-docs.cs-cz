---
title: Spuštění vlastních skriptů na virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Automatizaci úloh konfigurace virtuálního počítače s Linuxem pomocí rozšíření vlastních skriptů v2
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: cf17ab2b-8d7e-4078-b6df-955c6d5071c2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: danis
ms.openlocfilehash: 433b750c1078828dcc3b18234125adf04625dbe8
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325169"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Virtuální počítače s Linuxem pomocí Azure Custom Script rozšíření verze 2
Verze 2 pro rozšíření vlastního skriptu stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalace softwaru nebo jakoukoliv jinou úlohu konfigurace a správy. Skripty si můžete stáhnout z Azure Storage nebo jiné dostupné umístění v Internetu, nebo je lze zadat rozšíření modulu runtime. 

Rozšíření vlastních skriptů integruje šablony Azure Resource Manageru. Můžete ho spustit také pomocí rozhraní příkazového řádku Azure, PowerShell, na webu Azure portal nebo REST API služby Azure Virtual Machines.

Tento článek podrobně popisuje, jak použít rozšíření vlastních skriptů z příkazového řádku Azure a jak spustit rozšíření pomocí šablony Azure Resource Manageru. Tento článek také obsahuje postup řešení problémů v systémech Linux.


Existují dvě rozšíření vlastního skriptu Linux:
* Verze 1 – Microsoft.OSTCExtensions.CustomScriptForLinux
* Verze 2 - Microsoft.Azure.Extensions.CustomScript

Přepněte nové a stávající nasazení místo toho použít nová verze 2. Nová verze je určena být což je náhrada databáze. Proto se migrace je stejně snadné jako změna názvu a verze, není potřeba změnit konfiguraci rozšíření.


### <a name="operating-system"></a>Operační systém

Rozšíření vlastních skriptů pro Linux se spustí na rozšíření nepodporuje rozšíření OS, další informace najdete v tomto [článku](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Umístění skriptu

Můžete použít rozšíření použijte svoje přihlašovací údaje úložiště objektů Blob v Azure pro přístup k úložišti objektů Blob v Azure. Můžete také umístění skriptu může být libovolné where, tak dlouho, dokud virtuální počítač může směrovat do tohoto koncového bodu, jako je například GitHub, interní souborový server atd.

### <a name="internet-connectivity"></a>Připojení k Internetu
Pokud potřebujete stáhnout skript externě jako GitHub nebo služby Azure Storage, potom další brány firewall nebo sítě skupiny zabezpečení musí být otevřené porty. Například pokud váš skript nachází ve službě Azure Storage, můžete povolit přístup pomocí značky služeb Azure NSG pro [úložiště](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Pokud váš skript je na místním serveru, pak může i nadále potřebovat další brány firewall nebo sítě zabezpečení musejí být otevřeny porty skupiny.

### <a name="tips-and-tricks"></a>Tipy a triky
* Nejvyšší chybovost pro toto rozšíření je z důvodu chyby syntaxe v skriptu testu, které skript se spustí bez chyb, a také vložit další protokolování do skriptu, aby bylo snazší najít, kde se nezdařilo.
* Psát skripty, které jsou idempotentní, takže pokud získat spusťte znovu více než jednou omylem, nesmí způsobit změny systému.
* Zajistěte, aby že skripty nevyžadují, aby uživatelský vstup při spuštění.
* Není povolené pro spuštění skriptu 90 minut, cokoli delšího způsobí selhání zřizování rozšíření.
* Neumisťujte restartování uvnitř skriptu, to způsobí problémy s další rozšíření, které se nainstalují, a po restartování počítače, rozšíření nebude pokračovat po restartování. 
* Pokud máte skript, který způsobí restartování, instalace aplikací a spouštět skripty atd. Měli byste naplánovat pomocí úlohy Cron, nebo pomocí nástrojů, jako je DSC, Chef, Puppet rozšíření nebo restartování.
* Rozšíření se pouze spuštění skriptu jednou, pokud chcete spustit skript na každém spuštění počítače, můžete použít [cloud-init image](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init) a použít [skripty na spouštěcí](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modulu. Alternativně můžete použít skript k vytvoření jednotky služby Systemd.
* Pokud chcete naplánovat, kdy bude skript spuštěn, používejte rozšíření k vytvoření úlohy Cron. 
* Když je spuštěný skript, zobrazí se pouze "přenos" stav rozšíření z webu Azure portal nebo rozhraní příkazového řádku. Pokud chcete častější aktualizace stavu spuštění skriptu, je potřeba vytvořit svoje vlastní řešení.
* Rozšíření vlastních skriptů nativně nepodporuje proxy servery, ale můžete použít nástroj pro přenos souborů, která podporuje proxy servery v rámci skriptu, jako například *Curl*. 
* Mějte na paměti z jiné výchozí adresář umístění, které mohou spoléhat skripty nebo příkazy, mají logiky, která by to.



## <a name="extension-schema"></a>Schéma rozšíření

Konfiguraci rozšíření vlastních skriptů určuje věci, jako je umístění skriptu a příkazu ke spuštění. Uložit tuto konfiguraci v konfiguračních souborech, zadejte na příkazovém řádku nebo ho zadat v šabloně Azure Resource Manageru. 

Ukládat citlivá data v chráněném konfigurace, který je šifrovaný a dešifrovat jenom uvnitř virtuálního počítače. Chráněné konfigurace je užitečná při provádění příkazu zahrnuje tajné kódy jako jsou hesla.

Tyto položky by měly považovat za citlivá data a zadaný v konfiguraci chráněných nastavení rozšíření. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaný a dešifrovat jenom na cílovém virtuálním počítači.

```json
{
  "name": "config-app",
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "skipDos2Unix":false,
      "timestamp":"123456789",          
    },
    "protectedSettings": {
       "commandToExecute": "<command-to-execute>",
       "script": "<base64-script-to-execute>",
       "storageAccountName": "<storage-account-name>",
       "storageAccountKey": "<storage-account-key>",
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad | Typ dat | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| vydavatele | Microsoft.Compute.Extensions | řetězec |
| type | CustomScript | řetězec |
| typeHandlerVersion | 2.0 | int |
| fileUris (např.) | https://github.com/MyProject/Archive/MyPythonScript.py | pole |
| commandToExecute (např.) | Python MyPythonScript.py < my parametr1 > | řetězec |
| skript | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo = | řetězec |
| skipDos2Unix (např.) | false (nepravda) | Boolean |
| časové razítko (např.) | 123456789 | 32bitové celé číslo |
| storageAccountName (např.) | examplestorageacct | řetězec |
| storageAccountKey (např.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | řetězec |

### <a name="property-value-details"></a>Podrobnosti o hodnotě vlastnosti
* `skipDos2Unix`: (volitelné, logická hodnota) přeskočit dos2unix převodu adres URL založených na skriptech souboru nebo skriptu.
* `timestamp` (volitelné, 32bitové celé číslo), použijte toto pole pouze pro aktivaci znovu spusťte tento skript změnou hodnoty tohoto pole.  Libovolné celé číslo je přijatelné; musí být pouze jiný než předchozí hodnota.
 * `commandToExecute`: (**požadované** Pokud skript není nastavený, string) skript vstupního bodu ke spuštění. Místo toho použijte toto pole, pokud váš příkaz obsahuje tajné kódy jako jsou hesla.
* `script`: (**požadované** Pokud commandToExecute není nastavený, string) kódováním base64 (a volitelně gzip'ed) skript spuštěn pomocí/Bin/sh.
* `fileUris`: (volitelné, pole řetězců) adresy URL pro soubory ke stažení.
* `storageAccountName`: (volitelné, string) název účtu úložiště. Pokud zadáte přihlašovací údaje úložiště všechny `fileUris` musí být adresy URL pro objekty BLOB Azure.
* `storageAccountKey`: (volitelné, string) přístupový klíč účtu úložiště


Následující hodnoty lze nastavit v nastavení veřejná nebo chráněná, rozšíření odmítnou jakékoli konfigurace, ve kterém níže uvedené hodnoty se nastavují v nastavení veřejné a chráněné.
* `commandToExecute`
* `script`
* `fileUris`

Pomocí nastavení veřejné může být užitečné pro ladění, ale důrazně doporučujeme používat chráněné nastavení.

Nastavení veřejné odesílají ve formátu prostého textu do virtuálních počítačů, ve kterém se skript spustí.  Chráněné nastavení jsou šifrované pomocí klíče zná pouze Azure a virtuální počítač. Nastavení se ukládají do virtuálního počítače, protože byly odeslány, tedy pokud byly šifrované nastavení jsou uloženy zašifrované na virtuálním počítači. Certifikát používaný k dešifrování šifrovaných hodnot je uložená ve virtuálním počítači a použité k dešifrování nastavení (v případě potřeby) za běhu.

#### <a name="property-skipdos2unix"></a>Vlastnost: skipDos2Unix

Výchozí hodnota je false, což znamená, že převod dos2unix **je** proveden.

Předchozí verze CustomScript Microsoft.OSTCExtensions.CustomScriptForLinux, by automaticky převést souborů DOS souborů UNIX překladu `\r\n` k `\n`. Tento překlad stále existuje a je ve výchozím. Tento převod platí pro všechny soubory stahované z fileUris nebo tento skript se změněnými založené na některé z následujících kritérií.

* Pokud rozšíření je jedním z `.sh`, `.txt`, `.py`, nebo `.pl` budou převedeny. Tento skript se změněnými vždy odpovídat tato kritéria, protože se předpokládá, že bude skript spuštěn /bin/sh a je uložen jako script.sh na virtuálním počítači.
* Pokud soubor začíná `#!`.

Převod dos2unix mohly být přeskočeny, tak, že nastavíte skipDos2Unix na hodnotu true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Vlastnost: skript

CustomScript podporuje provádění skriptu definovaný uživatelem. Nastavení skriptu commandToExecute a fileUris zkombinovat do jednoho nastavení. Namísto toho na instalační soubor ke stažení ze služby Azure storage nebo GitHub gist, můžete jednoduše kódovat skript jako nastavení. Skript je možné do nahrazeného commandToExecute a fileUris.

Skript **musí** kódovaný ve formátu base64.  Skript může **volitelně** být gzip'ed. Tento skript se změněnými lze v nastavení veřejná nebo chráněná. Maximální velikost dat parametr skriptu je 256 KB. Pokud skript je větší než tato velikost, nebude provedeno.

Například následující skript uložit do souboru /script.sh/ zadaný.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Správné nastavení ve skriptu CustomScript by vytvořený pomocí výstupu následujícího příkazu.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Skript může být volitelně gzip'ed dál snížit velikost (ve většině případů). (CustomScript automaticky zjistí použití kompresi gzip.)

```sh
cat script | gzip -9 | base64 -w 0
```

Následující požadovaný algoritmus CustomScript používá ke spuštění skriptu.

 1. Assert – délka hodnoty skriptu nepřekračuje 256 KB.
 1. Tento skript hodnotu dekódovat z formátu Base64
 1. _Pokus_ k gunzip base64 dekódovaná hodnota
 1. zapsat hodnotu dekódovaný (a volitelně dekomprimovaný) na disk (/ var/lib/waagent/custom-script/#/script.sh)
 1. Spusťte skript pomocí _ / bin/sh – c /var/lib/waagent/custom-script/#/script.sh.


## <a name="template-deployment"></a>Nasazení šablon
Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Schéma JSON, které jsou podrobně popsané v předchozí části lze použít v šabloně Azure Resource Manageru pro spuštění pomocí rozšíření vlastních skriptů při nasazení šablony Azure Resource Manageru. Ukázková šablona, která zahrnuje rozšíření vlastních skriptů můžete najít zde, [Githubu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


```json
{
  "name": "config-app",
  "type": "extensions",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-06-15",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
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
>Tyto názvy vlastností rozlišují malá a velká písmena. Aby se zabránilo problémů s nasazením, použijte názvy, jak je znázorněno zde.

## <a name="azure-cli"></a>Azure CLI
Pokud používáte rozhraní příkazového řádku Azure pro spuštění pomocí rozšíření vlastních skriptů, vytvořte konfigurační soubor nebo soubory. Minimálně musí mít "commandToExecute".

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Volitelně můžete zadat nastavení v příkazu jako řetězec ve formátu JSON. To umožňuje konfiguraci zadat při spuštění a bez samostatného konfiguračního souboru.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Příklady Azure CLI

#### <a name="public-configuration-with-script-file"></a>Veřejná konfigurace pomocí souboru skriptu

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

#### <a name="public-configuration-with-no-script-file"></a>Veřejná konfigurace se žádný soubor skriptu

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

#### <a name="public-and-protected-configuration-files"></a>Veřejné a chráněné konfiguračních souborů

Soubor veřejné konfigurace můžete zadat soubor skriptu identifikátoru URI. Používat chráněný konfiguračního souboru k určení příkazu ke spuštění.

Soubor veřejné konfigurace:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Chráněné konfiguračního souboru:  

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
Při spuštění rozšíření vlastních skriptů, vytvoření skriptu nebo stáhne do adresáře, který se podobá následujícímu příkladu. Výstup tohoto příkazu se také uloží do tohoto adresáře `stdout` a `stderr` soubory.

```bash
/var/lib/waagent/custom-script/download/0/
```

Řešení potíží s, nejprve najdete v protokolu agenta pro Linux, ověřte rozšíření běžel, zkontrolujte:

```bash
/var/log/waagent.log 
```

Je vhodné vyhledat spuštění rozšíření, to bude vypadat podobně jako:
```text
2018/04/26 17:47:22.110231 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] [Enable] current handler state is: notinstalled
2018/04/26 17:47:22.306407 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Download, message=Download succeeded, duration=167
2018/04/26 17:47:22.339958 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Initialize extension directory
2018/04/26 17:47:22.368293 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Update settings file: 0.settings
2018/04/26 17:47:22.394482 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Install extension [bin/custom-script-shim install]
2018/04/26 17:47:23.432774 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Install, message=Launch command succeeded: bin/custom-script-shim install, duration=1007
2018/04/26 17:47:23.476151 INFO [Microsoft.Azure.Extensions.customScript-2.0.6] Enable extension [bin/custom-script-shim enable]
2018/04/26 17:47:24.516444 INFO Event: name=Microsoft.Azure.Extensions.customScript, op=Enable, message=Launch command succeeded: bin/custom-sc
```
Některé body, které mějte na paměti:
1. Povolit je při spuštění příkazu.
2. Stahování se týká stahování balíčku rozšíření CustomScript z Azure, není skript soubory určené v fileUris.


Rozšíření skriptů Azure vytvoří protokol, který najdete tady:

```bash
/var/log/azure/custom-script/handler.log
```

Je vhodné vyhledat induvidual provedení příkazu, to bude vypadat podobně jako:
```text
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
Zde můžete zobrazit:
* Povolit příkaz spuštění je tento protokol
* Nastavení předaná rozšíření
* Rozšíření stahování souborů a výsledek tohoto objektu.
* Příkaz, který se spustí a výsledek.

Stav provádění rozšíření vlastních skriptů můžete také načíst pomocí rozhraní příkazového řádku Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Výstup bude vypadat jako následující text:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Další postup
Kód, aktuální problémy a verze, najdete v sekci [vlastní skript rozšíření linux úložiště](https://github.com/Azure/custom-script-extension-linux).

