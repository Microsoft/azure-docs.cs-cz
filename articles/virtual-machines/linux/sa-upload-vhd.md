---
title: Nahrání vlastního linuxového disku pomocí azure cli
description: Vytvoření a nahrání virtuálního pevného disku (VHD) do Azure pomocí modelu nasazení Správce prostředků a rozhraní příkazového příkazového nastavení Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.openlocfilehash: bc90a409dd2695ce16f8c7d5909f8e2d7867673c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060244"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Nahrání a vytvoření virtuálního počítače s Linuxem z vlastního disku pomocí azure cli

Tento článek ukazuje, jak nahrát virtuální pevný disk (VHD) do účtu úložiště Azure s Azure CLI a vytvořit virtuální počítače Linux z tohoto vlastního disku. Tato funkce umožňuje nainstalovat a nakonfigurovat distro Linuxu podle vašich požadavků a pak použít tento virtuální pevný disk k rychlému vytvoření virtuálních počítačů Azure (VM).

Toto téma používá účty úložiště pro konečné virtuální pevné disky, ale můžete také provést tyto kroky pomocí [spravovaných disků](upload-vhd.md). 

## <a name="quick-commands"></a>Rychlé příkazy
Pokud potřebujete rychle provést úlohu, v následující části podrobnosti základní příkazy pro nahrání virtuálního pevného disku do Azure. Podrobnější informace a kontext pro každý krok naleznete ve zbytku dokumentu, [počínaje tímto .](#requirements)

Ujistěte se, že máte nainstalovaný nejnovější [Azure CLI](/cli/azure/install-az-cli2) a přihlášenka k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index).

