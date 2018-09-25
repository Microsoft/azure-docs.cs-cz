---
title: Stáhněte si VHD s Linuxem v Azure | Dokumentace Microsoftu
description: Stáhněte si VHD s Linuxem pomocí Azure CLI a webu Azure portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
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
ms.openlocfilehash: 5f269f074236beef3e213c888e540bcf18238be1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46953697"
---
# <a name="download-a-linux-vhd-from-azure"></a>Stáhněte si VHD s Linuxem z Azure

V tomto článku se dozvíte, jak stáhnout [Linux virtuálního pevného disku (VHD)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) souborů z Azure pomocí Azure CLI a webu Azure portal. 

Pokud jste tak již neučinili, nainstalujte [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk nejde stáhnout z Azure, pokud je připojen k spuštěného virtuálního počítače. Budete muset zastavit virtuální počítač ke stažení virtuálního pevného disku. Pokud chcete použít jako virtuální pevný disk [image](tutorial-custom-images.md) k vytvoření dalších virtuálních počítačů pomocí nové disky, budete muset zrušit zřízení a generalizace operační systém obsažený v souboru a zastavte virtuální počítač. Použití virtuálního pevného disku jako disku pro nové instance existujícího virtuálního počítače nebo datový disk, potřebujete jenom zastavit a zrušit přidělení virtuálního počítače.

Chcete-li použít virtuální pevný disk jako image k vytvoření dalších virtuálních počítačů, postupujte takto:

1. Pomocí SSH, název účtu a veřejnou IP adresu virtuálního počítače se připojte k němu a zrušení jeho zřízení. Můžete najít veřejnou IP adresu pomocí [az network public-ip show](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). + Parametr uživatele také odebere posledního zřízeného uživatelského účtu. Pokud jsou pečení přihlašovací údaje k virtuálnímu počítači, nechte si to + parametr uživatele. Následující příklad odebere posledního zřízeného uživatelského účtu:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Přihlaste se ke svému účtu Azure pomocí [az login](https://docs.microsoft.com/cli/azure/reference-index#az_login).
3. Zastavit a zrušit přidělení virtuálního počítače.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalizace virtuálního počítače. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Použití virtuálního pevného disku jako disku pro nové instance existujícího virtuálního počítače nebo datový disk, proveďte tyto kroky:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2.  V nabídce centra klikněte na **Virtuální počítače**.
3.  Vyberte virtuální počítač ze seznamu.
4.  V okně pro virtuální počítač, klikněte na **Zastavit**.

    ![Zastavení virtuálního počítače](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Vygenerovat adresu SAS URL

Stáhněte soubor virtuálního pevného disku, budete muset vygenerovat [sdílený přístupový podpis (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) adresy URL. Při generování adresy URL, čas vypršení platnosti je přiřazena k adrese URL.

1.  V nabídce v okně pro virtuální počítač, klikněte na tlačítko **disky**.
2.  Vyberte disk s operačním systémem virtuálního počítače a potom klikněte na tlačítko **exportovat**.
3.  Klikněte na tlačítko **generování adresy URL**.

    ![Generování adresy URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Stažení virtuálního pevného disku

1.  V části Adresa URL, která byla vygenerována klikněte na stáhnout soubor VHD.

    ![Stažení virtuálního pevného disku](./media/download-vhd/export-download.png)

2.  Budete muset kliknout na **Uložit** v prohlížeči a spusťte soubor ke stažení. Výchozí název pro soubor virtuálního pevného disku je *abcd*.

    ![Klikněte na Uložit v prohlížeči](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [nahrání a vytvoření virtuálního počítače s Linuxem z vlastního disku pomocí rozhraní příkazového řádku Azure](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Správa disků v Azure Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

