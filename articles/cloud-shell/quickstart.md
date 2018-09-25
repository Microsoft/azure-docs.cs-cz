---
title: Bash v rámci rychlého startu Azure Cloud Shell | Dokumentace Microsoftu
description: Rychlý start pro Bash ve službě Cloud Shell
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: juluk
ms.openlocfilehash: 0b3616a723e793ab1ce8d7bcca1f53ca10ec4f96
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970650"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Rychlý start pro Bash ve službě Azure Cloud Shell

Tento dokument podrobně popisuje, jak pomocí prostředí Bash ve službě Azure Cloud Shell v [webu Azure portal](https://ms.portal.azure.com/).

> [!NOTE]
> A [prostředí PowerShell ve službě Azure Cloud Shell](quickstart-powershell.md) rychlý start je také k dispozici.

## <a name="start-cloud-shell"></a>Spustit Cloud Shell
1. Spuštění **Cloud Shell** z horního navigačního panelu na webu Azure portal. <br>
![](media/quickstart/shell-icon.png)

2. Vyberte předplatné, jak vytvořit účet úložiště a sdílet soubory aplikace Microsoft Azure.
3. Vyberte možnost "Vytvořit úložiště"

> [!TIP]
> Jste automaticky ověřeni pro Azure CLI v každé relaci.

### <a name="select-the-bash-environment"></a>Vyberte prostředí Bash
Zkontrolujte, že prostředí rozevíracího seznamu na levé straně okna prostředí `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Nastavení předplatného
1. Seznam předplatných, ke kterým máte přístup.
```azurecli-interactive
az account list
```

2. Nastavení předplatného upřednostňované: <br>
```azurecli-interactive
az account set --subscription my-subscription-name`
```

> [!TIP]
> Vaše předplatné se zachová pro budoucí relace pomocí `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte novou skupinu prostředků v WestUS s názvem "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Vytvoření virtuálního počítače s Linuxem
Vytvoření virtuálního počítače s Ubuntu v nové skupiny prostředků. Azure CLI vytvořit klíče SSH, který se nastavení virtuálního počítače s nimi. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Pomocí `--generate-ssh-keys` instruuje Azure CLI k vytvoření a nastavení veřejných a privátních klíčů ve virtuálním počítači a `$Home` adresáře. Ve výchozím nastavení jsou umístěny klíče ve službě Cloud Shell v `/home/<user>/.ssh/id_rsa` a `/home/<user>/.ssh/id_rsa.pub`. Vaše `.ssh` složky se ukládají v připojené sdílené složky 5 GB image použité k uchování `$Home`.

Vaše uživatelské jméno v tomto virtuálním počítači bude vaše uživatelské jméno používané ve službě Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH do virtuálního počítače s Linuxem
1. Vyhledejte název vašeho virtuálního počítače na panelu Azure search na portálu.
2. Klikněte na možnost připojit, aby název virtuálního počítače a veřejnou IP adresu. <br>
![](media/quickstart/sshcmd-copy.png)

3. SSH k virtuálnímu počítači s `ssh` cmd.
```
ssh username@ipaddress
```

Při navazování připojení SSH, měli byste vidět řádku uvítání systémem Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Čištění 
1. Ukončení vaší ssh relace.
```azurecli-interactive
exit
```

2. Odstraňte skupinu prostředků a všechny prostředky v ní.
```azurecli-interactive
az group delete -n MyRG
```

## <a name="next-steps"></a>Další postup
[Další informace o zachování souborů pro Bash ve službě Cloud Shell](persisting-shell-storage.md) <br>
[Další informace o rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/) <br>
[Další informace o službě soubory Azure storage](../storage/files/storage-files-introduction.md) <br>
