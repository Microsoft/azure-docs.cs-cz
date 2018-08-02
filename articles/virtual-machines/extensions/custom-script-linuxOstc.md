---
title: Spuštění vlastních skriptů na virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Automatizaci úloh konfigurace virtuálního počítače s Linuxem pomocí rozšíření vlastních skriptů v1
services: virtual-machines-linux
documentationcenter: ''
author: zroiy
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/25/2018
ms.author: roiyz
ms.openlocfilehash: 918d09a870d5f8b523fb49141e4950ccdde825f2
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39413469"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Virtuální počítače s Linuxem pomocí Azure Custom Script rozšíření verze 1
Verze 1 pro rozšíření vlastního skriptu stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalace softwaru nebo jakoukoliv jinou úlohu konfigurace a správy. Skripty si můžete stáhnout z Azure Storage nebo jiné dostupné umístění v Internetu, nebo je lze zadat rozšíření modulu runtime. 

Rozšíření vlastních skriptů integruje šablony Azure Resource Manageru. Můžete ho spustit také pomocí rozhraní příkazového řádku Azure, PowerShell, na webu Azure portal nebo REST API služby Azure Virtual Machines.

Tento článek podrobně popisuje, jak použít rozšíření vlastních skriptů z příkazového řádku Azure a jak spustit rozšíření pomocí šablony Azure Resource Manageru. Tento článek také obsahuje postup řešení problémů v systémech Linux.


Existují dvě rozšíření vlastního skriptu Linux:
* Verze 1 – Microsoft.OSTCExtensions.CustomScriptForLinux
* Verze 2 - Microsoft.Azure.Extensions.CustomScript

Nové a stávající nasazení na použití nové verze, přepněte ([Microsoft.Azure.Extensions.CustomScript](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/custom-script-linux)) místo toho. Nová verze je určena být což je náhrada databáze. Proto se migrace je stejně snadné jako změna názvu a verze, není potřeba změnit konfiguraci rozšíření.

 

### <a name="operating-system"></a>Operační systém
Podporované distribuce Linuxu:

- CentOS 6.5 a vyšší
- Debian 8 a vyšší
    - Debian 8.7 se nedodává s Python2 nejnovější Image, která dělí CustomScriptForLinux.
- FreeBSD
- OpenSUSE 13.1 nebo vyšší
- Oracle Linux 6.4 a vyšší
- SUSE Linux Enterprise Server 11 SP3 nebo novější
- Ubuntu 12.04 a vyšší

### <a name="script-location"></a>Umístění skriptu

Můžete použít rozšíření použijte svoje přihlašovací údaje úložiště objektů Blob v Azure pro přístup k úložišti objektů Blob v Azure. Můžete také umístění skriptu může být libovolné where, tak dlouho, dokud virtuální počítač může směrovat do tohoto koncového bodu, jako je například GitHub, interní souborový server atd.

### <a name="internet-connectivity"></a>Připojení k Internetu
Pokud potřebujete stáhnout skript externě jako GitHub nebo služby Azure Storage, potom další brány firewall nebo sítě skupiny zabezpečení musí být otevřené porty. Například pokud váš skript nachází ve službě Azure Storage, můžete povolit přístup pomocí značky služeb Azure NSG pro [úložiště](https://docs.microsoft.com/en-us/azure/virtual-network/security-overview#service-tags).

Pokud váš skript je na místním serveru, pak může i nadále potřebovat další brány firewall nebo sítě zabezpečení musejí být otevřeny porty skupiny.

### <a name="tips-and-tricks"></a>Tipy a triky
* Nejvyšší chybovost pro toto rozšíření je z důvodu chyby syntaxe v skriptu testu, které skript se spustí bez chyb, a také vložit další protokolování do skriptu, aby bylo snazší najít, kde se nezdařilo.
* Psát skripty, které jsou idempotentní, takže pokud získat spusťte znovu více než jednou omylem, nesmí způsobit změny systému.
* Zajistěte, aby že skripty nevyžadují, aby uživatelský vstup při spuštění.
* Není povolené pro spuštění skriptu 90 minut, cokoli delšího způsobí selhání zřizování rozšíření.
* Neumisťujte restartování uvnitř skriptu, to způsobí problémy s další rozšíření, které se nainstalují, a po restartování počítače, rozšíření nebude pokračovat po restartování. 
* Pokud máte skript, který způsobí restartování, instalace aplikací a spouštět skripty atd. Měli byste naplánovat pomocí úlohy Cron, nebo pomocí nástrojů, jako je DSC, Chef, Puppet rozšíření nebo restartování.
* Rozšíření se pouze spuštění skriptu jednou, pokud chcete spustit skript na každém spuštění počítače, můžete použít [cloud-init image](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/using-cloud-init) a použít [skripty na spouštěcí](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modulu. Alternativně můžete použít skript k vytvoření jednotky služby Systemd.
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
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "<url>"
      ],
      "enableInternalDNSCheck": true
    },
    "protectedSettings": {
      "storageAccountName": "<storage-account-name>",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "<command>"
    }
  }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad | Typ dat | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| vydavatele | Microsoft.OSTCExtensions | řetězec |
