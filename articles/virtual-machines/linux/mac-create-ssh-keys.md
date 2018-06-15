---
title: Vytvoření a použití páru klíčů SSH pro virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Postup vytvoření a používání dvojici klíčů veřejný soukromý SSH pro virtuální počítače s Linuxem v Azure pro zlepšení zabezpečení procesu ověřování.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: iainfou
ms.openlocfilehash: 137fb13ff286e5284b8e8910834913ec9f1d48a9
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2018
ms.locfileid: "31602626"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Rychlé kroky: vytváření a používání dvojici klíčů veřejný soukromý SSH pro virtuální počítače s Linuxem v Azure
Pomocí páru klíčů SSH (Secure Shell) můžete v Azure vytvořit virtuální počítače, které k ověřování používají klíče SSH. Není potom potřeba používat k přihlašování hesla. Tento článek ukazuje, jak rychle generovat a používat páru veřejného a privátního klíče souboru SSH pro virtuální počítače s Linuxem. Můžete provést tyto kroky prostředí cloudu Azure, systému macOS nebo Linux hostitele, subsystému Windows pro Linux a dalších nástrojů, které podporují OpenSSH. 

Pozadí a příklady naleznete v tématu [podrobné kroky k vytvoření SSH klíče dvojice](create-ssh-keys-detailed.md).

Další způsoby, jak vygenerovat a používat klíče SSH na počítači se systémem Windows, najdete v části [klíče použití SSH se systémem Windows v Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH
Použití `ssh-keygen` příkazu vygenerujte SSH veřejné a soukromé klíče soubory, které jsou ve výchozím nastavení, které jsou vytvořené v `~/.ssh` adresáře. Můžete zadat jiné umístění a další přístupové heslo (heslo pro přístup k souboru privátního klíče) při zobrazení výzvy. Pokud dvojici klíčů SSH existuje v aktuální umístění, tyto soubory jsou přepsány.

```bash
ssh-keygen -t rsa -b 2048
```

Pokud používáte [Azure CLI 2.0](/cli/azure) k vytvoření virtuálního počítače, může volitelně generovat SSH soubory veřejného a privátního klíče spuštěním [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) s `--generate-ssh-keys` možnost. Klíče jsou uložené v adresáři ~/.ssh. Poznámka: Tato možnost příkaz Pokud již existují v umístění v nepřepisuje klíče.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Zadejte veřejný klíč SSH při nasazování virtuálního počítače
Pokud chcete vytvořit virtuální počítač Linux, který používá klíče SSH pro ověřování, zadejte svůj veřejný klíč SSH při vytváření virtuálního počítače pomocí portálu Azure, rozhraní příkazového řádku, šablony správce prostředků nebo jiných metod:

* [Vytvořit virtuální počítač s Linuxem pomocí portálu Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvořit virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí šablony Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Pokud si nejste obeznámeni s formátem veřejný klíč SSH, zobrazí se svým veřejným klíčem spuštěním `cat` následujícím způsobem, nahraďte `~/.ssh/id_rsa.pub` s vlastní umístění soubor veřejného klíče:

```bash
cat ~/.ssh/id_rsa.pub
```

Pokud obsah souboru veřejného klíče kopírujete a vkládáte za účelem použití na webu Azure Portal nebo v šabloně Resource Manageru, ujistěte se, že nekopírujete žádné prázdné znaky. Například pokud použijete systému macOS, můžete předat soubor veřejného klíče (ve výchozím nastavení, `~/.ssh/id_rsa.pub`) k **pbcopy** zkopírovat obsah (existují další programy Linux, které provedou totéž, jako například **xclip**).

Veřejný klíč, který můžete umístit na virtuálním počítačům s Linuxem v Azure je ve výchozím nastavení uložená v `~/.ssh/id_rsa.pub`, pokud jste změnili umístění při vytváření klíče. Pokud používáte [Azure CLI 2.0](/cli/azure) Pokud chcete vytvořit virtuální počítač s existující veřejný klíč, zadejte hodnotu nebo umístění tohoto veřejného klíče spuštěním [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create) s `--ssh-key-value` možnost. 

## <a name="ssh-to-your-vm"></a>SSH k virtuálnímu počítači
S veřejný klíč nasazené na vašem virtuálním počítači Azure a privátní klíč v lokálním systému, SSH k virtuálnímu počítači pomocí IP adresu nebo název DNS vašeho virtuálního počítače. Nahraďte *azureuser* a *myvm.westus.cloudapp.azure.com* v následujícím příkazu s uživatelské jméno správce a plně kvalifikovaný název domény (nebo IP adresa):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste při vytváření páru klíčů zadali přístupové heslo, zadejte jej na vyzvání během procesu přihlašování. (Server se přidá do vaší složky `~/.ssh/known_hosts` a nové připojení se nebude vyžadovat, dokud se nezmění veřejný klíč na virtuálním počítači Azure nebo se neodebere název serveru ze složky `~/.ssh/known_hosts`.)

Virtuální počítače Azure vytvořené pomocí páru klíčů SSH jsou ve výchozím nastavení nakonfigurované se zakázaným heslem, aby byly pokusy o rozluštění hesla útokem hrubou silou výrazně nákladnější a tedy obtížnější. 

## <a name="next-steps"></a>Další postup

Tento článek popisuje vytvoření jednoduché pár klíčů SSH pro rychlé použití. 

* Pokud potřebujete další pomoc pro práci s dvojici klíčů SSH, přečtěte si téma [podrobné pokyny k vytváření a správě SSH klíče dvojice](create-ssh-keys-detailed.md).

* Pokud máte problémy s připojení SSH pro virtuální počítač Azure, najdete v části [řešení SSH připojení k virtuální počítač Azure Linux](troubleshoot-ssh-connection.md).