V následujících příkladech nahraďte názvy příkladů parametrů vlastními hodnotami. Příklady názvů `myResourceGroup` `mystorageaccount`parametrů `mydisks`zahrnuty , a .

Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků `myResourceGroup` v umístění `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Vytvořte účet úložiště pro uložení virtuálních disků s [vytvořením účtu úložiště az](/cli/azure/storage/account). Následující příklad vytvoří účet `mystorageaccount`úložiště s názvem :

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Seznam přístupových klíčů pro váš účet úložiště se [seznamem klíčů účtu úložiště az](/cli/azure/storage/account/keys). Poznamenejte `key1`si:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Vytvořte kontejner v rámci účtu úložiště pomocí klíče úložiště, který jste získali pomocí [az kontejneru úložiště vytvořit](/cli/azure/storage/container). Následující příklad vytvoří kontejner `mydisks` s názvem pomocí `key1`hodnoty klíče úložiště z :

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Nakonec nahrajte virtuální pevný disk do kontejneru, který jste vytvořili s [nahráním objektu blob úložiště AZ](/cli/azure/storage/blob). Zadejte místní cestu k `/path/to/disk/mydisk.vhd`virtuálnímu pevnému disku v části :

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Zadejte identifikátor URI`--image`na disku ( ) pomocí [vytvoření az vm](/cli/azure/vm). Následující příklad vytvoří virtuální `myVM` počítač s názvem pomocí virtuálního disku, který byl dříve nahrán:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Cílový účet úložiště musí být stejný jako tam, kam jste virtuální disk nahráli. Je také nutné zadat nebo odpovědět na výzvy pro všechny další parametry vyžadované **příkazem az vm vytvořit,** jako je virtuální síť, veřejná IP adresa, uživatelské jméno a klíče SSH. Další informace o [dostupných klasických parametrech správce zdrojů rozhraní PŘÍKAZOVÉHO](../azure-cli-arm-commands.md#virtual-machines)ŘÁDKU .

## <a name="requirements"></a>Požadavky
Chcete-li provést následující kroky, potřebujete:

* **Operační systém Linux nainstalovaný v souboru .vhd** – Nainstalujte [linuxovou distribuci schválenou Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (nebo podívejte [se na informace pro neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) na virtuální disk ve formátu Virtuálního pevného disku. Existuje více nástrojů pro vytvoření virtuálního a virtuálního pevného disku:
  * Nainstalujte a nakonfigurujte [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) nebo [KVM](https://www.linux-kvm.org/page/RunningKVM)a dávejte pozor na použití virtuálního pevného disku jako obrazového formátu. V případě potřeby můžete [obrázek převést](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) pomocí `qemu-img convert`aplikace .
  * Hyper-V můžete taky používat [ve Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) nebo [windows server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Novější formát VHDX není v Azure podporován. Při vytváření virtuálního virtuálního montu zadejte jako formát virtuální pevný disk. V případě potřeby můžete převést disky VHDX na virtuální pevný disk pomocí [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) rutiny [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) prostředí PowerShell. Azure navíc nepodporuje nahrávání dynamických virtuálních pevných disků, takže je třeba tyto disky před nahráním převést na statické virtuální disky. Pomocí nástrojů, jako jsou [nástroje Virtuálního pevného disku Azure pro GO,](https://github.com/Microsoft/azure-vhd-utils-for-go) můžete během procesu nahrávání do Azure převést dynamické disky.
> 
> 

* Virtuální počítače vytvořené z vlastního disku musí být umístěny ve stejném účtu úložiště jako samotný disk
  * Vytvoření účtu úložiště a kontejneru pro vlastní disk i vytvořené virtuální počítače
  * Po vytvoření všech virtuálních počítačů můžete disk bezpečně odstranit.

Ujistěte se, že máte nainstalovaný nejnovější [Azure CLI](/cli/azure/install-az-cli2) a přihlášenka k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index).

V následujících příkladech nahraďte názvy příkladů parametrů vlastními hodnotami. Příklady názvů `myResourceGroup` `mystorageaccount`parametrů `mydisks`zahrnuty , a .

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Příprava disku k nahrání
Azure podporuje různé distribuce Linuxu (viz [Schválené distribuce).](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Následující články vás provedou přípravou různých distribucí Linuxu, které jsou podporované v Azure:

* **[Distribuce založené na CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ostatní - neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Další obecné tipy na přípravu ibiz linuxových iobrazů pro Azure najdete také v **[poznámkách](create-upload-generic.md#general-linux-installation-notes)** k instalaci Linuxu.

> [!NOTE]
> Smlouva [SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se vztahuje na virtuální počítače se systémem Linux pouze v případě, že se jedna z schválených distribucí používá s podrobnostmi o konfiguraci, jak je uvedeno v části Podporované verze v Linuxu v [distribucích s podporou Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupiny prostředků logicky sdružují všechny prostředky Azure pro podporu vašich virtuálních počítačů, jako je virtuální síť a úložiště. Další informace skupiny prostředků naleznete v tématu [přehled skupin prostředků](../../azure-resource-manager/management/overview.md). Před nahráním vlastního disku a vytvořením virtuálních počítačů je třeba nejprve vytvořit skupinu prostředků s [vytvořením skupiny AZ](/cli/azure/group).

Následující příklad vytvoří skupinu prostředků `myResourceGroup` v umístění `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořte účet úložiště pro vlastní disk a virtuální počítače s [vytvořením účtu úložiště az](/cli/azure/storage/account). Všechny virtuální počítače s nespravovanými disky, které vytvoříte z vlastního disku, musí být ve stejném účtu úložiště jako tento disk. 

Následující příklad vytvoří účet `mystorageaccount` úložiště pojmenovaný ve skupině prostředků, která byla dříve vytvořena:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Seznam klíčů účtů úložiště
Azure generuje dva 512bitové přístupové klíče pro každý účet úložiště. Tyto přístupové klíče se používají při ověřování účtu úložiště, například k provádění operací zápisu. Další informace o přístupových klíčích účtu úložiště najdete v [tématu Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md). Zobrazíte přístupové klíče se [seznamem klíčů účtu úložiště az](/cli/azure/storage/account/keys).

Zobrazení přístupových klíčů pro účet úložiště, který jste vytvořili:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Výstup je podobný tomuto:

