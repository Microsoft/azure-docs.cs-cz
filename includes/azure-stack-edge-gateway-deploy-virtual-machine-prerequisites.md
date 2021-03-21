---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 71d5a910e36762d096763c4f45a13cbdad47414d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101730614"
---
Než budete moct nasadit virtuální počítače na Azure Stack hraniční zařízení, musíte klienta nakonfigurovat tak, aby se připojil k zařízení přes Azure Resource Manager přes Azure PowerShell. Podrobné pokyny najdete v tématu [připojení k Azure Resource Manager na zařízení Azure Stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Ujistěte se, že k přístupu k zařízení z klienta můžete použít následující postup. Tuto konfiguraci jste už provedli, když jste se připojili k Azure Resource Manager a teď ověříte, že konfigurace byla úspěšná. 

1. Spuštěním následujícího příkazu ověřte, že Azure Resource Manager komunikace funguje:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Pokud chcete volat rozhraní API místních zařízení k ověření, zadejte: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Pokud se chcete připojit prostřednictvím Azure Resource Manager, zadejte uživatelské jméno *EdgeARMuser* a heslo.

1. Pokud jste nakonfigurovali COMPUTE pro Kubernetes, můžete tento krok přeskočit. V opačném případě se ujistěte, že jste povolili síťové rozhraní pro výpočetní prostředky, a to následujícím způsobem: 

   a. V místním uživatelském rozhraní přejdete na nastavení **výpočtů** .  
   b. Vyberte síťové rozhraní, které chcete použít k vytvoření virtuálního přepínače. Virtuální počítače, které vytvoříte, budou připojené k virtuálnímu přepínači připojenému k tomuto portu a přidružené síti. Ujistěte se, že zvolíte síť, která odpovídá IP adrese, kterou použijete pro virtuální počítač.  

    ![Snímek obrazovky s oknem nastavení sítě COMPUTE Configuration.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. V části **Povolit pro výpočty** v síťovém rozhraní vyberte **Ano**. Azure Stack Edge vytvoří a bude spravovat virtuální přepínač, který odpovídá tomuto síťovému rozhraní. Nezadávejte v tuto chvíli konkrétní IP adresy pro Kubernetes. Povolení výpočetní kapacity může trvat několik minut.

    > [!NOTE]
    > Pokud vytváříte virtuální počítače GPU, vyberte síťové rozhraní, které je připojené k Internetu. V takovém případě vám umožní nainstalovat na zařízení rozšíření GPU.


