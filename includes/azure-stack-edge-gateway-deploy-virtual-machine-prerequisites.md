---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 8f3031669723cd61715c12a42f99869ac0eaf3bc
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500180"
---
Než budete moct nasadit virtuální počítače na Azure Stack hraniční zařízení, musíte klienta nakonfigurovat tak, aby se připojil k zařízení přes Azure Resource Manager přes Azure PowerShell. Podrobný postup najdete [v tomto zařízení v Azure Stack Edge](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md), kde se můžete připojit k Azure Resource Manager.

Ujistěte se, že se k přístupu k zařízení z klienta dají použít následující kroky. (Tuto konfiguraci jste dokončili při připojování k Azure Resource Manager. Nyní právě ověřujete, že konfigurace byla úspěšná.) 

1. Ověřte, zda Azure Resource Manager komunikace funguje. Zadejte:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. Pro ověření zavolejte rozhraní API místních zařízení. Zadejte: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Zadejte uživatelské jméno – *EdgeARMuser* a heslo pro připojení prostřednictvím Azure Resource Manager.

1. Pokud jste nakonfigurovali **COMPUTE** pro Kubernetes, můžete tento krok přeskočit. Pokračujte a ujistěte se, že jste povolili síťové rozhraní pro výpočetní výkon. V místním uživatelském rozhraní přejít na nastavení **výpočtů** . Vyberte síťové rozhraní, které chcete použít k vytvoření virtuálního přepínače. Virtuální počítače, které vytvoříte, budou připojené k virtuálnímu přepínači připojenému k tomuto portu a přidružené síti. Ujistěte se, že zvolíte síť, která odpovídá IP adrese, kterou použijete pro virtuální počítač.  

    ![Snímek obrazovky, který ukazuje, jak povolit nastavení výpočtů](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    Povolte výpočetní prostředky v síťovém rozhraní. Azure Stack Edge vytvoří a bude spravovat virtuální přepínač odpovídající tomuto síťovému rozhraní. Nezadávejte v tuto chvíli konkrétní IP adresy pro Kubernetes. Povolení výpočetní kapacity může trvat několik minut.

    > [!NOTE]
    > Pokud vytváříte virtuální počítače GPU, vyberte síťové rozhraní připojené k Internetu. To vám umožní nainstalovat na zařízení rozšíření GPU.


