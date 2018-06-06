---
title: Stáhnout Linux virtuální pevný disk z Azure | Microsoft Docs
description: Stáhněte si Linux virtuální pevný disk pomocí rozhraní příkazového řádku Azure a webu Azure portal.
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
ms.openlocfilehash: 1c6751d980a7bb28e58a3aa00514411959f515d7
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34725862"
---
# <a name="download-a-linux-vhd-from-azure"></a>Stáhnout Linux virtuální pevný disk z Azure

V tomto článku se dozvíte, jak stáhnout [Linux virtuální pevný disk (VHD)](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) soubor z Azure pomocí rozhraní příkazového řádku Azure a portálu Azure. 

Pokud jste tak již neučinili, nainstalujte [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2).

## <a name="stop-the-vm"></a>Zastavení virtuálního počítače

Virtuální pevný disk nelze stáhnout ze služby Azure, pokud je připojen k spuštění virtuálního počítače. Budete muset zastavit virtuální počítač ke stažení virtuální pevný disk. Pokud chcete použít jako virtuální pevný disk [image](tutorial-custom-images.md) vytvoření dalších virtuálních počítačů pomocí nové disky, budete muset zrušit jejich zřízení a zobecní operační systém obsažený v souboru a zastavte virtuální počítač. Pokud chcete použít virtuální pevný disk jako disk pro novou instanci třídy na existující virtuální počítač nebo datový disk, stačí k zastavení a zrušit přidělení virtuálního počítače.

Chcete-li použít virtuální pevný disk jako bitovou kopii k vytvoření dalších virtuálních počítačů, proveďte tyto kroky:

1. Použijte k připojení k němu a zrušit jejich zřízení se SSH, název účtu a veřejnou IP adresu virtuálního počítače. Můžete najít veřejnou IP adresu s [az sítě veřejné ip zobrazit](https://docs.microsoft.com/cli/azure/network/public-ip#az-network-public-ip-show). + Uživatele parametr také odebere poslední účet zřízení uživatele. Pokud jsou pečení přihlašovací údaje k virtuálnímu počítači, nechte si to + parametr uživatele. Následující příklad odebere poslední účet zřízení uživatele:

    ```bash
    ssh azureuser@<publicIpAddress>
    sudo waagent -deprovision+user -force
    exit 
    ```

2. Přihlaste se k účtu Azure s [az přihlášení](https://docs.microsoft.com/cli/azure/reference-index#az_login).
3. Zastavte a zrušit přidělení virtuálního počítače.

    ```azurecli
    az vm deallocate --resource-group myResourceGroup --name myVM
    ```

4. Generalize virtuálního počítače. 

    ```azurecli
    az vm generalize --resource-group myResourceGroup --name myVM
    ``` 

Pokud chcete použít virtuální pevný disk jako disk pro novou instanci třídy na existující virtuální počítač nebo datový disk, proveďte tyto kroky:

1.  Přihlaste se k webu [Azure Portal](https://portal.azure.com/).
2.  V nabídce centra klikněte na **Virtuální počítače**.
3.  Vyberte virtuální počítač ze seznamu.
4.  V okně pro virtuální počítač, klikněte na **Zastavit**.

    ![Zastavit virtuální počítač](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>Generování adresy URL SAS

Ke stažení souboru virtuálního pevného disku, je nutné generovat [sdílený přístupový podpis (SAS)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) adresy URL. Generování adresy URL čas vypršení platnosti je přiřazena k adrese URL.

1.  V nabídce v okně pro virtuální počítač, klikněte na tlačítko **disky**.
2.  Vyberte disk operačního systému pro virtuální počítač a pak klikněte na tlačítko **exportovat**.
3.  Klikněte na tlačítko **generování adresy URL**.

    ![Generování adresy URL](./media/download-vhd/export-generate.png)

## <a name="download-vhd"></a>Stáhnout virtuálního pevného disku

1.  V části Adresa URL, která byla vygenerována klikněte na tlačítko Stáhnout soubor VHD.

    ![Stáhnout virtuálního pevného disku](./media/download-vhd/export-download.png)

2.  Je třeba kliknout na **Uložit** v prohlížeči zahájíte stahování. Výchozí název souboru virtuálního pevného disku je *abcd*.

    ![Kliknutím na Uložit v prohlížeči](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>Další postup

- Zjistěte, jak [odesílání a vytvoření virtuálního počítače s Linuxem z vlastní disk s Azure CLI 2.0](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- [Správa Azure disků Azure CLI](tutorial-manage-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

