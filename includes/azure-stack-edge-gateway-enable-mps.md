---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/11/2021
ms.author: alkohli
ms.openlocfilehash: b2c1ebe390b1a2dec7be678b5d6f3a991056a23b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102517550"
---
1. Než začnete, ujistěte se, že:

    1. Nakonfigurovali jste a [aktivovali své zařízení Azure Stack Edge pro](../articles/databox-online/azure-stack-edge-gpu-deploy-activate.md) s využitím prostředků Azure Stack Edge v Azure.
    1. [Na tomto zařízení jste nakonfigurovali COMPUTE na Azure Portal](../articles/databox-online/azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Připojte se k rozhraní PowerShell](../articles/databox-online/azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Pomocí následujícího příkazu povolte na svém zařízení MPS.

    ```powershell
    Start-HcsGpuMPS
    ```