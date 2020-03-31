---
title: Stažení virtuálního pevného disku linuxového systému z Azure
description: Stáhněte si virtuální disk Linuxu pomocí rozhraní příkazového příkazu Azure a portálu Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: 02c3ee483e6a31960fd5123070a49f568ac4c690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968799"
---
# <a name="download-a-linux-vhd-from-azure"></a>Stažení virtuálního pevného disku linuxového systému z Azure

V tomto článku se dozvíte, jak stáhnout soubor virtuálního pevného disku (VHD) Linuxu z Azure pomocí Azure CLI a portálu Azure. 

Pokud jste tak ještě neučinili, nainstalujte [azure cli](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk nelze stáhnout z Azure, pokud je připojený ke spuštěnému virtuálnímu počítači. Chcete-li stáhnout virtuální pevný disk, musíte virtuální ho zastavit. Pokud chcete použít virtuální pevný disk jako [image](tutorial-custom-images.md) k vytvoření dalších virtuálních počítačů s novými disky, je třeba deprovision a generalizovat operační systém obsažený v souboru a zastavit virtuální počítače. Chcete-li použít virtuální pevný disk jako disk pro novou instanci existujícího virtuálního počítače nebo datového disku, stačí zastavit a navrátit virtuální ho.

Chcete-li použít virtuální pevný disk jako image k vytvoření dalších virtuálních virtuálních disek, proveďte následující kroky:

1. Použijte SSH, název účtu a veřejnou IP adresu virtuálního počítačů k němu připojit a zrušení jeho zřízení. Veřejnou IP adresu najdete s [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). Parametr +user také odebere poslední zřízený uživatelský účet. Pokud pečete přihlašovací údaje účtu do virtuálního účtu, vynechejte tento parametr +user. Následující příklad odebere poslední zřízený uživatelský účet:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Přihlaste se ke svému účtu Azure pomocí [az přihlášení](https://docs.microsoft.com/cli/azure/reference-index).
3. Zastavit a navrátit virtuální ho.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Zobecnit virtuální ho. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Chcete-li použít virtuální pevný disk jako disk pro novou instanci existujícího virtuálního počítače nebo datového disku, proveďte tyto kroky:

1.  Přihlaste se k [portálu Azure](https://portal.azure.com/).
2.  V levé nabídce vyberte **Virtuální počítače**.
3.  Vyberte virtuální ho v seznamu.
4.  Na stránce virtuálního počítače vyberte **Zastavit**.

    ![Zastavení virtuálního počítače](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generovat adresu URL SAS

Chcete-li stáhnout soubor VHD, je třeba vygenerovat adresu URL [sdíleného přístupového podpisu (SAS).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Při generování adresy URL je adrese URL přiřazen čas vypršení platnosti.

1.  V nabídce stránky pro virtuální počítač vyberte **disky**.
2.  Vyberte disk operačního systému pro virtuální počítače a pak vyberte **Export disku**.
3.  Vyberte **Generovat adresu URL**.

    ![Generovat adresu URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Stažení VHD

1.  Pod vygenerovanou adresou URL vyberte **Stáhnout soubor VHD**.
**
    ![Stažení VHD](./media/download-vhd/export-download.png)

2.  Ke stažení může být nutné vybrat **možnost Uložit** v prohlížeči. Výchozí název souboru VHD je *abcd*.

    ![Vybrat Uložit v prohlížeči](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Další kroky

- Naučte se, jak [nahrát a vytvořit virtuální počítač s Linuxem z vlastního disku pomocí azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Správa disků Azure v příkazovém příkazovém příkazu k řešení Azure](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

