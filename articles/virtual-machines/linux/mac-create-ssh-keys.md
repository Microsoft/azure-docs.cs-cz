---
title: Vytvoření a použití páru klíčů SSH pro virtuální počítače se systémem Linux v Azure
description: Jak vytvořit a používat pár veřejného a privátního klíče SSH pro virtuální počítače se systémem Linux v Azure pro zlepšení zabezpečení procesu ověřování.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c77375782ba23114be1953d9f8ad7de31ab06f1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582183"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Rychlé kroky: vytvoření a použití páru veřejných privátních klíčů SSH pro virtuální počítače se systémem Linux v Azure

Pomocí páru klíčů SSH (Secure Shell) můžete v Azure vytvořit virtuální počítače, které k ověřování používají klíče SSH. V tomto článku se dozvíte, jak rychle vygenerovat a používat dvojici souborů klíčů veřejného privátního klíče SSH pro virtuální počítače se systémem Linux. Tento postup můžete provést pomocí Azure Cloud Shell, hostitele macOS nebo Linux. 

> [!NOTE]
> Virtuální počítače vytvořené pomocí klíčů SSH jsou ve výchozím nastavení nakonfigurovány se zakázanými hesly, což významně zvyšuje obtížnost útoků hrubou silou. 

Další informace a příklady najdete v tématu [podrobný postup vytvoření párů klíčů ssh](create-ssh-keys-detailed.md).

Další způsoby, jak vygenerovat a používat klíče SSH na počítači s Windows, najdete v tématu [použití klíčů ssh s Windows v Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Vytvoření páru klíčů SSH

Pomocí `ssh-keygen` příkazu vygenerujte soubory veřejného a privátního klíče SSH. Ve výchozím nastavení se tyto soubory vytvoří v adresáři ~/.ssh. Pro přístup k souboru privátního klíče můžete zadat jiné umístění a volitelné *heslo (heslo*). Pokud v daném umístění existuje pár klíčů SSH se stejným názvem, tyto soubory se přepíší.

Následující příkaz vytvoří pár klíčů SSH pomocí šifrování RSA a bitovou délku 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Pokud k vytvoření virtuálního počítače pomocí příkazu [AZ VM Create](/cli/azure/vm#az-vm-create) použijete rozhraní příkazového [řádku Azure CLI](/cli/azure) , můžete volitelně vygenerovat soubory veřejného a privátního klíče SSH pomocí `--generate-ssh-keys` Možnosti. Klíčové soubory jsou uloženy v adresáři ~/.ssh, pokud nejsou zadány jinak s `--ssh-dest-key-path` možností. Pokud pár klíčů ssh již existuje a  `--generate-ssh-keys` možnost se použije, nový pár klíčů se nevygeneruje, ale místo toho se použije existující pár klíčů. V následujícím příkazu nahraďte *VMname* a *RGname* vlastními hodnotami:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Při nasazování virtuálního počítače zadejte veřejný klíč SSH.

Pokud chcete vytvořit virtuální počítač se systémem Linux, který používá klíče SSH k ověřování, zadejte svůj veřejný klíč SSH při vytváření virtuálního počítače pomocí Azure Portal, Azure CLI, Azure Resource Manager Templates nebo jiných metod:

* [Vytvoření virtuálního počítače s Linuxem pomocí webu Azure Portal](quick-create-portal.md)
* [Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI](quick-create-cli.md)
* [Vytvoření virtuálního počítače s Linuxem pomocí šablony Azure](create-ssh-secured-vm-from-template.md)

Pokud nejste obeznámeni s formátem veřejného klíče SSH, můžete zobrazit veřejný klíč pomocí následujícího `cat` příkazu, který v `~/.ssh/id_rsa.pub` případě potřeby nahradí cestu a název souboru vlastního veřejného klíče:

```bash
cat ~/.ssh/id_rsa.pub
```

Typická hodnota veřejného klíče vypadá jako v tomto příkladu:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Pokud zkopírujete a vložíte obsah souboru veřejného klíče, který chcete použít v Azure Portal nebo šabloně Správce prostředků, ujistěte se, že nekopírujete žádné koncové mezery. Chcete-li zkopírovat veřejný klíč v macOS, můžete soubor veřejného klíče předat kanálu `pbcopy` . Podobně v systému Linux můžete soubor veřejného klíče přesměrovat do programů, jako je například `xclip` .

Veřejný klíč, který umístíte na virtuální počítač Linux v Azure, je ve výchozím nastavení uložený v ~/.ssh/id_rsa. pub, pokud jste nezadali jiné umístění při vytváření páru klíčů. Pokud chcete použít rozhraní příkazového [řádku Azure CLI 2,0](/cli/azure) k vytvoření virtuálního počítače s existujícím veřejným klíčem, zadejte hodnotu a volitelně umístění tohoto veřejného klíče pomocí příkazu [AZ VM Create](/cli/azure/vm#az-vm-create) s `--ssh-key-values` možností. V následujícím příkazu nahraďte *myVM*, *myResourceGroup*, *UbuntuLTS*, *azureuser* a *mysshkey. pub* vlastními hodnotami:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Pokud chcete s vaším VIRTUÁLNÍm počítačem používat víc klíčů SSH, můžete je zadat do seznamu odděleného mezerami, jako je to `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub` .


## <a name="ssh-into-your-vm"></a>Připojení SSH k virtuálnímu počítači

S veřejným klíčem nasazeným na VIRTUÁLNÍm počítači Azure a privátním klíčem v místním systému se k VIRTUÁLNÍmu počítači přihlaste přes SSH pomocí IP adresy nebo názvu DNS vašeho virtuálního počítače. V následujícím příkazu nahraďte *azureuser* a *myvm.westus.cloudapp.Azure.com* uživatelským jménem správce a plně kvalifikovaným názvem domény (nebo IP adresou):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Pokud jste při vytváření páru klíčů zadali heslo, zadejte toto heslo při zobrazení výzvy během procesu přihlášení. Virtuální počítač se přidá do souboru ~/.ssh/known_hosts a nebudete se k němu znovu připojovat, dokud se nezmění veřejný klíč na VIRTUÁLNÍm počítači Azure nebo se neodebere název serveru z ~/.ssh/known_hosts.

Pokud virtuální počítač používá zásady přístupu za běhu, musíte požádat o přístup, abyste se mohli připojit k virtuálnímu počítači. Další informace o zásadách za běhu najdete v tématu [Správa přístupu k virtuálním počítačům pomocí zásad podle časových údajů](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Další kroky

* Další informace o práci s páry klíčů SSH najdete v tématu [Podrobné kroky pro vytváření a správu párů klíčů ssh](create-ssh-keys-detailed.md).

* Pokud máte problémy s připojením SSH k virtuálním počítačům Azure, přečtěte si téma [řešení potíží s připojením SSH k virtuálnímu počítači Azure Linux](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).