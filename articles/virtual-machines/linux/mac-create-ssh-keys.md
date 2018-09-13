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
ms.date: 09/11/2018
ms.author: cynthn
ms.openlocfilehash: 4b86749f8a2e68dd5bae11ae440db4d6524e3a91
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44713761"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Rychlé kroky: vytvořit a používat pár veřejného a privátního klíče SSH pro virtuální počítače s Linuxem v Azure

Pomocí páru klíčů SSH (Secure Shell) můžete v Azure vytvořit virtuální počítače, které k ověřování používají klíče SSH. Není potom potřeba používat k přihlašování hesla. Tento článek ukazuje, jak rychle vygenerovat a použít pár souborů veřejného a privátního klíče SSH pro virtuální počítače s Linuxem. Můžete provést tyto kroky s Azure Cloud Shell, s macOS nebo Linux hostitele, subsystém Windows pro Linux a další nástroje, které podporují OpenSSH. 

> [!NOTE]
> Virtuální počítače vytvořené pomocí klíčů SSH jsou ve výchozím nastavení nakonfigurované se hesla, který výrazně zvyšuje obtížnost rozluštění útoky hrubou silou. 

Další související informace a příklady najdete v tématu [dvojic klíčů podrobný postup vytvoření SSH](create-ssh-keys-detailed.md).

Další způsoby, jak vygenerovat a používat klíče SSH na počítači Windows, naleznete v tématu [klíče, jak použít SSH s Windows v Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

Použití `ssh-keygen` příkazu vygenerujte soubory veřejného a privátního klíče SSH. Ve výchozím nastavení jsou tyto soubory v adresáři ~/.ssh vytvoří. Můžete zadat jiné umístění a volitelné heslo (*heslo*) pro přístup k souboru privátního klíče. Pokud v daném umístění existuje pár klíčů SSH se stejným názvem, tyto soubory jsou přepsány.

Následující příkaz vytvoří pár klíčů SSH pomocí šifrování RSA a bitovou délku 2048:

```bash
ssh-keygen -t rsa -b 2048
```

Pokud používáte [příkazového řádku Azure CLI 2.0](/cli/azure) k vytvoření virtuálního počítače s [az vm vytvořit](/cli/azure/vm#az-vm-create) příkaz, může volitelně generovat SSH soubory veřejného a privátního klíče pomocí `--generate-ssh-keys` možnost. Soubory klíče jsou uloženy v adresáři ~/.ssh, pokud není určeno jinak s `--ssh-dest-key-path` možnost. `--generate-ssh-keys` Možnost nepřepíše existující soubory klíčů, místo toho vrátí chybu. V následujícím příkazu nahraďte *VMname* a *RGname* vlastními hodnotami:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Zadejte veřejný klíč SSH při nasazování virtuálního počítače

Pokud chcete vytvořit virtuální počítač s Linuxem, který používá klíče SSH pro ověřování, zadejte veřejný klíč SSH při vytváření virtuálního počítače pomocí webu Azure portal, rozhraní příkazového řádku Azure, šablon Azure Resource Manageru nebo jiné metody:

* [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí šablony Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Pokud nejste obeznámeni s formátem veřejný klíč SSH, můžete zobrazit veřejný klíč následujícím `cat` příkaz a nahraďte `~/.ssh/id_rsa.pub` s cesta a název souboru vlastního souboru veřejného klíče v případě potřeby:

```bash
cat ~/.ssh/id_rsa.pub
```

Typická hodnota veřejného klíče vypadá takto:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Pokud kopírujete a vkládáte obsah souboru veřejného klíče pro použití webu Azure portal nebo šablony Resource Manageru, ujistěte se, že nekopírujete žádné koncové mezery. Pokud chcete zkopírovat veřejný klíč v systému macOS, můžete předat souboru veřejného klíče pro **pbcopy**. Podobně jako v systému Linux, můžete předat souboru veřejného klíče pro programy, jako **xclip**.

Veřejný klíč, který umístíte na virtuální počítač s Linuxem v Azure je ve výchozím nastavení uloží v ~ /.ssh/id_rsa.pub, pokud jste zadali při vytváření páru klíčů do jiného umístění. Použít [příkazového řádku Azure CLI 2.0](/cli/azure) k vytvoření virtuálního počítače pomocí stávajícího veřejného klíče, zadejte hodnotu a volitelně umístění tento veřejný klíč pomocí [az vm vytvořit](/cli/azure/vm#az-vm-create) příkazů `--ssh-key-value` možnost. V následujícím příkazu nahraďte *VMname*, *RGname*, a *keyFile* vlastními hodnotami:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-value @keyFile
```

## <a name="ssh-into-your-vm"></a>Připojení SSH k virtuálnímu počítači

Veřejný klíč nasazené na vašem virtuálním počítači Azure, a privátní klíč v místním systému, SSH k virtuálnímu počítači pomocí IP adresy nebo název DNS virtuálního počítače. V následujícím příkazu nahraďte *azureuser* a *myvm.westus.cloudapp.azure.com* se uživatelské jméno správce a plně kvalifikovaný název domény (nebo IP adresou):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste při vytváření páru klíčů zadali přístupové heslo, zadejte přístupové heslo vyzvání během procesu přihlášení. Virtuální počítač se přidá do souboru ~/.ssh/known_hosts a nebudete vyzváni k připojení znovu, dokud buď veřejný klíč na změny virtuálního počítače Azure nebo z ~/.ssh/known_hosts neodebere název serveru.

## <a name="next-steps"></a>Další postup

* Další informace o práci s páry klíčů SSH najdete v tématu [dvojic klíčů podrobné pokyny k vytváření a správě SSH](create-ssh-keys-detailed.md).

* Pokud máte potíže s připojením SSH k virtuálním počítačům Azure, přečtěte si [řešení potíží s připojení SSH k virtuálnímu počítači Azure Linux](troubleshoot-ssh-connection.md).
