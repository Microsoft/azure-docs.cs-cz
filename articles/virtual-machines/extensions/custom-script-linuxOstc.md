---
title: Spustit rozšíření vlastních skriptů verze 1 (zastaralé) na virtuálních počítačích se systémem Linux v Azure
description: Automatizace úloh konfigurace virtuálních počítačů se systémem Linux pomocí rozšíření vlastních skriptů v1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: afce76c8f5330f7d73ff58cc9b9307af2b8832f4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962123"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Použití rozšíření vlastních skriptů Azure verze 1 s virtuálními počítači se systémem Linux

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Rozšíření vlastních skriptů verze 1 stáhne a spustí skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakoukoli jinou úlohu konfigurace nebo správy. Můžete stáhnout skripty z Azure Storage nebo jiného přístupného internetového umístění, nebo je můžete poskytnout modulu runtime rozšíření.

Rozšíření vlastních skriptů se integruje s Azure Resource Manager šablonami. Můžete ho také spustit pomocí rozhraní příkazového řádku Azure CLI, PowerShellu, Azure Portal nebo Azure Virtual Machines REST API.

Tento článek podrobně popisuje, jak použít rozšíření vlastních skriptů z Azure CLI a jak spustit rozšíření pomocí šablony Azure Resource Manager. Tento článek také popisuje postup řešení potíží pro systémy Linux.

Existují dvě rozšíření vlastních skriptů pro Linux:

* Verze 1 – Microsoft. OSTCExtensions. CustomScriptForLinux

* Verze 2 – Microsoft. Azure. Extensions. CustomScript

Místo toho prosím přepněte nová a existující nasazení na použití nové verze ([Microsoft. Azure. Extensions. CustomScript](custom-script-linux.md)). Nová verze představuje funkční náhradu starší verze. Proto při migraci není potřeba měnit konfiguraci rozšíření, ale stačí změnit jeho název a verzi.

### <a name="operating-system"></a>Operační systém

Podporované distribuce systému Linux:

* CentOS 6,5 a vyšší
* Debian 8 a vyšší
  * Debian 8,7 se nedodává s Python2 v posledních obrázcích, což přerušuje CustomScriptForLinux.
* FreeBSD
* OpenSUSE 13,1 a vyšší
* Oracle Linux 6,4 a vyšší
* SUSE Linux Enterprise Server 11 SP3 a vyšší
* Ubuntu 12,04 a vyšší

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
* Pokud máte skript, který způsobí restartování, nainstalujte aplikace a spusťte skripty atd. Restartování byste měli naplánovat pomocí úlohy cron nebo pomocí nástrojů, jako je DSC nebo Puppet rozšíření.
* Rozšíření spustí pouze jeden skript, pokud chcete spustit skript při každém spuštění, můžete použít [bitovou kopii Cloud-init](../linux/using-cloud-init.md)  a použít [skripty na spouštěcí](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) modul. Případně můžete použít skript k vytvoření jednotky systémové služby.
* Pokud chcete naplánovat, kdy se skript spustí, měli byste použít rozšíření k vytvoření úlohy cron.
* Když je skript spuštěný, na webu Azure Portal nebo v rozhraní příkazového řádku se rozšíření zobrazí pouze v přechodném stavu. Pokud chcete častěji aktualizovat stav spuštěného skriptu, budete muset vytvořit vlastní řešení.
* Rozšíření vlastních skriptů nepodporují nativně proxy servery, ale můžete použít nástroj pro přenos souborů, který podporuje proxy servery ve vašem skriptu, jako je například *kudrlinkou*.
* Mějte na paměti, že nevýchozí umístění adresářů, na kterých se můžou skripty nebo příkazy spoléhat, mají logiku k tomuto zpracování.

## <a name="extension-schema"></a>Schéma rozšíření

Konfigurace rozšíření vlastních skriptů určuje například umístění skriptu a příkaz, který má být spuštěn. Tuto konfiguraci můžete uložit do konfiguračních souborů, zadat ji na příkazovém řádku nebo ji zadat v šabloně Azure Resource Manager. 

Citlivá data můžete ukládat do chráněné konfigurace, která je zašifrovaná a v rámci virtuálního počítače se šifruje jenom. Chráněná konfigurace je užitečná, když příkaz pro spuštění zahrnuje tajné klíče, jako je třeba heslo.

Tyto položky by měly být považovány za citlivá data a specifikována v konfiguraci nastavení chráněného rozšíření. Data nastavení chráněná rozšířením virtuálního počítače Azure jsou šifrovaná a v cílovém virtuálním počítači se dešifrují jenom.

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

