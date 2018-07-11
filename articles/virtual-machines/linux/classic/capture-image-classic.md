---
title: Zachycení image virtuálního počítače s Linuxem | Dokumentace Microsoftu
description: Zjistěte, jak zachytit image založené na Linuxu Azure virtuální počítač (VM) vytvořené pomocí modelu nasazení classic.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: ae87af45ffc442c0de6c7f703694a994e536cdb8
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37929208"
---
# <a name="how-to-capture-a-classic-linux-virtual-machine-as-an-image"></a>Jak zachytit klasický virtuální počítač s Linuxem jako image
> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [provést tento postup pomocí modelu Resource Manageru](../capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

Tento článek ukazuje, jak zachytit klasický Azure virtuální počítač (VM) s Linuxem jako image k vytvoření dalších virtuálních počítačů. Tato image obsahuje disk s operačním systémem a datové disky připojené k virtuálnímu počítači. Konfiguraci sítě, neobsahuje, je nutné nakonfigurovat, která při vytváření dalších virtuálních počítačů z image.

Azure ukládá image v rámci **image**, spolu s všech imagí, které jste odeslali. Další informace o imagích najdete v tématu [o Imagí virtuálních počítačů v Azure][About Virtual Machine Images in Azure].

## <a name="before-you-begin"></a>Než začnete
Tento postup předpokládá, že je vytvoření virtuálního počítače Azure pomocí modelu nasazení Classic a konfiguraci operačního systému, včetně připojení všechny datové disky. Pokud potřebujete vytvořit virtuální počítač, přečtěte si [jak vytvořit virtuální počítač s Linuxem][How to Create a Linux Virtual Machine].

## <a name="capture-the-virtual-machine"></a>Zachytit virtuální počítač
1. [Připojte se k virtuálnímu počítači](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) pomocí klienta SSH podle vašeho výběru.
2. V okně SSH zadejte následující příkaz. Výstup z `waagent` může mírně lišit v závislosti na verzi tohoto nástroje:

    ```bash
    sudo waagent -deprovision+user
    ```

    Předchozí příkaz se pokusí vyčistit systému a že je vhodný pro neukončil. Tato operace provede následující úlohy:

   * Odebere klíče hostitele SSH (Pokud Provisioning.RegenerateSshHostKeyPair "y" v konfiguračním souboru)
   * Odstraní konfiguraci názvového serveru v /etc/resolv.conf
   * Odebere `root` heslo uživatele z/etc/stínové (Pokud u Provisioning.DeleteRootPassword hodnotu y' v konfiguračním souboru)
   * Odstraní zapůjčení DHCP klientů uložená v mezipaměti
   * Resetuje název hostitele na localhost.localdomain
   * Odstraní poslední zřízené uživatelský účet (získané z /var/lib/waagent) **a přidružená data**.

     > [!NOTE]
     > Zrušení zřízení odstraní soubory a data "Zobecněte" image. Tento příkaz spusťte jenom na virtuální počítač, který máte v úmyslu zachytit v podobě nové image šablony. To není zaručeno, že na obrázku se vymaže všechny citlivých informací nebo je vhodný pro distribuci třetím stranám.

3. Typ **y** pokračujte. Můžete přidat `-force` parametr, aby tento krok potvrzení.
4. Typ **ukončovací** zavřete klienta SSH.

   > [!NOTE]
   > Zbývající kroky předpokládají, že už máte [nenainstalovali Azure CLI](../../../cli-install-nodejs.md) na klientském počítači. Všechny následující kroky je možné provést [webu Azure portal](http://portal.azure.com).

5. Z klientského počítače otevřete rozhraní příkazového řádku Azure a přihlaste se ke svému předplatnému Azure. Podrobnosti najdete v článku [připojení k předplatnému Azure z příkazového řádku Azure](/cli/azure/authenticate-azure-cli).

   > [!NOTE]
   > Na webu Azure Portal Přihlaste se k portálu.

6. Ujistěte se, že jste v režimu správy služby:

    ```azurecli
    azure config mode asm
    ```

7. Vypněte virtuální počítač, který je již zrušení zřízení. Následující příklad vypne virtuální počítač s názvem `myVM`:

    ```azurecli
    azure vm shutdown myVM
    ```
   V případě potřeby můžete zobrazit seznam všech virtuálních počítačů vytvořené ve vašem předplatném pomocí `azure vm list`

   > [!NOTE]
   > Pokud používáte na webu Azure portal, vyberte virtuální počítač a klikněte na tlačítko **Zastavit** vypnutí virtuálního počítače.

8. Když se zastaví virtuální počítač, zachytíte image. Následující příklad zachycuje virtuální počítač s názvem `myVM` a vytvoří generalizované image s názvem `myNewVM`:

    ```azurecli
    azure vm capture -t myVM myNewVM
    ```

    `-t` Podpříkaz odstraní původní virtuální počítač.

    > [!NOTE]
    > Na webu Azure Portal, můžete zachytit image tak, že vyberete **Image** v nabídce centra. Budete muset zadat následující informace pro image: název, skupinu prostředků, umístění, typ operačního systému a cesta k úložišti objektů blob.

9. Nová bitová kopie je teď dostupná v seznamu imagí, které můžete použít ke konfiguraci libovolné nového virtuálního počítače. Můžete ji zobrazit pomocí příkazu:

   ```azurecli
   azure vm image list
   ```

   Na [webu Azure portal](http://portal.azure.com), nový obrázek se zobrazí v **Image virtuálního počítače (klasické)** , který patří do **Compute** služby. Můžete přistupovat **Image virtuálního počítače (klasické)** kliknutím **všechny služby** v horní části stránky Azure služby seznamu a potom hledání v **Compute** služby.   

   ![Zachycení Image úspěšné](./media/capture-image/VMCapturedImageAvailable.png)

## <a name="next-steps"></a>Další postup
Na obrázku je připravená k použití k vytvoření virtuálních počítačů. Můžete použít příkaz rozhraní příkazového řádku Azure `azure vm create` a zadejte název image, který jste vytvořili. Další informace najdete v tématu [pomocí rozhraní příkazového řádku Azure s modelem nasazení Classic](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2).

Můžete taky použít [webu Azure portal](http://portal.azure.com) k vytvoření vlastního virtuálního počítače pomocí **Image** metoda a výběru image, které jste vytvořili. Další informace najdete v tématu [vytvoření virtuálního počítače s vlastní][How to Create a Custom Virtual Machine].

**Viz také:** [uživatelská příručka k Azure Linux Agent](../../extensions/agent-linux.md)

[About Virtual Machine Images in Azure]:../../virtual-machines-linux-classic-about-images.md
[How to Create a Custom Virtual Machine]:create-custom-classic.md
[How to Attach a Data Disk to a Virtual Machine]:attach-disk-classic.md
[How to Create a Linux Virtual Machine]:create-custom-classic.md
