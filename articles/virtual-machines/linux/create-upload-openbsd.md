---
title: Vytvoření a nahrání image virtuálního počítače OpenBSD do Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit a nahrát virtuální pevný disk (VHD), který obsahuje operační systém OpenBSD k vytvoření virtuálního počítače Azure pomocí Azure CLI
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
ms.openlocfilehash: de5d3fcd7eff0042e912e164050f917a0070b2c3
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164640"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Vytvoření a nahrání image disku OpenBSD do Azure
V tomto článku se dozvíte, jak vytvořit a nahrát virtuální pevný disk (VHD), který obsahuje operační systém OpenBSD. Po odeslání, můžete ho použít jako vlastní image k vytvoření virtuálního počítače (VM) v Azure pomocí Azure CLI.


## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že máte následující položky:

* **Předplatné Azure** – Pokud nemáte účet, můžete můžete vytvořit během několika minut. Pokud máte předplatné MSDN, přečtěte si téma [Azure měsíční kredit pro předplatitele sady Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). V opačném případě zjistěte, jak [vytvořit Bezplatný zkušební účet](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure CLI** – Ujistěte se, že máte nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az_login).
* **OpenBSD operační systém nainstalovaný na soubor VHD** – podporované OpenBSD operační systém ([verze 6.1 AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.1/amd64/)) musí být nainstalován na virtuální pevný disk. Postup vytvoření souborů .vhd existují více nástroje. Například můžete použít řešení virtualizace, jako je Hyper-V k vytvoření souboru .vhd a instalace operačního systému. Pokyny o tom, jak nainstalovat a používat technologie Hyper-V najdete v tématu [instalace technologie Hyper-V a vytvoření virtuálního počítače](https://technet.microsoft.com/library/hh846766.aspx).


## <a name="prepare-openbsd-image-for-azure"></a>Příprava OpenBSD image pro Azure
Na virtuálním počítači, kam jste nainstalovali operačního systému OpenBSD 6.1, která přidá Hyper-V podporují, proveďte následující procedury:

1. Pokud je DHCP není povoleno během instalace, povolte službu následujícím způsobem:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Konzoly sériového portu wmm nastavte takto:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Instalace balíčku nakonfigurujte následujícím způsobem:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Ve výchozím nastavení `root` je uživatel zakázán na virtuálních počítačích v Azure. Uživatelé můžou spouštět příkazy se zvýšenými oprávněními pomocí `doas` příkaz na virtuálním počítači OpenBSD. Doas je standardně povolená. Další informace najdete v tématu [doas.conf](http://man.openbsd.org/doas.conf.5). 

5. Nainstalujte a nakonfigurujte požadované součásti pro agenta Azure následujícím způsobem:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Nejnovější verzi agenta služby Azure najdete vždy v [Githubu](https://github.com/Azure/WALinuxAgent/releases). Nainstalujte agenta následujícím způsobem:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Když nainstalujete agenta služby Azure, je vhodné ověřit, že běží následujícím způsobem:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Zrušení zřízení systému, že je vhodný pro neukončil a vyčistit ho. Následující příkaz odstraní také posledního zřízeného uživatelského účtu a přidružená data:

    ```sh
    waagent -deprovision+user -force
    ```

Teď můžete vypnout virtuální počítač.


## <a name="prepare-the-vhd"></a>Příprava virtuálního pevného disku
Formát VHDX nepodporuje v Azure, pouze **oprava virtuálního pevného disku**. Můžete převést disk na pevný formát virtuálního pevného disku pomocí Správce technologie Hyper-V nebo prostředí Powershell [convert-vhd](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) rutiny. Příkladem je jako následující.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Vytvoření prostředků úložiště a odeslat
Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

Pokud chcete nahrát virtuální pevný disk, vytvořte účet úložiště s [vytvořit účet úložiště az](/cli/azure/storage/account#az_storage_account_create). Názvy účtů úložiště musí být jedinečný, takže zadejte vlastní název. Následující příklad vytvoří účet úložiště s názvem *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Pokud chcete řídit přístup k účtu úložiště, získat klíč úložiště s [seznamu klíčů účtu úložiště az](/cli/azure/storage/account/keys#az_storage_account_keys_list) následujícím způsobem:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Logickému oddělení nahrajete virtuální pevné disky, vytvořte kontejner v účtu úložiště pomocí [vytvořit kontejner úložiště az](/cli/azure/storage/container#az_storage_container_create):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Nakonec odešlete svůj virtuální pevný disk s [az storage blob nahrávání](/cli/azure/storage/blob#az_storage_blob_upload) následujícím způsobem:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Vytvoření virtuálního počítače z vašeho virtuálního pevného disku
Můžete vytvořit virtuální počítač s [ukázkový skript](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) nebo přímo s [az vm vytvořit](/cli/azure/vm#az_vm_create). Pokud chcete zadat OpenBSD virtuálního pevného disku, jste nahráli, použijte `--image` parametr následujícím způsobem:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Získejte IP adresu pro váš virtuální počítač OpenBSD s [az vm list-ip-addresses](/cli/azure/vm#list-ip-addresses) následujícím způsobem:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Teď můžete získat přístup přes SSH k vašemu virtuálnímu počítači OpenBSD jako za normálních okolností:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Další postup
Pokud chcete získat další informace o podporu technologie Hyper-V na OpenBSD6.1, přečtěte si [OpenBSD 6.1](https://www.openbsd.org/61.html) a [hyperv.4](http://man.openbsd.org/hyperv.4).

Pokud chcete vytvořit virtuální počítač ze spravovaného disku, přečtěte si [az disk](/cli/azure/disk). 
