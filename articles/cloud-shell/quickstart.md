---
title: Azure Cloud Shell Rychlý start - Bash
description: Přečtěte si, jak používat příkazový řádek Bash ve vašem prohlížeči pomocí Azure Cloud Shell.
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 58d795a5aee79e4149864a79a923ce34950b31d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458065"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Úvodní příručka pro Bash ve službě Azure Cloud Shell

Tento dokument podrobně popisuje, jak používat Bash v Azure Cloud Shell na [webu Azure Portal](https://ms.portal.azure.com/).

> [!NOTE]
> [PowerShell v Azure Cloud Shell](quickstart-powershell.md) Úvodní je také k dispozici.

## <a name="start-cloud-shell"></a>Spuštění Cloud Shellu
1. Spusťte **Cloud Shell** z horní navigace na webu Azure Portal. <br>
![](media/quickstart/shell-icon.png)

2. Vyberte předplatné a vytvořte účet úložiště a sdílenou složku Microsoft Azure Files.
3. Vyberte možnost Vytvořit úložiště.

> [!TIP]
> Jste automaticky ověřeni pro Azure CLI v každé relaci.

### <a name="select-the-bash-environment"></a>Vyberte prostředí Bash
Zkontrolujte, zda prostředí drop-down z levé strany okna shell udává `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Nastavení předplatného
1. Seznam odběrů, ke kterých máte přístup.
   ```azurecli-interactive
   az account list
   ```

2. Nastavte preferované předplatné: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Vaše předplatné bude zapamatováno pro budoucí relace pomocí `/home/<user>/.azure/azureProfile.json`aplikace .

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte novou skupinu prostředků ve společnosti WestUS s názvem "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Vytvoření virtuálního počítače s Linuxem
Vytvořte virtuální počítač Ubuntu ve své nové skupině prostředků. Azure CLI vytvoří klíče SSH a nastavit virtuální počítač s nimi. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Použití `--generate-ssh-keys` pokyn Azure CLI k vytvoření a nastavení veřejných `$Home` a soukromých klíčů ve vašem virtuálním počítači a adresáři. Ve výchozím nastavení jsou klíče `/home/<user>/.ssh/id_rsa` `/home/<user>/.ssh/id_rsa.pub`umístěny v prostředí Cloud Shell na adrese a . Složka `.ssh` je zachována v obrázku s připojenou sdílenou `$Home`složkou o velikosti 5 GB, který slouží k uchování .

Vaše uživatelské jméno na tomto virtuálním počítači budeUser@Azure:vaše uživatelské jméno používané v Cloud Shell ($ ).

### <a name="ssh-into-your-linux-vm"></a>SSH do vašeho virtuálního počítače s Linuxem
1. Vyhledejte název virtuálního počítače na panelu hledání na portálu Azure.
2. Kliknutím na "Připojit" získáte název virtuálního počítače a veřejnou IP adresu. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH do virtuálního `ssh` počítače s cmd.
   ```
   ssh username@ipaddress
   ```

Po navázání připojení SSH byste měli vidět výzvu uvítání Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Čištění 
1. Ukončete relaci ssh.
   ```
   exit
   ```

2. Odstraňte skupinu prostředků a všechny prostředky v ní.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Další kroky
[Další informace o uchování souborů pro Bash v Cloud Shellu](persisting-shell-storage.md) <br>
[Další informace o azure cli](https://docs.microsoft.com/cli/azure/) <br>
[Informace o úložišti Souborů Azure](../storage/files/storage-files-introduction.md) <br>
