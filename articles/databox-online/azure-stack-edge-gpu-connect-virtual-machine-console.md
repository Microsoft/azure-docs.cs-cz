---
title: Připojte se ke konzole virtuálního počítače na zařízení s GRAFICKÝm rozhraním Azure Stack Edge pro.
description: Popisuje, jak se připojit k konzole virtuálního počítače na VIRTUÁLNÍm počítači běžícím na zařízení s grafickým procesorem Azure Stack Edge pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2021
ms.author: alkohli
ms.openlocfilehash: 68cf157a512e9b1f6caee4734869c5bb4b836e2f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962566"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Připojení k konzole virtuálního počítače na zařízení s GRAFICKÝm rozhraním Azure Stack Edge pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Řešení Azure Stack Edge pro GPU spouští nekontejnerové úlohy prostřednictvím virtuálních počítačů. Tento článek popisuje, jak se připojit ke konzole virtuálního počítače nasazeného na vašem zařízení. 

Konzola virtuálního počítače umožňuje přístup k virtuálním počítačům pomocí funkcí klávesnice, myši a obrazovky s využitím běžně dostupných nástrojů vzdálené plochy. Ke konzole nástroje můžete získat přístup a vyřešit případné problémy, které se vyskytly při nasazování virtuálního počítače do vašeho zařízení. K konzole virtuálního počítače se můžete připojit i v případě, že se váš virtuální počítač nepovedlo zřídit.


## <a name="workflow"></a>Pracovní postup

Pracovní postup nejvyšší úrovně zahrnuje následující kroky:

1. Připojte se k rozhraní PowerShell na svém zařízení.
1. Povolte přístup konzoly k virtuálnímu počítači.
1. Připojte se k virtuálnímu počítači pomocí protokolu RDP (Remote Desktop Protocol).
1. Odvolejte přístup ke konzole k virtuálnímu počítači.

## <a name="prerequisites"></a>Požadavky

Než začnete, ujistěte se, že jste dokončili následující požadavky:

#### <a name="for-your-device"></a>Pro vaše zařízení

Měli byste mít přístup k aktivovanému zařízení GRAFICKÉho rozhraní Azure Stack Edge pro. Zařízení musí mít nasazený jeden nebo víc virtuálních počítačů. Virtuální počítače můžete nasadit prostřednictvím Azure PowerShell, prostřednictvím šablon nebo prostřednictvím Azure Portal.

#### <a name="for-client-accessing-the-device"></a>Pro klientský přístup k zařízení

Ujistěte se, že máte přístup ke klientskému systému, který:

- Má přístup k rozhraní PowerShellu zařízení. Na klientovi je spuštěný [podporovaný operační systém](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device).
- Na klientovi běží PowerShell 7,0 nebo novější. Tato verze PowerShellu funguje pro klienty se systémy Windows, Mac a Linux. Viz pokyny pro [instalaci powershellu 7,0](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true).
- Má funkce vzdálené plochy. V závislosti na tom, jestli používáte Windows, macOS nebo Linux, byste měli nainstalovat jednoho z těchto [klientů vzdálené plochy](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Tento článek poskytuje pokyny ke službě [Vzdálená plocha systému Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client) a [FreeRDP](https://www.freerdp.com/). <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>Připojit ke konzole virtuálních počítačů

Pomocí těchto kroků se připojte k konzole virtuálního počítače na svém zařízení.

### <a name="connect-to-the-powershell-interface-on-your-device"></a>Připojení k rozhraní PowerShellu na zařízení

Prvním krokem je [připojení k rozhraní PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) vašeho zařízení. 

### <a name="enable-console-access-to-the-vm"></a>Povolit přístup konzoly k virtuálnímu počítači

1.  V prostředí PowerShell spusťte následující příkaz, který povolí přístup k konzole virtuálních počítačů.

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. V ukázkovém výstupu si poznamenejte ID virtuálního počítače. Budete ho potřebovat pro pozdější krok.

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Nyní můžete použít klienta vzdálené plochy pro připojení k konzole virtuálního počítače.

#### <a name="use-windows-remote-desktop"></a>Použití vzdálené plochy systému Windows

1. Vytvořte nový textový soubor a zadejte následující text.

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. Uložte soubor jako **. RDP* v klientském systému. Tento profil použijete k připojení k virtuálnímu počítači.
1. Dvojím kliknutím na profil se připojte k virtuálnímu počítači. Zadejte následující přihlašovací údaje:

    - **Uživatelské jméno**: Přihlaste se jako EdgeARMUser.
    - **Heslo**: Zadejte místní heslo Azure Resource Manager pro vaše zařízení. Pokud jste zapomněli heslo, [resetujte Azure Resource Manager heslo přes Azure Portal](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal). 

#### <a name="use-freerdp"></a>Použití FreeRDP

Pokud používáte FreeRDP v klientském počítači se systémem Linux, spusťte následující příkaz: 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>Odvolání přístupu ke konzole virtuálních počítačů

Pokud chcete odvolat přístup ke konzole virtuálních počítačů, vraťte se do prostředí PowerShell vašeho zařízení. Spusťte následující příkaz:

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
Tady je příklad výstupu:

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> Doporučujeme, abyste po dokončení používání konzoly virtuálního počítače buď odvolali přístup, nebo zavřete okno PowerShellu a ukončíte relaci. 

## <a name="next-steps"></a>Další kroky

- Řešení [Azure Stack Edge pro](azure-stack-edge-gpu-troubleshoot.md) v Azure Portal.