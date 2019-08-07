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
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 5639571739f3eb6263f62444e7ab02186e2ca945
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742585"
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
2.  V nabídce centra klikněte na **Virtuální počítače**.
3.  V seznamu vyberte virtuální počítač.
4.  V okně pro virtuální počítač klikněte na **zastavit**.

    ![Zastavit virtuální počítač](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Vygenerovat adresu URL SAS

Pokud chcete stáhnout soubor VHD, musíte vygenerovat adresu URL [sdíleného přístupového podpisu (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) . Po vygenerování adresy URL se adresa URL přiřadí čas vypršení platnosti.

1.  V nabídce okna pro virtuální počítač klikněte na **disky**.
2.  Vyberte disk s operačním systémem pro virtuální počítač a klikněte na **exportovat disk**.
3.  Klikněte na **vygenerovat adresu URL**.

    ![Vygenerovat URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Stáhnout VHD

1.  Pod vygenerovanou adresou URL klikněte na Stáhnout soubor VHD.

    ![Stáhnout VHD](./media/download-vhd/export-download.png)

2.  Možná budete muset kliknutím na **Uložit** v prohlížeči spustit stahování. Výchozí název souboru VHD je *abcd*.

    ![V prohlížeči klikněte na Uložit.](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Další postup

- Přečtěte si, jak [nahrát a vytvořit virtuální počítač se systémem Linux z vlastního disku pomocí Azure CLI](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Spravujte Azure na discích Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

