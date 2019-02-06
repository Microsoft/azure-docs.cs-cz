---
title: Použít s Linuxem, řešení potíží s virtuálního počítače pomocí rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Informace o řešení potíží virtuální počítač s Linuxem pomocí připojení disku s operačním systémem k obnovení virtuálního počítače pomocí rozhraní příkazového řádku Azure
services: virtual-machines-linux
documentationCenter: ''
authors: genlin
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/16/2017
ms.author: genli
ms.openlocfilehash: 76a29ce05aab39d9460dcf068ec8a7f60d1e8fac
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55753278"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-with-the-azure-cli"></a>Řešení potíží s virtuálního počítače s Linuxem připojením disku s operačním systémem k obnovení virtuálního počítače pomocí Azure CLI
Pokud virtuální počítač s Linuxem (VM), zaznamená chybám spouštění nebo disku, budete muset provést postup řešení potíží na samotném virtuálním pevném disku. Běžným příkladem by byla neplatná položka v `/etc/fstab` virtuální počítač, který brání tomu nebudou moct úspěšně spustil. Tento článek podrobně popisuje, jak připojit virtuální pevný disk k jinému virtuálnímu počítači Linux opravte všechny chyby a pak znovu vytvořit původní virtuální počítač pomocí Azure CLI. 


## <a name="recovery-process-overview"></a>Přehled procesu obnovení
Proces řešení potíží je následující:

1. Odstranění virtuálního počítače k chybám, zachování virtuálních pevných disků.
2. Připojení a připojte virtuální pevný disk k jinému virtuálnímu počítači s Linuxem pro účely odstraňování potíží.
3. Připojení k virtuálnímu počítači pro řešení potíží. Upravení souborů nebo spuštění všech nástrojů k opravě potíží na původním virtuálním pevném disku.
4. Odpojení virtuálního pevného disku od virtuálního počítače pro řešení potíží.
5. Vytvoření virtuálního počítače s použitím původního virtuálního pevného disku.

Pro virtuální počítač, který používá spravovaný disk, najdete v článku [vyřešit spravovaný virtuální počítač Disk připojit nový disk s operačním systémem](#troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk).

Pokud chcete provést tyto kroky pro řešení potíží, je třeba nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index).

V následujících příkladech nahraďte názvy parametrů s vlastními hodnotami. Zahrnout názvy parametrů příklad `myResourceGroup`, `mystorageaccount`, a `myVM`.


## <a name="determine-boot-issues"></a>Určete spouštěcí problémy
Prohlédněte si výstup sériové určit, proč váš virtuální počítač není možné správně spustit. Běžným příkladem jsou neplatná položka v `/etc/fstab`, nebo základní virtuální pevný disk se odstraní nebo přesune.

Získání protokolů spouštění s [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics). Následující příklad získá sériového portu výstup z virtuálního počítače s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:

```azurecli
az vm boot-diagnostics get-boot-log --resource-group myResourceGroup --name myVM
```

Zkontrolujte výstup sériové určit, proč tento virtuální počítač se nedaří spustit. Pokud sériové výstup neposkytuje žádné informace, budete muset zkontrolujte soubory protokolu v `/var/log` až budete mít virtuálního pevného disku připojeného k řešení potíží virtuální počítač.


## <a name="view-existing-virtual-hard-disk-details"></a>Zobrazit podrobnosti o stávající virtuální pevný disk
Než budete moct připojit virtuální pevný disk (VHD) k jinému virtuálnímu počítači, je potřeba identifikovat URI disk s operačním systémem. 

Zobrazení informací o virtuálním počítači s [az vm show](/cli/azure/vm). Použití `--query` příznak extrahovat identifikátor URI pro disk s operačním systémem. Následující příklad získá informace o disku pro virtuální počítač s názvem `myVM` ve skupině prostředků s názvem `myResourceGroup`:

```azurecli
az vm show --resource-group myResourceGroup --name myVM \
    --query [storageProfile.osDisk.vhd.uri] --output tsv
```

Identifikátor URI je podobný **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd**.

## <a name="delete-existing-vm"></a>Odstranění existujícího virtuálního počítače
Virtuální pevné disky a virtuální počítače jsou v Azure dva různé prostředky. Virtuální pevný disk je, kde jsou uloženy samotného operačního systému, aplikací a konfigurace. Virtuální počítač je jenom metadata, která definují velikost nebo umístění a odkazuje na prostředky, jako jsou virtuální pevný disk nebo virtuální síťová karta (NIC). Každý virtuální pevný disk má při připojení k virtuálnímu počítači přiřadí zapůjčení. Přestože datové disky je možné připojovat a odpojovat dokonce i za běhu virtuálního počítače, disk s operačním systémem není možné odpojit, dokud se neodstraní prostředek virtuálního počítače. Zapůjčení se nadále přidružuje disk s operačním systémem v případě virtuálních počítačů i v případě, že je virtuální počítač v zastaveném a uvolněném stavu.

