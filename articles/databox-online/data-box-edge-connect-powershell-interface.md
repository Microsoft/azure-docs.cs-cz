---
title: Připojení a Správa Microsoft Azure Data Box Edge zařízení přes rozhraní Windows PowerShellu | Microsoft Docs
description: Popisuje, jak se připojit ke službě Data Box Edge přes rozhraní Windows PowerShell a potom je spravovat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265477"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Správa zařízení Azure Data Box Edge přes Windows PowerShell

Azure Data Box Edge řešení umožňuje zpracovávat data a odesílat je přes síť do Azure. Tento článek popisuje některé úlohy konfigurace a správy pro vaše zařízení Data Box Edge. Ke správě zařízení můžete použít rozhraní Azure Portal, místní webové uživatelské rozhraní nebo prostředí Windows PowerShell.

Tento článek se zaměřuje na úlohy, které provedete pomocí prostředí PowerShell.

Tento článek obsahuje následující postupy:

- Připojení k rozhraní PowerShell
- Vytvoření balíčku pro podporu
- Nahrání certifikátu
- Resetování zařízení
- Zobrazit informace o zařízení
- Získat výpočetní protokoly
- Monitorování a řešení potíží s výpočetními moduly

## <a name="connect-to-the-powershell-interface"></a>Připojení k rozhraní PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Vytvoření balíčku pro podporu

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Nahrání certifikátu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Můžete také nahrát IoT Edge certifikátů a povolit tak zabezpečené připojení mezi zařízením IoT Edge a zařízeními pro příjem dat, která se k němu mohou připojit. Existují tři certifikáty IoT Edge (formát *. pem* ), které je třeba nainstalovat:

- Certifikát kořenové certifikační autority nebo certifikační autorita vlastníka
- Certifikát certifikační autority zařízení
- Certifikát klíče zařízení

Následující příklad ukazuje použití této rutiny k instalaci IoT Edgech certifikátů:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Při spuštění této rutiny se zobrazí výzva, abyste zadali heslo pro sdílenou síťovou složku.

Další informace o certifikátech najdete v [Azure IoT Edge certifikáty](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) nebo [Instalace certifikátů na bránu](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Zobrazit informace o zařízení
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Resetování zařízení

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Získat výpočetní protokoly

Pokud je v zařízení nakonfigurovaná výpočetní role, můžete získat výpočetní protokoly i přes rozhraní PowerShell.

1. [Připojte se k rozhraní PowerShell](#connect-to-the-powershell-interface).
2. Použijte `Get-AzureDataBoxEdgeComputeRoleLogs` k získání výpočetních protokolů pro vaše zařízení.

    Následující příklad ukazuje použití této rutiny:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Tady je popis parametrů použitých pro rutinu:
    - `Path`: Zadejte síťovou cestu ke sdílené složce, ve které chcete vytvořit balíček výpočetního protokolu.
    - `Credential`: Zadejte uživatelské jméno pro sdílenou síťovou složku. Při spuštění této rutiny budete muset zadat heslo pro sdílení.
    - `FullLogCollection`: Tento parametr zajišťuje, že balíček protokolu bude obsahovat všechny protokoly výpočtů. Ve výchozím nastavení obsahuje balíček protokolu pouze podmnožinu protokolů.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Monitorování a řešení potíží s výpočetními moduly

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Ukončit vzdálenou relaci

Pokud chcete ukončit vzdálenou relaci PowerShellu, zavřete okno PowerShell.

## <a name="next-steps"></a>Další kroky

- Rychle nasaďte [Azure Data Box Edge](data-box-edge-deploy-prep.md) na webu Azure Portal.
