---
title: Nasazení virtuálních počítačů na zařízení GPU Azure Stack Edge pro pomocí skriptu Azure PowerShell
description: Popisuje, jak vytvořit a spravovat virtuální počítače na zařízení Azure Stack Edge pro pomocí skriptu Azure PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: cd877a91c7c082ce7845394f2636857f33b7940f
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578752"
---
# <a name="deploy-vms-on-your-azure-stack-edge-pro-gpu-device-via-azure-powershell-script"></a>Nasazení virtuálních počítačů na zařízení GPU Azure Stack Edge pro pomocí skriptu Azure PowerShell

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

V tomto kurzu se dozvíte, jak vytvořit a spravovat virtuální počítač na zařízení Azure Stack Edge pro pomocí skriptu Azure PowerShell.

## <a name="prerequisites"></a>Požadavky

Než začnete vytvářet a spravovat virtuální počítač na zařízení Azure Stack Edge pro pomocí tohoto skriptu, musíte se ujistit, že jste dokončili požadavky uvedené v následujících krocích:

### <a name="for-azure-stack-edge-pro-device-via-the-local-web-ui"></a>Pro zařízení Azure Stack Edge pro prostřednictvím místního webového uživatelského rozhraní

1. V zařízení Azure Stack Edge pro jste dokončili nastavení sítě, jak je popsáno v části [Krok 1: konfigurace zařízení Azure Stack Edge pro](azure-stack-edge-j-series-connect-resource-manager.md#step-1-configure-azure-stack-edge-pro-device).

2. Bylo povoleno síťové rozhraní pro výpočetní výkon. Tato IP adresa síťového rozhraní se používá k vytvoření virtuálního přepínače pro nasazení virtuálního počítače. Následující kroky vás provedou procesem:

    1. Přejít na **nastavení výpočtů**. Vyberte síťové rozhraní, které budete používat k vytvoření virtuálního přepínače.

        > [!IMPORTANT] 
        > Pro výpočetní výkon můžete nakonfigurovat jenom jeden port.

    2. Povolte výpočetní prostředky v síťovém rozhraní. Azure Stack Edge pro vytvoří a spravuje virtuální přepínač odpovídající tomuto síťovému rozhraní.

3. Vytvořili jste a nainstalovali jste všechny certifikáty na zařízení Azure Stack Edge pro a do důvěryhodného kořenového úložiště vašeho klienta. Postupujte podle postupu popsaného v části [Krok 2: vytvoření a instalace certifikátů](azure-stack-edge-j-series-connect-resource-manager.md#step-2-create-and-install-certificates).

### <a name="for-your-windows-client"></a>Pro klienta Windows

1. V místním webovém uživatelském rozhraní zařízení jste na stránce **síť** definovali virtuální Internet Protocol (VIP) konzistentní služby Azure. Tuto virtuální IP adresu musíte přidat do:

    - Hostitelský soubor na klientovi, nebo,
    - Konfigurace serveru DNS
    
    > [!IMPORTANT]
    > Doporučujeme, abyste změnili konfiguraci serveru DNS pro překlad názvů koncových bodů.

    1. Spusťte **Poznámkový blok** jako správce (k uložení souboru se vyžaduje oprávnění správce) a pak otevřete soubor **hosts** v umístění `C:\Windows\System32\Drivers\etc` .
    
        ![Soubor hostitelů Průzkumníka Windows](media/azure-stack-edge-j-series-connect-resource-manager/hosts-file.png)
    
    2. Do souboru **hostitelů** přidejte následující položky, které nahradíte odpovídajícími hodnotami pro vaše zařízení:
    
        ```
        <Azure consistent services VIP> login.<appliance name>.<DNS domain>
        <Azure consistent services VIP> management.<appliance name>.<DNS domain>
        <Azure consistent services VIP> <storage name>.blob.<appliance name>.<DNS domain>
        ```
        Pro účet úložiště můžete zadat název, který má skript použít později pro vytvoření nového účtu úložiště. Skript nekontroluje, jestli je účet úložiště existující.

    3. Pro referenci použijte následující obrázek. Uložte soubor **hostitelů** .

        ![soubor hostitelů v programu Poznámkový blok](media/azure-stack-edge-j-series-deploy-virtual-machine-cli-python/hosts-screenshot-boxed.png)

2. [Stáhněte si skript prostředí PowerShell](https://aka.ms/ase-vm-powershell) použitý v tomto postupu.

3. Ujistěte se, že je na vašem klientovi Windows spuštěný PowerShell 5,0 nebo novější.

4. Ujistěte se, že `Azure.Storage Module version 4.5.0` je v systému nainstalovaný. Tento modul můžete získat z [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/Azure.Storage/4.5.0). Tento modul nainstalujete tak, že zadáte:

    `Install-Module -Name Azure.Storage -RequiredVersion 4.5.0`

    Verzi nainstalovaného modulu ověříte tak, že zadáte:

    `Get-InstalledModule -name Azure.Storage`

    Chcete-li odinstalovat jakékoli jiné moduly verze, zadejte:

    `Uninstall-Module -Name Azure.Storage`

5. Stáhněte si do svého klienta Windows [AzCopy 10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10#download-azcopy) . Poznamenejte si toto umístění, protože ho předáte jako parametr při spuštění skriptu.

6. Ujistěte se, že je na vašem klientovi Windows spuštěný protokol TLS 1,2 nebo novější.


## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Spusťte PowerShell jako správce.
2. Přejít do složky, do které jste stáhli skript na klienta.  
3. Ke spuštění skriptu použijte následující příkaz:
 
    `.\ArmPowershellClient.ps1 -VNetAddressSpace <AddressSpace> -NicPrivateIp <Private IP> -VHDPath <Path> -VHDFile <VHD File, with extension> -StorageAccountName <Name> -OS <Windows/Linux> -VMSize <Supported VM Size> -VMUserName <UserName to be used to login into VM> -VMPassword <Password for the VM login> --AzCopy10Path <Absolute Path>`

    Tady jsou příklady, kdy se skript spustí pro vytvoření virtuálního počítače s Windows a virtuálního počítače se systémem Linux.

    **Pro virtuální počítač s Windows:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.73 -VHDPath \\intel01\d$\vm_vhds\AzureWindowsVMmode -VHDFile WindowsServer2016Datacenter.vhd -StorageAccountName teaaccount1 -OS Windows -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`

    **Pro virtuální počítač se systémem Linux:**

    `.\ArmPowershellClient.ps1 -VNetAddressSpace 5.5.0.0/16 -NicPrivateIp 5.5.168.83 -VHDPath \\intel01\d$\vm_vhds\AzurestackLinux -VHDFile ubuntu13.vhd -StorageAccountName sa2 -OS Linux -VMSize Standard_D1_v2 -VMUserName Administrator -VMPassword Password1 -AzCopy10Path C:\azcopy10\azcopy.exe`
    
4. Pokud chcete vyčistit prostředky vytvořené skriptem, použijte následující příkazy:
    
    ```powershell
    Get-AzureRmVM | Remove-AzureRmVM -Force
    Get-AzureRmNetworkInterface | Remove-AzureRmNetworkInterface -Force
    Get-AzureRmResource | Remove-AzureRmResource -f
    Get-AzureRmResourceGroup | Remove-AzureRmResourceGroup -f
    ```


## <a name="next-steps"></a>Další kroky

[Nasazení virtuálních počítačů pomocí rutin Azure PowerShell](azure-stack-edge-j-series-deploy-virtual-machine-powershell.md)
