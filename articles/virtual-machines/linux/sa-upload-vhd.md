---
title: Nahrání vlastního disku pro Linux pomocí Azure CLI
description: Vytvoření a nahrání virtuálního pevného disku (VHD) do Azure s využitím modelu nasazení Správce prostředků a Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.custom: storage accounts
ms.openlocfilehash: dcc7c69809ae623606bd091821c5f2fc661f6c8b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088746"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Nahrání a vytvoření virtuálního počítače se systémem Linux z vlastního disku pomocí Azure CLI

V tomto článku se dozvíte, jak nahrát virtuální pevný disk (VHD) do účtu služby Azure Storage pomocí Azure CLI a vytvořit virtuální počítače se systémem Linux z tohoto vlastního disku. Tato funkce umožňuje nainstalovat a nakonfigurovat Linux distribuce podle vašich požadavků a pak pomocí tohoto virtuálního pevného disku rychle vytvořit virtuální počítače Azure.

Toto téma používá účty úložiště pro finální virtuální pevné disky, ale tyto kroky můžete provést také pomocí [spravovaných disků](upload-vhd.md). 

## <a name="quick-commands"></a>Rychlé příkazy
Pokud potřebujete úkol rychle provést, následující část podrobně popisuje základní příkazy pro nahrání VHD do Azure. Podrobnější informace a kontext pro každý krok najdete ve zbývající části dokumentu, který [začíná tady](#requirements).

Ujistěte se, že máte nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlášení k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnují `myResourceGroup` , `mystorageaccount` a `mydisks` .

Nejdřív vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří skupinu prostředků `myResourceGroup` v umístění `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Vytvořte účet úložiště pro ukládání virtuálních disků pomocí [AZ Storage Account Create](/cli/azure/storage/account). Následující příklad vytvoří účet úložiště s názvem `mystorageaccount` :

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Seznam přístupových klíčů pro účet úložiště pomocí [seznamu AZ Storage Account Keys list](/cli/azure/storage/account/keys). Poznamenejte si `key1` :

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Vytvořte kontejner v rámci svého účtu úložiště pomocí klíče úložiště, který jste získali pomocí [AZ Storage Container Create](/cli/azure/storage/container). Následující příklad vytvoří kontejner s názvem `mydisks` pomocí hodnoty klíče úložiště z `key1` :

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Nakonec nahrajte virtuální pevný disk do kontejneru, který jste vytvořili pomocí příkazu [AZ Storage BLOB upload](/cli/azure/storage/blob). V části zadejte místní cestu k virtuálnímu pevnému disku `/path/to/disk/mydisk.vhd` :

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Zadejte identifikátor URI k disku ( `--image` ) pomocí [AZ VM Create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem `myVM` pomocí dříve odeslaného virtuálního disku:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Cílový účet úložiště musí být stejný jako místo, kam jste nahráli virtuální disk. Je také nutné zadat nebo odpovědět na výzvu pro všechny další parametry vyžadované příkazem **AZ VM Create** , jako je například virtuální síť, veřejná IP adresa, uživatelské jméno a klíč SSH. Můžete si přečíst další informace o [dostupných parametrech rozhraní příkazového řádku classic správce prostředků](../azure-cli-arm-commands.md#virtual-machines).

## <a name="requirements"></a>Požadavky
K provedení následujících kroků potřebujete:

* **Operační systém Linux nainstalovaný v souboru. VHD** – nainstalujte [distribuci Linux schválené pro Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (nebo si přečtěte [informace pro neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) na virtuální disk ve formátu VHD. Existuje několik nástrojů, které slouží k vytvoření virtuálního počítače a virtuálního pevného disku:
  * Nainstalujte a nakonfigurujte [qemu](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) nebo [KVM](https://www.linux-kvm.org/page/RunningKVM), přičemž se ujistěte, že jako formát obrázku použijete VHD. V případě potřeby můžete [obrázek převést](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) pomocí `qemu-img convert` .
  * Můžete také použít Hyper-V [ve Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) nebo [Windows Serveru 2012/2012 R2](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

> [!NOTE]
> Novější formát VHDX se v Azure nepodporuje. Když vytváříte virtuální počítač, zadejte jako formát VHD. V případě potřeby můžete disky VHDX převést na VHD pomocí [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) [`Convert-VHD`](/powershell/module/hyper-v/convert-vhd?view=win10-ps) rutiny nebo PowerShellu. Kromě toho Azure nepodporuje nahrávání dynamických virtuálních pevných disků, takže před odesláním musíte tyto disky převést na statické virtuální pevné disky. Pomocí nástrojů, jako jsou například [nástroje Azure VHD](https://github.com/Microsoft/azure-vhd-utils-for-go) Tools, můžete v průběhu nahrávání do Azure převést dynamické disky.
> 
> 

* Virtuální počítače vytvořené z vašeho vlastního disku se musí nacházet ve stejném účtu úložiště jako samotný disk.
  * Vytvořte účet úložiště a kontejner, který bude obsahovat jak svůj vlastní disk, tak i vytvořené virtuální počítače.
  * Po vytvoření všech virtuálních počítačů můžete disk bezpečně odstranit.

Ujistěte se, že máte nainstalované nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlášení k účtu Azure pomocí [AZ Login](/cli/azure/reference-index).

V následujících příkladech nahraďte příklady názvů parametrů vlastními hodnotami. Příklady názvů parametrů zahrnují `myResourceGroup` , `mystorageaccount` a `mydisks` .

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>Příprava disku, který se má nahrát
Azure podporuje různé distribuce systému Linux (viz [schválené distribuce](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). Následující články vás seznámí s postupem přípravy různých distribucí systému Linux, které jsou podporovány v Azure:

* **[Distribuce založené na CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES a openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Jiné neschválené distribuce](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Další obecné tipy k přípravě imagí pro Linux pro Azure najdete také v **[poznámkách k instalaci systému Linux](create-upload-generic.md#general-linux-installation-notes)** .

> [!NOTE]
> [Smlouva SLA platformy Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) se vztahuje na virtuální počítače se systémem Linux jenom v případě, že se jedno z označených distribucí používá s podrobnostmi konfigurace, jak je uvedené v části podporovaná verze v [Linuxech v distribucích schválených pro Azure](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků
Skupiny prostředků logicky přinášejí všechny prostředky Azure za účelem podpory vašich virtuálních počítačů, jako jsou virtuální sítě a úložiště. Další informace o skupinách prostředků najdete v tématu [Přehled skupin prostředků](../../azure-resource-manager/management/overview.md). Než nahrajete vlastní disk a vytvoříte virtuální počítače, musíte nejdřív vytvořit skupinu prostředků pomocí [AZ Group Create](/cli/azure/group).

Následující příklad vytvoří skupinu prostředků `myResourceGroup` v umístění `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>vytvořit účet úložiště

Vytvořte účet úložiště pro vlastní disk a virtuální počítače pomocí [AZ Storage Account Create](/cli/azure/storage/account). Všechny virtuální počítače s nespravovanými disky, které vytvoříte z vlastního disku, se musí nacházet ve stejném účtu úložiště jako tento disk. 

Následující příklad vytvoří účet úložiště s názvem `mystorageaccount` ve skupině prostředků, která byla dříve vytvořena:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Výpis klíčů účtu úložiště
Azure vygeneruje 2 512 přístupové klíče pro každý účet úložiště. Tyto přístupové klíče se používají při ověřování účtu úložiště, například k provádění operací zápisu. Další informace o přístupových klíčích účtu úložiště najdete v tématu [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md). Přístupové klíče zobrazíte pomocí [seznamu AZ Storage Account Keys](/cli/azure/storage/account/keys).

Zobrazte přístupové klíče pro účet úložiště, který jste vytvořili:

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

Poznamenejte si, `key1` jak ho budete používat k interakci s účtem úložiště v následujících krocích.

## <a name="create-a-storage-container"></a>Vytvoření kontejneru úložiště
Stejným způsobem, jakým vytvoříte různé adresáře pro logickou organizaci místního systému souborů, vytvoříte kontejnery v rámci účtu úložiště, abyste mohli své disky uspořádat. Účet úložiště může obsahovat libovolný počet kontejnerů. Vytvořte kontejner pomocí [AZ Storage Container Create](/cli/azure/storage/container).

Následující příklad vytvoří kontejner s názvem `mydisks` :

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>Nahrát VHD
Nyní nahrajte vlastní disk pomocí programu [AZ Storage BLOB upload](/cli/azure/storage/blob). Svůj vlastní disk nahráváte a ukládáte jako objekt blob stránky.

Zadejte přístupový klíč, kontejner, který jste vytvořili v předchozím kroku, a pak cestu k vlastnímu disku v místním počítači:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>Vytvoření virtuálního počítače
Pokud chcete vytvořit virtuální počítač s nespravovanými disky, zadejte identifikátor URI k disku ( `--image` ) pomocí [AZ VM Create](/cli/azure/vm). Následující příklad vytvoří virtuální počítač s názvem `myVM` pomocí dříve odeslaného virtuálního disku:

`--image`Parametr pomocí [AZ VM Create](/cli/azure/vm) určíte tak, aby odkazoval na vlastní disk. Zajistěte, aby `--storage-account` odpovídaly účtu úložiště, ve kterém je uložený vlastní disk. Nemusíte používat stejný kontejner jako vlastní disk pro ukládání virtuálních počítačů. Před nahráním vlastního disku nezapomeňte vytvořit další kontejnery stejným způsobem jako v předchozích krocích.

Následující příklad vytvoří virtuální počítač s názvem `myVM` z vašeho vlastního disku:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

Stále musíte zadat nebo zodpovědět výzvy pro všechny další parametry vyžadované příkazem **AZ VM Create** , jako je uživatelské jméno a klíče SSH.


## <a name="resource-manager-template"></a>Šablona Resource Manageru
Šablony Azure Resource Manager jsou soubory JavaScript Object Notation (JSON), které definují prostředí, které chcete sestavit. Šablony jsou rozdělené do různých poskytovatelů prostředků, jako jsou výpočetní prostředky nebo síť. Můžete použít existující šablony nebo napsat vlastní. Přečtěte si další informace o [použití Správce prostředků a šablon](../../azure-resource-manager/management/overview.md).

V rámci `Microsoft.Compute/virtualMachines` poskytovatele šablony máte `storageProfile` uzel, který obsahuje podrobnosti o konfiguraci pro váš virtuální počítač. Dva hlavní parametry, které je potřeba upravit `image` `vhd` , jsou identifikátory URI a, které odkazují na váš vlastní disk a virtuální disk nového virtuálního počítače. V následujícím příkladu vidíte příklad formátu JSON pro použití vlastního disku:

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

Pomocí [této existující šablony můžete vytvořit virtuální počítač z vlastní image](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) nebo si přečtěte informace o [vytváření vlastních šablon Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). 

Po nakonfigurování šablony použijte příkaz [AZ Group Deployment Create](/cli/azure/group/deployment) a vytvořte své virtuální počítače. Zadejte identifikátor URI šablony JSON s `--template-uri` parametrem:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Pokud máte soubor JSON uložený místně na svém počítači, můžete `--template-file` místo toho použít parametr:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Další kroky
Po přípravě a nahrání vlastního virtuálního disku si můžete přečíst další informace o [používání Správce prostředků a šablon](../../azure-resource-manager/management/overview.md). Na nové virtuální počítače můžete také [přidat datový disk](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) . Pokud máte aplikace spuštěné na virtuálních počítačích, ke kterým potřebujete přístup, nezapomeňte [otevřít porty a koncové body](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
