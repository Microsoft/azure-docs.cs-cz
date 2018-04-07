---
title: Spustit vlastní skripty na virtuální počítače s Linuxem v Azure | Microsoft Docs
description: Automatizovat úkoly konfigurace virtuálního počítače s Linuxem pomocí rozšíření vlastních skriptů
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
ms.date: 04/26/2017
ms.author: danis
ms.openlocfilehash: 88f337df14b7e2647a76cce7ef91ec6f8950bbc6
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="use-the-azure-custom-script-extension-with-linux-virtual-machines"></a>Virtuální počítače s Linuxem pomocí rozšíření vlastních skriptů Azure
Rozšíření vlastních skriptů stáhne a spustí skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalace softwaru nebo jiná úloha správy nebo konfigurace. Skripty si můžete stáhnout z Azure Storage nebo jiné dostupné umístění v Internetu, nebo je můžete zadat rozšíření modulu runtime. 

Rozšíření vlastních skriptů se integruje s šablon Azure Resource Manageru. Můžete také spustit jej pomocí rozhraní příkazového řádku Azure, PowerShell, portálu Azure nebo REST API pro virtuální počítače Azure.

Tento článek podrobně popisuje, jak používat rozšíření vlastních skriptů z příkazového řádku Azure a jak spustit rozšíření pomocí šablony Azure Resource Manager. Tento článek také obsahuje pokyny k odstraňování potíží pro systémy Linux.

## <a name="extension-configuration"></a>Konfigurace rozšíření
Konfigurace rozšíření vlastních skriptů určuje takové věci, jako umístění skriptu a příkaz ke spuštění. Můžete tuto konfiguraci uložit konfigurační soubory, zadejte na příkazovém řádku nebo zadejte jej v šablonu Azure Resource Manager. 

V konfiguraci chráněného, které se budou šifrovat a dešifrovat jenom ve virtuálním počítači můžete ukládat citlivá data. Chráněné konfigurace je užitečné při provádění příkazu zahrnuje tajné klíče, jako například heslo.

### <a name="public-configuration"></a>Veřejná konfigurace
Schéma veřejné konfigurace je následující.

>[!NOTE]
>Názvy těchto vlastností jsou malá a velká písmena. Abyste zabránili problémům nasazení, použijte názvy, jak je vidět tady.

* **commandToExecute** (vyžaduje se, řetězce): spuštění skriptu vstupní bod.
* **fileUris** (volitelná pole řetězců): adresách URL soubory ke stažení.
* **časové razítko** (volitelné, celé číslo): časové razítko skriptu. Změňte hodnotu tohoto pole pouze v případě, že se má spustit spusťte skript.

```json
{
  "fileUris": ["<url>"],
  "commandToExecute": "<command-to-execute>"
}
```

### <a name="protected-configuration"></a>Chráněné konfigurace
Schéma pro chráněné konfigurace je následující.

>[!NOTE]
>Názvy těchto vlastností jsou malá a velká písmena. Abyste zabránili problémům nasazení, použijte názvy, jak je vidět tady.

* **commandToExecute** (volitelný, řetězec): spuštění skriptu vstupní bod. Toto pole použijte, pokud váš příkaz obsahuje tajné klíče, jako jsou hesla.
* **storageAccountName** (volitelný, řetězec): název účtu úložiště. Pokud chcete zadat pověření pro úložiště, všechny soubor identifikátory URI musí být adresy URL pro objekty BLOB Azure.
* **storageAccountKey** (volitelný, řetězec): přístupový klíč účtu úložiště.

```json
{
  "commandToExecute": "<command-to-execute>",
  "storageAccountName": "<storage-account-name>",
  "storageAccountKey": "<storage-account-key>"
}
```

## <a name="azure-cli"></a>Azure CLI
Pokud používáte Azure CLI ke spuštění rozšíření vlastních skriptů, vytvořte konfigurační soubor nebo soubory. Minimálně konfigurační soubory obsahují soubor URI a spuštění příkazu skriptu.

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

Volitelně můžete zadat nastavení v příkazu jako řetězec formátu JSON. To umožňuje konfiguraci zadat během provádění a bez jiný konfigurační soubor.

```azurecli
az vm extension set '
  --resource-group exttest `
  --vm-name exttest `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"],"commandToExecute": "./config-music.sh"}'
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
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-configuration-with-no-script-file"></a>Veřejná konfigurace s žádný soubor skriptu

```json
{
  "commandToExecute": "apt-get -y update && apt-get install -y apache2"
}
```

Azure CLI příkaz:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json
```

#### <a name="public-and-protected-configuration-files"></a>Veřejné a chráněné konfigurační soubory

Použít soubor veřejné konfigurace zadat soubor skriptu identifikátor URI. Použijete chráněné konfiguračního souboru k zadání příkazu ke spuštění.

Veřejné konfigurační soubor:

```json
{
  "fileUris": ["https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"]
}
```

Chráněné konfigurační soubor:  

```json
{
  "commandToExecute": "./hello.sh <password>"
}
```

Azure CLI příkaz:

```azurecli
az vm extension set --resource-group myResourceGroup --vm-name myVM --name customScript --publisher Microsoft.Azure.Extensions --settings ./script-config.json --protected-settings ./protected-config.json
```

## <a name="resource-manager-template"></a>Šablona Resource Manageru
Rozšíření vlastních skriptů Azure můžete spustit v době nasazení virtuálního počítače pomocí šablony Resource Manageru. Uděláte to tak, přidejte do šablony nasazení správně formátovaný JSON.

### <a name="resource-manager-examples"></a>Příklady Resource Manager

#### <a name="public-configuration"></a>Veřejná konfigurace

```json
{
    "name": "scriptextensiondemo",
    "type": "extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-15",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', parameters('scriptextensiondemoName'))]"
    ],
    "tags": {
        "displayName": "scriptextensiondemo"
    },
    "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
      "settings": {
        "fileUris": [
          "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
        ],
        "commandToExecute": "sh hello.sh"
      }
    }
}
```

#### <a name="execution-command-in-protected-configuration"></a>Spuštění příkazu v chráněné konfigurace

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
      "fileUris": [
        "https://gist.github.com/ahmetalpbalkan/b5d4a856fe15464015ae87d5587a4439/raw/466f5c30507c990a4d5a2f5c79f901fa89a80841/hello.sh"
      ]              
    },
    "protectedSettings": {
      "commandToExecute": "sh hello.sh <password>"
    }
  }
}
```

Úplný příklad, najdete v článku [.NET Hudba úložiště ukázkové](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

## <a name="troubleshooting"></a>Řešení potíží
Při spuštění rozšíření vlastních skriptů, skript se vytvoří nebo stáhli do adresáře, který je podobný v následujícím příkladu. Výstup příkazu je také uložen do tohoto adresáře `stdout` a `stderr` soubory.

```bash
/var/lib/waagent/custom-script/download/0/
```

Přípona skriptu Azure vytvoří protokol, který najdete tady:

```bash
/var/log/azure/custom-script/handler.log
```

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
Informace o skriptu rozšíření jiných virtuálních počítačů, v tématu [přehled rozšíření Azure skriptů pro Linux](extensions-features.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

