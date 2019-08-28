---
title: Stažení virtuálního pevného disku se systémem Linux z Azure | Microsoft Docs
description: Stažení virtuálního pevného disku se systémem Linux pomocí rozhraní příkazového řádku Azure a Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 08/21/2019
ms.author: cynthn
ms.openlocfilehash: ed79df03a42c1558b975cd1c21c79716d50d4616
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70083489"
---
# <a name="download-a-linux-vhd-from-azure"></a>Stažení virtuálního pevného disku se systémem Linux z Azure

V tomto článku se dozvíte, jak stáhnout soubor virtuálního pevného disku (VHD) pro Linux z Azure pomocí rozhraní příkazového řádku Azure a Azure Portal. 

Pokud jste to ještě neudělali, nainstalujte rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk se nedá stáhnout z Azure, pokud je připojený ke spuštěnému virtuálnímu počítači. Pro stažení virtuálního pevného disku je nutné zastavit virtuální počítač. Pokud chcete použít virtuální pevný disk jako [Image](tutorial-custom-images.md) k vytvoření dalších virtuálních počítačů s novými disky, musíte zrušit zřízení a zobecnit operační systém obsažený v souboru a zastavit virtuální počítač. Pokud chcete virtuální pevný disk použít jako disk pro novou instanci existujícího virtuálního počítače nebo datového disku, stačí zastavit a zrušit přidělení virtuálního počítače.

Pokud chcete virtuální pevný disk použít jako image k vytvoření dalších virtuálních počítačů, proveďte tyto kroky:

1. K připojení a zrušení zřízení použijte SSH, název účtu a veřejnou IP adresu virtuálního počítače. Veřejnou IP adresu najdete pomocí [AZ Network Public-IP show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). Parametr + uživatel odebere také naposledy zřízený uživatelský účet. Pokud jste k virtuálnímu počítači přihlásili přihlašovací údaje účtu, ponechte tento parametr uživatelem. Následující příklad odebere poslední zřízený účet uživatele:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Přihlaste se ke svému účtu Azure pomocí [AZ Login](https://docs.microsoft.com/cli/azure/reference-index).
3. Zastavte a zrušte přidělení virtuálního počítače.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalizujte virtuální počítač. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Pokud chcete virtuální pevný disk použít jako disk pro novou instanci existujícího virtuálního počítače nebo datového disku, proveďte tyto kroky:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2.  V nabídce vlevo vyberte **Virtual Machines**.
3.  V seznamu vyberte virtuální počítač.
4.  Na stránce pro virtuální počítač vyberte **zastavit**.

    ![Zastavit virtuální počítač](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Vygenerovat adresu URL SAS

Pokud chcete stáhnout soubor VHD, musíte vygenerovat adresu URL [sdíleného přístupového podpisu (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Po vygenerování adresy URL se adresa URL přiřadí čas vypršení platnosti.

1.  V nabídce stránky pro virtuální počítač vyberte **disky**.
2.  Vyberte disk s operačním systémem pro virtuální počítač a pak vyberte **exportovat disk**.
3.  Vyberte **generovat adresu URL**.

    ![Vygenerovat URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Stáhnout VHD

1.  V části vygenerovaná adresa URL vyberte **Stáhnout soubor VHD**.
**
    ![Stáhnout VHD](./media/download-vhd/export-download.png)

2.  Možná budete muset vybrat **Uložit** v prohlížeči a zahájit stahování. Výchozí název souboru VHD je *abcd*.

    ![V prohlížeči vyberte Save (Uložit).](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nahrát a vytvořit virtuální počítač se systémem Linux z vlastního disku pomocí Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Spravujte Azure na discích Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