| Name | Hodnota/příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | datum |
| vydavatel | Microsoft. OSTCExtensions | řetězec |
| typ | CustomScriptForLinux | řetězec |
| typeHandlerVersion | 1.5 | int |
| Identifikátory URI (např.) | `https://github.com/MyProject/Archive/MyPythonScript.py` | array |
| commandToExecute (např.) | Python MyPythonScript.py \<my-param1\> | řetězec |
| enableInternalDNSCheck | true | boolean |
| storageAccountName (např.) | examplestorageacct | řetězec |
| storageAccountKey (např.) | TmJK/1N3AbAZ3q/+ hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg = = | řetězec |

### <a name="property-value-details"></a>Podrobnosti hodnoty vlastnosti

* `fileUris`: (volitelné, pole řetězců) seznam identifikátorů URI skriptů
* `enableInternalDNSCheck`: (volitelné, bool) výchozí hodnota je true, nastavením na hodnotu false zakážete kontrolu DNS.
* `commandToExecute`: (volitelné, String) skript vstupního bodu, který se má spustit
* `storageAccountName`: (volitelné, String) název účtu úložiště
* `storageAccountKey`: (volitelné, String) přístupový klíč účtu úložiště

V nastavení veřejné nebo chráněné nastavení lze nastavit následující hodnoty, ale v nastavení veřejné a chráněné nastavení nesmí být tyto hodnoty nastaveny níže.

* `commandToExecute`

Použití veřejného nastavení může být užitečné pro ladění, ale důrazně doporučujeme použít chráněná nastavení.

Veřejné nastavení se odesílá ve formě prostého textu do virtuálního počítače, ve kterém se skript spustí.  Chráněná nastavení se šifrují pomocí klíče, který je známý jenom pro Azure a virtuální počítač. Nastavení se uloží do virtuálního počítače, protože se poslala, tj. Pokud byla nastavení zašifrovaná, uloží se na virtuálním počítači jako šifrované. Certifikát použitý k dešifrování šifrovaných hodnot je uložený ve virtuálním počítači a slouží k dešifrování nastavení (v případě potřeby) za běhu.

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure je možné nasadit pomocí šablon Azure Resource Manager. Schéma JSON popsané v předchozí části lze použít v šabloně Azure Resource Manager ke spuštění rozšíření vlastních skriptů během nasazování Azure Resource Manager šablony.

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
>U těchto názvů vlastností se rozlišují velká a malá písmena. Aby nedocházelo k potížím s nasazením, použijte názvy, jak je znázorněno zde.

## <a name="azure-cli"></a>Azure CLI

Pokud ke spuštění rozšíření vlastních skriptů používáte Azure CLI, vytvořte konfigurační soubor nebo soubory. Minimálně musíte mít ' commandToExecute '.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Volitelně můžete zadat nastavení v příkazu jako řetězec ve formátu JSON. To umožňuje zadání konfigurace během provádění a bez samostatného konfiguračního souboru.

```azurecli
az vm extension set \
  --resource-group exttest \
  --vm-name exttest \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
```

### <a name="azure-cli-examples"></a>Příklady Azure CLI

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
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
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
az vm extension set
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json \
  --protected-settings ./protected-config.json
```

## <a name="troubleshooting"></a>Řešení potíží

Když se rozšíření vlastních skriptů spustí, skript se vytvoří nebo stáhne do adresáře, který je podobný následujícímu příkladu. Výstup příkazu je také uložen do tohoto adresáře v `stdout` souborech a `stderr` .

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Pokud chcete řešit potíže, nejdřív zkontrolujte protokol agenta pro Linux, zkontrolujte, jestli je rozšíření spuštěné, zkontrolujte:

```bash
/var/log/waagent.log
```

Měli byste Hledat spuštění rozšíření, bude vypadat přibližně takto:

```output
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

Poznámka:

1. Povolit je při spuštění příkazu.
1. Stažení se týká stahování balíčku rozšíření CustomScript z Azure, nikoli souborů skriptu zadaných v identifikátorech URI.
1. Můžete si také prohlédnout, na který soubor protokolu se zapisuje. `/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

Dalším krokem je Projděte si soubor protokolu, který má následující formát:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Měli byste hledat konkrétní spuštění, bude vypadat přibližně takto:

```output
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

Tady můžete vidět:

* Tento protokol se spouští příkazem Enable.
* Nastavení předaná rozšíření
* Rozšíření stahuje soubor a jeho výsledek.
* Příkaz, který se spouští, a výsledek.

Stav provádění rozšíření vlastních skriptů můžete také načíst pomocí Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Výstup bude vypadat jako následující text:

```output
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Další kroky

Chcete-li zobrazit kód, aktuální problémy a verze, přečtěte si téma [úložiště rozšíření CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).