| type | CustomScriptForLinux | řetězec |
| typeHandlerVersion | 1,5 | int |
| fileUris (např.) | https://github.com/MyProject/Archive/MyPythonScript.py | pole |
| commandToExecute (např.) | Python MyPythonScript.py < my parametr1 > | řetězec |
| enableInternalDNSCheck | true (pravda) | Boolean |
| storageAccountName (např.) | examplestorageacct | řetězec |
| storageAccountKey (např.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | řetězec |

### <a name="property-value-details"></a>Podrobnosti o hodnotě vlastnosti
* `fileUris`: (volitelné, pole řetězců) seznam identifikátorů uri skriptů
* `enableInternalDNSCheck`: (volitelné, logická hodnota) výchozí hodnota je True, chcete zakázat kontrolu DNS nastavena na hodnotu False.
* `commandToExecute`: (volitelné, string) skript vstupního bodu ke spuštění
* `storageAccountName`: (volitelné, string) název účtu úložiště
* `storageAccountKey`: (volitelné, string) přístupový klíč účtu úložiště

Následující hodnoty lze nastavit v nastavení veřejná nebo chráněná, nesmí mít následující sadu tyto hodnoty v nastavení veřejné a chráněné.
* `commandToExecute`

Pomocí nastavení veřejné může být užitečné pro ladění, ale důrazně doporučujeme používat chráněné nastavení.

Nastavení veřejné odesílají ve formátu prostého textu do virtuálních počítačů, ve kterém se skript spustí.  Chráněné nastavení jsou šifrované pomocí klíče zná pouze Azure a virtuální počítač. Nastavení se ukládají do virtuálního počítače, protože byly odeslány, tedy pokud byly šifrované nastavení jsou uloženy zašifrované na virtuálním počítači. Certifikát používaný k dešifrování šifrovaných hodnot je uložená ve virtuálním počítači a použité k dešifrování nastavení (v případě potřeby) za běhu.


## <a name="template-deployment"></a>Nasazení šablon
Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Schéma JSON, které jsou podrobně popsané v předchozí části lze použít v šabloně Azure Resource Manageru pro spuštění pomocí rozšíření vlastních skriptů při nasazení šablony Azure Resource Manageru. 


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
    "publisher": "Microsoft.OSTCExtensions",
    "type": "CustomScriptForLinux",
    "typeHandlerVersion": "1.5",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": ["http://MyAccount.blob.core.windows.net/vhds/MyShellScript.sh"]
    },
    "protectedSettings": {
      "storageAccountName": "MyAccount",
      "storageAccountKey": "<storage-account-key>",
      "commandToExecute": "sh MyShellScript.sh"
    }
  }
}
```

>[!NOTE]
>Tyto názvy vlastností rozlišují malá a velká písmena. Aby se zabránilo problémů s nasazením, použijte názvy, jak je znázorněno zde.

## <a name="azure-cli"></a>Azure CLI
Pokud používáte rozhraní příkazového řádku Azure pro spuštění pomocí rozšíření vlastních skriptů, vytvořte konfigurační soubor nebo soubory. Minimálně musí mít "commandToExecute".

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Volitelně můžete zadat nastavení v příkazu jako řetězec ve formátu JSON. To umožňuje konfiguraci zadat při spuštění a bez samostatného konfiguračního souboru.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Příklady Azure CLI

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
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
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
az vm extension set 
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Řešení potíží
Při spuštění rozšíření vlastních skriptů, vytvoření skriptu nebo stáhne do adresáře, který se podobá následujícímu příkladu. Výstup tohoto příkazu se také uloží do tohoto adresáře `stdout` a `stderr` soubory. 

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Řešení potíží s, nejprve najdete v protokolu agenta pro Linux, ověřte rozšíření běžel, zkontrolujte:

```bash
/var/log/waagent.log 
```

Je vhodné vyhledat spuštění rozšíření, to bude vypadat podobně jako:
```text
2018/04/26 15:29:44.835067 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Target handler state: enabled
2018/04/26 15:29:44.867625 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] [Enable] current handler state is: notinstalled
2018/04/26 15:29:44.959605 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Download, message=Download succeeded, duration=59
2018/04/26 15:29:44.993269 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Initialize extension directory
2018/04/26 15:29:45.022972 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Update settings file: 0.settings
2018/04/26 15:29:45.051763 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Install extension [customscript.py -install]
2018/04/26 15:29:45 CustomScriptForLinux started to handle.
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] cwd is /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2
2018/04/26 15:29:45 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Change log file to /var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log
2018/04/26 15:29:46.088212 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Install, message=Launch command succeeded: customscript.py -install, duration=1005
2018/04/26 15:29:46.133367 INFO [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable extension [customscript.py -enable]
2018/04/26 15:29:46 CustomScriptForLinux started to handle.
..
2018/04/26 15:29:47.178163 INFO Event: name=Microsoft.OSTCExtensions.CustomScriptForLinux, op=Enable, message=Launch command succeeded: customscript.py -enable, duration=1012
```
Některé body, které mějte na paměti:
1. Povolit je při spuštění příkazu.
2. Stahování se týká stahování balíčku rozšíření CustomScript z Azure, není skript soubory určené v fileUris.
3. Zobrazí se také určení souboru protokolu zapisuje navýšení kapacity na "/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log".

Dalším krokem je přejít kontrolu souboru protokolu, to je formát:
```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Je vhodné vyhledat induvidual provedení příkazu, to bude vypadat podobně jako:
```text
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Enable,transitioning,0,Launching the script...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] sequence number is 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] setting file path is/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/config/0.settings
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] JSON config: {"runtimeSettings": [{"handlerSettings": {"protectedSettings": "MIIB0AYJKoZIhvcNAQcDoIIBwTCCAb0CAQAxggF+hnEXRtFKTTuKiFC8gTfHKupUSs7qI0zFYRya", "publicSettings": {"fileUris": ["https://dannytesting.blob.core.windows.net/demo/myBash.sh"]}, "protectedSettingsCertThumbprint": "4385AB21617C2452FF6998C0A37F71A0A01C8368"}}]}
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Config decoded correctly.
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Will try to download files, number of retries = 10, wait SECONDS between retrievals = 20s
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Downloading,transitioning,0,Downloading files...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] No azure storage account and key specified in protected settings. Downloading scripts from external links...
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Converting /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh from DOS to Unix formats: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Removing BOM of /var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2/download/0/myBash.sh: Done
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Succeeded to download files, retry count = 0
2018/04/26 15:29:46 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Internal DNS is ready, retry count = 0
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 
2018/04/26 15:29:47 [Microsoft.OSTCExtensions.CustomScriptForLinux-1.5.2.2] Daemon,success,0,Command is finished.
2018/04/26 15:29:47 ---stdout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 ---errout---
2018/04/26 15:29:47 
2018/04/26 15:29:47 
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
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Další postup
Kód, aktuální problémy a verze, najdete v sekci [rozšíření CustomScript úložiště](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).

