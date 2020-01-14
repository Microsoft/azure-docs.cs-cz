---
title: Run custom scripts on Linux VMs in Azure
description: Automate Linux VM configuration tasks by using the Custom Script Extension v2
services: virtual-machines-linux
documentationcenter: ''
author: MicahMcKittrick-MSFT
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
ms.openlocfilehash: da7ade4b4724f8d155deb1c109587a311d03375c
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75931020"
---
# <a name="use-the-azure-custom-script-extension-version-2-with-linux-virtual-machines"></a>Use the Azure Custom Script Extension Version 2 with Linux virtual machines
The Custom Script Extension Version 2 downloads and runs scripts on Azure virtual machines. This extension is useful for post-deployment configuration, software installation, or any other configuration/management task. You can download scripts from Azure Storage or another accessible internet location, or you can provide them to the extension runtime. 

The Custom Script Extension integrates with Azure Resource Manager templates. You can also run it by using Azure CLI, PowerShell, or the Azure Virtual Machines REST API.

This article details how to use the Custom Script Extension from Azure CLI, and how to run the extension by using an Azure Resource Manager template. This article also provides troubleshooting steps for Linux systems.


There are two Linux Custom Script Extensions:
* Version 1 - Microsoft.OSTCExtensions.CustomScriptForLinux
* Version 2 - Microsoft.Azure.Extensions.CustomScript

Please switch new and existing deployments to use the new version 2 instead. Nová verze představuje funkční náhradu starší verze. Proto při migraci není potřeba měnit konfiguraci rozšíření, ale stačí změnit jeho název a verzi.


### <a name="operating-system"></a>Operační systém

The Custom Script Extension for Linux will run on the extension supported extension OS's, for more information, see this [article](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).

### <a name="script-location"></a>Script Location

You can use the extension to use your Azure Blob storage credentials, to access Azure Blob storage. Alternatively, the script location can be any where, as long as the VM can route to that end point, such as GitHub, internal file server etc.

### <a name="internet-connectivity"></a>Internet Connectivity
If you need to download a script externally such as GitHub or Azure Storage, then additional firewall/Network Security Group ports need to be opened. For example if your script is located in Azure Storage, you can allow access using Azure NSG Service Tags for [Storage](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

If your script is on a local server, then you may still need additional firewall/Network Security Group ports need to be opened.

### <a name="tips-and-tricks"></a>Tipy a triky
* Selhání tohoto rozšíření nejčastěji způsobují chyby syntaxe ve skriptu. Otestujte, že se skript spustí bez chyb, a implementujte do skriptu dodatečné protokolování, které vám pomůže snadněji zjistit, kde došlo k selhání.
* Pište skripty, které jsou idempotentní, takže když se omylem spustí více než jednou, nezpůsobí to změny systému.
* Ensure the scripts do not require user input when they run.
* There is 90 mins allowed for the script to run, anything longer will result in a failed provision of the extension.
* Do not put reboots inside the script, this will cause issues with other extensions that are being installed, and post reboot, the extension will not continue after the restart. 
* If you have a script that will cause a reboot, then install applications and run scripts etc. You should schedule the reboot using a Cron job, or using tools such as DSC, or Chef, Puppet extensions.
* The extension will only run a script once, if you want to run a script on every boot, then you can use [cloud-init image](https://docs.microsoft.com/azure/virtual-machines/linux/using-cloud-init)  and use a [Scripts Per Boot](https://cloudinit.readthedocs.io/en/latest/topics/modules.html#scripts-per-boot) module. Alternatively, you can use the script to create a Systemd service unit.
* If you want to schedule when a script will run, you should use the extension to create a Cron job. 
* Když je skript spuštěný, na webu Azure Portal nebo v rozhraní příkazového řádku se rozšíření zobrazí pouze v přechodném stavu. If you want more frequent status updates of a running script, you will need to create your own solution.
* Custom Script extension does not natively support proxy servers, however you can use a file transfer tool that supports proxy servers within your script, such as *Curl*. 
* Be aware of non default directory locations that your scripts or commands may rely on, have logic to handle this.
*  When deploying custom script to production VMSS instances it is suggested to deploy via json template and store your script storage account where you have control over the SAS token. 


## <a name="extension-schema"></a>Schéma rozšíření

The Custom Script Extension configuration specifies things like script location and the command to be run. You can store this configuration in configuration files, specify it on the command line, or specify it in an Azure Resource Manager template. 

You can store sensitive data in a protected configuration, which is encrypted and only decrypted inside the virtual machine. The protected configuration is useful when the execution command includes secrets such as a password.

These items should be treated as sensitive data and specified in the extensions protected setting configuration. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaný a dešifrovat jenom na cílovém virtuálním počítači.

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
    "typeHandlerVersion": "2.0",
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
       "fileUris": ["https://.."]  
    }
  }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Name (Název) | Hodnota / příklad | Typ dat | 
