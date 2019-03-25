---
title: Připojte se k a správě zařízení Microsoft Azure Data Box brány prostřednictvím rozhraní Windows PowerShell | Dokumentace Microsoftu
description: Popisuje, jak se připojit k a pak spravovat bránu pole dat prostřednictvím rozhraní Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 67caaa2c6c9bd615d0b88bdd5de3442b46aa32cb
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403489"
---
# <a name="manage-an-azure-data-box-gateway-device-via-windows-powershell"></a>Správa zařízení s Azure Data Box brány pomocí prostředí Windows PowerShell

Azure Data Box brány řešení umožňuje odesílání dat přes síť do Azure. Tento článek popisuje některé konfigurace a Správa úloh pro vaše zařízení Data Box brány. Na webu Azure portal, místního webového uživatelského rozhraní nebo rozhraní Windows PowerShell můžete použít ke správě vašeho zařízení.

Tento článek se zaměřuje na úlohy, které můžete provést pomocí rozhraní PowerShell.

Tento článek obsahuje následující postupy:

- Připojení k rozhraní prostředí PowerShell
- Spustit relaci
- Vytvořit balíček pro podporu
- Nahrání certifikátu
- Spustit v prostředí bez DHCP
- Zobrazení informací o zařízení

## <a name="connect-to-the-powershell-interface"></a>Připojení k rozhraní prostředí PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Vytvořit balíček pro podporu

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Nahrání certifikátu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

## <a name="boot-up-in-non-dhcp-environment"></a>Spustit v prostředí bez DHCP

[!INCLUDE [Boot up in non-DHCP environment](../../includes/data-box-edge-gateway-boot-non-dhcp.md)]

## <a name="view-device-information"></a>Zobrazení informací o zařízení

[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="next-steps"></a>Další postup

- Rychle nasaďte [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) na webu Azure Portal.