Prvním krokem k obnovení vašeho virtuálního počítače je odstranění samotného prostředku virtuálního počítače. Když odstraníte virtuální počítač, virtuální pevné disky zůstanou ve vašem účtu úložiště. Po odstranění virtuálního počítače, připojit virtuální pevný disk k jinému virtuálnímu počítači odstraňovat potíže a řešit chyby.

Odstranění virtuálního počítače s [az vm delete](/cli/azure/vm). Následující příklad odstraní virtuální počítač s názvem `myVM` ze skupiny prostředků s názvem `myResourceGroup`:

```azurecli
az vm delete --resource-group myResourceGroup --name myVM 
```

Počkejte, dokud virtuální počítač má bylo dokončeno odstraňování než připojit virtuální pevný disk k jinému virtuálnímu počítači. Zapůjčený virtuální pevný disk, který se přidruží k němu virtuální počítač je potřeba uvolnit, než budete moct připojit virtuální pevný disk k jinému virtuálnímu počítači.


## <a name="attach-existing-virtual-hard-disk-to-another-vm"></a>Připojit existující virtuální pevný disk k jinému virtuálnímu počítači
Pro několik dalších kroků použijte jiný virtuální počítač pro účely odstraňování potíží. Připojit existující virtuální pevný disk k tomuto řešení problémů s virtuálnímu počítači na Procházet a upravovat obsah na disku. Tento proces umožňuje opravte případné chyby v konfiguraci, případně si můžete přečíst další aplikace nebo systému souborů protokolu, např. Zvolte nebo vytvořte jiný virtuální počítač určený pro účely odstraňování potíží.

Připojení existujícího virtuálního pevného disku s [az vm unmanaged disku připojit](/cli/azure/vm/unmanaged-disk). Po připojení existující virtuální pevný disk zadejte identifikátor URI na disk, kterou jste získali v předchozím `az vm show` příkazu. Následující příklad připojí k řešení potíží virtuální počítač s názvem existujícího virtuálního pevného disku `myVMRecovery` ve skupině prostředků s názvem `myResourceGroup`:

```azurecli
az vm unmanaged-disk attach --resource-group myResourceGroup --vm-name myVMRecovery \
    --vhd-uri https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd
```


## <a name="mount-the-attached-data-disk"></a>Připojte přídavný datový disk

> [!NOTE]
> Následující příklady jsou podrobně popsané kroky potřebné na virtuální počítač s Ubuntu. Pokud používáte jiné distribuce Linuxu, jako je Red Hat Enterprise Linux nebo SUSE, umístění souborů protokolu a `mount` příkazy mohou být mírně liší. Naleznete v dokumentaci pro vaše konkrétní distribuce pro odpovídající změny v příkazech.

1. Připojte přes SSH k virtuálnímu počítači poradce při potížích s pomocí příslušných přihlašovacích údajů. Pokud tento disk je první datový disk připojený k řešení potíží virtuální počítač, disk je pravděpodobně připojený k `/dev/sdc`. Použití `dmseg` zobrazíte připojené disky:

    ```bash
    dmesg | grep SCSI
    ```

    Výstup se podobá následujícímu příkladu:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    V předchozím příkladu je disk s operačním systémem na `/dev/sda` a dočasný disk k dispozici pro každý virtuální počítač je v `/dev/sdb`. Pokud jste měli více datových disků, měly by být na `/dev/sdd`, `/dev/sde`, a tak dále.

2. Vytvořte adresář připojte existující virtuální pevný disk. Následující příklad vytvoří adresář s názvem `troubleshootingdisk`:

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Pokud máte více oddílů na existující virtuální pevný disk, připojte oddíl povinný. Následující příklad připojí na první primární oddíl `/dev/sdc1`:

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Osvědčeným postupem je připojit datové disky na virtuálních počítačích v Azure s využitím univerzálně jedinečným identifikátorem (UUID) virtuálního pevného disku. Pro tento krátký Poradce při potížích scénář připojení virtuálního pevného disku pomocí identifikátoru UUID je nezbytné. Ale při normálním používání úpravy `/etc/fstab` připojit virtuální pevné disky pomocí místo UUID název zařízení může způsobit selhání spuštění virtuálního počítače.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Oprava problémů na původním virtuálním pevném disku
Pomocí existující virtuální pevný disk připojený můžete teď provádět údržbu a řešení potíží s kroky, podle potřeby. Jakmile vyřešíte problémy, pokračujte následujícími kroky.


## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odpojení původního virtuálního pevného disku
Po vyřešení chyby odpojte Image a odpojit existující virtuální pevný disk z vašeho řešení potíží virtuálního počítače. Virtuální pevný disk s jakýkoli jiný virtuální počítač nelze použít, dokud se neuvolní zapůjčení virtuální pevný disk se připojuje k řešení potíží virtuální počítač.

