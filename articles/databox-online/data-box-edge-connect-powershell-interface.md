---
title: Připojení k a správu Microsoft Azure Data Box hraničních zařízení prostřednictvím rozhraní Windows PowerShell | Dokumentace Microsoftu
description: Popisuje, jak k připojení a potom spravovat okraj pole Data prostřednictvím rozhraní Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/25/2019
ms.author: alkohli
ms.openlocfilehash: 8cd89b21e80662ec50746e0c7721a5544cfbce30
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64717500"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Správa Azure Data Box hraničního zařízení pomocí Windows Powershellu

Azure Data Box řešení vám umožní zpracování dat a odesílání přes síť do Azure. Tento článek popisuje některé z úloh konfigurace a správa pro vaše zařízení Data Box Edge. Na webu Azure portal, místního webového uživatelského rozhraní nebo rozhraní Windows PowerShell můžete použít ke správě vašeho zařízení.

Tento článek se zaměřuje na úlohy, které můžete provést pomocí rozhraní PowerShell.

Tento článek obsahuje následující postupy:

- Připojení k rozhraní prostředí PowerShell
- Vytvořit balíček pro podporu
- Nahrání certifikátu
- Resetování zařízení
- Zobrazení informací o zařízení
- Získání protokolů výpočetní prostředky
- Monitorování a odstraňování potíží výpočetní moduly

## <a name="connect-to-the-powershell-interface"></a>Připojení k rozhraní prostředí PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Vytvořit balíček pro podporu

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Nahrání certifikátu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Můžete také nahrát certifikáty IoT Edge umožňuje zabezpečené připojení mezi zařízení IoT Edge a podřízené zařízení může připojit k němu. Existují tři certifikáty IoT Edge (*.pem* formátu), který je potřeba nainstalovat:

- Certifikát kořenové certifikační Autority nebo vlastník certifikační Autority
- Certifikát certifikační Autority zařízení
- Zařízení klíče certifikátu

Následující příklad ukazuje použití této rutiny instalace certifikátů IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username/password"
```

Další informace o certifikátech naleznete v části [certifikáty Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) nebo [nainstalujte certifikáty na bránu](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway#install-certificates-on-the-gateway).

## <a name="view-device-information"></a>Zobrazení informací o zařízení
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Tovární nastavení zařízení

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Získání protokolů výpočetní prostředky

Pokud výpočetní roli je nakonfigurovaný na vašem zařízení, můžete také získat protokoly výpočetní prostřednictvím rozhraní PowerShell.

1. [Připojení k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. Použití `Get-AzureDataBoxEdgeComputeRoleLogs` vám pomůže získat protokoly výpočetní prostředky pro vaše zařízení.

    Následující příklad ukazuje použití této rutiny:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username/password" -RoleInstanceName "IotRole" -FullLogCollection
    ```

    Tady je popis parametrů použitých pro tuto rutinu:
    - `Path`: Zadejte síťovou cestu ke sdílené složce, ve kterém chcete vytvořit balíček protokolu výpočetní prostředky.
    - `Credential`: Zadejte uživatelské jméno a heslo pro sdílené síťové složce.
    - `RoleInstanceName`: Zadejte tento řetězec `IotRole` pro tento parametr.
    - `FullLogCollection`: Tento parametr zajišťuje, že balíček protokolu bude obsahovat všechny protokoly výpočetní prostředky. Ve výchozím nastavení balíček protokolu obsahuje pouze podmnožinu protokolů.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorování a odstraňování potíží výpočetní moduly

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Ukončete relaci vzdálené

Pro ukončení vzdálené relace prostředí PowerShell, zavřete okno prostředí PowerShell.

## <a name="next-steps"></a>Další postup

- Rychle nasaďte [Azure Data Box Edge](data-box-edge-deploy-prep.md) na webu Azure Portal.
