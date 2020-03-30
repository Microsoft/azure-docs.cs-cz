---
title: Připojení a správa zařízení Microsoft Azure Data Box Edge prostřednictvím rozhraní Prostředí Windows PowerShell | Dokumenty společnosti Microsoft
description: Popisuje, jak se připojit k datovému poli A potom ke správě data boxu Edge prostřednictvím rozhraní prostředí Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 06/25/2019
ms.author: alkohli
ms.openlocfilehash: f49396331a31f7ca9eaf453dc8bf6880da2e0da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265477"
---
# <a name="manage-an-azure-data-box-edge-device-via-windows-powershell"></a>Správa zařízení Azure Data Box Edge přes Windows PowerShell

Řešení Azure Data Box Edge umožňuje zpracovávat data a odesílat je přes síť do Azure. Tento článek popisuje některé úlohy konfigurace a správy zařízení Data Box Edge. Ke správě zařízení můžete použít portál Azure, místní webové uživatelské rozhraní nebo rozhraní Prostředí Windows PowerShell.

Tento článek se zaměřuje na úkoly, které děláte pomocí rozhraní Prostředí PowerShell.

Tento článek obsahuje následující postupy:

- Připojení k rozhraní PowerShellu
- Vytvoření balíčku podpory
- Nahrání certifikátu
- Resetování zařízení
- Zobrazit informace o zařízení
- Získání výpočetních protokolů
- Sledování a odstraňování problémů s výpočetními moduly

## <a name="connect-to-the-powershell-interface"></a>Připojení k rozhraní PowerShellu

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Vytvoření balíčku podpory

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Nahrání certifikátu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Můžete také nahrát certifikáty IoT Edge a povolit zabezpečené připojení mezi zařízením IoT Edge a zařízeními pro příjem dat, která se k němu mohou připojit. Existují tři certifikáty IoT Edge ( formát *.pem),* které je třeba nainstalovat:

- Kořenový certifikát certifikační autority nebo certifikační autorita vlastníka
- Certifikát certifikační autority zařízení
- Certifikát klíče zařízení

Následující příklad ukazuje použití této rutiny k instalaci certifikátů IoT Edge:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Po spuštění této rutiny budete vyzváni k zadání hesla pro sdílenou síťovou složku.

Další informace o certifikátech najdete v [certifikátech Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) nebo [Instalace certifikátů na bráně](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

## <a name="view-device-information"></a>Zobrazit informace o zařízení
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="reset-your-device"></a>Resetování zařízení

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Získání výpočetních protokolů

Pokud je výpočetní role nakonfigurovaná na vašem zařízení, můžete také získat výpočetní protokoly prostřednictvím rozhraní PowerShellu.

1. [Připojte se k rozhraní prostředí PowerShell](#connect-to-the-powershell-interface).
2. Použijte `Get-AzureDataBoxEdgeComputeRoleLogs` k získání výpočetních protokolů pro vaše zařízení.

    Následující příklad ukazuje použití této rutiny:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection
    ```

    Zde je popis parametrů použitých pro rutinu:
    - `Path`: Zadejte síťovou cestu ke sdílené položce, kde chcete vytvořit balíček protokolu výpočetních prostředků.
    - `Credential`: Zadejte uživatelské jméno pro sdílenou síťovou složku. Při spuštění této rutiny bude nutné zadat heslo pro sdílení.
    - `FullLogCollection`: Tento parametr zajišťuje, že balíček protokolu bude obsahovat všechny výpočetní protokoly. Ve výchozím nastavení obsahuje balíček protokolu pouze podmnožinu protokolů.

## <a name="monitor-and-troubleshoot-compute-modules"></a>Sledování a odstraňování problémů s výpočetními moduly

[!INCLUDE [Monitor and troubleshoot compute modules](../../includes/data-box-edge-monitor-troubleshoot-compute.md)]

## <a name="exit-the-remote-session"></a>Ukončení vzdálené relace

Chcete-li ukončit vzdálenou relaci prostředí PowerShell, zavřete okno prostředí PowerShell.

## <a name="next-steps"></a>Další kroky

- Rychle nasaďte [Azure Data Box Edge](data-box-edge-deploy-prep.md) na webu Azure Portal.
