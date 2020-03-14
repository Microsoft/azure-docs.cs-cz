---
title: Připojení a Správa zařízení Azure Data Box Gateway pomocí prostředí Windows PowerShell
description: Popisuje, jak se připojit ke službě Data Box Gateway přes rozhraní Windows PowerShell a potom je spravovat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260212"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Správa zařízení Azure Data Box Gateway přes Windows PowerShell

Azure Data Box Gateway řešení umožňuje odesílat data přes síť do Azure. Tento článek popisuje některé úlohy konfigurace a správy pro vaše zařízení Data Box Gateway. Ke správě zařízení můžete použít rozhraní Azure Portal, místní webové uživatelské rozhraní nebo prostředí Windows PowerShell.

Tento článek se zaměřuje na úlohy, které provedete pomocí prostředí PowerShell.

Tento článek obsahuje následující postupy:

- Připojení k rozhraní PowerShell
- Vytvoření balíčku pro podporu
- Nahrání certifikátu
- Spuštění v prostředí bez DHCP
- Zobrazit informace o zařízení

## <a name="connect-to-the-powershell-interface"></a>Připojení k rozhraní PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Vytvoření balíčku pro podporu

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Nahrání certifikátu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Spuštění v prostředí bez DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Zobrazit informace o zařízení

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Další kroky

- Rychle nasaďte [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) na webu Azure Portal.
