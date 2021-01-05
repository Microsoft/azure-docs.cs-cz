---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 12/21/2020
ms.author: alkohli
ms.openlocfilehash: f2443765ecc9116193cefbc729ced25fa5657e59
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763420"
---
Než budete moct nasadit virtuální počítače na Azure Stack hraniční zařízení, musíte klienta nakonfigurovat tak, aby se připojil k zařízení přes Azure Resource Manager přes Azure PowerShell. Podrobný postup najdete [v tomto zařízení v Azure Stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md), kde se můžete připojit k Azure Resource Manager.


Ujistěte se, že se k přístupu k zařízení z klienta dají použít tyto kroky (Tato konfigurace jste provedli při připojování k Azure Resource Manager, právě ověříte, že konfigurace byla úspěšná.): 

1. Ověřte, zda Azure Resource Manager komunikace funguje. Zadejte:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Pro ověření zavolejte rozhraní API místních zařízení. Zadejte: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Zadejte uživatelské jméno – *EdgeARMuser* a heslo pro připojení prostřednictvím Azure Resource Manager.

1. Pokud jste nakonfigurovali **COMPUTE** pro Kubernetes, můžete tento krok přeskočit. Pokračujte a ujistěte se, že jste povolili síťové rozhraní pro výpočetní výkon. V místní uživatelské rozhraní přejít na nastavení **výpočtů** . Vyberte síťové rozhraní, které budete používat k vytvoření virtuálního přepínače. Virtuální počítače, které vytvoříte, budou připojené k virtuálnímu přepínači připojenému k tomuto portu a přidružené síti. Ujistěte se, že zvolíte síť, která odpovídá IP adrese, kterou použijete pro virtuální počítač.  

    ![Povolit výpočetní nastavení 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Povolte výpočetní prostředky v síťovém rozhraní. Azure Stack Edge vytvoří a bude spravovat virtuální přepínač odpovídající tomuto síťovému rozhraní. Nezadávejte v tuto chvíli konkrétní IP adresy pro Kubernetes. Povolení výpočetní kapacity může trvat několik minut.

    > [!NOTE]
    > Pokud vytváříte virtuální počítače GPU, vyberte síťové rozhraní připojené k Internetu. To vám umožní nainstalovat na zařízení rozšíření GPU.


1. Povolte roli virtuálních počítačů z Azure Portal. Tento krok vytvoří jedinečné předplatné pro vaše zařízení, které se používá k vytváření virtuálních počítačů prostřednictvím místních rozhraní API daného zařízení. 

    1. Pokud chcete povolit roli virtuálních počítačů, v Azure Portal přejít na prostředek Azure Stack Edge pro zařízení Azure Stack Edge. Přejít na **> výpočtů na Edge Virtual Machines**.

        ![Přidat image virtuálního počítače 1](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-1.png)

    1. Vyberte **Virtual Machines** pro přechod na stránku **Přehled** . **Povolte** správu cloudu virtuálních počítačů.
        ![Přidat image virtuálního počítače 2](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-portal/add-virtual-machine-image-2.png)