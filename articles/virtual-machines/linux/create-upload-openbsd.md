---
title: Vytvoření a nahrání image virtuálního počítače s OpenBSD do Azure
description: Zjistěte, jak vytvořit a nahrát virtuální pevný disk (VHD), který obsahuje operační systém OpenBSD k vytvoření virtuálního počítače Azure pomocí rozhraní příkazového řádku Azure.
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: ee15836906eef0b9205691f9a6003cea0b9fae80
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74036449"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Vytvoření a nahrání image OpenBSD disku do Azure
V tomto článku se dozvíte, jak vytvořit a nahrát virtuální pevný disk (VHD), který obsahuje operační systém OpenBSD. Po nahrání ji můžete použít jako vlastní image k vytvoření virtuálního počítače v Azure prostřednictvím rozhraní příkazového řádku Azure CLI.


## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že máte následující položky:

* **Předplatné Azure** – Pokud účet nemáte, můžete si ho vytvořit během několika minut. Pokud máte předplatné MSDN, podívejte se na [měsíční kredit Azure pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). V opačném případě se dozvíte, jak [vytvořit bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure CLI** – Ujistěte se, že máte nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-azure-cli) a přihlásili jste se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).
* **OpenBSD operační systém nainstalovaný v souboru. VHD** – na virtuální pevný disk musí být nainstalovaný podporovaný operační systém OpenBSD ([6,2 verze amd64](https://ftp.openbsd.org/pub/OpenBSD/6.2/amd64/)). Pro vytváření souborů. VHD existuje víc nástrojů. Například můžete použít virtualizační řešení, jako je Hyper-V, a vytvořit soubor. VHD a nainstalovat operační systém. Pokyny k instalaci a používání technologie Hyper-V najdete v tématu [instalace technologie Hyper-v a vytvoření virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Příprava image OpenBSD pro Azure
Na virtuálním počítači, na který jste nainstalovali operační systém OpenBSD 6,1, který přidal podporu technologie Hyper-V, proveďte následující postupy:

1. Pokud během instalace není protokol DHCP povolený, povolte službu následujícím způsobem:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Pomocí následujícího postupu nastavte konzolu sériového portu:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Nakonfigurujte instalaci balíčku následujícím způsobem:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Ve výchozím nastavení je uživatel `root` na virtuálních počítačích v Azure zakázaný. Uživatelé můžou spouštět příkazy se zvýšenými oprávněními pomocí příkazu `doas` na virtuálním počítači s OpenBSD. DOAs je ve výchozím nastavení povolená. Další informace najdete v tématu [DOAs. conf](https://man.openbsd.org/doas.conf.5). 

5. Požadavky na agenta Azure nainstalujte a nakonfigurujte následujícím způsobem:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Nejnovější verzi agenta Azure můžete vždycky najít na [GitHubu](https://github.com/Azure/WALinuxAgent/releases). Nainstalujte agenta následujícím způsobem:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Po instalaci agenta Azure je vhodné ověřit, že je spuštěný takto:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Zrušením zřízení systému ho vyčistěte a zpřístupněte pro opětovné zřízení. Následující příkaz odstraní také naposledy zřízený uživatelský účet a přidružená data:

    ```sh
    waagent -deprovision+user -force
    ```

Nyní můžete virtuální počítač vypnout.


## <a name="prepare-the-vhd"></a>Příprava virtuálního pevného disku
Formát VHDX není v Azure podporovaný, jenom **pevný virtuální pevný disk**. Disk můžete převést na pevný formát VHD pomocí Správce technologie Hyper-V nebo rutiny [Convert-VHD](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) prostředí PowerShell. Příklad je následující.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Vytvoření prostředků úložiště a nahrání
Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Pokud chcete nahrát virtuální pevný disk, vytvořte účet úložiště pomocí [AZ Storage Account Create](/cli/azure/storage/account). Názvy účtů úložiště musí být jedinečné, takže zadejte vlastní název. Následující příklad vytvoří účet úložiště s názvem *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Pokud chcete řídit přístup k účtu úložiště, Získejte klíč úložiště pomocí příkazu [AZ Storage Account Keys list](/cli/azure/storage/account/keys) takto:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Pokud chcete logicky oddělit virtuální pevné disky, které nahráváte, vytvořte kontejner v rámci účtu úložiště pomocí [AZ Storage Container Create](/cli/azure/storage/container):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Nakonec nahrajte virtuální pevný disk pomocí příkazu [AZ Storage BLOB upload](/cli/azure/storage/blob) následujícím způsobem:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Vytvoření virtuálního počítače z virtuálního pevného disku
Virtuální počítač můžete vytvořit pomocí [ukázkového skriptu](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) nebo přímo pomocí [AZ VM Create](/cli/azure/vm). K určení nahraného virtuálního pevného disku OpenBSD použijte parametr `--image` následujícím způsobem:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Získejte IP adresu pro virtuální počítač s OpenBSD pomocí příkazu [AZ VM list-IP-addresss](/cli/azure/vm) následujícím způsobem:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Nyní můžete SSH na virtuální počítač s OpenBSD jako normální:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Další kroky
Pokud se chcete dozvědět více o podpoře technologie Hyper-V na OpenBSD 6.1, přečtěte si [OpenBSD 6,1](https://www.openbsd.org/61.html) a [HyperV. 4](https://man.openbsd.org/hyperv.4).

Pokud chcete vytvořit virtuální počítač ze spravovaného disku, přečtěte si [AZ disk](/cli/azure/disk). 
