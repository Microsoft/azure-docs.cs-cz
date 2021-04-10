---
title: Připojení a Správa zařízení Microsoft Azure Stack Edge pro prostřednictvím rozhraní Windows PowerShellu | Microsoft Docs
description: Popisuje, jak se připojit ke službě Azure Stack Edge pro a jak ji spravovat přes rozhraní Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 09/30/2020
ms.author: alkohli
ms.openlocfilehash: c9c6247f021b7af4cfdd899ffd4b6bd178f2256c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96345589"
---
# <a name="manage-an-azure-stack-edge-pro-fpga-device-via-windows-powershell"></a>Správa zařízení Azure Stack Edge pro FPGA přes Windows PowerShell

Řešení Azure Stack Edge pro umožňuje zpracovávat data a odesílat je přes síť do Azure. Tento článek popisuje některé úlohy konfigurace a správy pro zařízení Azure Stack Edge pro. Ke správě zařízení můžete použít rozhraní Azure Portal, místní webové uživatelské rozhraní nebo prostředí Windows PowerShell.

Tento článek se zaměřuje na úlohy, které provedete pomocí prostředí PowerShell. 

Tento článek obsahuje následující postupy:

- Připojte se k rozhraní PowerShellu.
- Vytvoření balíčku pro podporu
- Nahrání certifikátu
- Resetování zařízení
- Zobrazit informace o zařízení
- Získat výpočetní protokoly
- Monitorování a řešení potíží s výpočetními moduly

## <a name="connect-to-the-powershell-interface"></a>Připojte se k rozhraní PowerShellu.

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Vytvoření balíčku pro podporu

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Nahrání certifikátu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Můžete také nahrát certifikáty IoT Edge a tím umožnit zabezpečené připojení mezi vaším zařízením IoT Edge a podřízenými zařízeními, která se k němu můžou připojovat. Existují tři soubory (formát *. pem* ), které je třeba nainstalovat:

- Certifikát kořenové certifikační autority nebo certifikační autorita vlastníka
- Certifikát certifikační autority zařízení
- Privátní klíč zařízení 

Následující příklad ukazuje použití této rutiny k instalaci IoT Edgech certifikátů:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-private-key.pem" -Credential "username"
```
Při spuštění této rutiny se zobrazí výzva, abyste zadali heslo pro sdílenou síťovou složku.

Další informace o certifikátech najdete v [Azure IoT Edge certifikáty](../iot-edge/iot-edge-certs.md) nebo [Instalace certifikátů na bránu](../iot-edge/how-to-create-transparent-gateway.md).

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

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/azure-stack-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Ukončit vzdálenou relaci

Pokud chcete ukončit vzdálenou relaci PowerShellu, zavřete okno PowerShell.

## <a name="next-steps"></a>Další kroky

- Nasaďte [Azure Stack Edge pro](azure-stack-edge-deploy-prep.md) v Azure Portal.