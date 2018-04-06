---
title: Zachycení virtuálního počítače Azure Linux chcete použít jako šablonu | Microsoft Docs
description: Informace o zaznamenání a generalize bitové kopie založené na systému Linux Azure virtuálního počítače (VM) vytvořené pomocí modelu nasazení Azure Resource Manager.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: e608116f-f478-41be-b787-c2ad91b5a802
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 71c60c8d29e4db8aab1932a1bece03396a12e4da
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="capture-a-linux-virtual-machine-running-on-azure"></a>Zachytit virtuální počítač Linux spuštěné v Azure
Postupujte podle kroků v tomto článku generalize a zachycení Azure Linux virtuálního počítače (VM) v modelu nasazení Resource Manager. Při průchodu generalize virtuálního počítače, můžete odebrat informace o osobní účet a připravit virtuální počítač, který se má použít jako obrázek. Můžete potom zachycení bitové kopie zobecněný virtuální pevný disk (VHD) pro operační systém, virtuální pevné disky pro připojené datových disků, a [šablony Resource Manageru](../../azure-resource-manager/resource-group-overview.md) pro nová nasazení virtuálních počítačů. Tento článek popisuje, jak zachytit image virtuálního počítače s 1.0 rozhraní příkazového řádku Azure pro virtuální počítač pomocí nespravované disků. Můžete také [zachytit virtuální počítač Azure spravované disky pomocí Azure CLI 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Spravované disky jsou zpracovávány platformy Azure a nevyžadují, aby všechny přípravné nebo umístění pro uložení. Další informace najdete v tématu [Přehled služby Azure Managed Disks](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Pokud chcete vytvořit virtuální počítače pomocí bitové kopie, nastavit síťovým prostředkům pro každý nový virtuální počítač a nasazení ze zaznamenané bitové kopie virtuálního pevného disku pomocí šablony (soubor JavaScript Object Notation nebo formát JSON,). Tímto způsobem můžete replikovat virtuální počítač s aktuální konfigurace softwaru, podobně jako pomocí bitové kopie v Azure Marketplace.

> [!TIP]
> Pokud chcete vytvořit kopii existující virtuální počítač Linux s jejím specializované stavu pro zálohování nebo ladění, přečtěte si téma [vytvoření kopie virtuálního počítače Linux spuštěné v Azure](copy-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). A pokud chcete nahrát Linux virtuální pevný disk z virtuálního počítače místní, přečtěte si téma [nahrát a vytvoření virtuálního počítače s Linuxem z bitové kopie disku vlastní](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).  

## <a name="cli-versions-to-complete-the-task"></a>Verze rozhraní příkazového řádku pro dokončení úlohy
K dokončení úlohy můžete využít jednu z následujících verzí rozhraní příkazového řádku:

- [Azure CLI 1.0](#before-you-begin) – naše rozhraní příkazového řádku pro classic a resource správu modelech nasazení (v tomto článku)
- [Azure CLI 2.0](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) – naše rozhraní příkazového řádku nové generace pro model nasazení správy prostředků

## <a name="before-you-begin"></a>Než začnete
Ujistěte se, že splňujete následující požadavky:

* **Virtuální počítač Azure vytvořené v modelu nasazení Resource Manager** – Pokud jste nevytvořili virtuální počítač s Linuxem, můžete použít [portál](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), [rozhraní příkazového řádku Azure](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), nebo [šablony Resource Manageru ](create-ssh-secured-vm-from-template.md). 
  
    Podle potřeby nakonfigurujte virtuální počítač. Například [přidat datových disků](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), aktualizace a instalovat aplikace. 
* **Rozhraní příkazového řádku Azure** -nainstalovat [rozhraní příkazového řádku Azure](../../cli-install-nodejs.md) v místním počítači.

## <a name="step-1-remove-the-azure-linux-agent"></a>Krok 1: Odebrání agenta Azure Linux
Nejprve spustit **příkaz waagent** s **deprovision** parametr na virtuální počítač s Linuxem. Tento příkaz odstraní všechny soubory a data tak, aby virtuální počítač připraven na generalizací. Podrobnosti najdete v tématu [Azure Linux Agent uživatelská příručka](../windows/agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

1. Připojte k virtuálním počítačům s Linuxem pomocí klienta SSH.
2. V okně SSH zadejte následující příkaz:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Tento příkaz lze spusťte pouze na virtuální počítač, který máte v úmyslu bitovou kopii. Není zaručeno, že bitovou kopii vymaže všechny citlivých informací nebo je vhodný pro opětovnou distribuci.
 
3. Typ **y** pokračujte. Můžete přidat **-force** parametr předejdete tento krok potvrzení.
4. Po dokončení příkazu, zadejte **ukončete**. Tento krok zavře použije klient SSH.

## <a name="step-2-capture-the-vm"></a>Krok 2: Zachycení virtuálního počítače
Pomocí rozhraní příkazového řádku Azure generalize a zachycení virtuálního počítače. V následujících příkladech nahraďte názvy parametrů příklad vlastní hodnoty. Zahrnout názvy parametrů příklad **myResourceGroup**, **myVnet**, a **Můjvp**.

1. Z místního počítače, otevřete rozhraní příkazového řádku Azure a [přihlášení k předplatnému Azure](/cli/azure/authenticate-azure-cli). 
2. Ujistěte se, že jste v režimu Resource Manager.
   
    ```azurecli
    azure config mode arm
    ```
3. Vypněte virtuální počítač, který již zrušit pomocí následujícího příkazu:
   
    ```azurecli
    azure vm deallocate -g myResourceGroup -n myVM
    ```
4. Generalize virtuálního počítače pomocí následujícího příkazu:
   
    ```azurecli
    azure vm generalize -g myResourceGroup -n myVM
    ```
5. Teď spustit **zachycení virtuálního počítače azure** příkaz, který zachycuje virtuálního počítače. V následujícím příkladu, bitovou kopii virtuální pevné disky jsou zachyceny s názvy počínaje **MyVHDNamePrefix**a **-t** možnost určuje cestu k šabloně **MyTemplate.json**. 
   
    ```azurecli
    azure vm capture -g myResourceGroup -n myVM -p myVHDNamePrefix -t myTemplate.json
    ```
   
   > [!IMPORTANT]
   > Soubory VHD image vytvářeny ve výchozím nastavení ve stejném účtu úložiště, který používá původní virtuální počítač. Použití *stejný účet úložiště* k ukládání virtuálních pevných disků pro všechny nové virtuální počítače, můžete vytvořit z image. 

6. Najít umístění zaznamenané bitové kopie, otevřete šablonu JSON v textovém editoru. V **storageProfile**, Najít **uri** z **image** umístěný v **systému** kontejneru. Například je podobná identifikátor URI bitové kopie disku operačního systému `https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Krok 3: Vytvoření virtuálního počítače ze zaznamenané bitové kopie
Chcete-li vytvořit virtuální počítač s Linuxem teď použijte bitovou kopii pomocí šablony. Tyto kroky ukazují, jak používat rozhraní příkazového řádku Azure a šablona souboru JSON, kterou zachycené vytvořte virtuální počítač v nové virtuální sítě.

### <a name="create-network-resources"></a>Vytvoření síťových prostředků
Abyste mohli použít šablonu, musíte nejprve nastavit virtuální sítě a síťovou kartu pro nový virtuální počítač. Doporučujeme že vytvořit skupinu prostředků pro tyto prostředky v umístění, kde jsou uložené vaše image virtuálního počítače. Příkazy spouštějte podobná následující, nahraďte názvy pro vaše prostředky a příslušné umístění Azure ("centralus" v těchto příkazech):

```azurecli
azure group create myResourceGroup1 -l "centralus"

azure network vnet create myResourceGroup1 myVnet -l "centralus"

azure network vnet subnet create myResourceGroup1 myVnet mySubnet

azure network public-ip create myResourceGroup1 myPublicIP -l "centralus"

azure network nic create myResourceGroup1 myNIC -k mySubnet -m myVnet -p myPublicIP -l "centralus"
```

### <a name="get-the-id-of-the-nic"></a>Získání Id síťového adaptéru
Pokud chcete nasadit virtuální počítač z bitové kopie pomocí JSON, který jste uložili během zachycení, je třeba Id na síťový adaptér. Ho můžete získejte spuštěním následujícího příkazu:

```azurecli
azure network nic show myResourceGroup1 myNIC
```

**Id** ve výstupu je podobná `/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic`

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače
Nyní spusťte následující příkaz pro vytvoření virtuálního počítače ze zaznamenané bitové kopie virtuálního počítače. Použití **-f** parametru určete cestu k souboru šablony JSON jste uložili.

```azurecli
azure group deployment create myResourceGroup1 MyDeployment -f MyTemplate.json
```

Ve výstupu příkazu zobrazí se výzva k zadejte nový název virtuálního počítače, uživatelské jméno správce a heslo a Id síťového adaptéru, který jste předtím vytvořili.

```bash
info:    Executing command group deployment create
info:    Supply values for the following parameters
vmName: myNewVM
adminUserName: myAdminuser
adminPassword: ********
networkInterfaceId: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resource Groups/myResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
```

Následující příklad ukazuje, co vidíte pro úspěšné nasazení:

```bash
+ Initializing template configurations and parameters
+ Creating a deployment
info:    Created template deployment xxxxxxx
+ Waiting for deployment to complete
data:    DeploymentName     : MyDeployment
data:    ResourceGroupName  : MyResourceGroup1
data:    ProvisioningState  : Succeeded
data:    Timestamp          : xxxxxxx
data:    Mode               : Incremental
data:    Name                Type          Value

data:    ------------------  ------------  -------------------------------------

data:    vmName              String        myNewVM

data:    vmSize              String        Standard_D1

data:    adminUserName       String        myAdminuser

data:    adminPassword       SecureString  undefined

data:    networkInterfaceId  String        /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/MyResourceGroup1/providers/Microsoft.Network/networkInterfaces/myNic
info:    group deployment create command OK
```

### <a name="verify-the-deployment"></a>Ověření nasazení
Nyní SSH k virtuálnímu počítači, který jste vytvořili pro ověření nasazení a začít používat nový virtuální počítač. Pro připojení pomocí protokolu SSH, najít IP adresu virtuálního počítače, které jste vytvořili pomocí následujícího příkazu:

```azurecli
azure network public-ip show myResourceGroup1 myPublicIP
```

Veřejná IP adresa je uvedena ve výstupu příkazu. Ve výchozím nastavení připojení k virtuálního počítače s Linuxem pomocí SSH na port 22.

## <a name="create-additional-vms"></a>Vytvoření dalších virtuálních počítačů
Pomocí zaznamenané bitové kopie a šablony můžete nasadit další virtuální počítače pomocí kroků v předchozím oddílu. Další možnosti, jak vytvořit virtuální počítače z image zahrnout pomocí šabloně pro rychlý start nebo spuštěné **vytvoření virtuálního počítače azure** příkaz.

### <a name="use-the-captured-template"></a>Použití zaznamenané šablony
Zaznamenané bitové kopie a šablony, postupujte podle těchto kroků (popsané v předchozí části):

* Zajistěte, aby vaše image virtuálního počítače ve stejném účtu úložiště, který je hostitelem virtuálního pevného disku Virtuálního počítače.
* Zkopírujte soubor šablony JSON a zadejte jedinečný název pro disk operačního systému nový virtuální počítač virtuálního pevného disku (nebo virtuální pevné disky). Například v **storageProfile**v části **virtuálního pevného disku**v **uri**, zadejte jedinečný název pro **osDisk** virtuálního pevného disku, podobně jako `https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`
* Vytvořte síťový adaptér ve stejné nebo jiné virtuální síti.
* Ve skupině prostředků, ve kterém můžete nastavit virtuální sítě pomocí souboru JSON změny šablony, vytvořte nasazení.

### <a name="use-a-quickstart-template"></a>Použít šabloně pro rychlý start
Pokud chcete nastavit automaticky při vytváření virtuálního počítače z image sítě, můžete tyto prostředky v šabloně. Například najdete v článku [101-vm z – uživatele – image šablony](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) z Githubu. Tato šablona vytvoří virtuální počítač pomocí vlastní image a potřebná virtuální sítě, veřejnou IP adresu a Síťových prostředků. Návod, pomocí šablony na portálu Azure, najdete v části [postup vytvoření virtuálního počítače z vlastní image pomocí šablony Resource Manageru](http://codeisahighway.com/how-to-create-a-virtual-machine-from-a-custom-image-using-an-arm-template/).

### <a name="use-the-azure-vm-create-command"></a>Příkaz vytváření použít virtuální počítač azure
Obvykle je to nejjednodušší provádět vytvořit virtuální počítač z bitové kopie pomocí šablony Resource Manageru. Však můžete vytvořit virtuální počítač *imperativní* pomocí **vytvoření virtuálního počítače azure** s **-Q** (**– image urn**) parametr. Pokud použijete tuto metodu, můžete předat také **-d** (**vhd disk operačního systému –**) parametr k určení umístění souboru VHD operačního systému pro nový virtuální počítač. Tento soubor musí být v kontejneru virtuální pevné disky účtu úložiště, kde je uložen soubor bitové kopie virtuálního pevného disku. Příkaz zkopíruje virtuálního pevného disku pro nový virtuální počítač automaticky **virtuální pevné disky** kontejneru.

Dřív, než spustíte **vytvoření virtuálního počítače azure** Image, proveďte následující kroky:

1. Vytvořte skupinu prostředků nebo identifikovat existující skupinu prostředků pro nasazení.
2. Vytvořte prostředek veřejné IP adresy a Síťových prostředků pro nový virtuální počítač. Pokyny k vytvoření virtuální sítě, veřejnou IP adresu a síťový adaptér pomocí rozhraní příkazového řádku najdete v části výše v tomto článku. (**vytvoření virtuálního počítače azure** můžete také vytvořit síťovou kartu, ale je třeba předat další parametry pro virtuální síť a podsíť.)

Spusťte příkaz, který předává identifikátory URI do nového souboru virtuálního pevného disku operačního systému a existující bitová kopie. V tomto příkladu je vytvořen s velikostí virtuálních počítačů Standard_A1 v oblasti Východ USA.

```azurecli
azure vm create -g myResourceGroup1 -n myNewVM -l eastus -y Linux \
-z Standard_A1 -u myAdminname -p myPassword -f myNIC \
-d "https://xxxxxxxxxxxxxx.blob.core.windows.net/vhds/MyNewVHDNamePrefix.vhd" \
-Q "https://xxxxxxxxxxxxxx.blob.core.windows.net/system/Microsoft.Compute/Images/vhds/MyVHDNamePrefix-osDisk.vhd"
```

Další příkaz Možnosti spustit `azure help vm create`.

## <a name="next-steps"></a>Další postup
Pokud chcete spravovat virtuální počítače pomocí rozhraní příkazového řádku, najdete v části úlohy v [nasadit a spravovat virtuální počítače pomocí šablony Azure Resource Manager a rozhraní příkazového řádku Azure](create-ssh-secured-vm-from-template.md).

