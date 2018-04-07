---
title: Vytvořit a odeslat bitovou kopii virtuálního počítače OpenBSD do Azure | Microsoft Docs
description: Zjistěte, jak vytvořit a odeslat virtuální pevný disk (VHD), který obsahuje operační systém OpenBSD k vytvoření virtuálního počítače Azure pomocí rozhraní příkazového řádku Azure
services: virtual-machines-linux
documentationcenter: ''
author: thomas1206
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 1ef30f32-61c1-4ba8-9542-801d7b18e9bf
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: huishao
ms.openlocfilehash: 23ca965b12afc8931a67d32e3a11a4aa9777a5d5
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Vytvořit a odeslat bitovou kopii disku OpenBSD do Azure
Tento článek ukazuje, jak vytvořit a odeslat virtuální pevný disk (VHD), který obsahuje OpenBSD operační systém. Po odeslání, můžete ho použít jako vlastní image pro vytvoření virtuálního počítače (VM) v Azure pomocí rozhraní příkazového řádku Azure.


## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte následující položky:

* **Předplatné Azure** – Pokud nemáte účet, můžete vytvořit jeden si během několika minut. Pokud máte předplatné MSDN, najdete v části [platební měsíční Azure pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Jinak, zjistěte, jak [vytvořit Bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure CLI 2.0** – Ujistěte se, že máte nejnovější [Azure CLI 2.0](/cli/azure/install-azure-cli) nainstalován a přihlášení k účtu Azure s [az přihlášení](/cli/azure/reference-index#az_login).
* **OpenBSD operační systém nainstalovaný v souboru VHD** -podporovaný operační systém OpenBSD (verze 6.1) musí být nainstalována na virtuální pevný disk. Postup vytvoření souborů VHD existovat několik nástrojů. Řešení virtualizace, jako je například technologie Hyper-V můžete například použít k vytvoření souboru VHD a instalace operačního systému. Pokyny o tom, jak nainstalovat a používat technologie Hyper-V najdete v tématu [instalaci technologie Hyper-V a vytvoření virtuálního počítače](http://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Příprava OpenBSD image pro Azure
Na virtuálním počítači, kam jste nainstalovali operační systém OpenBSD 6.1, která přidá technologie Hyper-V podporují, proveďte následující postupy:

1. Pokud během instalace není povolený DHCP, povolte službu následujícím způsobem:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Nastavení konzoly sériového portu následujícím způsobem:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Instalace balíčku nakonfigurujte následujícím způsobem:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Ve výchozím nastavení `root` je uživatel zakázán na virtuálních počítačích v Azure. Uživatelé mohou spouštět příkazy se zvýšeným oprávněním pomocí `doas` příkaz OpenBSD virtuálního počítače. Doas je ve výchozím nastavení povolené. Další informace najdete v tématu [doas.conf](http://man.openbsd.org/doas.conf.5). 

5. Nainstalujte a nakonfigurujte požadavky pro Azure Agent následujícím způsobem:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Nejnovější verzi agenta Azure vždy najdete na [Githubu](https://github.com/Azure/WALinuxAgent/releases). Nainstalujte agenta následujícím způsobem:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Po instalaci agenta Azure, je vhodné ověřit, zda je spuštěna následujícím způsobem:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Zrušení zřízení systému a vyčistit ho a nastavit jej jako vhodný pro reprovisioning. Následující příkaz taky odstraní poslední zřízené uživatelský účet a přidružená data:

    ```sh
    waagent -deprovision+user -force
    ```

Nyní můžete vypnout virtuální počítač.


## <a name="prepare-the-vhd"></a>Příprava virtuálního pevného disku
Formát VHDX není podporován v Azure, pouze **pevný virtuální pevný disk**. Můžete převést disk na pevnou Formát virtuálního pevného disku pomocí Správce technologie Hyper-V nebo prostředí Powershell [convert-VHD prostředí](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) rutiny. Příkladem je jako následující.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Vytvořit prostředky úložiště a odeslat
Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Pokud chcete nahrát svůj disk VHD, vytvořit účet úložiště s [vytvořit účet úložiště az](/cli/azure/storage/account#az_storage_account_create). Názvy účtů úložiště musí být jedinečný, takže zadejte vlastní název. Následující příklad vytvoří účet úložiště s názvem *můj_účet_úložiště*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Chcete-li řídit přístup k účtu úložiště, získat klíč úložiště s [seznam klíčů účtu úložiště az](/cli/azure/storage/account/keys#az_storage_account_keys_list) následujícím způsobem:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Logicky jednotlivé virtuální pevné disky můžete nahrávat na server, vytvořit kontejner v rámci účtu úložiště s [vytvořit kontejner úložiště az](/cli/azure/storage/container#az_storage_container_create):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Nakonec nahrát svůj disk VHD s [az úložiště objektů blob nahrávání](/cli/azure/storage/blob#az_storage_blob_upload) následujícím způsobem:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Vytvoření virtuálního počítače z vaší virtuálního pevného disku
Můžete vytvořit virtuální počítač s [ukázkový skript](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) nebo přímo pomocí [vytvořit virtuální počítač az](/cli/azure/vm#az_vm_create). Pokud chcete zadat OpenBSD virtuální pevný disk, který jste nahráli, použijte `--image` parametr následujícím způsobem:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Získat IP adresu pro virtuální počítač OpenBSD s [az virtuálních počítačů seznamu ip-addresses](/cli/azure/vm#list-ip-addresses) následujícím způsobem:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Nyní můžete SSH k virtuálnímu počítači OpenBSD jako za normálních okolností:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Další postup
Pokud chcete získat další informace o podporu technologie Hyper-V na OpenBSD6.1, přečtěte si [OpenBSD 6.1](https://www.openbsd.org/61.html) a [hyperv.4](http://man.openbsd.org/hyperv.4).

Pokud chcete vytvořit virtuální počítač ze spravovaných disků, přečtěte si [disku az](/cli/azure/disk). 
