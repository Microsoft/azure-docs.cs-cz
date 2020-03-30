---
title: Spouštění vlastních skriptů na virtuálních počítačích SIO v Azure
description: Automatizace úloh konfigurace virtuálních počítačů s Linuxem pomocí rozšíření Vlastní skript v1
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: danis
ms.openlocfilehash: a3eae08510e57227b91deeeb7a7a608a6652cb4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535404"
---
# <a name="use-the-azure-custom-script-extension-version-1-with-linux-virtual-machines"></a>Použití rozšíření azure custom script verze 1 s virtuálními počítači s Linuxem

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]

Rozšíření vlastního skriptu verze 1 stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakoukoli jinou úlohu konfigurace/správy. Skripty si můžete stáhnout z Azure Storage nebo z jiného dostupného internetového umístění, nebo je můžete poskytnout do runtime rozšíření.

Rozšíření vlastní skript integruje se šablonami Azure Resource Manager. Můžete ji spustit také pomocí Azure CLI, PowerShell, portál Azure nebo rozhraní REST virtuálních počítačů Azure.

Tento článek podrobně popisuje, jak používat rozšíření vlastního skriptu z Azure CLI a jak spustit rozšíření pomocí šablony Azure Resource Manager. Tento článek také obsahuje kroky řešení potíží pro systémy Linux.

Existují dvě rozšíření vlastního skriptu pro Linux:

* Verze 1 - Microsoft.OSTCExtensions.CustomScriptForLinux

* Verze 2 - Microsoft.Azure.Extensions.CustomScript

Přepněte nové a stávající nasazení a použijte místo toho novou verzi ([Microsoft.Azure.Extensions.CustomScript).](custom-script-linux.md) Nová verze představuje funkční náhradu starší verze. Proto při migraci není potřeba měnit konfiguraci rozšíření, ale stačí změnit jeho název a verzi.

### <a name="operating-system"></a>Operační systém

Podporované distribuce Linuxu:

* CentOS 6,5 a vyšší
* Debian 8 a vyšší
  * Debian 8.7 nedoručuje python2 v nejnovějších obrázcích, což porušuje CustomScriptForLinux.
* FreeBSD
* OpenSUSE 13.1 a vyšší
* Oracle Linux 6.4 a vyšší
* SUSE Linux Enterprise Server 11 SP3 a vyšší
* Ubuntu 12.04 a vyšší

### <a name="script-location"></a>Umístění skriptu

Toto rozšíření můžete použít k použití přihlašovacích údajů úložiště objektů Blob Azure pro přístup k úložišti objektů Blob Azure. Případně umístění skriptu může být libovolné místo, tak dlouho, dokud virtuální ho virtuálního serveru můžete směrovat do tohoto koncového bodu, jako je například GitHub, interní souborový server atd.

### <a name="internet-connectivity"></a>Připojení k Internetu

