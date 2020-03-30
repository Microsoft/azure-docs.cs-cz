---
title: Připojení k zařízení Azure Data Box Gateway a správa zařízení Azure Data Box Gateway pomocí Windows PowerShellu
description: Popisuje, jak se připojit k bráně datové schránky a spravovat ji prostřednictvím rozhraní prostředí Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 08/02/2019
ms.author: alkohli
ms.openlocfilehash: 6c9f3455a07001a8d1b9b8a1e84f2af3392b5690
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260212"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Správa zařízení Azure Data Box Gateway přes Windows PowerShell

Řešení Azure Data Box Gateway umožňuje odesílat data přes síť do Azure. Tento článek popisuje některé úlohy konfigurace a správy pro zařízení Brány datové schránky. Ke správě zařízení můžete použít portál Azure, místní webové uživatelské rozhraní nebo rozhraní Prostředí Windows PowerShell.

Tento článek se zaměřuje na úkoly, které děláte pomocí rozhraní Prostředí PowerShell.

Tento článek obsahuje následující postupy:

- Připojení k rozhraní PowerShellu
- Vytvoření balíčku podpory
- Nahrání certifikátu
- Spuštění v prostředí mimo server DHCP
- Zobrazit informace o zařízení

## <a name="connect-to-the-powershell-interface"></a>Připojení k rozhraní PowerShellu

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Vytvoření balíčku podpory

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Nahrání certifikátu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Spuštění v prostředí mimo server DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Zobrazit informace o zařízení

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Další kroky

- Rychle nasaďte [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) na webu Azure Portal.
