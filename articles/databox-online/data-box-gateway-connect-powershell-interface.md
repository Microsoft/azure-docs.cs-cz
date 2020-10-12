---
title: Připojení a Správa zařízení Azure Data Box Gateway pomocí prostředí Windows PowerShell
description: Popisuje, jak se připojit ke službě Data Box Gateway přes rozhraní Windows PowerShell a potom je spravovat.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: c071d372ba90d29806fd8a44909e2c803a8d3fa4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84339276"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Správa zařízení Azure Data Box Gateway přes Windows PowerShell

Azure Data Box Gateway řešení umožňuje odesílat data přes síť do Azure. Tento článek popisuje některé úlohy konfigurace a správy pro vaše zařízení Data Box Gateway. Ke správě zařízení můžete použít rozhraní Azure Portal, místní webové uživatelské rozhraní nebo prostředí Windows PowerShell.

Tento článek se zaměřuje na úlohy, které provedete pomocí prostředí PowerShell.

Tento článek obsahuje následující postupy:

- Připojte se k rozhraní PowerShellu.
- Vytvoření balíčku pro podporu
- Nahrání certifikátu
- Spuštění v prostředí bez DHCP
- Zobrazit informace o zařízení

## <a name="connect-to-the-powershell-interface"></a>Připojte se k rozhraní PowerShellu.

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