1. Z relace SSH k řešení potíží virtuální počítač odpojte existující virtuální pevný disk. Nejprve změňte mimo nadřazený adresář pro přípojného bodu:

    ```bash
    cd /
    ```

    Nyní odpojte existující virtuální pevný disk. Následující příklad odpojí zařízení `/dev/sdc1`:

    ```bash
    sudo umount /dev/sdc1
    ```

2. Nyní Odpojte virtuální pevný disk z virtuálního počítače. Ukončete relaci SSH k virtuálnímu počítači řešení potíží. Seznam připojených datových disků k virtuálnímu počítači poradce při potížích s [az vm unmanaged-disk list](/cli/azure/vm/unmanaged-disk). Následující příklad zobrazí seznam datových disků připojených k virtuálnímu počítači s názvem `myVMRecovery` ve skupině prostředků s názvem `myResourceGroup`:

    ```azurecli
    azure vm unmanaged-disk list --resource-group myResourceGroup --vm-name myVMRecovery \
        --query '[].{Disk:vhd.uri}' --output table
    ```

    Poznamenejte si název pro existující virtuální pevný disk. Například název disku s identifikátorem URI z **https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd** je **myVHD**. 

    Odpojení datového disku z virtuálního počítače [nespravovaného virtuálního počítače az-disk detach](/cli/azure/vm/unmanaged-disk). Následující příklad odpojí disk s názvem `myVHD` z virtuálního počítače s názvem `myVMRecovery` v `myResourceGroup` skupina prostředků:

    ```azurecli
    az vm unmanaged-disk detach --resource-group myResourceGroup --vm-name myVMRecovery \
        --name myVHD
    ```


## <a name="create-vm-from-original-hard-disk"></a>Vytvoření virtuálního počítače z původního pevného disku
Vytvoření virtuálního počítače z původního virtuálního pevného disku, použijte [tuto šablonu Azure Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-specialized-vhd). Skutečnou šablonu JSON je na následující odkaz:

- https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-specialized-vhd-new-or-existing-vnet/azuredeploy.json

Šablona nasadí virtuální počítač pomocí identifikátoru URI virtuálního pevného disku z předchozích příkazu. Nasazení šablony s [vytvořit nasazení skupiny pro az](/cli/azure/group/deployment). Zadejte identifikátor URI k původní virtuální pevný disk a pak zadejte typ operačního systému, velikost virtuálního počítače a název virtuálního počítače následujícím způsobem:

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeployment \
  --parameters '{"osDiskVhdUri": {"value": "https://mystorageaccount.blob.core.windows.net/vhds/myVM.vhd"},
    "osType": {"value": "Linux"},
    "vmSize": {"value": "Standard_DS1_v2"},
    "vmName": {"value": "myDeployedVM"}}' \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-specialized-vhd/azuredeploy.json
```

## <a name="re-enable-boot-diagnostics"></a>Opětovné povolení diagnostiky spouštění
Při vytváření virtuálního počítače z existujícího virtuálního pevného disku, nemusí Diagnostika spouštění automaticky povolená. Povolte diagnostiku spouštění pomocí [az Diagnostika spouštění virtuálních počítačů – povolit](/cli/azure/vm/boot-diagnostics). Následující příklad povolí diagnostické rozšíření na virtuální počítač s názvem `myDeployedVM` ve skupině prostředků s názvem `myResourceGroup`:

```azurecli
az vm boot-diagnostics enable --resource-group myResourceGroup --name myDeployedVM
```

## <a name="troubleshoot-a-managed-disk-vm-by-attaching-a-new-os-disk"></a>Řešení potíží s spravovaný virtuální počítač Disk připojením nový disk operačního systému
1. Ovlivněné virtuální počítač zastavte.
2. [Vytvoření snímku spravovaného disku](../linux/snapshot-copy-managed-disk.md) z disku s operačním systémem virtuálního počítače spravovaného disku.
3. [Vytvoření spravovaného disku ze snímku](../scripts/virtual-machines-windows-powershell-sample-create-managed-disk-from-snapshot.md).
4. [Připojení spravovaného disku jako datového disku virtuálního počítače](../windows/attach-disk-ps.md).
5. [Změňte datový disk na disk s operačním systémem z kroku 4](../windows/os-disk-swap.md).

## <a name="next-steps"></a>Další postup
Pokud máte problémy s připojením k virtuálnímu počítači, přečtěte si téma [řešení potíží s připojení SSH k virtuálnímu počítači Azure](troubleshoot-ssh-connection.md). Problémy s přístupem k aplikacím spuštěným na vašem virtuálním počítači, naleznete v tématu [řešit problémy s připojením aplikace na virtuální počítač s Linuxem](troubleshoot-app-connection.md).

