---
title: Spustit vlastní skripty na virtuální počítače s Linuxem v Azure | Microsoft Docs
description: Automatizovat úkoly konfigurace virtuálního počítače s Linuxem pomocí rozšíření vlastních skriptů v2
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
ms.openlocfilehash: e614c78ca4e8ed7afbce0d3f2423ce137c5225b5
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294971"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Pomocí virtuální počítače s Linuxem v Azure vlastní skript rozšíření verze 2
Vlastní skript rozšíření verze 2 se stáhne a spustí skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalace softwaru nebo jiná úloha správy nebo konfigurace. Skripty si můžete stáhnout z Azure Storage nebo jiné dostupné umístění v Internetu, nebo je můžete zadat rozšíření modulu runtime. 

Rozšíření vlastních skriptů se integruje s šablon Azure Resource Manageru. Můžete také spustit jej pomocí rozhraní příkazového řádku Azure, PowerShell, portálu Azure nebo REST API pro virtuální počítače Azure.

Tento článek podrobně popisuje, jak používat rozšíření vlastních skriptů z příkazového řádku Azure a jak spustit rozšíření pomocí šablony Azure Resource Manager. Tento článek také obsahuje pokyny k odstraňování potíží pro systémy Linux.


Existují dvě rozšíření vlastních skriptů Linux:
* Verze 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Verze 2 - Microsoft.Azure.Extensions.CustomScript

Přejděte prosím nové a stávající nasazení místo toho použít nová verze 2. Nové verze je určena jako náhrada drop-in. Proto migrace je stejně snadná jako změnit název a verze, není potřeba změnit konfiguraci rozšíření.


### <a name="operating-system"></a>Operační systém

Rozšíření vlastních skriptů Linux se spustí na rozšíření rozšíření podporován operační systém na, další informace najdete v tématu to [článku](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems).

### <a name="script-location"></a>Umístění skriptu

Rozšíření můžete použijte svoje přihlašovací údaje úložiště objektů Blob v Azure pro přístup k úložišti objektů Blob v Azure. Případně umístění skriptu může být žádné where, tak dlouho, dokud virtuální počítač může směrovat do tohoto koncového bodu, jako je například Githubu, interní souborového serveru atd.

