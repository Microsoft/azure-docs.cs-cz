---
title: Připojení k a správu Microsoft Azure Data Box hraničních zařízení prostřednictvím rozhraní Windows PowerShell | Dokumentace Microsoftu
description: Popisuje, jak k připojení a potom spravovat okraj pole Data prostřednictvím rozhraní Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: b3effdbace2be582bfe85d0402088f8aa0d96fe7
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554936"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell-preview"></a>Správa Azure Data Box hraničního zařízení pomocí Windows Powershellu (preview)

Azure Data Box řešení vám umožní zpracování dat a odesílání přes síť do Azure. Tento článek popisuje některé z úloh konfigurace a správa pro vaše zařízení Data Box Edge. Na webu Azure portal, místního webového uživatelského rozhraní nebo rozhraní Windows PowerShell můžete použít ke správě vašeho zařízení.

Tento článek se zaměřuje na úlohy, které můžete provést pomocí rozhraní PowerShell.

Tento článek obsahuje následující postupy:

- Připojení k rozhraní prostředí PowerShell
- Spustit relaci
- Vytvořit balíček pro podporu
- Nahrání certifikátu
- Resetování zařízení
- Zobrazení informací o zařízení
- Získání protokolů výpočetní prostředky
- Monitorování a odstraňování potíží výpočetní moduly

> [!IMPORTANT]
> Azure Data Box Edge je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="connect-to-the-powershell-interface"></a>Připojení k rozhraní prostředí PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="start-a-support-session"></a>Spustit relaci

[!INCLUDE [Connect to support runspace](../../includes/data-box-edge-gateway-connect-support.md)]

## <a name="create-a-support-package"></a>Vytvořit balíček pro podporu

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Nahrání certifikátu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="view-device-information"></a>Zobrazení informací o zařízení
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Tovární nastavení zařízení

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Získání protokolů výpočetní prostředky

Pokud výpočetní roli je nakonfigurovaný na vašem zařízení, můžete také získat protokoly výpočetní prostřednictvím rozhraní PowerShell.

1. [Připojení k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. Použití `Get-AzureDataBoxEdgeComputeRoleLogs` vám pomůže získat protokoly výpočetní prostředky pro vaše zařízení.

    Následující příklad ukazuje použití této rutiny:

    ```
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```
    Tady je popis parametrů použitých pro tuto rutinu: 
    - `Path`: Zadejte síťovou cestu ke sdílené složce, ve kterém chcete vytvořit balíček protokolu výpočetní prostředky.
    - `Credential`: Zadejte uživatelské jméno a heslo pro sdílené síťové složce.
    - `RoleInstanceName`: Zadejte tento řetězec `IotRole` pro tento parametr.
    - `FullLogCollection`: Tento parametr zajišťuje, že balíček protokolu bude obsahovat všechny protokoly výpočetní prostředky. Ve výchozím nastavení balíček protokolu obsahuje pouze podmnožinu protokolů.


## <a name="next-steps"></a>Další postup

- Rychle nasaďte [Azure Data Box Edge](data-box-edge-deploy-prep.md) na webu Azure Portal.
