---
title: Vytvoření a použití páru klíčů SSH pro virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Jak vytvořit a používat pár veřejného a privátního klíče SSH pro virtuální počítače s Linuxem v Azure k vylepšení zabezpečení procesu ověřování.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 2e3d86d776f44c47a33bf075cf7f2140a3940e5e
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37928450"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Rychlé kroky: vytvořit a používat pár veřejného a privátního klíče SSH pro virtuální počítače s Linuxem v Azure
Pomocí páru klíčů SSH (Secure Shell) můžete v Azure vytvořit virtuální počítače, které k ověřování používají klíče SSH. Není potom potřeba používat k přihlašování hesla. Tento článek ukazuje, jak rychle vygenerovat a použít pár souborů veřejného a privátního klíče SSH pro virtuální počítače s Linuxem. Můžete provést tyto kroky s Azure Cloud Shell, s macOS nebo Linux hostitele, subsystém Windows pro Linux a další nástroje, které podporují OpenSSH. 

Další související informace a příklady najdete v tématu [dvojic klíčů podrobný postup vytvoření SSH](create-ssh-keys-detailed.md).

Další způsoby, jak vygenerovat a používat klíče SSH na počítači Windows, naleznete v tématu [klíče, jak použít SSH s Windows v Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH
Použití `ssh-keygen` příkazu vygenerujte SSH soubory veřejného a privátního klíče, které jsou ve výchozím nastavení vytvoří v `~/.ssh` adresáře. Můžete zadat jiné umístění a dodatečné přístupové heslo (heslo pro přístup k souboru privátního klíče) při zobrazení výzvy. Pokud existuje pár klíčů SSH v aktuálním umístění, tyto soubory jsou přepsány.

```bash
ssh-keygen -t rsa -b 2048
```

Pokud používáte [příkazového řádku Azure CLI 2.0](/cli/azure) k vytvoření virtuálního počítače, může volitelně Generovat soubory veřejného a privátního klíče SSH spuštěním [az vm vytvořit](/cli/azure/vm#az_vm_create) příkazů `--generate-ssh-keys` možnost. Klíče jsou uložené v adresáři ~/.ssh. Všimněte si, že tento parametr nepřepisuje klíče Pokud již existují v dané oblasti.

## <a name="provide-ssh-public-key-when-deploying-a-vm"></a>Zadejte veřejný klíč SSH při nasazování virtuálního počítače
Pokud chcete vytvořit virtuální počítač s Linuxem, který používá klíče SSH pro ověřování, zadejte veřejný klíč SSH při vytváření virtuálního počítače pomocí webu Azure portal, rozhraní příkazového řádku, šablon Resource Manageru nebo jiné metody:

* [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí šablony Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Pokud nejste obeznámeni s formátem veřejný klíč SSH, se zobrazí veřejný klíč spuštěním `cat` následujícím způsobem nahraďte `~/.ssh/id_rsa.pub` nahraďte vlastním umístěním souboru veřejného klíče:

```bash
cat ~/.ssh/id_rsa.pub
```

Pokud obsah souboru veřejného klíče kopírujete a vkládáte za účelem použití na webu Azure Portal nebo v šabloně Resource Manageru, ujistěte se, že nekopírujete žádné prázdné znaky. Například pokud používáte macOS, můžete předat soubor veřejného klíče (ve výchozím nastavení, `~/.ssh/id_rsa.pub`) k **pbcopy** chcete zkopírovat obsah (existují další programy v systému Linux, které totéž, jako například **xclip**).

Veřejný klíč, který umístíte na virtuální počítač s Linuxem v Azure je ve výchozím nastavení uloží v `~/.ssh/id_rsa.pub`, pokud jste změnili umístění při vytváření klíče. Pokud používáte [příkazového řádku Azure CLI 2.0](/cli/azure) k vytvoření virtuálního počítače pomocí stávajícího veřejného klíče, zadejte hodnotu nebo umístění veřejného klíče spuštěním [az vm vytvořit](/cli/azure/vm#az_vm_create) příkazů `--ssh-key-value` možnost. 

## <a name="ssh-to-your-vm"></a>Přístup k vašemu virtuálnímu počítači přes SSH
Veřejný klíč nasazené na vašem virtuálním počítači Azure, a privátní klíč v místním systému, SSH k virtuálnímu počítači pomocí IP adresy nebo název DNS virtuálního počítače. Nahraďte *azureuser* a *myvm.westus.cloudapp.azure.com* v následujícím příkazu se uživatelské jméno správce a plně kvalifikovaný název domény (nebo IP adresou):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste při vytváření páru klíčů zadali přístupové heslo, zadejte jej na vyzvání během procesu přihlašování. (Server se přidá do vaší složky `~/.ssh/known_hosts` a nové připojení se nebude vyžadovat, dokud se nezmění veřejný klíč na virtuálním počítači Azure nebo se neodebere název serveru ze složky `~/.ssh/known_hosts`.)

Virtuální počítače Azure vytvořené pomocí páru klíčů SSH jsou ve výchozím nastavení nakonfigurované se zakázaným heslem, aby byly pokusy o rozluštění hesla útokem hrubou silou výrazně nákladnější a tedy obtížnější. 

## <a name="next-steps"></a>Další postup

Tento článek popisuje vytvoření jednoduchého pár klíčů SSH pro rychlé použití. 

* Pokud potřebujete další pomoc pro práci s páru klíčů SSH, přečtěte si [dvojic klíčů podrobné pokyny k vytváření a správě SSH](create-ssh-keys-detailed.md).

* Pokud máte problémy s připojením SSH k virtuálnímu počítači Azure, přečtěte si [řešení potíží s připojení SSH k virtuálnímu počítači Azure Linux](troubleshoot-ssh-connection.md).


