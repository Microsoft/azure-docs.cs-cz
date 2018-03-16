---
title: "V cloudu Azure Quickstart prostředí bash | Microsoft Docs"
description: "Rychlý start pro Bash v prostředí cloudu"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: juluk
ms.openlocfilehash: e48c54216c5c4ae8e53d4802aafce8883ee97c11
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Rychlý start pro Bash v prostředí cloudu Azure

Tento dokument podrobně popisuje postup použití Bash v prostředí cloudu Azure v [portál Azure](https://ms.portal.azure.com/).

> [!NOTE]
> A [prostředí PowerShell v prostředí cloudu Azure](quickstart-powershell.md) rychlý start je také k dispozici.

## <a name="start-cloud-shell"></a>Spusťte prostředí cloudu
1. Spusťte **cloudové prostředí** z horním navigačním panelu portálu Azure. <br>
![](media/quickstart/shell-icon.png)

2. Vybrat odběr, který chcete vytvořit účet úložiště a sdílet soubory aplikace Microsoft Azure.
3. Vyberte "Vytvoření úložiště"

> [!TIP]
> Pro Azure CLI 2.0 se automaticky ověřeni v každé relaci.

### <a name="select-the-bash-environment"></a>Vyberte prostředí Bash
Zkontrolujte, zda je uveden prostředí rozevíracího seznamu na levé straně okna prostředí `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Nastavení předplatného
1. Seznam odběrů, ke kterým máte přístup k.
```azurecli-interactive
az account list
```

2. Nastavte upřednostňované předplatného: <br>
```azurecli-interactive
az account set --subscription my-subscription-name`
```

> [!TIP]
> Vaše předplatné se uloží pro budoucí relace pomocí `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte novou skupinu prostředků v WestUS s názvem "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Vytvoření virtuálního počítače s Linuxem
Vytvoření virtuálního počítače s Ubuntu v nové skupiny prostředků. Azure CLI 2.0 bude vytvoření klíčů SSH a nastavit virtuální počítač s nimi. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Pomocí `--generate-ssh-keys` dá pokyn 2.0 rozhraní příkazového řádku Azure k vytvoření a nastavení veřejných a privátních klíčů ve vašem virtuálním počítači a `$Home` adresáře. Ve výchozím nastavení klíče uloženy v prostředí cloudu v `/home/<user>/.ssh/id_rsa` a `/home/<user>/.ssh/id_rsa.pub`. Vaše `.ssh` složky je trvalé připojené sdílené složky na obrázku 5 GB použitý k zachování `$Home`.

Vaše uživatelské jméno pro tento virtuální počítač bude vaše uživatelské jméno použité v prostředí cloudu ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH do virtuálním počítačům s Linuxem
1. Vyhledejte název virtuálního počítače v panelu vyhledávání v portálu Azure.
2. Klikněte na tlačítko "Připojit" získat název virtuálního počítače a veřejnou IP adresu. <br>
![](media/quickstart/sshcmd-copy.png)

3. SSH ke svému virtuálnímu počítači pomocí `ssh` cmd.
```
ssh username@ipaddress
```

Při navazování připojení SSH, měli byste vidět úvodní řádku Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Čištění 
1. Ukončení vaší ssh relace.
```azurecli-interactive
exit
```

2. Odstranění skupiny prostředků a všechny prostředky v něm.
```azurecli-interactive
Run `az group delete -n MyRG`
```

## <a name="next-steps"></a>Další postup
[Další informace o zachování souborů pro Bash v prostředí cloudu](persisting-shell-storage.md) <br>
[Další informace o rozhraní příkazového řádku Azure 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Další informace o Azure Files storage](../storage/files/storage-files-introduction.md) <br>
