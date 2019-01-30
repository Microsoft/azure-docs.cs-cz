---
title: Spuštění a zastavení služby Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak ke spuštění a vypnutí služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: b8d13377b2fb1545ecf88645a5e19bf5eab24c74
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238131"
---
# <a name="start-and-stop-azure-stack"></a>Spuštění a zastavení služby Azure Stack
Postupujte podle postupů v tomto článku řádně vypnout a restartovat služby Azure Stack. Vypnutí se fyzicky vypnutí celého prostředí Azure Stack. Po spuštění zapne všechny role infrastruktury a vrátí prostředků tenanta power stavu, ve kterém se nacházely před vypnutí.

## <a name="stop-azure-stack"></a>Zastavení služby Azure Stack 

Vypnutí služby Azure Stack pomocí následujících kroků:

1. Příprava všechny úlohy běžící v prostředí Azure Stack tenanta prostředky pro nadcházející vypnutí. 

2. Otevřete relaci pro privilegované koncový bod (období) z počítače s přístupem k síti na virtuální počítače Azure Stack ERCS. Pokyny najdete v tématu [pomocí privilegovaných koncového bodu ve službě Azure Stack](azure-stack-privileged-endpoint.md).

3. Z období spusťte:

    ```powershell
      Stop-AzureStack
    ```

4. Počkejte všech fyzických uzlů Azure Stack k výkonu vypnout.

> [!Note]  
> Stav napájení fyzického uzlu lze ověřit podle pokynů od výrobce OEM (OEM), zadali svůj hardware Azure Stack. 

## <a name="start-azure-stack"></a>Spuštění služby Azure Stack 

Spuštění služby Azure Stack pomocí následujících kroků. Postupujte podle těchto kroků bez ohledu na to, jak Azure Stack zastavena.

1. Napájení na všech fyzických uzlů ve vašem prostředí Azure Stack. Ověřte zapnutí pokyny, jak fyzických uzlů podle pokynů od výrobce OEM (OEM) který poskytnutý hardware Azure Stack.

2. Počkejte, dokud se spustí služby infrastruktury Azure stacku. Služby infrastruktury Azure Stack může vyžadovat dvou hodin k dokončení procesu spuštění. Počáteční stav služby Azure Stack s můžete ověřit [ **Get-ActionStatus** rutiny](#get-the-startup-status-for-azure-stack).

3. Ujistěte se, že všech vašich prostředků tenanta vracet, stavu, ve kterém se nacházely před vypnutí. Úlohy běžící na prostředky tenanta může být potřeba překonfigurovat po spuštění Správce úloh.

## <a name="get-the-startup-status-for-azure-stack"></a>Získání stavu po spuštění pro službu Azure Stack

Získáte spuštění pro spouštěcí rutiny služby Azure Stack pomocí následujících kroků:

1. Otevřete relaci Privileged koncového bodu z počítače s přístupem k síti na virtuální počítače Azure Stack ERCS.

2. Z období spusťte:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Řešení potíží při spuštění a ukončení služby Azure Stack

Pokud služby infrastruktury a klientů není úspěšně spustit dvě hodiny po napájení můžete ve vašem prostředí Azure Stack, proveďte následující kroky. 

1. Otevřete relaci Privileged koncového bodu z počítače s přístupem k síti na virtuální počítače Azure Stack ERCS.

2. Spusťte: 

    ```powershell
      Test-AzureStack
      ```

3. Zkontrolujte výstup a vyřešte všechny chyby stavu. Další informace najdete v tématu [spustit test pro ověření služby Azure Stack](azure-stack-diagnostic-test.md).

4. Spusťte:

    ```powershell
      Start-AzureStack
    ```

5. Pokud systém **Start AzureStack** neúspěšně, obraťte se na zákaznickou podporu služeb Microsoftu. 

## <a name="next-steps"></a>Další postup 

Další informace o [diagnostické nástroje služby Azure Stack](azure-stack-diagnostics.md)
