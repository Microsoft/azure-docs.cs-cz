---
title: Vytvoření a nahrání obrázku OpenBSD
description: Zjistěte, jak vytvořit a nahrát virtuální pevný disk (VHD), který obsahuje operační systém OpenBSD pro vytvoření virtuálního počítače Azure prostřednictvím rozhraní příkazového příkazu Azure
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/24/2017
ms.author: guybo
ms.openlocfilehash: 1ad1a66d67be7aefe4d9a7acae993e8788cbb193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066739"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Vytvoření a nahrání bitové kopie disku OpenBSD do Azure
Tento článek vám ukáže, jak vytvořit a nahrát virtuální pevný disk (VHD), který obsahuje operační systém OpenBSD. Po nahrání ji můžete použít jako vlastní image k vytvoření virtuálního počítače (VM) v Azure prostřednictvím Azure CLI.


## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte následující položky:

* **Předplatné Azure** – Pokud nemáte účet, můžete ho vytvořit během několika minut. Pokud máte předplatné MSDN, přečtěte si [měsíční kredit Azure pro předplatitele Visual Studia](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). V opačném případě se dozvíte, jak [vytvořit bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure CLI** – ujistěte se, že máte nainstalovaný nejnovější [Azure CLI](/cli/azure/install-azure-cli) a přihlášen k účtu Azure s [az přihlášení](/cli/azure/reference-index).
* **Operační systém OpenBSD nainstalovaný v souboru .vhd** - Podporovaný operační systém OpenBSD ([6.6 verze AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)) musí být nainstalován na virtuální pevný disk. Pro vytváření souborů VHD existuje více nástrojů. Můžete například použít virtualizační řešení, jako je Například Technologie Hyper-V, k vytvoření souboru .vhd a k instalaci operačního systému. Pokyny k instalaci a použití technologie Hyper-V najdete v [tématu Instalace technologie Hyper-V a vytvoření virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Příprava image OpenBSD pro Azure
Na virtuálním počítači, kde jste nainstalovali operační systém OpenBSD 6.1, který přidal podporu Hyper-V, proveďte následující postupy:

1. Pokud není během instalace povolena funkce DHCP, povolte službu následujícím způsobem:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Konzolu sériového zařízení nastavte takto:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Nakonfigurujte instalaci balíčku následujícím způsobem:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Ve výchozím `root` nastavení je uživatel zakázán na virtuálních počítačích v Azure. Uživatelé mohou spouštět příkazy se `doas` zvýšenými oprávněními pomocí příkazu na virtuálním počítači OpenBSD. Doas je ve výchozím nastavení povolen. Další informace naleznete [v tématu doas.conf](https://man.openbsd.org/doas.conf.5). 

5. Nainstalujte a nakonfigurujte požadavky pro Azure Agent takto:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Nejnovější verzi agenta Azure lze vždy nalézt na [GitHubu](https://github.com/Azure/WALinuxAgent/releases). Agenta nainstaluje takto:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Po instalaci Azure Agent, je vhodné ověřit, že běží takto:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Zrušení zřízení systému k jeho vyčištění a jeho vhodná pro opětovné zřízení. Následující příkaz také odstraní poslední zřízený uživatelský účet a přidružená data:

    ```sh
    waagent -deprovision+user -force
    ```

Teď můžete virtuální počítač vypnout.


## <a name="prepare-the-vhd"></a>Příprava virtuálního pevného disku
Formát VHDX není v Azure podporován, pouze **pevný virtuální pevný disk .** Disk můžete převést do pevného formátu VHD pomocí správce technologie Hyper-V nebo rutiny [powershellového převodu vhd.](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) Příklad je následující.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Vytvoření prostředků úložiště a nahrání
Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků s názvem *myResourceGroup* v umístění *eastus:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Chcete-li nahrát virtuální pevný disk, vytvořte si účet úložiště s [vytvořením účtu úložiště az](/cli/azure/storage/account). Názvy účtů úložiště musí být jedinečné, proto zadejte své vlastní jméno. Následující příklad vytvoří účet úložiště s názvem *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Chcete-li řídit přístup k účtu úložiště, získejte klíč úložiště se [seznamem klíčů účtu úložiště az](/cli/azure/storage/account/keys) takto:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Chcete-li logicky oddělit nahrané virtuální disky, vytvořte kontejner v rámci účtu úložiště s [vytvořením kontejneru úložiště az](/cli/azure/storage/container):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Nakonec nahrajte svůj Virtuální pevný disk s [nahráním objektu blob úložiště AZ](/cli/azure/storage/blob) takto:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Vytvoření virtuálního počítače z virtuálního pevného disku
Virtuální ho virtuálního virtuálního ms můžete vytvořit pomocí [ukázkového skriptu](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) nebo přímo pomocí [az vm create](/cli/azure/vm). Chcete-li určit Nahraný Virtuální pevný disk `--image` OpenBSD, použijte parametr následujícím způsobem:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Získejte IP adresu pro váš Virtuální počítač OpenBSD se [seznamem az vm-ip-adres](/cli/azure/vm) takto:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Nyní můžete SSH do svého OpenBSD VM jako normální:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Další kroky
Pokud se chcete dozvědět více o podpoře Hyper-V na OpenBSD6.1, přečtěte si [OpenBSD 6.1](https://www.openbsd.org/61.html) a [hyperv.4](https://man.openbsd.org/hyperv.4).

Pokud chcete vytvořit virtuální hod ze spravovaného disku, přečtěte si [az disk](/cli/azure/disk). 