```output
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

Poznamenejte `key1` si to, jak budete používat k interakci s účtem úložiště v dalších krocích.

## <a name="create-a-storage-container"></a>Vytvoření kontejneru úložiště
Stejným způsobem, jakým vytváříte různé adresáře pro logické uspořádání místního systému souborů, vytvoříte kontejnery v rámci účtu úložiště pro uspořádání disků. Účet úložiště může obsahovat libovolný počet kontejnerů. Vytvořte kontejner s [vytvořením kontejneru úložiště az](/cli/azure/storage/container).

Následující příklad vytvoří kontejner `mydisks`s názvem :

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Nahrát virtuální pevný disk
Nyní nahrát vlastní disk s [az úložiště blob nahrát](/cli/azure/storage/blob). Nahrajete a uložíte vlastní disk jako objekt blob stránky.

Zadejte přístupový klíč, kontejner, který jste vytvořili v předchozím kroku, a cestu k vlastnímu disku v místním počítači:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Vytvořte virtuální počítač.
Chcete-li vytvořit virtuální počítač s nespravovanými`--image`disky, zadejte identifikátor URI na disku ( ) pomocí [vytvoření virtuálního počítače AZ](/cli/azure/vm). Následující příklad vytvoří virtuální `myVM` počítač s názvem pomocí virtuálního disku, který byl dříve nahrán:

Parametr zadáte `--image` pomocí [vytvoření az vm](/cli/azure/vm) tak, aby ukazoval na vlastní disk. Ujistěte `--storage-account` se, že odpovídá účtu úložiště, kde je uložen vlastní disk. Není třeba používat stejný kontejner jako vlastní disk pro ukládání virtuálních počítačů. Před nahráním vlastního disku nezapomeňte vytvořit všechny další kontejnery stejným způsobem jako předchozí kroky.

Následující příklad vytvoří virtuální `myVM` počítač pojmenovaný z vlastního disku:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Stále je třeba zadat nebo odpovědět na výzvy pro všechny další parametry vyžadované **příkazem az vm vytvořit,** jako je uživatelské jméno a klíče SSH.


## <a name="resource-manager-template"></a>Šablona Resource Manageru
Šablony Azure Resource Manager jsou soubory Zápisu objektů JavaScriptu (JSON), které definují prostředí, které chcete vytvořit. Šablony jsou rozděleny do různých poskytovatelů prostředků, jako jsou výpočetní prostředky nebo sítě. Můžete použít existující šablony nebo napsat vlastní. Přečtěte si další informace o [používání Správce prostředků a šablon](../../azure-resource-manager/management/overview.md).

V `Microsoft.Compute/virtualMachines` rámci poskytovatele šablony máte `storageProfile` uzel, který obsahuje podrobnosti konfigurace pro váš virtuální počítač. Dva hlavní parametry, které `image` `vhd` je třeba upravit, jsou identifikátory URI, které odkazují na vlastní disk a virtuální disk nového virtuálního počítače. Následující příklad ukazuje příklad JSON pro použití vlastního disku:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

Pomocí této existující šablony můžete [vytvořit virtuální počítač z vlastní bitové kopie](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) nebo si přečíst o vytváření [vlastních šablon Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). 

Jakmile máte nakonfigurovanou šablonu, vytvořte virtuální počítače pomocí [vytvoření nasazení skupiny AZ.](/cli/azure/group/deployment) Zadejte identifikátor URI šablony JSON s parametrem: `--template-uri`

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Pokud máte v počítači uložený soubor JSON, `--template-file` můžete místo toho použít parametr:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Další kroky
Po přípravě a nahrání vlastního virtuálního disku si můžete přečíst další informace o [použití Správce prostředků a šablon](../../azure-resource-manager/management/overview.md). Můžete také přidat [datový disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) do nových virtuálních počítačů. Pokud máte aplikace spuštěné na virtuálních počítačích, ke kterým potřebujete přístup, nezapomeňte [otevřít porty a koncové body](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

