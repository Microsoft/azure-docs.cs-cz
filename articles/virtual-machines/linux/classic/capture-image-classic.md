---
title: Vytvořte bitovou kopii virtuálního počítače s Linuxem | Microsoft Docs
description: Zjistěte, jak pro zachycení image systémem Linux Azure virtuálního počítače (VM) vytvořené pomocí modelu nasazení classic.
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 17d7ffee-a58e-4290-9de1-64c3cf1ddc05
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: iainfou
ms.openlocfilehash: 29a1d5bc839fcf533973271638bf283d6e48e93b
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Jak zachytit klasický virtuální počítač s Linuxem jako image
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [provést tento postup pomocí modelu Resource Manageru](../capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Tento článek ukazuje, jak zachytit klasický Azure virtuální počítač (VM) s Linuxem jako bitovou kopii k vytvoření dalších virtuálních počítačů. Tento image obsahuje disk operačního systému a datové disky připojené k virtuálnímu počítači. Konfigurace sítí, neobsahuje, je nutné nakonfigurovat, když vytvoříte další virtuální počítač z bitové kopie.

Obrázek v Azure uloží **bitové kopie**, společně s všechny Image, které jste odeslali. Další informace o bitových kopiích naleznete v tématu [o Image virtuálních počítačů v Azure][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Než začnete
Tento postup předpokládá, že jste vytvořili virtuální počítač Azure pomocí modelu nasazení Classic a nakonfigurovat operačního systému, včetně připojení jakýchkoli datových disků. Pokud potřebujete k vytvoření virtuálního počítače, přečtěte si [jak vytvořit virtuální počítač s Linuxem][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>Zachytit virtuální počítač
1. [Připojte se k Virtuálnímu](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pomocí SSH klienta podle vašeho výběru.
2. V okně SSH zadejte následující příkaz. Výstup z `waagent` může mírně lišit v závislosti na verzi tohoto obslužného nástroje:

    ```bash
    sudo waagent -deprovision+user
    ```

    Předchozí příkaz pokusí vyčistit systému a nastavit jej jako vhodný pro reprovisioning. Tato operace provede následující úlohy:

   * Odebere hostitele klíče SSH (Pokud Provisioning.RegenerateSshHostKeyPair 'y' v konfiguračním souboru)
   * Vymaže konfiguraci názvový server v /etc/resolv.conf
   * Odebere `root` heslo uživatele z/etc/stínové (Pokud Provisioning.DeleteRootPassword 'y' v konfiguračním souboru)
   * Odstraní zapůjčení DHCP klientů uložená v mezipaměti
   * Resetuje název hostitele na localhost.localdomain
   * Odstranění posledního zřízený uživatelský účet (získaný z /var/lib/waagent) **a související data**.

     > [!NOTE]
     > Zrušení zřízení odstraní soubory a data "zobecní" bitovou kopii. Tento příkaz lze spusťte pouze na virtuální počítač, který chcete zaznamenat jako novou šablonu bitové kopie. Není zaručeno, že bitovou kopii vymaže všechny citlivých informací nebo je vhodný pro opětovnou distribuci třetím stranám.

3. Typ **y** pokračujte. Můžete přidat `-force` parametr předejdete tento krok potvrzení.
4. Typ **ukončení** zavřít klienta SSH.

   > [!NOTE]
   > Zbývající kroky předpokládají, že už máte [nainstalované rozhraní příkazového řádku Azure](../../../cli-install-nodejs.md) na klientském počítači. Všechny následující kroky lze provést [portál Azure](http://portal.azure.com).

5. Na klientském počítači otevřete rozhraní příkazového řádku Azure a přihlaste se k předplatnému Azure. Podrobnosti najdete v tématu [připojení k předplatnému Azure z rozhraní příkazového řádku Azure](/cli/azure/authenticate-azure-cli).

   > [!NOTE]
   > Na portálu Azure Přihlaste se k portálu.

6. Ujistěte se, že jste v režimu správy služby:

    ```azurecli
    azure config mode asm
    ```

7. Vypněte virtuální počítač, který je již zrušit. Následující příklad vypne virtuální počítač s názvem `myVM`:

    ```azurecli
    azure vm shutdown myVM
    ```
   V případě potřeby můžete zobrazit seznam všech virtuálních počítačů ve vašem předplatném vytvořily s použitím `azure vm list`

   > [!NOTE]
   > Pokud používáte portál Azure, vyberte virtuální počítač a klikněte na tlačítko **Zastavit** vypnutí virtuálního počítače.

8. Při zastavení virtuálního počítače je zachycení bitové kopie. Následující příklad zaznamená virtuálního počítače s názvem `myVM` a vytváří zobecněný bitovou kopii s názvem `myNewVM`:

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    `-t` Podpříkaz odstraní původní virtuální počítač.

    > [!NOTE]
    > Na portálu Azure můžete zaznamenat bitovou kopii výběrem **Image** v nabídce centra. Budete muset zadat následující informace pro bitovou kopii: název, skupinu prostředků, umístění, typ operačního systému a cestu k úložišti objektů blob.

9. Nová bitová kopie je nyní k dispozici v seznamu bitové kopie, které můžete použít ke konfiguraci žádné nové virtuální počítače. Můžete ji zobrazit pomocí příkazu:

   ```azurecli
   azure vm image list
   ```

   Na [portál Azure](http://portal.azure.com), nová bitová kopie se zobrazí v **Image virtuálních počítačů (klasické)** který patří do **výpočetní** služby. Dostanete **Image virtuálních počítačů (klasické)** kliknutím **všechny služby** v horní části Azure služby seznamu a pak vyhledávání **výpočetní** služby.   

   ![Zachycení Image úspěšné](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Další postup
Obrázek je připraven použít k vytvoření virtuálních počítačů. Můžete použít příkaz příkazového řádku Azure CLI `azure vm create` a zadejte název bitové kopie, které jste vytvořili. Další informace najdete v tématu [pomocí rozhraní příkazového řádku Azure s modelem nasazení Classic](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

Můžete taky použít [portál Azure](http://portal.azure.com) k vytvoření vlastních virtuálních počítačů pomocí **bitové kopie** metoda a vybrat image jste vytvořili. Další informace najdete v tématu [postup vytvoření virtuálního počítače s vlastní][How to Create a Custom Virtual Machine].

**Viz také:** [Azure Linux Agent uživatelská příručka](../agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom-classic.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk-classic.md
[How to Create a Linux Virtual Machine]:create-custom-classic.md
