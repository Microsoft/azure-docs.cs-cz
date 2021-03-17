---
title: Rychlý Start Azure Cloud Shell – bash
description: Naučte se používat příkazový řádek bash v prohlížeči s Azure Cloud Shell.
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 91b7c58890518559c046023bd78c9248e9840f9f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89468745"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Rychlý Start pro bash v Azure Cloud Shell

Tento dokument popisuje, jak používat Bash v Azure Cloud Shell v [Azure Portal](https://ms.portal.azure.com/).

> [!NOTE]
> K dispozici je také [PowerShell v Azure Cloud Shell](quickstart-powershell.md) rychlý Start.

## <a name="start-cloud-shell"></a>Spuštění Cloud Shellu
1. **Cloud Shell** spustit z horní navigace Azure Portal. <br>
![Snímek obrazovky znázorňující, jak spustit Azure Cloud Shell v Azure Portal](media/quickstart/shell-icon.png)

2. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště a sdílenou složku Microsoft Azure Files.
3. Vyberte vytvořit úložiště.

> [!TIP]
> Při každé relaci se automaticky ověřují pro Azure CLI.

### <a name="select-the-bash-environment"></a>Výběr prostředí bash
Ověřte, že je v rozevíracím seznamu prostředí v levé části okna prostředí uvedena ikona prostředí `Bash` . <br>
![Snímek obrazovky ukazující, jak vybrat prostředí bash pro Azure Cloud Shell.](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Nastavení předplatného
1. Seznam předplatných, ke kterým máte přístup.
   ```azurecli-interactive
   az account list
   ```

2. Nastavte preferované předplatné:

   ```azurecli-interactive
   az account set --subscription 'my-subscription-name'
   ```

> [!TIP]
> Vaše předplatné bude zapamatovatelné pro budoucí relace pomocí `/home/<user>/.azure/azureProfile.json` .

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte novou skupinu prostředků v WestUS s názvem "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Vytvoření virtuálního počítače s Linuxem
Vytvořte virtuální počítač s Ubuntu v nové skupině prostředků. Rozhraní příkazového řádku Azure vytvoří klíče SSH a nastaví pro něj virtuální počítač. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Pomocí `--generate-ssh-keys` pokynů příkazového řádku Azure CLI vytvoří a nastaví veřejné a privátní klíče ve vašem virtuálním počítači a `$Home` adresáři. Ve výchozím nastavení jsou klíče umístěny v Cloud Shell v `/home/<user>/.ssh/id_rsa` a `/home/<user>/.ssh/id_rsa.pub` . Vaše `.ssh` Složka je trvalá v imagi 5 GB připojené sdílené složky, která se používá k zachování `$Home` .

Vaše uživatelské jméno na tomto VIRTUÁLNÍm počítači bude vaše uživatelské jméno použité v Cloud Shell ($ User@Azure: ).

### <a name="ssh-into-your-linux-vm"></a>SSH do virtuálního počítače se systémem Linux
1. Na panelu hledání Azure Portal vyhledejte název svého virtuálního počítače.
2. Kliknutím na připojit získáte název virtuálního počítače a veřejnou IP adresu. <br>
   ![Snímek obrazovky ukazující, jak se připojit k systému Linux V M pomocí verze S H](media/quickstart/sshcmd-copy.png)

3. Připojte se přes SSH k VIRTUÁLNÍmu počítači pomocí `ssh` příkazu cmd.
   ```
   ssh username@ipaddress
   ```

Při navazování připojení SSH by se měla zobrazit úvodní výzva Ubuntu. <br>
![Snímek obrazovky s inicializací Ubuntu a úvodní výzvou po navázání připojení S hod.](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Vyčištění 
1. Ukončete relaci SSH.
   ```
   exit
   ```

2. Odstraňte skupinu prostředků a všechny prostředky, které jsou v ní obsažené.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Další kroky
[Další informace o trvalém ukládání souborů pro bash v Cloud Shell](persisting-shell-storage.md) <br>
[Další informace o Azure CLI](/cli/azure/) <br>
[Další informace o službě Azure Files Storage](../storage/files/storage-files-introduction.md) <br>