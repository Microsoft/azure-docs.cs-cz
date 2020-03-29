---
title: Vytvoření a použití páru klíčů SSH pro virtuální počítače s Linuxem v Azure
description: Jak vytvořit a použít dvojici veřejných a soukromých klíčů SSH pro virtuální počítače s Linuxem v Azure ke zlepšení zabezpečení procesu ověřování.
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: af18a32143ebc9db7be923b09de106b79022321f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969040"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Rychlé kroky: Vytvoření a použití páru sklíčů s veřejným a soukromým klíčem SSH pro virtuální počítače s Linuxem v Azure

Pomocí zabezpečeného páru klíčů prostředí (SSH) můžete v Azure vytvořit virtuální počítače, které používají klíče SSH pro ověřování, což eliminuje potřebu hesla pro přihlášení. Tento článek ukazuje, jak rychle generovat a používat dvojici souborů s veřejným a soukromým klíčem SSH pro virtuální počítače s Linuxem. Tyto kroky můžete provést pomocí Azure Cloud Shell, hostitele macOS nebo Linuxu, podsystému Windows pro Linux a dalších nástrojů, které podporují OpenSSH. 

> [!NOTE]
> Virtuální počítače vytvořené pomocí klíčů SSH jsou ve výchozím nastavení nakonfigurovány se zakázanými hesly, což výrazně zvyšuje obtížnost útoků hrubou silou. 

Další pozadí a příklady najdete [v tématu Podrobné kroky k vytvoření párů klíčů SSH](create-ssh-keys-detailed.md).

Další způsoby generování a používání klíčů SSH v počítači se systémem Windows najdete v tématu [Jak používat klíče SSH s Windows v Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

Pomocí `ssh-keygen` příkazu vygenerujte soubory s veřejným a soukromým klíčem SSH. Ve výchozím nastavení jsou tyto soubory vytvořeny v adresáři ~/.ssh. Můžete zadat jiné umístění a volitelné heslo *(přístupové heslo)* pro přístup k souboru soukromého klíče. Pokud v daném umístění existuje dvojice klíčů SSH se stejným názvem, jsou tyto soubory přepsány.

Následující příkaz vytvoří dvojici klíčů SSH pomocí šifrování RSA a bitovou délku 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Pokud použijete [Azure CLI](/cli/azure) k vytvoření virtuálního počítače pomocí [příkazu az vm create,](/cli/azure/vm#az-vm-create) můžete `--generate-ssh-keys` volitelně generovat soubory s veřejným a soukromým klíčem SSH pomocí možnosti. Klíčové soubory jsou uloženy v adresáři ~/.ssh, pokud není uvedeno jinak s `--ssh-dest-key-path` možností. Tato `--generate-ssh-keys` možnost nepřepíše existující soubory klíčů, místo toho vrátí chybu. V následujícím příkazu nahraďte *název VMname* a *RGname* vlastními hodnotami:

```azurecli
az vm create --name VMname --resource-group RGname --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Poskytnutí veřejného klíče SSH při nasazování virtuálního mísy

Pokud chcete vytvořit virtuální počítač s Linuxem, který používá klíče SSH pro ověřování, zadejte veřejný klíč SSH při vytváření virtuálního počítače pomocí portálu Azure, Azure CLI, šablon Azure Resource Manager nebo jiných metod:

* [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure Portal](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Vytvoření virtuálního počítače s Linuxem pomocí šablony Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Pokud nejste obeznámeni s formátem veřejného klíče SSH, můžete zobrazit `cat` veřejný klíč `~/.ssh/id_rsa.pub` pomocí následujícího příkazu a v případě potřeby nahradit cestu a název vlastního souboru veřejného klíče:

```bash
cat ~/.ssh/id_rsa.pub
```

Typická hodnota veřejného klíče vypadá takto:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Pokud zkopírujete a vložíte obsah souboru veřejného klíče, který chcete použít na webu Azure Portal nebo v šabloně Správce prostředků, ujistěte se, že nekopírujete žádné koncové prázdné znaky. Chcete-li zkopírovat veřejný klíč v systému macOS, můžete soubor veřejného klíče převést do kanálu `pbcopy`. Podobně v Linuxu můžete kanál soubor veřejného `xclip`klíče do programů, jako je například .

Veřejný klíč, který umístíte na virtuální počítač s Linuxem v Azure, je ve výchozím nastavení uložen ý v ~/.ssh/id_rsa.pub, pokud jste při vytváření páru klíčů nezadali jiné umístění. Chcete-li použít [Azure CLI 2.0](/cli/azure) k vytvoření virtuálního počítače s existujícím veřejným klíčem, zadejte hodnotu a `--ssh-key-values` volitelně umístění tohoto veřejného klíče pomocí [příkazu az vm create](/cli/azure/vm#az-vm-create) s možností. V následujícím příkazu nahraďte *název VMname*, *RGname*a *keyFile* vlastními hodnotami:

```azurecli
az vm create --name VMname --resource-group RGname --ssh-key-values mysshkey.pub
```

Pokud chcete používat více klíčů SSH s virtuálním počítačem, můžete je zadat `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub`do seznamu odděleného mezerami, jako je tento .


## <a name="ssh-into-your-vm"></a>Připojení SSH k virtuálnímu počítači

S veřejným klíčem nasazeným na vašem virtuálním počítači Azure a privátním klíčem v místním systému SSH do virtuálního počítače pomocí IP adresy nebo názvu DNS vašeho virtuálního počítače. V následujícím příkazu nahraďte *azureuser* a *myvm.westus.cloudapp.azure.com* uživatelským jménem správce a plně kvalifikovaným názvem domény (nebo IP adresou):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste při vytváření dvojice klíčů zadali přístupové heslo, zadejte toto heslo po zobrazení výzvy během procesu přihlášení. Virtuální počítač se přidá do vašeho souboru ~/.ssh/known_hosts a nebudete vyzváni k opětovnému připojení, dokud se nezmění veřejný klíč na vašem virtuálním počítači Azure nebo dokud se název serveru neodebere z ~/.ssh/known_hosts.

Pokud virtuální ho správu používá zásady přístupu za chvíli, musíte požádat o přístup, než se budete moct připojit k virtuálnímu virtuálnímu zařízení. Další informace o zásadách just-in-time najdete v tématu [Správa přístupu k virtuálnímu počítači pomocí zásad just in time](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Další kroky

* Další informace o práci s dvojicemi klíčů SSH naleznete [v tématu Podrobné kroky k vytvoření a správě párů klíčů SSH](create-ssh-keys-detailed.md).

* Pokud máte potíže s připojením SSH k virtuálním počítačům Azure, [přečtěte si článek Poradce při potížích s připojením SSH k virtuálnímu počítači Azure Linux](troubleshoot-ssh-connection.md).