| ---- | ---- | ---- |
| apiVersion | 2019-03-01 | date |
| publisher | Microsoft.Compute.Extensions | string |
| type | CustomScript | string |
| typeHandlerVersion | 2.0 | int |
| fileUris (např.) | https://github.com/MyProject/Archive/MyPythonScript.py | pole |
| commandToExecute (e.g) | python MyPythonScript.py \<my-param1> | string |
| . | IyEvYmluL3NoCmVjaG8gIlVwZGF0aW5nIHBhY2thZ2VzIC4uLiIKYXB0IHVwZGF0ZQphcHQgdXBncmFkZSAteQo= | string |
| skipDos2Unix  (e.g) | false | Boolean |
| timestamp (např.) | 123456789 | 32-bit integer |
| storageAccountName (e.g) | examplestorageacct | string |
| storageAccountKey (e.g) | TmJK/1N3AbAZ3q/+hOXoi/l73zOqsaxXDhqa9Y83/v5UpXQp2DQIBuv2Tifp60cE/OaHsJZmQZ7teQfczQj8hg== | string |

### <a name="property-value-details"></a>Property value details
* `apiVersion`: The most up to date apiVersion can be found using [Resource Explorer](https://resources.azure.com/) or from Azure CLI using the following command `az provider list -o json`
* `skipDos2Unix`: (optional, boolean) skip dos2unix conversion of script-based file URLs or script.
* `timestamp` (optional, 32-bit integer) use this field only to trigger a re-run of the script by changing value of this field.  Je přijatelné libovolné celočíselné hodnoty; musí se lišit jenom od předchozí hodnoty.
  * `commandToExecute`: (**požadováno** , pokud se skript nenastavuje, řetězec) skript vstupního bodu, který se má spustit. Místo toho použijte toto pole, pokud váš příkaz obsahuje tajné klíče jako hesla.
* `script`: (**požadováno** , pokud commandToExecute není nastaven, String) skript kódovaný jako Base64 (a volitelně gzip'ed), který spustil/bin/sh.
* `fileUris`: (volitelné, pole řetězců) adresy URL pro soubory, které se mají stáhnout.
* `storageAccountName`: (nepovinný, String) název účtu úložiště. Pokud zadáte přihlašovací údaje úložiště, musí být všechny `fileUris` adresy URL pro objekty blob Azure.
* `storageAccountKey`: (nepovinný, String) přístupový klíč účtu úložiště


V rámci veřejného nebo chráněného nastavení lze nastavit následující hodnoty, rozšíření bude odmítat všechny konfigurace, kde jsou níže uvedené hodnoty nastaveny v nastavení veřejné i chráněné.
* `commandToExecute`
* `script`
* `fileUris`

Použití veřejného nastavení může být užitečné pro ladění, ale důrazně doporučujeme použít chráněná nastavení.

Veřejné nastavení se odesílá ve formě prostého textu do virtuálního počítače, ve kterém se skript spustí.  Chráněná nastavení se šifrují pomocí klíče, který je známý jenom pro Azure a virtuální počítač. Nastavení se uloží do virtuálního počítače, protože se poslala, tj. Pokud byla nastavení zašifrovaná, uloží se na virtuálním počítači jako šifrované. Certifikát použitý k dešifrování šifrovaných hodnot je uložený ve virtuálním počítači a slouží k dešifrování nastavení (v případě potřeby) za běhu.

#### <a name="property-skipdos2unix"></a>Vlastnost: skipDos2Unix

Výchozí hodnota je false, což znamená, že **je** proveden dos2unix převod.

Předchozí verze CustomScript, Microsoft. OSTCExtensions. CustomScriptForLinux, by automaticky převedla soubory systému DOS do souborů systému UNIX překládáním `\r\n` na `\n`. Tento překlad stále existuje a je ve výchozím nastavení zapnutý. Tento převod se použije na všechny soubory stažené z identifikátorů URI nebo nastavení skriptu na základě některého z následujících kritérií.

* Pokud je rozšíření jedním z `.sh`, `.txt`, `.py`nebo `.pl` bude převedeno. Nastavení skriptu bude vždy odpovídat těmto kritériím, protože se předpokládá, že se jedná o skript spouštěný pomocí/bin/sh a je uložen jako script.sh na virtuálním počítači.
* Pokud soubor začíná na `#!`.

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


## <a name="template-deployment"></a>Nasazení šablon
Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Schéma JSON popsané v předchozí části lze použít v šabloně Azure Resource Manager ke spuštění rozšíření vlastních skriptů během nasazování Azure Resource Manager šablony. Ukázkovou šablonu, která obsahuje rozšíření vlastních skriptů, najdete tady: [GitHub](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).


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

### <a name="azure-cli-examples"></a>Příklady rozhraní příkazového řádku Azure

#### <a name="public-configuration-with-script-file"></a>Public configuration with script file

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],
  "commandToExecute": "./config-music.sh"
}
```

Azure CLI command:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Public configuration with no script file

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI command:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Public and protected configuration files

You use a public configuration file to specify the script file URI. You use a protected configuration file to specify the command to be run.

Public configuration file:

```json
{
  "fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"]
}
```

Protected configuration file:  

```json
{
  "commandToExecute": "./config-music.sh <param1>"
}
```

Azure CLI command:

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
When the Custom Script Extension runs, the script is created or downloaded into a directory that's similar to the following example. The command output is also saved into this directory in `stdout` and `stderr` files.

```bash
/var/lib/waagent/custom-script/download/0/
```

To troubleshoot, first check the Linux Agent Log, ensure the extension ran, check:

```bash
/var/log/waagent.log 
```

You should look for the extension execution, it will look something like:
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
Some points to note:
1. Enable is when the command starts running.
2. Download relates to the downloading of the CustomScript extension package from Azure, not the script files specified in fileUris.


The Azure Script Extension produces a log, which you can find here:

```bash
/var/log/azure/custom-script/handler.log
```

You should look for the individual execution, it will look something like:
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
Here you can see:
* The Enable command starting is this log
* The settings passed to the extension
* The extension downloading file and the result of that.
* The command being run and the result.

You can also retrieve the execution state of the Custom Script Extension by using Azure CLI:

```azurecli
az vm extension list -g myResourceGroup --vm-name myVM
```

The output looks like the following text:

```azurecli
info:    Executing command vm extension get
+ Looking up the VM "scripttst001"
data:    Publisher                   Name                                      Version  State
data:    --------------------------  ----------------------------------------  -------  ---------
data:    Microsoft.Azure.Extensions  CustomScript                              2.0      Succeeded
data:    Microsoft.OSTCExtensions    Microsoft.Insights.VMDiagnosticsSettings  2.3      Succeeded
info:    vm extension get command OK
```

## <a name="next-steps"></a>Další kroky
To see the code, current issues and versions, see [custom-script-extension-linux repo](https://github.com/Azure/custom-script-extension-linux).

