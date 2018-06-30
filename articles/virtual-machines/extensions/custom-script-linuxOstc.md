---
title: Spustit vlastní skripty na virtuální počítače s Linuxem v Azure | Microsoft Docs
description: Automatizovat úkoly konfigurace virtuálního počítače s Linuxem pomocí rozšíření vlastních skriptů v1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
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
ms.author: danis
ms.openlocfilehash: 526021ca238be7bc934e639c34d3e49879279a6a
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37127648"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Virtuální počítače s Linuxem pomocí Azure vlastní skript rozšíření verze 1
Vlastní skript rozšíření verze 1 se stáhne a spustí skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalace softwaru nebo jiná úloha správy nebo konfigurace. Skripty si můžete stáhnout z Azure Storage nebo jiné dostupné umístění v Internetu, nebo je můžete zadat rozšíření modulu runtime. 

Rozšíření vlastních skriptů se integruje s šablon Azure Resource Manageru. Můžete také spustit jej pomocí rozhraní příkazového řádku Azure, PowerShell, portálu Azure nebo REST API pro virtuální počítače Azure.

Tento článek podrobně popisuje, jak používat rozšíření vlastních skriptů z příkazového řádku Azure a jak spustit rozšíření pomocí šablony Azure Resource Manager. Tento článek také obsahuje pokyny k odstraňování potíží pro systémy Linux.


Existují dvě rozšíření vlastních skriptů Linux:
* Verze 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Verze 2 - Microsoft.Azure.Extensions.CustomScript

Nové a stávající nasazení na použití nové verze, přepněte ([Microsoft.Azure.Extensions.CustomScript](https://docs.microsoft.com/en-us/azure/virtual-machines/extensions/custom-script-linux)) místo. Nové verze je určena jako náhrada drop-in. Proto migrace je stejně snadná jako změnit název a verze, není potřeba změnit konfiguraci rozšíření.

 

### <a name="operating-system"></a>Operační systém
Podporované distribuce systému Linux:

- CentOS verze 6.5 a vyšší
- Debian 8 a vyšší
    - Debian 8.7 nedodává s Python2 v nejnovější Image, která dělí CustomScriptForLinux.
- FreeBSD
- OpenSUSE 13,1 a vyšší
- Oracle Linux 6.4 a vyšší
- SUSE Linux Enterprise Server 11 SP3 a vyšší
- Ubuntu 12.04 a vyšší

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

| Název | Hodnota nebo příklad | Typ dat | 
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| Vydavatele | Microsoft.OSTCExtensions | řetězec |
| type | CustomScriptForLinux | řetězec |
| typeHandlerVersion | 1,5 | celá čísla |
| fileUris (např.) | https://github.com/MyProject/Archive/MyPythonScript.py | pole |
| commandToExecute (např.) | Python MyPythonScript.py < Moje param1 > | řetězec |
| enableInternalDNSCheck | true (pravda) | Boolean |
| storageAccountName (např.) | examplestorageacct | řetězec |
| storageAccountKey (např.) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | řetězec |

### <a name="property-value-details"></a>Podrobnosti o hodnotě vlastnosti
* `fileUris`: (volitelné, pole řetězců) uri seznam skriptů
* `enableInternalDNSCheck`: (volitelné, bool) výchozí hodnota je True, nastavena na hodnotu False, chcete-li zakázat kontrolu DNS.
* `commandToExecute`: (volitelné, string) k provedení skriptu vstupního bodu
* `storageAccountName`: (volitelné, string) název účtu úložiště
* `storageAccountKey`: (volitelné, string) přístupový klíč účtu úložiště

Následující hodnoty lze nastavit v nastavení veřejného nebo chráněné, nesmí mít tyto hodnoty níže sadu nastavení veřejných a chráněné.
* `commandToExecute`

Pomocí nastavení veřejných může být užitečné pro ladění, ale důrazně doporučujeme použít chráněné nastavení.

Nastavení veřejných jsou odesílány ve formátu prostého textu k virtuálnímu počítači, kde bude skript proveden.  Chráněné nastavení jsou šifrované pomocí klíče zná pouze Azure a virtuální počítač. Nastavení se ukládají do virtuálního počítače, protože byly odeslány, tj. Pokud byly šifrované nastavení jsou uloženy šifrované ve virtuálním počítači. Certifikát používaný k dešifrování šifrovaných hodnot je uložená ve virtuálním počítači a použité k dešifrování nastavení (v případě potřeby) za běhu.


## <a name="template-deployment"></a>Nasazení šablon
Rozšíření virtuálního počítače Azure se dá nasadit pomocí šablon Azure Resource Manager. Schéma JSON, které jsou popsané v předchozí části lze použít v šablonu Azure Resource Manager ke spuštění rozšíření vlastních skriptů při nasazení šablony Azure Resource Manager. 


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
>Názvy těchto vlastností jsou malá a velká písmena. Abyste zabránili problémům nasazení, použijte názvy, jak je vidět tady.

## <a name="azure-cli"></a>Azure CLI
Pokud používáte Azure CLI ke spuštění rozšíření vlastních skriptů, vytvořte konfigurační soubor nebo soubory. Minimálně musí mít 'commandToExecute'.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Volitelně můžete zadat nastavení v příkazu jako řetězec formátu JSON. To umožňuje konfiguraci zadat během provádění a bez jiný konfigurační soubor.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Příklady Azure CLI

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
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
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
az vm extension set 
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Řešení potíží
Při spuštění rozšíření vlastních skriptů, skript se vytvoří nebo stáhli do adresáře, který je podobný v následujícím příkladu. Výstup příkazu je také uložen do tohoto adresáře `stdout` a `stderr` soubory. 

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Řešení potíží s, nejprve zkontrolujte protokol agenta systému Linux, zajišťovat provádění rozšíření spustili, zkontrolujte:

```bash
/var/log/waagent.log 
```

Je vhodné vyhledat rozšíření spuštění, bude vypadat nějak takto:
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
Některé body Všimněte si:
1. Povolit je při spuštění příkazu.
2. Stažení má vztah ke stahování balíčku rozšíření CustomScript z Azure, ne soubory skriptu zadaný v fileUris.
3. Zobrazí se také který soubor protokolu zapisuje do '/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log'.

Dalším krokem je chcete přejít zkontrolujte soubor protokolu, to je formát:
```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Je vhodné vyhledat induvidual provádění, bude vypadat nějak takto:
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
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Další postup
Kód, aktuální problémy a verze, najdete v sekci [úložišti rozšíření CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).