Pokud potřebujete stáhnout skript externě, jako je GitHub nebo Azure Storage, pak je třeba otevřít další porty firewallu/skupiny zabezpečení sítě. Pokud je například váš skript umístěný ve službě Azure Storage, můžete povolit přístup pomocí značek služby Azure NSG pro [úložiště](../../virtual-network/security-overview.md#service-tags).

Pokud je skript na místním serveru, může být stále nutné otevřít další porty brány firewall/skupiny zabezpečení sítě.

### <a name="tips-and-tricks"></a>Tipy a triky

* Selhání tohoto rozšíření nejčastěji způsobují chyby syntaxe ve skriptu. Otestujte, že se skript spustí bez chyb, a implementujte do skriptu dodatečné protokolování, které vám pomůže snadněji zjistit, kde došlo k selhání.
* Pište skripty, které jsou idempotentní, takže když se omylem spustí více než jednou, nezpůsobí to změny systému.
* Ujistěte se, že skripty nevyžadují vstup uživatele při jejich spuštění.
* Je povoleno 90 minut pro spuštění skriptu, cokoli v delším případě bude mít za následek neúspěšné poskytnutí rozšíření.
* Nevkládáme restarty uvnitř skriptu, to způsobí problémy s jinými rozšířeními, které jsou instalovány, a po restartu, rozšíření nebude pokračovat po restartu. 
* Pokud máte skript, který způsobí restart, pak nainstalovat aplikace a spouštět skripty atd. Restart byste měli naplánovat pomocí úlohy Cron nebo pomocí nástrojů, jako je DSC nebo Chef, Puppet extensions.
* Rozšíření spustí skript pouze jednou, pokud chcete spustit skript při každém spuštění, pak můžete použít [image cloud-init](../linux/using-cloud-init.md) a použít modul [Skripty na startu.](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) Případně můžete použít skript k vytvoření servisní jednotky Systemd.
* Pokud chcete naplánovat, kdy bude skript spuštěn, měli byste použít rozšíření k vytvoření úlohy Cron.
* Když je skript spuštěný, na webu Azure Portal nebo v rozhraní příkazového řádku se rozšíření zobrazí pouze v přechodném stavu. Pokud chcete častější aktualizace stavu spuštěného skriptu, budete muset vytvořit vlastní řešení.
* Vlastní skript rozšíření nepodporuje nativně proxy servery, ale můžete použít nástroj pro přenos souborů, který podporuje proxy servery v rámci skriptu, jako je *například Curl*.
* Uvědomte si, že umístění adresáře, na která se mohou spolehnout skripty nebo příkazy, mají logiku, která to zvládnou.

## <a name="extension-schema"></a>Schéma rozšíření

Konfigurace rozšíření vlastního skriptu určuje věci, jako je umístění skriptu a příkaz, který má být spuštěn. Tuto konfiguraci můžete uložit do konfiguračních souborů, zadat ji na příkazovém řádku nebo ji zadat v šabloně Správce prostředků Azure. 

Citlivá data můžete ukládat v chráněné konfiguraci, která je šifrovaná a dešifrovaná pouze uvnitř virtuálního počítače. Chráněná konfigurace je užitečná, pokud příkaz spuštění obsahuje tajné klíče, například heslo.

Tyto položky by měly být považovány za citlivá data a zadali v konfiguraci nastavení chráněné rozšíření. Data nastavení chráněné rozšířením virtuálního počítače Azure jsou šifrovaná a dešifrovaná jenom na cílovém virtuálním počítači.

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

| Name (Název) | Hodnota / Příklad | Typ dat |
| ---- | ---- | ---- |
| apiVersion | 2015-06-15 | date |
| vydavatel | Rozšíření Microsoft.OSTCExtensions | řetězec |
| type | Vlastní ScriptForLinux | řetězec |
| typeHandlerVersion | 1,5 | int |
| fileUris (např. | https://github.com/MyProject/Archive/MyPythonScript.py | pole |
| commandToExecute (např. | python \<MyPythonScript.py my-param1\> | řetězec |
| enableInternalDNSCheck | true | Boolean |
| storageAccountName (např. | examplestorageacct | řetězec |
| storageAccountKey (např. | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | řetězec |

### <a name="property-value-details"></a>Podrobnosti o hodnotě nemovitosti

* `fileUris`: (volitelné, pole řetězců) seznam uri skriptů
* `enableInternalDNSCheck`: (volitelné, bool) výchozí je True, nastavena na False zakázat kontrolu DNS.
* `commandToExecute`: (nepovinné, řetězec) vstupní skript ke spuštění
* `storageAccountName`: (nepovinné, řetězec) název účtu úložiště
* `storageAccountKey`: (nepovinné, řetězec) přístupový klíč účtu úložiště

Následující hodnoty lze nastavit ve veřejném nebo chráněném nastavení, tyto hodnoty nesmí být nastaveny níže ve veřejném i chráněném nastavení.

* `commandToExecute`

Použití veřejné nastavení může být užitečné pro ladění, ale důrazně doporučujeme použít chráněné nastavení.

Veřejná nastavení se odesílají ve prostém textu do virtuálního počítače, kde bude skript spuštěn.  Chráněná nastavení se šifrují pomocí klíče známého jenom pro Azure a virtuální počítač. Nastavení se uloží do virtuálního počítače při jejich odeslání, tj. Certifikát použitý k dešifrování šifrovaných hodnot je uložen na virtuálním počítači a používá se k dešifrování nastavení (v případě potřeby) za běhu.

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálních počítačů Azure se můžou nasadit pomocí šablon Azure Resource Manageru. Schéma JSON popsané v předchozí části lze použít v šabloně Správce prostředků Azure ke spuštění rozšíření vlastního skriptu během nasazení šablony Azure Resource Manager.

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
>Tyto názvy vlastností rozlišují malá a velká písmena. Chcete-li se vyhnout problémům s nasazením, použijte názvy, jak je znázorněno zde.

## <a name="azure-cli"></a>Azure CLI

Když používáte Azure CLI ke spuštění rozšíření vlastního skriptu, vytvořte konfigurační soubor nebo soubory. Minimálně musíte mít 'commandToExecute'.

```azurecli
az vm extension set -n VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.5 \
  --vm-name MyVm --resource-group MyResourceGroup \
  --protected-settings '{"commandToExecute": "echo hello"}'
```

Volitelně můžete zadat nastavení v příkazu jako řetězec ve formátu JSON. To umožňuje konfiguraci zadat během provádění a bez samostatného konfiguračního souboru.

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

Příkaz Příkaz příkazu KONS Azure:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name CustomScriptForLinux \
  --publisher Microsoft.OSTCExtensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Veřejné a chráněné konfigurační soubory

K určení identifikátoru URI souboru skriptu se používá veřejný konfigurační soubor. K určení příkazu, který má být spuštěn, se používá chráněný konfigurační soubor.

Veřejný konfigurační soubor:

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

Příkaz Příkaz příkazu KONS Azure:

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

Při spuštění rozšíření vlastní skript je skript vytvořen nebo stažen do adresáře, který je podobný následujícímu příkladu. Výstup příkazu je také uložen `stdout` `stderr` do tohoto adresáře a souborů.

```bash
/var/lib/waagent/Microsoft.OSTCExtensions.CustomScriptForLinux-<version>/download/1
```

Chcete-li vyřešit potíže, nejprve zkontrolujte protokol agenta Linuxu, zkontrolujte, zda je rozšíření spuštěno, zkontrolujte:

```bash
/var/log/waagent.log
```

Měli byste se podívat na spuštění rozšíření, bude to vypadat jako:

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

Některé body na vědomí:

1. Povolit je, když se příkaz spustí.
1. Stahování se týká stahování balíčku rozšíření CustomScript z Azure, nikoli souborů skriptů zadaných v fileUris.
1. Můžete také zjistit, který soubor protokolu je zápis`/var/log/azure/Microsoft.OSTCExtensions.CustomScriptForLinux/1.5.2.2/extension.log`

Dalším krokem je jít zkontrolovat soubor protokolu, to je formát:

```bash
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Měli byste se podívat na individuální provedení, bude to vypadat jako:

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

Zde si můžete prohlédnout:

* Spuštění příkazu Povolit je tento protokol
* Nastavení předaná rozšíření
* Rozšíření stahování souboru a výsledek, který.
* Příkaz je spuštěn a výsledek.

Můžete také načíst stav spuštění rozšíření vlastního skriptu pomocí Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

Výstup vypadá takto:

```output
Name                  ProvisioningState    Publisher                   Version  AutoUpgradeMinorVersion
--------------------  -------------------  ------------------------  ---------  -------------------------
CustomScriptForLinux  Succeeded            Microsoft.OSTCExtensions        1.5  True
```

## <a name="next-steps"></a>Další kroky

Kód, aktuální problémy a verze najdete v [tématu Úložiště rozšíření jazyka CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript).