### <a name="internet-connectivity"></a>Připojení k Internetu
Pokud budete muset stáhnout skript externě například GitHub nebo úložiště Azure, pak další brány firewall nebo síťových zabezpečení skupiny musí být otevřené porty. Například pokud váš skript nachází ve službě Azure Storage, můžete povolit přístup pomocí značek služby Azure skupina NSG pro [úložiště](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Pokud váš skript se na místním serveru, pak může potřebujete ještě další brány firewall nebo síťových zabezpečení muset otevřít porty skupiny.

### <a name="tips-and-tricks"></a>Tipy a triky
* Nejvyšší míra selhání pro toto rozšíření je z důvodu chyby syntaxe ve skriptu, testů, které skript se spustí bez chyby, a také vložit do další protokolování do skript tak, aby bylo snazší najít, kde se nezdařilo.
* Napsat skripty, které jsou idempotent, takže pokud získat spustit znovu, více než jednou omylem, nezpůsobí změny systému.
* Zajistěte, aby že skripty nevyžadují vstup uživatele při spuštění.
* Je 90 minut povolené pro spuštění skriptu, nic delší způsobí selhání zřídit rozšíření.
* Nevkládejte restartování uvnitř skriptu, to může způsobit problémy s další rozšíření, které se nainstalují, a po restartování, rozšíření nebude pokračovat po restartování. 
* Pokud máte skript, který způsobí restart, instalace aplikací a spouštět skripty atd. Měli byste naplánovat pomocí procesu Cron úlohy, nebo pomocí nástrojů, jako je DSC, Chef, Puppet rozšíření nebo restartování.
* Rozšíření pouze spustí skript jednou, pokud chcete spustit skript na každém spuštění počítače, můžete použít [cloudu init image](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/using-cloud-init) a použít [skriptů na spouštěcí](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modulu. Alternativně můžete použít skript vytvoření jednotky Systemd služby.
* Pokud chcete naplánovat, kdy bude skript spuštěn, používejte k vytvoření úlohy Cron rozšíření. 
* Když je skript spuštěn, zobrazí se pouze 'přechodu, stav rozšíření z portálu Azure nebo rozhraní příkazového řádku. Pokud chcete častější aktualizace stavu spouštění skriptu, musíte vytvořit vlastní řešení.
* Rozšíření vlastních skriptů nenabízí nativní podporu proxy servery, ale můžete použít nástroj přenos souboru, který podporuje proxy servery v rámci vašeho skriptu, například *Curl*. 
* Mějte na jiné výchozí umístění adresáře, které skripty nebo příkazy, můžou záviset, mají logiku pro toto zpracování.



## <a name="extension-schema"></a>Schéma rozšíření

Konfigurace rozšíření vlastních skriptů určuje takové věci, jako umístění skriptu a příkaz ke spuštění. Můžete tuto konfiguraci uložit konfigurační soubory, zadejte na příkazovém řádku nebo zadejte jej v šablonu Azure Resource Manager. 

V konfiguraci chráněného, které se budou šifrovat a dešifrovat jenom ve virtuálním počítači můžete ukládat citlivá data. Chráněné konfigurace je užitečné při provádění příkazu zahrnuje tajné klíče, jako například heslo.

Tyto položky by měl být považován za citlivá data a zadaný v konfiguraci chráněných nastavení rozšíření. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaná a dešifrovat jenom na cílový virtuální počítač.

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

| Název | Hodnota nebo příklad | Typ dat | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| Vydavatele | Microsoft.Compute.Extensions | řetězec |
| type | CustomScript | řetězec |
| typeHandlerVersion | 2.0 | celá čísla |
| fileUris (např.) | https://github.com/MyProject/Archive/MyPythonScript.py | pole |
| commandToExecute (např.) | Python MyPythonScript.py < Moje param1 > | řetězec |
| Skript | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo = | řetězec |
| skipDos2Unix (např.) | false (nepravda) | Boolean |
| časové razítko (např.) | 123456789 | 32bitové celé číslo |
| storageAccountName (např.) | examplestorageacct | řetězec |
| storageAccountKey (např.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | řetězec |

### <a name="property-value-details"></a>Podrobnosti o hodnotě vlastnosti
* `skipDos2Unix`: (nepovinný, logická hodnota) přeskočit dos2unix převod adres URL založených na skriptech soubor nebo skript.
* `timestamp` Toto pole pouze pro aktivaci znovu spusťte skript tím, že změníte hodnotu tohoto pole použití (volitelné 32bitové celé číslo).  Všechny celočíselná hodnota je přijatelné; musí být pouze liší od předchozí hodnotu.
 * `commandToExecute`: (**požadované** Pokud skript není nastavena, řetězec) k provedení skriptu bodu položku. Toto pole použijte místo toho, pokud váš příkaz obsahuje tajné klíče, jako jsou hesla.
* `script`: (**požadované** Pokud commandToExecute není nastavena, řetězec) kódováním base64 (a volitelně gzip'ed) provést/Bin/dílet skriptu.
* `fileUris`: (volitelné, pole řetězců) adresy URL pro soubory ke stažení.
* `storageAccountName`: (volitelné, string) název účtu úložiště. Pokud chcete zadat pověření pro úložiště, všechny `fileUris` musí být adresy URL pro objekty BLOB Azure.
* `storageAccountKey`: (volitelné, string) přístupový klíč účtu úložiště


Následující hodnoty lze nastavit v nastavení veřejného nebo chráněné, rozšíření odmítnou jakákoli konfigurace, kde se níže uvedené hodnoty nastavují v nastavení veřejných a chráněné.
* `commandToExecute`
* `script`
* `fileUris`

Pomocí nastavení veřejných může být užitečné pro ladění, ale důrazně doporučujeme použít chráněné nastavení.

Nastavení veřejných jsou odesílány ve formátu prostého textu k virtuálnímu počítači, kde bude skript proveden.  Chráněné nastavení jsou šifrované pomocí klíče zná pouze Azure a virtuální počítač. Nastavení se ukládají do virtuálního počítače, protože byly odeslány, tj. Pokud byly šifrované nastavení jsou uloženy šifrované ve virtuálním počítači. Certifikát používaný k dešifrování šifrovaných hodnot je uložená ve virtuálním počítači a použité k dešifrování nastavení (v případě potřeby) za běhu.

#### <a name="property-skipdos2unix"></a>Vlastnost: skipDos2Unix

Výchozí hodnota je nastavena hodnota false, což znamená, že dos2unix conversion **je** provést.

V předchozí verzi CustomScript, Microsoft.OSTCExtensions.CustomScriptForLinux, by automaticky DOS soubory převést do souborů UNIX při převodu `\r\n` k `\n`. Tento překlad stále existuje a ve výchozím nastavení zapnutý. Tento převod se použijí na všechny soubory stahované z fileUris nebo pomocí nastavení skriptu, který je založen na některém z následujících kritérií.

* Pokud rozšíření je jedním z `.sh`, `.txt`, `.py`, nebo `.pl` bude převeden. Nastavení skriptu bude vždy odpovídat tato kritéria, protože bude skript proveden s /bin/sh hodnota který je uložený jako script.sh ve virtuálním počítači.
* Pokud soubor začíná `#!`.

Převod dos2unix mohou být přeskočeny nastavením skipDos2Unix na hodnotu true.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>",
  "skipDos2Unix": true
}
```

####  <a name="property-script"></a>Vlastnost: skript

CustomScript podporuje provádění skriptu definovaný uživatelem. Nastavení skriptu ke kombinování commandToExecute a fileUris do jednoho nastavení. Místo nutnosti nastavit soubor ke stažení z úložiště Azure nebo gist Githubu, můžete jednoduše zakódovat skript jako nastavení. Skript lze nahrazené commandToExecute a fileUris.

Skript **musí** se kódování base64.  Skript může **volitelně** být gzip'ed. Skript nastavení může být použito v nastavení veřejného nebo chráněný. Maximální velikost dat parametr skriptu je 256 KB. Pokud skript překročí tuto velikost, nebudou provedeny.

Například následující skript uloží do souboru /script.sh/ zadána.

```sh
#!/bin/sh
echo "Updating packages ..."
apt update
apt upgrade -y
```

Správné nastavení skriptu CustomScript by zkonstruovat provedením výstup následující příkaz.

```sh
cat script.sh | base64 -w0
```

```json
{
  "script": "IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo="
}
```

Skript může být volitelně gzip'ed k dalšímu snížení velikosti (ve většině případů). (CustomScript automaticky zjistí použití kompresi gzip.)

```sh
cat script | gzip -9 | base64 -w 0
```

CustomScript používá následující algoritmus pro spuštění skriptu.

 1. Assert –, že délka hodnoty ke skriptu není delší než 256 KB.
 1. Tento skript hodnota dekódovat formátu Base64.
 1. _Pokus_ k gunzip dekódovat Base64, pomocí hodnoty
 1. zápis hodnoty dekódované (a volitelně dekomprimovaných) na disk (nebo var/lib/waagent/custom-script/#/script.sh)
 1. Spusťte skript pomocí _ / bin/dílet - c /var/lib/waagent/custom-script/#/script.sh.


## <a name="template-deployment"></a>Nasazení šablon
Rozšíření virtuálního počítače Azure se dá nasadit pomocí šablon Azure Resource Manager. Schéma JSON, které jsou popsané v předchozí části lze použít v šablonu Azure Resource Manager ke spuštění rozšíření vlastních skriptů při nasazení šablony Azure Resource Manager. Ukázka šablony, která obsahuje rozšíření vlastních skriptů je zde uveden, [Githubu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


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
      "fileUris": ["https://github.com/MyProject/Archive/MyPythonScript.py"
      ]  
    }
  }
}
```

>[!NOTE]
>Názvy těchto vlastností jsou malá a velká písmena. Abyste zabránili problémům nasazení, použijte názvy, jak je vidět tady.

## <a name="azure-cli"></a>Azure CLI
Pokud používáte Azure CLI ke spuštění rozšíření vlastních skriptů, vytvořte konfigurační soubor nebo soubory. Minimálně musí mít 'commandToExecute'.

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --protected-settings ./script-config.json
```

Volitelně můžete zadat nastavení v příkazu jako řetězec formátu JSON. To umožňuje konfiguraci zadat během provádění a bez jiný konfigurační soubor.

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

Azure CLI příkaz:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Veřejná konfigurace s žádný soubor skriptu

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI příkaz:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Veřejné a chráněné konfigurační soubory

Použít soubor veřejné konfigurace zadat soubor skriptu identifikátor URI. Použijete chráněné konfiguračního souboru k zadání příkazu ke spuštění.

Veřejné konfigurační soubor:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Chráněné konfigurační soubor:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLI příkaz:

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
Při spuštění rozšíření vlastních skriptů, skript se vytvoří nebo stáhli do adresáře, který je podobný v následujícím příkladu. Výstup příkazu je také uložen do tohoto adresáře `stdout` a `stderr` soubory.

```bash
/var/lib/waagent/custom-script/download/0/
```

Řešení potíží s, nejprve zkontrolujte protokol agenta systému Linux, zajišťovat provádění rozšíření spustili, zkontrolujte:

```bash
/var/log/waagent.log 
```

Je vhodné vyhledat rozšíření spuštění, bude vypadat nějak takto:
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
Některé body Všimněte si:
1. Povolit je při spuštění příkazu.
2. Stažení má vztah ke stahování balíčku rozšíření CustomScript z Azure, ne soubory skriptu zadaný v fileUris.


Přípona skriptu Azure vytvoří protokol, který najdete tady:

```bash
/var/log/azure/custom-script/handler.log
```

Je vhodné vyhledat induvidual provádění, bude vypadat nějak takto:
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
Zde se zobrazí:
* Spuštěním příkazu Enable je tento protokol
* Nastavení předaná rozšíření
* Rozšíření stahování souborů a výsledek této.
* Příkaz, který se spustí a výsledek.

Provádění stav rozšíření vlastních skriptů můžete také načíst pomocí rozhraní příkazového řádku Azure:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Výstup vypadá následující text:

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
Kód, aktuální problémy a verze, najdete v sekci [vlastní skript rozšíření linux úložišti](https://github.com/Azure/custom-script-extension-linux).

