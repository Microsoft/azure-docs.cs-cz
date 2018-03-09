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
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 3f605645e7a53f285cb7e508034ebab0daa0d335
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Rychlý start pro Bash v prostředí cloudu Azure

Tento dokument podrobně popisuje postup použití Bash v prostředí cloudu Azure v [portál Azure](https://ms.portal.azure.com/).

> [!NOTE]
> A [prostředí PowerShell v prostředí cloudu Azure](quickstart-powershell.md) rychlý start je také k dispozici.

## <a name="start-cloud-shell"></a>Spusťte prostředí cloudu
1. Spusťte **cloudové prostředí** z horním navigačním panelu portálu Azure <br>
![](media/quickstart/shell-icon.png)
2. Vybrat odběr, který chcete vytvořit účet úložiště a sdílet soubory aplikace Microsoft Azure
3. Vyberte "Vytvoření úložiště"

> [!TIP]
> Pro Azure CLI 2.0 se automaticky ověřeni v každé relaci.

### <a name="select-the-bash-environment"></a>Vyberte prostředí Bash
1. Vyberte prostředí rozevíracího seznamu na levé straně okna prostředí <br>
![](media/quickstart/env-selector.png)
2. Vyberte Bash

### <a name="set-your-subscription"></a>Nastavení předplatného
1. Seznam odběrů, ke kterým máte přístup k: <br>
`az account list`
2. Nastavte upřednostňované předplatného: <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> Vaše předplatné se uloží pro budoucí relace pomocí `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Vytvořte novou skupinu prostředků v WestUS s názvem "MyRG": <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>Vytvoření virtuálního počítače s Linuxem
Vytvoření virtuálního počítače s Ubuntu v nové skupiny prostředků. Azure CLI 2.0 bude vytvoření klíčů SSH a instalace virtuálních počítačů s nimi. <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> Veřejné a soukromé klíče používá k ověření svého virtuálního počítače jsou umístěny v `/home/<user>/.ssh/id_rsa` a `/home/<user>/.ssh/id_rsa.pub` pomocí Azure CLI 2.0 ve výchozím nastavení. Je vaší složky .ssh uchovávané v obrázku 5 GB vaší připojené soubory Azure sdílené složky.

Vaše uživatelské jméno pro tento virtuální počítač bude vaše uživatelské jméno použité v prostředí cloudu ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH do virtuálním počítačům s Linuxem
1. Vyhledejte název virtuálního počítače v panelu vyhledávání v portálu Azure
2. Klikněte na tlačítko "Připojit" a spusťte: `ssh username@ipaddress`

![](media/quickstart/sshcmd-copy.png)

Při navazování připojení SSH, měli byste vidět úvodní řádku Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Čištění 
Odstranění skupiny prostředků a všechny prostředky v něm: <br>
Spusťte `az group delete -n MyRG`.

## <a name="next-steps"></a>Další postup
[Další informace o zachování souborů pro Bash v prostředí cloudu](persisting-shell-storage.md) <br>
[Další informace o rozhraní příkazového řádku Azure 2.0](https://docs.microsoft.com/cli/azure/) <br>
[Další informace o Azure Files storage](../storage/files/storage-files-introduction.md) <br>
